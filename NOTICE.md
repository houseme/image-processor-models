# 第三方 ML 模型 — 许可与归属声明（NOTICE）

本目录记录 CompressX / ImageProcessorSDK 按需下载并在自托管位置再分发的两个
ONNX 推理模型的许可证与归属，满足各上游许可证的再分发要求。

> 校验：下载后由 `ModelDownloadManager` 做 SHA-256 校验（见
> `image-processor-swift/Sources/ImageProcessorSDK/ML/ModelDownloadManager.swift`）。
> 下列哈希即代码中 pin 的值。

---

## 1. esrgan_x2.onnx — 超分辨率

| 项 | 内容 |
|----|------|
| 用途 | 2× AI 超分辨率（ESRGAN/Real-ESRGAN） |
| 上游模型 | **Real-ESRGAN** — Xintao Wang 等，<https://github.com/xinntao/Real-ESRGAN> |
| 许可证 | **BSD 3-Clause**（见 `Real-ESRGAN-LICENSE.txt`） |
| 论文 | Xintao Wang, Liangbin Xie, Chao Dong, Ying Shan. *Real-ESRGAN: Training Real-World Blind Super-Resolution with Pure Synthetic Data.* ICCVW 2021. |
| 制品来源 | HuggingFace 社区转换 `JoPmt/Real_Esrgan_x2_Onnx_Tflite_Tfjs`（PyTorch → ONNX 转换） |
| 文件大小 | 67,116,767 字节 |
| **SHA-256** | `4b650e0f8cc10057635e24ac411902a54307401fb6c9f265bf6e1eaf9ae7c949` |

**BSD-3 合规**：再分发需保留版权声明与本许可证文本（见 `Real-ESRGAN-LICENSE.txt`）；
不得未经书面许可使用版权持有人名义为衍生产品背书（第 3 条）。

> 出处说明：该 ONNX 来自社区上传者（JoPmt），非 xinntao 官方发布。已 pin 其
> SHA-256 以锁定确切字节；长期建议替换为官方 ONNX 制品（哈希将相应更新）。

---

## 2. u2net.onnx — 背景移除

| 项 | 内容 |
|----|------|
| 用途 | 显著性分割 / 背景移除（U²-Net） |
| 上游模型 | **U²-Net** — Xuebin Qin 等，<https://github.com/xuebinqin/U-2-Net> |
| 许可证 | **Apache License 2.0**（见 `U-2-Net-LICENSE.txt`） |
| 论文 | Xuebin Qin, Zichen Zhang, Chenyang Huang, Masood Dehghan, Osmar R. Zaiane, Martin Jagersand. *U²-Net: Going Deeper with Nested U-Structure for Salient Object Detection.* Pattern Recognition, 2020. |
| 转换工具 | **rembg** — Daniel Gatis，<https://github.com/danielgatis/rembg>，**MIT**（见 `rembg-LICENSE.txt`） |
| 制品来源 | rembg GitHub Release v0.0.0 的 `u2net.onnx` |
| 文件大小 | 175,997,641 字节 |
| **SHA-256** | `8d10d2f3bb75ae3b6d527c77944fc5e7dcd94b29809d47a739a7a728a912b491` |

**Apache-2 合规**：再分发需保留许可证文本（见 `U-2-Net-LICENSE.txt`）与本归属声明。
该 `.onnx` 为 U²-Net 预训练权重的 ONNX 格式转换制品（由 rembg 完成）；权重的
Apache-2 条款随制品延续。转换工具 rembg 本身为 MIT（见 `rembg-LICENSE.txt`）。

---

## 再分发清单

自托管（P2-5）上线后，以下文件将在此项目自有位置再分发，并随附上述三项许可证文本：

- `esrgan_x2.onnx`（Real-ESRGAN，BSD-3）
- `u2net.onnx`（U²-Net，Apache-2；经 rembg MIT 工具转换）

> 若未来调整模型（换官方制品 / 换模型），须同步更新本声明、对应 LICENSE 文件，
> 以及 `ModelDownloadManager` 中的 `expectedSHA256`。
