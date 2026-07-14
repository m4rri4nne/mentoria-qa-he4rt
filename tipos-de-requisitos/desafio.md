# Desafio 05 — Identifique e Questione Requisitos em um Refinamento

[![Dificuldade](https://img.shields.io/badge/Dificuldade-Intermediária-orange?style=for-the-badge)]()
[![Tema](https://img.shields.io/badge/Tema-Requisitos-blueviolet?style=for-the-badge)]()

> **Pré-requisito:** leia [`tipos-de-requisito.md`](./tipos-de-requisito.md) por completo antes de começar — em especial a seção 9 (Requisitos Explícitos vs. Implícitos). Este desafio assume que você já sabe diferenciar requisito funcional, não funcional, regra de negócio, restrição e regulatório.
>
> **Por que "Intermediária":** não existe código pra rodar e não existe uma única resposta certa. O trabalho aqui é ler pistas em uma conversa de refinamento — algumas explícitas, outras nas entrelinhas — e transformar suposições em perguntas concretas. É a habilidade que diferencia um QA que só executa o que o critério de aceite diz de um QA que participa da construção do critério de aceite.

---

## Objetivo

Simular a sua primeira participação em uma reunião de refinamento como QA recém-chegado a um time. Você não vai escrever requisitos — isso é papel do PO. Você vai **ler** os critérios de aceite e a conversa do time, **classificar** o que já está explícito, e **identificar** o que ficou implícito, subentendido ou simplesmente não foi dito, formulando as perguntas que você faria naquele momento para trazer isso à tona.

O foco não é encontrar bugs de código. É treinar o olhar para requisito incompleto — o tipo de gap que, se não for questionado no refinamento, vira retrabalho ou incidente em produção semanas depois.

---

## Por que isso importa

Quando você entra em um time novo, é comum sentir que não tem "bagagem" suficiente para opinar em um refinamento — afinal, todo mundo ali já conhece o produto, o domínio, as regras de negócio. Mas isso é exatamente o que te coloca em uma posição valiosa: você ainda não naturalizou os subentendidos daquele time, então as perguntas "óbvias" que você faria são, com frequência, perguntas que ninguém mais no time pensaria em fazer — porque para eles aquilo deixou de parecer uma pergunta há muito tempo.

Um QA que só recebe o critério de aceite pronto e parte direto para escrever casos de teste está aceitando qualquer lacuna que exista ali. Um QA que participa do refinamento e pergunta *"o que acontece se..."*, *"isso vale pra todo mundo ou só pra esse caso?"*, *"quem definiu esse número?"* está ajudando o time a **encontrar o requisito antes que ele vire bug**. Essa é uma das formas mais diretas de um QA júnior demonstrar valor cedo — não porque sabe mais do que o time, mas porque ainda enxerga o que o time deixou de ver.

---

## O Cenário: LojaVibe

Você acabou de entrar como QA em uma squad de e-commerce, a **LojaVibe**. É sua primeira reunião de refinamento no time. A história em pauta é sobre aplicação de cupom de desconto no carrinho, e a Black Friday está a 5 semanas de distância.

### Critérios de aceite (como o PO escreveu no card)

```
US-142 — Aplicar cupom de desconto no carrinho

Como cliente, quero aplicar um cupom de desconto no carrinho,
para pagar menos pelo meu pedido.

Critérios de aceite:
- Existe um campo "Cupom de desconto" no carrinho, com botão "Aplicar"
- Cliente digita o código e clica em "Aplicar"
- Se o cupom for válido, o sistema aplica o desconto sobre o subtotal
  e mostra quanto o cliente economizou
- Se o cupom for inválido ou expirado, o sistema mostra a mensagem
  "Cupom inválido ou expirado"
- O cupom "BEMVINDO10" concede 10% de desconto no primeiro pedido
  do cliente
```

### Trecho da conversa no refinamento

```
Marina (PO): Essa é simples, galera, só falta ativar antes da Black
  Friday. É basicamente igual ao cupom de aniversário que já existe.

Rafael (Dev Backend): Igual mesmo? O de aniversário é gerado
  automaticamente pelo nosso sistema. Esse aqui de boas-vindas
  quem vai gerar os códigos é o time de Marketing, né? Eles usam
  aquela plataforma de cupons de terceiro que integramos ano passado.

Marina (PO): Isso, a validação do código passa por lá. A gente só
  recebe "válido" ou "inválido" da resposta deles.

Bia (Dev Frontend): E se a plataforma deles cair? Isso rolou uma vez
  no Natal passado.

Marina (PO): Ah, é raro. Mas separado disso — sim, mesma regra do
  cupom de aniversário: um cliente só pode usar um cupom de boas-vindas
  na vida dele, óbvio.

Rafael (Dev Backend): Beleza, então a gente guarda isso vinculado ao
  CPF ou ao e-mail? Porque o de aniversário a gente vincula à conta.

Marina (PO): Vincula à conta mesmo, padrão.

Bia (Dev Frontend): Ah, e não faz sentido dar pra aplicar o cupom de
  boas-vindas junto com o de aniversário no mesmo pedido, certo?
  Isso o card não fala nada.

Marina (PO): Boa pergunta... acho que não, mas deixa eu confirmar
  com o time de Marketing depois.

Rafael (Dev Backend): Beleza. Ah, esse card vai entrar pro sprint que
  fecha bem em cima da Black Friday, então lembrem que o tráfego no
  carrinho nessa época costuma ser 8x o normal.
```

---

## A Tarefa

### Parte 1 — Classifique o que já está explícito

Para cada item dos critérios de aceite **e** cada afirmação relevante feita na conversa (mesmo que não esteja no card), classifique o tipo de requisito segundo [`tipos-de-requisito.md`](./tipos-de-requisito.md): Funcional, Não Funcional (indique o subtipo), Regra de Negócio, Restrição, Requisito de Sistema/Técnico ou Regulatório.

Monte uma tabela como esta:

```markdown
| # | O que foi dito | Onde apareceu (card ou fala de quem) | Tipo de requisito |
|---|-----------------|----------------------------------------|---------------------|
| 1 | Campo "Cupom de desconto" com botão "Aplicar" | Card | Funcional |
| 2 | ...                                              | ...                                      | ...                  |
```

### Parte 2 — Encontre o que ficou implícito

Liste **ao menos 8 requisitos implícitos** — coisas que precisam ser verdade para a funcionalidade funcionar direito em produção, mas que **não foram formalizadas** em nenhum critério de aceite, mesmo tendo sido tocadas (ou não) na conversa. Eles devem cobrir **pelo menos 4 tipos diferentes** de requisito (não vale listar 8 variações só de não funcional, por exemplo).

Pistas de onde procurar: a fala da Bia sobre a plataforma cair, o "óbvio" da Marina sobre um cupom por cliente, o pico de tráfego de Black Friday, o fato de dois devs diferentes descreverem "vincular à conta" de formas ligeiramente diferentes, e tudo que **não** foi dito sobre auditoria financeira, formatação de valores, ou o que conta como "primeiro pedido".

Para cada requisito implícito, entregue:

```markdown
### Implícito #1
**Tipo:** <Funcional / Não Funcional (subtipo) / Regra de Negócio / Restrição / Sistema / Regulatório>
**O que não foi dito:** <a lacuna, em uma frase>
**Pista que te levou até aqui:** <o que na conversa ou no card sugeriu isso>
**Pergunta que você faria no refinamento:** <a pergunta exata, com a redação que você realmente diria em voz alta para a Marina ou o Rafael>
```

> A pergunta precisa ser **respondível** por quem está na sala. "O sistema deveria ser seguro?" não é uma pergunta de refinamento — é uma afirmação genérica disfarçada de pergunta. "Se a plataforma de cupom do Marketing cair ou demorar pra responder, o que o cliente vê no carrinho — erro, ou deixamos ele finalizar sem desconto?" é uma pergunta real, específica, que alguém na sala consegue responder ali mesmo.

### Parte 3 — Priorize o risco

Escolha as **3 perguntas** da Parte 2 que você considera mais urgentes de responder **antes** do time começar a desenvolver, e explique, para cada uma, o que pode dar errado em produção se ela ficar sem resposta — conecte com o contexto real do card (Black Friday, integração externa, cupom de uso único). Evite justificativas genéricas — a resposta deve deixar claro por que *esse* risco, *nesse* contexto, é mais grave que os outros 5.

---

## Restrições

- ❌ Não escreva casos de teste — isso não é o objetivo deste desafio (veja [`criacao-caso-teste.md`](../criacao-caso-testes/criacao-caso-teste.md) se quiser praticar isso separadamente).
- ❌ Não invente requisitos implícitos genéricos que serviriam para qualquer sistema (ex: "deve ser seguro", "deve ser rápido") sem conectar com uma pista específica do cenário da LojaVibe.
- ❌ Não classifique tudo como "Funcional" — se você não conseguir diferenciar os tipos, revise a seção 9 de [`tipos-de-requisito.md`](./tipos-de-requisito.md) antes de continuar.
- ✅ Pode (e deve) usar bom senso de domínio de e-commerce para imaginar lacunas que nem a Bia nem o Rafael levantaram na conversa.
- ✅ Pode formular a pergunta com suas próprias palavras — o que importa é que ela seja específica e respondível, não que siga um script.

---

## Formato de entrega

Crie um repositório público no GitHub (ex: `refinamento-cupom-lojavibe`) com a seguinte estrutura:

```
refinamento-cupom-lojavibe/
├── README.md      ← breve descrição do desafio
└── entrega.md     ← Partes 1, 2 e 3 deste desafio
```

---

## Critérios de avaliação

- **Precisão na classificação** — os tipos de requisito da Parte 1 estão corretos e justificáveis pela definição de [`tipos-de-requisito.md`](./tipos-de-requisito.md)?
- **Profundidade da leitura de contexto** — os implícitos da Parte 2 usam de fato as pistas da conversa (integração externa, pico de tráfego, "óbvio" da PO), ou são genéricos o suficiente para servir qualquer card?
- **Diversidade de tipos** — os 8 implícitos cobrem múltiplos tipos de requisito, não só um?
- **Qualidade da pergunta** — a pergunta é algo que você realmente diria em uma reunião, e que alguém na sala conseguiria responder ali mesmo? Ou é uma afirmação disfarçada?
- **Julgamento de risco** — a priorização da Parte 3 está ancorada em consequência real de negócio (perda de venda na Black Friday, cupom aplicado em dobro, indisponibilidade do parceiro), não em preferência pessoal?

> Uma entrega que lista 8 requisitos não funcionais genéricos (`deve ser rápido`, `deve ser seguro`, `deve ser confiável`...) sem nenhuma conexão com a conversa da LojaVibe mostra que você decorou a lista de tipos, não que aprendeu a ler contexto. O objetivo aqui é simular a atenção real que se espera de alguém participando de um refinamento pela primeira vez.

---

## Referência

Revise [`tipos-de-requisito.md`](./tipos-de-requisito.md) antes de começar — em especial a seção 4 (Requisito Não Funcional, com seus subtipos) e a seção 9 (Explícito vs. Implícito), que são a base para as Partes 2 e 3 deste desafio.
