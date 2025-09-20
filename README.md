# myapp-zero

Helm chart simples para Kubernetes, com templates de Deployment e Service que criei para estudo.

## Sobre

Este projeto demonstra:

- Um **Deployment** com:
  - Contêiner principal: Nginx
  - Sidecar: Busybox (`sleep 3600`)
  - Configuração de volumes com `emptyDir`
- Um **Service** do tipo ClusterIP para expor o Deployment.
- Uso de **Helm templates** para parametrização de contêineres, portas, variáveis de ambiente e volumes.
- Um ambiente de fácil reprodução para aprendizado de Helm e Kubernetes.

## Pré-requisitos

Para executar este projeto localmente, você precisará de:

- [Docker](https://www.docker.com/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [Kind](https://kind.sigs.k8s.io/)
- [Helm](https://helm.sh/)

## Como usar

Siga os passos abaixo para implantar a aplicação em um cluster local.

### 1. Criar um cluster local com Kind

```bash
# Cria o cluster com o nome helm-demo
kind create cluster --name helm-demo

# Define o contexto do kubectl para o novo cluster
kubectl cluster-info --context kind-helm-demo
```

### 2. Clonar o repositório

```bash
# Clone este repositório (substitua pelo seu usuário)
git clone [https://github.com/seu-usuario/myapp-zero.git](https://github.com/seu-usuario/myapp-zero.git)

# Acesse o diretório do projeto
cd myapp-zero
```

### 3. Instalar o Helm Chart

```bash
# Instala o chart com o nome "myapp-zero" no cluster
helm install myapp-zero .
```

### 4. Verificar os pods

```bash
# Liste os pods criados com o label app=myapp-zero
kubectl get pods -l app=myapp-zero
```

### 5. Acessar a aplicação localmente

Para acessar a página do Nginx, use o `port-forward` para expor o Service localmente.

```bash
# Encaminha a porta 8080 local para a porta 80 do serviço
kubectl port-forward svc/myapp-zero-service 8080:80
```

Agora, você pode acessar a aplicação de duas formas:
- **Via cURL:** `curl http://localhost:8080`
- **Via Navegador:** Abra [http://localhost:8080](http://localhost:8080) para ver a página padrão do Nginx.

### 6. Atualizar ou desinstalar o chart

```bash
# Para aplicar mudanças feitas no chart (ex: no values.yaml)
helm upgrade myapp-zero .

# Para remover a aplicação do cluster
helm uninstall myapp-zero
```

### 7. Exemplo de valores customizados (`values.yaml`)

O arquivo `values.yaml` permite customizar a implantação sem alterar os templates.

```yaml
replicaCount: 3

containers:
  - name: nginx
    image:
      repository: nginx
      tag: latest
    ports:
      - containerPort: 80
    env:
      - name: ENVIRONMENT
        value: production
      - name: LOG_LEVEL
        value: info
    volumeMounts:
      - name: html-volume
        mountPath: /usr/share/nginx/html

  - name: sidecar
    image:
      repository: busybox
      tag: latest
    command: ["sleep", "3600"]

volumes:
  - name: html-volume
    enabled: true
    # O tipo de volume é definido internamente no template como emptyDir
```

## Estrutura do projeto

```
myapp-zero/
├── charts/            # (Diretório para dependências de outros charts)
├── templates/         # Templates do Deployment e Service
│   ├── deployment.yaml
│   └── service.yaml
├── .helmignore        # Arquivos a serem ignorados pelo Helm
├── Chart.yaml         # Metadados do chart
├── values.yaml        # Valores padrão para customização
└── README.md          # Este arquivo
```

## Observações

- O volume `emptyDir` é temporário e seu conteúdo é perdido quando o Pod é destruído.
- Todos os contêineres, volumes e outras configurações podem ser parametrizados via `values.yaml`.
- Este chart foi criado com foco em aprendizado e pode ser facilmente adaptado e expandido para projetos mais complexos.
- Qualquer pessoa com as ferramentas listadas nos pré-requisitos pode reproduzir este ambiente localmente.
