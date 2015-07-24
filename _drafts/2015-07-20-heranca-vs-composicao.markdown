---
title: "Herança versus Composição"
date: 2015-07-20 01:00:00
description: "Minhas anotações de quando usar herança e quando usar composição"
---

Em geral ouço as pessoas repetindo o mantra:

"use composição no lugar de herança"

Mas realmente elas sabem o que recomendam ou repetem o que ouviram de alguém mais experiente?

Vou tentar explorar os conceito de herança e composição e explicar o que me motiva decidir por um ou por outro.

Saliento que esta é uma discussão desde 1986...

##O que é Herança?

O conceito básico de herança é: "A possibilidade de *estender* uma outra entidade." 
 
Observe que o verbo é estender, ou seja adicionar algo. Não sobre-escrever! Mas porque escrevemos classes abstratas? E as
 linguagens nos oferece a opção de sobre-escrita 

##O que é Composição?

Vamos usar o mesmo exemplo de herança para explicar como funciona a composição.

Você já percebeu que necessita criar as classes Mamiféro, Ave e Réptil, mas não tem certeza se existe uma relação clara entre
Ave, Mamífero e Animal (você matou aula de biologia?). Você sabe que um Gato é animal porque ele é um ser pluricelular
e sabe que é mamífero mas não sabe o que diferencia um gato de papagio, que é uma Ave. 

Então o que você pode modelar é uma composição, ou seja para quando as relações não estão claras.

{% highlight python %}
class Animal(object):
 ...
 
class Mamifero(object):
 ...

class Gato(Animal, Mamifero):
 ...

{% endhighlight %}

Mas também você pode usar composição para criar classes que não são *necessariamente* hierarquicos.


Note que eu usei composição por que eu não tinha certeza da relação entre Animal, Mamífero e Gato para usar uma estrutura
hirarquica, que é de certa forma rigida e de alto acomplamento.


##E então?

Note que a minha política de uso de hierarquia ou c