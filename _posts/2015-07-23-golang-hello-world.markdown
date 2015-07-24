---
title:  "Golang: Hello World"
date:   2015-07-23 23:00:00
description: "hello world do golang"
comments: true
---

Geralmente, em linguagens de programação, iniciamos o aprendizado com o objetivo de escrever na tela do usuário "Hello World!".

Para não fugir do lugar comum, vou mostrar o que é necessário para ter um hello world via go.

##Instalando Go

###No mac

Para instalar o go no mac o caminho mais curto é usando o [pacote oficial][golang-dl] distribuído no site do golang.
Escolha o arquivo de extensão .pkg e seja feliz.

Mas eu prefiro caminho via [brew][brew-home], uma vez que eu o utilizo para instalar facilmente outros programas que não tem a facilidade
de um pacote pronto. Para tal:

{% highlight bash %}
brew install go
{% endhighlight %}

###No windows

Eu realmente não uso windows há anos. Portanto acho que o [pacote oficial][golang-dl] oficial é o recomendado.

###No linux

Simplemente use os gerenciadores de pacotes padrão. Mas se você usa uma versão exótica do linux pode utilizar o
pacote pre-compilado.
 
##Hello World

Uma vez que você instalou o golang no seu computador, vamos escrever o programa hello-world.go:

{% highlight go linenos %}
package main

import "fmt"

func main() {
    fmt.Println("Hello World")
}
{% endhighlight %}

para rodar o seu programa:

{% highlight bash %}
go run hello-world.go
{% endhighlight %}

O resultado esperado é que você veja Hello World impresso.

###Explicando

Este pequeno programa já tem algumas informações bem importantes a cerca da estrutura de um programa go. 

A primeira linha contém o pacote do programa. O nome main não foi aleatório. Ele é necessário para dizer ao go que este é o pacote
que deve ser utilizado para iniciar o programa. Portanto, "package main" serve para dizer onde está o inicio de um programa go.

A próxima linha contém a importação de uma biblioteca. Go exige que você declare o que você vai usar no programa. A biblioteca
fmt, bult-in do go, serve para realizar I/O: ou seja impresões e capturas.

Quando você instala o go existe uma ferramente que auxilia na documentação: godoc. Portanto você pode usar godoc fmt para obter
 mais informações sobre a biblioteca fmt.
 
A próxima linha é a declaração de uma função. Da mesma forma que o nome do pacote foi importante para indicar que contém o
ponto de início do programa, todo programa go depende que existe uma função de nome main associada indicando qual é o
primeiro trecho de código a ser executado.

Já a linha que contém o "Hello World" utiliza a biblioteca fmt e a função PrintLn, que imprime na tela o arqgumento da função
 mais o caractere de pulo de linha.
  
Por fim, o comando go run hello-world.go executa o arquivo hello-world.go . 

##compilando

Você pode ter lido no meu primeiro <a href="/iniciando-os-estudos-em-go/">post</a> sobre go, que a linguagem
compilada. 

Para compilar o programa, faça:

{% highlight bash %}
go build hello-world.go
{% endhighlight %}

Terminado o build, você encontrará no mesmo diretório de hello-world.go um arquivo executável hello-world. Execute-o e você
verá "Hello World" no prompt. 

[golang-dl]: https://golang.org/dl/
[brew-home]: http://brew.sh
