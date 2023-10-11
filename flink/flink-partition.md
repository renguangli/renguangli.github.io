```
package com.renguangli.flink.datastream.partition;

import org.apache.flink.streaming.api.datastream.DataStreamSource;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;

/**
 * flink-sample
 *
 * @author renguangli Created at 2020/10/4 10:15
 * @since JDK1.8
 */
public class ShufflePartition {

    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        //  1.shuffle 随机分配到下游分区
        //  @Override
        //	public int selectChannel(SerializationDelegate<StreamRecord<T>> record) {
        //		return random.nextInt(numberOfChannels);
        //	}
        // 使用场景
        DataStreamSource<Long> stream = env.generateSequence(1, 10).setParallelism(2);
        stream.writeAsText("./data/stream1").setParallelism(2);
        stream.shuffle().writeAsText("./data/stream2").setParallelism(4);
        // 1.分区元素随机均匀分发到下游分区，网络开销比较大
        stream.rebalance(); // 2.轮询分区元素，均匀的将元素分发到下游分区，下游每个分区的数据比较均匀，
        // 在发生数据倾斜时非常有用，网络开销比较大
        stream.rescale();// 4.通过轮询分区元素，将一个元素集合从上游分区发送给下游分区，发送单位是集合，而不是一个个元素
                        //注意：rescale发生的是本地数据传输，而不需要通过网络传输数据，比如 taskmanager 的槽数。
                        //简单来说，上游的数据只会发送给本 TaskManager 中的下游
        stream.global(); // 5.上游分区的数据只分发给下游的第一个分区
        stream.broadcast(); // 6.上游中每一个元素内容广播到下游每一个分区中
//        stream.partitionCustom()7. 自定义分区策略 

        //
        env.execute();
    }
}

```