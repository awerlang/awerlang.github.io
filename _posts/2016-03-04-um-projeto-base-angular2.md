---
layout: post
title: Um projeto base para Angular 2
excerpt: "Disponibilizo um projeto inicial para o aprendizado do Angular 2."
modified: 2016-03-04
tags: [beta, angular 2, angular, projeto, desenvolvimento web, visão geral, ajuda]
categories: [angular]
comments: true
image:
  feature: angular2.png
  credit: Google
  creditlink: https://angular.io/
---

<section id="table-of-contents" class="toc">
  <header>
    <h3>Neste post</h3>
  </header>
  <div id="drawer" markdown="1">
  *  Auto generated table of contents
  {:toc}
  </div>
</section><!-- /#table-of-contents -->

> TL;DR; Disponibilizo um projeto inicial para o aprendizado do Angular 2.

Pouca coisa é tão frustrante durante o aprendizado de uma nova tecnologia quanto o tempo gasto na preparação de um ambiente para só então podermos praticar a tecnologia, não é mesmo?

O método que utilizo para aprender uma nova tecnologia envolve iterações de leituras e prática. A leitura para obter uma compreensão alto-nível, entender os conceitos, e a prática para comprovar como estes conceitos funcionam. Se percebo que estou travado na parte prática, busco mais leituras, e então repito a prática.   

Especialmente sobre a prática, o site oficial do Angular 2 dá uma força provendo ambientes preparados para a prática no aplicativo online [http://plnkr.co](http://plnkr.co). Mas este ambiente é bastante simples, voltado para a demonstração online.

Pensando nisso criei um repositório para construção de apps com Angular 2, mas totalmente offline. Assim ficamos mais próximos da infraestrutura de um app para produção, mas ainda simples o suficiente para experimentação.

Pretendo utilizar este projeto como base ao criar exemplos para os próximos posts deste blog.

Requisitos:

* Instalar todos as dependências localmente;
* Etapa de transpilação *offline*;

Em um projeto real, ainda iremos querer, no mínimo, otimizar o *deploy* usando uma ferramenta com o `jspm` ou `Webpack`, mas não será o caso deste repositório.

## Tecnologias utilizadas

Além é claro do próprio Angular 2, RxJS e vários *polyfills*, escolhi estas opções:

* SystemJS: usaremos em *runtime* para importar os módulos da aplicação;
* Typescript: transpilação em modo *watch* para ES5 na pasta `dist`;
* lite-server: disponibiliza o app num *endpoint* HTTP e atualiza a página automaticamente após alteração.

Para experimentação, eu sugiro que siga estas opções. Se quiser trocar o `lite-server` por outro, tudo bem, mas a motivação deste post/repositório neste momento é que queremos menos fricção, para facilitar o estudo. E estas escolhas funcionam bem em conjunto. Mais tarde, com um entendimento melhor das tecnologias, busque outras configurações. (No mínimo eu agregaria um `jspm`, e não, você não precisa do `gulp` pra nada disso.)

## Estrutura do repositório

Vá até [este repositório](https://github.com/awerlang/angular2-quick) no GitHub.

Para instalar na sua máquina, abra um prompt de comando numa pasta vazia e execute:

```
> git clone https://github.com/awerlang/angular2-quick.git
> cd angular2-quick
> npm install
```

> Qualquer erro proveniente do `node-gyp` pode ser ignorado.

No mesmo prompt, execute o comando abaixa para rodar a aplicação:

```
> npm start
```

Pronto, agora navegue até http://localhost:3000. Você deve estar visualizando a aplicação Tour of Heroes.

Esta é a estrutura do projeto:

```
 angular2-quickstart
 |
 +--app                     : pasta principal do app
 |  |
 |  +--heroes               : subpasta de exemplo
 |  |
 |  +--main.ts              : bootstrap do Angular 2
 |
 +--dist                    : arquivos gerados durante transpilação
 |
 +--node_modules            : dependências são instaladas aqui
 |
 +--typings                 : definições .d.ts para o TypeScript
 |
 +--index.html              : página inicial
 |
 +--package.json            : estamos instalando as dependências a partir do npm
 |
 +--tsconfig.json           : configuração do TypeScript
 |
 +--typings.json            : mapeamento das definições .d.ts
```

Por conveniência, este repositório vem com o código do tutorial `Tour of Heroes`. Para criar seu próprio código, crie uma nova pasta dentro da pasta `app`. Então em `app/main.ts` importe e atualize a chamada ao bootstrap com seu novo componente.  

Faça qualquer mudança no código. O navegador deverá ser atualizado automaticamente.

Caso você criou um arquivo .ts, pode ser necessário rodar o comando `npm start` novamente.
{: .notice} 

## Fechando a conta

Pronto! Repositório operante! Espero que facilite tua prática.

E não deixe de verificar as referências indicadas, para saber mais sobre as tecnologias.

## Referências

* [https://angular.io/docs/ts/latest/quickstart.html](https://angular.io/docs/ts/latest/quickstart.html)
* [https://angular.io/docs/ts/latest/guide/npm-packages.html](https://angular.io/docs/ts/latest/guide/npm-packages.html)
* [https://angular.io/docs/ts/latest/guide/typescript-configuration.html](https://angular.io/docs/ts/latest/guide/typescript-configuration.html)
* [https://github.com/mgechev/angular2-seed](https://github.com/mgechev/angular2-seed)
* [https://github.com/angularclass/angular2-webpack-starter](https://github.com/angularclass/angular2-webpack-starter)