Flink提供了不同的抽象级别以开发流式或者批处理应用程序

<img src="https://ci.apache.org/projects/flink/flink-docs-release-1.9/fig/levels_of_abstraction.svg"/>

- **Stateful Stream Processing** 最低级的抽象接口是状态化的数据流接口（stateful streaming）。这个接口是通过 ProcessFunction 集成到 DataStream API 中的。该接口允许用户自由的处理来自一个或多个流中的事件，并使用一致的容错状态。另外，用户也可以通过注册 event time 和 processing time 处理回调函数的方法来实现复杂的计算

- **DataStream/DataSet API** DataStream / DataSet API 是 Flink 提供的核心 API ，DataSet 处理有界的数据集，DataStream 处理有界或者无界的数据流。用户可以通过各种方法（map / flatmap / window / keyby / sum / max / min / avg / join 等）将数据进行转换 / 计算

- **Table API**  Table API 提供了例如 select、project、join、group-by、aggregate 等操作，使用起来却更加简洁,可以在表与 DataStream/DataSet 之间无缝切换，也允许程序将 Table API 与 DataStream 以及 DataSet 混合使用

- **SQL** Flink 提供的最高层级的抽象是 SQL 。这一层抽象在语法与表达能力上与 Table API 类似。SQL 抽象与 Table API 交互密切，同时 SQL 查询可以直接在 Table API 定义的表上执行

## Dataflows 数据流图

在Flink的世界观中，一切都是数据流，所以对于批计算来说，那只是流计算的一个特例而已

Flink Dataflows是由三部分组成，分别是：source、transformation、sink结束

source数据源会源源不断的产生数据，transformation将产生的数据进行各种业务逻辑的数据处理，最终由sink输出到外部（console、kafka、redis、DB......）

基于Flink开发的程序都能够映射成一个Dataflows

![img](https://upload-images.jianshu.io/upload_images/9654612-e9bd5f6661312e7d.jpeg?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

当source数据源的数量比较大或计算逻辑相对比较复杂的情况下，需要提高并行度来处理数据，采用并行数据流

通过设置不同算子的并行度 source并行度设置为2  map也是2.... 代表会启动多个并行的线程来处理数据

![A parallel dataflow](https://ci.apache.org/projects/flink/flink-docs-release-1.9/fig/parallel_dataflow.svg)

### 配置开发环境