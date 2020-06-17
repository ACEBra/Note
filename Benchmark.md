# Benchmark
http://hpctest.cs.tsinghua.edu.cn/programs.html

## 自主研发
- SIM-MPI
一款模拟并行程序通信行为的trace-driven模拟器.

- HPC_Net_Checker
该测试程序用来检查大规模集群系统中网络链接通信性能存在故障的节点.

- PBB
一款面向生物信息学领域的基准测试程序集，该程序包括7个用OpenMP并行的生物信息学测试程序，BLAST, PLSA, MUSCLE, Rosetta, Semphy, ModuleNet, SNP.

- TPC-C
我们按照TPC-C最新标准5.11设计并实现了一套TPC-C评测程序，并对国产高端服务器进行了性能评测。

## Micro-Benchmark
- OSU MPI Benchmarks
下载地址：http://mvapich.cse.ohio-state.edu/benchmarks/
简介：由Ohio State University提供，程序生成不同规模的数据并执行各种不同模式的MPI通信，测试 各种通信模式的带宽和延迟。分为点对点通信和组通信两种形式，其中点对点通信重点关注拓扑结构中非 等价节点之间的通信性能。

- STREAM
下载地址：http://www.cs.virginia.edu/stream/
简介：由Virginia University提供，通过生成四种不同模式下的内存读写操作，用于测试高性能计算机的内存带宽。 现代计算机中都是用缓存技术，为了保证测试正确反映计算机内存的读写性能，测试中使用的数据量应远大于缓存大小。

- Intel MPI Benchmarks
下载地址：http://software.intel.com/en-us/articles/intel-mpi-benchmarks
简介：由Intel公司提供，通过进行MPI点对点通信和全局通信在不同的消息长度下的消息传送测试统的性能， 包括：节点性能，网络延迟和吞吐量，测量值也反映使用的MPI实现的效率。

- IOzone Filesystem Benchmark
下载地址：http://www.iozone.org
简介:IOzone是一个开源软件，生成一系列的文件操作并进行测量，以评价文件系统的性能。

- IOR HPC Benchmark
下载地址：http://sourceforge.net/projects/ior-sio/
简介：IOR用于测试并行文件系统的性能，包括POSIX，MPIIO以及HDF5。

- LMbench
下载地址：http://www.bitmover.com/lmbench/
简介：由BitMover公司提供，程序测试内存访问和常用系统调用的带宽和延迟。

## Kernel Benchmark
- SPEC CPU
下载地址：http://spec.org
简介：由The Standard Performance Evaluation Corporation提供， 程序测试计算密集型程序在高性能计算机上的计算效率，包含SPEC INT和SPEC FP， 分别对应整型计算机与浮点计算。根据编译方式的不同，可以分为base测试和peak测试，peak测试使用对程序包中的每个程序 使用单独的编译选项，相较于base模式会有更好的性能。根据运行方式的不同，可以分为rate测试和speed测试，rate测试 衡量计算机能够同时执行的大规模应用程序的吞吐量，speed测试衡量计算机最快速的完成某项应用程序的耗时， SPEC是一个非营利组织。

- SPEC OMP
下载地址：http://spec.org
简介：SPEC OMP用于测试使用OpenMP进行共享内存并行运算的应用的性能。 SPEC OMP还包括一项测量功耗的可选值。

- SPEC MPI
下载地址：http://spec.org
简介：SPEC MPI测试各种不同的SMP硬件以及集群上进行MPI并行，浮点运算，计算密集的性能。 SPEC MPI包括MPIM和MPIL两个套件，其中MPIL的运行时间以及实用的数据集都会更大。

- HPL
下载地址：http://www.netlib.org/benchmark/hpl/
简介：由the University of Tennessee提供，通过在分布式系统上求解双精度 稠密线性方程组测试高性能计算机的整体计算能力。linpack是高性能计算机应用的代表，也是TOP500计算机排名的唯一指标。

- NAS Parallel Benchmarks
下载地址：http://www.nas.nasa.gov/publications/npb.html
简介：NPB是一组用于测试超级计算机性能的程序，程序由一组计算流体力学程序演变而来， 最初的版本包括5个计算内核和3个伪应用程序，后来新增了非结构化可适应性网格， 并行IO，多区域应用以及计算网格的内容。求解问题的规模可以根据需求调整。

- HPC Challenge Benchmark
下载地址：http://icl.cs.utk.edu/hpcc/
简介：HPCC是由the University of Tennessee整理和提供的一组用于测试高性能计算机各方面性能的程序， 涵盖了高性能计算机性能测试的各个方面，著名的benchmark如stream，hpl程序包中都有包含。

## Application
- Parallel Ocean Program (POP)
下载地址：http://climate.lanl.gov/Models/POP/
简介：POP 是著名的并行海洋模拟程序

- GRAPES
简介：GRAPES 是中国气象局自主研制的数值天气预报业务系统。数值天气预 报模型主要内容是求解和气象过程相关的数值方程,这些过程包括空气流体动力 学过程,热力学过程,水汽和它的相变过程等等。

- mpiBLAST
mpiBLAST是一个生物基因对匹配程序.

## Others
- UnixBench
https://github.com/kdlucas/byte-unixbench
```
https://yq.aliyun.com/articles/674729
Benchmark Run: 一 6月 25 2018 20:25:47 - 20:54:19
4 CPUs in system; running 1 parallel copy of tests

Dhrystone 2 using register variables       30971628.9 lps   (10.0 s, 7 samples)
Double-Precision Whetstone                     3928.1 MWIPS (12.4 s, 7 samples)
Execl Throughput                               3117.6 lps   (30.0 s, 2 samples)
File Copy 1024 bufsize 2000 maxblocks        645027.2 KBps  (30.0 s, 2 samples)
File Copy 256 bufsize 500 maxblocks          229505.4 KBps  (30.0 s, 2 samples)
File Copy 4096 bufsize 8000 maxblocks       1288742.6 KBps  (30.0 s, 2 samples)
Pipe Throughput                             1635960.9 lps   (10.0 s, 7 samples)
Pipe-based Context Switching                 337333.8 lps   (10.0 s, 7 samples)
Process Creation                               8238.2 lps   (30.0 s, 2 samples)
Shell Scripts (1 concurrent)                   5817.0 lpm   (60.0 s, 2 samples)
Shell Scripts (8 concurrent)                   2183.9 lpm   (60.0 s, 2 samples)
System Call Overhead                        2465754.7 lps   (10.0 s, 7 samples)

System Benchmarks Index Values               BASELINE       RESULT    INDEX
Dhrystone 2 using register variables         116700.0   30971628.9   2654.0
Double-Precision Whetstone                       55.0       3928.1    714.2
Execl Throughput                                 43.0       3117.6    725.0
File Copy 1024 bufsize 2000 maxblocks          3960.0     645027.2   1628.9
File Copy 256 bufsize 500 maxblocks            1655.0     229505.4   1386.7
File Copy 4096 bufsize 8000 maxblocks          5800.0    1288742.6   2222.0
Pipe Throughput                               12440.0    1635960.9   1315.1
Pipe-based Context Switching                   4000.0     337333.8    843.3
Process Creation                                126.0       8238.2    653.8
Shell Scripts (1 concurrent)                     42.4       5817.0   1371.9
Shell Scripts (8 concurrent)                      6.0       2183.9   3639.9
System Call Overhead                          15000.0    2465754.7   1643.8
                                                                   ========
System Benchmarks Index Score                                        1362.9

```

- Geekbench
https://www.geekbench.com/

- STREAM
Sustainable Memory Bandwidth in High Performance Computers

http://www.cs.virginia.edu/stream/

- Iperf
iPerf3 is a tool for active measurements of the maximum achievable bandwidth on IP networks. It supports tuning of various parameters related to timing, buffers and protocols (TCP, UDP, SCTP with IPv4 and IPv6). For each test it reports the bandwidth, loss, and other parameters. 

https://iperf.fr/

- sysbench
sysbench is a scriptable multi-threaded benchmark tool based on LuaJIT. It is most frequently used for database benchmarks, but can also be used to create arbitrarily complex workloads that do not involve a database server.

https://github.com/akopytov/sysbench
