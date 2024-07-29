# **Doris手工调优**

Apache Doris 是一个现代的 MPP（大规模并行处理）数据库系统，旨在提供高性能的实时分析。SQL 优化器在查询性能中扮演着至关重要的角色，通过调优 SQL 优化器，可以显著提高查询的执行效率。以下是一些调优 Apache Doris SQL 优化器的方法和策略：

### 1. 使用统计信息

- **统计信息收集**：确保表的统计信息是最新的。统计信息包括表的行数、列的基数（distinct values）、最小值、最大值等，这些信息对于优化器选择最优执行计划至关重要。

  ```sql
  ANALYZE TABLE table_name;
  ```

- **自动更新统计信息**：可以配置自动更新统计信息的策略，确保统计信息保持最新。

### 2. 利用索引

- **创建适当的索引**：在频繁查询的列上创建索引，以加速查询性能。Doris 支持主键索引、二级索引等。

  ```sql
  CREATE INDEX index_name ON table_name (column_name);
  ```

- **使用 Bitmap 索引**：在高基数列上创建 Bitmap 索引，有助于加快查询速度。

  ```sql
  CREATE INDEX index_name ON table_name (column_name) USING BITMAP;
  ```

### 3. 分区和分桶

- **表分区**：对大表进行分区，可以显著减少查询扫描的数据量。分区可以基于时间、范围或哈希等。

  ```sql
  CREATE TABLE table_name (
      column1 INT,
      column2 STRING
  )
  PARTITION BY RANGE (column1) (
      PARTITION p1 VALUES LESS THAN ("2023-01-01"),
      PARTITION p2 VALUES LESS THAN ("2024-01-01")
  );
  ```

- **分桶**：分桶可以进一步优化查询性能，特别是针对高并发的查询。

  ```sql
  CREATE TABLE table_name (
      column1 INT,
      column2 STRING
  )
  DISTRIBUTED BY HASH(column1) BUCKETS 10;
  ```

### 4. 查询重写

- **避免全表扫描**：尽量使用 WHERE 子句来限制查询范围，避免全表扫描。

  ```sql
  SELECT * FROM table_name WHERE column1 = 'value';
  ```

- **适当使用子查询**：将复杂查询拆分成多个子查询，有助于优化器更好地选择执行计划。
- **使用 JOIN 优化**：在连接操作中，确保适当使用索引，并选择合适的连接类型（如 INNER JOIN, LEFT JOIN）。

### 5. 参数调优

- **内存和并行度配置**：调整查询的内存使用和并行度参数，以提高查询性能。

  ```sql
  SET exec_mem_limit=2G;
  SET parallel_fragment_exec_instance_num=4;
  ```

- **调优系统参数**：根据实际使用场景调优系统参数，如查询超时时间、缓存大小等。

### 6. 使用 CBO 和 RBO

- **成本基优化器（CBO）**：Doris 使用 CBO 来选择最优执行计划。确保统计信息准确，并根据查询类型调整优化器参数。
- **规则基优化器（RBO）**：Doris 也使用 RBO 来进行初步的查询重写和优化，确保规则适用于常见查询场景。

### 7. 性能分析和调优工具

- **使用 EXPLAIN 分析查询计划**：通过 EXPLAIN 语句查看查询执行计划，识别潜在的性能瓶颈。

  ```sql
  EXPLAIN SELECT * FROM table_name WHERE column1 = 'value';
  ```

- **Profiling 工具**：使用 Doris 提供的 Profiling 工具，深入分析查询的执行细节，识别并解决性能问题。

### 8. 索引和数据布局优化

- **分区裁剪**：确保查询能够有效利用分区裁剪技术，只扫描必要的分区。
- **存储格式选择**：选择合适的存储格式（如列存储、行存储）以提高查询性能。

### 9. 查询缓存

- **结果缓存**：开启查询结果缓存，以减少重复查询的开销。

  ```sql
  SET enable_result_cache=true;
  ```

通过以上策略和方法，可以有效地调优 Apache Doris 的 SQL 优化器，提升查询性能和系统响应速度。在实际应用中，应根据具体的使用场景和查询特点，进行针对性的调优和优化。
