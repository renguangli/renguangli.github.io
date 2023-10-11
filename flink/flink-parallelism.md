```
package com.renguangli.flink.datastream.parallelism;

import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;

/**
 * flink-sample
 *
 * @author renguangli Created at 2020/10/10 15:00
 * @since JDK1.8
 */
public class ParallelismSamples {

    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();

        // 1. 在上下文环境中设置并行度
        env.setParallelism(1);

		2.在算子上设置并行度
        env.generateSequence(1, 100)
                .filter(v -> v % 2 == 0).setParallelism(1) // 
                .print();

        // 3.客户端启动任务时设置并行度
        // flink run -c com.renguangli.flink.datastream.parallelism.ParallelismSamples -p 3 flink-sample-SNAPSHOT.jar

        // 4.在 flink-conf.yaml 配置文件中设置
        // parallelism.default: 1

        // 4 种并行度设置的优先级从高到底依次为：
        // 算子 ->  上下文环境 -> flink run -d -p 3 -> flink-config.yml ： parallelism.default: 1
        env.execute();
    }
}

```

