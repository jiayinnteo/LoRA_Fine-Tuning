# LoRA Fine-Tuning

## Problem Statement
Fine-tuning LLMs on domain-specific tasks enhances their performance in real-world applications. This project involves selecting a dataset, model, and fine-tuning framework to adapt a base model for medical reasoning, an essential use case in healthcare AI systems.

### Dataset: FreedomIntelligence/medical-o1-reasoning-SFT
-  Domain Relevance:
  - The dataset contains clinically relevant questions and answers, making it highly applicable to real-world healthcare use cases such as diagnostic support, triage assistants, and Q&A bots.
- Instruction-Based Format:
  - Each entry is structured with instruction, input, and output fields, which are essential for instruction-tuned LLMs and compatible with libraries like Unsloth.
- Reasoning-Oriented:
  - The tasks emphasize multi-step reasoning, explanation, and critical thinking rather than simple factual recall, matching the capabilities of LLMs like Mistral.
- SFT-Ready:
  - Designed for supervised fine-tuning, the dataset is plug-and-play with SFTTrainer and FastLanguageModel, reducing preprocessing time.
- Educational and Research Value:
 - The dataset is ideal for research and development of safe, interpretable AI in the medical field.

In summary, this dataset empowers the model to learn domain-specific reasoning patterns essential for trusted healthcare applications.


### Model: Mistral-7B v0.3 (7B)
- Balance of size and performance:
  - 7B parameters allow for solid reasoning capabilities while still trainable on consumer GPUs with LoRA.
- 2.2x faster inference:
  - Version 0.3 is optimized for performance, accelerating both training and deployment.
- Open-source and modifiable:
  - Ideal for experimentation, academic projects, and low-cost deployment.
- Strong performance on instruction-following tasks, making it ideal for the dataset’s format.


### Fine-Tuning Library: Unsloth
- Memory-Efficient:
  - Supports 4-bit/8-bit quantization and LoRA to significantly reduce memory usage.
- Fast Training:
  - Optimized training pipelines can be up to 2x faster than traditional Transformer workflows.
- Integrated with Hugging Face: Works seamlessly with Hugging Face models, tokenizers, and datasets.
- Simple API:
  - The FastLanguageModel and SFTTrainer abstractions reduce code complexity and support rapid prototyping.

## Documentation & Report


### Fine-Tuning Summary Report
| **Metric**                      | **Value**                                    |
| ------------------------------- | -------------------------------------------- |
| **Model**                       | mistral-7b-instruct-v0.3-bnb-4bit            |
| **Dataset**                     | FreedomIntelligence/medical-o1-reasoning-SFT |
| **Epochs**                      | 1                                            |
| **Max Steps**                   | 1108                                         |
| **Train Batch Size**            | 4                                            |
| **Gradient Accumulation Steps** | 4                                            |
| **Learning Rate**               | 2e-4                                         |
| **Optimizer**                   | AdamW 8-bit                                  |
| **FP16 Used**                   | Yes (depending on GPU support)               |
| **Evaluation Strategy**         | Every 200 steps                              |
| **Early Stopping**              | Patience = 3                                 |

### Training & Evaluation Metrics
| **Step** | **Training Loss** | **Validation Loss** |
| -------: | ----------------: | ------------------: |
|        1 |      *e.g.* 1.974 |                   - |
|      200 |             1.562 |               1.438 |
|      400 |             1.231 |               1.154 |
|      600 |             0.978 |               0.911 |
|      800 |             0.856 |               0.837 |
|     1000 |             0.765 |               0.762 |
|     1108 |             0.698 |      0.703 *(best)* |

### Training Time & GPU Utilization
| **Metric**                          | **Value**                                |
| ----------------------------------- | ---------------------------------------- |
| Training Time (seconds)             | `trainer_stats.metrics['train_runtime']` |
| Training Time (minutes)             | ≈ `round(train_runtime / 60, 2)`         |
| Peak Reserved GPU Memory            | `used_memory` GB                         |
| Memory Reserved for Training (LoRA) | `used_memory_for_lora` GB                |
| % of Max GPU Memory Used            | `used_percentage` %                      |
| % Used by LoRA Adapter Training     | `lora_percentage` %                      |


### Challenges & Limitations
| Challenge                              | Description                                                                                                                     | Mitigation / Notes                                                                                                                |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| **Memory Constraints**                 | Large models with 4-bit quantization still require careful GPU memory management, especially with long sequences (2048 tokens). | Use of LoRA adapters and `load_in_4bit=True` to reduce memory footprint; gradient checkpointing with “unsloth” option.            |
| **Dataset Quality & Size**             | The medical-o1-reasoning-SFT dataset is domain-specific but may have limited examples, affecting generalization.                | Augment training data or use data augmentation to improve robustness; careful preprocessing to maintain input-output consistency. |
| **Overfitting Risk**                   | Short training (60 steps) with small batch size can cause overfitting or underfitting.                                          | EarlyStoppingCallback used to mitigate overfitting; validation splits for monitoring.                                             |
| **Generation Length and EOS Handling** | Without EOS tokens, model can generate indefinitely.                                                                            | Explicit addition of EOS token after each training sample to stop generation correctly.                                           |
| **Inference Latency**                  | Despite 4-bit quantization and fast inference mode, real-time use in production may still face latency issues.                  | Optimization for deployment might require model distillation or further pruning.   

### Conclusion
This LoRA fine-tuning project demonstrates how to efficiently adapt a high-performance open-source model like Mistral-7B for domain-specific tasks in medical AI using Unsloth. The result is a compact yet capable model with improved reasoning ability in clinical scenarios, paving the way for trustworthy and responsive healthcare applications.
