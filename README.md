# angular-deploy-k8s

# Objectivo
Criar uma aplicação angular e efetuar seu deploy no cluster local k8s gerenciado pelo Rancher Desktop. 

Será criado:

### Docker
- Dockerfile
- docker-compose

### K8s
- Deployment
- Service
- Namespace
- Ingress

# 1 - Gerar imagem

-> Obs: Abrir pasta raiz.

```docker build -t portal:v1 ./portal```

* Testar container 

```docker run --name portal1  -p 80:80 -d portal:v1```

* Rodar docker-compose

```docker-compose -f .\docker\docker-compose.yaml up --build -d```

## Containers
![image](https://user-images.githubusercontent.com/8622005/188758135-f32b5685-a58d-47b4-8ea8-bb885ce30940.png)

## Portal
![image](https://user-images.githubusercontent.com/8622005/188758160-e3df3c7f-f02b-4bef-b9b5-e56924e6d46d.png)


# 2 - Publicar imagem no repositório

Obs: Pelo script automatizador.

Execute ``` export DOCKER_USERNAME="NOME_USUARIO_DOCKER"``` para declarar a variável no shell.
Cheque o valor executando ```echo ${DOCKER_USERNAME}```

imagem

Em seguida execute o script passando o nome da imagem e a tag:

```.\script\publicar.sh portal:v1```

![image](https://user-images.githubusercontent.com/8622005/188762210-72696caf-261a-4a2d-bf02-d8b1dae8546f.png)

# 3 - Adicionar a imagem no Rancher Desktop
Exemplo: registry.hub.docker.com/MEU_USUARIO_DOCKERHUB/portal:v1

![image](https://user-images.githubusercontent.com/8622005/188764741-bcde0031-0dac-4159-af72-ceee1815f33e.png)

Ao finalizar aparecerá a seguinte imagem:

![image](https://user-images.githubusercontent.com/8622005/188764838-51f0d8b3-ede9-466d-a924-49814dbc6cf9.png)


# 4 - Criar os objetos

## Namespace

```
apiVersion: v1
kind: Namespace
metadata:
  name: portals
```

## Deployment

```
apiVersion: v1
kind: Deployment
metadata:
  labels:
    app: portal
  name: portal-v1
  namespace: portals
spec:
  replicas: 1
  selector:
    matchLabels:
      app: portal
  template:
    metadata:
      labels:
        app: portal
    spec:
      containers:
        - name: portal
          image: registry.hub.docker.com/phillrog/portal:v1
          ports:
            - containerPort: 80
              name: portal
          
```

## Service

```
apiVersion: v1
kind: Service
metadata:
  labels:
    app: portal
  name: portal-v1
  namespace: portals
spec:
  type: NodePort
  ports:
    - port: 5000
      name: portal-service
      targetPort: 80
      nodePort: 30000
  selector:
    app: portal

```

# 5 - Executação dos yaml

Entrar no diretório k8s e executar:

``` kubectl create -f namespace.yaml ```

``` kubectl create -f deployment.yaml ```

``` kubectl create -f service.yaml ```

Objetos criados do namespace "front"

![image](https://user-images.githubusercontent.com/8622005/188767541-36e005d1-b740-4743-bf93-4fd8f498561d.png)

Lista

![image](https://user-images.githubusercontent.com/8622005/188767909-d711b839-5ead-44ac-b0eb-0764092b2dba.png)

Portal

![image](https://user-images.githubusercontent.com/8622005/188768017-9b1fc0b4-7faf-43a8-9f98-66bb8201b5e5.png)


# 6 - Ingress

Com o ingress é possível definir um host apontando para o serviço criado na porta 80 e não mais na 30000.

## Ingress

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: portal-ingress
  namespace: front
spec:
  rules:
  - host: meu-portal.localhost.com
    http:
      paths:
        - pathType: Prefix
          path: "/"
          backend:
            service:
              name: portal-v1
              port:
                number: 5000
                
```

Execute o seguinte comando para criar o ingress:

``` kubectl create -f ingress.yaml   ```

Vá no hosts e defina um DNS personalizado.

![image](https://user-images.githubusercontent.com/8622005/188770956-57fad5df-b854-4802-82d4-2ff6f52561ab.png)

# Resultado

Portal
![image](https://user-images.githubusercontent.com/8622005/188771027-dd254b4a-e6e9-4ae3-871e-58f5faff2c34.png)

Ingress no Rancher Desktop
![image](https://user-images.githubusercontent.com/8622005/188771185-0f34531f-60be-4b04-93d2-6f93ebf493a9.png)



