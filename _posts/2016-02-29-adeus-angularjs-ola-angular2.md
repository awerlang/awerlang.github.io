---
layout: post
title: Adeus AngularJS, olá Angular 2
excerpt: "Confira o que tu precisa saber sobre a tão falada nova versão do Angular, antes de começar."
modified: 2016-02-29
tags: [angular2, beta, visão geral, iniciante, desenvolvimento web, aprendizado]
categories: [angular2]
comments: false
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

> TL;DR; Confira o que tu precisa saber sobre a tão falada nova versão do Angular, antes de começar.  

Há alguns dias iniciei a explorar a nova versão *major* do Angular, 
que entrou oficialmente [em beta](http://angularjs.blogspot.com.br/2015/12/angular-2-beta.html) mês passado. 
Conforme o anúncio, o time do Angular Core aguarda que os usuários
experimentem e coloquem em prática esta versão. Apesar de que quebras 
de compatibilidade devem ser esperadas até a versão *release*, os principais conceitos e 
sintaxes devem permanecer para a versão final.

> Angular 2, now in beta, is a next generation mobile and desktop application development platform.

É bem sútil, mas agora o framework está sendo chamado simplesmente de Angular. 
Percebeu? Caiu o sufixo JS! JavaScript é apenas 
uma das linguagens suportadas. Agora eu não vou ficar mais 
confuso sobre a forma de escrever o nome do *framework*.  

Possivelmente o principal alvoroço na comunidade trata-se das anunciadas
mudanças da próxima versão. Agora que estou me aprofundando no assunto,
posso compreender este sentimento. Obviamente que a migração não se trata de 
um *find/replace* automatizado, eu recomendo revisar a aplicação. Comento sobre isto um 
pouco mais, adiante.

# O que há de novo?

* Símbolos nos templates (`[] () * #`) conforme a intenção da *binding*
* Pipes
* Decorators (ES.next)
* Estilos encapsulados (shadow DOM)
* Eventos de ciclo de vida
* Detecção de mudança configurável e invocável
* Novo Router
* Rápido por padrão!
* RxJS' Observable integrado "de fábrica"

O Angular 2 aproveita melhor recursos nativos da web mas tomou a 
decisão de integrar uma biblioteca externa, a RxJS. Ela fornece
suporte a programação reativa que te permite ser mais produtivo
ao compor fluxos de dados assíncronos. Quer uma introdução rápida 
ao tema? Confere [estes slides](http://blog.werlangtecnologia.com.br/talks/slides/frp/) e [estes exemplos](https://github.com/awerlang/rxjs-samples) de uma talk recente 
minha. 

O intuito deste *post* ainda não é começar a programar, mas não 
poderíamos ficar sem um snippet do novo código.

```js
import { Component, OnInit } from 'angular2/core';
import { Router } from 'angular2/router';

import { Hero } from './hero';
import { HeroService } from './hero.service';

@Component({
  selector: 'my-dashboard',
  template: `
    <h3>Top Heroes</h3>
    <div class="grid grid-pad">
        <div *ngFor="#hero of heroes" (click)="gotoDetail(hero)" class="col-1-4" >
            <div class="module hero">
                <h4>{{hero.name}}</h4>
            </div>
        </div>
    </div>`,
  styleUrls: ['app/dashboard.component.css']
})
export class DashboardComponent implements OnInit {

  heroes: Hero[] = [];

  constructor(
    private _router: Router,
    private _heroService: HeroService) {
  }

  ngOnInit() {
    this._heroService.getHeroes()
      .then(heroes => this.heroes = heroes.slice(1,5));
  }

  gotoDetail(hero: Hero) {
    let link = ['HeroDetail', { id: hero.id }];
    this._router.navigate(link);
  }
}
```

# O que ficou?

Vamos identificar na nova versão os principais conceitos consagrados
na versão precursora. Buscamos no AngularJS um ambiente que combinasse
produtividade e testabilidade, e estes são conceitos que permanecem,
talvez até mais fortes. 

Estes conceitos, apesar de terem passados por mudanças de sintaxe e 
semântica, devem ser assimilados facilmente.

* Templates HTML
  * separados do código JavaScript
  * Expressões
  * Estruturas de controle
* Forms
  * *Two-way data binding*
* Serviços
* Injeção de dependência
* *Plain-old-JavaScript-object* como *model*
* Testabilidade

Outros conceitos remanescentes da versão anterior estão presentes 
mas devido a uma mudança mais profunda vamos ver na próxima seção.

# O que mudou?

O Angular 2 foi projetado para a web e navegadores do futuro. 
Isto significa que algumas coisas ainda não tem suporte nativo e 
precisamos carregar shims e polyfills conforme nossa necessidade. 
Vamos usar transpiladores se quisermos lançar mão de uma sintaxe 
mais enxuta que o oferecido pelo ES5.

E o que isto representa pra nós? Vamos pensar menos em angularizar
componentes e bibliotecas, vamos pensar em desenvolver em JavaScript.
Pelo que entendi e vi até aqui, até bibliotecas e componentes criadas 
fora do ecosistema Angular poderão ser aproveitadas mais facilmente. 
Menos reescritas de componentes como vimos acontecer com o Bootstrap
e dezenas de componentes. Se isto se concretizar será bom, não é?

É bem possível que no primeiro dia você já consiga estar desenvolvendo
em Angular 2, mas algumas funcionalidades podem te enganar. Podemos elencar:

* Forms
* Detecção e propagação de mudança nos dados 

# O que saiu? 

Alguns recursos foram descontinuados. No Angular 2, substituíremos
por recursos nativos, ou através de novos conceitos na versão.
Em algumas situações não temos um substituto imediato ou previsto, 
neste caso teremos que construir nós mesmos ou utilizar algum projeto
da comunidade.

* Conceitos
  * directive definition object (link, transclude, priority, required, controllerAs, etc.)
  * controller
  * scope ($watch, $apply, events)
  * module
  * fases config() / run()
  * ciclo $digest
  * decorator
  * provider
* Serviços:
  * $promise
  * $timeout
  * $scope / $rootScope
  * $resource
  * $log
* Diretivas
  * ng-app
  * ng-controller
  * ng-include
  * ng-options
  * ng-messages
  * todas as diretivas de eventos DOM
* Outros
  * funções utilitárias no objeto angular
  * jqLite (versão reduzida do jQuery)

# Por onde começar?

Eu sugiro começar desenvolvendo com TypeScript. Mesmo que tu 
prefira JavaScript, e sendo uma linguagem suportada nativamente
pelo Angular, aqui vai a má notícia: a documentação neste momento 
é incompleta para TypeScript, mas é não-existente para JavaScript.

Já vimos muitos devs reclamando sobre a situação da documentação nos 
primórdios do AngularJS. Esta lição foi aprendida, mas ainda estamos 
em beta. O público-alvo desta versão deve estar mais acostumado com 
estas condições. Além disso, a comunidade já é muito maior e haverá 
mais gente blogando, assim mais conteúdo vai haver disponível.

Com o ambiente preparado, faça o [quickstart](https://angular.io/docs/ts/latest/quickstart.html) de (um pouco mais de) 5 minutos.

Na sequência passe para o [tutorial](https://angular.io/docs/ts/latest/tutorial/), onde uma pequena aplicação é criada passo a passo.
Não tem como fugir do *copy-paste*, então eu sugiro que tu pule a parte prática, pois ela 
apenas vai te tomar tempo. É suficiente acompanhar as etapas e entender o que está sendo
tratado, e apenas no final, se te interessar, crie um *plunker* com
a implementação final.

E favorite o [cheatsheet](https://angular.io/docs/ts/latest/guide/cheatsheet.html)!

# Posso começar um novo projeto em Angular 2?

Sim.

# C'mon.

Certo. Na verdade a pergunta não é bem esta. 

A pergunta é: Quando começar um novo projeto em Angular 2?

A pergunta dramática seria: Começo um projeto novo em Angular 1 ou Angular 2?

Apesar do AngularJS 1.x permanecer conosco por mais algum tempo, e ainda 
ser a versão atualmente recomendada para uso, como ocorre fatalmente com 
toda tecnologia, ela não receberá inovações.

Além do mais, Angular 2 pode não estar "pronto" conforme a tua expectativa,
mas é uma versão não apenas posterior, mas é melhor de várias formas que 
a versão anterior. Eu estou gostando muito do que estou experimentando 
~~apesar de estar quebrando a cabeça com a propagação de mudanças em dados~~.

Então, resumindo, aguarde conforme tuas possibilidades, mas fique 
à vontade para se apropriar da nova versão.     

# Posso migrar um projeto baseado em Angular 1?

Eu estou avaliando esta possibilidade.

A situação melhorou drasticamente desde fins de 2014. Existem 
basicamente 2 soluções abençoadas pela equipe Angular para facilitar
o processo. 

O método *ngUpgrade* permite alternar camadas das versões 1 e 2 
na aplicação. Nos componentes do Angular 2, pode invocar serviços 
do AngularJS 1. Componentes do AngularJS 1 podem utilizar componentes do Angular 2 nos *templates*, e vice-versa. Legal né?

Já o *ngForward* permite usar alguma sintaxe do Angular 2 ao construir sobre o AngularJS 1.x.
Bobagem! Uma perda de tempo que não vai te ajudar significativamente. 
 
Como eu não devo utilizar nenhuma das duas, irei apenas 
compartilhar [este link](https://angular.io/docs/ts/latest/guide/upgrade.html) 
e [este também](https://github.com/ngUpgraders/ng-forward) para elas.

E claro, permanecer na versão 1.x também é uma opção, para aplicações
em estágio de manutenção isto é aceitável.

Em caso de querer aproveitar novos recursos como pré-renderização 
no servidor, processamento em *webworkers*, e renderização nativa, 
estes são argumentos para justificar a migração.

Caso decida reescrever, saiba que a implementação de serviços será
mais facilmente aproveitada. Terá que revisar todos os templates HTML
devido a introdução de uma nova sintaxe para *data binding*.

Terá mais trabalho para migrar controllers e diretivas, pois estas
foram unificadas em torno do conceito de componente. 

Espere ainda mais mais caso tenha optado pela má prática de utilizar `$scope`.

# Onde encontrar documentação e ajuda?

* Guides: [https://angular.io/docs/ts/latest/guide/](https://angular.io/docs/ts/latest/guide/)
* API (preview): [https://angular.io/docs/ts/latest/api/](https://angular.io/docs/ts/latest/api/)
* Chat: [https://gitter.im/angular/angular](https://gitter.im/angular/angular)
* Q&A: [http://stackoverflow.com/questions/tagged/angular2](http://stackoverflow.com/questions/tagged/angular2)
* Bugs, etc: [https://github.com/angular/angular/issues](https://github.com/angular/angular/issues)

# Fechando a conta

Bueno, espero que eu tenha trazido informações úteis pra ti. 
Se puder, deixa um feedback.

Em breve retorno com mais descobertas. Vou descrever as diferenças 
na prática, pra quem está vindo do AngularJS 1.x.
Fica ligado!