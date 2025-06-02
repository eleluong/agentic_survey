# State-of-the-Art LLM Variants by Category

## 1. Text-Only Models
| Model                | Architecture          | Key Features                               | Use Cases               |
|----------------------|----------------------|-------------------------------------------|-------------------------|
| **Claude 3 Opus** (Anthropic) | Dense decoder-only Transformer | Competitive with GPT-4 (MMLU ~87%), multilingual, low refusal rate | Analysis, coding, chatbots |
| **Mistral Large 2**          | 123B dense Transformer | 128K context, top-tier code/math, open weights, efficient inference | Long-context reasoning, multilingual NLP |
| **LLaMA 3.1** (Meta)         | 405B parameters       | Largest open text-only (MMLU ~87%), multilingual | Enterprise, research |

## 2. Vision-Enabled (Multimodal) Models
| Model                         | Architecture               | Key Features                               | Use Cases                     |
|-------------------------------|---------------------------|-------------------------------------------|-------------------------------|
| **GPT-4V** (OpenAI)           | Dense Transformer         | Text + image input, high visual reasoning | Document/image analysis, medical |
| **Gemini 1.5 Pro** (Google)   | Sparse MoE                | 1M token context, text/image/audio/video  | Long-form multimodal agents   |
| **Claude 3 Vision**           | -                         | Visual QA (charts/graphs/photos)          | Presentations, diagrams       |
| **Pixtral Large** (Mistral)   | 124B + 1B vision encoder  | SOTA on ChartQA/DocVQA, open weights      | Open-source vision tasks      |

## 3. Mixture-of-Experts (MoE) Models
| Model                                      | Architecture                     | Key Features                                  | Use Cases                   |
|--------------------------------------------|----------------------------------|----------------------------------------------|-----------------------------|
| **Mixtral 8x7B**                           | 46.7B total (12.9B active)       | Open weights, high efficiency                | Scalable generation, RAG    |
| **Qwen3** (Alibaba)                        | 128 experts (8 active)           | 22B/3B active, 128K context, dual-mode       | Agents, multilingual LLMs   |
| **LLaMA 4 Scout/Maverick** (Meta)          | Scout: 109B total (17B active)<br>Maverick: 400B total (17B active) | 10M/1M context, text+image | High-efficiency reasoning |
| **DeepSeek R1/R1-Zero**                    | 671B total (37B active MoE)      | Zero supervised fine-tuning, self-reflection | Autonomous agents, planning |

## 4. Omni-Modal Models
| Model                   | Architecture   | Key Features                              | Use Cases                          |
|-------------------------|---------------|------------------------------------------|------------------------------------|
| **GPT-4o** (OpenAI)     | Dense + MoE   | Real-time output (232ms), all modalities | Interactive agents, voice/video    |
| **Gemini 1.5 Pro**      | Unified       | Cross-media understanding                | Complex multimodal analysis        |
| **Grok 1.5V** (xAI)     | -             | Diagrams/spatial reasoning               | Robotics, real-world object QA     |

---

## 📊 Model Type Comparison
| Category                  | Modalities               | Architecture  | Context Length | Performance               | Best Use Cases                |
|---------------------------|--------------------------|---------------|----------------|---------------------------|-------------------------------|
| **Text-Only**             | Text                     | Dense         | ≤128K+         | GPT-4-level reasoning     | Coding, chat, summarization   |
| **Vision-Enabled**        | Text + Image             | Dense         | ≤128K          | Visual QA (charts/docs)   | Diagrams, medical images     |
| **Mixture-of-Experts**    | Text (some multimodal)  | Sparse MoE    | ≤10M           | High efficiency           | Cost-sensitive agents, RAG    |
| **Omni-Modal**            | All modalities           | Hybrid        | ≤1M            | Real-time interaction     | Multimedia assistants        |

---

## 🧭 Key Takeaways
- **Text-Only Models**  
  *When to use:* Pure language tasks requiring strong reasoning.  
  **Top picks:** Claude 3 Opus (closed), LLaMA 3.1 (open)

- **Vision-Enabled Models**  
  *When to use:* Image/document understanding.  
  **Top picks:** GPT-4V (commercial), Pixtral (open-source)

- **MoE Models**  
  *When to use:* Compute-efficient scaling.  
  **Top picks:** Mixtral 8x7B (lightweight), DeepSeek R1 (planning)

- **Omni-Modal Models**  
  *When to use:* Real-time multimodal interaction.  
  **Top picks:** GPT-4o (low latency), Gemini 1.5 Pro (long-context)

---

🔗 **Model Resources**  
- [Claude 3](https://www.anthropic.com/index/claude)  
- [Mistral](https://mistral.ai)  
- [LLaMA](https://ai.meta.com/llama/)  
- [Gemini 1.5](https://deepmind.google/technologies/gemini/)  
- [GPT-4](https://openai.com/gpt-4)  
- [DeepSeek R1](https://github.com/deepseek-ai/DeepSeek-R1)  
- [Qwen3](https://github.com/QwenLM/Qwen3)  