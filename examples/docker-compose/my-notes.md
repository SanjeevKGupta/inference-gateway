### Examples docker-compose

https://github.com/SanjeevKGupta/inference-gateway/tree/main/examples/docker-compose

1. [Setup on Mac](#1-setup-on-mac)
2. [Basic Examples](#2-basic-examples)


### 1. Setup on Mac
#### 1.1 You will need `docker`CLI 
Docker desktop having restriction on Mac. Use rancher desktop instead. Comes with `docker` CLI.
- https://rancherdesktop.io
#### 1.2 Update PATH
```
export PATH=$PATH:/Applications/Rancher\ Desktop.app/Contents/Resources/resources/darwin/bin
```
#### 1.3 Fork and then clone locally on your Mac 
```
git clone https://github.com/SanjeevKGupta/inference-gateway.git
```
### 2. Basic Examples
#### 2.1 Create a `.env` file
```
cp .env.example .env
```
#### 2.2 Update API Key in `.env`
Update one of the LLM providers API Key in .env. I used GROQ_API_KEY from `groqcloud`
```
GROQ_API_URL=https://api.groq.com/openai/v1
GROQ_API_KEY=<groq-api-key>
```
#### 2.3 Review `docker-compose.yml` file
Note use of `.env` file,  `port`, `cpu` and `memory` resources used. You may have to modify them as needed, if current values do not work. 
#### 2.4 Start `inference-gateway` docker container and verify
This creates a `inference-gateway` docker container that is accessible on localhost at the above `port`(default 8080) 
to query specified model using the specified API_KEY . 
```
docker compose up -d
docker ps
```
#### 2.5 Get list of `groqcloud` hosted models
```
curl -X GET http://localhost:8080/v1/models | jq -r
```
#### 2.6 Test with `curl` command
Use the list of models and try testing with different models. Outputs may be different. All models may not work. See notes below.
```
curl -s -X POST http://localhost:8080/v1/chat/completions -H "Content-Type: application/json" -d '{"model": "groq/llama3-70b-8192", "messages": [{"role": "user", "content": "Hello, how are you today?" }]}' | jq -r
```
#### 2.7 Test notes.
See my notes for the models that work out of the box and few others errorin out.





