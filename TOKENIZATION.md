BoundlessBPE: Overcomes the pre-tokenization barrier by allowing merges across pretoken (word) boundaries. This produces superwords (concatenations of entire words or substrings), yielding more uniform token frequency distributions, notably higher vocabulary utilization (up to 99%) and improved Rényi efficiency while enhancing compression by 19.7% in bytes per token (Schmidt et al., 31 Mar 2025).


Source: https://arxiv.org/pdf/2511.17573.pdf
Title: Binary BPE: A Family of Cross-Platform Tokenizers for Binary Analysis
Description: To address
this issue, we introduce the Binary BPE family, a set of cross-platform Byte Pair Encoding (BPE)
tokenizers for executables trained on a large corpus of binaries spanning multiple platforms,