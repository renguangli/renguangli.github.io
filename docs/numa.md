numa 是一种关于多个 cpu 如何访问内存的架构模型，现在的 cpu 基本都是 numa 架构，linux 内核 2.5 开始支持 numa。

numa 架构简单点儿说就是，一个物理 cpu（一般包含多个逻辑cpu或者说多个核心）构成一个 node，这个 node不仅包括 cpu，还包括一组内存插槽，也就是说一个物理 cpu 以及一块内存构成了一个 node。每个 cpu 可以访问自己 node 下的内存，也可以访问其他 node 的内存，但是访问速度是不一样的，自己 node 下的更快。`numactl --hardware` 命令可以查看 `node` 状况。

