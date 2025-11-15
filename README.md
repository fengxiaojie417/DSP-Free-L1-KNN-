基于FPGA的自适应K值L1-KNN人脸识别加速器

本项目实现了一个用于人脸识别的 K-最近邻（KNN）算法加速器。与传统的KNN加速器不同，本项目具有两大核心创新点：

DSP-Free 硬件设计：使用曼哈顿距离（L1-Norm）代替欧氏距离（L2-Norm），使得距离计算完全不依赖FPGA中的乘法器（DSP），仅使用加法器和查找表（LUT）实现，极大地提高了能效和资源利用率。

自适应K值算法（Adaptive-K）：实现了一种动态K值选择算法，取代了传统KNN中固定的K值。该算法会根据每个查询样本周围的数据分布，动态计算一个**“综合置信度”**，并自动选择最优的K值进行分类。

实验证明（见 adaptive_face_knn_analysis.png），“自适应K值”算法的准确率（92.5%）优于任何固定K值所能达到的最佳准确率（91.25%）。

代码库结构

本代码库包含用于算法验证、软件基准测试和硬件文件生成的完整Python脚本。

.
├── knn_algorithm_validator.py          # 核心：自适应K值KNN算法的Python验证器
├── software_baseline_benchmark.py      # 软件基准测试（Python, NumPy）与FPGA性能估算
├── hardware_testbench_generator.py     # 硬件文件（.mem, .coe, .vh, .h）生成器
├── run_all_tests.py                    # 一键运行所有脚本的主程序
│
├── face_knn_params.vh                  # [已生成] Verilog硬件参数
├── face_knn_data.h                     # [已生成] ARM端C语言头文件
├── face_train_data_bram.mem            # [已生成] BRAM 仿真初始化文件 (训练集)
├── face_train_data.coe                 # [已生成] BRAM 综合初始化文件 (训练集)
├── face_test_vectors.mem               # [已生成] Verilog Testbench 测试向量 (测试集)
│
├── adaptive_face_knn_analysis.png      # [已生成] 自适应K值算法的性能分析图表
├── face_software_baseline_comparison.png # [已生成] 软硬件性能对比基准图表
└── final_report.txt                    # [已生成] 最终的运行总结报告


如何运行

本项目依赖 numpy, scikit-learn 和 matplotlib。

# 1. (可选) 安装依赖
pip install numpy scikit-learn matplotlib

# 2. 运行主控脚本
python run_all_tests.py


run_all_tests.py 脚本将会自动按顺序执行以下所有步骤：

算法验证 (knn_algorithm_validator.py):

在 Olivetti 人脸数据集上验证“自适应K值”算法的性能。

将其与多个“固定K值”的算法进行准确率对比。

生成 adaptive_face_knn_analysis.png。

基准测试 (software_baseline_benchmark.py):

测试纯 Python 和 NumPy 实现L1-KNN（固定K=5）的延迟和吞吐量。

估算FPGA实现（保守/乐观）的性能。

生成 face_software_baseline_comparison.png。

文件生成 (hardware_testbench_generator.py):

为Verilog仿真、综合以及ARM端软件开发生成所有必要的数据和头文件。

所有生成的文件都会保存在当前目录，并生成一份 final_report.txt 总结报告。
