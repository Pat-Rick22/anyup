### AnyUp: Universal Feature Upsampling

[**Thomas Wimmer**](https://wimmerth.github.io/)<sup>1,2</sup>,
[Prune Truong](https://prunetruong.com/)<sup>3</sup>,
[Marie-Julie Rakotosaona](https://scholar.google.com/citations?user=eQ0om98AAAAJ&hl=en)<sup>3</sup>,
[Michael Oechsle](https://moechsle.github.io/)<sup>3</sup>,
[Federico Tombari](https://federicotombari.github.io/)<sup>3,4</sup>,
[Bernt Schiele](https://www.mpi-inf.mpg.de/departments/computer-vision-and-machine-learning/people/bernt-schiele)<sup>1</sup>
[Jan Eric Lenssen](https://janericlenssen.github.io/)<sup>1</sup>

<sup>1</sup>Max Planck Institute for Informatics, <sup>2</sup>ETH Zurich, <sup>3</sup>Google, <sup>4</sup>TU Munich

[![Website](https://img.shields.io/badge/Website-AnyUp-blue)](https://wimmerth.github.io/anyup/)
[![arXiv](https://img.shields.io/badge/arXiv-2510.12764-b31b1b.svg)](https://arxiv.org/abs/2510.12764)

[![AnyUp Teaser](https://wimmerth.github.io/anyup/assets/figures/teaser-anyup.png)](https://wimmerth.github.io/anyup/)

**Abstract:**

We introduce AnyUp, a method for feature upsampling that can be applied to any vision feature at any resolution, without
encoder-specific training. Existing learning-based upsamplers for features like DINO or CLIP need to be re-trained for
every feature extractor and thus do not generalize to different feature types at inference time. In this work, we
propose an _inference-time_ feature-agnostic upsampling architecture to alleviate this limitation and improve upsampling
quality. In our experiments, AnyUp sets a new state of the art for upsampled features, generalizes to different feature
types, and preserves feature semantics while being efficient and easy to apply to a wide range of downstream tasks.

---

### Use AnyUp to upsample your features!

Upsample features from any model, at any layer without having to retrain the upsampler. It's as easy as this:

```python
import torch
# high-resolution image (B, 3, H, W)
hr_image    = ...
# low-resolution features (B, C, h, w) 
lr_features = ...
# load the AnyUp upsampler model
upsampler   = torch.hub.load('wimmerth/anyup', 'anyup')
# upsampled high-resolution features (B, C, H, W)
hr_features = upsampler(hr_image, lr_features)
```

**Notes:**
- The `hr_image` should be normalized to ImageNet mean and std as usual for most vision encoders.
- The `lr_features` can be any features from any encoder, e.g. DINO, CLIP, or ResNet.

The `hr_features` will have the same spatial resolution as the `hr_image` by default.
If you want a different output resolution, you can specify it with the `output_size` argument:

```python
# upsampled features with custom output size (B, C, H', W')
hr_features = upsampler(hr_image, lr_features, output_size=(H_prime, W_prime))
```

If you have limited compute resources and run into OOM issues when upsampling to high resolutions, you can use the
`q_chunk_size` argument to trade off speed for memory:

```python
# upsampled features using chunking to save memory (B, C, H, W)
hr_features = upsampler(hr_image, lr_features, q_chunk_size=128)
```

If you are interested in the attention that is used by AnyUp to upsample the features, we included an optional
visualization thereof in the forward pass:

```python
# matplotlib must be installed to use this feature
# upsampled features and display attention map visualization (B, C, H, W)
hr_features = upsampler(hr_image, lr_features, vis_attn=True)
```

---

**Training code** for AnyUp will be released soon!

We are also planning to integrate FlexAttention support to speed up the window attention and reduce memory consumption.
We are always happy for a helping hand, so feel free to reach out if you want to contribute!

---

**Evaluation** followed the protocols of [JAFAR](https://github.com/PaulCouairon/JAFAR) for semantic segmentation and
[Probe3D](https://github.com/mbanani/probe3d) for surface normal and depth estimation. Note that we applied a small fix
to the probe training in JAFAR (updating LR scheduling to per epoch instead of per iteration). Therefore, we re-ran all
experiments with baselines to ensure a fair comparison.

**Acknowledgements:**
We built our implementation on top of the [JAFAR repository](https://github.com/PaulCouairon/JAFAR) and thank the
authors for open-sourcing their code. Other note-worthy open-source repositories include:
[LoftUp](https://github.com/andrehuang/loftup), [FeatUp](https://github.com/mhamilton723/FeatUp), and
[Probe3D](https://github.com/mbanani/probe3d).
