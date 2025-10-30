---
id: velu-ai
title: Velu AI Activation Function
sidebar_label: Velu AI
description: Overview and integration guidance for the Variance-enhanced Learning Unit activation function.
---

## Overview

Velu AI, released as the Variance-enhanced Learning Unit (VeLU) in April 2025, adapts activation behavior to per-layer statistics to counteract ReLU drift in deep models.[^1][^2]

## Key innovations

- **ArcTan–Sin composition:** VeLU blends arctangent and arcsine transforms inside a sigmoid envelope, creating smooth, bounded responses that still propagate informative negative activations.[^1]
- **Variance-aware scaling:** A momentum-smoothed estimate of the input standard deviation modulates the activation gain so that slopes evolve with feature distributions.[^2]
- **Wasserstein-2 regularization:** An optimal-transport penalty keeps activation outputs close to a Gaussian prior, reducing internal covariate shift without adding extra layers.[^1]

## Parameterization primer

VeLU exposes hyperparameters (α, β₁, β₂, γ, μ) alongside trainable scale λ and regularization weight λ<sub>OT</sub>.[^2] The paper’s implementation recommends gradient clipping (clipnorm ≈ 1.0), raising the variance epsilon to 1e-3, and clamping the adaptive scaling factor between 0.5 and 2.0 to prevent numerical instability.[^2] If training diverges, lowering λ<sub>OT</sub> by an order of magnitude typically restores convergence.[^2]

## Integration patterns

The authors evaluated two adoption modes: swapping only classifier-head activations for quick fine-tuning, and full-network replacement for maximal stability gains.[^1] Begin with the partial configuration on pretrained backbones, expand coverage once validation loss improvements plateau, and track λ<sub>OT</sub> adjustments for auditability.

## Performance highlights

Across ResNet50, DenseNet121, MobileNetV2, EfficientNetB3, and ViT-B16, VeLU reported top-1 accuracy improvements approaching two percentage points in deeper residual architectures under full replacement.[^1] MobileNetV2 trials on CIFAR-100, Fashion-MNIST, and Corel-10K also showed concurrent gains in accuracy and inference speed, indicating that variance-aware scaling stays lightweight for edge-friendly models.[^1][^3]

## Use cases and readiness checklist

VeLU is well-suited for vision workloads that experience activation drift—such as transfer learning on class-imbalanced image sets—and for transformer backbones needing smoother loss landscapes without GELU’s unbounded growth.[^2] Teams prioritizing deployment efficiency can adopt the head-only scenario first, then profile full-model replacements in staging environments.

**Implementation checklist**

- Prototype VeLU in an isolated branch with consistent seeds before touching production.
- Monitor the adaptive scaling metric (s<sub>adaptive</sub>) to confirm it stays inside the recommended 0.5–2.0 range.[^2]
- Pair VeLU with existing normalization layers; no architectural overhaul is required.[^1]
- Schedule hyperparameter sweeps for γ and λ<sub>OT</sub> rather than relying on defaults.[^2]
- Capture baseline and post-VeLU latency to confirm the variance computation meets SLA targets.

## References

[^1]: Ashkan Shakarami et al., “VeLU: Variance-enhanced Learning Unit for Deep Neural Networks,” arXiv, April 21, 2025. https://arxiv.org/html/2504.15051v1
[^2]: Moonlight AI, “Literature Review – VeLU: Variance-enhanced Learning Unit for Deep Neural Networks,” April 20, 2025. https://www.themoonlight.io/en/review/velu-variance-enhanced-learning-unit-for-deep-neural-networks
[^3]: CatalyzeX, “VeLU: Variance-enhanced Learning Unit for Deep Neural Networks,” April 21, 2025. https://www.catalyzex.com/paper/velu-variance-enhanced-learning-unit-for-deep
