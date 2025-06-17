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
