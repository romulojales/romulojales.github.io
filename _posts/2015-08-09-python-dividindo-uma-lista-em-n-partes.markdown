---
title: "Python: Dividindo uma lista em N partes"
date: 2015-08-09 23:00:00
description: "Como dividir uma lista em n partes"
keywords: "python, listas"
---

Este é um post recuperado do limbo do 404 e incrementado de uns dois anos atrás.

Frequentemente eu uso esse código, então ao invés de ir no código antigo vou neste post.
Basicamente o que eu quero é o seguinte: Tenho uma lista e quero dividir em n partes quase-iguais. Explico

Suponha a lista [1,2,3,4,5,6,7,8,9,10] quero dividir a lista em 5 partes.

A divisão da lista resultaria em [1,2], [3,4], [5,6], [7,8], [9,10]

Ok, isso é chato de fazer...

Mas vamos ao código.

A lista
lista = [1,2,3,4,5,6,7,8,9,10]
quebrando tudo:

{% highlight python %}
def quebrador(lista, partes):
    return list(lista[ parte*len(lista)/partes:(parte+1)*len(lista)/partes ] for parte in range(partes))
    
print quebrador(lista,5)
[[1, 2], [3, 4], [5, 6], [7, 8], [9, 10]]

print quebrador(lista,2)
[[1, 2, 3, 4, 5], [6, 7, 8, 9, 10]]

print quebrador(lista,10)
[[1], [2], [3], [4], [5], [6], [7], [8], [9], [10]]

print quebrador(lista,0)
[]

{% endhighlight %}

É possível que ocorrram variações para o tipo de lista. Inclusive é possível usar um gerador (yield) para criar a nova lista dividida.

