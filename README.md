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

Imagem