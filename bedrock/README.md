# AWS Bedrock Prompt Engineering Experiments

Hands-on experiments exploring prompt engineering and multi-model inference using Amazon Bedrock and SageMaker, built as part of my learning journey toward AWS/ML engineering skills.

## Overview

This project demonstrates how to invoke and compare multiple foundation models available through Amazon Bedrock — including Anthropic's Claude, Amazon's Nova, Meta's Llama, DeepSeek, and Moonshot AI's Kimi — from a SageMaker JupyterLab notebook. It covers practical prompt engineering techniques (zero-shot prompting, both standard and streamed responses) and highlights real-world AWS configuration challenges encountered along the way (IAM roles, cross-region inference profiles, and Marketplace model access).

## What's inside

- `prompt_eng.ipynb` — Notebook containing:
  - Bedrock `invoke_model` / `Converse` API calls
  - Prompt design and comparison across models
  - Model configuration for Claude Haiku 4.5, Claude Sonnet 4.5, Amazon Nova Pro, Amazon Nova 2 Lite, and Meta Llama 3.1 70B

- `text-generation.py` — Script that:
  - Sends a zero-shot prompt to Bedrock via `invoke_model`
  - Uses the DeepSeek v3.2 model and parses the `choices[0]['message']['content']` field from the response
  - Handles `AccessDeniedException` gracefully with a clear troubleshooting message

- `streaming-text-generation.py` — Script that:
  - Sends a zero-shot prompt to Bedrock via `invoke_model_with_response_stream`
  - Uses the Moonshot AI Kimi K2.5 model, reading the response as a stream of chunks rather than a single completed response
  - Reconstructs the full output by concatenating each chunk's `delta.content` field as it arrives
  - Handles `AccessDeniedException` gracefully with a clear troubleshooting message

## Tech stack

- **Amazon Bedrock** — foundation model access (Anthropic Claude, Amazon Nova, Meta Llama, DeepSeek, Moonshot AI Kimi)
- **Amazon SageMaker** — notebook environment
- **boto3** — AWS SDK for Python
- **IAM** — role-based access control for Bedrock and Marketplace permissions

## Key learnings

- Newer Claude models on Bedrock require **cross-region inference profile IDs** (e.g. `us.anthropic.claude-haiku-4-5-20251001-v1:0`) rather than bare model IDs, since they route through cross-region inference rather than on-demand throughput.
- Enabling access to third-party models (Anthropic, Meta) triggers an **AWS Marketplace subscription** behind the scenes, which requires:
  - IAM permissions for `aws-marketplace:ViewSubscriptions` and `aws-marketplace:Subscribe`
  - A valid payment method on the account, even when covered entirely by promotional credits
- Amazon's first-party models (Nova Pro, Nova 2 Lite) don't require a Marketplace subscription, making them a good starting point when Marketplace access is restricted.
- Different model providers on Bedrock return responses in different shapes — Claude models return `content[0]['text']`, while OpenAI-compatible models like DeepSeek and Kimi return `choices[0]['message']['content']` (or `choices[0]['delta']['content']` when streaming). Response parsing needs to be adapted per model and per API (standard vs. streaming).
- `invoke_model_with_response_stream` returns the response as a series of chunked events rather than one complete payload — each chunk must be decoded and its partial content concatenated in order to reconstruct the full response, and the final chunk carries `finish_reason: "stop"` along with invocation metrics (token counts, latency).

## Setup

1. Clone this repo.
2. Ensure you have an AWS account with Bedrock model access enabled for the models you want to test.
3. Configure AWS credentials (`aws configure` or a SageMaker execution role with Bedrock permissions).
4. Install dependencies:
   ```bash
   pip install boto3
   ```
5. Open `prompt_eng.ipynb` in Jupyter/SageMaker and run the cells, or run `python text-generation.py` / `python streaming-text-generation.py` directly.
