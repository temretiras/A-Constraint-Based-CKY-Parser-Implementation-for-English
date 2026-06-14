# Constraint-Based CKY Parser for English

**Developers:** Mehmet Ali Özdemir and Tarık Emre Tıraş (Boğaziçi University)

A Python implementation of a feature-augmented Cocke-Younger-Kasami (CKY) parser for English. The system integrates a morphological analyzer with a unification-based grammar to enforce syntactic constraints without expanding the core CFG symbol set.

## System Architecture
* **Morphological Analysis (`MorphAnalyzer`)**: Maps terminal tokens to Penn Treebank POS tags and tracks linguistic features (number, person, tense, verbal subcategorization).
* **Grammar Normalization**: Converts the grammar into Chomsky Normal Form (CNF) via a two-stage pipeline:
  * *Terminal Separation*: Substitutes mixed terminals with dummy non-terminals.
  * *Binarization*: Decomposes $n$-ary rules recursively into binary pairs, attaching feature validators to the top-most rule of the chain.
* **CKY Core Chart Parsing**: A bottom-up dynamic programming parser extended to handle feature constraint validation. Handles unary chains natively and discards ungrammatical derivations immediately during chart construction.
* **Tree Reconstruction & Unbinarization**: Traverses chart backpointers to extract the parse tree and flattens intermediate CNF placeholder rules back into readable $n$-ary structures.

## Implemented Constraints
* **Subject-Verb Agreement**: Validates person and number features between the NP and VP, handling exceptions like 1st-person singular "I".
* **Determiner-Noun Agreement**: Enforces number consistency within noun phrases (e.g., rejecting *"these book"*).
* **Subcategorization (Valency)**: Enforces strict complement structures for transitive (requires object), intransitive (forbids object/allows specific PPs), and copular frames.
* **Interrogatives & Inversion**: Models Subject-Auxiliary Inversion and do-support for Yes/No and Wh-questions, ensuring the verb within the question body remains non-finite (e.g., rejecting *"Did he went?"*).
* **Feature Percolation**: Propagates morphosyntactic features from the head child up to the parent node based on X-bar principles.
* **Semantic Filtering**: Restricts specific noun phrase adjunctions (e.g., temporal adjuncts like *"every night"*) using a semantic time feature filter.

## Performance Evaluation
Tested against a suite of 30 structured sentences to verify syntactic coverage and strict error blocking:

| Test Set Split | Target Phenomenon | Expected Outcome | Result |
| :--- | :--- | :---: | :---: |
| **Valid Set** (20 sentences) | Core arguments, adjunct modification, inversion | $\ge 1$ Parse Tree | **95.0% Pass** *(1 Lexical Gap)* |
| **Invalid Set** (10 sentences) | Agreement mismatches, valency/mood violations | Return `None` | **100% Correctly Rejected** |

* **Total Accuracy**: **96.7%** (29/30 sentences handled correctly).
* Detailed parsing traces and tree outputs are written to `parser_results.txt`.
