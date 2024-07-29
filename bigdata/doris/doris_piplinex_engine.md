# PipelineX 执行引擎
## 背景
PipelineX 执行引擎的目标是为了解决 Doris pipeline 引擎的四大问题：

1. 执行并发上，当前 Doris 执行并发收到两个因素的制约，一个是 fe 设置的参数，另一个是受存储层 bucket 数量的限制，这样的静态并发使得执行引擎无法充分利用机器资源。
2. 执行逻辑上，当前 Doris 有一些固定的额外开销，例如表达式部分各个 instance 彼此独立，而 instance 的初始化参数有很多公共部分，所以需要额外进行很多重复的初始化步骤。
3. 调度逻辑上，当前 pipeline 的调度器会把阻塞 task 全部放入一个阻塞队列中，由一个线程负责轮询并从阻塞队列中取出可执行 task 放入 runnable 队列，所以在有查询执行的过程中，会固定有一个核的资源作为调度的开销。
4. profile 方面，目前 pipeline 无法为用户提供简单易懂的指标。

它的具体设计、实现和效果可以参阅 [(DSIP-035: PipelineX Execution Engine - DORIS - Apache Software Foundation] (<https://cwiki.apache.org/confluence/display/DORIS/DSIP-035%3A+PipelineX+Execution+Engine)。>


## 预期效果


1. 执行并发上，依赖 local exchange 使 pipelinex 充分并发，可以让数据被均匀分布到不同的 task 中，尽可能减少数据倾斜，此外，pipelineX 也将不再受存储层 tablet 数量的制约。
2. 执行逻辑上，多个 pipeline task 共享同一个 pipeline 的全部共享状态，例如表达式和一些 const 变量，消除了额外的初始化开销。
3. 调度逻辑上，所有 pipeline task 的阻塞条件都使用 Dependency 进行了封装，通过外部事件（例如 rpc 完成）触发 task 的执行逻辑进入 runnable 队列，从而消除了阻塞轮询线程的开销。
4. profile：为用户提供简单易懂的指标。

## 用户接口变更
**设置 Session 变量**

- enable_pipeline_x_engine
将 session 变量enable_pipeline_x_engine 设置为true，则 BE 在进行查询执行时就会默认将 SQL 的执行模型转变 PipelineX 的执行方式。如果 Pipeline 和 PipelineX 引擎都设置为开启，Doris 将优先使用 PipelineX 引擎。

<code>set enable_pipeline_x_engine = true;</code>



enable_local_shuffle
设置enable_local_shuffle为 true 则打开 local shuffle 优化。local shuffle 将尽可能将数据均匀分布给不同的 pipeline task 从而尽可能避免数据倾斜。

    set enable_local_shuffle = true;

ignore_storage_data_distribution
设置ignore_storage_data_distribution为 true 则表示忽略存储层的数据分布。结合 local shuffle 一起使用，则 pipelineX 引擎的并发能力将不再受到存储层 tablet 数量的制约，从而充分利用机器资源。

    set ignore_storage_data_distribution = true;