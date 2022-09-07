# angular-deploy-k8s

# Objectivo
Criar uma aplicação angular e efetuar seu deploy no cluster local k8s gerenciado pelo Rancher Desktop.

# 1 - Gerar imagem

-> Obs: Abrir pasta raiz.

```docker build -t portal:v1 ./portal```

* Testar container 

```docker run --name portal1  -p 80:80 -d portal:v1```

* Rodar docker-compose

```docker-compose -f .\docker\docker-compose.yaml up --build -d```

# 2 - Publicar imagem no repositório

Obs: Pelo script automatizador.

Execute ``` export DOCKER_USERNAME="NOME_USUARIO_DOCKER"``` para declarar a variável no shell.
Cheque o valor executando ```echo ${DOCKER_USERNAME}```

imagem

Em seguida execute o script passando o nome da imagem e a tag:

```.\script\publicar.sh portal:v1```


## Containers
![image](https://user-images.githubusercontent.com/8622005/188758135-f32b5685-a58d-47b4-8ea8-bb885ce30940.png)

## Portal
![image](https://user-images.githubusercontent.com/8622005/188758160-e3df3c7f-f02b-4bef-b9b5-e56924e6d46d.png)
