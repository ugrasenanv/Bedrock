# AWS Bedrock Hands-On Repo

## Overview
This repository contains practical AWS Bedrock examples that demonstrate how to build serverless generative AI solutions using AWS Bedrock, Amazon S3, and AWS Lambda.

The project includes three core use cases:
- **Code generation** using Anthropic Claude via AWS Bedrock
- **Image generation** using Stability AI Stable Diffusion via AWS Bedrock
- **Meeting summarization** using Anthropic Claude via AWS Bedrock

Each use case shows an end-to-end flow from API request to model invocation to saving results in S3.

## End-to-End Architecture

1. **Input Request**
   - A client sends a request, typically through API Gateway or another HTTP front-end.
   - The payload includes the user prompt or meeting content.

2. **AWS Lambda Execution**
   - The relevant Lambda function receives the request.
   - It constructs a Bedrock-compatible request payload.
   - It calls `bedrock-runtime.invoke_model()` to run the model.

3. **Model Output**
   - For code generation and meeting summarization, Bedrock returns text output.
   - For image generation, Bedrock returns a base64-encoded image artifact.

4. **Result Storage**
   - The Lambda functions save outputs to an S3 bucket named `bedrock-course-bucket`.
   - Code results are stored under `code-output/`, summaries under `summary-output/`, and images under `output-images/`.

## File Explanations

### `code_generation.py`
This file implements a Lambda handler for generating code from a natural language prompt.

- `generate_code_using_bedrock(message: str, language: str) -> str`
  - Builds a prompt using the user message and target programming language.
  - Calls Bedrock model `anthropic.claude-v2` to generate code.
  - Uses generation settings such as `temperature`, `top_k`, `top_p`, and `stop_sequences`.

- `save_code_to_s3_bucket(code, s3_bucket, s3_key)`
  - Saves the generated code to an S3 object.
  - Prints success or error messages for debugging.

- `lambda_handler(event, context)`
  - Parses the incoming request body for `message` and `language`.
  - Invokes the code generator and saves the results to S3.
  - Returns a standard HTTP response.

### `image_generation.py`
This file implements a Lambda handler for generating images using Bedrock.

- `lambda_handler(event, context)`
  - Parses a JSON body containing a `message` prompt.
  - Calls Bedrock model `stability.stable-diffusion-xl-v0`.
  - Uses settings such as `cfg_scale`, `seed`, and `steps`.
  - Reads the base64 image artifact from the Bedrock response.
  - Decodes the image and uploads it to S3 as a PNG.

### `meeting_summarization.py`
This file implements a Lambda handler that extracts meeting text and summarizes it.

- `extract_text_from_multipart(data)`
  - Parses multipart email or form-data content.
  - Extracts the `text/plain` sections.
  - Returns a clean text string for summarization.

- `generate_summary_from_bedrock(content: str) -> str`
  - Builds a summarization prompt from meeting notes.
  - Calls Bedrock model `anthropic.claude-v2` with low temperature for stable output.
  - Returns the summary text.

- `save_summary_to_s3_bucket(summary, s3_bucket, s3_key)`
  - Saves the generated summary to S3.

- `lambda_handler(event, context)`
  - Decodes a base64-encoded request body.
  - Extracts meeting text from multipart content.
  - Generates the meeting summary and saves it to S3.

### `boto3Layer.script`
This script shows how to package `boto3` into a Lambda layer.

- Creates a Python package directory structure expected by Lambda.
- Installs `boto3` into the package path.
- Bundles the package into a zip file.

This is useful when deploying Lambda functions that need a specific version of `boto3`.

## AWS Services Used
- AWS Bedrock
- AWS Lambda
- Amazon S3
- API Gateway (recommended for HTTP trigger)
- Lambda Layers (for packaging dependencies like `boto3`)

## Why AWS Bedrock?
AWS Bedrock is a fully managed service that provides a single API for multiple foundation models from Anthropic, Cohere, Meta, Mistral AI, Stability AI, and Amazon.

Key benefits:
- Access to high-performing foundation models without managing infrastructure.
- Simplified API for text and image generation.
- Built-in security and privacy controls.
- Enables rapid prototyping of generative AI applications.

## What You Learn from This Project
- How to invoke AWS Bedrock models from Python.
- How to build serverless AI applications with AWS Lambda.
- How to store AI outputs in Amazon S3.
- How to work with both text and image generation workflows.
- How to structure prompts and model requests for Claude and Stable Diffusion.

## Learning Outcomes and Value
- Gain insights into AWS Bedrock and LLMs.
- Build a practical foundation covering LLMs and Diffusion Models.
- Apply knowledge through projects like code generation, summarization, and image creation.
- Learn serverless integration with AWS Lambda, API Gateway, and S3.
- Understand ethical model evaluation and responsible prompt design.
- Prepare for real-world AI roles with transferable cloud and AI skills.

## How to Use This Repo
1. Configure AWS credentials and set region to `us-west-2`.
2. Deploy each Python script as a Lambda function.
3. Grant the Lambda functions permissions to invoke Bedrock and write to S3.
4. Connect the functions to API Gateway or another HTTP frontend.
5. Send requests to generate code, images, or meeting summaries.

## Notes
- The S3 bucket name is hard-coded as `bedrock-course-bucket` in the sample code.
- Update bucket names and model IDs as needed for your environment.
- Use Postman or any HTTP client to test Lambda-triggered endpoints.

## Project Vision
This repository is designed to help you learn by doing. It demonstrates how to build an end-to-end AWS Bedrock application that covers input parsing, model invocation, output processing, and cloud-native storage.

By exploring this code and deploying the functions, you gain direct experience with the kinds of AI workflows that are driving modern generative AI solutions.