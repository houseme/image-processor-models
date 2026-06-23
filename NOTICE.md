# 第三方 ML 模型 — 许可与归属声明（NOTICE）

本仓库记录 image-processor / CompressX 所托管与再分发的 ONNX 推理模型的许可证与归属，
满足各上游许可证的再分发要求。

> 校验：App 下载后由 `ModelDownloadManager` 做 SHA-256 校验
> （`image-processor-swift/Sources/ImageProcessorSDK/ML/ModelDownloadManager.swift`）。
> 下列哈希即代码中 pin 的值（quality_assessment 当前 App 未接入，哈希已托管备用）。

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

## 3. quality_assessment.onnx — 感知质量（仅托管，App 未接入）

| 项 | 内容 |
|----|------|
| 用途 | 图像感知质量评分 |
| 上游模型 | **Swin Transformer**（tiny, patch4, window7, 224×224）— Microsoft，<https://github.com/microsoft/Swin-Transformer> |
| 许可证 | **MIT**（见 `LICENSES/Swin-Transformer-LICENSE.txt`） |
| 制品来源 | HuggingFace `onnx-community/swin-tiny-patch4-window7-224-finetuned-image_quality-ONNX` |
| 文件大小 | 112,769,969 字节 |
| **SHA-256** | `e3d39aa7f89cf87e518315649126096cf0b9cd908075c119e91a2a494dd13841` |

**MIT 合规**：再分发需保留版权声明与许可证文本。

> ⚠ **App 当前未接入**：路线 B（S3/P3-1）已移除 App 的 perceptual-quality 全链——该 swin
> 模型本质为图像分类器，并非真正的感知质量回归器，原管线缺 LPIPS 双图输入、属死代码。
> 本文件仅托管留档与未来重接。真正接入需换质量回归模型（NIMA/DBCNN）+ LPIPS 双图输入。

---

## 再分发清单

本仓库以 Git LFS 跟踪并以 GitHub Release 分发以下文件，随附上述四项许可证文本：

- `esrgan_x2.onnx`（Real-ESRGAN，BSD-3）— App 使用中
- `u2net.onnx`（U²-Net，Apache-2；经 rembg MIT 工具转换）— App 使用中
- `quality_assessment.onnx`（Swin Transformer，MIT）— 仅托管，App 未接入

> 若未来调整模型（换官方制品 / 换模型 / 重接质量线），须同步更新本声明、对应 LICENSE
> 文件、README，以及 App 侧 `ModelDownloadManager` 中的 `expectedSHA256`/`remoteURL`。
