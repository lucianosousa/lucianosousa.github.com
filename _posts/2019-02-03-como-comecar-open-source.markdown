---
layout: post
title: Open Source em Ruby/ RubyOnRails, por onde começar?
status: published
type: :post
published: true
---

Como membro de vários grupos de Ruby e desenvolvimento em geral, constantemente eu vejo pessoas perguntando como e por onde começar a contribuir em projetos open source. Neste post vou tentar resumir como eu comecei e qual caminho acabei seguindo.

Lembrando que não é a melhor forma de começar, é só mais uma das imensas maneiras.
Não sou o maior expert em open source que você vai ver por aí, então se você encontrar outros guias/ dicas sobre, sinta-se à vontade em compartilhar nos comentários.

1 - Documentação (Primeira, mais importante e mais fácil de realizar)

Traduzir documentação foi minha terceira contribuição, e primeira relevante, em um projeto Open Source. Minha ideia era começar a traduzir o [Sinatra Book](https://github.com/sinatra/sinatra-book/), porque eu achei que seria uma coisa muito grande e boa para minha carreira como dev, mas por recomendação do [Kostantin Haase](https://twitter.com/konstantinhaase), achei melhor começar pela doc oficial do [Sinatra](http://sinatrarb.com/intro-pt-br.html). Até hoje, uma boa parte do que eu [fiz](https://github.com/sinatra/sinatra/pull/123) ainda faz parte da tradução atual.

Com certeza esse é o melhor caminho para começar a contribuir com projetos Open Source. Se você ainda não sabe por onde começar, fica aqui minha primeira e mais valiosa sugestão. Aprendi bastante sobre o Sinatra na época, e até hoje me recordo de bastante coisa que eu li essa época, mais de 8 anos atrás.

Resumo: Encontre um projeto/gem que não possua e/ou esteja com tradução atrasada e melhore ela, quer uma ideia? Comece agora pela gem do Sinatra.

[Rubygems.org](https://github.com/rubygems/rubygems.org) O site do rubygems tem bastante coisa desatualizada e pendente no locale do [pt-BR](https://github.com/rubygems/rubygems.org/blob/master/config/locales/pt-BR.yml).

[Factory Bot](https://github.com/thoughtbot/factory_bot/blob/master/GETTING_STARTED.md) A documentação oficial do FactoryBot não tem nenhuma tradução. Por que não fazer a primeira?

Se você usa bastante alguma gem no seu dia a dia, dê uma olhada no repositório dela e veja se existe alguma tradução, e/ ou a tradução está desatualizada. Caso não tenha, crie uma issue perguntando se é interessante criar essa tradução a partir da documentação oficial, e explique o por quê você acha relevante.

Sugestões:
- Traduzir documentação de gems/ projetos para sua língua nativa ou outra que você seja fluente
- Melhorar tradução de documentação já existente nessas gems/ projetos


2 - Simples Refactoring

Reduzir duplicação de código é um grande candidato a primeiro PR. Essa ideia eu aprendi no livro do [Robert Martin](https://twitter.com/unclebobmartin), [Clean Code](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882), e [aqui](https://martinfowler.com/bliki/OpportunisticRefactoring.html) o Martin Fowler faz uma grande explicação sobre, o OpportunitiscRefactoring.
 Um exemplo de como fazer um PR desses está [aqui](https://github.com/mperham/sidekiq/pull/1702) no Sidekiq.

Sugestões:
- Utilizar ferramentas como o [rails best practices](https://github.com/flyerhzm/rails_best_practices), [bullet](https://github.com/flyerhzm/bullet), [codeclimate](https://github.com/codeclimate/codeclimate) e/ ou afins localmente em busca de code smell e envie PR's corrigindo os problemas identificados.

3 - Pequenas melhorias

A terceira dica já começa a ficar um pouco mais hostil, mas analisando bem o site/ gem/ etc que você usa no dia a dia, fica muito fácil de perceber que existe sim espaço para esse tipo de PR.

Esse [PR](https://github.com/edavis10/redmine-stuff-to-do-plugin/pull/1) (ignorado, mas valeu a tentativa e o aprendizado na época, 2010) no Stuff-to-do exemplifica essa situação.

Uma outra dica nesse caso é: melhore a cobertura de testes, de todas as formas, seja adicionando testes, corrigindo [testes quebrados/flacky](https://github.com/metricfu/metric_fu/pull/270) ou seja adicionando novos [builds](https://github.com/smartinez87/exception_notification/pull/227) para melhorar a cobertura.

Sugestões:
- A gem factory-bot tem 98% de cobertura de [testes](https://travis-ci.org/thoughtbot/factory_bot/jobs/486730214). Por que não aumentar um pouco mais essa cobertura?
- Analise as gems que você sempre usa e veja se a cobertura de testes está boa, caso esteja abaixo de 90%, por exemplo, tente descobrir onde você pode melhorar a cobertura;
- Analise que tipo de feature falta em alguma biblioteca que você usa e escolha uma para trabalhar;

4 - Novas features

A quarta e última dica, eu recomendo você tentar somente após fazer ao menos duas das três sugestões acima, para você ser mais confiante e mais familiarizado com o processo de enviar PR's e saber como maintaners trabalham, aceitam  e revisam PR's.
É bem comum um maintaner recusar ou mandar seu PR para revisão por alguma convenção do projeto que você não está familiarizado, então, quanto mais você fizer PR's pequenos e simples, mais rápido você pega a manha de como essas análises funcionam.
Não é uma tarefa que só de bater o olho você vai identificar o que precisa ser feito, mas se você trabalha todos os dias com alguma gem/ projeto, em algum momento entenderá que falta alguma coisa, e assim terá um ótimo motivo para trabalhar em algo do tipo.
 Meu exemplo nesse caso foi criar o matcher `:source` para a gem shoulda-matchers, confira [aqui](https://github.com/thoughtbot/shoulda-matchers/commit/803c06b0b06a6188c5f0e134e113cb9d296a9353). Em algum momento precisava testar isso, e como não existia esse matcher, tentei e consegui adicionar :D
Sugestões:
- Analise as gems que estão em seu `Gemfile` e pense quais features faltam nelas.

Plus:
Muita gente que constantemente trabalha com uma gem pode já ter criado uma issue no github requisitando determinada feature, se você está familiarizado com algum projeto, é interessante acompanhar as issues deste e assim que surgir uma requisição de alguém por alguma feature, sinta-se à vontade em falar que vai trabalhar na tarefa, e trabalhe! :D

Espero que este post tenha te ajudado de alguma forma. Sinta-se à vontade em agregar outras dicas nos comentários para quem chegou até aqui buscando de alguma forma ajudar a comunidade.

Até a próxima.

