# Phishing URL Classifier

Fine-tuned a small language model (SmolLM2 1.7B) to detect phishing URLs
using LoRA/QLoRA on a balanced dataset of 120 labeled examples.

## Results
- **Training data:** 120 URLs (60 phishing, 60 safe) from a public dataset
- **Model:** SmolLM2-1.7B-Instruct + LoRA adapters (q_proj, v_proj)
- **Training:** 3 epochs, QLoRA 4-bit quantization, Google Colab T4 GPU
- **Accuracy on 20 unseen URLs:** 17/20 (85%)
- **Loss:** dropped from 2.77 → 1.61 during training

## What I learned
- Balancing safe/unsafe training examples is critical — an unbalanced
  dataset caused false positives on legitimate URLs like amazon.com
- URL-only classifiers have a ceiling: redirect obfuscation attacks
  (e.g. astra-antiques.com/bt32u5) are nearly impossible to catch
  without also analyzing page content
- Homoglyph attacks (picass0.com) and WordPress path abuse need
  specific training examples to be detected reliably

## Failure analysis
| URL | Expected | Got | Why |
|-----|----------|-----|-----|
| picass0.com/rtl/sign.php | UNSAFE | SAFE | Homoglyph substitution (0→o) not in training data |
| ralucatodorut.com/wp-includes/mmc.htm | UNSAFE | SAFE | WordPress path abuse pattern missing from training |
| astra-antiques.com/bt32u5 | UNSAFE | SAFE | Redirect obfuscation — undetectable from URL alone |

## Stack
- Python, HuggingFace Transformers, PEFT, TRL, Google Colab
- Model: HuggingFaceTB/SmolLM2-1.7B-Instruct
- Dataset: shawhin/phishing-site-classification

## Author
 
Kazumasa Iinuma — Applied Mathematics, Boston University  
[GitHub](https://github.com/kiinuma) · [LinkedIn](https://linkedin.com/in/kazumasa-iinuma)
