---
title: "GOLANG: Trabalhando com JSON"
date: 2015-09-06 20:00:00
description: "como trabalhar com json com golang"
keywords: "golang, json, structs, marshal, unmarshal"
---

A primeira coisa que eu tive que lidar com golang foi com [Json](http://json.org/). Tanto criar um quanto ler um.

Golang traz em sua biblioteca padrão suporte a serialização (criar) e
desserialização (ler) de jsons: [encoding/json](http://golang.org/pkg/encoding/json/).

#Criando um JSON

A função que cria um json é json.Marshal. Podemos traduzir Marshal como ordenar. Quando
você cria um Json você transforma um objeto em um conjunto de bytes enfileirados. Muitas
vezes representamos esse conjunto de bytes ordenados por uma string.

Lembrem-se que uma string é um conjunto de ordenados de caracteres. Cujo o valor, usando-se a tabela
ASCII pode ser visualizado como simbolos inteligíveis.

Por que é necessário discutir isso inicialmente? Porque o retorno da função golang json.Marshal é um array de bytes.
E caso queiramos ler o valor é necessário transformar este array de bytes em string.

Já o parametro de entrada do json.Marshal é uma interface. Interfaces podem ser interpretadas como uma forma de polimorfismo
para golang. Ou seja, a função aceita qualquer tipo de *estrutura* como entrada.

Vamos a um exemplo simples:

{% highlight go %}
type SimpleJsonStructure struct {
		Integer int
		String string
		Boolean bool
}
{% endhighlight %}

O código acima mostra uma estrutura de dados, passível de virar um json, de nome SimpleJsonStructure.
A estrutura contém três atributos:

* Integer do tipo inteiro (int)
* String do tipo string
* Boolean do tipo bool, que aceita valore true ou false.

Os nomes Integer, String, Boolean são nomes de variáveis e não de tipos.
Em go, quando você declara uma variável explicitamente, como foi
feito, o tipo fica a direita do nome. O motivo que usei estes nomes é apenas para expressar os tipos que eles pertencem.

Para instanciar esta estrutura, fazemos de modo simples:

{% highlight go %}
simpleObject := SimpleJsonStructure { 123, "Hello World", true}
{% endhighlight %}

Em Javascript o o SimpleJsonStructure e simpleObject seriam semelhante a:

{% highlight Javascript %}
simpleObject = {Integer: 123, String: "Hello World", Boolean: true}
{% endhighlight %}

Para criar um json:

{% highlight go lineno %}
 simpleObjectJson, err := json.Marshal(simpleObject)
 if err != nil {
   fmt.Println("Erro!")
 }
 fmt.Print(string(simpleObjectJson))
{% endhighlight %}

Em Golang, não temos try/catch, o que temos é o retorno de erro. Sempre precisamos
inspecionar o resultado do erro. Em geral, caso o erro seja igual a nil, significa que uma
função executou com sucesso.

O objeto simpleObjectJson é um array de bytes. Para vermos o que foi gerado é necessário converter para string
antes de executar o Print (linha 5). Com a saída esperada: {"Integer":123,"String":"Hello World","Boolean":true}.

##Testes

Para testar se a função foi gerada satisfatoriamente uma das formas de testar é comparar a saída com uma string.

Porém, lembre-se que o retorno da função é um array de bytes.

Assim, para testar eu costumo fazer a seguinte comparação:

{% highlight go lineno %}
 simpleObjectJson, err := json.Marshal(simpleObject)
 if err != nil {
   fmt.Println("Erro!")
 }
 if !bytes.Equal(simpleObjectJson, []byte(`{"Integer":123,"String":"Hello World","Boolean":true}`)){
   fmt.Println("Erro!")
 }
{% endhighlight %}

#Lendo um Json

A operação inversa do json.Marshal é json.Unmarshal. A assinatura da função Unmarshal é:

{% highlight go lineno %}
json.Unmarshal(data []byte, v interace{}) error
{% endhighlight %}

Ou seja, recebe um array de bytes, uma interface e retona uma erro. Como o Unmarshal é a uma
operação inversa do Marshal, que por sua vez retorna um array de bytes, o primeiro argumento é
o json a ser lido.

Já o segundo argumento é o objeto que receberá o json, uma interface. Conforme eu disse, argumentos que
são interface funcionam como uma assinatura polimórfica da função. Assim, o Unmarshal aceita qualquer tipo de *estrutura*.

Como no Marshal passamos uma estrutura que será traduzida num json. No Unmarshal a estrutura de parâmetro é acoplada ao
formato do json. Ou seja, você precisa conhecer previamente a estrutura do json para decodifica-lo em um objeto.

Vejamos o teste:

{% highlight go lineno %}

type SimpleJsonStructure struct {
		Integer int
		String string
		Boolean bool
}
simpleObject := SimpleJsonStructure{}
jsonObj :=  []byte(`{"Integer":123,"String":"Hello World","Boolean":true}`)
err := json.Unmarshal(jsonObj, &simpleObject)
 if err != nil {
   fmt.Println("Erro!")
 }
{% endhighlight %}

Primeiro nós criamos a estrutura do json e instanciamos um objeto que irá receber os atributos. Então no Unmarshal,
decodifico o json na estrutura mapeada.

##Algumas perguntas sobre o unmarshal

**Mas se o Json tiver um atributo não mapeado? Por exemplo tivermos mais uma chave "NaoEsperado" de valor 123?**

O Unmarshal só irá traduzir o que está na estrutura. A chave NaoEsperado será ignorada.

**Se o Json não tiver um atributo que a estrutura depende? Por exemplo não vir a chave "Integer"?**

O Unmarshal dará erro na conversão. Para evitar que o programe falhe ou para contemplar casos onde um campo é opcional
a função Unmarshal utiliza "Tags", que funcionam como atributos de campo da estrutura, informando se um campo da
estrutura deve ser ignorado completamente quando for vazio.


{% highlight go lineno %}

type SimpleJsonStructure struct {
		Integer int "json:,omitempty"
		String string
		Boolean bool
}
{% endhighlight %}

**Como trabalhar com chaves quando nome no json é diferente da estrutura?**

Novamente usa-se tags: Observe o exemplo

{% highlight go lineno %}
type SimpleJsonStructure struct {
	Integer  int         `json:"numero,omitempty"`
	String   string      `json:"nome"`
	Boolean  bool        `json:"ehBrasileiro"`
}
{% endhighlight %}

No exemplo, o json terá as chavas numero (opcional), nome e ativo que são mapeados respectivamente para
Integer, String, Boolean na estrutura.

#Json genérico

Num cenário em que não sabemos o que esperar de uma resposta, quando pode vir campos que você não conhece o formato ou
quando você sabe que podem variar, usamos campos do tipo interface{}

{% highlight go lineno %}
type SimpleJsonStructure struct {
	Integer  int         `json:"numero,omitempty"`
	String   string      `json:"nome"`
	Boolean  bool        `json:"ehBrasileiro"`
	TheWorld interface{} `json:"oRestoDoMundo,omitempty"`
}

func main() {
	simple := SimpleJsonStructure{}
	json.Unmarshal([]byte(`{"numero": 1, "nome": "romulo", "ehBrasileiro": true, "oRestoDoMundo": {"umaString": "abc", "umArray":[1,2,3]}}`),
		&simple)
	fmt.Println(simple.TheWorld)

	json.Unmarshal([]byte(`{"numero": 1, "nome": "romulo", "ehBrasileiro": true, "oRestoDoMundo": "o universo"}`),
		&simple)
	fmt.Println(simple.TheWorld)
}
{% endhighlight %}

A saída esperada é: map[umArray:[1 2 3] umaString:abc] e para a segunda conversão é "o universo"

Ou seja, não importa a estrutura da chave de json "oRestoDoMundo" como o tipo TheWorld na estrutura é interface{}
será convertido para o tipo associado, garantindo a compatibilidade.
