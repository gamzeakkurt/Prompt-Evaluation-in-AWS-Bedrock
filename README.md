# Prompt Evaluation in AWS Bedrock

This repository demonstrates practical approaches for evaluating AI-generated outputs using **Amazon Bedrock**. It focuses on lightweight, reproducible techniques to assess the quality of model responses for structured tasks such as generating **Python code, JSON, and Regular Expressions**.

## 🚀 Overview

Evaluating LLM outputs is critical for building reliable AI systems. This project implements three core grading strategies:

### 1. Code Graders

Programmatic validation using deterministic logic:

* JSON validation via parsing
* Python syntax validation via AST
* Regex validation via compilation

### 2. Model Graders

Use an LLM (Claude 3.5 Haiku via Bedrock) to:

* Analyze outputs
* Provide structured feedback
* Assign quality scores

### 3. Human Graders

Manual review for:

* Subjective quality assessment
* Edge cases
* Final validation (not automated in this repo, but supported conceptually)

---

## 🧠 Architecture

The workflow consists of:

1. **Dataset Generation**

   * Automatically generate evaluation tasks using an LLM

2. **Response Generation (Notebook)**

   * Prompts are executed and responses are collected

3. **Evaluation**

   * Syntax-based grading (code graders)
   * LLM-based grading (model graders)

---

## 🛠️ Setup

### Prerequisites

* Python 3.9+
* AWS account with Bedrock access
* `boto3` configured with credentials

### Install Dependencies

```bash
pip install boto3
```

---

## ⚙️ Configuration

```python
import boto3

client = boto3.client("bedrock-runtime", region_name="us-west-2")

# Claude 3.5 Haiku (fast + cost-effective for evaluation)
model_id = "us.anthropic.claude-3-5-haiku-20241022-v1:0"
```

---

## 💬 Chat Helper

Utility wrapper for interacting with Bedrock:

```python
def chat(messages, system=None, temperature=1.0, stop_sequences=[]):
    params = {
        "modelId": model_id,
        "messages": messages,
        "inferenceConfig": {
            "temperature": temperature,
            "stopSequences": stop_sequences,
        },
    }

    if system:
        params["system"] = [{"text": system}]

    response = client.converse(**params)
    return response["output"]["message"]["content"][0]["text"]
```

---

## 📊 Dataset Generation

Generates structured evaluation tasks:

```python
def generate_dataset():
    ...
```

Each dataset item includes:

* `task`: Problem description
* `format`: `python`, `json`, or `regex`
* `solution_criteria`: Evaluation guidelines

---

## 🤖 Model-Based Grading

Uses Claude to evaluate outputs:

```python
def grade_by_model(test_case, output):
    ...
```

### Output Format

```json
{
  "strengths": ["..."],
  "weaknesses": ["..."],
  "reasoning": "...",
  "score": 8
}
```

---

## 🔍 Code-Based Grading

Fast, deterministic validation:

```python
def grade_syntax(response, test_case):
    ...
```

### Supported Validators

* ✅ JSON → `json.loads`
* ✅ Python → `ast.parse`
* ✅ Regex → `re.compile`

Returns:

* `10` → Valid
* `0` → Invalid

---

## 📓 Notebook

A Jupyter notebook (to be added) demonstrates:

* End-to-end evaluation workflow
* Prompt testing
* Scoring comparisons

---

## 📈 Evaluation Strategy

| Method        | Speed    | Cost   | Accuracy   | Use Case            |
| ------------- | -------- | ------ | ---------- | ------------------- |
| Code Graders  | ⚡ Fast   | 💲 Low | ⚖️ Medium  | Syntax validation   |
| Model Graders | ⏱ Medium | 💲💲   | 🎯 High    | Semantic evaluation |
| Human Review  | 🐢 Slow  | 💲💲💲 | 🧠 Highest | Final judgment      |

---

## 🔮 Future Improvements

* Add human annotation interface
* Store evaluation results (e.g., DynamoDB / S3)
* Benchmark multiple models
* Add unit-test-based grading for Python tasks

---

## 📚 References

* [Amazon Bedrock Documentation](https://docs.aws.amazon.com/bedrock/)
* [Anthropic Claude Models](https://www.anthropic.com/)

---


## 🤝 Contributing

Contributions are welcome! Feel free to open issues or submit pull requests to improve evaluation methods or extend support for additional formats.

---

## ⭐ Summary

This repo provides a simple but powerful framework for:

* Generating evaluation datasets
* Scoring LLM outputs
* Comparing grading strategies

It’s a practical starting point for anyone building **LLM evaluation pipelines on AWS Bedrock**.
