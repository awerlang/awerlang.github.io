---
layout: post
title: Primeiras impressões no Angular 2
excerpt: "Algumas diferenças notáveis que nos deparamos na nova versão. Conhecer estes tópicos de antemão pode ser a diferença entre ficar frustrado e aproveitar melhor os novos recursos."
modified: 2016-03-02
tags: [beta, angular 2, angular, migração, desenvolvimento web, visão geral, ajuda]
categories: [angular]
comments: false
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

> TL;DR; Algumas diferenças notáveis que nos deparamos na nova versão. Conhecer estes tópicos de antemão pode ser a diferença entre ficar frustrado e aproveitar melhor os novos recursos.

Vou registrar aqui as impressões que tive nos primeiros contatos com a nova versão, o Angular 2. Boa parte desta lista é fruto dos dois primeiros dias de estudo. Aqui estou listando algumas surpresas para quem vem do AngularJS 1.x, que em função da semelhança podem nos enganar. Vou tentar responder às principais questões que tu irá te perguntar.

{: .notice} O Angular 2 está em *beta*, e algumas observações aqui podem ficar obsoletas até a *release*

## Nova sintaxe para bindings de propriedades e eventos

Para fazer binding de propriedades e eventos usamos uma sintaxe que a princípio parece esquisita, mas fará sentido e iremos nos habituar facilmente. Usaremos `[]`, `()` e `[()]`. 

```
<input [propriedade]="expressao" [(ngModel)]="dataField" (nomeDoEvento)="funcao($event)">
```

O uso do par `[]` atribui o resultado da expressão para a) uma propriedade do componente (se houver), ou b) uma propriedade do elemento HTML. Diferentemente do AngularJS 1.x, por padrão não utilizaremos atributos, mas as propriedades diretamente. Lembre que pode haver diferenças na escrita, como no caso do atributo `readonly` e propriedade `readOnly`.

Já o par `()` conecta *statements* a eventos. Para eventos HTML ocorre *bubbling*, já eventos sintéticos, com `EventEmitter`, são capturados apenas no próprio elemento.

Em alguns eventos poderemos executar o *statement* condicionalmente:

```
<input (keyup.enter)="save()">
```

A sintaxe para *two-way binding* também pode ser feita na seguinte convenção:

```
<input [ngModel]="dataField" (ngModelChange)="dataField = $event">
```

Para definir atributos no elementos a sintaxe é a tradicional `atributo="string"`, e alguns componentes do angular exigem esta forma, como o `ngControl`.

Vale lembrar que agora o Angular possui um parser HTML próprio, com isso, cai a convenção de usar hífens (*kebab case*) e passa a ser sensível a maiúsculas/minúsculas. 

## Nova sintaxe para diretivas do core

Isto inclui ng-if, ng-repeat e ng-switch. Melhor dizendo, ngIf, ngFor e ngSwitch. Há duas formas de utilizar estas diretivas. A mais simples é apenas *syntactic sugar*:

```
<tr *ngFor="#item of lista | pipe"><td>{{item}}</td></tr>
```

Depois de errar algumas vezes, tu vai começar a usar a palavra-chave `of` invés de `in`. Esta sintaxe é mais próxima da estrutura `for-of` do JavaScript.

A outra sintaxe envolve usar o elemento `<template>` ([link](https://angular.io/docs/ts/latest/guide/template-syntax.html#!#star-template)).

Não está disponível o uso de ngIf e ngFor no [mesmo elemento](https://github.com/angular/angular/issues/4792#issuecomment-149282241). 

## CSS embarcado

No decorator `Component` é possível passar através de `styles: string[]` ou `styleUrls: string[]` CSS que deve ser aplicado apenas no componente em questão, sem vazar estilos para outros elements.

## Avaliação de expressões rigorosa

A semântica da interpretação das *template expressions* está mais próxima do JavaScript. Na expressão `user.name`, caso `user` seja `null` ou `undefined` o Angular vai disparar um erro.

## Operador Elvis ?.

Algumas vezes é conveniente avaliar expressões que possam conter `null` e `undefined` no caminho de avaliação. Nestas situações podemos usar o [operador Elvis](https://angular.io/docs/ts/latest/guide/template-syntax.html#!#expression-operators) `?.` para cortar o circuito, como ocorre no AngularJS 1.x. Lembre que às vezes é melhor usar um `ngIf`.

## Declaração de diretivas do template

Agora passa a ser necessário declarar na metadata do componente quais diretivas são utilizadas no template do mesmo. Eu vejo como uma boa solução para evitar conflitos em componentes de mesmo nome em bibliotecas distintas.

```
@Component({
    selector: 'cmp',
    directives: [MinhaDiretiva]
})
```

Diretivas mantidas pelo projeto principal do Angular já são disponíveis por padrão para todos os componentes. É possível também adicionar novas diretivas padrão através do *token* `PLATFORM_DIRECTIVES`.

## Serviços associados à instância do componente

De forma semelhante às diretivas, também iremos declarar os providers/serviços para injetar no construtor do componente.  A diferença é que é possível injetar serviços em um elemento ancestral e consumir em um descendente. Isto porque agora os serviços não são mais *singleton*, e sim tem seu ciclo de vida associado a *component tree*. É óbvio que preciso dedicar um post próprio para isto, pois torna possível fazer muita coisa bacana. 

## Eventos de lifecycle no componente

Os componentes podem implementar métodos para o seu * lifecycle*. De certa forma substitui o `link` das diretivas do AngularJS 1.x e evento `$destroy` do serviço `$scope. Os nomes dos métodos são predefinidos. vejamos alguns:

```
class Componente {
    constructor() {
        // inicializar variáveis simples
    }
    
    ngOnInit() {
        // bindings já estarão amarradas
        // inicializar processos mais complexos
        // ajax, etc
    }

    ngOnDestroy() {
        // liberar recursos
    }
}
```

## Promise nativa

O novo Angular não vem com uma implementação para *promises*. O substituto imediato é o construtor `Promise` nativo do ES2015. Uma diferença é que este não suporta notificação de progresso, o terceiro parâmetro no método `$q.when(...).then(resolveFn, rejectFn, notifyFn)`. É possível também que não seja tão usada, pois nenhum componente ou serviço do Angular retorna ou consome uma promise diretamente.

```
const p = new Promise((resolve, reject) => {
    resolve() || reject(new Error());
});
return p;
``` 

## Abordagem de construtores

O Angular força a utilização de classes ou funções construtoras, isto é, tudo que é instanciado via `new Funcao()`. Não iremos injectar funções simples e retornar objetos dentro de uma *closure*.

## Sem mais controllerAs

Componentes (controllers) não podem mais ser publicados através de um nome (`vm`, `ctrl`) no contexto. Componentes agora **são o contexto**. Só isso já resolve todos os problemas causados pela herança de `$scope` no AngularJS 1.x.

## Acessar dados de controllers superiores na view

Não faça isso, se é que for possível. Utilize propriedades e eventos em componentes especializados.

## Event emitters invés de bindings &

No lugar das bindings `&` nas *directive definition objects*, temos eventos e `EventEmitter`'s. Eles são instâncias e tem uma interface bem definida (métodos `emit()` e `subscribe()`), invés de uma função que era chamada pela diretiva. 

## angular.decorator()'s

Já era.

## Variável local ao template (#)

É possível inserir variáveis locais no template com o prefixo `#`, ou ainda `var-`. Temos 3 formas de uso: como variável de iteração (`local1`), a instância de `HTMLElement` (`local2`) e o componente vinculado ao elemento (`local3`). 

```
<div *ngFor="#local1 of items">{{local1}}</div>
<input #local2 (input)="send(local2.value)">
<form #local3="ngForm"></form>
<button [disabled]="!local3.form.valid">
```

## ngModel e ngControl divididos

A diretiva `ngModel` foi reduzida em funcionalidade e serve apenas para *two-way binding*. Não há formatters nem parsers no momento e é possível que não teremos isto.
A `ngControl` passa a manter o status do controle.

```
<input [(ngModel)]="model" ngControl="name">
``` 

## $timeout -> setTimeout

Os serviços `$timeout` e `$interval` também não existem mais. Use alternativas nativas. Sem precisar chamar `$scope.$apply()`, porque este também é passado.

## OpaqueToken para DI

Apesar de associarmos serviços pelo próprio identificador, também podemos registrar strings com serviços. Este 2 modos são unificados atráves do `OpaqueToken`, que também pode ser utilizado diretamente.

## Sem mais $scope.$on, $broadcast(), etc.

Já foi tarde.

## Menos diretivas na infraestrutura, mais expressividade

Se você procurar por diretivas ng-click, ng-mousemove, ng-keydown, e tantas outras mais, não irá encontrar. Na versão anterior a definição destas diretivas no *core* era puro *boilerplate* que a nova sintaxe veio pra remover. 

Da mesma forma, não temos tantas diretivas para alterar propriedades dos componentes. `ng-show`, `ng-hide`, etc., também são desnecessárias.

```
<div [hidden]="!temErro">Mensagem.</div> 
```

Cuidado com esta substituição (propriedade `hidden`), pois se aplicado em algum componente com um estilo `display`, este tem precedência, e o elemento não será ocultado.

## input[type=date]

Até o momento o `input[type=date]` somente aceita string no formato yyyy-MM-dd. O que é um pouco estranho pois `valueAsDate` retorna um `Date`.

# Http

O suporte no serviço `Http` é beeeem simples. E neste momento esta passando por um refactoring, embora a interface deve permanecer a mesma.

* Não suporta JSON, transforme com `JSON.stringify()` antes de passar o body;
* Configure o header ContentType como necessário (ex. application/json);
* Disponibiliza `response.json()`;
* Não há suporte para interceptors.

Mas o mais importante para saber mesmo, é que Angular empacota a biblioteca [RxJS 5](https://github.com/ReactiveX/RxJS) como base de uma programação reativa. Vale a pena estudar a técnica, pois aumenta a produtividade na programação assíncrona. Sequências, repeticação, consumo em templates. Tudo muito melhorado.

Minha sugestão é encapsular no seu próprio serviço `Api`, para evitar impacto de melhorias futuras.

# Fechando a conta

Ontem também foi publicado um artigo novo nos docs oficiais. Se trata de uma [referência](https://angular.io/docs/ts/latest/cookbook/a1-a2-quick-reference.html) de sintaxe para migração a partir do Angular 1.

Com o tempo vou abordar alguns itens da lista acima com maior profundidade. E também há muito mais para tratar, como formulários, detecção de mudança, injeção de dependência, padrões de componentização, navegação, observables, testes. 

Acompanhe! Ah, e se quiser contribuir com esta lista, deixe um comentário!