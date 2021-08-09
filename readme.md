# Descrição das Pastas

Podemos ver os seguintes arquivos nas pastas:

|Pasta                |Conteúdo                 |
|----------------|-------------------------------|
|terraform|`Arquivos utilizados para criar o EKS via Terraform`            
|monitoramento|`Arquivos utilizados para criação do Monitoramento`
|webapp|`Arquivos YML para deployment do Projeto Hello`            |




# Iniciando Docker com Amazon CLI

- Se for utilizar o kubernetes em ambiente local ir para o item `Executando o webapp`

 **Iniciando Amazon CLI**
```sh
  docker run -it --rm -v ${PWD}:/work -w /work --entrypoint /bin/sh amazon/aws-cli:2.0.43
```
   **Ferramentas uteis** 
```sh
 yum install -y jq gzip nano tar git unzip wget
```


# Login da conta Amazon
***Configuração realizada dentro do docker***

```sh
aws configure
```
AWS Access Key ID [None]: ***seu key***

AWS Secret Access Key [None]:***sua senha***

Default region name: us-east-1

Default output format: json



# Instalando Terraform no Docker

**Download Terraform** 
```sh
curl -o /tmp/terraform.zip -LO https://releases.hashicorp.com/terraform/0.13.1/terraform_0.13.1_linux_amd64.zip
```
```sh
unzip /tmp/terraform.zip
```
```sh
chmod +x terraform && mv terraform /usr/local/bin/
```

***Testar Terraform***
```sh
terraform
```



# Criando o Amazon Kubernetes com Terraform

***Acessar a pasta com os arquivos .tf***
```sh
cd terraform/
```
***Iniciar, planejar e aplicar o Terraform***
```sh
terraform init
```
```sh
terraform plan
```
```sh
terraform apply
```



# Deployed aplicação Kubernetes


**Pegar Configurações EKS** 
```sh
aws eks update-kubeconfig --name eks --region us-east-1
```

**Instalar kubectl no Docker** 
```sh
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
```
```sh
chmod +x ./kubectl
```
```sh
mv ./kubectl /usr/local/bin/kubectl
```


***Voltando para a Raiz para executar os arquivos do WebApp e Monitoramento***
```sh
cd ..
```

***Executando o webapp***
```sh
kubectl apply -f webapp/
```


***Executando o Monitoramento***
```sh
kubectl apply -f monitoramento/kube-state-metrics/
```
```sh
kubectl create namespace monitoring
```
```sh
kubectl create -f monitoramento/k8s-prometheus/ --namespace=monitoring
```
```sh
kubectl create -f monitoramento/k8s-grafana/ --namespace=monitoring
```


***Comandos uteis para verificar os serviços***

```sh
kubectl get pods
```
```sh
kubectl get svc
```
```sh
kubectl get pods --namespace=monitoring
```
```sh
kubectl get svc --namespace=monitoring
```


***Acessando os serviços publicados***

Para acessar a aplicação webapp Hello acesse o endereço:

http://EXTERNAL-IP/ (App Hello)

Para acessar o Prometheus acesse o endereço:

http://EXTERNAL-IP:8080/ (Prometheus)

Para acessar o Grafana acesse o endereço:

http://EXTERNAL-IP:3000/ (Grafana)

Será pedido login para acessar o Grafana digite:

Usuario: admin

Senha: admin

Devemos inserir uma nova senha no primeiro acesso.


Iremos utilizar inicialmente um dashboard já pronto para monitorar as metricas.

Importe o dashboard do GrafanaLabs (https://grafana.com/grafana/dashboards/14205)

ID: 14205




***Apagar projeto webapp***
```sh
kubectl delete -f webapp/
```


***Apagar projeto webapp***
```sh
kubectl delete -f monitoramento/kube-state-metrics/
```
```sh
kubectl delete -f monitoramento/k8s-prometheus/ --namespace=monitoring
```
```sh
kubectl delete -f monitoramento/k8s-grafana/ --namespace=monitoring
```


# Apagar infraestrutura criada pelo Terraform
- Somente se foi utilizado o kubernetes em ambiente AWS criado no inicio

```sh
cd terraform/
```
```sh
terraform destroy
 ```

 # Sair do Docker
 
```sh
exit
```
