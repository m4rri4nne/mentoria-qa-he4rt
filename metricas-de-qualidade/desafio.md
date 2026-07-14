# Desafio 06 — Plano de Ação e Métricas para uma Crise de Qualidade (MedAgenda)

[![Dificuldade](https://img.shields.io/badge/Dificuldade-Avançada-red?style=for-the-badge)]()
[![Tema](https://img.shields.io/badge/Tema-Métricas%20e%20Causa%20Raiz-blueviolet?style=for-the-badge)]()

> **Pré-requisito:** leia [`metricas-de-qualidade.md`](./metricas-de-qualidade.md) e [`root-causes.md`](./root-causes.md) por completo antes de começar. Se você já resolveu o [Desafio 02 — Estratégia de Testes do MedAgenda](../tipos-de-teste/desafio.md), vai reconhecer o produto: é o mesmo MedAgenda, alguns meses depois de ir ao ar.
>
> **Por que "Avançada":** não existe uma causa raiz "certa" nem uma métrica "certa" pré-definida — você vai precisar ler dados de bugs reais (fictícios, mas realistas), agrupar por padrão, decidir prioridade e conectar cada ação a uma forma objetiva de medir se ela funcionou. É o tipo de raciocínio que se espera de quem participa de uma retro ou de uma reunião de crise como voz ativa, não só como quem registra os bugs.

---

## Objetivo

Colocar você no lugar de QA de um time Scrum em crise de qualidade. Você vai analisar um backlog real de bugs de produção, usar Root Cause Analysis para agrupar os problemas por padrão (e não tratar cada bug como um caso isolado), propor ações de melhoria específicas para cada causa raiz, e — principal ponto do desafio — **definir a métrica que vai comprovar se cada ação está funcionando**, não só assumir que "vai melhorar".

---

## O Contexto: MedAgenda em crise

O MedAgenda está no ar há 6 meses. O time trabalha em Scrum, sprints de 2 semanas. Nos últimos 3 sprints, o número de bugs de produção disparou, o tempo de resolução está alto, e o time começou a ter medo de fazer deploy — o que, ironicamente, está deixando os releases maiores e mais arriscados, porque tudo se acumula.

Clínicas parceiras (o cliente B2B do MedAgenda) começaram a reclamar, e o time de Customer Success já reportou que algumas estão avaliando migrar para o concorrente.

### Reunião de crise — trecho da conversa

```
Marina (PO): Gente, esse trimestre não pode se repetir. Três clínicas
  já falaram que estão insatisfeitas, e uma delas foi clara: "se isso
  continuar, a gente vai testar o sistema da concorrência".

Tech Lead (Rafael): O time também tá exausto. Toda sprint tem hotfix.
  A gente tá com medo de dar deploy porque toda vez que sobe algo,
  quebra outra coisa que ninguém esperava.

CS Lead (Bia): E o pior é que os bugs que os médicos mais reclamam
  são de dinheiro — reembolso errado, taxa retida errada. Isso mexe
  na confiança deles com a plataforma.

Rafael (Tech Lead): Uma parte disso é porque a regra de cancelamento
  mudou duas vezes nesse trimestre e cada mudança pareceu "isolada"
  no card, mas afetava três telas diferentes que ninguém tinha
  mapeado.

Marina (PO): E o refinamento andou meio corrido esse trimestre, sendo
  bem sincera. A gente tava com pressão de prazo e simplificou.

Bia (CS Lead): O que eu preciso levar pro cliente é: "isso vai
  melhorar e é assim que vamos saber que melhorou". Não adianta só
  dizer "vamos ter mais cuidado".
```

### Backlog de bugs dos últimos 3 sprints

Cada bug já foi investigado o suficiente para ter uma causa raiz identificada pelo time (usando as categorias descritas em [`root-causes.md`](./root-causes.md)):

| ID | Título | Severidade | Causa raiz | Dias até resolução | Reaberto? |
|---|---|---|---|---|---|
| BUG-201 | Reembolso de cancelamento calculado errado quando há cupom de desconto aplicado | Alta | Lógica de negócio incorreta | 6 | Sim |
| BUG-202 | Médico consegue ver prontuário de paciente vinculado a outro médico ao trocar filtro de especialidade | Crítica | Impacto não mapeado (regressão) | 9 | Não |
| BUG-203 | Lembrete de consulta (24h antes) não é enviado quando a consulta foi remarcada | Média | Requisito ambíguo | 4 | Não |
| BUG-204 | Dois pacientes conseguem agendar o mesmo horário simultaneamente no pico de segunda 8h | Crítica | Impacto não mapeado (regressão) | 11 | Sim |
| BUG-205 | Filtro de busca por avaliação passou a ignorar médicos com nota 5.0 após o último deploy | Alta | Impacto não mapeado (regressão) | 7 | Não |
| BUG-206 | Cálculo de taxa retida do médico ficou errado após ajuste na regra de cancelamento | Alta | Lógica de negócio incorreta | 5 | Sim |
| BUG-207 | Erro 500 ao aplicar cupom quando o gateway de pagamento está lento | Média | Falha de integração externa | 3 | Não |
| BUG-208 | Cancelamento com menos de 2h aparece "sem custo" pro paciente, mas cobra no financeiro | Alta | Impacto não mapeado (regressão) | 8 | Sim |
| BUG-209 | Relatório financeiro do admin mostra valores diferentes do relatório do médico | Alta | Lógica de negócio incorreta | 10 | Não |
| BUG-210 | Login falha para médicos com acento no nome após atualização recente | Baixa | Falta de teste automatizado | 2 | Não |
| BUG-211 | Consulta remarcada duas vezes perde vínculo com o pagamento original e aparece "pendente" pro paciente que já pagou | Crítica | Impacto não mapeado (regressão) | 12 | Sim |
| BUG-212 | Avaliação de consulta pode ser enviada antes da consulta acontecer | Média | Requisito ambíguo | 4 | Não |

### Métricas atuais do produto (últimos 3 sprints)

| Métrica | Valor atual | Valor de 2 trimestres atrás |
|---|---|---|
| Defect Escape Rate | 45% | 20% |
| Bug Reopen Rate | 42% | 15% |
| MTTR médio | 7,1 dias | 2,5 dias |
| Change Failure Rate | 35% dos deploys geram hotfix em até 48h | 12% |
| Deployment Frequency | 1x a cada 3 semanas | 1x por semana |
| NPS (clínicas parceiras) | -5 | 42 |

---

## A Tarefa

### Parte 1 — Agrupe e priorize por causa raiz

Usando a tabela de bugs, agrupe os 12 bugs por categoria de causa raiz e, para cada categoria, calcule:
- Quantidade de bugs e % do total
- Soma e média de dias até resolução
- % de bugs reabertos dentro daquela categoria

Aplique o raciocínio de Pareto (visto em [`root-causes.md`](./root-causes.md)) para apontar **as 3 causas raiz mais críticas** a atacar primeiro. Justifique — não vale só "essa tem mais bugs", conecte com severidade, tempo de resolução e reabertura também.

### Parte 2 — Planos de ação por causa raiz

Para **cada uma das 3 causas raiz** priorizadas na Parte 1, proponha **ao menos 2 ações concretas** de melhoria de processo ou técnica. As ações precisam ser específicas àquela causa — nada de "escrever mais testes" ou "ter mais cuidado" de forma genérica.

> Dica de direção (não é resposta pronta): pense em quando e como cada causa raiz poderia ter sido pega mais cedo — no refinamento? Numa checklist de impacto no PR? Num teste de regressão automatizado específico? Numa revisão cruzada de regra de negócio entre quem escreve e quem implementa?

### Parte 3 — Métrica para cada ação

Para **cada ação** proposta na Parte 2, defina:
- Qual métrica (de [`metricas-de-qualidade.md`](./metricas-de-qualidade.md), incluindo DORA) vai comprovar que a ação funcionou
- Uma meta numérica realista (ex: "reduzir Bug Reopen Rate de 42% para menos de 20%")
- Em quanto tempo essa métrica deveria ser reavaliada (ex: "após 2 sprints")

Monte isso como uma tabela:

```markdown
| Causa raiz | Ação proposta | Métrica de validação | Meta | Prazo de reavaliação |
|---|---|---|---|---|
| Impacto não mapeado (regressão) | ... | ... | ... | ... |
```

### Parte 4 — Como comunicar isso

Em poucas frases: como você apresentaria esse plano para o time (que está exausto e desmotivado) e, separadamente, o que a Bia (CS Lead) poderia levar para as clínicas insatisfeitas? A resposta para o cliente não pode ser "vamos ter mais cuidado" — precisa citar a métrica e a meta que provam que algo mudou.

---

## Restrições

- ❌ Não proponha "contratar mais QA" ou "contratar mais dev" como a solução para as 3 causas raiz — o desafio é sobre processo e prática, não sobre headcount.
- ❌ Não proponha ações genéricas demais para caber em qualquer causa raiz (ex: "ter mais atenção", "revisar melhor"). Se a ação serviria igualmente bem pra qualquer uma das 3 causas, ela não é específica o suficiente.
- ❌ Não deixe nenhuma ação sem uma métrica de validação com meta numérica — uma ação sem métrica associada não atende à Parte 3.
- ✅ Pode combinar mais de uma métrica para validar uma única ação, se fizer sentido.
- ✅ Pode (e deve) usar sua leitura da conversa da reunião de crise como contexto adicional para justificar as ações — a Marina mencionou refinamento corrido, por exemplo.

---

## Formato de entrega

Crie um repositório público no GitHub (ex: `medagenda-plano-crise-qualidade`) com a seguinte estrutura:

```
medagenda-plano-crise-qualidade/
├── README.md      ← breve descrição do desafio
└── entrega.md     ← Partes 1, 2, 3 e 4 deste desafio
```

---

## Critérios de avaliação

- **Qualidade da análise de Pareto** — a priorização das 3 causas raiz da Parte 1 é sustentada pelos números da tabela (quantidade, tempo, reabertura), não só por impressão?
- **Especificidade das ações** — cada ação da Parte 2 ataca a causa raiz específica, ou serviria para qualquer bug de qualquer natureza?
- **Rigor nas métricas** — cada ação da Parte 3 tem métrica, meta numérica e prazo, e a métrica escolhida realmente mede o que a ação promete resolver?
- **Viabilidade** — o plano é realista para um time Scrum em crise, cansado, com pressão de cliente, ou é uma lista de "fazer tudo perfeito ao mesmo tempo"?
- **Comunicação** — a resposta da Parte 4 mostra que o cliente insatisfeito receberia algo mais convincente do que uma promessa vaga?

> Uma entrega que separa bugs por causa raiz mas propõe a mesma ação genérica ("mais testes", "mais atenção") pra todas elas não demonstra RCA de verdade — demonstra que os bugs foram agrupados, mas não compreendidos. O objetivo é sair desse desafio sabendo justificar, com números, por que uma ação específica ataca uma causa raiz específica.

---

## Referência

Revise [`root-causes.md`](./root-causes.md) para o raciocínio de Pareto e as categorias de causa raiz, e [`metricas-de-qualidade.md`](./metricas-de-qualidade.md) — em especial a seção de DORA Metrics — para escolher as métricas de validação da Parte 3.
