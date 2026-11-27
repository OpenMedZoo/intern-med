# Intern-Med

**Intern-Med** is a medical-domain large language model built upon [Intern-S2-Preview](https://huggingface.co/internlm/Intern-S2-Preview). It strengthens medical knowledge and clinical reasoning, and produces explicit multi-stage structured reasoning (checklist-style chain-of-thought) in thinking mode, while preserving the general and mathematical capabilities of the base model.

## Model Weights

The model weights are hosted on Hugging Face:

**[OpenMedZoo/intern-med](https://huggingface.co/OpenMedZoo/intern-med)**

The repository contains the full model (bfloat16, custom code included), the model card, and deployment instructions.

## Usage

The model ships custom code, so `trust_remote_code=True` is required.

### vLLM (recommended)

```python
from openai import OpenAI

# Serving example:
#   vllm serve OpenMedZoo/intern-med \
#       --trust-remote-code --reasoning-parser qwen3 --max-model-len 8192

client = OpenAI(base_url="http://localhost:8000/v1", api_key="EMPTY")

resp = client.chat.completions.create(
    model="OpenMedZoo/intern-med",
    messages=[
        {"role": "system", "content": "<your system prompt>"},
        {"role": "user", "content": "<your question>"},
    ],
    temperature=0.0,
)
print(resp.choices[0].message)
```

### Transformers

```python
from transformers import AutoModelForImageTextToText, AutoProcessor

model = AutoModelForImageTextToText.from_pretrained(
    "OpenMedZoo/intern-med", torch_dtype="bfloat16",
    device_map="auto", trust_remote_code=True,
)
processor = AutoProcessor.from_pretrained("OpenMedZoo/intern-med", trust_remote_code=True)
```

## Recommended decoding

- Greedy / low-temperature decoding works best for structured reasoning and benchmark-style tasks.
- Thinking mode: use a reasoning parser (e.g. `--reasoning-parser qwen3` in vLLM) to separate the reasoning trace from the final answer.

## Disclaimer

Intern-Med is a research model. Its outputs are for informational purposes only and **do not constitute medical advice, diagnosis, or treatment**. Always consult a qualified healthcare professional.

## License

Apache 2.0. This model is derived from Intern-S2-Preview; please also comply with the base model's license terms.
