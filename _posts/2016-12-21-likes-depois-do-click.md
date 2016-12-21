---
layout: post
title: Likes, depois do click
excerpt: "Como você implementaria um simples like na sua aplicação?"
modified: 2016-12-21
tags: [user experience, databases, distributed systems, api, web development]
categories: [design]
comments: true
---

> Como você implementaria um simples like na sua aplicação?

Eis uma forma:

```js
app.post('/like', async (req, res) => {
    const user = req.user.id
    const article = req.body.article
    if (await db.get({article, user})) {
        await db.delete({article, user})
    } else {
        await db.put({article, user})
    }
    res.send()
})
```

(Não se assuste com as palavras _async_ e _await_ aqui, são apenas _promises_ .)


Qual o resultado desta operação? Quer dar um palpite?

Se você achou isto parecido com o comportamento de um _checkbox_ (ou _toggle_), você provavelmente acertou a intenção 100% das vezes de quem escreve este tipo de código. **Mas dificilmente a intenção do usuário é inverter o estado atual.** Ele quer marcar. Ou desmarcar. Se o estado futuro é igual ao estado atual, ele não quer inverter.

Alguns problemas desta abordagem:

* A _API_ não carrega a intenção do usuário;
* Não apresenta idempotência;
* Uso dos recursos de banco de dados é ineficiente;
* Ignora efeitos do ambiente distribuído.

O objetivo deste artigo é demonstrar porque este conceito (_toggle_) deve ser desconsiderado quando se trata de uma operação assíncrona. **Apesar de simples, é baseado em pressupostos demais.** Apresento uma solução mais robusta e devidamente justificada.

---

Sem mais delongas, vamos resolver isto da seguinte forma:

```js
app.post('/like', async (req, res) => {
    const user = req.user.id
    const article = req.body.article
    await db.put({article, user})
    res.send()
})

app.post('/unlike', async (req, res) => {
    const user = req.user.id
    const article = req.body.article
    await db.delete({article, user})
    res.send()
})
```

Nós fizemos o seguinte:

* Separamos a operação em dois _endpoints_, `/like` e `/unlike`, garantindo assim a intenção do usuário;
* Em cada _endpoint_, apenas uma operação de banco de dados, atômica e idempotente, é executada.

Note que não há mais condicionais nesta implementação. E com estas 2 simples medidas, atendemos aos 4 pontos que motivaram uma implementação melhor.

Mas isto apenas transfere o controle para o front end, você argumenta. Eu concordo. E vejo como uma forma adequada para preservar a intenção do usuário. O servidor não mantém estado e não sabe qual a saída esperada. E o banco de dados só armazena o último dado que você enviou. Já o front end é o que se apresenta ao usuário, e o usuário toma ações baseado no que vê nesta apresentação.

## Sobre APIs

Estamos implementando uma API que será consumida por uma aplicação _web_ ou _mobile_. Não é uma API consumida por outro back end. Seguir um padrão REST fielmente não é o ideal. Neste caso em questão, a necessidade do cliente (front end) é superior a qualquer ideia que a gente tenha sobre como se implementa um API. Se o front end precisar de um campo a mais para evitar uma chamada secundária até a API, que assim seja!

Logo, esta API deve refletir as operações disponíveis no front end. O front end não ficará mais complicado tendo que escolher entre duas operações distintas, afinal ele tem o conhecimento do estado e sabe qual chamar. E ainda temos o benefício de poder medir qual intenção do usuário está sendo mais frequente (_like_ ou _unlike_), se for desejado.

Por último, torna possível executar a operação com maior eficiência no banco de dados.

## Sobre Banco de Dados

Quando falamos de banco de dados, estamos falando em latência, eficiência, e escalabilidade, entre outros fatores. Não raro, o gargalo da aplicação vive neste componente. Quando chega aqui, não tem o que fazer. Ou tem?

Bancos de dados relacionais (SGDBs) dão suporte a transações. Elas são atômicas, consistentes, isoladas e duráveis. Por isoladas (as demais características não importam no momento), quer dizer que não é observável o efeito da execução concorrente das transações. É como se cada transação executasse complementamente antes da próxima começar. Para isso o SGDB coloca _locks_ em registros escritos durante a transação. Mas lembra-se que no primeiro trecho de código começamos com uma operação de leitura? Ainda assim nestes SGBDs é possível solicitar que a leitura coloque um _lock_ no registro. Outro processo efetuando uma leitura na tabela/registro precisará aguardar a conclusão da transação que mantém o _lock_. Isto pode levar vários segundos e até minutos! Imagine o impacto no desempenho global do sistema, considerando todos os usuários concorrentes. E transações não estão disponíveis em um banco de dados NoSQL.

A execução do banco de dados é sequencial? Vejamos. Uma única instância de banco de dados pode ter a capacidade de executar mais de uma operação simultaneamente. O servidor pode ter mais de um _core_, os dados já estão em _cache_, as requisições são processadas em paralelo em diferentes estágios da _pipeline_ (analisar a consulta, E/S, processar). E principalmente, mesmo com transação, o banco de dados não está impedido de executar uma consulta de outro processo, mesmo antes de retornar um resultado em andamento. Salvo a existência de _locks_, o banco de dados irá paralelizar a execução dos comandos.

Ainda há o cenário de _sharding_ ou consistência eventual, mecanismos lançados para proporcionar escalabilidade e disponibilidade, mas onde a leitura pode ser efetuada em uma versão obsoleta dos dados. Para evitar isto é necessário ativar o modo de consistência robusta, menos performática, pois executa a operação em ao menos dois nodes no servidor.

**Estou usando este exemplo simples para demostrar conceitos**. Claro que neste caso específico, tão simples, a implementação original pode passar sem problemas. O conceito que eu quero transmitir é que, **podendo fazer melhor, o que nos impede?** Por quê ficar preso a uma estrutura menos eficiente?

Reduzindo o número de operações de banco de dados pela metade, temos um ganho de desempenho de 50%, por assim dizer. E ainda evitamos cenários de execução entrelaçada e desvantagens do uso de transações.

## Sobre User Experience

Crie um ambiente mais seguro para o usuário. Se o botão de like for clicado, e algo demorar para acontecer, é possível que o usuário insista e clique novamente. Isto é física pura, é a latência agindo e o usuário reagindo. Isto é uma UX ruim. Para evitar isto se costuma criar um _spinner_, bloqueando ou não novas ações por parte do usuário. É um avanço mas é possível fazer ainda melhor em alguns casos.

Então você, no front end, decide implementar o padrão idiomático _optimistic updates_:

```js
like() {
    setState({like: true})
    try {
        await post('/like')
    } catch (e) {
        setState({like: false})
    }
}

unlike() {
    setState({like: false})
    try {
        await post('/unlike')
    } catch (e) {
        setState({like: true})
    }
}

onLikeClick() {
    getState().like ? unlike() : like()
}
```

Este padrão consiste em refletir em tela como seria o resultado da operação, mesmo antes dela ser iniciada. Bacana hein?! Isto inverte a lógica pessismista (ou realista) que precisamos esperar pelo término da operação. Você sabe que isto remove o efeito da latência. Não somente da latência, mas o custo da operação completa. E se dá por satisfeito. Não esqueça de tratar apropriadamente em caso de insucesso, ainda que improvável.
Eu uso e recomendo este padrão, mas agora precisamos falar sobre idempotência e as mentiras que te contaram sobre programação de sistemas distribuídos.


---

# Sobre o Ambiente

As 8 falácias da computação distribuída (_Fallacies of distributed computing_), catalogadas inicialmente por Peter Deutsch, eram 7 em 1994 e conhecidas como sendo 8 desde 1997 após um acréscimo feito por James Gosling, tratam de pressupostos, falsos, que são ótimas causas de dor de cabeça quando se leva a aplicação pra produção.

São elas:

* A rede é confiável;
* A latência é zero;
* A banda é infinita;
* A rede é segura;
* A topologia não muda;
* Existe um administrador;
* O custo de transporte é zero;
* A rede é homogênea.

Aqui, nos interessam apenas os 2 primeiros itens.

## A latência é zero.

O usuário, impaciente, ou até involuntariamente, clica 2 ou mais vezes no botão de like. Se o rótulo ou ícone do botão alterna conforme o estado, não há dúvida aqui, se o usuário percebe que há um like e clica para desfazê-lo, o like deve ser removido.

A latência pode ser inócua do ponto de vista do usuário, com a atualização precoce do estado da tela, mas ela ainda existe. O tempo de execução de um comando no banco de dados pode resultar em diferença no resultado quando há entrelaçamento de operações, como vimos antes.

É possível alcançar este resultado implementando um _toggle_?

> Se estiver marcado, então desmarque; caso contrário, então marque.

Na execução serial das requisições, ou seja, processar completamente uma requisição após a outra, a segunda reverte o efeito da primeira.

Na situação da execução em paralelo, onde uma leitura ocorre antes da escrita anterior em outro processo concluir, **50% das vezes** será como se apenas uma única requisição foi posta, e pode até resultar em likes duplicados. Eu não gosto que meu software seja imprevisível desta forma.

Você não quer depender de fatores que você não pode controlar. Alguns fatores que você não tem controle:

* A ordem de execução de comandos de processos distintos no banco de dados;
* Que ambas operações irão resultar ou em sucesso, ou em erro;
* A notificação da execução com sucesso da requisição: um timeout pode ocorrer mesmo com a operação completada.

## A rede é confiável.

Por isso, a caraterística da idempotência é fundamental. Se houve mais de uma execução, o efeito desejado foi atingido. Se houve algum erro, repita a operação. Mesmo que a operação teve êxito e o erro ocorreu entre o front end e a API, repetir a operação não terá efeito adverso. Assim, definimos _idempotência como a capacidade da operação ter o mesmo efeito independente de quantas vezes ela foi invocada em duplicidade_.


---

Agora você já está convencido de separar suas APIs conforme a intenção. Vamos com calma. Este caminho também tem suas nuances. Você precisa de um mecanismo que determine a ordem das requisições. Não é porque uma requisição saiu primeiro que ela chegará primeiro. Aí você já pensa em voltar pro _toggle_. Mas lembre que com _toggle_, você precisa garantir isolamento na operação, algo que teu banco NoSQL não fornece, e o banco SGDB reduz teu desempenho. Abordarei soluções para sequenciamento em um artigo futuro.

Referências:

* The Eight Fallacies of Distributed Computing
* Optimistic Updates



---

Espero que tenha valido a pena ler a até aqui! Eu gostei de poder escrever sobre isso, eu curto muito quando diferentes habilidades são necessárias para resolver um problema e aqui encontrei um probleminha bem propício. E mantenha seus toggles apenas nos checkboxes.

Feedbacks serão muito bem-vindos! Desenvolvi os conceitos demais? De menos? O conteúdo é relevante? Aguardo teu comentário.

Ah e se gostou, dá um like!

Agradecimentos: aos membros do grupo nodebr no Slack, em particular ao joaoarau que trouxe este debate.