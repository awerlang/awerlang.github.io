---
layout: post
title: Reescrevendo a historia com git rebase 
excerpt: ""
modified: 2015-05-26
tags: [git, github]
categories: [git, stories]
comments: true
draft: true
---

* Introdução
  * Problematização
* Desenvolvimento
  * git rebase
  * git push --force
* Conclusão

## Introdução

O Git, além de versátil, é uma ferramenta extremamente inovativa na sua concepção.
Para projetos open-source é premissa básica, pois o workflow baseado em pull requests
do GitHub permite a qualquer pessoa, integrante do projeto ou não, contribuir para um
projeto, sem que haja uma coordenação ou autorização prévia.

Diferentemente de ferramentas de controle de versão centralizadas, um commit ocorre em 
um repositório local. Ele pode ser então replicado (*pushed*) para um repositório remoto
como o GitHub.

Estes recursos não vem sem gotchas. Pra quem vem de scm centralizado (svn, tfs) como eu,
tenho que tomar alguns cuidados que não estava habituado. 

Hoje, ao fazer meu push diário para o GitHub, criei um pequeno problema na linearidade 
no histórico em um dos meus projetos e aproveitei para praticar um recurso do git
que até então não havia tido necessidade.

Fiz a alteração e rodei um `git commit`.

Próxima etapa, enviar para o GitHub:

    git push origin master
    
Neste momento acusou que o topo do *remote* não estava na minha versão local. Futuramente, é neste ponto terei que tomar uma ação diferente para evitar retrabalho.

Meu erro começou nesta inocente sequência:

{% highlight batch %}
git pull origin master
git push origin master
{% endhighlight %}

GitHub atualizado e mostrando os commits que fiz. Com o detalhe que aparece um commit "merge" que eu não criei, não diretamente ao menos. Neste commit de merge, estão registrados 2 commit's como *parent*, um deles que fiz diretamente na interface do GitHub, e outro paralelo a este no repositório local.

Quem acrescentou este commit extra foi o `git pull`, um merge simples que já estamos habituados a ver por aí.

## git rebase

    git pull --rebase origin master

Simples. Isto pega todos os commits que só existem no remote e copia para o local. Antes disto salva os commits locais para uma área temporária e depois replica um a um sobre o último commit que veio do remote. Tudo isto localmente.

## Limpando a bagunça no remote

Preciso remover o commit de merge que publiquei acidentalmente no remote. Para isto existe o `git push --force`. Muitos desprezam o uso do --force, com certa razão, embora eu vejo que se a ferramenta está disponível é porque ela tem sua utilidade. Na sequência vou comentar sobre o efeito colateral disto.

Na situação que algum outro dev baseou seu próprio desenvolvimento sobre um commit publicado no remote, e este commit some sem deixar rastro, este desenvolvedor terá dificuldade. Eu mesmo não sei se um simples rebase resolveria, nem espero passar por isto, mas no mínimo seria algum trabalho para cada desenvolvedor afetado.

<figure>
  <img src>
  <figcaption>Como aprendemos no cartoon, basta não olhar pra baixo</figcaption>
</figure>

Mesmo que a intenção seja mudar a mensagem do commit, lembre-se que para o git isto nãofaz diferença. A mensagem, juntamente com os arquivos são a base para um hash que identifica unicamente o commit. Esta é a natureza distribuída do git.

No meu caso, sei que o impacto da alteração é zero (nenhuma fork do projeto).

## git push --force

{% highlight batch %}
git commit
git push origin master
git pull origin master
git push origin master

git checkout -b rebase
git checkout master
git reset --hard HEAD~1
git push --force origin master

git pull --rebase origin master
{% endhighlight %}
    
## Fechando

Mesmo com o incidente de hoje, o Git me disponibilizou uma ferramenta que me permitir realizar algo
que jamais foi possível fazer em outra ferramenta, então é uma lição aprendida e um dia salvo.

Também, meu aprendizado no Git tem sido gradual. Não "parei" para aprender tudo, estou mapeando mentalmente meus conhecimentos de scm centralizado para o scm distribuído. Embora é possível fazer desta forma, tenho que saber sair destas situações que ocorrem. Para quem quer começar certo, o [Pro Git](https://git-scm.com/book/en/v2) é recomendado, foi traduzido parcialmente para o português, inclusive.

