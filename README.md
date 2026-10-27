# AIP201TC Coursework 1

个人课程代码备份，用于在其他设备上重新运行和核对结果。Notebook 保留已完成的代码、英文分析以及原始运行输出；上传时没有更改模型或实验参数。

## 文件

- `AIP201TC_CW1_2468905.ipynb`：完整 Notebook，按从上到下的顺序执行。
- `requirements.txt`：老师提供的原始依赖文件，保持原样。
- `requirements-reproduce.txt`：在课程依赖之外补充绘图、Notebook 执行和兼容依赖。
- `reference_results.json`：原电脑的两项测试准确率，供复现后比较。
- `PDF_REVIEW.md`：当前导出 PDF 的排版问题与处理方案。

数据集不会上传；Notebook 首次运行时会自动下载 Fashion-MNIST 到相对路径 `data/`。仓库没有包含课程说明 PDF、封面或本机环境目录。

## 新设备环境

原验证环境是 Windows、Python 3.12.15、PyTorch 2.5.1+cu121 和 NVIDIA RTX 4060 Laptop GPU。

以下步骤面向 Windows/Linux x86-64。原依赖固定了 CUDA 12.1 的 PyTorch 构建；不能原样用于 macOS/Apple Silicon。无可用 CUDA 时，Notebook 会自动使用 CPU，但完整训练明显更慢。其他操作系统、驱动和硬件尚未完成全量验证。

先安装 Git、Conda 和 VS Code 的 Python/Jupyter 扩展，再在终端执行：

```shell
git clone https://github.com/Anon-Tokyo9527/AIP201TC-CW1.git
cd AIP201TC-CW1
conda create -n aip201tc-cw1 python=3.12.15 -y
conda activate aip201tc-cw1
python -m pip install -r requirements-reproduce.txt
python -m pip check
python -m ipykernel install --user --name aip201tc-cw1 --display-name "Python (aip201tc-cw1)"
```

这是私有仓库，克隆时需要使用有权限的 GitHub 账号登录。使用 GitHub CLI 的设备也可以执行 `gh repo clone Anon-Tokyo9527/AIP201TC-CW1`。

在 VS Code 打开仓库文件夹，打开 Notebook，选择 `Python (aip201tc-cw1)` 内核，然后使用 **Restart Kernel and Run All**。不要只反复运行标准化单元格；数据载入单元格应先恢复原始输入。

## 命令行完整复现

在已激活的环境和仓库根目录运行：

```shell
python -m nbconvert --to notebook --execute AIP201TC_CW1_2468905.ipynb --output verified.ipynb --output-dir verification --ExecutePreprocessor.kernel_name=aip201tc-cw1 --ExecutePreprocessor.timeout=-1
```

该命令从头执行所有单元格，遇到异常时停止，并将成功执行的 Notebook 保存到 `verification/verified.ipynb`，不覆盖仓库内原 Notebook。课程报告函数另在工作目录生成 `results.json`。

## 核对结果

此前在原电脑完成了 8 轮实验、每轮 25 个 epoch，共 200 个 epoch 的完整验证，没有执行错误。CPU 仅做过小样本、各模型一轮训练的检查，并非全量 CPU 复现。

| 项目 | 原电脑结果 |
| --- | ---: |
| 训练 / 验证 / 测试样本数 | 51,000 / 9,000 / 10,000 |
| MLP 测试准确率 | 87.92% |
| CNN 测试准确率 | 90.79% |
| MLP / CNN 参数数 | 101,770 / 20,586 |
| MLP / CNN 最佳验证 epoch | 8 / 7 |
| 最大双向混淆类别对 | T-shirt/top 与 Shirt |
| 双向混淆总数 | 215 |

先确认所有单元格无异常、每次训练有 25 条记录、图表完整、`results.json` 成功生成，再比较数值。不同设备的浮点运算和底层实现可能造成差异，不要求逐位相同。若差异较大，先核对依赖版本、固定种子、数据划分、是否从头执行以及是否恢复最佳验证权重。

当前文字分析引用的是原电脑结果；如果后续以另一组运行结果作为最终提交版本，应同步检查分析中的数字。
