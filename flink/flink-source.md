Flink内嵌支持的数据源非常多，例如 HDFS、Socket、Kafka、Collections 。。。 Flink也提供了addSource方式，可以自定义数据源。

### File Source

通过读取本地、HDFS文件创建一个数据源

如果读取的是HDFS上的文件，那么需要导入Hadoop依赖

```
<dependency>
	<groupId>org.apache.hadoop</groupId>
	<artifactId>hadoop-client</artifactId>
	<version>2.9.2</version>
</dependency>
```

代码示例

```
package com.renguangli.flink.datastream.source;

import java.util.Arrays;
import org.apache.flink.api.common.functions.FlatMapFunction;
import org.apache.flink.api.common.functions.ReduceFunction;
import org.apache.flink.api.common.io.FilePathFilter;
import org.apache.flink.api.common.typeinfo.BasicTypeInfo;
import org.apache.flink.api.common.typeinfo.Types;
import org.apache.flink.api.java.functions.KeySelector;
import org.apache.flink.api.java.io.TextInputFormat;
import org.apache.flink.api.java.tuple.Tuple2;
import org.apache.flink.core.fs.Path;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.streaming.api.functions.source.FileProcessingMode;

/**
 * flink-sample
 *
 * @author renguangli Created at 2020/10/9 10:25
 * @since JDK1.8
 */
public class FileSourceSamples {

    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        env.setParallelism(1); // 并行度设置为 1

        // fileSource 读取本地文件或者 hdfs

        // 如果是文件读取文件内容，如果是目录会读取该目录下所有文件
//        String filePath = "D:/data";
//        String path = "D:/data/a.json";
        String filePath = "hdfs://hadoop:9000/user/root";
//        String path = "hdfs://hadoop:9000/user/root/slaves";

        TextInputFormat format = new TextInputFormat(new Path(filePath));
        format.setFilesFilter(FilePathFilter.createDefaultFilter());
//        format.setCharsetName("UTF-8");  默认 UTF-8

        // readTextFile 最终调用的是 readFile
//        env.readTextFile(filePath)

        // FileProcessingMode.PROCESS_ONCE 执行一次
        // PROCESS_CONTINUOUSLY 文件有更改时重新读取
        // interval 100 毫秒监控一次
        env.readFile(format, filePath, FileProcessingMode.PROCESS_CONTINUOUSLY, 100, BasicTypeInfo.STRING_TYPE_INFO)
                .flatMap((FlatMapFunction<String, String>) (value, out) -> Arrays.stream(value.split(" ")).forEach(out::collect)).returns(String.class)
                .map(v -> Tuple2.of(v, 1)).returns(Types.TUPLE(Types.STRING, Types.INT))
                .keyBy((KeySelector<Tuple2<String, Integer>, String>) value -> value.f0)
                .reduce((ReduceFunction<Tuple2<String, Integer>>) (value1, value2) -> Tuple2.of(value1.f0, value1.f1 + value2.f1))
                .print();

        env.execute();
    }

}

```

### Collection Source

基于集合的数据源，一般用于测试。

```
package com.renguangli.flink.datastream.source;

import java.util.ArrayList;
import java.util.List;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;

/**
 * flink-sample
 *
 * @author renguangli Created at 2020/10/9 10:53
 * @since JDK1.8
 */
public class CollectionSourceSamples {

    static class User{}

    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        env.setParallelism(1);

        List<String> list = new ArrayList<>();
        list.add("hello flink");
        list.add("hello spark");
        list.add("hello hadoop");

        // 无实际意思，仅用于test
        env.fromCollection(list).print();
//        env.fromElements("hello flink", "hello spark", "hello hadoop").print();
        env.fromElements(String.class, "hello flink", "hello spark", "hello hadoop").print();

        env.execute();
    }

}
```

### Socket Source

接受 Socket Server中的数据

```
package com.renguangli.flink.datastream.source;

import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;

/**
 * flink-sample
 *
 * @author renguangli Created at 2020/10/9 11:03
 * @since JDK1.8
 */
public class SocketSourceSamples {

    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
//        env.socketTextStream("192.168.10.10", 8888);
//        env.socketTextStream("192.168.10.10", 8888, "\t");
        // 分隔符默认 \t
        // 最大重试次数为 3
//        env.socketTextStream("192.168.10.10", 8888, "\t", 3);

        env.socketTextStream("192.168.10.10", 8888).print();
        env.execute();
    }
}

```

### Kafka Source

flink 接收 kafka 的数据

配置 flink 与 kafka 的连接器

```xml
<dependency>
    <groupId>org.apache.flink</groupId>
    <artifactId>flink-connector-kafka_2.11</artifactId>
    <version>1.11.0</version>
</dependency>
```

代码示例

```
package com.renguangli.flink.datastream.source;

import java.util.Arrays;
import java.util.List;
import java.util.Properties;
import java.util.regex.Pattern;
import org.apache.flink.api.common.serialization.SimpleStringSchema;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.streaming.connectors.kafka.FlinkKafkaConsumer;
import org.apache.kafka.common.serialization.StringDeserializer;

/**
 * flink-sample
 *
 * @author renguangli Created at 2020/10/9 11:22
 * @since JDK1.8
 */
public class KafkaSourceSamples {

    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();

        String topic = "iot_v3_property_up";
        Pattern topicPattern = Pattern.compile("flink-kafka-source-*");
        List<String> topics = Arrays.asList("iot_v3_property_up");

        // new SimpleStringSchema() 只消费 value
//        env.addSource(new FlinkKafkaConsumer<>(topic, new SimpleStringSchema(), kafkaProperties())).print();
        // 消费多个 topic
//        env.addSource(new FlinkKafkaConsumer<>(topics, new SimpleStringSchema(), kafkaProperties())).print();
        // 消费符合正则表达式的 topic
//        env.addSource(new FlinkKafkaConsumer<>(topicPattern, new SimpleStringSchema(), kafkaProperties())).print();

        FlinkKafkaConsumer<String> kafkaConsumer = new FlinkKafkaConsumer<>(topic, new SimpleStringSchema(), kafkaProperties());

        // kafkaConsumer.setStartFromGroupOffsets(); // the default behaviour

        // start from specified epoch timestamp (milliseconds)
//        kafkaConsumer.setStartFromTimestamp(1);


        // 消费指定分区
//        Map<KafkaTopicPartition, Long> specificStartOffsets = new HashMap<>();
          // topic partition offset
//        specificStartOffsets.put(new KafkaTopicPartition("myTopic", 0), 0L);
//        kafkaConsumer.setStartFromSpecificOffsets(specificStartOffsets);

        env.addSource(kafkaConsumer).print();
        env.execute();
    }


    public static Properties kafkaProperties() {
        Properties p = new Properties();
        p.setProperty("bootstrap.servers", "172.16.22.134:9092,172.16.22.135:9092,172.16.22.137:9092");
        p.setProperty("group.id", "rule-consumer-group");
        p.setProperty("key.deserializer", StringDeserializer.class.getName());
        p.setProperty("value.deserializer", StringDeserializer.class.getName());
        /*
         * earliest:从头开始消费
         * latest:从最近的数据开始消费，不再消费旧数据
         */
        p.setProperty("auto.offset.reset", "latest");
        return p;
    }

}
```

