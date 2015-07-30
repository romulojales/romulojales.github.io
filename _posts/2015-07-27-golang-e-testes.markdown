---
title: "GOLANG: Tudo começa com um teste"
date: 2015-07-27 01:00:00
description: "Iniciando o estudo de golang por testes"
---

Todos concordam que ter testes é bom, que tdd melhora a qualidade do software e que ter CI (continuos integration) é
essencial. Tendo esta questão em mente, decidi que vou aprender GO (golang) pelo viés dos teste.

Go já incorpora em sua filosofia como linguagem testes. Sem a necessidade de usar bibliotecas ou comandos de terceiros.

A ideia deste post é estudar testes no contexto das quatro operações básicas de inteiros.

Inteiro porque é um tipo muito simples e em geral o seu comportamento é igual em todas as linguagens. Se começasse por
string, por exemplo, eu não sei, neste momento, qual o resultado ao aplicar o operador "+" entre duas strings. Testando
por um tipo cuja natureza tende a não mudar de linguagem para linguagem, posso explorar bem os conceitos de testes, o
objetivo primário deste post, sem precisar me ater a detalhes profundos da linguagem, como alocação de memória.
 
Antes de começarmos, o comando básico para executar testes em go é **test** (¬¬)

Você pode acessar os códigos deste post no meu [repositório](https://github.com/romulojales/estudos-golang/tree/master/src/inteiros).

#Criando o primeiro caso de teste

Para criar um teste basta criar um arquivo com o sufixo \_test.go. Para o nosso caso vamos criar o arquivo
inteiro_test.go.

Dentro do arquivo tenho:

{% highlight go  %}
package main

import "testing"

func TestSoma(t *testing.T){}
{% endhighlight %}
 
Conforme mencionado no post do [hello world](/golang-hello-world/) a primeira linha indica que o arquivo está no pacote
 que
contém o ponto inicial de execução de um programa. Já a linha importa para o contexto a biblioteca de testes. Observe
que eu não precisei instalar nada para ter este módulo. Já a quinta linha declara o escopo de teste. Vamos por hora 
ignorar a sintaxe do argumento do teste.
  
Podemos executar o comando test e observar o resultado:

{% highlight bash %}
go test inteiro_test.go 
ok  	command-line-arguments	0.074s
{% endhighlight %}

Como não escrevemos nada no escopo do teste TestSoma o resultado ok é o esperado.

#Testando soma de inteiros

Vamos agora testar a soma de dois inteiros e atribuir a uma variável. O objetivo também é observar como é a sintaxe de
inicialização de variáveis.

Pode-se iniciar uma variável declarando que é uma variável, seu nome e seu tipo. Exemplo:
 
{% highlight go  %}
var x int
x = 1 + 1
{% endhighlight %}

**var** é a declaração de uma variável. **x** é o nome da variável e **int** é o tipo da variável.
Ou pode-se usar outra sintaxe que aproveita o resultado de uma atribuição:

{% highlight go  %}
x := 1 + 1
{% endhighlight %}

Neste exemplo não foi necessário declarar previamente a variável nem seu tipo. O tipo é determinado pelo resultado da
operação.

Observe que ":=" é utilizado para quando a variável está sendo declarada pela primeira vez no escopo. No exemplo
declarativo utilizei apenas "=" porque x já estava inicializada.

Uma das coisas bacanas do go, é que o compilador não permite que você declare uma variável e não use. Se complementarmos
o arquivo inteiro_test.go com:

{% highlight go  %}
package main

import "testing"

func TestSoma(t *testing.T){
	x := 1 + 1
}
{% endhighlight %}

O compilador já retorna uma mensagem de erro, sem ao menos testar:

{% highlight bash %}
./inteiro_test.go:6: x declared and not used
FAIL	command-line-arguments [build failed]
{% endhighlight %}

Vamos agora criar um caso real de teste. Vamos ver se ao somar 1 + 1 o resultado da operação retorna 2.

{% highlight go  %}
package main

import "testing"

func TestSoma(t *testing.T){
	x := 1 + 1
	if x != 2 {
	 t.Error("Opa! 1+1 não é igual a 2, obtive", x)
	}
}
{% endhighlight %}

{% highlight bash %}
go test inteiro_test.go 
ok  	command-line-arguments	0.098s
{% endhighlight %}

Amém! 1 + 1 é igual a 2 em go!

Como go sabe que o teste não deu erro? Simplesmente se o teste não é interrompido, o teste é satisfatório. Por isto
o primeiro caso de teste, sem nada dentro da função, passou.

Vamos forçar um erro. Vamos supor que eu pense que o operador "+" na verdade concatene os números, e 1 + 1 seja 11.

{% highlight go  %}
package main

import "testing"

func TestSoma(t *testing.T){
	x := 1 + 1
	if x != 11 {
	 t.Error("Opa! 1 + 1 não é igual a 11, obtive", x)
	}
}
{% endhighlight %}

{% highlight bash %}
 go test inteiro_test.go 
--- FAIL: TestSoma (0.00s)
	inteiro_test.go:8: Opa! 1 + 1 não é igual a 11, obtive 2
FAIL
FAIL	command-line-arguments	0.044s
{% endhighlight %}

Então, 1 + 1 não é igual a 11. huuuum!

O if do go não precisa por os argumentos de teste entre parênteses. Caso o argumento de teste seja verdadeiro vai entrar
 no bloco subsequente. Ai encontramos o na linha 8: t.Error.
 
O t (minúsculo) é uma instância de testing.T . O módulo testing possuem diversos tipos. Vamos começar pelo T. Você pode
saber sobre o tipo T fazendo: "godoc testing T"

O T é usado para controlar o estado dos testes e para formatar as mensagens e possui diversos tipos de funções. No nosso
primeiro exemplo uso a função Error, que para a execução dos testes e imprime uma mensagem de erro passada como
argumento.
 
#Criando uma função que soma dois números

Vamos criar uma situação um pouco mais real. Vou criar uma função Soma noutro arquivo e testar a funcionalidade numa
abordagem TDD.

Primeiro vou criar o arquivo inteiro.go e dentro dele uma função soma. O objetivo deste arquivo será agrupar métodos de
operação de inteiros. 

{% highlight go  %}
package main

func soma(){}
{% endhighlight %}

Nada muito especial no arquivo. Observe que o nome do pacote é main. O mesmo do teste.

Agora vou mudar o teste para usar a função soma.

{% highlight go  %}
package main

import "testing"

func TestSoma(t *testing.T){
	x := soma(1, 1)
	if x != 2 {
	 t.Error("Opa! 1 + 1 não é igual a 2, obtive", x)
	}
}
{% endhighlight %}

Executando:

{% highlight bash %}
 go test
./inteiro_test.go:6: too many arguments in call to soma
./inteiro_test.go:6: soma(1, 1) used as value
FAIL	_/inteiros [build failed]
{% endhighlight %}

Eu precisei mudar o comando primeiro, antes executávamos "go test inteiro_test.go" agora é apenas "go test" isto para
incluir
no contexto de execução o arquivo inteiro.go

Observe que ao executar o comando o teste não deu erro. Por que a função não está pronta para a execução. Eu passo, dois 
valores como parâmetro de soma. E a função não espera nada. Mudando a função para aceitar dois parâmetros:

{% highlight go  %}
package main

func soma(x int, y int){ }
{% endhighlight %}

Lembrem-se que o tipo vem a direita da variável. A função está correta sintaticamente mas não está idiomática. O jeito
GO de escrever dois argumentos de mesmo tipo sequencialmente é: **func soma(x, y int)**

Vejamos agora o que teremos como resposta ao teste:

{% highlight bash %}
 go test
./inteiro_test.go:6: soma(1, 1) used as value
FAIL	_/inteiros [build failed]
{% endhighlight %}

Ou seja, quando uso a função soma eu espero que tenha uma resultado. basta alterar a função soma para:

{% highlight go  %}
func soma(x, y int) int {

}
{% endhighlight %}

{% highlight bash %}
 go test
./inteiro.go:5: missing return at end of function
FAIL	_/inteiros [build failed]
{% endhighlight %}

Falta o argumento de retorno.

{% highlight go  %}
func soma(x, y int) int {
 return x + x
}
{% endhighlight %}

{% highlight bash %}
 go test 
PASS
ok  	_/inteiros	0.034s
{% endhighlight %}

Sucesso! Eu consegui escrever minha primeira função em go! Mas será que ela está correta? Vamos estressar um pouco mais
a função. Vamos somar valores diferentes.

{% highlight go  %}
package main

import "testing"

func TestSomaUmMaisDois(t *testing.T){
	x := soma(1, 2)
	if x != 3 {
	 t.Error("Opa! 1 + 2 não é igual a 3, obtive", x)
	}
}

func TestSomaUmMaisUm(t *testing.T){
	x := soma(1, 1)
	if x != 2 {
	 t.Error("Opa! 1 + 1 não é igual a 2, obtive", x)
	}
}
{% endhighlight %}

Agora criei uma função que testa valores diferentes, no caso 1 + 2 vejamos a execução:

{% highlight bash %}
go test 
--- FAIL: TestSomaUmMaisDois (0.00s)
	inteiro_test.go:8: Opa! 1 + 2 não é igual a 3, obtive 2
FAIL
exit status 1
FAIL	_/inteiros	0.023s
{% endhighlight %}

De fato, o teste evidenciou um erro! Tem algo de errado na minha função soma. Olhando a função de perto vejo que somei
duas vezes o número x. alterando para x + y o resultado do testes é satisfatório.

#Dividindo ou testando um erro esperado

Vamos criar mais uma função, a divisão. Sabemos que a divisão por 0 não está definida. Então é esperado que o código
consiga validar este tipo de situação.
 
Vamos criar a função divide:

{% highlight go  %}
package main

func divide(x, y int) int{
 return x / y
}
{% endhighlight %}

Testando o caminho feliz:
{% highlight go lineno %}
package main

import "testing"

func TestDivide(t *testing.T){
	x := divide(4, 2)
	if x != 2 {
	 t.Error("Opa! 4 / 2 não é igual a 2, obtive", x)
	}
}
{% endhighlight %}

{% highlight bash %}
go test
PASS
ok  	_/inteiros	0.040s
{% endhighlight %}

Tudo feliz.

Agora vamos forçar uma situação de erro. Dividindo por 0.

{% highlight go lineno %}
func TestDividePorZero(t *testing.T){
	x := divide(4, 0)
	if x != 2 {
	 t.Error("Opa! 4 / 2 não é igual a 2, obtive", x)
	}
}
{% endhighlight %}

{% highlight bash %}
go test
--- FAIL: TestDividePorZero (0.00s)
panic: runtime error: integer divide by zero [recovered]
	panic: runtime error: integer divide by zero
[signal 0x8 code=0x7 addr=0x5be42 pc=0x5be42]

...
{% endhighlight %}

Então minha função não está pronta para um caso absurdo como a divisão por zero. O que preciso fazer é preparar a função
para retornar um erro de validação para o absurdo.

Go, aceita saídas de multiplos tipos, alterando a função para:

{% highlight go  %}
func divide(x int, y int) (int, error){
 return x / y, nil
}
{% endhighlight %}

Agora a função retorna dois valores, o resultado da função e um possível erro.  Mas já estamos cientes que desta forma o
teste não passará pois é necessário validar melhor a entrada. Assim precisamos implementar a função de forma a não dar
erro:

{% highlight go  %}

import "errors"

var errorDivisorZero = errors.New("O divisor da operação não pode ser zero")

func divide(x int, y int) (int, error){
 var err error
 var resultado int
 if y == 0{
   err = errorDivisorZero
 }else{
  resultado = x / y
 }
 return resultado, err
}

{% endhighlight %}



Eu alterei a função para validar se o segundo parâmetro (divisor) é zero. Caso seja, err passa a ser ter como valor o
erro declarado **errorDivisorZero**, que contém a mensagem "O divisor da operação não pode ser zero"
  
Caso o segundo parâmetro não seja zero, é realizado a divisão.

Agora precisamos alterar o teste para o caso de retorno de uma mensagem de erro:


{% highlight go  %}

func TestDividePorZero(t *testing.T){
	_, err := divide(4, 0)
	if err != nil {
	 t.Error(err)
	}
}
{% endhighlight %}
{% highlight bash %}

go test
--- FAIL: TestDividePorZero (0.00s)
	inteiro_test.go:29: O divisor da operação não pode ser zero
FAIL
exit status 1
FAIL	_/inteiros	0.049s
{% endhighlight %}

Agora eu obtenho a mensagem de erro que programei. Mas o teste ainda falha. O que eu quero testar é se ao passar zero
como divisor e testar o erro esperado:

{% highlight go  %}

func TestDividePorZero(t *testing.T){
	_, err := divide(4, 0)
	if err != errorDivisorZero {
	 t.Error(err)
	}
}
{% endhighlight %}
{% highlight bash %}

go test
PASS
ok  	_/inteiros	0.073s
{% endhighlight %}

Sucesso! Obtive o erro esperado quando tento fazer uma divisão por zero.

#Cobertura de código, quando menos é menos mesmo!

Das quatro operações básicas da matemática ainda temos a subtração e a multiplicação. Vamos escrever uma função para
subtração:

{% highlight go  %}
func subtrai(x, y int) int {
 return x - x
}
{% endhighlight %}

Testando...

{% highlight bash %}
go test
PASS
ok  	_/inteiros	0.073s
{% endhighlight %}

Epa, mas você não escreveu o teste! Como ser notificado que existe um trecho de código que não está sendo testado? Isto
é o caso para a cobertura de testes! Lembra que eu falei que golang tem preocupação com testes desde o início?

Pois bem, o comando test aceita o argumento -cover para medir a cobertura de teste. Mas antes precisamos ver o conceito
de GOPATH.

##GOPATH, primeiros passos

Go necessita que você especifique um caminho absoluto para o ambiente do projeto. Este caminho será baseado as
dependências do projeto, os comandos auxiliares, sua fonte de arquivos. Vamos criar um ambiente voltado para concluir o
caso de testes:

* crie um diretório onde você quiser chamado estudo-golang. Será a raiz do seu projeto
* crie um diretório src e dentro dele crie um diretório inteiros
* mova os arquivos inteiro.go e inteiro_test.go para dentro do diretório inteiros

o resultado esperado é:

{% highlight bash %}
| - estudo-golang/
   |- src/
    |- inteiros/
    	|- inteiro.go
    	|- inteiro_test.go
{% endhighlight %}

ou você pode clonar meu [repositório no github](https://github.com/romulojales/estudos-golang) que já contém esta
estrutura de arquivos;

agora defina a variável de ambiente GOPATH

{% highlight bash %}
export GOPATH=${PWD}
{% endhighlight %}

##Cover

O go não trás na conjunto padrão de ferramentas a cobertura de testes. Para instalar a ferramenta cover faça:

{% highlight bash %}
go get golang.org/x/tools/cmd/cover
{% endhighlight %}

Caso tenha dificuldades com o seu sistema operacional não exite em comentar que tentarei te ajudar.

Vejamos o que acontece quando eu executo os testes medindo a cobertura com a função subtrai declarada mas não testada,
lembrando que as funções soma e divide estão no contexto:

{% highlight bash %}
go test inteiros -cover
ok  	inteiros	0.002s	coverage: 87.5% of statements
{% endhighlight %}

Aha! O comando de teste agora diz que tenho 87.5% de cobertura. 

Num projeto grande fica difícil saber qual o ponto do código não está sendo testado, vamos melhorar um pouco a saída do
teste:

primeiro gere um arquivo de saída:

{% highlight bash %}
go test -coverprofile=c.out inteiros
{% endhighlight %}

então abra no browser a interpretação da saída:

{% highlight bash %}
go tool cover -html=c.out
{% endhighlight %}

Como resultado:

![cobertura do teste de inteiros](/assets/images/go-cov-inteiros.png)

Observe que as linhas em vermelho são os trechos sem cobertura.

Agora escrevo um teste para subtração:

{% highlight go  %}
func TestSubtrai(t *testing.T){
	resultado := subtrai(4, 5)
	if resultado != -1 {
	 t.Error("curioso, na terra dos gophers 4-5 não é -1. é", resultado)
	}
}
{% endhighlight %}

E temos cobertura de 100%!

{% highlight bash %}
go test inteiros -cover
ok  	inteiros	0.004s	coverage: 100.0% of statements
{% endhighlight %}

#Teste de performance: multiplicando o tempo

Go prove outro tipo de teste: Benchmark! A classe usada para realizar este tipo de teste é B.

Mas, antes de escrevermos um teste de performance vamos escrever uma função que multiplica. Lembro quando estava na 
escola a primeira forma de multiplicar que aprendi foi realizar uma sequencia de somas. Em go o algoritmo seria desta
 forma:
 
{% highlight go %}
func multiplicaSomando(x, y int) int{
	resultado := 0
    if y > 0{
     for i := 0; i<y; i++{
		resultado = soma(resultado, x)
	 }
    }else if y < 0{
		for i := y; i<0; i++ {
			resultado = subtrai(resultado, x)
		}
    }
	return resultado;
}
{% endhighlight %}

O exemplo acima apresenta como fazer um laço usando **for**. A sintaxe lembra bastante C ou javascript, mas não temos
 a necessidade de por a expressão entre parênteses e observe como é iniciada a variável de controle "i". E mostra o 
 reuso das funções soma e subtrai previamente criadas. 
    
Antes de fazermos o teste de performance, vejamos se a função consegue multiplicar corretamente, considerando os casos:

* multiplica por 0
* multiplica por -1
* multiplica por outro número qualquer
* multiplica menos com menos

{% highlight go %}
func TestMultiplicaSomandoSimples(t *testing.T){
	resultado := multiplicaSomando(4, 5)
	if resultado != 20 {
	 t.Errorf("4 * 5 não é 20 é %d", resultado)
	}
}

func TestMultiplicaSomandoPor0(t *testing.T){
	resultado := multiplicaSomando(4, 0)
	if resultado != 0 {
	 t.Errorf("4 * 0 não é 0 é %d", resultado)
	}
}

func TestMultiplicaSomandoPorMenos1(t *testing.T){
	resultado := multiplicaSomando(4, -1)
	if resultado != -4 {
	 t.Errorf("4 * -1 não é -4 é %d", resultado)
	}
}

func TestMultiplicaSomandoPor1(t *testing.T){
	resultado := multiplicaSomando(4, 1)
	if resultado != 4 {
	 t.Errorf("4 * 1 não é 4 é %d", resultado)
	}
}

func TestMultiplicaSomandoPorMenos3(t *testing.T){
	resultado := multiplicaSomando(4, -3)
	if resultado != -12 {
	 t.Errorf("4 * -3 não é -12 é %d", resultado)
	}
}

func TestMultiplicaSomandoMenosComMenos(t *testing.T){
	resultado := multiplicaSomando(-4, -3)
	if resultado != 12 {
	 t.Errorf("-4 * -3 não é 12 é %d", resultado)
	}
}


func TestMultiplicaSomandoMenosComMenosUm(t *testing.T){
	resultado := multiplicaSomando(-4, -1)
	if resultado != 4 {
	 t.Errorf("-4 * -1 não é 4 é %d", resultado)
	}
}
{% endhighlight %}

Antes de discutirmos os resultados, observe que mudei o a função para reportar o erro, de t.Error para t.Errorf. A 
diferença é que no caso de t.Error quando é evocado para o testes. Já o Errorf marca a teste como falho mas não 
interrompe a execução dos outros testes por vir.

Executando os testes:

{% highlight bash %}
go test inteiros
ok  	inteiros	0.054s
{% endhighlight %}

Perfeito! 

Mas sabemos que podemos usar outra abordagem para multiplicar. Que é utilizar o operador correto, "\*". Então escrevo
 outra a função, multiplica, usando o operador correto e duplico as funções de testes. Mudando o prefixo de 
 TestMultiplicaSomando para TestMultiplica apenas. Você pode ver as funções indo no [repositório](https://github
 .com/romulojales/estudos-golang/tree/master/src/inteiros) que criei no github.
 
Agora, vem a pergunta. Qual das duas funções é executa mais rápido? Para responder a esta pergunta vamos o tipo de 
teste Benchmark.

Primeiro eu crio outro arquivo: "inteiro_bench_test.go", para deixar separado os tipos de teste, com o seguinte 
conteúdo:

{% highlight go %}
package main

import "testing"

func BenchmarkMultiplicaSomando(b *testing.B) {
	for i := 0; i < b.N; i++ {
		multiplicaSomando(-4, -3)
		multiplicaSomando(-4, -1)

		multiplicaSomando(4, -3)
		multiplicaSomando(4, -1)
		multiplicaSomando(4, 0)

		multiplicaSomando(4, 1)
		multiplicaSomando(4, 5)
	}
}

func BenchmarkMultiplica(b *testing.B) {
	for i := 0; i < b.N; i++ {
		multiplica(-4, -3)
		multiplica(-4, -1)

		multiplica(4, -3)
		multiplica(4, -1)
		multiplica(4, 0)

		multiplica(4, 1)
		multiplica(4, 5)
	}
}
{% endhighlight %}

Observe que o nome da função agora inicia com Benchmark. Isto é necessário para o go saber que o tipo do teste é de 
benchmark. Agora, usamos como tipo de o B, que é de benchmark. 

O laço dentro da função é para repetir o bloco que se quer testar repetidas vezes. Note que a condição de parada é b
.N e não há nada dentro da função que inicialize esta variável. Isto é o próprio go que especifica a quantidade de 
repetições para ter realizar uma média de tempo confiável de cada função.

Apliquei a mesma quantidade de chamadas a cada função e utilizei os mesmo dados. Já tinha testado previamente cada 
função segundo as mesmas entradas e sei que cada um tem o resultado esperado. Assim, os dois testes tem o mesmo valor
 semântico.

Então vamos rodar o comando:

{% highlight go %}
go test -bench=.
PASS
BenchmarkMultiplicaSomando	30000000	        36.2 ns/op
BenchmarkMultiplica	500000000	         3.70 ns/op
ok  	_/inteiros	3.384s
{% endhighlight %}

O resultado nos mostra que multiplicar usando o operador próprio de multiplicação é o mais rápido! O teste está nos 
mostrando que:

* 30 milhões de operações de multiplicaSomando a 36.2 nano segundos por laço.
* 500 milhões de operações de multiplica a 3,7 nano segundos por laço.

O teste de benchmark é útil para fazer comparação e inclusive para monitorar a cada nova implementação o impacto do 
novo código.

Quanto ao comando utilizado, para rodar o teste de benchmark é necessário passar a flag "-bench" com o parametro de 
qual caminho a olhar os testes.

#Conclusão

O go tem um ferramental para testes bom. O fato de ter nativo testes de benchmark me deixou bem feliz. Agora é 
possível sem grandes malabarismos realizar métricas de performance evolutivas com o testes (tema para um próximo post).

No entando a forma de testar em go nativa achei bem "pé-duro". Mas existem diversas bibliotecas que auxiliam o teste
(tema para outro post :) ).

