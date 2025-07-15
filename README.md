# O crescimento do Discord e o desafio de armazenar trilhões de mensagens

O Discord é uma plataforma de comunicação largamente utilizada nos dias de hoje. Foi lançado em maio de 2015 e rapidamente conquistou o gosto do público. Em 2016, a plataforma, extraordinariamente, já registrava uma base ativa mensal de 10 milhões de usuários.

Pouco tempo depois, em 2017, grandes updates foram feitos, trazendo chamadas por vídeo e compartilhamento de tela, por exemplo. Já em 2018, a base mensal de usuários havia crescido mais de 4,5 vezes, alcançando 45 milhões de usuários ativos por mês.  
Fonte: [Business of Apps](https://www.businessofapps.com/data/discord-statistics/)

---

O crescimento fora do comum da plataforma se deve principalmente a dois fatores:

1. O público gamer
2. A pandemia global da COVID-19

Diversas parcerias entre o Discord e empresas do universo gamer foram feitas ao longo dos anos. Em 2017, por exemplo, foram desenvolvidas integrações com o Xbox e a Twitch, que, apesar de não ser puramente destinada ao público em questão, é grandemente consumida e produzida pelos gamers.

Em adição a isto, em 2020, durante a pandemia, o Discord registrou incríveis 800 mil downloads por dia, o que levou, em 2021, a uma base mensal de aproximadamente 150 milhões de usuários.  
Fontes:

- [Notta](<https://www.notta.ai/en/blog/discord-statistics#:~:text=about%20this%20platform.-,Discord%20stats%20(top%20picks),user%20base%20in%20September%202021>)
- [Business of Apps](https://www.businessofapps.com/data/discord-statistics/)

---

Dado o crescimento tão expressivo, também veio um aumento ainda mais significativo no número de mensagens trocadas todos os dias.

- Julho de 2016: 40 milhões de mensagens trocadas por dia
- Dezembro de 2016: 100 milhões de mensagens trocadas por dia

Fonte: [Discord Blog – How Discord Stores Billions of Messages](https://discord.com/blog/how-discord-stores-billions-of-messages)

---

Um ponto crucial para entender o desafio de escalar esse sistema é a decisão técnica da equipe de que as mensagens nunca deveriam expirar. Desde os primeiros anos da plataforma, o Discord adotou como premissa que os usuários deveriam ter acesso a todo o histórico de mensagens, independentemente do tempo. Isso significa que nenhuma mensagem é descartada ou apagada automaticamente, e que a quantidade total de dados armazenados cresce continuamente à medida que mais mensagens são enviadas.

Tendo um crescimento tão expressivo, não demorou muito para que os executivos do Discord percebessem um grande tsunami se aproximando: como armazenar tantas mensagens trocadas a todo tempo por uma base de usuários tão grande e que cresce mais grandemente ainda?

Felizmente, esta é uma pergunta com respostas — e boas respostas!

Em 2017, Stanislav Vishnevskiy, atual CTO e cofundador do Discord, escreveu com boa profundidade sobre como eles, na época, armazenavam bilhões de mensagens, no artigo “How Discord Stores Billions of Messages”. Neste artigo, é detalhado como e por que migraram do MongoDB para uma alternativa mais confiável (segundo eles), o Cassandra.  
Fontes:

- [Discord Blog – How Discord Stores Billions of Messages](https://discord.com/blog/how-discord-stores-billions-of-messages)
- [Perfil de Stanislav Vishnevskiy](https://www.linkedin.com/in/svishnevskiy/)

Como mencionado anteriormente, em 2017, apesar de já ser uma plataforma bem reconhecida no mercado e com uma grande quantidade de usuários ativos, o Discord ainda não havia passado pelo seu maior boom de crescimento — que ocorreu durante a pandemia.

Em 2023, com uma base de 200 milhões de usuários ativos por mês, Bo Ingram, engenheiro de software do Discord, escreveu um artigo descrevendo como, agora, estavam armazenando trilhões de mensagens.  
Fontes:

- [Discord Blog – How Discord Stores Trillions of Messages](https://discord.com/blog/how-discord-stores-trillions-of-messages)
- [Perfil de Bo Ingram](https://www.linkedin.com/in/bo-ingram-1a069275/)

---

## Objetivos

Este projeto se propõe a entender e explicar como o Discord armazena quantidades massivas de mensagens.

O objetivo não é apenas explicar o sistema de armazenamento de dados do Discord atualmente, mas compreender, dada cada fase do crescimento da plataforma:

- quais eram os desafios e as necessidades,
- como a equipe foi moldando o sistema para atender tais requisitos.

Ou seja, a ideia é entender as motivações de cada atualização do sistema de armazenamento e também explicar cada tomada de decisão.

Por exemplo, na seção de introdução, foi dito que, no começo, a equipe do Discord utilizava o MongoDB para o armazenamento das mensagens. Este projeto se propõe a:

- explicar as motivações da equipe para, em 2015, terem optado pelo MongoDB,
- mostrar o que os levou a atualizar o sistema para utilizar o Cassandra, e
- compreender o que os levou a atualizar o sistema mais uma vez em 2023.

Os títulos dos artigos ressaltam e engrandecem a ordem de grandeza da quantidade de mensagens armazenadas: “bilhões” e “trilhões”.

Além da pura quantidade de mensagens armazenadas, existem outros fatores que motivaram essas atualizações?

Este projeto tem como objetivo entender esse questionamento.

## 3. Escolha do MongoDB(2015-2016)

O trecho, em todo o artigo “How Discord Stores Billions of Messages”, que melhor resume o motivo de terem adotado o MongoDB no início do projeto é: "Nós não temos engenheiros de DevOps”.[Discord Blog – How Discord Stores Billions of Messages](https://discord.com/blog/how-discord-stores-billions-of-messages)

Diante deste resumo, é bem fácil entender o principal motivo pelo o qual os primeiros desenvolvedores do Discord optaram pelo o MongoDB como banco de dados. Eles simplesmente acreditaram que este seria o mais fácil de usar e o com que teriam a menor quantidade de dor de cabeça possível. Stanislav Vishnevskiy, Diretor de Tecnologia e Co-fundador do Discord, explica que eles apenas queriam entregar uma primeira versão do aplicativo, ou seja, o mais importante para eles, naquele momento, era velocidade de implementação e uma performance razoável, não precisavam de um sistema absolutamente robusto e performático, apenas velocidade e pouca dor de cabeça!

Em 2015, em apenas 60 dias, os primeiros devs criaram a primeira versão do Discord e o MongoDB, sendo um banco de documentos flexível, permitia salvar estruturas complexas sem definição rígida de esquema, facilitando a iteração veloz. Além disso, mensagens, usuários e canais eram naturalmente representados como documentos JSON, encaixando-se bem no modelo do MongoDB e tornando o acesso e a manipulação dos dados grandes simples e intuitivos, o que também contribuiu para o desenvolvimento rápido.

Neste primeiro momento do desenvolvimento, todas as mensagens eram armazenadas em um único replica set do MongoDB, ou seja, todas as mensagens ficavam centralizadas em um único conjunto de servidores com replicação automática. A ideia era manter a arquitetura simples e facilitar o desenvolvimento rápido, mesmo sabendo que, com o tempo, seria necessário escalar e repensar essa estrutura. Dessa forma, eles conseguiram manter, já no começo do desenvolvimento, boa disponibilidade e redundância, mas escolheram não distribuir os dados horizontalmente.

#### ⚠️ O que é sharding?⚠️

Para o melhor entendimento dos próximos tópicos, é bom refrescar a memória e entender o que é Sharding!
Sharding é uma técnica de particionamento de dados em que um banco divide grandes volumes de informações em partes menores chamadas shards, distribuídas entre diferentes servidores. Cada shard contém apenas uma fração dos dados totais, e o sistema precisa decidir onde armazenar e como consultar cada pedaço. Essa abordagem melhora a escalabilidade horizontal, mas adiciona complexidade à arquitetura, exigindo regras específicas para roteamento, consistência e manutenção dos dados.

![Sharding](sharding.jpg)

Fonte: [Documentação do MongoDB](https://www.mongodb.com/resources/products/capabilities/database-sharding-explained)

Eles ativamente optaram por não usar a função de sharding do MongoDB. O primeiro motivo que explica essa decisão já foi posto aqui: eles não queriam dor de cabeça. O autor do artigo explica, como também já foi mencionado aqui, que eles não possuíam nenhum engenheiro de DevOps. Nesse sentido, eles entendiam que o Sharding era complicado de usar e também não era muito conhecido por estabilidade, o que em meados de 2015 era muito aceitável de dizer.

Segundo eles, o sharding era complicado e não tinha reputação muito boa em questões de estabilidade. Essa afirmação não foi justificada no artigo, mas em alguns fóruns da internet é possível ver essa discussão:

- Nesse tópico do reddit, justamente sobre essa afirmação, é discutido esse ponto e um usuário encontra um artigo dizendo que na verdade o sharding do mongo é até melhor do que o do Cassandra. Outro diz que os 'old dev folks' tem preconceito com novas tecnologias como o MongoDB. [Reddit](https://www.reddit.com/r/mongodb/comments/f83ihc/mongodb_sharding_low_stability/)

- [Este artigo do medium](https://medium.com/geekculture/mongodb-why-avoid-sharding-it-should-be-kept-as-the-last-option-cb8fdc693b66) diz que o sharding é um anti-pattern, mas cita pouca coisa especificamente sobre o Mongo e sim sobre o sharding de forma geral.

- [Este artigo do próprio Mongo](https://www.mongodb.com/resources/products/platform/demystifying-sharding-mongodb) tem o objetivo de desmistificar o sharding no Mongo explicando como ele funciona e como fazer.

Diante dessa análise, parece que essa afirmação no artigo não foi tão embasada e o sharding em si não entra como um fator decisivo na migração de tecnologia É importante mencionar que essa migração começou em 2015/2016 e o Mongo evoluiu muito desde então.

Uma outra razão pela qual os fizeram escolher fugir do sharding e usar apenas uma única replica set é que eles estavam cientes de suas limitações técnicas naquele momento, então essas duas escolhas foram propositais não só por simplicidade, mas também porque facilitariam muito a migração futura. Eles já sabiam que o MongoDB não daria conta da escala futura, e projetaram sua arquitetura de forma a evitar problemas quando chegasse a hora de mudar. [Discord Blog – How Discord Stores Billions of Messages](https://discord.com/blog/how-discord-stores-billions-of-messages)

Evitar o sharding no início e manter todos os dados concentrados em um único replica set deixou a arquitetura do Discord mais simples, previsível e fácil de mover. Quando se usa sharding, os dados ficam espalhados por múltiplos servidores, com regras específicas para particionamento, roteamento de consultas e consistência entre shards. Isso tudo dificulta a exportação dos dados e também não contribui para a simplicidade do sistema. Já com um replica set único, os dados estão centralizados, a estrutura é mais próxima de um banco relacional ou de outro NoSQL tradicional, e as consultas são mais diretas. Isso permitiu que a equipe do Discord planejasse uma migração gradual desde o princípio.

### O começo do fim

Até cerca de 100 milhões de mensagens em novembro de 2015, a performance era muito aceitável e realmente cumpriu o desejo inicial dos devs de causar pouca dor de cabeça. O uso de um índice composto mantinha a consulta de mensagens recentes bem eficiente.

As mensagens, neste ponto, eram armazenadas em uma coleção do Mongo com um único índice composto nos atributos channel_id e created_at. Essa escolha de índice se dá pelo seguinte principal motivo:

- Uma conversa pertence à um canal (um grupo - normalmente chamado de servidor - ou um chat entre dois usuários, como na imagem abaixo) e elas possuem uma ordem de tempo nas mensagens enviadas. Logo, para buscar as mensagens rapidamente e de maneira ordenada, faz muito sentido criar um índice nesses campos. Como a seletividade das mensagens sempre será baixa (já que se uma conversa tiver 1 milhão de mensagens - o que não é muito realista, ainda mais em 2015 quando o discord não era tão popular, ainda assim é somente 10% da quantidade total de mensagens armazenadas por volta do fim desse mesmo ano), o índice será utilizado para a grande maioria das queries.

![Conversa no discord](discord-chat.png)

Contudo, quando as mensagens começaram a passar das 100 milhões em Novembro de 2015, o conjunto de dados excedeu a capacidade de memória disponível, os dados e os índices já não cabiam mais na memória RAM, o que forçava o acesso direto ao disco, que, por sua vez, acabou degradando muito a latência e a experiência do usuário. [Linkedin](https://www.linkedin.com/pulse/discords-journey-from-mongodb-cassandra-ankit-shaw/)

Esse foi o estopim para o que podemos chamar de começo do fim do MongoDB no Discord. A latência ficou imprevisível, especialmente em horários de pico, e a equipe logo percebeu que o sistema já não atendia mais aos níveis de desempenho necessários para manter uma experiência fluida de chat.

Diante deste cenário, os devs do Discord perceberam que finalmente havia chegado o momento de começar a migração de banco de dados, o qual estavam se preparando desde o princípio do projeto.

## 4. Migração para o Cassandra (2017)

Em julho de 2016, foi anunciado que o discord armazenava 40 milhões de mensagens por dia. Em dezembro desse mesmo ano, o número mais que dobrou: 100 milhões. Segundo o artigo [How Discord Stores Billions of Messages](https://discord.com/blog/how-discord-stores-billions-of-messages), eles tomaram a decisão de projeto inicial de nunca apagar mensagens e chats, o que gera um enorme volume de dados. Como eles fazem isso? Cassandra!

### Padrões de Leitura/Escrita e problemas com a arquitetura atual

- Os reads eram extremamente aleatórios e a proporção de reads/writes era por volta de 50/50.

- Servidores discord focados principalmente em chat por voz eram um problema, já que pouquissimas mensagens eram trocadas e isso causava um problema conhecido: as poucas mensagens podem estar muito distantes fisicamente no disco, fazendo com sejam feitas muitas leituras aleatórias, o que é um dos principais gargalos de sistemas computacionais, já que é uma operação cara. Além disso, isso causa o que é conhecido como 'disk cache evictions':

  - É o processo de remoção de dados do cache de disco (uma área rápida de memória) para dar lugar a novos dados. O problema ocorre quando dados raramente acessados (como mensagens antigas em chats inativos, ou mesmo as mensagens desses servidores onde os canais de chat são pouco utilizados) são lidos do disco e forçam a remoção de dados "quentes" (frequentemente acessados) do cache. Quando esses dados "quentes" são necessários novamente, eles precisam ser lidos do disco, que é uma operação lenta, causando degradação na performance e latências imprevisíveis. [Fonte](https://www.geeksforgeeks.org/system-design/cache-eviction-policies-system-design/)

- Servidores médios que são mais focados em chat de mensagens não possuem esse problema em especifíco, mas acaba sendo similar uma vez que possuem poucos usuários, então as mensagens usualmente não estão em cache, sendo necessário acesso ao disco.

- Servidores grandes mandam muitas mensagens. Possuem milhares de usuários mandando milhares de mensagens. Eles fazem requests de mensagens recentes praticamente o tempo todo, então a chance da mensagem procurada estar em cache é alta.

- As features encaminhadas iriam implicar em ainda mais acessos aleatórios ao disco, como por exemplo a possibilidade de ver suas menções (@eu) nos últimos 30 dias.

### Requisitos definidos

- Escalabilidade linear: Não queriam reconsiderar a solução ou precisar fazer 're-shard' dos dados.

- Recuperação automática: o Discord deveria se auto-recuperar o máximo possível.

- Baixa manutenção: Deve funcionar depois da configuração. A única manutenção a ser realizada deve ser a de adicionar novos nós conforme os dados crescem.

- Tecnologia já consolidada.

- Performance previsível: Internamente, 95% de todas as requisições tem obrigatóriamente que serem feitas abaixo de 80 milisegundos. Se esse tempo for ultrapassado, então alertas serão enviados para a equipe de engenharia averiguar. Além disso, eles não querem 'cachear' mensagens em um sistema como o Redis e o Memchaced, afim de evitar uma camada de complexidade extra no sistema.

- Não é uma 'blob-store': Um "blob" (Binary Large Object) é uma coleção de dados de tamanho arbitrário (como um arquivo de imagem ou vídeo) armazenada como uma sequência de bytes, sem um formato ou estrutura específica que o banco de dados possa entender. Embora o Discord armazene anexos, a base de dados principal das mensagens precisava de consultas estruturadas (buscar por autor, data, etc.), algo que um blob-store não oferece eficientemente. Ter que lidar com os processos envolvidos com blobs para a base de mensagens seria muito custoso.
  [O que é um blob-storage segundo a cloudflare](https://www.cloudflare.com/learning/cloud/what-is-blob-storage/)

- Open source: Eles não queriam depender de uma outra empresa para algo tão essêncial no negócio deles.

O Cassandra preenche todos os requisitos:

- Para escalar, basta adicionar nós.

- Pode tolerar perda de nós sem impacto nenhum na aplicação.

- Empresas como a Netflix e Apple possuem milhares de nós Cassandra.

- Dados relacionados são mantidos continuamente no disco, proporcionando o minimo de seeks e fácil distribuição no cluster.

- É open source.

### Modelagem dos dados

Segundo o artigo, a melhor maneira de descrever o Cassandra é como uma 'KKV store' - **K**ey-**K**ey-**V**alue. Imagine que temos um arquivo físico com diversas pastas e que cada pasta contém fichas ordenadas.

O primeiro K é a chave de partição que o Cassandra usa para decidir em qual servidor (nó) o dado vai ser armazenado. Ela também determina a localização física do dado no disco do servidor. Na nossa analogia, esse K seria equivalente à etiqueta na frente da pasta, então teríamos pastas do tipo: 'Canal A', 'Canal B', 'Canal C' e etc, sendo que K seria o nome do canal.

Cada partição contém múltiplas tuplas. Dentro de cada partição definida pela chave de partição, é possível ter diversos registros de dados. Na analogia, é como se na pasta 'Canal A', tivessemos várias fichas de papel, sendo que cada ficha representa uma mensagem.

Cada tupla dentro da partição é identificada pelo segundo K, que é a chave de agrupamento. Além de ser o identificador único de cada tupla, ele também é responsável pela ordem que as linhas serão armazenadas no disco. Na analogia, seria como a data e hora de uma mensagem (para esta analogia, suponha que as mensagens não podem ser enviadas exatamente no mesmo momento, algo que é possível no discord). As fichas não estariam simplesmente jogadas na pasta, elas seriam organizadas por data e hora, sendo que cada data/hora identifica unicamente uma mensagem.

[Documentação do Cassandra sobre modelagem de dados](https://cassandra.apache.org/doc/latest/cassandra/developing/data-modeling/intro.html)

### Aplicando a Modelagem de Dados no Discord

Com o modelo KKV em mente, a equipe do Discord aplicou esses conceitos diretamente para resolver seus problemas de performance. A estratégia foi mapear o padrão de acesso mais comum — carregar as mensagens de um canal específico — para a estrutura de chaves do Cassandra.

A chave primária da tabela de mensagens foi definida como (channel_id, message_id):

1.  channel_id como Chave de Partição: Assim como no MongoDB o índice era em channel_id e created_at, a channel_id foi a escolha natural para a chave de partição. Isso garante que todas as mensagens de um mesmo canal sejam armazenadas juntas no mesmo nó, otimizando a busca inicial.

2.  message_id como Chave de Agrupamento: Inicialmente, created_at (data de criação) parecia uma boa candidata para a chave de agrupamento, pois as mensagens são exibidas em ordem cronológica. No entanto, duas mensagens podem ser criadas no mesmo instante, o que violaria a unicidade da chave. A solução foi usar o message_id. Cada ID no Discord é um Snowflake, um tipo de identificador que, além de ser único, é cronologicamente ordenável. Isso permitiu que o message_id servisse perfeitamente como a chave de agrupamento, garantindo unicidade e ordem.

Com essa estrutura, ao carregar um canal, o Discord pode pedir ao Cassandra um "range scan" (leitura de intervalo) exato, tornando a busca de mensagens extremamente rápida e previsível.

O esquema simplificado da tabela de mensagens ficou assim:

```sql
CREATE TABLE messages (
  channel_id bigint,
  message_id bigint,
  author_id bigint,
  content text,
  PRIMARY KEY (channel_id, message_id)
) WITH CLUSTERING ORDER BY (message_id DESC);
```

A cláusula WITH CLUSTERING ORDER BY (message_id DESC) instrui o Cassandra a armazenar as mensagens em ordem decrescente de message_id dentro de cada partição. Isso é uma otimização extra: como os usuários quase sempre querem ver as mensagens mais recentes primeiro, o banco de dados já as armazena na ordem exata em que serão exibidas, tornando a leitura ainda mais eficiente.

### O Problema das Partições Gigantes e a Solução com "Bucketing"

Durante a migração dos dados do MongoDB, a equipe do Discord encontrou um problema: alguns canais eram tão ativos que suas partições no Cassandra estavam ultrapassando o [limite recomendado de 100MB](https://cassandra.apache.org/doc/latest/cassandra/developing/data-modeling/intro.html#data-model-analysis).

Segundo o artigo do Discord, as razões de partições grandes serem um problema são operacionais e afetam a performance e estabilidade do banco de dados:

- **Pressão na Memória (GC):** Operações em partições muito grandes consomem uma quantidade significativa de memória RAM no servidor, podendo levar a longas pausas para "coleta de lixo" (Garbage Collection).
  - O cassandra é escrito em Java, por isso existe essa pressão na JVM. [repositório oficial do cassandra](https://github.com/apache/cassandra)
- **Latência de Leitura/Escrita:** O banco de dados precisa trabalhar mais para ler e manter a consistência de dados espalhados em uma partição enorme.
- **Manutenção Lenta:** Processos internos do Cassandra, como compactação, tornam-se extremamente lentos e custosos quando precisam operar em partições gigantes.
  - Processo de compactação: Para entender esse processo, é importante entender como o Cassandra armazena fisicamente seus dados. Eles são escritos em arquivos chamados de SSTables. Para que o write do Cassandra seja extremamente rápido, eles não escrevem diretamente nesse arquivo, ao invés disso, os dados são primeiramente escritos de maneira ordenada em uma estrutura em memória principal chamada memtable até que um limite configurável seja atingido. Então essa memtable passa por um processo de 'flushing' para escrever os dados no arquivo SSTable. Para manter essas SSTables, a cada inserção/update, o Cassandra escreve versões com timestamps dos novos dados em novas SSTables. Ao deletar dados, eles não são fisicamente deletados, ao invés disso são marcados como tombstones. Com o passar do tempo, acessar as tabelas podem exigir percorrer mais SSTables e para evitar isso, ocorre o processo de compactação, que é basicamente o merge das tabelas mantendo as versões mais recentes e apagando fisicamente os tombstones. [Documentação da empresa que mantém o Cassandra sobre o processo de compactação](https://docs.datastax.com/en/cassandra-oss/3.x/cassandra/dml/dmlHowDataMaintain.html#Compaction)

Para resolver isso, o Discord aplicou uma técnica de modelagem de dados comum no Cassandra chamada bucketing. A ideia é dividir artificialmente uma partição grande em várias partições físicas menores, adicionando um novo campo à chave de partição.

A chave primária foi alterada:

```sql
CREATE TABLE messages (
   channel_id bigint,
   bucket int,
   message_id bigint,
   author_id bigint,
   content text,
   PRIMARY KEY ((channel_id, bucket), message_id)
) WITH CLUSTERING ORDER BY (message_id DESC);
```

No caso do Discord, o campo `bucket` é calculado com base no tempo. Eles fizeram um estudo nos maiores servidores e perceberam que guardar os últimos 10 dias de mensagens seria o ideal para manter os buckets menores que 100MB e ainda assim garantir uma performance e quantidade de dados confortável. Com isso, em vez de uma única partição gigante para um canal, agora existem várias partições menores e gerenciáveis: `(canal_123, semana_1)`, `(canal_123, semana_2)`, etc.

Isso mantém as partições pequenas, garantindo a performance e a estabilidade do cluster, com a pequena desvantagem de que a aplicação agora precisa consultar múltiplos buckets para carregar um histórico de mensagens muito antigo. Isso indica que os serivores pouco utilizados precisariam percorrer diversos buckets para coletar mensagens suficientes, mas isso é OK porque para servidores mais ativos a quantidade de mensagens suficientes é encontrada na primeira partição e esses servidores são maioria.

### Subindo para produção

Antes de colocar o novo sistema com Cassandra totalmente em produção, eles modificaram o código para fazerem os reads/writes tanto no mongo quanto no Cassandra, afim de evitar impacto aos usuários.

Logo que subiram essas mudanças eles perceberam que nos logs do sistema existiam entradas de erro dizendo que o campo obrigatório `author_id` era nulo. A culpa disso? Consistência eventual.

### Consistência eventual

O Cassandra é um banco AP (do teorema CAP), o que significa que é um banco com alta disponibilidade e alta resistência a partição.

[Documentação do Cassandra sobre suas garantias](https://cassandra.apache.org/doc/latest/cassandra/architecture/guarantees.html)

Para entender o porquê da consistência eventual ser a causa desse problema, vamos entender como a inserção dos dados no Cassandra (complementar à descrição feita acima no tópico de compactação).

O Cassandra faz um Upsert: inSERT se a tupla não existe e UPdate se ela existe. O Upsert é feito somente nas colunas, não atualizando a tupla inteira. Se o mesmo campo for atualizado concorrentemente, então a mudança com maior timestamp será mantida, implementando uma forma de "last-write-wins" por coluna.

[Artigo da empresa que mantém o Cassandra sobre a forma que o Cassandra faz o update de tuplas com "last-write-wins"](https://www.datastax.com/blog/why-cassandra-doesnt-need-vector-clocks)

Voltando ao problema do `author_id` ser nulo, imagine a seguinte situação:

- O Usuário PenidoBR está conversando em um servidor X com diversos outros usuários e envia uma mensagem ofensiva, sem perceber. O Cassandra, então, guarda a mensagem:
  ```json
  {
    message_id: 123,
    author_id: "PenidoBR",
    content: "@Dival, você é muito frango! HAHAHAHA",
    ...
  }
  ```
- Alguns minutos depois, PenidoBR percebe que a mensagem era ofensiva e vai editá-la. Porém, ao mesmo tempo, o usuário Skineura, um moderador do servidor, vê a mensagem e decide apagá-la. Ao mesmo tempo, Skineura clica o botão de apagar e PenidoBR clica o botão de editar. Ambos ainda tem acesso à mensagem.

- Por conta de latências de rede, a requisição de delete é efetuada primeiro:

  ```json
  {
    message_id: null,
    author_id: null,
    content: null,
    ...
  }
  ```

- Logo depois, a requisição de update é efetuada:

  ```json
  {
    message_id: 123,
    author_id: null,
    content: "@Dival, você precisa treinar mais! Se quiser, me chama!"
    ...
  }
  ```

  O resultado é uma mensagem corrompida: uma linha no banco que só tem channel_id, message_id e content. Todos os outros campos, como author_id, que não foram incluídos na operação de edição, ficam nulos. A mensagem "ressuscita", mas de forma incompleta.

- Exemplo em gif:

  ![Race condition](race-cond.gif)

Eles pensaram em duas soluções para esse problema:

1. Escrever a mensagem inteira quando for editar. Isso abre margem para a "ressureição" de mensagens deletadas e aumenta a chance de conlitos em writes concorrentes em outras colunas.

2. Setando mensagens como corrompidas e deletando-as se campos obrigatórios forem nulos.

Eles escolheram a segunda abordagem e escolheram o campo author_id para ser o ponto de decisão se uma mensagem está corrompida ou não.

Ao resolverem esse problema, eles perceberam que estavam sendo ineficientes com seus writes. Como já citado anteriormente, ao deletar um registro, cria-se uma tombstone. O motivo disso é principalmente para manter a consistência individual, já que um registro deletado pode ressucitar no seguinte cenário (Cada [A] significa o dado A em um certo nó [ ]):

- Situação inicial: [A][A][A]
- Delete A é enviado ao primeiro nó e propagado aos demais: [] -> [] -/ [A]. O último nó ainda não recebeu o delete por conta de qualquer problema de comunicação entre eles.
- Dessa forma, em uma eventual sincronização do sistema, o dado A pode ressucitar para os outros nós se o dado A for atualizado e seu timestamp for maior que o do delete, de forma semelhante ao que foi descrito no caso do Discord.

Se marcarmos com a tombstone, é fácil evitar esse comportamento:

- Situação inicial: [A][A][A]
- Delete A é enviado ao primeiro nó e propagado aos demais: [tombstone(A)] -> [tombstone(A)] -/ [A]. O último nó ainda não recebeu o delete por conta de qualquer problema de comunicação entre eles.
- Dessa forma, em uma eventual sincronização do sistema, o dado A não pode ressucitar pois já foi marcado como 'morto' em outros nós, independente de sua última atualização.

O sistema, ao ler tombstones, apenas "pula" elas. Elas vivem por 10 dias por default e são permanentemente deletadas no processo de compressão.

[Documentação sobre tombstones](https://cassandra.apache.org/doc/5.0/cassandra/managing/operating/compaction/overview.html#tombstones)

Deletar ou setar como nulo são essecialmente a mesma coisa. [Este artigo mostra, na prática, como setar uma coluna como null cria uma tombstone nessa coluna](https://digitalis.io/post/what-are-tombstones-in-cassandra-and-why-are-there-too-many). Os engenheiros do discord perceberam que, dos 16 campos da mensagem, aproximadamente 4 eram escritos com valores não nulos de fato. Eles estavam inserindo 12 tombstones na maior parte do tempo, gerando um overhead relacionado ao gerenciamento de tombstones sem motivo nenhum. A solução utilizada foi simples: se o campo não está preenchido, não envia. Dessa maneira, somente os campos de fato enviados são escritos no banco, evitando a geração de tombstones inúteis.

### Performance obtida

![Dados sobre a performance](performance-data.png)

Como visto nessa imagem, a performance obtida foi absurda. Os reads aconteceram em cerca de 5ms e os writes em menos de 1ms.

Anteriormente, foi dito que por conta da maneira que o Mongo armazenava as mensagens, era muito custoso buscar mensagens antigas pois seriam necessários muitos acessos aleatórios ao disco, uma vez que elas raramente estariam em cache. No gif a seguir, é mostrado como um jump-back de 1 ano de mensagens acontece de maneira extremamente rápida:

![Jump back de um ano](jump-back.gif)

### A grande surpresa

Tudo correu bem por cerca de 6 meses, até que "A grande surpresa" aconteceu. O sistema estava funcionando perfeitamente, até o dia em que o Cassandra simplesmente parou de responder.

A equipe de engenharia notou que o Cassandra estava executando pausas de "stop-the-world" para Garbage Collection (GC) de 10 segundos constantemente, mas não havia uma causa aparente. Após investigarem, encontraram um canal do Discord que levava 20 segundos para carregar. O culpado era o servidor público do subreddit "Puzzles & Dragons". Como era um servidor público, os engenheiros entraram para investigar e, para sua surpresa, o canal continha apenas uma única mensagem.

Nesse momento, ficou claro o que havia acontecido: os administradores do servidor haviam deletado milhões de mensagens usando a API do Discord, deixando apenas uma para trás.

Como explicado anteriormente na seção sobre consistência eventual, o Cassandra lida com exclusões usando tombstones. Quando um usuário tentava carregar aquele canal, embora houvesse apenas uma mensagem visível, o Cassandra precisava escanear milhões de tombstones de mensagens, gerando uma quantidade de "lixo" (objetos a serem coletados pelo GC) muito maior do que a JVM conseguia processar, causando as longas pausas.

A solução para este problema foi implementada em duas frentes:

1.  **Redução do tempo de vida dos tombstones:** O tempo de vida padrão dos tombstones foi reduzido de 10 dias para 2 dias. Isso foi possível porque o Discord já executava reparos (um processo anti-entropia para garantir a consistência entre os nós) todas as noites no cluster de mensagens.
2.  **Otimização da query:** O código da aplicação foi alterado para rastrear buckets vazios e evitá-los em consultas futuras para um mesmo canal. Isso significa que, se um usuário gerasse essa consulta novamente, na pior das hipóteses, o Cassandra escanearia apenas o bucket mais recente, em vez de todos os buckets que continham apenas tombstones.

## 5. Migração para o ScyllaDB (2023)

Com o passar dos anos, o Discord continuou a crescer exponencialmente, atingindo a marca de trilhões de mensagens armazenadas e uma base de usuários cada vez mais ativa. O sistema baseado em Cassandra, que havia sido uma solução robusta para a fase dos “bilhões de mensagens”, começou a apresentar limitações severas diante do novo patamar de escala e exigências operacionais.

### Novos Desafios: O Limite do Cassandra

No início de 2022, o cluster de mensagens do Discord rodava com 177 nós Cassandra, armazenando trilhões de mensagens. Apesar de toda a engenharia investida, a equipe enfrentava problemas recorrentes:

- **Latência imprevisível:** A latência das operações variava de 5 ms a picos de 125 ms, especialmente em momentos de tráfego intenso ou durante operações de manutenção. Isso dificultava o cumprimento do SLA interno de respostas rápidas e impactava diretamente a experiência do usuário.
- **Manutenção custosa:** Operações como compactação de SSTables exigiam intervenções manuais frequentes, incluindo a chamada “dança da fofoca” (remover nós da rotação para compactação e reintegrá-los depois). Esse processo era trabalhoso e arriscado, pois qualquer erro poderia afetar a disponibilidade do serviço.
- **Partições quentes (_hot partitions_):** Em bancos de dados distribuídos, uma "hot partition" ocorre quando uma partição específica recebe um volume desproporcional de tráfego, sobrecarregando um nó do cluster e impactando a latência global do sistema. No caso do Discord, canais muito populares ou eventos como menções em massa (@everyone) geravam esse tipo de situação, concentrando milhares de requisições simultâneas em uma única partição e tornando esse um desafio típico em sistemas de mensageria massiva.
- **Pausas de Garbage Collection (GC):** Por ser implementado em Java, o Cassandra sofria com pausas de GC (“stop-the-world”) que podiam chegar a 10 segundos, afetando a disponibilidade e previsibilidade do sistema. Essas pausas eram agravadas pelo acúmulo de tombstones — marcadores de exclusão criados quando um dado é deletado em bancos baseados em LSM-tree, como o Cassandra e o ScyllaDB. O excesso desses marcadores pode degradar a performance e travar operações de compactação e migração — além do volume massivo de dados.
- **Compactação ineficiente:** O processo de compactação, fundamental para manter a performance de leitura em bancos baseados em LSM-tree (Log-Structured Merge-Tree, uma estrutura de dados que prioriza escritas rápidas em memória e posterior flush para disco, com compactação periódica), tornava-se cada vez mais custoso à medida que o volume de dados crescia. Em alguns casos, a compactação de partições com muitos tombstones podia travar a operação do cluster.

Esses problemas não eram exclusivos do cluster de mensagens, mas se manifestavam de forma mais grave devido ao volume e ao padrão de acesso do Discord. A equipe buscava uma solução que permitisse escalar de forma sustentável, reduzisse custos operacionais e garantisse performance estável mesmo sob cargas extremas.

### Avaliação e Escolha do ScyllaDB

Diante desse cenário, o Discord avaliou alternativas e decidiu migrar para o **ScyllaDB**, um banco de dados compatível com Cassandra, mas reescrito em C++ e com arquitetura baseada em _shard-per-core_ (cada núcleo físico do servidor opera de forma isolada, reduzindo contenção e maximizando o uso de hardware moderno).

As principais motivações para a escolha do ScyllaDB foram:

- **Eliminação das pausas de GC:** Por não depender da JVM, o ScyllaDB não sofre com pausas de coleta de lixo, garantindo latências mais estáveis.
- **Isolamento de carga:** O modelo _shard-per-core_ — arquitetura em que cada núcleo físico do processador opera como um shard independente, processando requisições de forma isolada e paralela, maximizando o uso do hardware e evitando contenção de recursos — permite que cada núcleo processe suas próprias requisições, evitando que partições quentes impactem o cluster inteiro. Isso é especialmente relevante para workloads com picos localizados, como grandes servidores do Discord.
- **Desempenho superior:** Testes internos mostraram que o ScyllaDB oferecia latências menores e throughput maior, especialmente em operações de leitura reversa (essencial para buscar mensagens mais antigas). O suporte a consultas reversas otimizadas foi um diferencial importante, já que o padrão de acesso do Discord frequentemente exige "scroll back" em grandes volumes de mensagens.
- **Facilidade de manutenção:** Operações como compactação e reparo são mais rápidas e menos intrusivas. O ScyllaDB também oferece ferramentas de monitoramento e automação mais modernas.
- **Escalabilidade e eficiência:** Após a migração, foi possível reduzir o número de nós de 177 para 72, com cada nó suportando 9 TB (vs. 4 TB anteriormente), o que representa um ganho significativo em eficiência operacional e redução de custos.

Antes de migrar o cluster principal de mensagens, o Discord testou o ScyllaDB em outros clusters menores, ajustando configurações e colaborando com a equipe do ScyllaDB para otimizar consultas reversas, que eram um gargalo inicial. Esse processo de validação gradual é uma prática recomendada em migrações de sistemas críticos.

### O modelo _shard-per-core_ do ScyllaDB: conceito, vantagens e benchmarks

O ScyllaDB diferencia-se do Cassandra principalmente por sua arquitetura _shard-per-core_. Nesse modelo, cada núcleo físico do processador (core) é responsável por um shard independente dentro do mesmo nó, processando requisições de forma isolada e paralela. Isso elimina a necessidade de locks globais, reduz a contenção de recursos e permite que o banco de dados aproveite ao máximo o hardware moderno, especialmente em servidores com muitos núcleos e arquitetura NUMA. Cada shard possui sua própria thread, memória e filas de requisições, tornando o processamento mais previsível e eficiente.

**Vantagens do modelo shard-per-core:**

- Aproveitamento máximo do paralelismo do hardware, escalando linearmente com o número de núcleos.
- Redução drástica da contenção de recursos e eliminação de locks globais.
- Latência mais estável e previsível, mesmo sob cargas elevadas.
- Isolamento de falhas: problemas em um shard não afetam os demais.
- Menor overhead de contexto e gerenciamento de threads.
- Eficiência energética e melhor utilização de cache de CPU.
- Operações de manutenção (compactação, reparo) podem ser distribuídas entre shards, acelerando processos críticos.

**Benchmarks comparativos: ScyllaDB vs Cassandra**

| Métrica                   | ScyllaDB (shard-per-core) | Cassandra (tradicional) |
| ------------------------- | :-----------------------: | :---------------------: |
| Throughput (ops/s)        |           1.2M            |          300k           |
| Latência p99 de leitura   |           2 ms            |          20 ms          |
| Latência p99 de escrita   |          1.5 ms           |          15 ms          |
| Uso de CPU                |          80–90%           |         40–60%          |
| Eficiência energética     |           Alta            |          Média          |
| Escalabilidade horizontal |          Linear           |        Sublinear        |
| Overhead de manutenção    |           Baixo           |          Alto           |

_Fontes: [ScyllaDB vs Cassandra Performance Comparison](https://www.datasciencecentral.com/scylla-vs-cassandra-performance-comparison/), [ScyllaDB Benchmarks](https://www.scylladb.com/product/benchmarks/), [ScyllaDB vs Apache Cassandra](https://www.scylladb.com/compare/scylladb-vs-apache-cassandra/)_

### Serviços de Dados em Rust: Protegendo o Banco de Dados

Mesmo com a migração para o ScyllaDB, um desafio persistia: o padrão de acesso do Discord gerava picos de tráfego concentrados em determinados canais, especialmente durante grandes anúncios ou menções em massa (@everyone). Para mitigar o risco de _hot partitions_, a equipe desenvolveu uma nova camada intermediária chamada **serviços de dados** (_data services_), implementada em Rust.

Esses serviços atuam como um “buffer inteligente” entre a API do Discord e o banco de dados, com as seguintes funções:

- **Coalescência de requisições:** Se múltiplos usuários solicitam a mesma mensagem ou conjunto de mensagens simultaneamente, o serviço agrupa as requisições idênticas em uma única consulta ao banco, retornando o resultado para todos. Essa técnica, chamada coalescência de requisições, reduz drasticamente a concorrência no banco de dados e otimiza o acesso, sendo especialmente útil para workloads massivos e para evitar sobrecarga em partições quentes.
- **Roteamento consistente:** Utilizando hash do `channel_id`, todas as requisições para um mesmo canal são direcionadas para a mesma instância do serviço, evitando concorrência descontrolada e protegendo o banco de dados de sobrecarga. Esse padrão de roteamento é fundamental para garantir isolamento e previsibilidade de performance.
- **Concorrência segura:** O uso de Rust e do ecossistema Tokio permite alta performance e segurança em operações assíncronas, sem os riscos de concorrência típicos de linguagens como Java ou C++. O Rust foi escolhido por sua segurança de memória, performance próxima ao C++ e excelente suporte a programação concorrente.
- **Comunicação eficiente:** Os serviços utilizam gRPC para comunicação entre si e com a API, garantindo baixa latência e escalabilidade horizontal.

Essa arquitetura foi fundamental para garantir que, mesmo em eventos de tráfego extremo (como a final da Copa do Mundo de 2022, que gerou nove picos de mensagens em minutos), o sistema permanecesse estável e responsivo. O conceito de coalescência de requisições é uma técnica avançada de otimização para workloads massivos e pode ser estudado em sistemas de cache e filas distribuídas.

### O Processo de Migração

A migração de trilhões de mensagens do Cassandra para o ScyllaDB foi um desafio técnico de grande escala, com requisitos de zero downtime e validação rigorosa dos dados.

O processo envolveu:

1. **Provisionamento do novo cluster:** O ScyllaDB foi configurado com SSDs locais e RAID para maximizar desempenho e durabilidade. O uso de SSDs é fundamental para workloads de leitura intensiva e grandes volumes de dados.
2. **Gravação duplicada:** Durante a transição, novas mensagens eram gravadas tanto no Cassandra quanto no ScyllaDB, garantindo consistência e permitindo rollback em caso de falhas.
3. **Ferramenta de migração em Rust:** Inicialmente, a migração seria feita com Spark, mas a equipe desenvolveu uma ferramenta própria em Rust, capaz de migrar até 3,2 milhões de mensagens por segundo, reduzindo o tempo estimado de migração de três meses para apenas nove dias. O uso de SQLite para validação local durante a migração foi uma escolha pragmática para garantir integridade dos dados.
4. **Validação automática:** Uma fração das leituras era comparada entre os dois bancos para garantir integridade. Esse tipo de validação cruzada é essencial em migrações de sistemas críticos.
5. **Resolução de tombstones:** A migração travou nos últimos 0,0001% dos dados devido a intervalos massivos de tombstones não compactados no Cassandra. Após compactação manual desses intervalos, a migração foi concluída com sucesso. O acúmulo de tombstones é um problema conhecido em bancos baseados em LSM-tree e pode ser estudado em artigos sobre "Cassandra tombstone compaction".

### Mudanças na Modelagem de Dados e Otimizações

Curiosamente, a migração para o ScyllaDB não exigiu mudanças no esquema de dados principal. O modelo de partição por `(channel_id, bucket)` e ordenação por `message_id DESC` foi mantido, pois já estava otimizado para o padrão de acesso do Discord. No entanto, o ScyllaDB trouxe otimizações nativas, como suporte eficiente a consultas reversas e melhor distribuição de carga entre os shards.

Além disso, os serviços de dados permitiram mitigar o problema das hot partitions sem alterar a modelagem, ao agrupar e roteirizar requisições de forma inteligente. Isso demonstra a importância de soluções arquiteturais além do banco de dados em si.

### Resultados Obtidos

A migração para o ScyllaDB trouxe ganhos expressivos:

- **Redução de infraestrutura:** O número de nós caiu de 177 (Cassandra) para 72 (ScyllaDB), com cada nó suportando 9 TB (vs. 4 TB anteriormente).
- **Latência drasticamente menor:** A latência p99 de leitura caiu de 40–125 ms para 15 ms; a de escrita estabilizou em 5 ms.
- **Menor custo operacional:** Menos nós, menos manutenção manual e maior previsibilidade.
- **Resiliência comprovada:** O sistema suportou picos globais de tráfego sem degradação perceptível, como evidenciado durante a Copa do Mundo de 2022.
- **Escalabilidade sustentável:** O novo sistema permite crescer horizontalmente com adição de nós, mantendo performance estável.

Esses resultados são corroborados por métricas apresentadas publicamente pela equipe do Discord e por relatos de outros grandes usuários do ScyllaDB, como Netflix e Apple.

### Conceitos Técnicos Fundamentais

Para compreender a fundo as decisões do Discord, é importante detalhar alguns conceitos:

- **Hot Partition:** Partição que recebe tráfego desproporcional, sobrecarregando um nó e impactando a latência global. No Discord, grandes servidores ou eventos (@everyone) são fontes típicas desse problema.
- **Tombstones:** Marcadores de exclusão em bancos baseados em LSM-tree. O acúmulo excessivo pode degradar a performance e travar operações de compactação e migração.
- **LSM-Tree:** Estrutura de dados que prioriza escrita rápida em memória (memtable) e posterior flush para disco (SSTables), com compactação periódica. Essencial para bancos como Cassandra e ScyllaDB.
- **Shard-per-Core:** Arquitetura do ScyllaDB onde cada núcleo físico opera como um shard isolado, maximizando o uso do hardware e evitando contenção.
- **Coalescência de Requisições:** Técnica de agrupar múltiplas requisições idênticas para reduzir concorrência e otimizar acesso ao banco.

### Considerações Finais e Lições Aprendidas

A jornada do Discord ilustra a necessidade de evolução contínua em sistemas de processamento massivo de dados. O que era suficiente para bilhões de mensagens tornou-se insuficiente diante de trilhões. A migração para o ScyllaDB, aliada à criação dos serviços de dados em Rust, permitiu ao Discord atingir um novo patamar de escala, performance e confiabilidade.

**Lições importantes:**

- **Monitoramento e automação são essenciais:** O sucesso da migração e da operação contínua dependeu de validação automatizada, reparos noturnos e monitoramento proativo.
- **Adoção de tecnologias modernas:** O uso de Rust e ScyllaDB mostrou-se decisivo para superar limitações de soluções anteriores.
- **Desafios futuros:** Apesar dos avanços, o Discord reconhece que novas ordens de magnitude trarão novos desafios, como _tiered storage_ (armazenamento em camadas) e sharding automático para clusters multi-TB por nó. Estratégias como uso de S3 para mensagens antigas ou Bloom filters para otimizar buscas por tombstones são discutidas na comunidade e podem ser exploradas em trabalhos futuros.
- **Evitar vendor lock-in:** A dependência de soluções específicas (como ScyllaDB) é um risco a ser monitorado, e alternativas devem ser avaliadas periodicamente.

---