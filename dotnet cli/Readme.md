## DOTNET CLI

Para instalar o dotnet CLI:
	
	dotnet tool install

Para verificar os SDKs instalados:
	
	dotnet --list-sdks
	dotnet --list-runtimes
	
Para selecionar a versão do SDK a ser usada no novo projeto:
	
Criar um arquivo global.json na pasta onde será criada a sln:
		
(global.json)
		
	{
	  "sdk": {
	    "version": "3.0.0"
	  }
	}

Para criar uma nova solution via CLI:

	dotnet new sln -n <nome da solution>

Exemplo:
	
	dotnet new sln -n Curso

Para criar um novo projeto via CLI:

	dotnet new tipo-de-projeto -n nome-do-projeto -o pasta -f versao do dotnetcore a ser usada

tipo-de-projeto: console, classlib, web, mvc, webapi, mstest... 

Exemplo:
	
		dotnet new console -n CursoEFCore -o Curso -f netcoreapp3.1

Para adicionar um projeto a solution via CLI:

	dotnet sln <nome-da-solution.sln> add <projeto> -s <nome-da-pasta>

Exemplo:
	
		dotnet sln Curso.sln add ./Curso/CursoEFCore.csproj
		
Para remover um projeto de uma solution:
	
	dotnet sln <nome-da-solution.sln> remove <path-do-projeto>
	
Exemplo:
	
	dotnet sln Curso.sln remove ./Curso/CursoEFCore.csproj
	
Para listar os projetos de uma solution:
	
	dotnet sln <nome-da-solution> list
	
Para adicionar um package ao projeto via CLI:

	dotnet add <caminho-completo-do-projeto> package <nome-do-pacote> --version <versao-do-pacote>

Exemplo:
	
	dotnet add ./Curso/CursoEFCore.csproj package Microsoft.EntityFrameworkCore.SqlServer --version 3.1.5
		
Para compilar um projeto ou solução:
	
	dotnet build
	
Para compilar e rodar um projeto:
	
	dotnet run --verbosity normal --project <nome-do-projeto>
	
Para compilar e rodar um projeto em modo debug/release:
	
	dotnet run -c Debug --project <nome-do-projeto>
	dotnet run -c Release --project <nome-do-projeto>