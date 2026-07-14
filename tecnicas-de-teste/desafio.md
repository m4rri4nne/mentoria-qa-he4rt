# Desafio 07 — Caixa Preta, Branca e Cinza na Prática (TrocaFácil)

[![Dificuldade](https://img.shields.io/badge/Dificuldade-Iniciante-brightgreen?style=for-the-badge)]()
[![Tema](https://img.shields.io/badge/Tema-Técnicas%20de%20Teste-blueviolet?style=for-the-badge)]()

> **Pré-requisito:** leia [`tecnicas-de-teste.md`](./tecnicas-de-teste.md) por completo antes de começar — em especial as técnicas de Particionamento de Equivalência, Valor Limite, Tabela de Decisão e Cobertura de Decisão/Branch.
>
> **Por que "Iniciante":** você não precisa saber programar para resolver esse desafio. A parte de Caixa Branca usa um código bem curto e comentado só para praticar o raciocínio de "quais caminhos esse código pode seguir" — não é preciso escrever nem executar nada, só ler e identificar. O foco é entender a diferença entre as três técnicas na prática, antes de aplicá-las em cenários mais complexos.

---

## Objetivo

Aplicar, em um único cenário fictício, as três técnicas descritas em [`tecnicas-de-teste.md`](./tecnicas-de-teste.md): projetar testes de **Caixa Preta** a partir de regras de negócio, ler um trecho de código para projetar testes de **Caixa Branca** com cobertura de decisão, e usar conhecimento parcial da arquitetura para projetar testes de **Caixa Cinza**.

---

## O Cenário: TrocaFácil

A **TrocaFácil** é uma plataforma fictícia onde usuários cadastram itens para trocar ou vender entre si. Para se cadastrar, existem regras de negócio que o time definiu no refinamento:

```
Regras de negócio — Cadastro de usuário:

- Idade menor que 13 anos: cadastro bloqueado.
- Idade entre 13 e 17 anos (inclusive): cadastro permitido apenas
  se houver autorização de um responsável (um checkbox de
  confirmação no formulário).
- Idade 18 anos ou mais: cadastro permitido livremente, sem
  necessidade de autorização.
- Nome de usuário: precisa ter entre 3 e 15 caracteres.
```

---

## A Tarefa

### Parte 1 — Caixa Preta

Usando **Particionamento de Equivalência** e **Análise de Valor Limite**, defina as partições e os valores de fronteira relevantes para o campo **idade**. Em seguida, monte uma **Tabela de Decisão** cruzando as partições de idade com a condição "tem autorização do responsável? (sim/não)", indicando o resultado esperado (cadastro permitido ou bloqueado) para cada combinação.

Depois, aplique Particionamento de Equivalência e Valor Limite também para o campo **nome de usuário** (tamanho entre 3 e 15 caracteres).

Entregue como uma tabela:

```markdown
| # | Idade | Autorização do responsável | Nome de usuário (tamanho) | Resultado esperado | Técnica aplicada |
|---|---|---|---|---|---|
| 1 | 12 | N/A | "ana123" (6) | Bloqueado | Valor Limite (fronteira 13) |
| 2 | ... | ... | ... | ... | ... |
```

> Sua tabela deve ter, no mínimo, **10 linhas**, cobrindo todas as fronteiras de idade (12, 13, 17, 18) e de tamanho de nome (2, 3, 15, 16 caracteres), além de pelo menos uma combinação de cada partição de idade com autorização presente e ausente.

### Parte 2 — Caixa Branca

Este é o código que implementa as regras de negócio acima:

```javascript
function validarCadastro(idade, autorizacaoResponsavel, nomeUsuario) {
  if (idade < 13) {
    return { permitido: false, motivo: "Idade mínima não atingida" };
  }

  if (idade < 18 && !autorizacaoResponsavel) {
    return { permitido: false, motivo: "Autorização do responsável necessária" };
  }

  if (nomeUsuario.length < 3 || nomeUsuario.length > 15) {
    return { permitido: false, motivo: "Nome de usuário fora do tamanho permitido" };
  }

  return { permitido: true, motivo: null };
}
```

1. Identifique quantas decisões (`if`) existem na função e, para cada uma, o que precisa ser verdadeiro e o que precisa ser falso para "entrar" nela.
2. Monte o **menor conjunto de casos de teste** (valores de `idade`, `autorizacaoResponsavel` e `nomeUsuario`) que garanta **Cobertura de Decisão (Branch Coverage) completa** — ou seja, que cada `if` seja exercitado como verdadeiro **e** como falso pelo menos uma vez ao longo do conjunto.
3. Entregue como uma tabela indicando, para cada teste, **qual branch específica** ele cobre — igual ao exemplo de `calcularFrete` em [`tecnicas-de-teste.md`](./tecnicas-de-teste.md#exemplo-prático-1).

> Repare que o segundo `if` tem uma condição composta (`idade < 18 && !autorizacaoResponsavel`) — pense em quais valores fazem essa condição ser verdadeira e quais a fazem ser falsa (não são só os valores óbvios de idade).

### Parte 3 — Caixa Cinza

Durante o refinamento, um dev do time comentou duas coisas que não estão em nenhum critério de aceite formal:

```
Dev: "Ah, só um detalhe técnico — a verificação de nome de usuário
  duplicado é feita direto no banco, com uma constraint UNIQUE. E a
  verificação de CPF passa por um serviço externo de terceiros, que
  de vez em quando fica lento ou indisponível."
```

Com base **só** nessa informação (que não veio de olhar código, mas também não é algo que um usuário comum saberia), descreva **3 cenários de teste de Caixa Cinza** que você projetaria para essa funcionalidade — cenários que alguém testando 100% às cegas (caixa preta) provavelmente não pensaria em cobrir. Para cada um, explique **por que** o conhecimento parcial da arquitetura foi o que te levou a pensar naquele cenário.

---

## Restrições

- ❌ Na Parte 1, não pule as fronteiras — testar só "um valor bem no meio de cada partição" não é Análise de Valor Limite.
- ❌ Na Parte 2, não maximize o número de testes — o objetivo é o **menor conjunto possível** que ainda cubra todas as branches. Testes redundantes (que cobrem exatamente as mesmas branches que outro já cobre) não pontuam a mais.
- ❌ Na Parte 3, não descreva cenários que qualquer pessoa pensaria só olhando o formulário (isso seria caixa preta) — os 3 cenários precisam depender especificamente da informação que o dev deu.
- ✅ Pode usar sua própria notação de tabela, desde que todos os campos pedidos estejam presentes.

---

## Formato de entrega

Crie um repositório público no GitHub (ex: `trocafacil-tecnicas-de-teste`) com a seguinte estrutura:

```
trocafacil-tecnicas-de-teste/
├── README.md      ← breve descrição do desafio
└── entrega.md     ← Partes 1, 2 e 3 deste desafio
```

---

## Critérios de avaliação

- **Parte 1:** as partições e valores de fronteira estão corretos e completos? A tabela de decisão cobre todas as combinações relevantes de idade × autorização?
- **Parte 2:** o conjunto de testes realmente atinge 100% de cobertura de decisão? É o menor conjunto possível, ou tem redundância?
- **Parte 3:** os cenários de caixa cinza usam de fato o conhecimento repassado pelo dev (constraint UNIQUE, serviço externo instável), e não são só testes funcionais genéricos disfarçados?
- **Clareza geral:** outra pessoa do time conseguiria entender o raciocínio por trás de cada teste sem precisar te perguntar nada?

> O critério mais importante aqui não é "quantos testes você escreveu" — é se você consegue **justificar por que cada teste existe** e **a qual técnica ele pertence**. Um QA que sabe nomear a técnica por trás de cada teste consegue explicar decisões de cobertura para o time; um QA que só "testa bastante" sem saber classificar o que está fazendo tem mais dificuldade em argumentar quando alguém pergunta "por que você não testou X?".

---

## Referência

Revise [`tecnicas-de-teste.md`](./tecnicas-de-teste.md) antes de começar — em especial o exemplo de `calcularFrete` na seção de Caixa Branca, que serve de modelo para o formato de entrega da Parte 2 deste desafio.
