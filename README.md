# aula-0604

Aula ao vivo 06/04

https://kind.sigs.k8s.io/docs/user/quick-start/

Copy
cat <<EOF | kind create cluster --config=-
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: worker
- role: worker
- role: control-plane
  kubeadmConfigPatches:
  - |
    kind: InitConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        node-labels: "ingress-ready=true"
  extraPortMappings:
  - containerPort: 80
    hostPort: 80
    protocol: TCP
  - containerPort: 443
    hostPort: 443
    protocol: TCP
EOF

kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml

kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.6.3/components.yaml


spec:
  template:
    spec
      containers:
      - args:
        - --cert-dir=/tmp
        - --secure-port=4443
        - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
        - --kubelet-use-node-status-port
        - --metric-resolution=15s
        - --kubelet-insecure-tls
        name: metrics-server



CKA ---- 

1 - Adicione a taint NoSchedule em um dos nós e remova todos os pods em execução nesse nó 

2 - Execute os seguintes comandos:
    - kubectl create ns dr-teste
    - kubectl run webserver -n dr-teste

Agora crie um backup do etcd, o backup deve ser feito no nó do control-plane no diretorio /tmp/pre-dr.db 


Execute os seguintes comandos:
    - kubectl delete ns dr-teste
    - kubectl delete po webserver -n dr-teste

Agora faça o restore no etcd e verifique se o namespace e o pod estão "Running"

3 - Verifique o log do Kubelet e salve-o em /tmp/kubelet.log

4 - Crie um Pod chamado httpd com a imagem httpd:latest e porta 80

5 - Crie um deployment com os seguintes items:
  * initContainers
    - image: busybox
    - command: /bin/sh -c echo "VAIIIII > /volume/initfile.txt"
    - volumeMounts:
      * name: tempfile
      * /volume
  * container:
    - image: nginx
    - volumeMounts:
      * name: tempfile
      * /volume
  * Utilize um volume do tipo emptyDir

6 - Crie um cronjob com o schedule "*/1 * * * *" e executando o comando  'echo VAII' completions 5 e parallelism 5 e  um timeout de 15 segundos.


7 - Crie uma deployment chamado "rollback" , com imagem nginx:1.23 e 1 réplica. Grave a versão. Em seguida, atualize o deployment para imagem nginx:1.25 usando rolling update. Certifique-se de que a atualização da versão esteja registrada na anotação do recurso


--- CKAD

1 - Crie 2 pods:
    - Pod 1:
      name: nginx
      port: 80
      imagem: nginx
    - Pod 2:
      name: httpd
      imagem: httpd

    Crie o serviço para os 2 pods como:
    - tipo: Cluster IP 
    - port: 8080
    - target-port: 80
    - O nome pode ser a sua escolha 

    Agora crie um ingress com 2 rotas /v1 apontando para o serviço do nginx e /v2 para o serviço do httpd

2 - Crie um deployment:
    - Nome: autoscalling
    - image: nginx
    - port 80
    - Replicas 2

Agora crie "HPA" para o deployment com no minimo 2 réplicas e maximo 5 e o cpu-percernt deve ser 50.

3 - Crie 2 deployments:
    - v1:
      - name: v1
      - image: nginx
    - v2:
      - name: v2
      - image: httpd
    
    Agora crie um Canary deployment "simples" onde 90% do tráfego irá para v1 e 10% para v2

    Agora coloque 50/50

    Agora 100 para v2

4 - Verifique as metricas dos pods da v2 

5 - Colete o logs dos containers do CodeDNS e salve em um arquivo em /tmp/log.txt

6 - Liste todos os CRDS disponíveis no Cluster


--- CKS

1 - Crie um namespace chamado "blackhole"
  - Agora crie uma network-policy do tipo deny bloqueado todo o trafego de saida

2 -  Arrume o seguinte deploy para seguir as regras de segurança:

Não pode executar com root 
Deve utilizar o usuario 1001
Não pode fazer a escalação de privilégios 
E Dropar todas as capabilities
 
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: secure-app
  name: secure-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: secure-app
  strategy: {}
  template:
    metadata:
      labels:
        app: secure-app
    spec:
      containers:
      - image: registry.kubesim.tech/development/production-app:latest
        name: ubuntu
        args: ["sleep", "10000"]

3 - Verifique as seguintes images e escreva no arquivos /tmp/vuln.txt a imagem que tiver mais vulnerabildiades
  - nginx:1.25.4-alpine3.18
  - nginx:1.23.4
  - nginx:1.23.4-alpine3.17

Agora lista apenas as imagens CRITICAS

4 - Crie um service account chamada list-pods
    - Crie uma role que permita listar os pods no namespace default
    - Crie um pod para utilizar essa service account e liste os pods

5 - Utilize o Kube-bench no cluster

