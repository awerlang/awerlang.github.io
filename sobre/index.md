---
layout: page
title: Sobre
excerpt: "Informações sobre o autor do site."
tags: [about]
comments: false
image:
  feature: sample-image-1.jpg
  credit: WeGraphics
  creditlink: http://wegraphics.net/downloads/free-ultimate-blurred-background-pack/
---

## Quem eu sou

Sou um desenvolver de software baseado na grande Porto Alegre, na cidade de São Leopoldo, RS &ndash; Brasil.
Desde outubro/2014 atuo como consultor independente. Caso queira contar com minha experiência no seu projeto,
entre em contato comigo.

## Agenda

<div>
  <style>
    .agenda {
        position: relative;
        width: 25px;
        height: 25px;
        display: inline-block;
        background-color: lightgray;
        text-align: center;
        vertical-align: middle;
        font-weight: bold;
        cursor: default;
    }
    
    .agenda.current {
        border: solid 3px black;
    }
    
    .agenda.limited {
        background-color: coral;
    }
    
    .agenda.available {
        background-color: chartreuse;
    }
    
    .agenda.blocked {
        background-color: firebrick;
        color: white;
    }
    
    .agenda:hover::after {
        position: absolute;
        top: 30px;
        left: 0px;
        padding: 2px;
    }
    
    .agenda.limited:hover::after {
        content: "Parcial";
        background-color: coral;
    }
    
    .agenda.available:hover::after {
        content: "Disponível";
        background-color: chartreuse;
    }
    
    .agenda.blocked:hover::after {
        content: "Indisponível";
        background-color: firebrick;
        color: white;
    }
  </style>
  <div class="agenda">J</div>
  <div class="agenda">F</div>
  <div class="agenda">M</div>
  <div class="agenda">A</div>
  <div class="agenda">M</div>
  <div class="agenda">J</div>
  <div class="agenda limited">J</div>
  <div class="agenda limited">A</div>
  <div class="agenda limited">S</div>
  <div class="agenda limited">O</div>
  <div class="agenda limited">N</div>
  <div class="agenda limited">D</div>
  
  <script type="text/javascript">
    var agenda = document.querySelectorAll('.agenda'),
        mesAtual = new Date().getMonth();
        
    Array.prototype.slice.call(agenda, 0, mesAtual).forEach(function (item) {
        item.className = 'agenda';
    });
    agenda[mesAtual].classList.add('current');
  </script>
</div>

## O que eu faço

Atuo em projetos web, mobile, e sistemas que demandem alta escalabilidade.
Gosto de contribuir em todas as etapas do desenvolvimento do produto, da visão até a implementação e
go-live.

Caso seu projeto já esteja em andamento e precise de um olhar de um especialista, também posso encontrar
uma forma de ajudar no projeto.

Aqui neste site também escrevo minhas ideias sobre desenvolvimento de produtos de software.

Além disso, contribuo e mantenho alguns projetos open-source no GitHub.

## Sobre este site

O site tem o propósito de apresentar meu trabalho e hospedar minhas postagens sobre o que eu faço.
Espero que seja útil para você.

## Contatos

<script type="text/javascript">
  function revealEmail() {
    var el = document.querySelector('.email a');
    var email = ["andre", "@", "werlangtecnologia", ".com", ".br"].join("");
    el.href = "mailto:" + email;
    el.textContent = email;
    
    document.removeEventListener('scroll', revealEmail);
  }
  
  document.addEventListener('scroll', revealEmail);
</script>
<i class="fa fa-envelope-o"></i><span class="email"> Email: <a href="mail@domain.com.br">&nbsp;</a> </span><br>
<i class="fa fa-linkedin"></i> Linkedin: [@awerlang](https://www.linkedin.com/in/awerlang) <br>
<i class="fa fa-github"></i> GitHub: [@awerlang](https://github.com/awerlang) <br>
<i class="fa fa-twitter"></i> Twitter: [@awerlang](https://twitter.com/awerlang) <br>
