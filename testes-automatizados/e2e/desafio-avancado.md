# Desafio 10 — Suíte E2E Consolidada, Cross-Browser e com Performance da Própria Suíte

[![Dificuldade](https://img.shields.io/badge/Dificuldade-Avançada-red?style=for-the-badge)]()
[![Tema](https://img.shields.io/badge/Tema-Testes%20Automatizados-blueviolet?style=for-the-badge)]()

> **Pré-requisito:** ter concluído o [Desafio 08 (Fácil)](./desafio-facil.md) e o [Desafio 09 (Intermediário)](./desafio-intermediario.md) — este desafio é a consolidação dos dois, não um projeto novo.
>
> **Por que "Avançada":** aqui você não está só adicionando funcionalidade nova — está fazendo a suíte **medir a si mesma**. Vai precisar comparar comportamento entre browsers diferentes, identificar por que um teste é mais lento que outro, e decidir o que fazer com essa informação. É o tipo de trabalho que normalmente aparece quando uma suíte de automação já é grande o bastante para começar a incomodar o time (pipeline lenta, resultado diferente entre browsers).

---

## Objetivo

Consolidar tudo que você construiu nos desafios anteriores em uma única suíte, rodando em **múltiplos browsers**, e adicionar visibilidade sobre a **performance da própria suíte de testes** — não a performance do sistema sob teste, mas quanto tempo os seus testes levam para rodar, onde esse tempo é gasto, e como isso varia entre browsers.

---

## Por que isso importa

Depois que uma suíte E2E cresce, um novo tipo de problema aparece: a própria suíte vira gargalo. Uma pipeline que demora 40 minutos para rodar atrasa todo o Lead Time for Changes do time (veja [DORA Metrics](../../metricas-de-qualidade/metricas-de-qualidade.md#92-lead-time-for-changes-tempo-entre-o-commit-e-produção)) — e é comum que ninguém perceba isso até alguém medir. Da mesma forma, testes que passam no Chrome mas falham no Firefox (ou vice-versa) são bugs reais de compatibilidade que uma suíte rodando em um único browser nunca vai encontrar. Um QA que sabe instrumentar a própria suíte para expor esses dois problemas está entregando visibilidade que o time normalmente só descobre depois que já virou dor.

---

## A Tarefa

### 1. Consolide a suíte

Garanta que todos os cenários dos desafios anteriores continuam funcionando juntos, sem duplicação, usando a estrutura de Page Objects / dados do [Desafio 09](./desafio-intermediario.md):

- Login com os 4 usuários (data-driven)
- Compra completa (fluxo positivo)
- Login bloqueado (fluxo negativo)
- Cenários de `problem_user` e `performance_glitch_user`

### 2. Rode em diferentes browsers

Configure a suíte para rodar em **pelo menos 3 navegadores diferentes** (ex: Chromium, Firefox e WebKit, se estiver usando Playwright; Chrome, Firefox e Edge, se estiver usando Selenium/WebdriverIO). Ajuste a pipeline de CI do Desafio 09 para executar os navegadores **em paralelo** (ex: `matrix strategy` no GitHub Actions), não um atrás do outro sem necessidade.

### 3. Meça a performance da própria suíte

- Capture o tempo de execução de **cada teste individual** e da **suíte completa**, para cada browser.
- Identifique o teste mais lento da suíte e escreva uma hipótese de por que ele é o mais lento (ex: "o cenário do `performance_glitch_user` é o mais lento porque o site introduz atraso proposital nesse usuário — é esperado, mas vale isolar esse teste do restante para não distorcer o tempo total").
- Compare o tempo total da suíte entre os 3 browsers e aponte se algum navegador é consistentemente mais lento que os outros.
- Consolide tudo isso em **um único relatório**, comparando os 3 browsers lado a lado (pode ser uma tabela simples gerada manualmente a partir dos relatórios de cada execução, ou uma ferramenta que já agregue isso automaticamente).

### Entregável da Parte 3

```markdown
| Teste                          | Chromium | Firefox | WebKit |
|----------------------------------|----------|---------|--------|
| Login (standard_user)            | 1.2s     | 1.5s    | 1.8s   |
| Login (locked_out_user)          | 0.8s     | 0.9s    | 1.1s   |
| Compra completa                  | 4.3s     | 5.1s    | 6.0s   |
| performance_glitch_user          | 12.1s    | 12.8s   | 13.4s  |
| **Total da suíte**               | ...      | ...     | ...    |

Teste mais lento: performance_glitch_user
Hipótese: ...

Browser mais lento no geral: ...
Hipótese: ...
```

(Os valores acima são só exemplo de formato — use os tempos reais da sua execução.)

---

## Restrições

- ❌ Não vale rodar em um único browser e chamar de "cross-browser" — precisa ser, de fato, execução real nos 3+ navegadores configurados.
- ❌ Não vale rodar os browsers em sequência na pipeline sem justificativa — se o seu framework/CI suporta paralelismo (a maioria suporta via matrix), use.
- ❌ Não ignore diferenças de tempo entre browsers — mesmo que pequenas, registre-as no relatório da Parte 3.
- ✅ Pode isolar o teste do `performance_glitch_user` do cálculo de "tempo médio por teste" se ele for um outlier proposital do próprio site — mas precisa dizer que fez isso, e por quê.

---

## Formato de entrega

Continue no mesmo repositório dos desafios anteriores, adicionando:

```
saucedemo-e2e-basico/
├── .github/workflows/e2e.yml       ← atualizado com matrix de browsers
├── relatorio-performance-suite.md  ← tabela comparativa + hipóteses da Parte 3
└── (demais arquivos dos desafios anteriores)
```

---

## Critérios de avaliação

- **Cross-browser real** — os 3 browsers realmente executam e os resultados (inclusive tempos) vêm de execuções de verdade, não estimados?
- **Paralelismo na pipeline** — a matrix de browsers roda em paralelo, reduzindo o tempo total da pipeline em vez de multiplicá-lo?
- **Qualidade da análise de performance** — as hipóteses sobre o teste mais lento e o browser mais lento são plausíveis e conectadas ao que se sabe sobre o site (ex: o atraso proposital do `performance_glitch_user`)?
- **Consolidação** — existe um único relatório comparando os browsers, ou ficaram 3 relatórios soltos e desconectados?

> O objetivo final deste módulo (Fácil → Intermediário → Avançado) é sair sabendo não só escrever um teste E2E, mas também operar essa suíte como parte da esteira de entrega do time — sabendo se ela está rápida o suficiente, confiável o suficiente, e cobrindo os ambientes reais em que os usuários acessam o produto.

---

## Referência

Revise [`metricas-de-qualidade.md`](../../metricas-de-qualidade/metricas-de-qualidade.md) — em especial Lead Time for Changes e Flakiness Rate — para conectar a performance da sua suíte com o impacto real dela na esteira de entrega do time.
