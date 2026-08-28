# llama-kylinos-kunpeng920

使用 GitHub Actions 编译银河麒麟 V10 SP3（glibc 2.28）下的 llama.cpp 二进制，针对**鲲鹏920 5220 × 2**（2路，每路32核，主频2.6GHz，支持 SVE）优化，用以提高本地模型运行性能。

## 目标服务器

- CPU：鲲鹏920 5220 × 2（共 64 核，armv8.2-a，支持 SVE / i8mm / dotprod）
- OS：银河麒麟高级服务器操作系统 V10 SP3（glibc 2.28）
- 加速：华为毕昇编译器（BiSheng 5.1.0.2）+ KML 数学库（2.5.0）

## 编译特性

| 特性 | 值 |
|------|----|
| NEON | 1 |
| ARM_FMA | 1 |
| FP16_VA | 1 |
| MATMUL_INT8 | 1 |
| SVE | 1 |
| DOTPROD | 1 |
| SVE_CNT | 32 |
| OPENMP | 1 |
| REPACK | 1 |

关键编译参数：

```
-march=armv8.2-a+fp16+simd+crypto+dotprod+i8mm+sve -mtune=tsv110
```

## 使用方式

1. 将本仓库推送到 GitHub（或点击 Actions → Build → Run workflow 手动触发）。
2. 等待编译完成，下载 `llama-kunpeng920-kylin-v10sp3` artifact。
3. 在目标服务器解压：
   ```bash
   tar -zxvf llama-kunpeng920-kylin-v10sp3.tar.gz
   ```

## 说明

- 源码直接使用 `ggml-org/llama.cpp`（b9488 分支），而非 MyLLaMA 仓库（其不含 llama 源码）。
- 毕昇编译器与 KML 库来自 `biliops/MyLLaMA` v1.0.0 release 附件。
- 在 QEMU 模拟的 aarch64 环境下，KML 的 RPM 安装脚本会因检测不到真实鲲鹏 CPU 而失败，因此采用 `rpm2cpio` 直接解包安装。
- 二进制在 Kylin V10 SP3 容器内编译，链接 glibc 2.28，可直接在目标系统运行。