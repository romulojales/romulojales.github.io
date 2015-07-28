---
title:  "Otimizações de performance para o meu site"
date:   2015-07-26 02:06:00
description: "O que fiz para otimizar o tempo de carregamento no meu site"
comments: true
---

#TL;TR
Eu fiz os seguintes passos para otimizar a performance do meu blog:

* Minifiquei dos css
* Removi o jquery
* Otimizei imagens para retina display.
* Minifiquei o html

##Resultado:

Utilizei como benchmark o post que falo como fazer [hello world usando golang](/golang-hello-world/): 

* De duas requisições de css para uma
* De 13,7 kb de tamanho de css para 10,7kb
* Para o caso de uso de js, de 46,3kb para 11,4kb.
* Redução de 12% no tamanho do markup

##Trabalhos futuros

Melhorar o html e o css.

#Discussão

Quando eu voltei a blogar, acho que esta é minha quarta tentativa, decidi que a estrutura necessária para o blog seria a
mais simples possível. Não queria gastar dinheiro com hospedagem do blog, me preocupar com software, atualizações e banco
de dados. Queria, e ainda quero, é apenas focar em escrever. 

Como alguns amigos na globo estavam usando o [Jekyll](http://jekyllrb.com) e [github pages](https://pages.github.com),
decidi avaliar estas opções.
 
O Jekyll é um gerador de páginas estáticas escrito em ruby. Ou seja, você escreve na sua máquina um post em [markdown](http://daringfireball.net/projects/markdown/)
ou [rst](http://docutils.sourceforge.net/rst.html) e o Jekyll trata de gerar um html completo do seu blog. Portanto,
não preciso de um software rodando na internet e nem suas dependências. Tudo estático. Mas ainda é necessário servir para
a internet estes htmls. Ai entra o github pages. 
 
O github pages olha para um repositório no github e usando uma estrutura de arquivos e pastas, bem acoplada ao que
 o jekyll depende, gera o html final, tal qual o jekyll. Assim, eu consigo manter meus posts num repositório na internet,
  salvo de problemas eventuais na minha máquina e ainda sirvo meu blog. E o melhor, sem custos financeiros.
  
Mas ainda é necessário investir tempo na escolha de um tema bacana para o blog, observar se é responsivo e ficar de olho
nas métricas de performance e SEO.

Eu escolhi o [Cactus](https://github.com/koenbok/Cactus) como tema. Minimalista, responsivo, já integrado com pygments - para 
coloração de sintaxe de linguagens de programação-. Tudo que precisava.

Depois que botei o blog no ar e alguns posts - para dar uma animada - passei a olhar a performance de carregamento do
site. E anotei alguns pontos chaves:
  
  * Css não era minificado
  * Js não minificado
  * Html não minificado

Do ponto de vista de backend, não há o que otimizar. O site já está na forma ótima para internet e como uso o github pages
não tenho acesso a nenhuma configuração em termos de máquinas e servidores.

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

O primeiro passo foi minificar o CSS. Minificar é um processamento do arquivo para remover todos os espaços em brancos 
e caracteres, como quebra de linha (\n), não necessários. A primeira abordagem que pensei foi copiar e colar numa destes
sites que fazem a minificação. Mas e se eu precisar modificar o css? Vai ser um inferno. Assim, também resolvi atacar outro
problema, não relacionado a performance de load, mas de gerência de código. Criei a estrutura de [scss](http://sass-lang.com/documentation/file.SCSS_FOR_SASS_USERS.html)
para o meu projeto. 

Assim, antes tinha:

{% highlight bash %}
| - assets/
   |- css/
    |- style.css
    |- highlight.css
{% endhighlight %}

O arquivo style.css tem 12,3kb e o highlight tem 1,4k. O maior problema aqui não é tão o tamanho dos arquivos, mas a necessidade de 
fazer duas requisições. Além do que, quando o browser encontra a diretiva de carregamento do css ele para o carregamento
da página até tê-lo no contexto.

Então, tenho que ter apenas um arquivo! Poderia até por inline no html. Mas isto não é uma boa prática.

Agora tenho:
  
{% highlight bash %}
| - assets/
   |- css/
    |- estilo.scss
{% endhighlight %}

Dentro do arquivo estilo.scss

{% highlight scss %}
---
--- 
@import "style";
@import "highlight";
{% endhighlight %}

Os @imports são diretivas do scss para incluir outros scss, que são exatamente os arquivos style.css e
highlight.css. No momento eu só fiz renomear a extensão de .css para .scss e mover para outra pasta.

Já as duas linhas com "---" no início do arquivo são necessárias para informar ao jekyll que o arquivo estilo.scss deve
ser processada como um arquivo final, que vai entrar na estrutura final do blog. O legal é que o jekyll já suporta nativamente
o sass.

Logo, a nova estrutura de diretórios ficou:

{% highlight bash %}
   |- _sass/
    |- _style.scss
    |- _highlight.scss
{% endhighlight %}

O jekyll irá compilar os arquivos scss num único arquivo estilo.css. Mas ainda tenho que mudar no html para o novo arquivo de
estilos:

De:
{% highlight html %}
<link rel="stylesheet" href="/assets/css/style.css">
<link rel="stylesheet" href="/assets/css/highlight.css">
{% endhighlight %}
Para:
{% highlight html %}
  <link rel="stylesheet" href="/assets/css/estilo.css">
{% endhighlight %}

Resolvido o problema de ter dois arquivos de css. Ainda faltava o problema da minificação do arquivo. 

Com jekyll foi extremamente simples de resolver. Bastou adicionar no \_config.yml o bloco:

{% highlight yaml %}
sass:
    style: :compressed
{% endhighlight %}

Que reduziu o arquivo de 13,4kb para 10,7kb, aproximadamente 20%.

Nada mal! De dois arquivos para apenas um, com redução de 20%! Isto pode não parecer muito. Mas a técnica usada é escalável
e é usada por exemplo no globoesporte.com, portal para o qual trabalho.

#Javascript

Também podemos aplicar a técnica de minificar css para arquivos js. Mas decidi analisar primeiro o js.

A primeira coisa que estranhei foi o carregamento do jquery. Ora, meu site não tem nada de especial em termos de js, porque
preciso do jquery?

Analisando todos os elementos de js da página, o único lugar que precisava do jquery era um js para mudar as imagens para
 quando a tela do dispositivo que acessasse o meu blog fosse retina. huuuuum...
 
Quais os impactos desta abordagem:

* Carregar duas vezes uma imagem de valor semântico igual
* Se o javascript der erro ou estiver desabilitado, o efeito é nulo
* Carregar o jquery apenas para substituição de imagem? WTF!

Existem outra técnicas para assegurar que a imagem será a melhor no retina. A que eu escolhi foi ofertar imagens com as dimensões
sendo o dobro da necessidade. Exemplo, meu avatar na home precisa de uma imagem 80x80, então vou ofertar uma imagem de 160x160.
Você pode está se afirmando: "opa, ele está aumentando o peso da página sem necessidade". Mas na computação tudo é uma 
  questão de medir a relação custo benefício. Eu sei que para uma pessoa nova entrando no meu site ela irá pagar o custo 
  desta imagem maior, mas a tendência a longo prazo que a imagem estará cacheada no browser (já que esta imagem não será 
  mudada com tanta frequência e ela é transversal ao blog). E esta abordagem é melhor que usar um js, que demandará toda vez
  que o usuário entrar na página executa-lo. 
  
Antes deste post eu tinha duas imagens para o avatar com densidades diferentes. Uma de 80x80 com 3,8Kb e outra de 160x160
com 12,4kb. Ou seja, caso o js detectasse que era uma tela retina, só para mostrar o meu avatar seria necessário baixar 16,8kb
só de imagem, além do jquery: 29,3kb; e do outro js de processamento: 0,7kb. Totalizando 46,3kb. Usando a abordagem de 
uma imagem dimensionada baixo apenas 11,4kb para qualquer caso!

Ainda tive mais uma oportunidade de performance que foi por o js de highlight em modo defer. Outros js que preciso, são o do
google analytics e do disqus que já estão em sua forma recomendada.

#HTML

Apesar do github pages entregar todos os seu conteúdos usando gzip, ainda é possível ganhar alguns bytes minificando o html.

O que eu utilizei foi o [jekyll-compress](https://github.com/penibelst/jekyll-compress-html) para minificar html usando
jekyll e o github pages. Há outras ferramentas para realizar esta tarefa, mas o github pages tem um conjunto limitado de plugins 
 disponíveis que não inclui um para minificar html. 
 
O primeiro passo e configurar o \_config.yml para ter o bloco compress_html:

{% highlight yaml %}
compress_html:
  clippings: all
  comments: all
  endings: []
  profile: false
{% endhighlight %}

**profile** indica o resultado da compressão. Pode deixar false. Caso queria ver as estatísticas, deixe true o valor
que aparecerá uma tabela ao final da página.

**endings**, remove todas as tags opcionais de fechamento. Por exemplo, é opcional ao abri "\<p\>" por o "\</p\>". Eu não gosto desta
abordagem, por isso o valor []. Caso você queira remover todas, use all. ou preencha a lista das tags que você deseja remover.

**comments**, remove todos os comentários. Qual seria o caso de um valor diferente de all? Por exemplo os bloco de comentários
condicionais, como o "<!--[if gte IE 7]>" . Caso você **não** queira remover este tipo de bloco, mas remover os comentários
regulares "<!-- isto é um comentário regular --!>" você usa a valor para comments como: ["<!--", "--!>"].

**clippings**, são as tags que podem ter seus espaços em brancos removidos, já considerando o caso da tag pre. Caso, você
queira ser explicito, enumere uma a uma dentro da lista.

Por fim, é necessário preparar o html. É necessário usar o arquivo compress.html do jekyll compress e adiciona-lo a pasta
_layout:

{% highlight bash %}
   |- _layout/
    |- compress.html
    |- default.html
    |- post.html
{% endhighlight %}

E mudar o arquivo default para conter o seguinte cabeçalho:

{% highlight bash %}
---
layout: compress
---
{% endhighlight %}

O jekyll compress usa a própria linguagem de template [liquid](https://github.com/Shopify/liquid/wiki) para minificar o 
html.

O resultado que obtive para a página [hello world usando golang](/golang-hello-world/) foi uma redução de 9kb para 7,9kb.
12% de melhoria (sem o gzip).

Novamente, minificar html é uma técnica que escala, pode ser usada em grandes portais e num contexto de tráfico de dados
intenso pode resultar em economia de dinheiro.

#Conclusão

Para a página [hello world usando golang](/golang-hello-world/) todo o esforço, sem considerar o disqus e o google analytics,
resultou em:

* Três requisições a menos
* Melhora na renderização da página com menos elementos bloqueantes
* Redução de 360kb para 330Kb, 8% de melhora global para o post.

De modo geral, o trabalho de otimização foi bom e a escolha pela dupla jekyll + github pages foi boa. 

O próximo passo é tentar melhorar o css e o markup e fontes que vieram com o tema que escolhi.
