### Hybrid deployment - Infra, ollama with depseek and inference-gateway
- Using `docker` that comes with Rancher Desktop.
- See
  - my-notes-basic.md
  - hybrid/README.md

#### 1. Modify `cpu` and `memory` resources in deployment.yaml 
- To lower numbers in line with available resources on Mac, add ollama/mac and update `deployment.yaml`
- Create a separate task target deploy-ollama-mac and use above path `ollama/mac`

#### 2. To avoid this error
```
Error: error getting credentials - err: exec: "docker-credential-osxkeychain": executable file not found in $PATH
```
Update PATH with rancher bin so that `docker-credential-osxkeychain` is in path
```
export PATH=$PATH:/Applications/Rancher\ Desktop.app/Contents/Resources/resources/darwin/bin
```
#### 3. Deploy infrastructure
```
sanjeev@Sanjeevs-MBP hybrid % task deploy-infrastructure

sanjeev@sanjeevs-mbp hybrid % kc get all -n kube-system
NAME                                                READY   STATUS    RESTARTS   AGE
pod/coredns-ff8999cc5-v8j6x                         1/1     Running   0          52m
pod/ingress-nginx-controller-6885cfc548-gjzq7       1/1     Running   0          52m
pod/local-path-provisioner-698b58967b-zfqpg         1/1     Running   0          52m
pod/metrics-server-8584b5786c-k5j8f                 1/1     Running   0          52m
pod/svclb-ingress-nginx-controller-0a202a15-59wlz   2/2     Running   0          52m
pod/svclb-ingress-nginx-controller-0a202a15-c6lr4   2/2     Running   0          52m
pod/svclb-ingress-nginx-controller-0a202a15-wwnpv   2/2     Running   0          52m
pod/svclb-ingress-nginx-controller-0a202a15-z97nm   2/2     Running   0          52m

NAME                                         TYPE           CLUSTER-IP      EXTERNAL-IP                                   PORT(S)                      AGE
service/ingress-nginx-controller             LoadBalancer   10.43.37.94     172.18.0.2,172.18.0.3,172.18.0.4,172.18.0.5   80:30609/TCP,443:32753/TCP   52m
service/ingress-nginx-controller-admission   ClusterIP      10.43.152.99    <none>                                        443/TCP                      52m
service/kube-dns                             ClusterIP      10.43.0.10      <none>                                        53/UDP,53/TCP,9153/TCP       52m
service/metrics-server                       ClusterIP      10.43.113.182   <none>                                        443/TCP                      52m

NAME                                                     DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
daemonset.apps/svclb-ingress-nginx-controller-0a202a15   4         4         4       4            4           <none>          52m

NAME                                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/coredns                    1/1     1            1           52m
deployment.apps/ingress-nginx-controller   1/1     1            1           52m
deployment.apps/local-path-provisioner     1/1     1            1           52m
deployment.apps/metrics-server             1/1     1            1           52m

NAME                                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/coredns-ff8999cc5                     1         1         1       52m
replicaset.apps/ingress-nginx-controller-6885cfc548   1         1         1       52m
replicaset.apps/local-path-provisioner-698b58967b     1         1         1       52m
replicaset.apps/metrics-server-8584b5786c             1         1         1       52m
```
#### 4. Deploy ollama with a model
It may take 3-4 mts for ollama image and model to get pulled. 
```
sanjeev@Sanjeevs-MBP hybrid % task deploy-ollama-mac

sanjeev@sanjeevs-mbp hybrid % kc get all -n ollama     
NAME                          READY   STATUS    RESTARTS   AGE
pod/ollama-5f9db98cf8-lrf6s   2/2     Running   0          52m

NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/ollama   ClusterIP   10.43.177.224   <none>        8080/TCP   31m

NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/ollama   1/1     1            1           52m

NAME                                DESIRED   CURRENT   READY   AGE
replicaset.apps/ollama-5f9db98cf8   1         1         1       52m
```
#### 5. Deploy inference-gateway
```
sanjeev@sanjeevs-mbp hybrid % task deploy-inference-gateway

sanjeev@sanjeevs-mbp hybrid % kc get all -n inference-gateway
NAME                                     READY   STATUS    RESTARTS   AGE
pod/inference-gateway-5bc5979b5c-qkxk9   1/1     Running   0          16m

NAME                        TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
service/inference-gateway   ClusterIP   10.43.80.155   <none>        8080/TCP   16m

NAME                                READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/inference-gateway   1/1     1            1           16m

NAME                                           DESIRED   CURRENT   READY   AGE
replicaset.apps/inference-gateway-5bc5979b5c   1         1         1       16m
```
#### 6. All the namespaces
```
sanjeev@sanjeevs-mbp hybrid % kc get ns
NAME                STATUS   AGE
default             Active   52m
inference-gateway   Active   12m
kube-node-lease     Active   52m
kube-public         Active   52m
kube-system         Active   52m
ollama              Active   51m
```
#### 7. Test with `curl`
##### 7.1 Prompt
hello 
```
curl -s POST http://api.inference-gateway.local/v1/chat/completions  -H "Content-Type: application/json"   -d '{"model":"ollama/deepseek-r1:1.5b","messages":[{"role":"user","content":"Hello"}]}' | jq -r 
{
  "choices": [
    {
      "finish_reason": "stop",
      "index": 0,
      "message": {
        "content": "<think>\n\n</think>\n\nHello! How can I assist you today? 😊",
        "role": "assistant"
      }
    }
  ],
  "created": 1751523542,
  "id": "chatcmpl-437",
  "model": "deepseek-r1:1.5b",
  "object": "chat.completion",
  "usage": {
    "completion_tokens": 16,
    "prompt_tokens": 4,
    "total_tokens": 20
  }
}
```
##### 7.2 Prompt
how is the weather in San francisco"
```
curl -s POST http://api.inference-gateway.local/v1/chat/completions  -H "Content-Type: application/json"   -d '{"model":"ollama/deepseek-r1:1.5b","messages":[{"role":"user","content":"how is the weather in San francisco"}]}' | jq -r
```
##### 7.3 List models
```
curl -s http://api.inference-gateway.local/v1/models
```
##### 7.4 `nference-gateway` Configmap
```
sanjeev@sanjeevs-mbp mac % kc get cm -n inference-gateway inference-gateway-defaults -o yaml
apiVersion: v1
data:
  ANTHROPIC_API_URL: https://api.anthropic.com/v1
  CLIENT_IDLE_CONN_TIMEOUT: 30s
  CLIENT_MAX_IDLE_CONNS: "20"
  CLIENT_MAX_IDLE_CONNS_PER_HOST: "20"
  CLIENT_TIMEOUT: 30s
  CLIENT_TLS_MIN_VERSION: TLS12
  CLOUDFLARE_API_URL: https://api.cloudflare.com/client/v4/accounts/{ACCOUNT_ID}/ai
  COHERE_API_URL: https://api.cohere.ai
  DEEPSEEK_API_URL: https://api.deepseek.com
  ENABLE_AUTH: "false"
  ENABLE_TELEMETRY: "false"
  ENVIRONMENT: production
  GROQ_API_URL: https://api.groq.com/openai/v1
  MCP_CLIENT_TIMEOUT: 5s
  MCP_DIAL_TIMEOUT: 3s
  MCP_ENABLE: "false"
  MCP_EXPECT_CONTINUE_TIMEOUT: 1s
  MCP_EXPOSE: "false"
  MCP_REQUEST_TIMEOUT: 5s
  MCP_RESPONSE_HEADER_TIMEOUT: 3s
  MCP_SERVERS: ""
  MCP_TLS_HANDSHAKE_TIMEOUT: 3s
  OIDC_ISSUER_URL: http://keycloak:8080/realms/inference-gateway-realm
  OLLAMA_API_URL: http://ollama.ollama:8080/v1
  OPENAI_API_URL: https://api.openai.com/v1
  SERVER_HOST: 0.0.0.0
  SERVER_IDLE_TIMEOUT: 120s
  SERVER_PORT: "8080"
  SERVER_READ_TIMEOUT: 30s
  SERVER_TLS_CERT_PATH: ""
  SERVER_TLS_KEY_PATH: ""
  SERVER_WRITE_TIMEOUT: 30s
kind: ConfigMap
metadata:
  annotations:
    meta.helm.sh/release-name: inference-gateway
    meta.helm.sh/release-namespace: inference-gateway
  creationTimestamp: "2025-07-04T00:05:36Z"
  labels:
    app.kubernetes.io/instance: inference-gateway
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: inference-gateway
    app.kubernetes.io/version: 0.7.1
    helm.sh/chart: inference-gateway-0.7.1
  name: inference-gateway-defaults
  namespace: inference-gateway
  resourceVersion: "1181"
  uid: b62ec336-909c-4b24-b5fe-ca01d4d4d727
```





