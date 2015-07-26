---
title:  "Otimizações de performance para o meu site"
date:   2015-07-26 02:06:00
description: "O que fiz para otimizar o tempo de carregamento no meu site"
comments: true
---

Quando eu voltei a blogar, acho que esta é minha quarta tentativa, decidi que a estrutura necessária para o blog seria a
mais simples possível. Não queria gastar dinheiro com hospedagem do blog, me preocupar com software, atualizações, banco
de dados. Queria, e ainda quero, é apenas focar em escrever. 

Como alguns amigos na globo estavam usando o [Jekyll](http://jekyllrb.com) e [github pages](https://pages.github.com),
decidi avaliar estas opções.
 
O Jekyll é um gerador de páginas estáticas escrito em ruby. Ou seja, você escreve na sua máquina um post em [markdown](http://daringfireball.net/projects/markdown/)
ou [rst](http://docutils.sourceforge.net/rst.html) e o Jekyll trata de gerar um html completo do seu portal. Portanto,
não preciso de um software rodando na internet e nem suas dependências. Tudo estático. Mas ainda é necessário servir para
a internet estes htmls. Ai entra o github pages. 
 
O github pages olha para um repositório no github e usando uma estrutura de arquivos e pastas, bem acoplada ao que
 o jekyll depende, gera o html final, tal qual o jekyll. Assim, eu consigo manter meus posts num repositório na internet,
  salvo de problemas eventuais na minha máquina, e ainda sirvo meu blog. E o melhor, sem custos financeiros.
  
Mas ainda é necessário investir tempo na escolha de um tema bacana para o blog, obeservar se é responsivo e ficar de olho
nas métricas de perfomance e SEO.

Eu escolhi o [Cactus](https://github.com/koenbok/Cactus) como tema. Minalista, responsivo, já integrado com pygments e 
coloração de sintaxe de linguagens de programação. Tudo que precisava.

Depois que botei o blog no ar e alguns posts - para dar uma animada - passei a olhar a performance de carregamento do
site. E anotei alguns pontos chaves:
  
  * Css não era minificado
  * Js não minificado
  * Html não minificado

#CSS

Como trabalho com web sei que existem algumas regras básicas ao lidar com carregamento de css:

 * O css deve ficar no topo da página.
 * Css minificado
 * Css comprimido
 
obs.: Foge do escopo deste post performance do css em si. Trato apenas de carregamento

Ao analisar o tema que escolhi tinha para css vi que:

* O css já estava no topo da página. - OK
* dois arquivos de css sendo carregados - Não tão OK
* não estavam minificados - NOK
* não estavam comprimidos. - NOK

O primeiro passo foi minificar o CSS. Minificar é um processamento do arquivo para remover todos os espaços em brancos 
e caracteres, como quebra de linha (\n), não necessários. A primeira abordagem que pensei foi copiar e colar numa destes
sites que fazem a minificação. Mas e se eu precisar modificar o css? Vai ser um inferno. Assim, também resolvi atacar outro
problema, não relacionado a performance de load, mas de gerência de código. Criei a estrutura de [scss](http://sass-lang.com/documentation/file.SCSS_FOR_SASS_USERS.html)
para o meu projeto. O Jekyll suporta scss nativamente.   

Assim, antes tinhamos:

{% highlight bash %}
css
   /assets
   |----- style.css
   |----- highlight.css
{% endhighlight %}

