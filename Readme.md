# 🧪 Laboratório: Canary Deployment com Istio no Minikube + Kiali

Este laboratório demonstra como configurar um ambiente local com **Minikube**, **Istio** e **Kiali**, aplicando uma estratégia de **Canary Release** com duas versões de uma aplicação.  

---

## 📋 Pré-requisitos

- [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [Istioctl](https://istio.io/latest/docs/setup/getting-started/#download)

---

## 🚀 Etapas do Laboratório

### 1. Inicie o Minikube

```bash
minikube start --driver=kvm2 --cni=flannel --cpus=4 --memory=8000 -p=lab1
```

### 2. Instale o Istio

```bash
istioctl install --set profile=demo -y
```

### 3. Habilite a injeção automática de sidecars

```bash
kubectl label namespace default istio-injection=enabled
```

---

## 4. Deploy da Aplicação

Execute:

```bash
kubectl apply -f app.yaml
```

---

## 5. Configuração Istio Canary

Execute:

```bash
kubectl apply -f canary.yaml
```

---

## 6. Gateway para acesso externo

Execute:

```bash
kubectl apply -f gateway.yaml
```

---

## 7. Instale o Kiali (com Grafana, Jaeger e Prometheus)

```bash
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.22/samples/addons/kiali.yaml
```

Exponha o Kiali localmente:

```bash
kubectl port-forward -n istio-system svc/kiali 20001:20001
```

Acesse: [http://localhost:20001](http://localhost:20001)  
Usuário/senha padrão: `admin / admin`

---

## 8. Teste o Canary

### 8.1 Inicie o tunnel para expor o serviço

```bash
minikube tunnel
```

### 8.2 Descubra o IP externo do Ingress Gateway

```bash
kubectl get svc istio-ingressgateway -n istio-system
```

### 8.3 Faça requisições para ver a distribuição do tráfego

Obtenha o IP do ingress:

```bash
export GATEWAY_IP=$(kubectl get svc -n istio-system istio-ingressgateway -ojsonpath='{.status.loadBalancer.ingress[0].ip}')
```

```bash
curl http://$GATEWAY_IP
```

Rode várias vezes e observe a alternância entre respostas das versões `v1` (90%) e `v2` (10%).

---

## 🔍 Observabilidade com Kiali

1. Acesse [http://localhost:20001](http://localhost:20001)
2. Navegue até a aplicação `httpbin`
3. Visualize o tráfego entre as versões e os percentuais de roteamento
4. Use filtros e animações para entender o comportamento

---

## 📚 Recursos adicionais

- [Documentação do Istio](https://istio.io/latest/docs/)
- [Kiali](https://kiali.io/)
- [HTTPBin (test server)](https://httpbin.org/)

---

## 👨‍🏫 Créditos

Este laboratório foi desenvolvido para fins educacionais como parte da palestra sobre **Fundamentos de Istio**.

Fique à vontade para compartilhar e adaptar!
