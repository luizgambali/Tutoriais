# Container

### docker ps [parametros]
Exibe os containeres em execução.

Parâmetros:

	nenhum 	Mostra todos os containeres que estão sendo executados no momento
	-a 		Mostra todos os containeres criados no meu computador
	-q 		Mostra somente o container-id
	-l 		Mostra somente o ultimo container criado
	-s 		Mostra o tamanho do container

Exemplos:

	docker ps
	docker ps -a
	docker ps -a -s
	docker ps -l

### docker rm [container-id] 

Exclui um container do nosso computador. Para ver os containeres já executados, utilizar docker ps -a. Pode ser o container-id ou container-name, se foi utilizado o parâmetro --name na sua criação. Se for utilizar o container-id, não precisa digitar todo o id, bastam os 3 primeiros digitos: 0d4 ao invés de 0d4c10716d1f. Contudo, se possível, sempre dê um nome para o container, isso ajuda e torma mais fácil de identifica-lo

	-f Força a remoção do container
Para remover todos os containeres, usar:

	docker rm -f $(docker ps -a -q)

### docker run [parametros] [opções] imagem

Roda uma imagem em um novo container.

Parâmetros:

	-it Roda o container em modo interativo. Você consegue acessar o terminal do container
	-d 	Roda o comando em modo deattached, ou seja, o terminal não fica preso no comando docker run
	-p 	pl:pc expõe a porta do container (pc) na porta local (pl)

Opções:

	--name	Define o nome do container a ser executado. Isso é util porque podemos identificar mais fácil o container na lista do "docker ps -a" pelo nome, podendo iniciar e parar por esse nome e não pelo id.
	--rm 	Remove automaticamente o container ao final da execução, para não ficar ocupando espaço no computador.

Exemplos:

	docker run -it ubuntu
	docker run -d -p 80:80 nginx
	docker run -d -p 80:80 --name meu-container nginx
	docker run -d -p 80:80 --name meu-container --rm nginx

	Exemplo NGINX:
 
		docker run -it --rm -d -p 8080:80 --name web nginx 

		para acessar o container: http://localhost:8080

### docker stop [container-id]
Encerra a execução de um container que está rodando no momento. [container-id] pode ser o id do container ou o nome dele.

### docker start [container-id]
Reinicia um container que já foi executado anteriormente. Para ver o [container-id] utilize o comando docker ps -a. Pode ser o [container-d] ou container-name, se foi utilizado o parâmetro --name na sua criação.

### docker logs [container-id]
Mostra os logs do container. Container-id pode ser o id do container ou o nome dele.

### docker stats [container-id]
Mostra o status de um ou mais containeres (memoria, cpu, net)

### docker inspect [container-id]
Mostra informações sobre a configuração do container

### docker top [container-id]
Mostra dados como: quando ele foi criado, id do processo, etc.


# Volumes

### docker volume [parametros]

Parametros:

	ls 			Lista todos os volumes criados
	create 		Cria um volume
	rm 			Remove um volume
	inspect 	Mostra informações do volume
	prune 		Remove todos os volumes não utilizados

Exemplos:

	docker volume create meudisco
	docker volume ls
	docker volume inspect meudisco
	docker run -it --name teste --mount source=meudisco,target=/app ubuntu

No exemplo acima, foi criado um volume chamado meudisco. Depois, ao rodar o container, foi associado o volume meudisco a pasta app do container. Mesmo que o container seja finalizado e removido, o conteudo
do volume vai permanecer. O volume só será perdido caso ele seja removido.

# Imagens

### docker images [parametros]
Lista as imagens baixadas no meu computador

Parâmetros:
	
	nenhum	Lista as imagens
	-a 		Mostra todos as imagens no meu computador
	-q 		Mostra somente o image-id das imagens
	-f 		Força a remoção do container

### docker image ls
Lista as imagens disponiveis localmente. Igual a docker images


### docker pull [nome-da-imagem]

Baixa uma imagem para o meu computador. Procurar a imagem em https://hub.docker.com/
Dê sempre preferencia para imagens oficiais 

### docker rmi [image-id]
Exclui uma imagem do meu computador. Image-id pode ser tanto o id como o nome da imagem. Caso exista algum container utilizando aquela imagem, apagar antes o container, ou usar -f. Para remover todas as imagens do computador, usar:

	docker rmi -f $(docker images -a -q)

### docker system prune
Remove tudo que não está mais sendo utilizado.

# Criar uma imagem no Docker
Exemplo para uma aplicação Nodejs:

	- Criar uma pasta para o projeto
	- Executar node init -y
	- Executar npm install express
	- Apagar node_modules e package-lock.json
	- Criar um arquivo index.js:

Arquivo index.js:

	const express = require('express')
	const app = express()
	const port = 3000
	
	app.get('/', (req, res) => {
		res.send('Hello World!')
	})
	
	app.listen(port, () => {
		console.log(`Example app listening on port ${port}`)
	})


Arquivo Dockerfile:
  
	FROM node
	WORKDIR /urs/src/app
	COPY package.json .
	RUN npm install
	COPY . .
	EXPOSE 3000
	CMD ["node","index.js"]

Para criar a imagem:

	 docker build -t node-imagem-exemplo .

Para executar a imagem:

	 docker run -d -p 3000:3000 --name node-docker node-imagem-exemplo

## Entendendo o básico do arquivo Dockerfile:

	FROM 			Determina a imagem que vamos utilizar
	WORKDIR 		Define a pasta de trabalho, e entra na pasta
	COPY 			Copia o arquivo origem para o destino
	RUN 			Executa um comando
	EXPOSE 			Expõe uma porta do container (para usar com o -p do docker run)
	CMD 			Roda a nossa aplicação, com os parametros

Observação: se definido o workdir, basta usar "." no comando COPY

## Atualizando a Imagem criada

Se os arquivos de código foram alterados, basta rodar  docker build . novamente
