---
title: "WebGPU Timing Performance"
source: "https://webgpufundamentals.org/webgpu/lessons/webgpu-timing.html"
author:
  - "[[6 contributions]]"
published:
created: 2025-11-26
description: "Timing operations in WebGPU"
tags:
  - "clippings"
---
By default the `'timestamp-query'` time values are quantized to 100µ seconds. In Chrome, if you enable ["enable-webgpu-developer-features"](https://webgpufundamentals.org/webgpu/lessons/#enable-webgpu-developer-features) in [about:flags](https://webgpufundamentals.org/webgpu/lessons/#enable-webgpu-developer-features), the time values may not be quantized. This would theoretically give you more accurate timings. That said, normally 100µ second quantized values should be enough for you to compare shaders techniques for performance.