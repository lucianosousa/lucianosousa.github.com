---
layout: post
title: Rails Improvements - Part 1
status: published
type: post
published: true
---

Começo hoje uma série de dicas de como melhorar a performance de suas aplicações Rails.
A primeira dica vem de uma das mais críticas partes de uma aplicação, acesso ao banco de dados.

Minha primeira dica é mostrar a forma mais performática de fazer uma query de registro único.
No meu exemplo, eu quero buscar um cliente com o nome `Luciano Sousa` em um banco Postgres com 76496 registros.

```
$ ruby benchmark1.rb
Calculating -------------------------------------
         where_first   130.000  i/100ms
        find_by_name   223.000  i/100ms
             find_by   224.000  i/100ms
-------------------------------------------------
         where_first      1.400k (± 6.5%) i/s -      7.020k
        find_by_name      2.304k (± 6.9%) i/s -     11.596k
             find_by      2.330k (± 7.3%) i/s -     11.648k

Comparison:
             find_by:     2330.4 i/s
        find_by_name:     2303.5 i/s - 1.01x slower
         where_first:     1399.6 i/s - 1.67x slower
```

{% gist lucianosousa/5f188167b1f907e0f34f %}

A seguir, um teste utilizando o banco de dados sqlite em memória

```
$ ruby benchmark2.rb
Calculating -------------------------------------
        where_first   332.000  i/100ms
        find_by_name  922.000  i/100ms
        find_by       930.000  i/100ms
-------------------------------------------------
        where_first   3.416k (±10.2%)  i/s -     16.932k
        find_by_name  9.194k (± 8.0%)  i/s -     46.100k
        find_by       9.345k (±10.7%)  i/s -     46.500k

Comparison:
        find_by:       9345.0 i/s
        find_by_name:  9193.6 i/s - 1.02x slower
        where_first:   3415.8 i/s - 2.74x slower
```

{% gist lucianosousa/642e182eec4f6da356cb %}

Em Rails, você pode utilizar os 3 métodos acima para fazer essa query:

- `where().first`
- `find_by_name` (supondo que o nome do seu atributo é `name`)
- `find_by(name: nome_a_ser_buscado)`

O método `where().first` é o mais encontrado em projetos antigos.
O método `find_by_name` utiliza method missing em sua composição, que apesar de ser muito útil, é uma das features mais custosas do Ruby. Para a função com method missing ser chamada, o interpretador do Ruby precisa chamar a função na classe em questão, e em todas as classes pais, para por fim, executá-lo. É lindo, mas é caro.

Se você tem algum projeto com código legado, é hora de começar a fazer uma limpeza em seus métodos e substituir todos por `find_by()`, caso seu Rails esteja atualizado.

PS: Todos os benchmarks foram executados utilizando o Ruby 2.3.0, os resultados em outras versões do Ruby podem variar.