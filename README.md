# image-processor-models

ONNX 推理模型仓库，供 **image-processor**（CompressX / ImageProcessorSDK）按需下载。

- 模型以 **Git LFS** 跟踪于本仓库（保留版本历史）。
- App 运行时从 **GitHub Release** 资源 URL 下载（CDN 加速、免鉴权、无带宽上限），并做 SHA-256 校验。
- 所有模型均为宽松许可证（BSD-3 / Apache-2），再分发随附 [`LICENSES/`](LICENSES) 与 [`NOTICE.md`](NOTICE.md)。

## 模型清单

| 文件 | 用途 | 大小 (bytes) | 许可证 | Release 下载 |
|------|------|-------------:|--------|--------------|
| `esrgan_x2.onnx` | 2× AI 超分辨率（Real-ESRGAN） | 67,116,767 | BSD-3 | [v1.0.0/esrgan_x2.onnx](../../releases/download/v1.0.0/esrgan_x2.onnx) |
| `u2net.onnx` | 背景移除（U²-Net） | 175,997,641 | Apache-2 | [v1.0.0/u2net.onnx](../../releases/download/v1.0.0/u2net.onnx) |

### SHA-256（App 侧 pin 值）

```
esrgan_x2.onnx  4b650e0f8cc10057635e24ac411902a54307401fb6c9f265bf6e1eaf9ae7c949
u2net.onnx      8d10d2f3bb75ae3b6d527c77944fc5e7dcd94b29809d47a739a7a728a912b491
```

## 来源与归属

- **esrgan_x2.onnx** — [Real-ESRGAN](https://github.com/xinntao/Real-ESRGAN)（Xintao Wang）的 ONNX 转换，源自 HuggingFace 社区转换 `JoPmt/Real_Esrgan_x2_Onnx_Tflite_Tfjs`。**BSD 3-Clause**（见 [`LICENSES/Real-ESRGAN-LICENSE.txt`](LICENSES/Real-ESRGAN-LICENSE.txt)）。
- **u2net.onnx** — [U²-Net](https://github.com/xuebinqin/U-2-Net)（Xuebin Qin）经 [rembg](https://github.com/danielgatis/rembg)（Daniel Gatis）转换的 ONNX。**Apache 2.0**（见 [`LICENSES/U-2-Net-LICENSE.txt`](LICENSES/U-2-Net-LICENSE.txt)；转换工具 rembg 为 MIT，见 [`LICENSES/rembg-LICENSE.txt`](LICENSES/rembg-LICENSE.txt)）。

完整归属与合规说明见 [`NOTICE.md`](NOTICE.md)。

## 更新模型

1. 替换 `models/` 下文件（LFS 自动跟踪 `.onnx`）。
2. 重新计算 SHA-256（`shasum -a 256 models/*.onnx`），更新本 README 与 `NOTICE.md`。
3. 发布新 Release tag，并同步更新 App 侧 `ModelType.remoteURL` 与 `expectedSHA256`
   （`image-processor-swift/Sources/ImageProcessorSDK/ML/ModelDownloadManager.swift`）。
