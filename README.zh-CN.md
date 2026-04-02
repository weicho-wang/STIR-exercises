# STIR 练习（中文译文）

> 说明：这是对 `README.md` 的中文译读版，保留原文的练习流程、命令与问题引导，便于直接上手课程实验。

本材料用于基于 [STIR](http://stir.sourceforge.net) 的 PET / SPECT 图像重建实践演示。

本仓库包含帮助你快速开始 STIR 的练习内容。开始前请先阅读 `INSTALL.md`（如果你不是用已预装环境的虚拟机）。

当前练习版本至少需要 STIR 4.0。

作者：
- Kris Thielemans
- Charalampos Tsoumpas（散射与运动练习初始版本）
- 文档补充：Elisabetta Grecchi、Irene Polycarpou

许可证见 `LICENSE.txt`。

---

## 介绍

这些练习会带你接触 PET/SPECT 中的前向投影、图像重建、散射等内容。练习过程会生成模拟数据，主要基于两套数据：

- **胸腔（Thorax）数据**：来自 Tsoumpas et al 2013 Phys Med Biol，使用两个呼吸门控位置的 FDG PET 胸腔体模及对应 CTAC。
- **脑（Brain）数据**：来自 [BrainWeb](http://www.bic.mni.mcgill.ca/brainweb/)，使用分割脑图。

通常你只需跑 brain 或 thorax 其中一套（运动校正练习目前主要是 thorax）。

输入数据位于 `EX_*` 目录；但**必须在 STIR-exercises 根目录执行脚本**。

练习评估建议使用 Python：
- `python/*.py`（推荐用 Spyder）
- `notebooks/*.ipynb`（Jupyter Notebook）

也提供命令行评估方法（如 AMIDE 查看数据），但不如 Python 方便。

---

## 开始练习

在终端：

```bash
export STIR_exercises_PATH=~/devel/STIR-exercises
cd $STIR_exercises_PATH
```

启动：

```bash
jupyter notebook &
# 或
spyder &
```

注意：
- `*.sh` 脚本正常结束通常会输出 `DONE` / `Done`。
- 若失败，请检查 `working_folder/...` 下对应 `*.log`。
- 可通过 `./run_all.sh` 预先批量生成数据（耗时较长）。

---

## 练习 1：脑部数据模拟（Brain）

运行：

```bash
./run_simulation_brain.sh
```

特点：脑体模模拟；含 PSF；散射为 0；随机为常数。

评估：
- Spyder：`python/evaluate_simulation_brain.py`
- Notebook：`notebooks/evaluate_simulation_brain.ipynb`
- 命令行：`extract_segments` 后用 AMIDE 查看。

---

## 练习 2：胸腔 PET 数据模拟（Thorax）

运行：

```bash
./run_simulations_thorax.sh
```

特点：2 个门控；不含 PSF；散射由 STIR 模拟；随机为常数。

评估：
- Spyder：`python/evaluate_simulation_thorax.py`
- Notebook：`notebooks/evaluate_simulation_thorax.ipynb`
- 命令行：提取 GATE1/GATE2 的 sinogram，比较差异。

---

## 练习 3：胸腔单层 SPECT 模拟

运行：

```bash
./run_simulation_SPECT.sh
```

输出：`working_folder/single_slice_SPECT`

评估：
- Spyder：`python/evaluate_simulation_SPECT.py`
- Notebook：`notebooks/evaluate_simulation_SPECT.ipynb`

---

## 可选练习 1：用 Python 直接做模拟

演示如何在 Python 中直接用 STIR 类进行前后投影等操作。

- 脚本：`python/projection_demo.py`
- Notebook：`notebooks/projection_demo.ipynb`

---

## 练习 4：重建前准备（单层胸腔数据）

运行：

```bash
./run_simulation_single_slice.sh
```

输出：`working_folder/single_slice`

---

## 练习 5：图像重建 Part 1（EMML / OSEM）

运行（按数据类型选其一）：

```bash
./run_reconstruction_thorax.sh
./run_reconstruction_brain.sh
./run_reconstruction_SPECT.sh
```

内容：FBP、EMML(MLEM)、OSEM，多轮迭代并保存中间结果；末尾含高斯后滤波结果。

评估：
- `python/evaluate_reconstruction_brain.py`（可修改 `os.chdir(...)` 指向 thorax 或 SPECT）
- 或旧脚本 `old_evaluation_scripts/evaluate_reconstruction_*.sh`

---

## 可选练习 2：更小体素重建

运行：

```bash
run_reconstruction_single_slice_thorax_with_zoom.sh
```

比较：标准像素、半像素（双倍像素数）、半像素+更多射线追踪。

评估：`python/evaluate_reconstruction_single_slice_zoom.py`

---

## 可选练习 3：在 Python 中直接重建

- 脚本：`python/reconstruction_demo.py`
- Notebook：`notebooks/reconstruction_demo.ipynb`

---

## 练习 6：图像重建 Part 2（加入泊松噪声）

可用 `poisson_noise` 给模拟数据加噪声。示例：

```bash
./run_generate_Poisson_thorax.sh 0.5
```

说明：
- 缩放因子越小，噪声越大。
- 脚本会先备份无噪声数据，再生成噪声数据并存入 `noise_*` 子目录。
- 可从 `noiseless` 目录恢复无噪声数据。

---

## 练习 7：图像重建 Part 3（MAP）

运行：

```bash
./run_reconstruction_thorax_MAP.sh
./run_reconstruction_brain_MAP.sh
./run_reconstruction_SPECT_MAP.sh
```

内容：OSL / OSSPS + 二次先验（Quadratic Prior），通常从已有 OSEM 结果继续。

评估：`python/evaluate_reconstruction_brain_MAP.py`

---

## 练习 8：随机事件估计（Randoms）

运行：

```bash
./run_randoms.sh
```

内容：基于 delayed coincidences 的最大似然 singles 估计，再构建 randoms 估计。

评估：`python/evaluate_randoms.py`

---

## 练习 9：散射校正（Scatter Correction）

可运行 `run_scatter_0.sh`、`run_scatter_1.sh`、`run_scatter_3.sh`（run2 当前不可用）。

你应先运行：

```bash
./run_simulations_thorax.sh
```

评估脚本：
- `python/evaluate_scatter_run0.py`
- `python/evaluate_scatter_run3.py`

也可使用 AMIDE + `extract_segments`、`stir_subtract` 做对比。

---

## 练习 10：运动校正（Motion Correction）

依赖 `run_simulations_thorax.sh` 输出。脚本：

- `run_MCIR_0.sh`：有运动校正（正确运动向量）
- `run_MCIR_1.sh`：无运动校正
- `run_MCIR_2.sh`：运动校正 + 平均衰减图
- `run_MCIR_3.sh`：运动校正 + g1 衰减图

评估脚本：
- `evaluate_MCIR_part_I.py`
- `evaluate_MCIR_part_II.py`

---

## 练习 11：图像重建 Part 4（PSF + MAP）

推荐先做 SPECT。建议先完成：

```bash
./run_simulation_SPECT.sh
./run_reconstruction_SPECT.sh
./run_reconstruction_SPECT_MAP.sh
```

再运行：

```bash
./run_reconstruction_SPECT_PSF.sh
```

评估：`python/evaluate_reconstruction_SPECT_PSF.py`

---

## 练习 12：图像重建 Part 5（PSF + 解剖先验 PLS）

示例（brain）：

```bash
./run_simulation_brain.sh
./run_reconstruction_brain.sh
./run_reconstruction_brain_PSF.sh
./run_reconstruction_brain_PLS.sh
```

内容：比较是否使用 PSF、是否使用 PLS 先验（如 `OSMAPOSLPSF_PLS.par`）。

评估：`python/evaluate_reconstruction_brain_PLS.py`

---

## 附录

### 常见文件后缀

- `.hv`：图像 Interfile 头
- `.ahv`：旧式图像头（可忽略）
- `.v`：图像原始 float 数据
- `.hs`：投影数据（sinogram）Interfile 头
- `.s`：投影数据原始 float 数据
- `.par`：STIR 参数文件
- `.sh`：Shell 脚本
- `.bat`：Windows 批处理脚本
- `.log`：日志
- `.py`：Python 脚本

### Python / Jupyter / Spyder

- Jupyter：逐 cell 执行，常用快捷键 `Ctrl+Enter`、`Shift+Enter`、`Tab`。
- Spyder：可按 cell（`#%%`）执行，`F9` 执行选中代码。
- iPython 常用 magic：`%matplotlib`、`%matplotlib inline`、`%reset` 等。

### Python 中查看帮助

```python
help(stir.Scanner)
? stir.Scanner
```

很多 STIR 类有 `parameter_info()` 可导出参数说明。

### 不用 Python 的显示方式

可使用 AMIDE 直接读 Interfile，或用 ImageJ 导入 `.v` 原始浮点数据。

### 常用 STIR 命令（命令行）

```bash
list_projdata_info projdata.hs
list_image_info image.hv
OSMAPOSL someParameterFile.par
FBP2D someParameterFile.par
OSSPS someParameterFile.par
list_image_values prof.txt input_image min_plane max_plane min_row max_row min_col max_col
extract_segments projdata.hs
```

---

如果你希望，我可以继续做一个“逐段严格对照翻译版”（英文一段 + 中文一段），用于课程讲义或提交材料。