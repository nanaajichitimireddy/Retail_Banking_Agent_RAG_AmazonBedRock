# Retail Banking Agent - Using RAG and Amazon Bedrock
<img width="1589" height="795" alt="Retail_bank_agent" src="https://github.com/user-attachments/assets/9cf61c89-3e12-48bb-870e-7b7dadfc83dc" />

## Overview
This repository contains the architecture and implementation details for a **Retail Banking Agent** built using **Amazon Bedrock**. The system acts as an intelligent virtual assistant capable of handling customer banking queries, such as checking the status of a new account or explaining banking terminology, by seamlessly orchestrating between real-time database queries and an internal knowledge base.

## Architecture

The architecture utilizes a multi-layered approach centered around an **Amazon Bedrock Agent** to process natural language queries and route them to either actionable backend services or document retrieval systems.

### Core Components

1. **User Interaction & Pre-processing**
   - **User Input:** Customers interact with the agent, asking questions like "What is the status of AccountId=7777?" or "What does InvalidAddressProof mean?".
   - **Pre-processing:** The system formats the user's question into a prompt (e.g., "What is the status of bank AccountID = 7777?") supported by a **Prompt Store**.
   - **Bedrock Agent:** Powered by the **Claude 3 Sonnet** Foundation Model (FM), the agent processes the input and determines the next course of action (Orchestration). It utilizes **Chain of Thought (CoT) Reasoning** (Pre-processing, Orchestration, Post-processing) to determine how to resolve the user's intent.

2. **Action Groups (Database Querying)**
   - Used for fetching dynamic, real-time data.
   - **OpenAPI Schema:** Defines the API structure (e.g., `newBankAccountStatus` API for checking account status). It acts as the contract for the Bedrock Agent to know how to interact with the backend.
   - **AWS Lambda:** The Agent invokes a Lambda function based on the OpenAPI schema.
   - **Amazon DynamoDB:** The Lambda function retrieves the requested data (e.g., account status) from the DynamoDB table and returns the data back to the Agent.

3. **Knowledge Base / RAG (Document Retrieval)**
   - Used for answering static or informational queries (e.g., explaining "InvalidAddressProof").
   - **Amazon Bedrock Knowledge Bases:** Orchestrates the Retrieval-Augmented Generation (RAG) workflow.
   - **S3 Bucket:** Stores the source documents (PDFs) containing banking policies and terminology.
   - **Chunking & Embedding:** Documents are split into chunks and converted into vector embeddings using **Amazon Titan**.
   - **Vector Store (AWS OpenSearch):** Stores the embeddings. The Agent queries this vector store to find the most relevant context to answer the user's informational question.

## Workflow Execution

1. The user asks a question via the frontend interface.
2. The **Bedrock Agent (Claude 3 Sonnet)** analyzes the intent using CoT reasoning.
3. **Decision / Orchestration:**
   - If the user asks for account status (dynamic data), the Agent uses the **Action Group**. It reads the **OpenAPI schema**, invokes the **AWS Lambda** function, which in turn queries **DynamoDB**, and returns the specific account status.
   - If the user asks about terminology or policies (static data), the Agent queries the **Knowledge Base**. It searches the **AWS OpenSearch Vector Store** for relevant information previously ingested from the **S3 Bucket**.
4. The Agent formulates a natural language response based on the retrieved data and returns it to the user.

## Prerequisites
- AWS Account with **Amazon Bedrock** access.
- Enabled access for **Anthropic Claude 3 Sonnet** and **Amazon Titan** foundation models.
- **Amazon DynamoDB** table configured with customer account data.
- **Amazon S3** bucket for storing policy documents.
- Permissions to deploy and invoke **AWS Lambda** functions.

## Setup Instructions
1. Refer to the AWS sample OpenAI schemas - https://docs.aws.amazon.com/bedrock/latest/userguide/agents-api-
schema.html

2. Convert to YAML

3. Modify the Schema as per the requirement of the use case

4. Create an S3 bucket and store the Open API Schema in the S3 bucket
5. Permission for Agent to invoke Lambda function
• bedrock-agent
• bedrock.amazonaws.com
• ARN
• Lambda:InvokeFunction

Agent Instructions
You are an banking assistant in a Retail Bank. You are friendly and polite. You help resolve customer queries by
providing bank customers status on their new bank accounts.
