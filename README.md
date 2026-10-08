# hpc-learning

高性能计算（HPC）学习计划与实践笔记。

**目标**：从并行编程基础出发，系统掌握共享内存（OpenMP）、分布式内存（MPI）、异构计算（CUDA）三类并行模型，并能用性能分析工具定位并消除瓶颈。

---

## 环境准备

| 组件 | 版本 / 说明 |
| --- | --- |
| WSL2 Ubuntu | 22.04 / 24.04 |
| gcc / g++ | 13+（自带 OpenMP） |
| OpenMPI | 4.x |
| CUDA Toolkit | 12.x（需 NVIDIA GPU） |
| 性能分析 | perf、gprof、Nsight Systems / Compute（可选 Intel VTune） |

```bash
sudo apt update
sudo apt install -y build-essential gdb openmpi-bin libopenmpi-dev
gcc --version && mpirun --version
```

---

## 学习路线

### 第 1 阶段 · 并行计算基础

- [ ] 为什么需要并行：摩尔定律放缓、多核普及与内存墙
- [ ] 并行层次：指令级 / 数据级 / 线程级 / 进程级
- [ ] 性能度量：加速比、并行效率、Amdahl 定律、Gustafson 定律
- [ ] **实验**：测量串行程序在不同问题规模下的耗时，估算理论加速上限

### 第 2 阶段 · OpenMP（共享内存并行）

- [ ] `#pragma omp parallel for`、线程数控制与调度策略（static / dynamic / guided）
- [ ] 数据竞争与同步：`critical`、`atomic`、`reduction`
- [ ] 任务并行：`task`、`sections`，以及 `nowait` / `barrier`
- [ ] **Lab**：并行归约求和、矩阵乘法、数值积分求 π
- [ ] **常见坑**：伪共享（false sharing）、线程绑定（`OMP_PROC_BIND`）、`schedule` 选择

### 第 3 阶段 · MPI（分布式内存并行）

- [ ] 点对点通信：`MPI_Send` / `MPI_Recv`，死锁成因与 `MPI_Sendrecv` 化解
- [ ] 集合通信：`Bcast` / `Scatter` / `Gather` / `Reduce` / `Allreduce`
- [ ] 非阻塞通信、通信子（Communicator）与进程拓扑
- [ ] **Lab**：并行 Jacobi 迭代求解、MPI 求 π、分块矩阵乘法
- [ ] **常见坑**：负载不均衡、通信开销盖过计算收益、缓冲区大小

### 第 4 阶段 · CUDA（GPU 异构计算）

- [ ] 线程层次：grid / block / thread，warp 与 SIMT 执行模型
- [ ] 内存层次：global / shared / register / constant，合并访存（coalescing）
- [ ] 同步与 `__syncthreads()`、共享内存规约
- [ ] **Lab**：向量加法 → 矩阵乘法（含 shared memory tiling）→ 并行规约
- [ ] **常见坑**：warp divergence、bank conflict、occupancy 与寄存器压力

### 第 5 阶段 · 性能分析与调优

- [ ] 计时手段：`time`、`omp_get_wtime()`、`MPI_Wtime()`、CUDA events
- [ ] `perf stat` / `perf record` + 火焰图
- [ ] Roofline 模型与算术强度（arithmetic intensity）
- [ ] 强扩展性 / 弱扩展性（strong / weak scaling）曲线
- [ ] **Lab**：为自己的实现绘制性能曲线，定位瓶颈并迭代优化

---

## 目录规划

```
hpc-learning/
├── 01-basics/       # 并行基础与性能度量实验
├── 02-openmp/       # OpenMP 示例与 Lab
├── 03-mpi/          # MPI 示例与 Lab
├── 04-cuda/         # CUDA 示例与 Lab
├── 05-profiling/    # 性能分析与调优
├── scripts/         # 编译 / 运行 / 绘图脚本
└── notes/           # 学习笔记与踩坑记录
```

## 编译与运行约定

```bash
# OpenMP
gcc -O2 -fopenmp sum.c -o sum && ./sum

# MPI
mpicc -O2 jacobi.c -o jacobi && mpirun -np 4 ./jacobi

# CUDA
nvcc -O2 -arch=native matmul.cu -o matmul && ./matmul
```

## 参考资料

- Peter Pacheco,《并行程序设计导论》
- Timothy Mattson 等,《Patterns for Parallel Programming》
- LLNL HPC Tutorials：<https://hpc-tutorials.llnl.gov/>
- CUDA C++ Programming Guide：<https://docs.nvidia.com/cuda/cuda-c-programming-guide/>
- OpenMP 5.2 规范：<https://www.openmp.org/specifications/>
