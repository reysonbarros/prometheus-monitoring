### Instalação do docker
```
apt-get update
apt-get install curl -y
curl -fsSL https://get.docker.com/ | bash
docker version
```

### Instalação do kind

```
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-$(uname)-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
kind version
```

### Instalação do kubectl
```
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
kubectl version --client
```

### Criar o cluster
```
kind create cluster --config cluster.yaml
```

### Listar os nodes
```
kubectl get nodes
```

### Criar o ConfigMap
```
kubectl apply -f nginx-configmap.yaml
```

### Listar o ConfigMap
```
kubectl get configmap
```

### Criar o Deployment
```
kubectl apply -f nginx-deployment.yaml
```

### Listar o Deployment
```
kubectl get deployment
```

### Listar os Pods
```
kubectl get pods
```

### Criar o Service
```
kubectl apply -f nginx-nodeport-svc.yaml
```

### Listar os services
```
kubectl get services
```

### Clone do kube-prometheus
```
git clone https://github.com/prometheus-operator/kube-prometheus

cd kube-prometheus
```


### Criar os CRDs(Custom Resources Definitions)
```
kubectl create -f manifests/setup
```

### Listar os CustomResourceDefinitions(CRDS)
```
kubectl get crds
```

### Criar a conta no Slack e chatbot no Telegram
### Slack:
1 - Acessar o link https://slack.com/create

2 - Após realizar as customizações necessárias e criar seu canal, deve acessar o 
menu: Workspace name -> Settings & administration -> Manage Apps

3 - Pesquisar por Incoming WebHooks e clicar em Add to Slack

4 - Especificar qual o canal que deve receber as notificações do AlertManager

5 - Copiar o Webhook URL gerado no Slack

### Telegram
1 - Abrir o telegram desktop no navegador

2 - Clicar em New Channel(expandir botao azul no final do menu lateral esquerdo para 
exibir essa opção)

3 - Informar o nome do canal

4 - Na próxima tela digitar uma mensagem com o seguinte conteúdo:

@botfather

5 - Clicar no link da mensagem que foi gerada no chat

6 - Clicar em /start e logo após em /newbot

7 - Escolher um nome de usuário para o bot

8 - Copiar o token gerado

### Como encontrar o Chat Id no Telegram

1 - Clicar no link do seu bot gerado próximo do token

2 - Digitar uma mensagem de teste: hello-world!

3 - Em uma nova aba no navegador acessar o seguinte link:

https://api.telegram.org/botYOUR_BOT_TOKEN/getUpdates

Nota: Substituir o termo YOUR_BOT_TOKEN pelo se token gerado no Telegram

4 - Na resposta copiar o valor do campo chat.id:
```
"chat": {
  "id": 9999999999,
  "first_name": "Test",
  "last_name": "Test",
  "type": "private"
}
```

### Alterar o arquivo /kube-prometheus/manifests/alertmanager-secret.yaml
### Adicionar os seguintes parâmetros de config:
```
"global":
  "slack_api_url": "Webhook URL gerado no Slack"
  "telegram_api_url": "https://api.telegram.org"


"receivers":
- "name": "Slack"
   "slack_configs":
   - "channel": "#SEU_CANAL_SLACK"
     "send_resolved": true  
- "name": "Telegram"
   "telegram_configs":
   - "bot_token": "SEU_TOKEN_BOT_TELEGRAM"
     "chat_id": CHAT_ID_TELEGRAM

"route":
  "group_by":
  - "namespace"
  "group_interval": "5m"
  "group_wait": "30s"
  "receiver": "Default"
  "repeat_interval": "12h"
  "routes":  
    - "matchers":
      - "alertname = SEU_ALERT_NAME"
      "receiver": "Slack"
      "continue": true
    - "matchers":
      - "alertname = SEU_ALERT_NAME"
      "receiver": "Telegram"

```
### Instalação do Prometheus
```
kubectl create -f manifests/
```

### Listar os pods que foram criados baseados nos CRDs instalados
```
kubectl get pods -n monitoring
```

### Criar o PodMonitor
```
kubectl apply -f nginx-pod-monitor.yaml
```

### Listar o PodMonitor
```
kubectl get podmonitors
```

### Criar a PrometheusRule
```
kubectl apply -f nginx-prometheus-rule.yaml
```

### Listar o PrometheusRule
```
kubectl get prometheusrules -n monitoring
```

### Port-forward para acessar o Prometheus e o AlertManager
```
kubectl port-forward -n monitoring svc/prometheus-k8s 39090:9090
kubectl port-forward -n monitoring svc/alertmanager-main 39093:9093
```

### Acessar o Prometheus e o AlertManager pelo browser:
```
Prometheus: http://localhost:39090
AlertManager: http://localhost:39093
```

### Verificar no Prometheus se o Target e a Rule foram criados

### Executar o comando na máquinal local. Use o comando k get nodes -o wide para obter o IP
```
while true; do curl http://172.18.0.5:32000; done;
```
### Acompanhar no Prometheus, AlertManager, Slack e Telegram se foi gerado algum alerta/notificação








