## Slides
You can fine the slides [here](https://docs.google.com/presentation/d/1qQUNuIBocdBape2Duc-VSDrZFqRNXS-ybD_5HmaeYYU/edit?usp=sharing)

## Commands

### Google cloud GKE cluster creation 
```
gcloud container clusters create gpu-cluster \                     
  --zone=us-central1-c \
  --machine-type=e2-standard-4 \
  --num-nodes=3                      
```
### Adding GPU node with time slicing
```
gcloud container node-pools create gpu-node-pool \
  --cluster=gpu-cluster \
  --zone=us-central1-c \        
  --machine-type=n1-standard-4 \
  --accelerator=type=nvidia-tesla-t4,count=1,gpu-sharing-strategy=time-sharing,max-shared-clients-per-gpu=5 \
  --num-nodes=1
```
### vcluster creation 

```
vcluster create demo1 -n demo1 -f vcluster.yaml
vcluster create demo2 -n demo2 -f vcluster.yaml

```

### vCluster connect 

```
vcluster connect demo1 -n demo1
vcluster connect demo2 -n demo2
```

### Ollama Deployment 
```
kubectl apply -f deploy.yaml
```
### pulling the model
```
kubectl exec -it <pod-name> -- ollama pull mistral
```

### Testing 
```
 kubectl port-forward svc/ollama 8080:11434
```
```

curl -s http://localhost:8080/api/chat \
  -H "Content-Type: application/json" \
  -d '{
    "model": "mistral",
    "stream": false,
    "messages": [
      {"role": "user", "content": "Summarize vCluster in one line"}
    ]
  }' | jq -r '.message.content'
```

For RAG 
```
kubectl exec -it deploy/ollama -- ollama run llama3 "Hello"
curl -s -X POST http://localhost:5000/ask \ 
  -H "Content-Type: application/json" \
  -d '{"question":"What is a Kubernetes pod?"}' | jq

kubectl port-forward svc/simple-rag-service 5000:5000

```
