### Map

DataStream → DataStream

遍历数据流中的每一个元素，产生一个新的元素

### FlatMap

DataStream → DataStream

遍历数据流中的每一个元素，产生N个元素 N=0，1，2,......

### Filter

DataStream → DataStream

过滤算子，根据数据流的元素计算出一个boolean类型的值，true代表保留，false代表过滤掉

### KeyBy

DataStream → KeyedStream

根据数据流中指定的字段来分区，相同指定字段值的数据一定是在同一个分区中，内部分区使用的是 HashPartitioner

### Reduce

KeyedStream → DataStream

reduce 是基于分区后的流对象进行聚合

### Aggregations

```
keyedStream.sum(0)
keyedStream.sum("key")
keyedStream.min(0)
keyedStream.min("key")
keyedStream.max(0)
keyedStream.max("key")
keyedStream.minBy(0)
keyedStream.minBy("key")
keyedStream.maxBy(0)
keyedStream.maxBy("key")
```

### Union

**DataStream*** → DataStream

合并两个或者更多的数据流产生一个新的数据流

> 需要保证数据流中元素类型一致

```
package com.renguangli.flink.datastream.transformation;

import org.apache.flink.streaming.api.datastream.DataStream;
import org.apache.flink.streaming.api.datastream.DataStreamSource;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;

/**
 * Union 将两个数据流合并成一个数据流
 * @author renguangli Created at 2020/10/3 17:21
 * @since JDK1.8
 */
public class UnionOperatorSamples {

    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        env.setParallelism(1);

        DataStreamSource<Integer> s1 = env.fromElements(1, 2, 3, 4);
        DataStreamSource<Integer> s2 = env.fromElements(5,6,7);

        // DataStream -> DataStream
        // 两个数据流合并成一个数据流
        // 两个数据流数据类型必须一致
        DataStream<Integer> union = s1.union(s2);
        union.print();
        env.execute();
    }
}
```

### Connect

DataStream,DataStream → ConnectedStreams

合并两个数据流并且保留两个数据流的数据类型，能够共享两个流的状态

```
package com.renguangli.flink.datastream.transformation;

import java.util.HashMap;
import java.util.Map;
import org.apache.flink.api.java.tuple.Tuple2;
import org.apache.flink.streaming.api.datastream.DataStreamSource;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.streaming.api.functions.co.CoFlatMapFunction;
import org.apache.flink.streaming.api.functions.co.CoMapFunction;
import org.apache.flink.util.Collector;

/**
 * flink-sample
 *
 * @author renguangli Created at 2020/10/10 15:38
 * @since JDK1.8
 */
public class ConnectOperatorSamples {

    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        env.setParallelism(1);

        DataStreamSource<String> s1 = env.fromElements("1,23", "2,22", "3,21","1,23", "2,22", "3,21","1,23", "2,22", "3,21");
        DataStreamSource<String> s2 = env.fromElements("1,张飞", "2,曹云安", "3,刘辉");

        // DataStream → ConnectedStreams
        // 合并两个数据流并且保留两个数据流的数据类型，返回值必须一致
        // 使用场景，其中一个流作为 码表保存到本地内存中，问题，不能保证码表先处理

        // CoMap, CoFlatMap并不是具体算子名字，而是一类操作名称
        // 基于ConnectedStreams数据流做map遍历，这类操作叫做 CoMap
        // 基于ConnectedStreams数据流做flatMap遍历，这类操作叫做 CoFlatMap
        // CoFlatMap
        s1.connect(s2).flatMap(new CoFlatMapFunction<String, String, Tuple2<String, Integer>>() {
            final Map<String, String> map = new HashMap<>();

            @Override
            public void flatMap1(String value, Collector<Tuple2<String, Integer>> out) throws Exception {
                String[] split = value.split(",");
                out.collect(Tuple2.of(map.get(split[0]), Integer.parseInt(split[1])));
            }

            @Override
            public void flatMap2(String value, Collector<Tuple2<String, Integer>> out) throws Exception {
                String[] split = value.split(",");
                map.put(split[0], split[1]);
            }
        }).print();


        // CoMap
        s1.connect(s2).map(new CoMapFunction<String, String, Object>() {
            @Override
            public Object map1(String value) throws Exception {
                return value;
            }

            @Override
            public Object map2(String value) throws Exception {
                return value;
            }
        }).print();
        env.execute();
    }
}

```

### CoMap, CoFlatMap

ConnectedStreams → DataStream

CoMap, CoFlatMap 并不是具体算子名字，而是一类操作名称

凡是基于 ConnectedStreams 数据流做 map 遍历，这类操作叫做 CoMap

凡是基于 ConnectedStreams 数据流做 flatMap 遍历，这类操作叫做 CoFlatMap

```
package com.renguangli.flink.datastream.transformation;

import org.apache.flink.streaming.api.datastream.ConnectedStreams;
import org.apache.flink.streaming.api.datastream.DataStreamSource;
import org.apache.flink.streaming.api.datastream.SingleOutputStreamOperator;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.streaming.api.functions.co.CoFlatMapFunction;
import org.apache.flink.streaming.api.functions.co.CoMapFunction;
import org.apache.flink.util.Collector;

/**
 *
 * @author renguangli Created at 2020/10/3 17:21
 * @since JDK1.8
 */
public class ConnectAndCoMapAndCoFlatMapOperatorSamples {

    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        DataStreamSource<Integer> s1 = env.fromElements(1,2,3,4,5);
        DataStreamSource<String> s2 = env.fromElements("Spring","Mybatis","flink");

        // connect
        // dateStream > ConnectedStreams
        // 两个数据流的数据类型可以不一致，返回类型必须一致
        ConnectedStreams<Integer, String> connectStream = s1.connect(s2);

        // ConnectedStreams 进行 Map 操作时需要 传入 CoMapStream
        SingleOutputStreamOperator<Object> coMapStream = connectStream.map(new CoMapFunction<Integer, String, Object>() {
            @Override
            public Object map1(Integer integer) throws Exception {
                return "map1:" + integer;
            }

            @Override
            public Object map2(String s) throws Exception {
                return "map2:" + s;
            }
        });

        // ConnectedStreams 进行 flat 操作时需要 传入 CoFlatMapFunction
        SingleOutputStreamOperator<Object> flatMapStream = connectStream.flatMap(new CoFlatMapFunction<Integer, String, Object>() {
            @Override
            public void flatMap1(Integer integer, Collector<Object> collector) throws Exception {
                collector.collect(integer);
            }

            @Override
            public void flatMap2(String s, Collector<Object> collector) throws Exception {
                collector.collect(s);
            }
        });

        flatMapStream.print();

        env.execute();
    }
}
```

### Split

DataStream → SplitStream

根据条件将一个流分成两个或者更多的流

```
package com.renguangli.flink.datastream.transformation;

import java.util.Collections;
import org.apache.flink.streaming.api.collector.selector.OutputSelector;
import org.apache.flink.streaming.api.datastream.SplitStream;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;

/**
 * flink-sample
 *
 * @author renguangli Created at 2020/10/3 23:14
 * @since JDK1.8
 */
public class SplitSelectOperatorSamples {

    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        // SplitStream -> DataStream
        // split 根据条件将一个流分割成两个或者更多的流
        // 已过期建议使用 sideOutPut 侧输出流
        SplitStream<Long> split = env.generateSequence(1, 10).split((OutputSelector<Long>) aLong -> {
            if (aLong % 2 == 0) {
                return Collections.singletonList("first");
            }
            return Collections.singletonList("last");
        });
        split.select("last").print();
        //split.select("last").print();
        env.execute();
    }
}
```

> @deprecated Please use side output instead

### Select 

SplitStream → DataStream

从SplitStream中选择一个或者多个数据流

```
SplitStream<Long> split = env.generateSequence(1, 10).split((OutputSelector<Long>) aLong -> {
    if (aLong % 2 == 0) {
        return Collections.singletonList("first");
    }
    return Collections.singletonList("last");
});
split.select("last").print();
```

### Side output

侧输出流,流计算过程，可能遇到根据不同的条件来分隔数据流。filter分割造成不必要的数据复制

```
package com.renguangli.flink.datastream.transformation;

import org.apache.flink.api.common.typeinfo.Types;
import org.apache.flink.streaming.api.datastream.SingleOutputStreamOperator;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.streaming.api.functions.ProcessFunction;
import org.apache.flink.util.Collector;
import org.apache.flink.util.OutputTag;

/**
 * flink-sample
 *
 * @author renguangli Created at 2020/10/10 15:54
 * @since JDK1.8
 */
public class SideOutputStreamSamples {

    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        env.setParallelism(1);

        // 末尾大括号必须加上, = 号右边的泛型也必须加上,
//        OutputTag<Long> sideOutputTag = new OutputTag<Long>("side-output-tag"){};

        // 或者 传入第二个参数
        OutputTag<Long> sideOutputTag = new OutputTag<Long>("side-output-tag", Types.LONG);

        SingleOutputStreamOperator<Long> stream = env.generateSequence(1, 100)
                .process(new ProcessFunction<Long, Long>() {
                    @Override
                    public void processElement(Long value, Context ctx, Collector<Long> out) throws Exception {
                        if (value % 2 == 0) {
                            out.collect(value);
                        } else {
                            ctx.output(sideOutputTag, value);
                        }
                    }
                });
//        stream.print("main");  // 打印主流
        stream.getSideOutput(sideOutputTag).print(); // 打印主流
        env.execute();

    }
}
```

### Iterate

DataStream → IterativeStream → DataStream

Iterate算子提供了对数据流迭代的支持

迭代由两部分组成：迭代体、终止迭代条件

不满足终止迭代条件的数据流会返回到stream流中，进行下一次迭代

满足终止迭代条件的数据流继续往下游发送

```
package com.renguangli.flink.datastream.transformation;

import org.apache.flink.streaming.api.datastream.IterativeStream;
import org.apache.flink.streaming.api.datastream.SingleOutputStreamOperator;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;

/**
 * flink-sample
 *
 * @author renguangli Created at 2020/10/4 0:06
 * @since JDK1.8
 */
public class IterarteOperatorSamples {

    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();

        // DataStream → IterativeStream → DataStream
        // Iterate 算子提供了对数据流迭代的支持
        // 迭代由两部分组成：迭代体、终止迭代条件

        IterativeStream<Integer> iterate = env.fromElements(1, 2, 3, 4)
                .iterate();

        // 不满足终止迭代条件的数据流会返回到 stream 流中，进行下一次迭代
        SingleOutputStreamOperator<Integer> filter = iterate.filter(value -> value > 2)
                .map(value -> value - 1).setParallelism(1); // feedbackStream 的并行度必须和原始流的并行度一致
        iterate.closeWith(filter);

        // 满足终止迭代条件的数据流继续往下游发送
        SingleOutputStreamOperator<Integer> stream = iterate.filter(value -> value <= 2);
        stream.print();

        env.execute();
    }
}
```