# image-processor-models

**image-processor / CompressX** 的 ONNX 推理模型注册表与分发仓库。

收录三条 ML 管线用到的 ONNX 模型（超分辨率、背景移除、感知质量评估），统一托管、
版本化、完整性校验后供 App 按需下载。

- **Git LFS** 跟踪 `.onnx`（保留模型版本历史；单文件最大 176MB，超 GitHub 普通文件 100MB 上限）。
- **GitHub Release** 资源是 App 运行时下载源（CDN 加速、免鉴权、无 LFS 带宽配额）。
- App 下载后做 **SHA-256 校验**（`ModelDownloadManager.expectedSHA256`），不符即丢弃。
- 所有模型均为宽松许可证（BSD-3 / Apache-2 / MIT），再分发随附 [`LICENSES/`](LICENSES) 与 [`NOTICE.md`](NOTICE.md)。

> 详细下载/使用/后端选择见上游指南 `image-processor-rs/docs/ML_MODELS_GUIDE.md`
> （注：该指南早于路线 B 重构，其中 `assess_perceptual_quality` API 与 SessionCache
> 描述已过时；以本仓库与 App 实际代码为准）。

---

## 模型清单

| 文件 | 模型 / 架构 | 用途 | 输入尺寸 | 大小 (bytes) | 许可证 | App 状态 | Release |
|------|-------------|------|----------|-------------:|--------|----------|---------|
| `esrgan_x2.onnx` | Real-ESRGAN x2 | 2× AI 超分辨率 | 任意（pad 到 4 倍数） | 67,116,767 | BSD-3 | ✅ 使用中 | [v1.0.0](../../releases/download/v1.0.0/esrgan_x2.onnx) |
| `u2net.onnx` | U²-Net | 背景移除（前景分割） | 320×320 | 175,997,641 | Apache-2 | ✅ 使用中 | [v1.0.0](../../releases/download/v1.0.0/u2net.onnx) |
| `quality_assessment.onnx` | Swin Transformer (tiny) | 感知质量评分 | 224×224 | 112,769,969 | MIT | 🅿️ 仅托管 | [v1.0.0](../../releases/download/v1.0.0/quality_assessment.onnx) |

### SHA-256（App 侧 pin 值）

```
esrgan_x2.onnx          4b650e0f8cc10057635e24ac411902a54307401fb6c9f265bf6e1eaf9ae7c949
u2net.onnx              8d10d2f3bb75ae3b6d527c77944fc5e7dcd94b29809d47a739a7a728a912b491
quality_assessment.onnx e3d39aa7f89cf87e518315649126096cf0b9cd908075c119e91a2a494dd13841
```

---

## 模型详情

### esrgan_x2.onnx — 超分辨率（Real-ESRGAN）
2× 智能放大。Real-ESRGAN（Xintao Wang 等）的 ONNX 转换；输入图归一化到 [-1,1] 并 pad 到
4 的倍数，输出放大后的 RGB。**BSD 3-Clause**。

### u2net.onnx — 背景移除（U²-Net）
显著性分割，输出 RGBA（背景像素 alpha=0）。U²-Net（Xuebin Qin 等），经 rembg 转换为 ONNX；
输入归一化到 [0,1]、resize 到 320×320。**Apache 2.0**（转换工具 rembg 为 MIT）。

### quality_assessment.onnx — 感知质量（Swin Transformer）
Swin Transformer tiny（patch4, window7, 224×224），onnx-community 的 image-quality 微调版。
输入归一化到 [0,1]、resize 到 224×224，输出质量评分。**MIT**（Microsoft Swin Transformer）。

> ⚠ **App 当前未接入此模型**。路线 B（见 `docs/ML链路深度对齐方案.md` 的 S3/P3-1）已移除
> App 的 perceptual-quality 全链：该 swin 模型本质是图像**分类器**架构，并非真正的感知质量
> 回归器，且原管线缺 LPIPS 双图输入、0 下载 0 调用，属死代码。本仓库托管该文件仅为**留档与
> 未来重接**——按审计建议，真正接入需要换用质量回归模型（如 NIMA/DBCNN）并实现 LPIPS 双图
> 输入（约 1 周工作量），届时再在 App 侧恢复 `ModelType.qualityAssessment` 与 `remoteURL`。

---

## App 集成现状

- **接入中**：`esrgan_x2`（超分）、`u2net`（去背景）——由 `ModelDownloadManager` 下载并校验。
- **执行后端**：优先 `coreml`，失败自动回退 `cpu`（`SuperResolutionService`/`BackgroundRemovalService`，路线 B / P2-3）。
- **会话缓存**：`SessionCache` 缓存已加载 ONNX 会话，并在磁盘模型文件 mtime 变化时自动重载（P2-4）。
- **完整性**：下载后 SHA-256 校验，不符则删除并报 `checksumMismatch`（P2-2）。

> 其他可选后端（`cuda` / `nnapi`）由 ONNX Runtime 支持，App 当前固定 coreml→cpu。

---

## 来源与归属

| 模型 | 上游 | 制品来源 |
|------|------|----------|
| esrgan_x2 | [Real-ESRGAN](https://github.com/xinntao/Real-ESRGAN)（BSD-3） | HuggingFace 社区转换 `JoPmt/Real_Esrgan_x2_Onnx_Tflite_Tfjs` |
| u2net | [U²-Net](https://github.com/xuebinqin/U-2-Net)（Apache-2） | [rembg](https://github.com/danielgatis/rembg)（MIT）Release v0.0.0 |
| quality_assessment | [Swin Transformer](https://github.com/microsoft/Swin-Transformer)（MIT） | HuggingFace `onnx-community/swin-tiny-patch4-window7-224-finetuned-image_quality-ONNX` |

完整许可证文本见 [`LICENSES/`](LICENSES)，归属与合规说明见 [`NOTICE.md`](NOTICE.md)。

---

## 更新模型

1. 替换 `models/` 下文件（LFS 自动跟踪 `.onnx`）。
2. 重新计算 SHA-256（`shasum -a 256 models/*.onnx`），更新本 README 与 `NOTICE.md`。
3. 发布新 Release tag，并把 `.onnx` 作为资源上传（`gh release upload <tag> models/*.onnx`）。
4. 同步更新 App 侧 `ModelType.remoteURL` 与 `expectedSHA256`
   （`image-processor-swift/Sources/ImageProcessorSDK/ML/ModelDownloadManager.swift`）。
