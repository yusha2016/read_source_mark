Dori调用hudisdk使用jni,而不是使用其他的rpc协议,这样设计有什么优势吗

Apache Doris 通过 JNI（Java Native Interface）调用 Hudi SDK，而不是使用其他的 RPC（Remote Procedure Call）协议，这样的设计有其独特的优势。以下是一些主要的优势：

### 1. **性能优势**

#### 低延迟和高吞吐量
- **直接内存访问**：通过 JNI，Doris 可以直接访问 Hudi 的内存数据结构，避免了 RPC 调用中的序列化和反序列化开销。这样可以减少数据传输的延迟，提高系统的整体性能。
- **减少网络开销**：RPC 通常需要通过网络传输数据，而 JNI 调用是在本地进程中进行的，无需网络通信，这显著降低了数据传输的延迟和带宽消耗。

### 2. **资源利用效率**

#### 内存和CPU效率
- **共享内存**：JNI 调用允许 Doris 和 Hudi 在同一个进程内共享内存数据，避免了不同进程之间数据复制的开销。这提高了内存利用效率，减少了不必要的内存拷贝操作。
- **减少上下文切换**：相比于 RPC 调用可能引发的上下文切换，JNI 调用是在同一进程内进行的，减少了上下文切换的开销，从而提高了 CPU 的利用效率。

### 3. **简化架构设计**

#### 简化部署和维护
- **单一进程模型**：通过 JNI，Doris 和 Hudi 可以运行在同一个进程中，简化了系统架构设计，减少了进程间通信和协调的复杂性。
- **易于调试和监控**：单一进程模型下，调试和监控变得更加简单，因为所有组件都在同一个地址空间内运行，开发者可以更容易地跟踪问题和性能瓶颈。

### 4. **可靠性和稳定性**

#### 减少故障点
- **避免网络故障**：RPC 调用依赖于网络，网络故障可能导致调用失败。而 JNI 调用在本地进行，避免了网络故障的影响，提高了系统的可靠性和稳定性。
- **一致性保证**：在同一进程内进行调用可以更好地保证数据一致性，减少了分布式系统中常见的一致性问题。

### 5. **灵活性和扩展性**

#### 直接调用Java代码
- **利用Java生态**：通过 JNI 调用 Hudi SDK，Doris 可以直接利用 Java 生态中的各种工具和库，从而更好地扩展和增强系统功能。
- **灵活扩展**：如果需要扩展功能，开发者可以直接在 Java 代码中进行扩展，而无需涉及跨语言的 RPC 调用接口设计和维护。

### 设计权衡

虽然 JNI 调用有许多优势，但也存在一些权衡需要考虑：

- **开发复杂性**：JNI 调用需要编写和维护本地代码，增加了开发复杂性。
- **调试困难**：JNI 调用涉及本地代码和 JVM 之间的交互，可能会使调试变得更加困难。
- **平台依赖**：JNI 调用依赖于特定的操作系统和平台，可能会增加跨平台支持的复杂性。

### 总结

通过 JNI 调用 Hudi SDK，Doris 可以充分利用本地调用的性能优势，减少数据传输和处理的开销，提高系统的效率和可靠性。同时，简化的架构设计也使得系统更容易部署和维护。然而，这样的设计也需要在开发复杂性、调试难度和平台依赖性等方面进行权衡。总体而言，JNI 调用在高性能需求和资源受限的场景下是一种有效的设计选择。