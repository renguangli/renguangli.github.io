Flink 内置很多 sink，可以将 Flink 处理后的数据输出到 HDFS、kafka、Redis、ES、MySQL 等等

### Redis Sink

添加依赖

```
<dependency>
    <groupId>org.apache.bahir</groupId>
    <artifactId>flink-connector-redis_2.11</artifactId>
    <version>1.0</version>
</dependency>
```

代码示例

```
package com.renguangli.flink.datastream.sink;

import java.util.stream.Stream;
import org.apache.flink.api.common.typeinfo.Types;
import org.apache.flink.api.java.tuple.Tuple2;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.streaming.connectors.redis.RedisSink;
import org.apache.flink.streaming.connectors.redis.common.config.FlinkJedisPoolConfig;
import org.apache.flink.streaming.connectors.redis.common.mapper.RedisCommand;
import org.apache.flink.streaming.connectors.redis.common.mapper.RedisCommandDescription;
import org.apache.flink.streaming.connectors.redis.common.mapper.RedisMapper;

/**
 * flink-sample
 *
 * @author renguangli Created at 2020/10/4 13:07
 * @since JDK1.8
 */
public class RedisSinkSamples {

    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();

        env.setParallelism(1);

        FlinkJedisPoolConfig config = new FlinkJedisPoolConfig.Builder()
                .setHost("192.168.10.16")
                .setPort(6379).build();

        env.fromElements("hello spark", "hello flink", "hello java")
                .flatMap((value, collector) -> Stream.of(value.split(" ")).forEach(collector::collect)).returns(Object.class)
                .map(value -> Tuple2.of(value, 1)).returns(Types.TUPLE(Types.STRING, Types.INT))
                .keyBy(value -> value.f0)
                .reduce((v1, v2) -> Tuple2.of(v1.f0, v1.f1 + v2.f1))
                .addSink(new RedisSink<>(config, new RedisMapper<Tuple2<Object, Integer>>() {
                    @Override
                    public RedisCommandDescription getCommandDescription() {
                        return new RedisCommandDescription(RedisCommand.HSET, "wc");
                    }
                    @Override
                    public String getKeyFromData(Tuple2<Object, Integer> tuple2) {
                        return tuple2.f0.toString();
                    }
                    @Override
                    public String getValueFromData(Tuple2<Object, Integer> tuple2) {
                        return tuple2.f1.toString();
                    }
                }));
        env.execute();

    }
}
```

### Jdbc sink

添加依赖

```
 <dependency>
 <groupId>org.apache.flink</groupId>
 <artifactId>flink-connector-jdbc_2.11</artifactId>
 <version>1.11.1</version>
 </dependency>
```

> 从 1.11.0 版本开始支持

代码示例

```
package com.renguangli.flink.datastream.sink;

import com.mysql.jdbc.Driver;
import org.apache.flink.connector.jdbc.JdbcConnectionOptions;
import org.apache.flink.connector.jdbc.JdbcSink;
import org.apache.flink.connector.jdbc.JdbcStatementBuilder;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;

/**
 * huaneng-cn-alarm-task
 *
 * @author renguangli Created at 2021/6/18 16:53
 * @since JDK1.8
 */
public class JdbcSinkSamples {

    // jdbc sink : since 1.11.0
    public static void main(String[] args) throws Exception {
        String sql = "insert into user(username) values(?)";
        JdbcConnectionOptions jdbcOpts = new JdbcConnectionOptions.JdbcConnectionOptionsBuilder()
                .withDriverName(Driver.class.getName())
                .withUrl("jdbc:mysql://localhost:3306/test?useSSL=false")
                .withUsername("root")
                .withPassword("root")
                .build();

        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        env.fromElements("zhangsan", "lisi", "wangwu")
                .addSink(JdbcSink.sink(sql,
                        (JdbcStatementBuilder<String>) (pt, s) -> pt.setString(1, s),
                        jdbcOpts
                ));

        env.execute("jdbc sink");
    }
}
```

### Kafak sink

添加依赖

```
<dependency>
    <groupId>org.apache.flink</groupId>
    <artifactId>flink-connector-kafka_2.11</artifactId>
    <version>1.11.1</version>
</dependency>
```

代码示例

```
package com.renguangli.flink.datastream.sink;

import org.apache.flink.api.common.serialization.SimpleStringSchema;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.streaming.connectors.kafka.FlinkKafkaProducer;

/**
 * flink-samples
 *
 * @author renguangli Created at 2021/6/18 17:21
 * @since JDK1.8
 */
public class KafkaSinkSamples {
    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        env.fromElements("kafka").returns(String.class)
                .addSink(new FlinkKafkaProducer<>(
                        "localhost:9092",
                        "test",
                        new SimpleStringSchema()));
        env.execute("kafka sink");
    }
}
```

### File sink

### Custom Sink