---
title: Escrever por extenso Ruby on Rails
---

<p class="lead">Simples método para escrever por extenso em Ruby on Rails</p>

> <p style="text-align: justify;"> Resolvi fazer esse post para ajudar outros desenvolvedores que como eu encontrou dificuldades e complicações para escrever números e moedas por extenso.</p>

<p style="text-align: justify;"> Não vou contar toda a historia do porque precisei desse método, mas resumidadmente estava eu trabalhando num projeto em foi necessário escrever moéda em extenso num determinado relatório que era gerado, porém depois de muitas buscas e tentativas de alguams soluções das mais conhecidas como "BrazilianRails, MoneyRails, Helpers entre outros", não encontrei o que solucionaria isso de forma rápida sem muita implementação. No meu caso precisava fazer alguns tratamentos no Controller e Model, porém muitas dessas soluções trabalhavam como Helpers na view, tornando mais trabalhosa a implementação.<br>
Mas emfim com ajuda de uma lógica originalmete feita em PHP pelo Professor Fausto G. Cintra, emplementei em ruby num método, e depois acabei por fazer uma Gem para deixar disponível para quem tiver o mesmo problema.<br>
Se interessar a mais alguém, e quiser fazer parte desse projeto contribuindo para melhorar cada vez mais, tai o repositório: <b><a href="https://github.com/rickmax/extensobr/">ExtensoBr</a></b>, segue a baixo como usar. :D 
</p>

### Instalação

Adicionando em sua Gemfile:

```ruby
gem 'extensobr'
```

Ou instale você mesmo:

    $ gem install extensobr

### Exemplos de uso

Para obter o extenso de um número, utilize GExtenso.numero.

    irb

require 'Extensobr.rb'
 
    puts Extenso.numero(832); # oitocentos e trinta e dois
    puts Extenso.numero(832, Extenso::GENERO_FEM) # oitocentas e trinta e duas

Para obter o extenso de um valor monetário, utilize Extenso.moeda.

    require 'Extenso.rb'
 
### IMPORTANTE: este método recebe um valor inteiro(int), para a contagem das casas decimais!
    
    puts Extenso.moeda(15402) # cento e cinquenta e quatro reais e dois centavos
    puts Extenso.moeda(47)   # quarenta e sete centavos
    puts Extenso.moeda(357082, 2, ['peseta', 'pesetas', Extenso::GENERO_FEM], ['cêntimo', 'cêntimos', Extenso::GENERO_MASC])

### três mil, quinhentas e setenta pesetas e oitenta e dois cêntimos

<p>Se você leu até aqui, obrigado, use e seja feliz!</p>
