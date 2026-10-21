# Google Colab Pro — First Video Generation Experiment

**Status:** Experiment plan; not executed or benchmarked as part of this repository.

## Goal

Test a short generated scene on Colab Pro and assess image quality, motion stability, VRAM use, runtime, and saved output reliability.

## Preparation

1. Open a GPU-enabled Colab notebook and check the available GPU model/VRAM (`nvidia-smi`).
2. Review Colab compute units and available disk space. GPU type and runtime availability are not guaranteed by the Pro subscription.
3. Mount Google Drive or use a controlled output transfer for completed clips.
4. Select a model configuration supported by the allocated GPU; prefer an official repository and documented commands.
5. Start with a short, low-cost experiment before using higher resolutions or long sequences.

## Model selection guidance

- **T4 16GB:** begin with a lighter model/workflow; do not assume an unoptimized 5B model will fit.
- **L4 24GB:** test memory-efficient Wan 2.2 TI2V-5B workflows if dependencies and VRAM permit.
- **A100 40/80GB:** consider larger workflows after a lightweight baseline test.
- **MiniMax video models:** usually accessed through a hosted API rather than downloaded as local weights; review current vendor access and pricing.

These are planning suggestions, not verified current hardware requirements or promises of successful execution.

## Minimal initial diagnostics

```python
import torch
import subprocess

print(subprocess.run(['nvidia-smi'], capture_output=True, text=True).stdout)
print('CUDA available:', torch.cuda.is_available())
if torch.cuda.is_available():
    print('GPU:', torch.cuda.get_device_name(0))
    vram_gb = torch.cuda.get_device_properties(0).total_memory / 1024**3
    print(f'VRAM: {vram_gb:.1f} GiB')
```

## Test report template

| Field | Value |
|---|---|
| Date | _TBD_ |
| GPU / VRAM | _TBD_ |
| Model and commit / revision | _TBD_ |
| Prompt / reference image | _TBD_ |
| Resolution / fps / frames | _TBD_ |
| Sampler and steps | _TBD_ |
| Total elapsed runtime | _TBD_ |
| Peak memory | _TBD_ |
| Output MP4 link/path | _TBD_ |
| Problems observed | _TBD_ |

Never publish API keys, access tokens, private Drive links, or third-party copyrighted source assets without permission.
