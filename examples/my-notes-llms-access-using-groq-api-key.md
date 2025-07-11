### 1. Notes:
1. groq API Key provides access to host of LLMs with single API Key.
2. Few models are groq owned while rest are from other companies but served by groq.
3. Every LLM does not serve /chat/completions end-point

### 2. Models accessible using groq API key
[See list here](./docker-compose/basic/groq-models.md)
#### 2.1 Works out of box with groq API key
```
groq/qwen-qwq-32b
groq/allam-2-7b
groq/qwen/qwen3-32b
groq/llama-3.1-8b-instant
groq/compound-beta
groq/meta-llama/llama-guard-4-12b
groq/meta-llama/llama-4-maverick-17b-128e-instruct
groq/llama3-8b-8192
groq/meta-llama/llama-prompt-guard-2-86m
groq/llama3-70b-8192
groq/gemma2-9b-it
groq/meta-llama/llama-4-scout-17b-16e-instruct
groq/llama-3.3-70b-versatile
groq/meta-llama/llama-prompt-guard-2-22m
groq/deepseek-r1-distill-llama-70b
```
#### 2.2 Additional notes on use of each model

2.2.1 groq/distil-whisper-large-v3-en"
```
  "error": "Failed to generate tokens: HTTP error: 400 - Error generating chat completion: {\"error\":{\"message\":\"The mode2 `distil-whisper-large-v3-en` does not support chat completions\",\"type\":\"invalid_request_error\"}}\n"
```
2.2.2 groq/qwen-qwq-32b
- works

2.2.3 groq/allam-2-7b
- works

2.2.4 groq/playai-tts
```
  "error": "Failed to generate tokens: HTTP error: 400 - Error generating chat completion: {\"error\":{\"message\":\"The model `playai-tts` requires terms acceptance. Please have the org admin accept the terms at https://console.groq.com/playground?model=playai-tts\",\"type\":\"invalid_request_error\",\"code\":\"model_terms_required\"}}\n"
```
2.2.5 groq/qwen/qwen3-32b
- Works

2.2.6 groq/llama-3.1-8b-instant
- Works

2.2.7 groq/meta-llama/llama-prompt-guard-2-22m
- Works but no text content. A decimal number.

2.2.8 groq/compound-beta
- Works

2.2.9 groq/meta-llama/llama-guard-4-12b
- Works but no response

2.2.10 groq/meta-llama/llama-4-maverick-17b-128e-instruct
- Works

2.2.11 groq/compound-beta-min
```
  "error": "Failed to generate tokens: HTTP error: 404 - Error generating chat completion: {\"error\":{\"message\":\"The model `compound-beta-min` does not exist or you do not have access to it.\",\"type\":\"invalid_request_error\",\"code\":\"model_not_found\"}}\n"
```
2.2.12 groq/llama3-8b-8192
- Works

2.2.13 groq/meta-llama/llama-prompt-guard-2-86m
- Works but no text content. A decimal number.

2.2.14 groq/whisper-large-v3
```
  "error": "Failed to generate tokens: HTTP error: 400 - Error generating chat completion: {\"error\":{\"message\":\"The model `whisper-large-v3` does not support chat completions\",\"type\":\"invalid_request_error\"}}\n"
```
2.2.15 groq/llama3-70b-8192
- Works

2.2.16 groq/gemma2-9b-it
- Works

2.2.17 groq/meta-llama/llama-4-scout-17b-16e-instruct
- Works

2.2.18 groq/llama-3.3-70b-versatile
-Works

2.2.19 groq/whisper-large-v3-turbo
```
  "error": "Failed to generate tokens: HTTP error: 400 - Error generating chat completion: {\"error\":{\"message\":\"The model `whisper-large-v3-turbo` does not support chat completions\",\"type\":\"invalid_request_error\"}}\n"
```
2.2.20 groq/mistral-saba-24b
```
  "error": "Failed to generate tokens: HTTP error: 400 - Error generating chat completion: {\"error\":{\"message\":\"The model `mistral-saba-24b` requires terms acceptance. Please have the org admin accept the terms at https://console.groq.com/playground?model=mistral-saba-24b\",\"type\":\"invalid_request_error\",\"code\":\"model_terms_required\"}}\n"
```
2.2.21 groq/meta-llama/llama-prompt-guard-2-22m
- Works but no text content. A decimal number.

2.2.22 groq/deepseek-r1-distill-llama-70b
- Works

2.2.23 groq/playai-tts-arabic
```
  "error": "Failed to generate tokens: HTTP error: 400 - Error generating chat completion: {\"error\":{\"message\":\"The model `playai-tts-arabic` requires terms acceptance. Please have the org admin accept the terms at https://console.groq.com/playground?model=playai-tts-arabic\",\"type\":\"invalid_request_error\",\"code\":\"model_terms_required\"}}\n"
```
2.2.24 deepseek/deepseek-chat
```
  "error": "Failed to generate tokens: HTTP error: 402 - Error generating chat completion: {\"error\":{\"message\":\"Insufficient Balance\",\"type\":\"unknown_error\",\"param\":null,\"code\":\"invalid_request_error\"}}"
```
2.2.25 deepseek/deepseek-reasoner
```
  "error": "Failed to generate tokens: HTTP error: 402 - Error generating chat completion: {\"error\":{\"message\":\"Insufficient Balance\",\"type\":\"unknown_error\",\"param\":null,\"code\":\"invalid_request_error\"}}"
```
