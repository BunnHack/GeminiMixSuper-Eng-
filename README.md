# Hybrid AI Proxy User Documents 

## Introduction

This project is a hybrid AI agent server that combines the thinking ability of the deepseek-r1 model with the corpus and state capabilities of the gemini model.

## features 

- **Hybrid Model Architecture**：
  - Use DeepSeek-R1 as the front-end system thinking (supports different parameter scales from 7B to 671B)
  - Use Gemini model front-end information processing and discussion group, including processing Internet and polymorphic model information
  - Use Gemini as the main output model
  - Support model failure automatic switching and retry mechanism

- **Multimodality Support**：
 -Image support recognition and understanding
 -Supports simultaneous processing of multiple images
 -Image descriptions are automatically passed to the text model for deep understanding

- **High Availability**：
 - Automatic retry mechanism (up to 3 times)
 - Automatic switching of model failures
 - Streaming response support

- **Smart search function**：
 - AI autonomously determines whether online search is needed
 - Multi-language, multi-keyword comprehensive search
 - Search results are automatically integrated into the conversation context
 - Support real-time search and information update
 - Local https dynamic analysis

## Quick Start

### Prerequisites

- Node.js and npm installed
- Obtained the necessary API keys：
  - DeepSeek-R1 API Key
  - Gemini-Beta API Key
  -  Note that this project requires GeminiAPI to have high concurrency, dynamic load and security filtering, so direct single API cannot be used in this project. It is recommended to deploy a dynamic load project similar to OneAPI.
 -   Due to the different R1 output JSON formats of different provider, only Silicon Flow and the official API are compatible for the time being. Unexpected errors may occur in NIM and Azure.

### Install

1. Clone Project：
```bash
git clone <repository-url>
cd <project-directory>
```

2. Install Dependencies：
```bash
npm install
```

3. Configuring environment variables：
Create `.env` File and configure the following environment variables：

```env
# Proxy server configuration
PROXY_URL=http://your-proxy-url:3000
PROXY_URL2=http://your-proxy-url:3000
PROXY_URL3=http://your-proxy-url:3000
PROXY_PORT=4120

# DeepSeek R1 Configuration
DEEPSEEK_R1_API_KEY=your-api-key
DEEPSEEK_R1_MODEL=deepseek-ai/DeepSeek-R1
DEEPSEEK_R1_MAX_TOKENS=7985
DEEPSEEK_R1_CONTEXT_WINDOW=2000000
DEEPSEEK_R1_TEMPERATURE=0.7

# Image recognition model configuration
Image_Model_API_KEY=your-api-key
Image_MODEL=gemini-exp-1206
Image_Model_MAX_TOKENS=7985
Image_Model_CONTEXT_WINDOW=2000000
Image_Model_TEMPERATURE=0.4

# API Key
OUTPUT_API_KEY=your-api-key

# Online Search model configuration 
GoogleSearch_API_KEY=your-api-key
GoogleSearch_MODEL=gemini-2.0-flash-exp
GoogleSearch_Model_MAX_TOKENS=7985
GoogleSearch_Model_TEMPERATURE=0.4

# Search function prompt configuration
GoogleSearch_Determine_PROMPT=your-prompt
GoogleSearch_PROMPT=your-prompt
GoogleSearch_Send_PROMPT=your-prompt
```

### Start the service

```bash
node main.js
```

## API Instructions

### Text chat request

**Endpoints**：`/v1/chat/completions`

**Method**：`POST`

**Request Header**：
```
Content-Type: application/json
Authorization: Bearer your-api-key
```

**Text chat request body example**：
```json
{
  "model": "Gemini1206MIXR1",
  "messages": [
    {
      "role": "user",
      "content": "Hello, please introduce yourself"
    }
  ],
  "stream": true
}
```

### Image recognition request

**Image request body example**：
```json
{
  "model": "Gemini1206MIXR1",
  "messages": [
    {
      "role": "user",
      "content": [
        {
          "type": "text",
          "text": "What is this picture about？"
        },
        {
          "type": "image_url",
          "image_url": {
            "url": "data:image/jpeg;base64,/9j/4AAQSkZJRg..."
          }
        }
      ]
    }
  ],
  "stream": true
}
```

### Multiple image processing

Support sending multiple pictures in the same message：
```json
{
  "model": "Gemini1206MIXR1",
  "messages": [
    {
      "role": "user",
      "content": [
        {
          "type": "text",
          "text": "比较这两张图片的区别"
        },
        {
          "type": "image_url",
          "image_url": {
            "url": "data:image/jpeg;base64,/9j/4AAQSkZJRg..."
          }
        },
        {
          "type": "image_url",
          "image_url": {
            "url": "data:image/jpeg;base64,/9j/4AAQSkZJRg..."
          }
        }
      ]
    }
  ],
  "stream": true
}
```

## Advanced Features

### DeepSeek-R1 Model Select 

DeepSeek-R1 Supports models with various parameter sizes, ranging from 7B to 671B. You can configure it in the environment variables as needed：

```env
DEEPSEEK_R1_MODEL=deepseek-ai/DeepSeek-R1-7B
# 或
DEEPSEEK_R1_MODEL=deepseek-ai/DeepSeek-R1-67B
# 或
DEEPSEEK_R1_MODEL=deepseek-ai/DeepSeek-R1-671B
```

### Error handling and retry mechanism

- The system will automatically retry failed requests (up to 3 times)
- When the R1 model is unavailable, it will automatically switch to the Gemini model
- Detailed error information will be returned through the status code and response body

### Smart search function

The system integrates advanced AI-driven search capabilities with the following features：

1. **Autonomous judgment mechanism**：
  - AI automatically analyzes the content of the conversation to determine whether an online search is needed
  - Considers multiple dimensions such as timeliness information, professional data, news events, etc.
  - Intelligently avoids unnecessary search requests

2. **Multi-dimensional search strategy**：
 - Automatically generate multiple related search keywords
 - Use both Chinese and English to search
 - Consider professional terms and common terms
 - Support exact phrase matching

3. **Search result processing**：
  - Automatically integrate information from multiple sources
  - Results are filtered and summarized by AI
  - Seamlessly integrate into the conversation context
  - Keep information timely and accurate

4. **Usage scenarios**：
  - Need the latest data or statistics
  - Check for real-time news or events
  - Verify professional knowledge or technical details
  - Need cross-verification of information from multiple sources

### Search function configuration

在 `.env` Configure search related parameters in the file：

```env
# Online search model configuration
GoogleSearch_API_KEY=your-api-key
GoogleSearch_MODEL=gemini-2.0-flash-exp
GoogleSearch_Model_MAX_TOKENS=7985
GoogleSearch_Model_TEMPERATURE=0.4

# Search function prompt configuration
GoogleSearch_Determine_PROMPT=your-prompt
GoogleSearch_PROMPT=your-prompt
GoogleSearch_Send_PROMPT=your-prompt
```

### Search Examples

**Basic conversation request**：
```json
{
  "model": "Gemini1206MIXR1",
  "messages": [
    {
      "role": "user",
      "content": "请告诉我最近的 AI 发展情况"
    }
  ],
  "stream": true
}
```

System Will：
1. Automatically determine the need to search for the latest AI development information
2. Generate multiple search keywords (such as "latest AI developments 2024", "latest progress in artificial intelligence", "AI breakthrough news", etc.)
3. Perform the search and integrate the information
4. Incorporate the search results into the answer

## Precautions

1. Image data needs to be encoded in Base64 format
2. It is recommended to keep `stream: true` for a better response experience
3. All API calls require a valid API key
4. Image recognition results are automatically passed to the text model for deep understanding and answering

## Error code description

- 401: Unauthorized (Invalid API key)
- 429: Too many requests
- 503: Service temporarily unavailable
- 504: Request timed out

## Todo

- [ ] Support more model combinations
- [ ] Add automatic model selection function
- [ ] Optimize image recognition performance
- [ ] Add more error handling mechanisms

Original Repo:https://github.com/lioensky/GeminiMixSuper
