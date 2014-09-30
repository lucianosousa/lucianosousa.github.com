---
layout: post
title: Migrando do Paperclip para o Carrierwave
status: published
type: post
published: true
---

Recentemente, tive um problema muito grande com o paperclip ao carregar/paginar somente 20 itens.

Descrição do problema:
Antes de carregar as páginas, o paperclip fazia requets diretamente na S3 e só carregava a view depois de baixar/carregar/anything todas as imagens. Isso gerava um tempo de espera absurdo. Era praticamente impossível utilizar a app porque eu tinha uma lista de 20 itens, com 3 links cada item. uma imagem e dois outros arquivos, todos no S3. Ou seja, antes de carregar a página, minha app fazia 60 requests para a amazon, carregava todo os links e depois renderizava a mesma.

Durante a investigação do problema, o projeto não tinha newrelic e eu instalei para me ajudar a resolver ou me dar uma luz, visto que eu imagina que era isso mas não tinha certeza nenhuma, somente uma linha no meu terminal que dizia que minha view - uma única partial com esses 3 links- levava 20 segundos para carregar.
Agradeço imensamente ao serviço free deles. Por mais que eu imaginasse que o problema era isso, eu não tinha como provar nada, então graças a alguns gráficos e informações relevantes, pude de fato confirmar que o problema era isso.

Segue um exemplo de como estava o load da minha página, com dados:


```
Breakdown table
Category     Segment                                  % Time   Avg calls(per txn)  Avg time(ms)
External     Excon[example.s3.amazonaws.com]: HEAD    96.2     25.0                23,300
View         examples/_example.html.erb Partial        1.4     25.0                339
View         examples/index.html.erb Template          1.0      1.0                247
Middleware   ActionDispatch::Routing::RouteSet#call    0.4      1.0                97.3
View         examples/_filters.html.erb Partial        0.2      1.0                44.8
Database     SQL - SELECT                              0.2     38.0                59.3
Rack App     Example::Application#call                 0.1      1.0                16.3
View         layouts/application Template              0.1      1.0                18.3
```

Depois de procurar por muitas soluções, até nas issues já fechadas do github encontrei um ma solução parcial.
No meu caso, eu usava o paperclip com a gem fog. O fog era carregado por um arquivo initializer do paperclip. ex:

```
Paperclip::Attachment.default_options[:storage] = :fog
Paperclip::Attachment.default_options[:fog_credentials] = { 
  aws_access_key_id: Settings.aws_api_key,
  aws_secret_access_key: Settings.aws_secret_key,
  provider: Settings.file_provider
}
Paperclip::Attachment.default_options[:fog_directory] = Settings.aws_bucket
```
O problema do paperclip nesse caso, de acordo com infos da issue #909(https://github.com/thoughtbot/paperclip/issues/909) no github era que a cada load do paperclip em um item, ele fazia o load dos settings do paperclip com um parse das infos.m


Hoje em dia:


```
Breakdown table
Category     Segment                                   % Time     Avg calls(per txn)  Avg time (ms)
External     Net::HTTP[example.amazonaws.com]: HEAD    62.0       2.08                477
View         examples/_example.html.erb Partial        21.7       25.0                167
View         examples/index.html.erb Template          7.1        1.0                 54.6
Database     SQL - SELECT                              2.4        26.5                18.4
View         examples/_filters.html.erb Partial        1.7        1.0                 13.4
Middleware   ActionDispatch::Routing::RouteSet#call    1.1        1.0                 8.47
View         layouts/application Template              0.8        1.0                 6.43
```


Resumo:
Com um loadtime de ~23 segundos antes, agora temos um loadtime de ~4, praticamente 6 vezes mais rápido.
