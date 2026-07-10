# Desafio 04 — Casos de Teste para Agendamento e Cancelamento (MedAgenda)

[![Dificuldade](https://img.shields.io/badge/Dificuldade-Avançada-red?style=for-the-badge)]()
[![Tema](https://img.shields.io/badge/Tema-Casos%20de%20Teste-blueviolet?style=for-the-badge)]()

> **Pré-requisito:** leia [`criacao-caso-teste.md`](./criacao-caso-teste.md) por completo. Se possível, revise sua entrega do [Desafio 02 — Estratégia de Testes do MedAgenda](../tipos-de-teste/desafio.md) — vamos trabalhar no mesmo produto fictício.
>
> **Por que "Avançada":** aqui você não recebe o formato pronto. Você decide se cada cenário deve ser escrito em BDD ou Step-by-Step, justifica essa escolha, e aplica técnicas de design para encontrar casos que a regra de negócio, lida superficialmente, não deixa óbvios. É o tipo de julgamento que separa quem "escreve caso de teste porque pediram" de quem **desenha** a cobertura de teste de uma funcionalidade.

---

## Objetivo

Criar casos de teste reais para duas funcionalidades críticas do **MedAgenda** — Agendamento de Consulta e Cancelamento de Consulta — escolhendo o formato adequado para cada situação e aplicando ao menos duas técnicas de design de teste para descobrir cenários de borda.

---

## Contexto: regras de negócio relevantes

(Resumo das regras do MedAgenda necessárias para este desafio — caso não tenha feito o Desafio 02, isso é suficiente para começar.)

- Paciente agenda uma consulta em um horário disponível do médico, presencial ou por teleconsulta.
- Um médico não pode ter dois agendamentos no mesmo horário. Um paciente também não.
- Pagamento (cartão de crédito ou Pix) é feito no momento do agendamento.
- Cancelamento com **mais de 2h** de antecedência: sem custo. Cancelamento com **menos de 2h**: cobra 30% do valor da consulta.
- Notificações de lembrete são enviadas 24h e 1h antes da consulta.
- Médicos liberam novos horários da semana toda segunda-feira às 8h — horário de maior acesso simultâneo.

---

## A Tarefa

### 1. Escolha de formato

Para cada uma das duas funcionalidades (Agendamento e Cancelamento), decida: você escreveria os casos de teste em **BDD** ou em **Step-by-Step**? Justifique com base nas características específicas de cada funcionalidade — não vale repetir a justificativa genérica do material de referência.

### 2. Cenários de Agendamento de Consulta (formato BDD)

Escreva cenários em Gherkin (`Dado/Quando/Então`) cobrindo:

- O caminho feliz (agendamento bem-sucedido).
- Ao menos **4 cenários alternativos/de exceção**, incluindo obrigatoriamente:
  - Conflito de horário do médico (já tem outro paciente agendado no mesmo horário).
  - Conflito de horário do paciente (já tem outra consulta agendada no mesmo horário).
  - Pagamento recusado pelo gateway.
  - Um quarto cenário de sua escolha (ex: escolha entre presencial/teleconsulta, horário fora da disponibilidade do médico, etc.)

### 3. Casos de Cancelamento de Consulta (formato Step-by-Step)

Escreva **ao menos 5 casos de teste**, aplicando explicitamente a técnica de **Análise de Valor Limite** sobre a regra das 2 horas. Isso significa: não basta testar "cancelamento com bastante antecedência" e "cancelamento de última hora" — você precisa testar exatamente em torno da fronteira (ex: 2h e 1 minuto antes, exatamente 2h antes, 1h59 antes).

Para cada caso, deixe explícito: o valor de antecedência testado e o resultado esperado de cobrança (0% ou 30%).

### 4. Tabela de Decisão

Monte uma **Tabela de Decisão** combinando estas três variáveis:

- Tipo de consulta: presencial ou teleconsulta
- Forma de pagamento: cartão de crédito ou Pix
- Momento do cancelamento: mais de 2h ou menos de 2h de antecedência

Com base na tabela, aponte **ao menos 4 combinações** que resultam em um caso de teste — incluindo ao menos uma combinação que você não teria pensado em testar só lendo a regra de negócio de forma corrida (ex: reembolso de Pix funciona de forma diferente de reembolso de cartão? a regra do MedAgenda não deixa isso explícito — é uma boa hora de levantar essa pergunta).

### 5. Cenários de borda que a regra de negócio não deixa claro

Identifique **ao menos 3 ambiguidades ou cenários de borda** que a especificação, como está escrita, não responde. Exemplos do tipo de pergunta esperada (não copie estes, pense nos seus):

- O cálculo das "2 horas de antecedência" usa o horário do servidor ou o horário local do dispositivo do paciente?
- O que acontece se o **médico** cancelar a consulta, em vez do paciente? A regra de cobrança de 30% também se aplica a ele?
- Dois pacientes tentam agendar o mesmo horário ao mesmo tempo (race condition) — o sistema garante que só um consegue?

Para cada ambiguidade, proponha **como você testaria isso** (mesmo sem saber a resposta da regra) ou **qual pergunta você levaria para o PO** antes de escrever o caso de teste definitivo.

---

## Restrições

- ❌ Não escreva cenários genéricos de "agendamento com sucesso" repetidos com nomes diferentes — cada cenário deve testar algo que o anterior não testa.
- ❌ Não pule a aplicação explícita da técnica de Valor Limite no item 3 — o objetivo é ver os valores exatos testados, não só "testei perto do limite".
- ❌ Não responda o item 5 com ambiguidades triviais (ex: "e se a internet cair?") — busque algo específico às regras de negócio do MedAgenda.
- ✅ Pode (e deve) levantar suposições explícitas quando a regra de negócio for omissa, desde que você deixe claro que é uma suposição.

---

## Entrega

### Onde entregar

Crie um repositório **público** no GitHub com o nome `medagenda-casos-de-teste` (ou similar).

Estrutura sugerida:

```
medagenda-casos-de-teste/
├── README.md       ← breve descrição do desafio
└── entrega.md       ← sua entrega completa (itens 1 a 5)
```

---

## Critérios de avaliação

- **Justificativa de formato** — a escolha BDD vs Step-by-Step no item 1 é fundamentada em características reais da funcionalidade, não em preferência pessoal?
- **Qualidade dos cenários BDD** — os cenários do item 2 cobrem regras de negócio reais (conflito de horário, pagamento) e não só variações de UI?
- **Aplicação correta da técnica de Valor Limite** — os casos do item 3 realmente testam a fronteira das 2 horas, com valores explícitos?
- **Tabela de Decisão** — as combinações do item 4 são logicamente derivadas da tabela, e não inventadas à parte?
- **Pensamento crítico** — as ambiguidades do item 5 são específicas ao MedAgenda e mostram raciocínio sobre o domínio (saúde, pagamento, concorrência), não genéricas?

> Uma entrega que escreve "vários cenários de agendamento" sem usar as técnicas pedidas demonstra conhecimento superficial do material. O objetivo deste desafio é simular a situação real de decidir, sozinho(a), como cobrir uma funcionalidade crítica de negócio — incluindo admitir o que a especificação não responde.

---

## Referência

Revise [`test-case-creation.md`](./test-case-creation.md) — em especial as seções de BDD, Step-by-Step, Valor Limite e Tabela de Decisão — antes de começar.
