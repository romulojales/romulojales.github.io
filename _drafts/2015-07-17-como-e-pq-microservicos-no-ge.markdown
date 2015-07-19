---
title:  "Como e por que de micro-serviços no Globoesporte.com"
date:   2015-07-17 01:00:00
description:
---

O ano de 2014 para o globoesporte.com foi bastante positivo, tivemos a copa de 2014, crescimento da audiência, alcance dos eventos cobertos e presença nas regiões do Brasil. Entretanto, no inicio de 2015 passamos a ter problemas de performance, além do sentimento crescente que o projeto começara a ser difícil de manter e inchado.

Após um estudo detalhado dos problemas enfrentados concluímos que o modelo monolítico do projeto chegara ao seu limite, tanto em termos de manutenção de código quanto escalabilidade.

Passamos a considerar a adotar o modelo de arquitetura baseado em micro-serviços mas refletindo questões como:

Como integrar este novo modelo a base de código atual, que ainda será evoluída e mantida pelo nosso time e outro times
Como continuar utilizando de técnicas que achamos que funciona bem para um portal como o globoesporte.
Que oportunidades temos para evoluir a cultura de desenvolvimento aliada as melhores práticas de engenharia de software
Quais os riscos deste modelo ao processo de desenvolvimento
O projeto pan-americano 2015 surgiu como uma oportunidade de testar o modelo de micro-serviços e dirimir as questão levantadas.

Dentro do projeto pan, que inclui quadro de medalhas e agenda escolhemos refatorar totalmente a agenda de eventos dos jogos. De onde a base do código até então, em produção, não estava plenamente testada e os tempos de resposta das requisições estavam demasiadamente alto. Porém o fatos mais importante para a escolha foi que a agenda não dependia de outras partes do portal.

Durante o projeto agenda elaboramos um modelo de integração com a base de código atual, decidimos já no primeiro momento que o código só iria para produção usando continuos deployment após execução de testes automatizados e passar pelos critérios de qualidade de código no CI.

Como resultado o tempo dos serviços da agenda reduziram em 20x no tempo de resposta e o tempo para por o código em produção saiu de pelo menos uma hora para 5 minutos (tempo do ci rodar os testes e fazer deploy para todos os ambientes).

Outras questão surgiram que permanecem latentes, como: Como gerir a granularidade? Como difundir o conhecimento? Como documentar as integrações?

Por fim, achamos que o caminho para o globoesporte será em direção ao modelo de micro-serviços
