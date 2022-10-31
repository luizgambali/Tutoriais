## Configurações básicas
	
	git config --system init.defaultBranch main

define a branch padrão toda vez que for criada uma nova branch (neste caso, main) git bash deve ser iniciado como administrador

system -> fica no etc de onde o git foi instalado
global -> fica na pasta do usuario (c:\users\...)
local  -> fica no repositorio local do projeto

O git sempre vai ver da menor permissao para a maior permissao

Username e e-mail
	
	git config --global user.name "<seu nome>"
	git config --global user.email "<seu e-mail>"

Editor
	
	git config --global core.editor emacs

Verificando as configurações:
	
	git config --list
	git config user.name
	git config user.email

## Comandos basicos 

Criar um novo repositório:

	git init
	
Clonar um repositorio remoto existente:
	
	git clone <url-do-repositorio> <nome-da-pasta-destino>

Verificar o status dos arquivos da branch:

	git status

Começar a rastrear (staging) os arquivos:

	git add .
	git add <nome-do-arquivo>

Confirmar as alterações rastreadas (staging) na branch:

	git commit -m '<comentarios do commit>'

Subir as alterações:

	git push

Baixar as alterações realizadas na branch:

	git pull

Atualizar as informações do repositorio remoto, que ainda não estão no repositorio local:

	git fetch

## Trabalhando com branchs

Criar uma nova branch:
	
	git checkout -b <nome-da-branch>
	git checkout -b origin/<nome da branch remota>

Alternar entre várias branchs:

	git checkout <nome-da-branch>

Atualizar a branch local com os dados da branch remota:
	
	git pull

Enviar as alterações para a branch remota

	git push -u origin <nome-da-branch>

Fazendo merge de outra branch na branch atual:
	
	git merge <nome-da-branch>
	git merge origin/<nome-da-branch>
	
Fazendo merge da branch atual para outra branch:
	
	mesma coisa que o anterior! Faça o checkout na branch destino com git checkout <nome-da-branch> e repita o processo
	
Fazendo rebase:
	
	git rebase <nome-da-branch>
	
Diferença entre merge e rebase:
	
- O merge basicamente cria um novo commit na branch onde o merge é realizado
- O rebase literalmente unifica os branches envolvidos, puxando os commits para frente do branch de destino. 

  É como se ele estivesse “refazendo” a base do branch onde o comando é executado.
	
O que usar: merge ou rebase?
	
Se não sabe o que fazer (ou não entendeu a diferença), use o merge! O merge é mais simples de reverter!
	
Reverter um merge:
	
	git reset --hard <id-do-commit-anterior-ao-do-merge>

## Resolvendo problemas 

Estou trabalhando na branch errada, e preciso jogar as alterações para a branch correta:

	git stash
	git checkout <branch correta>
	git stash apply

Merge quebrou alguma coisa, e precisa voltar:
	
	git reset --hard <id-do-commit-anterior-ao-do-merge>

Desfazer todas as alterações que fiz na branch local:
	
	git revert --reset HEAD

Desfazer alterações em um arquivo especifico:
	
	git checkout -- <nome-do-arquivo>
