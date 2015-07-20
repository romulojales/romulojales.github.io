---
title:  "GOLANG: uma jornada se inicia"
date:   2015-07-19 01:00:00
description: "Um brevissimo resumo da linguagem GO e porque irei a fundo nela."
disqus:
---

[GOLANG][go-link], ou simplesmente GO, é uma linguagem de programação desenvolvida pelo google para o google.

Um grupo de desenvolvedores do google estava insatisfeito com as opções de
linguagens e sistemas existentes para produzir sistemas para o google. Problemas como:

* dificuldade de compilar/deployar um projeto grande
* deficiência de algumas linguagens em lidar com multiprogramação
* gerência de dependência confusa

Pensando nestes problemas, e noutros, [Robert Griesemer][robert-link], [Rob Pike][rob-pike-twitter] e [Ken Thompson][ken-wiki]
desenharam em 2007 os objetivos de uma nova linguagem.
Em 2008 o projeto ganhou dedicação exclusiva e em 2009 tornou-se público, cujo os objetivos principais são prover uma linguagem:

* Com rápida compilação
* Com análise de dependência fácil
* Tendo garbage-collection
* Multiprogramação já no design da linguagem

Portanto podemos resumir em uma linha que GO é:

**Uma linguagem compilada, com suporte a concorrência nativo, com garbage-collection e com tipagem estática**

Outro aspecto importante da filosofia por trás do desenvolvimento do GO é a engenharia de software. Explicitamente os autores
mencionam que foram orientados a resolver questões pertinentes a engenharia de software, como:

* Conjunto de ferramentas
* Simplicidade no modelo de programação (concorrência e garbage collector)
* Composição ao invés de herança
* Sintaxe simples
* Semântica simples
* Resolução de dependência nativa

De fato, tendo este conjunto de soluções já nativa à linguagem e não provido por bibliotecas facilita o trabalho da
engenharia. (mas lembrem-se que não há bala de prata)

Diante da natureza da linguagem orientada a resolver problemas já conhecidos e que outras ferramentas não tiveram
sucesso em endereçar (além da necessidade de extender um software já desenvolvido em go), iniciarei uma série de estudos
desta linguagem.

##maiores informações:

* [Go FAQ][go-faq]
* [Go at Google: Language Design in the Service of Software Engineering][go-splash]

[go-splash]: http://talks.golang.org/2012/splash.article
[go-faq]: http://golang.org/doc/faq
[go-link]: http://golang.org/
[robert-link]: https://plus.google.com/+RobertGriesemer/
[rob-pike-twitter]: https://twitter.com/rob_pike
[ken-wiki]: https://en.wikipedia.org/wiki/Ken_Thompson