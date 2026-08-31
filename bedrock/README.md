AWS Bedrock Prompt Engineering Experiments

Hands-on experiments exploring prompt engineering and multi-model inference using Amazon Bedrock and SageMaker, built as part of my learning journey toward AWS/ML engineering skills.

Overview

This project demonstrates how to invoke and compare multiple foundation models available through Amazon Bedrock — including Anthropic's Claude, Amazon's Nova, Meta's Llama, and DeepSeek — from a SageMaker JupyterLab notebook. It covers practical prompt engineering techniques (including zero-shot prompting) and highlights real-world AWS configuration challenges encountered along the way (IAM roles, cross-region inference profiles, and Marketplace model access).

What's inside
prompt_eng.ipynb — Notebook containing:
Bedrock invoke_model / Converse API calls
Prompt design and comparison across models
Model configuration for Claude Haiku 4.5, Claude Sonnet 4.5, Amazon Nova Pro, Amazon Nova 2 Lite, and Meta Llama 3.1 70B
text-generation.py — Script that:
Sends a zero-shot prompt to Bedrock via invoke_model
Uses the DeepSeek v3.2 model and parses the choices[0]['message']['content'] field from the response
Handles AccessDeniedException gracefully with a clear troubleshooting message
Tech stack
Amazon Bedrock — foundation model access (Anthropic Claude, Amazon Nova, Meta Llama, DeepSeek)
Amazon SageMaker — notebook environment
boto3 — AWS SDK for Python
IAM — role-based access control for Bedrock and Marketplace permissions
Key learnings
Newer Claude models on Bedrock require cross-region inference profile IDs (e.g. us.anthropic.claude-haiku-4-5-20251001-v1:0) rather than bare model IDs, since they route through cross-region inference rather than on-demand throughput.
Enabling access to third-party models (Anthropic, Meta) triggers an AWS Marketplace subscription behind the scenes, which requires:
IAM permissions for aws-marketplace:ViewSubscriptions and aws-marketplace:Subscribe
A valid payment method on the account, even when covered entirely by promotional credits
Amazon's first-party models (Nova Pro, Nova 2 Lite) don't require a Marketplace subscription, making them a good starting point when Marketplace access is restricted.
Different model providers on Bedrock return responses in different shapes — Claude models return content[0]['text'], while OpenAI-compatible models like DeepSeek return choices[0]['message']['content']. Response parsing needs to be adapted per model.
Setup
Clone this repo.
Ensure you have an AWS account with Bedrock model access enabled for the models you want to test.
Configure AWS credentials (aws configure or a SageMaker execution role with Bedrock permissions).
Install dependencies:
bash
   pip install boto3
Open prompt_eng.ipynb in Jupyter/SageMaker and run the cells, or run python text-generation.py directly.
