# Desafio 01 — CSS Selectors na Prática

[![Dificuldade](https://img.shields.io/badge/Dificuldade-Iniciante-brightgreen?style=for-the-badge)]()
[![Tema](https://img.shields.io/badge/Tema-CSS%20Selectors-blueviolet?style=for-the-badge)]()

> **Por que "Iniciante":** o foco aqui é praticar a sintaxe e o raciocínio de localização de elementos, sem decisões estratégicas envolvidas. É o ponto de partida antes de desafios que exigem julgamento mais sênior, como o de [estratégia de testes](../tipos-de-teste/desafio.md).

## Objetivo

Colocar em prática o conhecimento sobre CSS Selectors localizando elementos reais em um site público, sem depender de `data-testid` ou atributos criados especificamente para testes.

O objetivo não é apenas fazer funcionar — é entender **por que** aquele seletor é uma boa (ou má) escolha.

---

## O Site

Acesse: **[https://www.saucedemo.com](https://www.saucedemo.com)**

É uma aplicação de e-commerce fictícia criada para prática de automação. Você pode navegar livremente, fazer login e explorar as páginas.

**Credenciais de acesso:**
- Usuário: `standard_user`
- Senha: `secret_sauce`

---

## A Tarefa

Você deverá criar **ao menos 5 seletores CSS para cada tipo** listado abaixo. Para cada seletor, você deve documentar:

1. O seletor em si
2. Qual elemento ele identifica (o que é, onde está na página)
3. Por que você escolheu esse formato
4. O nível de estabilidade que você atribui a ele (alta / média / baixa) e o motivo
5. Quais dificuldades encontrou para chegar nesse seletor

### Tipos requeridos

| # | Tipo | Mínimo |
|---|---|---|
| 1 | Seletor por atributo (`[attr="valor"]`) | 5 seletores |
| 2 | Seletor por classe (`.classe`) | 5 seletores |
| 3 | Seletor combinado (tag + atributo, classe + pseudo-classe, etc.) | 5 seletores |
| 4 | Seletor descendente ou filho direto (`>` ou espaço) | 5 seletores |
| 5 | Pseudo-classe (`:checked`, `:disabled`, `:nth-child`, `:not()`, etc.) | 5 seletores |

> 💡 **Dica:** Use o DevTools do browser (F12 → aba Elements + Console) para inspecionar elementos e testar seus seletores em tempo real com `document.querySelector('seu-seletor')` ou `document.querySelectorAll('seu-seletor')`.

---

## Restrições

- ❌ Não use atributos `data-testid`, `data-cy` ou similares criados para teste
- ❌ Não use `id` gerado dinamicamente (ex: `#input-3f9a2b`)
- ✅ IDs semânticos e estáticos são permitidos
- ✅ Atributos de acessibilidade (`aria-label`, `role`, `name`) são encorajados

---

## Entrega

### Onde entregar

Crie um repositório **público** no GitHub com o nome `css-selectors-desafio` (ou similar).

A estrutura sugerida é:

```
css-selectors-desafio/
├── README.md          ← breve descrição do desafio
└── entrega.md         ← sua documentação dos seletores
```

### O que deve estar no `entrega.md`

Para cada seletor, documente seguindo este formato:

```markdown
### [Tipo do seletor] — [Nome/descrição do elemento]

**Seletor:** `seu-seletor-aqui`

**Elemento identificado:** Botão de login na página inicial

**Por que esse seletor:** Explique o raciocínio — por que esse atributo, essa classe, essa combinação?

**Estabilidade:** Alta / Média / Baixa

**Justificativa de estabilidade:** Por que você avalia assim?

**Dificuldades encontradas:** O que foi difícil? Tentou outras abordagens antes?
```

---

## Critérios de avaliação

A entrega será avaliada com base em:

- **Cobertura** — os 5 tipos foram cobertos com ao menos 5 seletores cada?
- **Precisão** — o seletor identifica exatamente o elemento que você descreveu?
- **Análise de estabilidade** — a avaliação está fundamentada ou é superficial?
- **Reflexão sobre dificuldades** — você documentou o processo real de busca?
- **Qualidade da escrita** — clareza e objetividade na documentação

> Documentação rasa ("escolhi porque funciona") não demonstra entendimento. O objetivo é que você consiga explicar o raciocínio para outra pessoa do time.

---

## Referência

Consulte o arquivo [`css-selectors.md`](./css-selectors.md) para revisar os conceitos antes de começar.
