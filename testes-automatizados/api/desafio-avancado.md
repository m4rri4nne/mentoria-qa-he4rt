# Desafio 13 — Suíte de API Consolidada, com Testes de Carga e Performance

[![Dificuldade](https://img.shields.io/badge/Dificuldade-Avançada-red?style=for-the-badge)]()
[![Tema](https://img.shields.io/badge/Tema-Testes%20de%20API-blueviolet?style=for-the-badge)]()

> **Pré-requisito:** ter concluído o [Desafio 11 (Fácil)](./desafio-facil.md) e o [Desafio 12 (Intermediário)](./desafio-intermediario.md) — este desafio é a consolidação dos dois, mais uma dimensão nova: teste **não funcional** de carga e performance.
>
> **Por que "Avançada":** até aqui você validou **o que** a API faz (funcional). Agora você vai validar **como ela se comporta sob carga** — um tipo de requisito não funcional (veja [`tipos-de-requisito.md`](../../tipos-de-requisitos/tipos-de-requisito.md#4-requisito-não-funcional-non-functional-requirement)) que raramente aparece em critério de aceite, mas que costuma ser exatamente o tipo de problema que só aparece em produção, sob uso real — como visto no [Desafio 06](../../metricas-de-qualidade/desafio.md), onde um bug crítico de concorrência (dois pacientes agendando o mesmo horário no pico de tráfego) só surgiu depois de ir ao ar.

---

## Objetivo

Consolidar os testes funcionais dos desafios anteriores e adicionar testes de **carga e performance** contra a API Restful Booker, medindo tempo de resposta, taxa de erro e throughput sob diferentes níveis de concorrência — com metas objetivas (thresholds) que fazem o teste falhar se a API não se comportar dentro do esperado.

---

## Por que isso importa

Um endpoint pode passar em 100% dos testes funcionais e ainda assim ser inutilizável em produção, se ele fica lento ou começa a falhar assim que múltiplos usuários acessam ao mesmo tempo. Testes de carga respondem a uma pergunta que testes funcionais não respondem: **o sistema continua correto e responsivo quando várias pessoas usam ao mesmo tempo?** É o tipo de requisito não funcional que, como discutido em [`tipos-de-requisito.md`](../../tipos-de-requisitos/tipos-de-requisito.md), raramente é dito em voz alta no refinamento — mas que vira incidente real quando ninguém mediu antes.

---

## Antes de começar: rode a API localmente

**Importante:** os testes de carga deste desafio **não podem** ser executados contra a instância pública `restful-booker.herokuapp.com` — ela é compartilhada por muita gente praticando o mesmo desafio, e gerar carga artificial nela afetaria a experiência de outras pessoas (e pode te deixar bloqueado por rate limit).

O Restful Booker é open source. Consulte o repositório oficial do projeto (busque por "restful-booker" no GitHub, de Mark Winteringham) para instruções de como rodar sua própria instância localmente via Docker. Use essa instância local como alvo de **todos** os testes de carga deste desafio — os testes funcionais dos desafios anteriores podem continuar apontando para a instância pública.

---

## A Tarefa

### 1. Consolide a suíte funcional

Garanta que todos os cenários funcionais dos Desafios 11 e 12 continuam passando, agora com a URL base da API **parametrizável** (variável de ambiente ou arquivo de configuração) — de forma que a mesma suíte funcional rode tanto contra a instância pública quanto contra a sua instância local.

### 2. Escreva os testes de carga

Usando uma ferramenta de teste de carga (recomendado: **k6**, por ser leve e ter sintaxe em JavaScript; alternativas: JMeter, Gatling, Locust), crie cenários de carga contra a sua instância **local**:

- **Load test:** simule um número de usuários virtuais simultâneos (ex: 10, 20, 50 — escolha um valor e justifique) fazendo requisições de `GET /booking` e `GET /booking/{id}` continuamente por um período definido (ex: 30-60 segundos).
- Capture, no mínimo: tempo de resposta (p50, p95 e p99), taxa de erro (% de requisições com status de erro ou timeout), e throughput (requisições por segundo).

### 3. Defina thresholds (metas objetivas)

Configure o teste para **falhar automaticamente** se as métricas não atingirem metas que você definir, por exemplo:
- p95 do tempo de resposta abaixo de um valor X (ex: 500ms)
- Taxa de erro abaixo de Y% (ex: 1%)

Justifique os valores escolhidos — não precisam ser "perfeitos", mas precisam ser deliberados, não arbitrários.

### 4. (Bônus) Teste de stress

Aumente gradualmente o número de usuários virtuais até encontrar o ponto em que a API começa a degradar (tempo de resposta sobe de forma acentuada, ou taxa de erro aumenta). Documente esse ponto de quebra — ele é informação valiosa para o time saber até onde o sistema aguenta.

### 5. Consolide em um relatório único

Produza um documento (`relatorio-final.md`) que reúna:
- Resultado da suíte funcional (quantos testes passaram/falharam, contra qual ambiente)
- Resultado dos testes de carga (p50/p95/p99, taxa de erro, throughput, se os thresholds foram atingidos)
- Se aplicável, o ponto de quebra encontrado no teste de stress

### 6. Pipeline

Ajuste (ou mantenha separada) a pipeline de CI do Desafio 12:
- A suíte **funcional** continua rodando a cada `push`/PR, como antes.
- O teste de **carga** deve rodar em um job separado, com gatilho manual ou agendado (não a cada commit) — justifique, em uma frase no README, por que teste de carga não deveria rodar a cada push trivial da mesma forma que o funcional.

---

## Restrições

- ❌ Não rode nenhum teste de carga contra a instância pública `restful-booker.herokuapp.com` — apenas contra sua instância local.
- ❌ Não entregue resultados de carga sem threshold/meta definida — "rodei e o gráfico ficou assim" não é uma validação, é só uma observação.
- ❌ Não gere carga massiva de escrita (`POST`/`DELETE`) sem controle — se incluir esses métodos no teste de carga, tenha rotina de limpeza dos dados gerados, mesmo sendo ambiente local.
- ✅ Pode ajustar os valores de usuários virtuais/duração para o que sua máquina local aguentar — o objetivo é o raciocínio e a metodologia, não bater um número específico de carga.

---

## Formato de entrega

Continue no mesmo repositório dos desafios anteriores, adicionando:

```
restful-booker-api-testes/
├── .github/workflows/
│   ├── api-tests.yml        ← funcional, a cada push/PR
│   └── load-tests.yml       ← carga, manual ou agendado
├── load/
│   └── load-test.<ext>      ← script de carga (ex: k6)
└── relatorio-final.md       ← consolidado pedido no item 5
```

---

## Critérios de avaliação

- **Ambiente correto** — os testes de carga rodam contra a instância local, não a pública?
- **Cobertura de métricas** — p50/p95/p99, taxa de erro e throughput estão presentes e corretamente interpretados?
- **Thresholds justificados** — as metas definidas fazem sentido e o teste realmente falha quando não são atingidas?
- **Consolidação** — o relatório final conecta funcional e carga de forma clara, ou são dois documentos desconexos?
- **Raciocínio sobre pipeline** — a justificativa de separar carga do funcional na pipeline mostra entendimento real do trade-off (custo/tempo de rodar carga a cada commit), não só "porque foi pedido"?

> O objetivo final deste módulo (Fácil → Intermediário → Avançado) é sair sabendo não só automatizar validações de contrato, mas também enxergar a API como algo que precisa se comportar bem sob uso real — não só responder certo para uma única requisição isolada.

---

## Referência

Revise [`tipos-de-requisito.md`](../../tipos-de-requisitos/tipos-de-requisito.md#4-requisito-não-funcional-non-functional-requirement) para relembrar onde performance se encaixa como requisito não funcional, e o [Desafio 06](../../metricas-de-qualidade/desafio.md) para um exemplo real (fictício) de bug de concorrência que um teste de carga bem feito teria capturado antes da produção.
