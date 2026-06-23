# image-processor-models

**image-processor / CompressX** 的 ONNX 推理模型注册表与分发仓库。

收录三条 ML 管线用到的 ONNX 模型（超分辨率、背景移除、感知质量评估），统一托管、
版本化、完整性校验后供 App 按需下载。

- **Git LFS** 跟踪 `.onnx`（保留模型版本历史）。
- **GitHub Release** 资源是 App 运行时下载源（CDN 加速、免鉴权、无 LFS 带宽配额）。
- App 下载后做 **SHA-256 校验**（`ModelDownloadManager.expectedSHA256`），不符即丢弃。
- 所有模型均为宽松许可证（BSD-3 / Apache-2 / BSD），再分发随附 [`LICENSES/`](LICENSES) 与 [`NOTICE.md`](NOTICE.md)。

## 模型清单

| 文件 | 模型 / 架构 | 用途 | 输入 | 大小 (bytes) | 许可证 | App 状态 | Release |
|------|-------------|------|------|-------------:|--------|----------|---------|
| `esrgan_x2.onnx` | Real-ESRGAN x2 | 2× AI 超分辨率 | 单图，pad 到 4 倍数 | 67,116,767 | BSD-3 | ✅ 使用中 | [v1.0.0](../../releases/download/v1.0.0/esrgan_x2.onnx) |
| `u2net.onnx` | U²-Net | 背景移除（前景分割） | 单图 320×320 | 175,997,641 | Apache-2 | ✅ 使用中 | [v1.0.0](../../releases/download/v1.0.0/u2net.onnx) |
| `lpips_alex.onnx` | LPIPS (AlexNet) | 感知差异（压缩质量损失） | **双图** [-1,1] 256×256 | 9,897,322 | BSD | ✅ 使用中 | [v1.0.0](../../releases/download/v1.0.0/lpips_alex.onnx) |

### SHA-256（App 侧 pin 值）

```
esrgan_x2.onnx  4b650e0f8cc10057635e24ac411902a54307401fb6c9f265bf6e1eaf9ae7c949
u2net.onnx      8d10d2f3bb75ae3b6d527c77944fc5e7dcd94b29809d47a739a7a728a912b491
lpips_alex.onnx 6680f203b2e44e288a337e17828d17d5fa353ecd1f2433f2cb172d3c366c4246
```

## 模型详情

### esrgan_x2.onnx — 超分辨率（Real-ESRGAN）
2× 智能放大。Real-ESRGAN（Xintao Wang 等）ONNX 转换；输入归一化 [-1,1]、pad 到 4 倍数。**BSD 3-Clause**。

### u2net.onnx — 背景移除（U²-Net）
显著性分割，输出 RGBA（背景 alpha=0）。U²-Net（Xuebin Qin 等）经 rembg 转换；输入 [0,1]、320×320。**Apache 2.0**（转换工具 rembg 为 MIT）。

### lpips_alex.onnx — 感知差异（LPIPS）
**Learned Perceptual Image Patch Similarity**（Richard Zhang 等）——衡量两张图之间的**感知距离**。
对压缩质量评估：`distance = LPIPS(original, compressed)`，**越低表示压缩后感知损失越小（质量保持越好）**，
0 = 视觉无差异。双输入（img1/img2），[-1,1] 归一化、256×256、NCHW。**BSD**（richzhang/PerceptualSimilarity + AlexNet backbone）。
本仓库制品为 PyTorch→ONNX 导出（legacy exporter，权重内嵌）。

> 历史说明：早期曾托管 `quality_assessment.onnx`（Swin Transformer），经实测为 2 分类器且对真实质量变化无区分度
> （clean vs degraded 仅差 0.36 个百分点），已移除，改用语义正确的 LPIPS。

## App 集成

- **超分**（`esrgan_x2`）、**去背景**（`u2net`）：`ModelDownloadManager` 下载 + 校验；coreml→cpu provider 回退（P2-3）。
- **感知差异**（`lpips_alex`）：`assessPerceptualQuality(original, compressed)` 返回 LPIPS 距离（f64，越低越好）。
  会话缓存 `SessionCache` 在磁盘 mtime 变化时重载（P2-4）；下载后 SHA-256 校验（P2-2）。

## 来源与归属

| 模型 | 上游 | 制品来源 |
|------|------|----------|
| esrgan_x2 | [Real-ESRGAN](https://github.com/xinntao/Real-ESRGAN)（BSD-3） | HuggingFace `JoPmt/Real_Esrgan_x2_Onnx_Tflite_Tfjs` |
| u2net | [U²-Net](https://github.com/xuebinqin/U-2-Net)（Apache-2） | [rembg](https://github.com/danielgatis/rembg)（MIT）Release v0.0.0 |
| lpips_alex | [LPIPS](https://github.com/richzhang/PerceptualSimilarity)（BSD） | 本项目 PyTorch→ONNX 导出（`lpips.LPIPS(net='alex')`） |

完整许可证见 [`LICENSES/`](LICENSES)，归属见 [`NOTICE.md`](NOTICE.md)。

## 更新模型

1. 替换 `models/` 下文件（LFS 自动跟踪 `.onnx`）。
2. 重新计算 SHA-256（`shasum -a 256 models/*.onnx`），更新本 README 与 `NOTICE.md`。
3. 发布新 Release tag，上传 `.onnx` 资源（`gh release upload <tag> models/*.onnx`）。
4. 同步更新 App 侧 `ModelType.remoteURL` 与 `expectedSHA256`
   （`image-processor-swift/Sources/ImageProcessorSDK/ML/ModelDownloadManager.swift`）。
