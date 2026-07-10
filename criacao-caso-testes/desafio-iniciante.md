# Desafio 03 — Escrevendo Casos de Teste no Formato Step-by-Step

[![Dificuldade](https://img.shields.io/badge/Dificuldade-Iniciante-brightgreen?style=for-the-badge)]()
[![Tema](https://img.shields.io/badge/Tema-Casos%20de%20Teste-blueviolet?style=for-the-badge)]()

> **Pré-requisito:** leia [`criacao-caso-teste.md`](./criacao-caso-teste.md) antes de começar — em especial a seção 1 (formato Step-by-Step) e as técnicas de Particionamento de Equivalência e Valor Limite.
>
> **Por que "Iniciante":** o foco aqui é praticar a escrita clara, completa e reprodutível de um caso de teste — sem ainda precisar decidir qual formato usar ou negociar trade-offs. É o ponto de partida antes do desafio que exige julgamento sobre formato e técnica, o [Desafio 04](./desafio-avancado.md).

---

## Objetivo

Praticar a escrita de casos de teste no formato **Step-by-Step**, cobrindo cenários positivos e negativos de forma clara o suficiente para que qualquer outra pessoa do time consiga executá-los sem te perguntar nada.

O objetivo não é só preencher os campos — é treinar o raciocínio de **o que** testar (caminho feliz, erro esperado, valores de borda) e **como** descrever isso sem ambiguidade.

---

## O Site

Acesse: **[https://www.saucedemo.com](https://www.saucedemo.com)** — o mesmo site usado no desafio de [CSS Selectors](../css-selectors/desafio.md).

O site possui múltiplos usuários de teste com comportamentos diferentes, o que é ótimo para casos negativos:

| Usuário | Senha | Comportamento |
|---|---|---|
| `standard_user` | `secret_sauce` | Funciona normalmente |
| `locked_out_user` | `secret_sauce` | Login bloqueado, exibe erro |
| `problem_user` | `secret_sauce` | Login funciona, mas há bugs visuais no catálogo |
| `performance_glitch_user` | `secret_sauce` | Login funciona, mas com lentidão proposital |

---

## A Tarefa

Você deve escrever **ao menos 8 casos de teste**, distribuídos assim:

| # | Funcionalidade | Mínimo de casos | Observação |
|---|---|---|---|
| 1 | Login | 3 casos | Ao menos 1 positivo e 2 negativos (ex: senha incorreta, `locked_out_user`) |
| 2 | Carrinho de compras | 3 casos | Adicionar produto, remover produto, validar contador do ícone do carrinho |
| 3 | Ordenação de produtos | 2 casos | Ex: "Name (A to Z)" e "Price (low to high)" no dropdown de ordenação |

Cada caso de teste deve seguir **exatamente** esta estrutura:

```
ID: TC-XXX
Título: <descrição curta e específica>
Pré-condições: <o que precisa ser verdade antes de começar>
Prioridade: <Alta / Média / Baixa>

Passos:
1. ...
2. ...
3. ...

Resultado esperado:
<o que deve acontecer, de forma verificável>
```

### Requisito extra (técnica de design)

Para os 3 casos de **Login**, identifique explicitamente no final de cada caso qual técnica de design você aplicou para escolher aquele cenário — **Particionamento de Equivalência** (ex: "credencial válida" vs "credencial inválida" são partições diferentes) ou **Valor Limite** (se aplicável). Justifique em 1-2 frases.

---

## Restrições

- ❌ Não escreva só o caminho feliz — cenários negativos são obrigatórios nas quantidades pedidas.
- ❌ Não pule a pré-condição nem o resultado esperado — um caso sem resultado esperado não é testável.
- ❌ Passos vagos como "testar o login" não valem — descreva a ação exata (campo, valor, botão).
- ✅ Pode escrever em uma tabela markdown, lista, ou usar uma ferramenta como TestRail/Notion e exportar — o que importa é a estrutura, não a ferramenta.

---

## Entrega

### Onde entregar

Crie um repositório **público** no GitHub com o nome `casos-de-teste-saucedemo` (ou similar).

Estrutura sugerida:

```
casos-de-teste-saucedemo/
├── README.md      ← breve descrição do desafio
└── entrega.md      ← seus 8+ casos de teste
```

---

## Critérios de avaliação

- **Clareza** — outra pessoa conseguiria executar seu caso de teste sem te perguntar nada?
- **Reprodutibilidade** — os passos levam, de forma determinística, ao resultado esperado descrito?
- **Cobertura** — os mínimos de positivo/negativo por funcionalidade foram respeitados?
- **Aplicação da técnica** — a justificativa de Particionamento de Equivalência / Valor Limite nos casos de login está correta e não é genérica?
- **Objetividade** — sem passos supérfluos, sem ambiguidade.

> Um caso de teste que só você entende não é um caso de teste — é uma nota pessoal. O critério de qualidade aqui é: dá pra entregar isso pra outro QA do time e ele executa sem te interromper.

---

## Referência

Consulte [`test-case-creation.md`](./test-case-creation.md) para revisar o formato Step-by-Step e as técnicas de design antes de começar.
