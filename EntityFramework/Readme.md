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
	
## Dotnet Core EF MIGRATIONS 
	
Para adicionar a ferramenta do Dotnet EF Core e realizar migrações:

	dotnet tool install --global dotnet-ef --version 3.1.5 (ou a versão q quiser)

Para confirmar que foi instalada, usar: dotnet ef
	
Para instalar as bibliotecas necessárias para o migrations (dentro da pasta da solution):
	
	dotnet add <caminho-completo-do-projeto> package Microsoft.EntityFrameworkCore.Design --version 3.1.5
	dotnet add <caminho-completo-do-projeto> package Microsoft.EntityFrameworkCore.Tools --version 3.1.5
	
	dotnet add <caminho-completo-do-projeto> package Microsoft.EntityFrameworkCore.SqlServer --version 3.1.5 (esse cara, para não dar o erro ModelBuilder does not contain a definition for ''UseIdentityColumns")
	
Exemplo:

	dotnet add ./Curso/CursoEFCore.csproj package Microsoft.EntityFrameworkCore.Design --version 3.1.5
	dotnet add ./Curso/CursoEFCore.csproj package Microsoft.EntityFrameworkCore.Tools --version 3.1.5
		
	
Add-Migration		-> cria uma migration
Remove-Migration	-> dá "rollback" em um migration
Update-Database		-> atualiza o modelo de acordo com as alterações criadas
Script-Migration 	-> gera o script para execução manual
	
Para fazer o primeiro migration, via CLI:
	
	dotnet ef migrations add PrimeiraMigracao -p <pasta do projeto de dados com as entidades> [-o <pasta para salvar o migrations] [-c nome do contexto que estará se fazendo a migração]
	
sendo que -o e -c são opcionais.
	-o é usado quando se quer salvar os arquivos de migração em uma pasta diferente da padrão do dotnet
	-c é usado para especificar o contexto o qual se está fazendo a migração (pode haver mais de um)
	
se o projeto de dados estiver numa pasta diferente:
	
	dotnet ef migrations add FirstMigration --startup-project <projeto startup> -p <projeto com os arquivos de dados> -o <nome da pasta de migrations> -c <nome da dbcontext>

Exemplo:
	
		dotnet ef migrations add PrimeiraMigracao -p ./Curso/CursoEFCore.csproj
	
Ao rodar o comando, será criada uma pasta Migrations no projeto, com três arquivos:
	
	<timestamp>_PrimeiraMigracao.cs -> métodos UP e DOWN. 
	
**Up** contém os detalhes do q vai subir para o banco (criação das tabelas, chaves, etc)
**Down** contém os detalhes para fazer o revert (drop table, drop constraint, etc)
		
		É o local também para criar comandos SQL especificos. Por exemplo, criar uma função no BD:
		
Up:
		migrationBuilder.Sql("CREATE FUNCTION ...");
	
Down:
		migrationBuilder.Sql("DROP FUNCTION ...");
	
	<timestamp>_PrimeiraMigracao.Designer.cs -> é basicamente a copia do nosso modelo de dados, no momento da criação.
	
É como se fosse o versionamento do nosso modelo de dados.

	<nome-da-context>ModelSnapshot.cs -> gerado uma única vez, e é atualizado a cada migração criada.
	
Para gerar o script SQL do migrations:
	
	dotnet ef migrations script -p <pasta-do-projeto> -o <nome-do-arquivo-script>
	
Exemplo:
	
	dotnet ef migrations script -p ./Curso/CursoEFCore.csproj -o ./Curso/PrimeiraMigracao.SQL
	
	
Para aplicar o migrations no banco de dados:
	
Forma 1: usar o arquivo com o script SQL no banco de dados
Forma 2: via prompt de comando
	
	dotnet ef database update -p <nome do projeto> -v
		
Exemplo:
		
	dotnet ef database update -p ./Curso/CursoEFCore.csproj -v
	
*** IMPORTANTE ***: se estiver rodando no linux, é necessário que a string de conexão tenha o Integrated Security=false. Senão será necessário instalar o kerberos, e é complicado!
	
Forma 3: através do contexto da aplicação (não indicada para utilização em produção)
	
No startup da aplicação:
	
	var db = new Data.ApplicationContext(); //é uma instancia do contexto da aplicação, dentro do projeto Data
	db.Database.Migrate(); //fara o mesmo que o database update, aplicando as migrações
		
	
Para gerando Scripts SQL Idempotentes (verificando se as tabelas ja existem na tabela de migracao):
	
	dotnet ef migrations script -p <nome-do-projeto> -o <nome do arquivo script> -i
		
Exemplo:
	
	dotnet ef migrations script -p ./Curso/CursoEFCore.csproj -o ./Curso/Idempotente.sql -i
	
Para fazer rollback de migrações:
	
Cenario: já há uma migration chamada PrimeiraMigracao
		
Crie uma nova migração com dotnet ef migrations add <nome-da-migrations> -p <nome-do-projeto>
Aplique a migração ao banco com dotnet ef database update -p <pasta-do-projeto>
		
Rollback: 

	dotnet ef database update <nome-da-migration> -p <nome-do-projeto> -v
		
Exemplo:

	dotnet ef database update PrimeiraMigracao -p ./Curso/CursoEFCore.csproj -v
		
Para excluir a ultima migracao criada:
		
	dotnet ef migrations remove -p <nome-do-projeto> 
		
Exemplo:
		
		dotnet ef migrations remove -p ./Curso/CursoEFCore.csproj
		
Validando migrações pendentes:
		
		static void Main(string[] args)
		{
		    using var db = new Data.ApplicationContext();

		    //db.Database.Migrate();

		    var existe = db.Database.GetPendingMigrations().Any();

		    if (existe)
		    {
		        //aplicar regra de negocio
		    }
		     
		}
		
Para instalar o log, e acompanhar no console o log da aplicação (dentro da pasta do projeto!):
	
	dotnet add package Microsoft.Extensions.Logging.Console --version 3.1.5
