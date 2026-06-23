# 第三方 ML 模型 — 许可与归属声明（NOTICE）

本仓库记录 image-processor / CompressX 所托管与再分发的 ONNX 推理模型的许可证与归属，
满足各上游许可证的再分发要求。

> 校验：App 下载后由 `ModelDownloadManager` 做 SHA-256 校验
> （`image-processor-swift/Sources/ImageProcessorSDK/ML/ModelDownloadManager.swift`）。
> 下列哈希即代码中 pin 的值。

---

## 1. esrgan_x2.onnx — 超分辨率

| 项 | 内容 |
|----|------|
| 用途 | 2× AI 超分辨率（Real-ESRGAN） |
| 上游模型 | **Real-ESRGAN** — Xintao Wang 等，<https://github.com/xinntao/Real-ESRGAN> |
| 许可证 | **BSD 3-Clause**（见 `LICENSES/Real-ESRGAN-LICENSE.txt`） |
| 论文 | Xintao Wang 等. *Real-ESRGAN: Training Real-World Blind Super-Resolution with Pure Synthetic Data.* ICCVW 2021. |
| 制品来源 | HuggingFace 社区转换 `JoPmt/Real_Esrgan_x2_Onnx_Tflite_Tfjs`（PyTorch → ONNX） |
| 文件大小 | 67,116,767 字节 |
| **SHA-256** | `4b650e0f8cc10057635e24ac411902a54307401fb6c9f265bf6e1eaf9ae7c949` |

**BSD-3 合规**：再分发需保留版权声明与本许可证文本；不得未经书面许可使用版权持有人
名义为衍生产品背书（第 3 条）。

---

## 2. u2net.onnx — 背景移除

| 项 | 内容 |
|----|------|
| 用途 | 显著性分割 / 背景移除（U²-Net） |
| 上游模型 | **U²-Net** — Xuebin Qin 等，<https://github.com/xuebinqin/U-2-Net> |
| 许可证 | **Apache License 2.0**（见 `LICENSES/U-2-Net-LICENSE.txt`） |
| 论文 | Xuebin Qin 等. *U²-Net: Going Deeper with Nested U-Structure for Salient Object Detection.* Pattern Recognition, 2020. |
| 转换工具 | **rembg** — Daniel Gatis，<https://github.com/danielgatis/rembg>，**MIT**（见 `LICENSES/rembg-LICENSE.txt`） |
| 制品来源 | rembg GitHub Release v0.0.0 的 `u2net.onnx` |
| 文件大小 | 175,997,641 字节 |
| **SHA-256** | `8d10d2f3bb75ae3b6d527c77944fc5e7dcd94b29809d47a739a7a728a912b491` |

**Apache-2 合规**：再分发需保留许可证文本与本归属声明。该 `.onnx` 为 U²-Net 预训练权重
的 ONNX 格式转换制品（由 rembg 完成）；权重 Apache-2 条款随制品延续。转换工具 rembg 为 MIT。

---

## 3. lpips_alex.onnx — 感知差异（LPIPS）

| 项 | 内容 |
|----|------|
| 用途 | 双图感知距离（衡量压缩感知损失；越低越好） |
| 上游模型 | **LPIPS (Learned Perceptual Image Patch Similarity)** — Richard Zhang 等，<https://github.com/richzhang/PerceptualSimilarity> |
| 许可证 | **BSD**（见 `LICENSES/LPIPS-LICENSE.txt`）；骨干 AlexNet（PyTorch，BSD） |
| 论文 | Richard Zhang 等. *The Unreasonable Effectiveness of Deep Features as a Perceptual Metric.* CVPR 2018. |
| 制品来源 | 本项目 PyTorch→ONNX 导出（`lpips.LPIPS(net='alex')`，legacy exporter 权重内嵌） |
| 输入 / 输出 | 双输入 `img1`/`img2`（[batch,3,h,w]，[-1,1]，256×256）→ `distance`（标量） |
| 文件大小 | 9,897,322 字节 |
| **SHA-256** | `6680f203b2e44e288a337e17828d17d5fa353ecd1f2433f2cb172d3c366c4246` |

**BSD 合规**：再分发需保留版权声明（© 2018 Richard Zhang 等）与许可证文本。

---

## 再分发清单

本仓库以 Git LFS 跟踪并以 GitHub Release 分发以下文件，随附上述四项许可证文本：

- `esrgan_x2.onnx`（Real-ESRGAN，BSD-3）— App 使用中
- `u2net.onnx`（U²-Net，Apache-2；经 rembg MIT 工具转换）— App 使用中
- `lpips_alex.onnx`（LPIPS，BSD）— App 使用中（感知差异）

> 历史：曾托管 `quality_assessment.onnx`（Swin Transformer），经实测对真实质量变化无区分度，
> 已移除并改用语义正确的 LPIPS。若未来调整模型，须同步更新本声明、对应 LICENSE 文件、
> README，以及 App 侧 `ModelDownloadManager` 中的 `expectedSHA256`/`remoteURL`。
