effectiveness is fundamentally constrained by tokenization. While tokenization critically impacts
transformer performance on code [15], existing approaches operate on disassembled text, limiting
applicability to stripped or obfuscated binaries.

Raw byte transformers [13]
avoid disassembly but lack learned vocabulary compression. The challenge remains: can one develop
a byte-level BPE tokenizer that learns cross-platform patterns from raw binaries without disassembly
or decompilation?