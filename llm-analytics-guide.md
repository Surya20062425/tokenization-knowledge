# LLM Analytics: Complete Guide to Building and Testing Your Own LLM for Free in the Cloud

## Overview

This guide teaches you how to build, fine-tune, and test Large Language Models (LLMs) entirely for free using cloud platforms. We'll cover the entire workflow from scratch to deployment.

## Table of Contents

1. [Free Cloud Platforms Overview](#free-cloud-platforms-overview)
2. [Step-by-Step Guide](#step-by-step-guide)
3. [Recommended Models](#recommended-models)
4. [Testing and Evaluation](#testing-and-evaluation)
5. [Deployment Options](#deployment-options)
6. [Troubleshooting](#troubleshooting)

---

## 1. Free Cloud Platforms Overview

### Google Colab (Free Tier)
- **GPU**: T4 or K80 (12-24 GB VRAM)
- **Limit**: 12-hour sessions, 90-hour/week limit
- **Storage**: 100 GB RAM disk, Google Drive integration
- **Access**: No credit card required

### Kaggle Notebooks (Free Tier)
- **GPU**: T4 (16 GB VRAM)
- **Session**: 30 hours/week maximum
- **Storage**: 20 GB disk space
- **Access**: Free with Kaggle account

### Hugging Face Spaces
- **Free Tier**: CPU-based inference apps
- **Pro Tier**: GPU access (requires credit card, but first try is free)
- **Storage**: 15 GB
- **Access**: Great for demo apps

---

## 2. Step-by-Step Guide

### Prerequisites
- GitHub account
- Google account (for Colab)
- Hugging Face account (free)

### Step 1: Choose Your Base Model

Visit [Hugging Face Model Hub](https://huggingface.co/models) and select from:

**Small Models (Easy to fine-tune):**
- Qwen2.5-3B-Instruct
- Gemma-2-2B-Instruct
- Phi-3-mini-instruct
- Mistral-7B-Instruct-v0.3

**Medium Models (Better quality, needs QLoRA):**
- Llama-3.2-3B-Instruct
- Qwen2.5-7B-Instruct
- Gemma-3-4B-Instruct

### Step 2: Set Up Environment in Google Colab

```python
# Install required packages
!pip install -q transformers accelerate peft bitsandbytes datasets trl huggingface_hub

# Verify GPU access
import torch
print(f"CUDA available: {torch.cuda.is_available()}")
if torch.cuda.is_available():
    print(f"GPU: {torch.cuda.get_device_name(0)}")
```

### Step 3: Download and Prepare Your Dataset

```python
from datasets import load_dataset

# Option 1: Use existing dataset
dataset = load_dataset("your-dataset-name")

# Option 2: Create custom dataset
# Format: JSON with "instruction", "input", "output" fields
data = [
    {"instruction": "Explain quantum computing", "input": "", "output": "Quantum computing uses quantum bits..."},
    # ... more examples
]
```

### Step 4: Fine-tune with QLoRA (Parameter-Efficient Fine-Tuning)

```python
from transformers import AutoModelForCausalLM, BitsAndBytesConfig
from peft import LoraConfig, get_peft_model
import torch

# QLoRA configuration for 4-bit quantization
bnb_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_quant_type="nf4",
    bnb_4bit_compute_dtype=torch.bfloat16
)

# Load model with quantization
model = AutoModelForCausalLM.from_pretrained(
    "model-name",
    quantization_config=bnb_config,
    device_map="auto"
)

# LoRA configuration
lora_config = LoraConfig(
    r=64,
    lora_alpha=16,
    target_modules=["q_proj", "v_proj"],
    lora_dropout=0.1,
    bias="none",
    task_type="CAUSAL_LM"
)

model = get_peft_model(model, lora_config)
```

### Step 5: Training with Transformers

```python
from transformers import TrainingArguments, Trainer
from trl import SFTTrainer

training_args = TrainingArguments(
    output_dir="./results",
    num_train_epochs=1,
    per_device_train_batch_size=1,
    gradient_accumulation_steps=4,
    save_steps=100,
    logging_steps=10,
    learning_rate=2e-4,
    fp16=True,
)

trainer = SFTTrainer(
    model=model,
    args=training_args,
    train_dataset=dataset["train"],
    dataset_text_field="text",
    max_seq_length=512,
)

trainer.train()
```

### Step 6: Save and Push to Hugging Face

```python
# Save locally
model.save_pretrained("your-model")

# Push to Hugging Face Hub
from huggingface_hub import login
login()  # Will prompt for token

model.push_to_hub("your-username/your-model-name")
```

---

## 3. Recommended Models

| Model | Size | Best For | Colab Friendly? |
|-------|------|----------|-----------------|
| Qwen2.5-3B | 3B | General purpose | ✅ Yes |
| Phi-3-mini | 3.8B | Code, reasoning | ✅ Yes |
| Gemma-2-2B | 2B | Chat, instruction | ✅ Yes |
| Mistral-7B | 7B | Quality, speed | ✅ With QLoRA |
| Llama-3.2-3B | 3B | Latest features | ✅ Yes |

---

## 4. Testing and Evaluation

### Test Your Fine-tuned Model

```python
from transformers import pipeline

# Load fine-tuned model
pipe = pipeline("text-generation", model="your-username/your-model")

# Test
prompt = "Explain the benefits of renewable energy"
result = pipe(prompt, max_length=200)
print(result[0]['generated_text'])
```

### Evaluation Metrics

```python
from datasets import load_metric

# ROUGE for summarization
rouge = load_metric("rouge")

# BLEU for translation
bleu = load_metric("bleu")

# Perplexity calculation
from transformers import AutoTokenizer
import torch

tokenizer = AutoTokenizer.from_pretrained("your-model")
inputs = tokenizer("test text", return_tensors="pt")
with torch.no_grad():
    outputs = model(**inputs, labels=inputs["input_ids"])
    perplexity = torch.exp(outputs.loss)
```

---

## 5. Deployment Options

### Option A: Hugging Face Inference API (Free Tier)
```python
from huggingface_hub import InferenceClient

client = InferenceClient(model="your-username/your-model")
response = client.text_generation("Your prompt here")
```

### Option B: Create a Space (Gradio Demo)
```python
import gradio as gr
from transformers import pipeline

def predict(prompt):
    return pipe(prompt)[0]['generated_text']

demo = gr.Interface(
    fn=predict,
    inputs=gr.Textbox(label="Prompt"),
    outputs=gr.Textbox(label="Response")
)
demo.launch()
```

### Option C: Deploy via Hugging Face Endpoints
- Upgrade to Pro tier ($9/month) for GPU endpoints
- REST API available at: `https://api-inference.huggingface.co/models/your-model`

---

## 6. Troubleshooting

### Common Issues

1. **Out of Memory (OOM) Error**
   - Solution: Reduce batch size, use QLoRA with 4-bit quantization

2. **CUDA Out of Memory**
   - Solution: Use `device_map="auto"` and enable gradient checkpointing

3. **Dataset Format Issues**
   - Solution: Ensure JSON format has "instruction", "input", "output" fields

4. **Slow Training**
   - Solution: Use LoRA/QLoRA instead of full fine-tuning

---

## Quick Reference Cheat Sheet

```bash
# Quick setup in Colab
pip install transformers accelerate peft bitsandbytes datasets trl
```

```python
# Quick fine-tune skeleton
from transformers import AutoModelForCausalLM, BitsAndBytesConfig
from peft import LoraConfig, get_peft_model

bnb_config = BitsAndBytesConfig(load_in_4bit=True)
model = AutoModelForCausalLM.from_pretrained("model", quantization_config=bnb_config)
model = get_peft_model(model, LoraConfig(r=64))
```

---

## Useful Resources

- **Hugging Face Model Hub**: https://huggingface.co/models
- **Google Colab**: https://colab.research.google.com
- **Kaggle**: https://kaggle.com
- **QLoRA Paper**: https://arxiv.org/abs/2305.14314
- **Training Notebooks**: https://github.com/deepal/fine-tuning-notebooks

---

## Next Steps

1. Start with a small model (Qwen2.5-3B or Phi-3-mini)
2. Fine-tune on a simple dataset (100-500 examples)
3. Evaluate quality
4. Scale up if needed

This approach allows you to build production-quality LLMs without spending any money on hardware.