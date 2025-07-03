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
```
curl -s POST http://api.inference-gateway.local/v1/chat/completions  -H "Content-Type: application/json"   -d '{"model":"ollama/deepseek-r1:1.5b","messages":[{"role":"user","content":"how is the weather in San francisco"}]}' | jq -r
```






