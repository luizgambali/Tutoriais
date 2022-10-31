
# Por que usar mock?

Para substituir a dependência real que seria usada em tempo de produção, por uma versão somente em tempo de teste para 	permitir o isolamento mais fácil do código que queremos testar.
	
O isolamento do segmento de teste que se está realizando, não exclui a necessidade futura de testes de  integração, por exemplo, mas permite que uma determinada parte do sistema seja testada, isolando esse teste do ambiente real de produção.
	
## Beneficios:
	
- Aumentar a velocidade de execução dos testes
- lógica pesada nos algoritmos do código real	
- recursos externos: banco de dados, chamadas de serviços externos, etc
- Suporte a fluxos de desenvolvimento paralelo
- O objeto real ainda não foi implementado pelo time ou outro fornecedor externo 
- Melhorar a confiabilidade dos testes
- O objeto real ou recurso a ser testado não é estável, então criamos um objeto mock para fornecer o funcionamento esperado
- Reduzir custos de desenvolvimento/testes
- Pagamento por uso de recursos externos
- Esforço de desenvolvimento (complexidade)
- Dependencia não deterministica	
- Data e hora, fornecendo data e hora fixas para os testes

# Por que usar testes de unidade?

- Low Level: para testar partes especificas de um sistema. É dificil testar partes especificas do sistema quando estamos testando uma API, por exemplo
- Highly Focused: foco maior em uma determinada parte do sistema, por exemplo, se um método está retornando o valor esperado
- Quick to Execute: Podemos executar varios testes rapidamente, e verificar se houve uma quebra no codigo.
- Easier to test all logical paths: é muito mais simples testar todo o fluxo lógico do código que estamos testando
- Testes de unidade não incluem necessariamente uma única classe. A idéia do teste de unidade é testar uma pequena parte do sistema.
- Unidades de comportamento sobre unidades de implementação

'...é uma coisa situacional - o tempo decide o que faz sentido ser uma unidade para fins de compreensão do sistema e seus testes' - Martin Fowler	

## Termos utilizados

Test Dobule
	
'é um termo genérico para qualquer caso em que você substitua um objeto de produção para fins de teste' - Martin Fowler

Por exemplo, substituir a chamada de uma API real por uma versão de testes.
		
- Dummies, Fakes, Stubs, Mocks
- Fakes: fornecem uma implementação funcional da dependência. Não é adequado para produção, é apenas para fins de teste. Ex.: EF Core in-memory provider
- Dummies: são passados como implementações reais, mas nunca são acessados ou usados. Ex.: parametros de métodos que não podem ser nulos
- Stubs: fornecem respostas para chamadas. Ex.: propriedades e métodos que retornam valor
- Mocks: esperam e verificam chamadas, feitas pelo código em produção. Ex.: se chamou um método ou setou uma propriedade
	
O Moq permite usar duplas de teste que atuam como dummies, stubs e mocks.
	
## Tipos de teste

** Testes baseados em estado **: são testes em que cria um objeto, e testa o resultado da operação

	Exemplo:

	- Cria-se um objeto
	- Configura os dados desse objeto
	- Executamos a ação
	- Verificamos o resultado da operação
	
	
** Testes baseados em comportamento **: são testes onde se testa o comportamento da funcionalidade, e não só o resultado da operação.
	
	Exemplo:
		
	- Cria-se um objeto
	- Configura os dados desse objeto
	- Executamos a ação
	- Verificamos se o comportamento esperado para aquela ação foi executado corretamente, e não só o resultado da ação
	
Explicando melhor:
	
Imagine um sistema onde há uma base de dados, e um cache entre o banco de dados e a aplicação, para que não seja necessária a consulta dos dados a todo momento no banco de dados. 
	
Em um determinado momento, o cache é atualizado; em algum momento, pode ocorrer uma inconsistência no cache em relação ao banco.	
O que diferencia o teste de comportamento do teste de estado é justamente isso: verificamos não apenas o resultado da operação	de consulta, mas se todo o comportamento esperado do ciclo foi realizado (o valor está atualizado? atualizou o valor no cache?	ocorreu algum erro de atualização durante a operação? etc)
	
# Moq

## Caracteristicas
	
- O Moq suporta tanto o Dotnet Core como o Dotnet Framework.
- É um dos mais populares para DotNet
- Pronuncia-se "Mock-You" ou "Mock"
- É um projeto open source
- https://github.com/moq
		
## Como adicionar um projeto de testes com Moq

### Visual Studio: 
	
			- Dotnet Core: tem template para projeto no Visual Studio 2019 para instalar o xUnit
			- Adicionar referencia no NuGet para a biblioteca Moq
	
			- Dotnet Framework: criar um projeto Class Library, e adicionar as seguintes referencias:
	
				-> 	xunit
				-> 	xxnit.runner.visualstudio

				using Xunit;
				using Moq
		
### Linha de comando:
		
			(Dotnet Core): dotnet add xunit
						   dotnet add moq
	
## Executar os testes
	
### Visual Studio:
		
	- Test Explorer: run all ou clicar com o botão direito em cima do teste que se quer executar e selecionar 'Run'
			
	- Linha de comando (dentro da pasta do projeto de testes):
		
	dotnet test
			
## Strict e Loose Mocks
	
Por padrão, o Moq cria Loose Mocks. (ao pé da letra, uma simulação de objeto "solta")
		
MockBehavior.Strict
		
	Gera uma exceção se o metodo mockado é chamado, mas não foi configurado
		
MockBehavior.Loose
		
- Nunca gera uma exceção, mesmo que um método simulado seja chamado, mas não tenha sido configurado.
- Retorna valores padrão para tipo de valor, nulo para tipos de referência, array vazio/lista
			
MockBehavior.Defailt
		
- Comportamento padrão se nenhum tipo for especificado (MockBehavior.Loose)
		
Para especficar o tipo de teste, colocar o tipo na criação do objeto Mock:
	
	var objetoMock = new Mock<ValidadorDeIdade>(MockBehavior.Strict);
		
		
Importante: o Strict exige que TODOS os métodos e TODAS as propriedades sejam configuradas. Se for implementado um novo teste, para uma nova propriedade, isso deve ser refletido em TODOS os métodos de teste que possuem Strict	no metodo de testes, senão os métodos vão quebrar. O que se pode fazer, para este caso, é deixar como Default, pois as configurações existentes serão utilizadas, e nenhuma configuração adicional será necessária para os testes	já implementados.

Comparação Loose x Strict 
		
Loose
		
	- Poucas linhas de configuração
	- Configuração apenas daquilo que é relevante
	- Default values
	- Testes menos frágeis
	- Testes existentes continuam funcionando
		
Strict
		
	- Mais linhas de configuração dos mocks
	- Talvez seja necessário configurar coisas irrelevantes para os testes
	- Necessário configurar cada chamada de método
	- Testes mais frágeis
	- Testes existentes podem quebrar
		
	
Instancia simples de uma classe "mockada"
	
	using Moq;
	using Xunit;
	
	[Trait("Category","testes")]
	public class MeusTestes 
	{
		[Fact]
		public void Teste()
		{
			var objetoMock = new Mock<tipo>();
				...
			var sut = new Classe(objetoMock.Object); //usar o .Object para pegar o objeto simulado	
				...
			}
		}
	
Setup de um valor ou retorno de método da classe mock
	
		using Moq;
		using Xunit;
	
		[Trait("Category","testes")]
		public class MeusTestes 
		{
			[Fact]
			public void Teste()
			{
				var objetoMock = new Mock<tipo>();
				objetoMock.Setup(x => x.<método>).Returns(valor a ser retornado);
				var sut = new Classe(objetoMock.Object); //usar o .Object para pegar o objeto simulado
				...
			}
		}
		
Importante (1): no exemplo acima, cuidado com o parametro que for passado no Setup para o método/propriedade,  e o objeto que você vai testar, pois os valores devem ser os mesmos. 
					
Exemplo: imagine uma classe que possua a validação de um aluno. Você passa um objeto Aluno,	e dentro da classe você possui algumas validações, dentre elas, idade do aluno. A idade do aluno	é validada por uma outra classe (ValidadorDeIdade), a qual você criou o Mock:
			
			[Fact]
			public void ValidarInformacoesDoAluno_ValidacaoOk()
			{
				var objetoMock = new Mock<ValidadorDeIdade>();
				objetoMock.Setup(x => x.ValidarIdade(16)).Returns(true);
				
				var sut = new ValidadorAluno(objetoMock);
				
				var aluno = new Aluno() 
				{
					Nome = "xpto",
					Idade = 16
				}
				
				var retorno = sut.ValidarAluno(aluno);
				
				Assert.Equals(true, retorno);
			}
			
O método foi configurado com o valor "16" para validação da idade. No nosso objeto Aluno, a idade deve	ser coerente com o que você passou para o Mock (neste caso, 16). Se for passado um valor diferente para a propriedade aluno, o teste irá falhar. Ou seja, o código do teste deve ser coerente com o valor configurado			para o objeto mock.
			
Importante (2): se o objeto Mock não for configurado, o valor das propriedades/retorno dos métodos será o valor padrão para aquele tipo de objeto. Exemplo, para objetos booleanos, será retornado falso
		
Outras formas de utilizar o setup informado um parametro:
			
	objetoMock.Setup(x => x.ValidarIdade(It.IsAny<int>())).Returns(true);
	objetoMock.Setup(x => x.NomeDoAluno(It.Is<string>(valor => valor.Contains("A"))).Returns(true);
			objetoMock.Setup(x => x.ValidarIdade(It.IsInRange<int>(1,50,Range.Inclusive))).Returns(true);
			objetoMock.Setup(x => x.ValidarIdade(It.IsRegex("/^[A-Z]*$/"))).Returns(true);
			
Para métodos que possuem parametros "out":
			
	var parametroOut = valor;
	objetoMock.Setup(x => x.ValidarIdade(It.IsAny<int>(), out parametroOut)).Returns(true);
			
Para métodos que possuem parametros "ref" (por referencia):
			
Exemplo 1:
		
	var person1 = new Person();
	var person2 = new Person();
	var mockGateway = new Mock<IGateway>();
				
	mockGateway.Setup(x => x.Incluir(ref person1)).Returns(-1);
				
	var sut = new Processor(mockGateway.Object);
				
	sut.Processor(person1); //retorna -1
	sut.Processor(person2); //retorna outro valor, porque person2 é uma instancia de person1
				
Exemplo 2:
			
	var person1 = new Person();
	var person2 = new Person();
	var mockGateway = new Mock<IGateway>();
				
	mockGateway.Setup(x => x.Incluir(ref It.Ref<Person>.IsAny)).Returns(-1);
				
	var sut = new Processor(mockGateway.Object);
				
	sut.Processor(person1); //retorna -1
	sut.Processor(person2); //também retorna -1, pois tanto faz a instancia por causa do IsAny no setup
			
			
Para ambos os exemplos, funcionaria também com herança. Se houvesse um método especializando a classe Person, como, por exemplo, Aluno, poderiamos passar para sut.Processor o objeto Aluno e o teste funcionaria de forma	identica.
			
Returns 
		
Nos exemplos dados até agora, um valor específico é retornado. Para os casos onde precisamos retornar algum valor "processado", pode-se criar uma função, e utilizar o retorno desta function dentro do método Returns.
			
Exemplo:
			...
			objetoMock.Setup(x => x.PrevisaoDoTempo(It.IsAny<DateTime>())).Returns(SimularPrevisaoDoTempoAleatoria);
			
	...
	public string SimularPrevisaoDoTempoAleatoria()
	{
		Random rnd = new Random();
		int result  = rnd.Next(1, 4);
		var tempo = "";
				
		switch(result)
		{
			case 1: tempo = "Sol"; break;
			case 2: tempo = "Parcialmente Nublado"; break;
			case 3: tempo = "Nublado"; break;
			case 4: tempo = "Chuva"; break;
			default: tempo = "SOL"; break;
		}	
				
		return tempo;
	}
			
Valores Default
		
É possível criar valores default para as propriedades do objeto mockado.
			
Exemplo:
			
	var mockValidador = new Mock<ISecurityInfoValidator>();
	mockValidator.DefaultValue = DefaultValue.Mock;
			
Desta forma, todos os valores de mockValidator vão assumir seus valores padrão. (mesmo objetos externos terão uma instancia criada). Se a propriedade for boolean, será retornado false, se for int, será retornado 0, etc.
			
Alteração de valores do objeto mock
	
Por padrão, os objetos mocados não permitem alteração de suas propriedades. Se, por exemplo, dentro do código da classe concreta, for alterado alguma propriedade do objeto mockado, esse valor não será refletido.
			
Para permitir a alteração, usar a instrução abaixo no método de testes:
			
	...
	var mockObject = new Mock<MinhaInterfaceTeste>();
	mockObject.SetupProperty(x => x.NomeDaPropriedade);
			
			
Se TODAS as propriedades do objeto mock puderem ser alteradas dentro da classe, usar:
			
	mockObject.SetupAllProperties();
			
Cuidado: os métodos de configuração SetupProperty e SetupAllProperties devem vir antes dos demais Setups do objeto mockado
			
Verificando o comportamento
	
Para verificar o comportamento da execução da ação, não testamos o resultado do teste, e sim, se um determinado método/propriedade	foram alterados.
		
Exemplo:
		
IValidaNumeroCartao possui um método chamado NumeroValido. Vamos testar se este método será chamado durante a execução do teste:
		
	[Fact]
	public void VerificaSeMetodoFoiAcionado()
	{
		var validadorNumeroCartao = new Mock<IValidadorNumeroCartao>();
		validadorNumeroCartao.Setup(x => x.NumeroValido(It.IsAny<string>())).Returns(true);

		//por padrao, o objeto mockado não guarda a informação.
		//A linha abaixo permite que o objeto seja alterado e seu valor guardado
		validadorNumeroCartao.SetupProperty(x => x.ModoDeValidacao);

		var sut = new ValidacaoProposta(validadorNumeroCartao.Object);

		var proposta = new Proposta()
		{
			NumeroCartao = "123"
		};

		sut.ValidarProposta(proposta);
		validadorNumeroCartao.Verify(x => x.NumeroValido(It.IsAny<string>()),"O método não foi acionado");
	}

Neste exemplo, não verificamos o retorno de sut.ValidarProposta(proposta). Utilizamos o método Verify do Moq,
e verificamos se o método NumeroValido foi acionado. Adicionalmente, incluimos um segundo parametro, com uma	mensagem customizada, para indicar um erro (no console do test explorer) caso o método não tenha sido acionado.
		
Em outra situação, poderiamos testar se o método não foi chamado. Em alguns casos, dependendo da regra de negócio,	um determinado método não deve ser chamado de acordo com os parâmetros passados no teste. Para esta situação,	utilizar o parametro Times.Never:
		
	validadorNumeroCartao.Verify(x => x.NumeroValido(It.IsAny<string>()),Times.Never);
		
O mesmo pode ocorrer para testar quantas vezes o método foi acionado. A estrutura Times possui várias opções.
		
Para propriedades, utilizar:
		
	validadorNumeroCartao.VerifyGet(x => x.NomeDaPropriedade);
	validadorNumeroCartao.VerifySet(x => x.NomeDaPropriedade);
		
	*VerifySet está acusando erro de deprecated, porém, no curso, não acusava essa mensagem
		
Verificando se uma exceção foi lançada

Durante o setup do mock, podemos verificar se alguma exception foi lançada. Para isso, configuramos o retorno
da configuração para Throws<Exception>():
		
	mockObject.Setup(x => x.MockMethod(It.IsAny<string>())).Throws<Exception>();
		
	mockObject.Setup(x => x.MockMethod(It.IsAny<string>())).Throws(new Exception("mensagem da exception para mostrar na console log do teste");
		
Eventos
	
É possivel simular o disparo de eventos em classe de duas formas:
		
- No setup do mock
		
			mockObject.Setup(x => x.MockMethod(It.IsAny<string>())).Returns(retorno).Raises(x => x.NomeDoEvento += null, EventArgs.Empty);
			
- No codigo do teste
		
			mockObject.Raise(x => x.NomeDoEvento += null, EventArgs.Empty);

Obtendo resultados diferentes, quando o método for chamado varias vezes
	
Em determinadas situações, podemos querer que o método retorne valores diferentes quando este for chamado mais de uma vez dentro do teste. Para isso, no setup do método, usar SetupSequence, e adicionar novos Returns para o metodo:
		
		mockObject.SetupSequence(x => x.NumeroValido(It.IsAny<string>()))
								.Returns(true)
								.Returns(false);
								
Verificando se um método foi chamado "n" vezes com os valores passados
	
Para verificar se um determinado método foi chamado usando uma lista de chamadas, e capturar as chamadas realizadas, cria-se uma lista	e usa-se o parametro Capture.In() passando a lista como parametro. Depois, realiza-se a chamada, e compara-se se o resultado é igual ao realizado nas chamadas.
		
Exemplo:
		
	[Fact]
	public void TestMethod()
       {
            var mockObject = new Mock<IMockObject>();
            var listaValidacoes = new List<string>();
	        mockObject.Setup(x => x.NumeroValido(Capture.In(listaValidacoes)));        
			var sut = new ObjetoASerTestado(mockObject.Object);

            var teste1 = new ObjetoTeste() { Parametro = "11" };
            var teste2 = new ObjetoTeste() { Parametro = "22" };
            var teste3 = new ObjetoTeste() { Parametro = "33" };

            sut.MetodoDeTeste(proposta1);
            sut.MetodoDeTeste(proposta2);
            sut.MetodoDeTeste(proposta3);

            Assert.Equal(new List<string> {"11","22","33"}, listaValidacoes);
        }
	
LinqToMoq

Há utra forma de realizar o setup de um objeto mock, usando uma sintaxe do LinqToMoq:
		
	IValidadorNumeroCartao validadorCartao = Mock.Of<IValidadorNumeroCartao>(
                    validador => validador.Chave == "EXPIRED" &&
                                 validador.NumeroValido(It.IsAny<string>()) == true
                );
	
Porém, nessa chamada, ao invés de usar validadorCartao.Object no objeto sut, usa-se apenas validadorCartao:
		
	var sut = new ValidacaoProposta(validadorCartao);
		
Parametros Genéricos
	
No setup do método, podemos informar um valor para o parametro do método em questão, ou podemos usar "It.IsAny<`tipo`>" para especificar que qualquer valor daquele tipo será aceito como parametro.
	
Porém, por algum motivo, podemos querer testar passando um valor qualquer, seja ele int, double, string, etc.
		
Neste caso, o setup deve ser feito com It.IsAny<`It.IsAnyType`>(). Assim, o método receberá qualquer valor como parametro.
		
Também é possivel testar por herança. Neste caso, usa-se It.IsSubType<`Type`>
		
Tratamento de métodos asincronos e tasks
	
Para tratamento de métodos assincronos ou tasks, tratamento no Result do setup:
		
Exemplo 1:
		
	mockObject.Setup(x => x.MetodoAssincrono(It.IsAny<type>)).Returns(Task.FromResult(new Objeto()));
		
Exemplo 2:
		
	mockObject.Setup(x => x.MetodoAssincrono(It.IsAny<type>)).Returns(Task.CompleteTask);
		
Exemplo 3:
		
		mockObject.Setup(x => x.MetodoAssincrono(It.IsAny<type>)).ReturnsAsync(44);