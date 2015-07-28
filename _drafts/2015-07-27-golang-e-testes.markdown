---
title: "GOLANG: Tudo começa com teste"
date: 2015-07-27 01:00:00
description: "Iniciando o estudo de golang por testes"
---

Todos concordam que ter testes é bom, que tdd melhora a qualidade do software e que ter CI (continuos integration) é essencial. Tendo esta questão em mente, decidi que vou aprender GO (golang) pelo viés dos teste.

Go já incorpora em sua filosofia como linguagem testes. Sem a necessidade de usar bibliotecas ou comandos de terceiros.

A ideia deste post é estudar testes no contexto das quatro operações básicas de inteiros.

Inteiro porque é um tipo muito simples e em geral o seu comportamento é igual em todas as linguagens. Se começasse por string, por exemplo, eu não sei, neste momento, qual o resultado ao aplicar o operador "+" entre duas strings. Testando por um tipo cuja natureza tende a não mudar de linguagem para linguagem, posso explorar bem os conceitos de testes, o objetivo primário deste post, sem precisar me ater a detalhes profundos da linguagem, como alocação de memória.
 
Antes de começarmos, o comando básico para executar testes em go é **test** (¬¬)

#Criando o primeiro caso de teste

Para criar um teste basta criar um arquivo com o sufixo \_test.go, no nosso caso vamos criar o arquivo inteiro_test.go.

Dentro do arquivo tenho:

{% highlight go linenos %}
package main

import "testing"

func TestSoma(t *testing.T){
	
}
{% endhighlight %}
 
Conforme mencionado no post do [hello world](/golang-hello-world/) a linha 1 indica que o arquivo está no pacote que contém
o ponto inicial de execução de um programa. Já a linha importa para o contexto a biblioteca de testes. Observe que eu não
 precisei instalar nada para ter este módulo. Já a linha 5 declara o escopo de teste. Vamos por hora ignorar a sintaxe do argumento
  do teste.
  
Podemos executar o comando test e observar o resultado:

{% highlight bash %}
go test inteiro_test.go 
ok  	command-line-arguments	0.074s
{% endhighlight %}

Como não escrevemos nada no escopo do teste TestSoma o resultado ok é esperado.

#Testando soma de inteiros

Vamos agora testar a soma de dois inteiros e atribuir a uma variável. O objetivo também é observar como é a sintaxe de inicialização
de variáveis.

Pode-se iniciar uma variável declarando que é uma variável, seu nome e seu tipo. Exemplo:
 
{% highlight go linenos %}
var x int
x = 1 + 1
{% endhighlight %}

Ou pode-se usar outra sintaxe que aproveita o resultado de uma atribuição:

{% highlight go linenos %}
x := 1 + 1
{% endhighlight %}

Neste exemplo não foi necessário declarar previamente a variável nem seu tipo. O tipo é determinado pelo resultado da operação.

Observe que ":=" é utilizado para quando a variável está sendo declarada pela primeira vez no escopo. No exemplo declarativo
utilizei apenas "=".

Uma das coisas bacanas do go, é que o compilador não permite declarar uma variável e não usa-la. Se complementarmos o arquivo inteiro_test.go com:

{% highlight go linenos %}
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

Vamos agora criar um caso real de teste. Vamos ver se eu somar 1 + 1 go retorna 2.

{% highlight go linenos %}
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

Como go sabe que o teste não deu erro? Simplesmente se o teste não é interrompido o teste é satisfatório. Por isto
o primeiro caso de teste, sem nada dentro da função, passou.

Vamos forçar um erro. Vamos supor que eu pense que o operador "+" na verdade concatene os números, e 1 + 1 seja 11.

{% highlight go linenos %}
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

O if do go não precisa por os argumentos de teste entre parenteses. Caso o argumento de teste seja verdadeiro vai entrar
 no bloco subsequente. Ai encontramos o na linha 8: t.Error.
 
O t (minúsculo) é uma instancia de testing.T . O módulo testing possuem diversos tipos. Vamos começar pelo T. Você pode saber 
sobre o tipo T fazendo: "godoc testing T"

O T é usado para controlar o estado dos testes e para formatar as mensagens. Possui diversos tipos de funções. No nosso
primeiro exemplo uso a função Error, que para a execução dos testes e imprime uma mensagem de erro passada como argumento.
 
#Criando uma função que soma dois números

Vamos criar uma situação um pouco mais real. Vou criar uma função Soma noutro arquivo e testar a funcionalidade numa abordagem TDD.

Primeiro vou criar o arquivo inteiro.go e dentro dele uma função soma. O objetivo deste arquivo será agrupar métodos de
operação de inteiros. 

{% highlight go linenos %}
package main

func soma(){

}
{% endhighlight %}

Nada muito especial no arquivo. Observe que o nome do pacote é main. O mesmo do teste.

Agora vou mudar o teste para usar a função soma.

{% highlight go linenos %}
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

Eu precisei mudar o comando primeiro, antes executávamos "go test inteiro_test.go" agora é apenas "go test" isto para incluir
no contexto de execução o arquivo inteiro.go

Observe que ao executar o comando o teste não deu erro. Por que a função não está pronta para a execução. Eu passo, dois 
valores como parâmetro de soma. E a função não espera nada. Mudando a função para aceitar dois parâmetros:

{% highlight go linenos %}
package main

func soma(x int, y int){

}
{% endhighlight %}

Lembrem-se que o tipo vem a direita da variável. A função está correta sintaticamente mas não está idiomática. O jeito GO
de escrever dois argumentos de mesmo tipo sequencialmente é: func soma(x, y int).

Vejamos agora o que teremos como resposta ao teste:

{% highlight bash %}
 go test
./inteiro_test.go:6: soma(1, 1) used as value
FAIL	_/inteiros [build failed]
{% endhighlight %}

Ou seja, quando uso a função soma eu espero que tenha uma resultado. basta alterar a função soma para:

{% highlight go linenos %}
func soma(x, y int) int {

}
{% endhighlight %}

{% highlight bash %}
 go test
./inteiro.go:5: missing return at end of function
FAIL	_/inteiros [build failed]
{% endhighlight %}

Falta o argumento de retorno.

{% highlight go linenos %}
func soma(x, y int) int {
 return x + x
}
{% endhighlight %}

{% highlight bash %}
 go test 
PASS
ok  	_/inteiros	0.034s
{% endhighlight %}

Sucesso! Eu consegui escrever minha primeira função em go! Mas será que ela está correta? Vamos estressar um pouco mais a função. 
Vamos somar valores diferentes.

{% highlight go linenos %}
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

De fato, o teste evidenciou um erro! Tem algo de errado na minha função soma. Olhando a função de perto vejo que somei duas
vezes o número x. alterando para x + y o resultado do testes é satisfatório.

#Dividindo ou testando um erro esperado

Vamos criar mais uma função, a divisão. Sabemos que a divisão por 0 não está definida. E é esperado que o código consiga
 validar este tipo de situação.
 
Vamos criar a função divide:

{% highlight go linenos %}
package main

func divide(x int, y int) int{
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

Então minha função não está pronta para um caso absurdo como divisão por zero. O que preciso fazer é preparar a função
para retornar um erro numa validação do absurdo.

Go, aceita saídas de multiplos tipos, alterando a função para:

{% highlight go linenos %}
func divide(x int, y int) (int, error){
 return x / y, nil
}
{% endhighlight %}

Agora a função retorna dois valores, o resultado da função e um possível erro.  Mas já estamos cientes que desta forma o teste
não passará é necessário validar melhor a entrada, assim precisamos implementar a função de forma a não dar erro:

{% highlight go linenos %}

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



Eu alterei a função para validar se o segundo parâmetro (divisor) é zero. Caso seja, err passa a ser ter como valor o erro declarado
  errorDivisorZero, que contém a mensagem "O divisor da operação não pode ser zero"
  
Caso o segundo parâmetro não seja zero, é realizado a divisão.

Agora precisamos alterar o teste para receber a função:


{% highlight go linenos %}

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

Agora eu obtenho a mensagem de erro que programei. Mas o teste ainda falha. O que eu quero testar é se ao passar zero como 
divisor acontece o erro esperado, assim:
{% highlight go linenos %}

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

Sucesso!

#Cobertura de código, quando menos é menos menos mesmo!

Das quatro operações básicas da matemática temos a subtração. Vamos escrever uma função para subtração:

{% highlight go linenos %}
func subtrai(x, y int) int {
 return x - x
}
{% endhighlight %}
http://romulojales.com/
Mas, você não escreveu o teste! Como ser notificado que existe um trecho de código que não está sendo testado? Isto é o caso
para a cobertura de testes. Lembra que eu falei que golang tem preocupação com testes desde o início?

Pois bem, o comando test aceita o argumento -cover para medir a cobertura de teste. Vejamos o que acontece quando eu executo os testes
com a função subtrai declarada mas não testada, lembrando que as funções soma e divide estão no contexto:



#Teste de performance
