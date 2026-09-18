Recent methods attempt to model internal structure more directly through sparse embeddings ( Deiseroth et al., 2024 ) or morpheme-aware GRU frameworks ( Singh et al., 2023 ) , but these approaches add training complexity and remain underexplored in large-scale LLMs.
Security should not be an afterthought: tokenization must be included in adversarial testing, with controlled preprocessing, careful treatment of special tokens, and rigorou


Source: https://arxiv.org/pdf/2601.13260v1
Title: Stop Taking Tokenizers for Granted: They Are Core Design Decisions in Large Language Models
Description: Stop Taking Tokenizers for Granted: They Are Core Design Decisions in Large Language Models
Tokenization underlies every large language model, yet it remains an under-theorized and inconsistently designed component. Common subword approaches such as Byte Pair En- coding (BPE) offer scalability but often mis- align with linguistic structure, amplify bias, and waste capacity across languages and do- mains.
Large Language Models (LLMs) have achieved remarkable success across tasks ranging from flu- ent text generation to advanced reasoning that now pushes the boundaries of agentic behavior (Laskar et al., 2023a; OpenAI, 2024; Grattafiori et al., 2024; Anil et al., 2025; Anthropic, 2025; DeepSeek-AI et al., 2025; Langford et al., 2025).
arXiv:2601.13260v1 [cs.CL] 19 Jan 2026
We empha- size that this perspective does not assume the abil- ity to retrain LLMs from scratch, which remains infeasible for many practitioners and language com- munities, particularly in low-resource settings. Our position is supported by Dewangan et al.
Subword tokenization addresses the drawbacks of word- and character-level models. Word-level ap- proaches suffer from large vocabularies and out- of-vocabulary issues, while character-level mod- els create long sequences that raise computational costs and obscure linguistic structure (Beinborn and Pinter, 2023).
View 1 — Subword Tokenization Is Sufficient and Scalable
Claim Subword methods are considered a practical default, offering scalability, compression, and stable training. Their success in general-purpose models has led to the assumption that changing the tokenizer has a minimal impact on LLMs (Jimenez Gutierrez et al., 2023; Schmidt et al., 2024).
Moreover, even large models suffer from inefficiencies due to poor tokenization, such as inflated sequence lengths. 2
View 2 — Current Evaluation Metrics Accurately Reflect Tokenizer Quality
Claim In the context of LLMs, tokenization is often deprioritized because existing evaluations suggest minimal performance differences across tokenizers.
2.2 Emerging Alternatives for the De Facto Tokenization Paradigm
Tokenization based on statistical frequency over large corpora has long dominated NLP work- flows, but it struggles with rare languages, domain-specific terms, and structural linguistic nu- ances (Meyer and Buys, 2022; Liu et al., 2024a; Chai et al., 2024).
2023), but these approaches add training complex- ity and remain underexplored in large-scale LLMs.
From Subwords to Raw Bytes: Subword tok- enizers struggle with unseen chara

