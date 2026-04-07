# ML / GPU Infrastructure

**Summary**: RunPod for training, E2E Networks for inference — hosts vLLM, Whisper, and IndicF5.
**Tags**: #infrastructure #gpu #runpod #e2e-networks #vllm
**Created**: 2026-04-07
**Last Updated**: 2026-04-07

---

## Training

- **Provider**: RunPod (GPU cloud, on-demand)
- **Use case**: QLoRA finetuning via Unsloth

## Inference

- **Provider**: E2E Networks GPU server
- **Services**: vLLM, Whisper, IndicF5
- **Specs**: Intel Xeon, 24GB RAM, NVIDIA RTX GPU

## Related Notes

- [[text-to-sql]]
- [[openclaw-guru]]
- [[ec2-setup]]
