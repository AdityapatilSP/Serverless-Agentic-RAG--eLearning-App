# Architecture Notes

## System Components

### 1. Frontend
A Streamlit-based learning interface named **Tech Cloud Byte** collects questions from users.

### 2. API Gateway
Amazon API Gateway exposes the `/elearning` POST endpoint and forwards requests to Lambda.

### 3. Lambda
The `elearning` Lambda function acts as the serverless application backend and connects the API layer with Amazon Bedrock.

### 4. Knowledge Base
Amazon Bedrock Knowledge Bases stores/indexes the application's learning content and retrieves relevant context for user questions.

### 5. S3
Amazon S3 stores the source learning documents used by the Knowledge Base.

### 6. Bedrock
Amazon Bedrock provides the generative AI capability used to formulate responses from retrieved context.

### 7. CloudWatch
Amazon CloudWatch provides execution logs that make the RAG pipeline observable and help troubleshoot requests.

## End-to-End Flow

```text
                    ┌──────────────────────┐
                    │      User            │
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │ Streamlit Frontend   │
                    └──────────┬───────────┘
                               │
                         POST /elearning
                               │
                               ▼
                    ┌──────────────────────┐
                    │ Amazon API Gateway   │
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │ AWS Lambda           │
                    │ elearning            │
                    └───────┬───────┬──────┘
                            │       │
                    retrieve│       │generate
                            ▼       ▼
                  ┌────────────┐ ┌─────────────┐
                  │ Bedrock KB │ │ Bedrock     │
                  │ Retrieval  │ │ Model       │
                  └─────┬──────┘ └──────┬──────┘
                        │                │
                        └───────┬────────┘
                                │
                                ▼
                       Grounded Response
                                │
                                ▼
                         Streamlit UI

S3 ───────────────► Bedrock Knowledge Base
Lambda ───────────► CloudWatch Logs
```
