# Métricas de Qualidade de Software

## Introdução

Métrica sem contexto é só um número. O valor de uma métrica de qualidade não está em reportá-la numa dashboard bonita — está em usá-la para responder a uma pergunta real: *estamos entregando com mais ou menos risco do que no mês passado? Onde exatamente o time está perdendo tempo? Essa ação que combinamos na retro está funcionando ou é só sensação?*

Para um QA, métricas cumprem um papel duplo. Primeiro, ajudam a enxergar **padrões que um bug isolado não mostra** — um bug de cálculo errado é um problema pontual; uma taxa de reabertura de 40% ao longo de 3 sprints é um problema sistêmico. Segundo, dão munição para conversas difíceis: é muito mais fácil defender "precisamos investir em automação de regressão" com um número que sustente o argumento do que com a sensação de "acho que a gente testa pouco isso".

Este material cobre as métricas de qualidade mais usadas no dia a dia e as **DORA Metrics**, que nasceram para medir performance de entrega de software, mas que têm muito a dizer sobre qualidade também.

---

## 1. Densidade de Defeitos (Defect Density)

### O que é
Quantidade de defeitos encontrados em relação ao tamanho do que foi entregue — geralmente por linha de código (KLOC), mas também por funcionalidade, história ou módulo.

### Como calcular
```
Densidade de Defeitos = Nº de defeitos encontrados / Tamanho da entrega
(ex: bugs por 1.000 linhas de código, ou bugs por história entregue)
```

### O que revela para o QA
Ajuda a comparar módulos entre si: se o módulo de pagamento tem densidade de defeitos 5x maior que o módulo de cadastro, isso é um sinal de onde concentrar esforço de teste e revisão — não é acaso, é um módulo estruturalmente mais frágil ou mais complexo.

---

## 2. Taxa de Vazamento de Defeitos (Defect Escape Rate)

### O que é
Percentual de defeitos que **escaparam** para produção em vez de serem pegos antes do release (em teste manual, automatizado, ou revisão).

### Como calcular
```
Defect Escape Rate = Defeitos encontrados em produção /
                      (Defeitos encontrados em produção + Defeitos encontrados antes do release)
```

### O que revela para o QA
É talvez a métrica mais direta sobre a **eficácia da estratégia de teste** do time. Uma taxa de vazamento subindo ao longo do tempo é sinal de que a cobertura de teste (ou a atenção dada a ela) não está acompanhando a complexidade que o produto está ganhando.

---

## 3. Eficiência de Remoção de Defeitos (DRE — Defect Removal Efficiency)

### O que é
Mede o quão eficiente o processo de teste é em capturar defeitos **antes** de chegarem ao cliente. É o complemento direto do Defect Escape Rate, mas expresso como eficiência.

### Como calcular
```
DRE = Defeitos encontrados antes do release /
      (Defeitos encontrados antes do release + Defeitos encontrados depois do release) × 100
```

### O que revela para o QA
Um DRE de 95% é considerado saudável na maioria dos contextos; abaixo de 80% costuma indicar que a estratégia de teste tem lacunas estruturais, não só falta de sorte num release específico.

---

## 4. Cobertura de Testes (Test Coverage)

### O que é
Percentual do sistema (código, requisitos ou fluxos) que está coberto por testes — pode ser cobertura de código (linhas/branches exercitadas), ou cobertura de requisitos (quantos critérios de aceite têm caso de teste associado).

### O que revela para o QA
Cobertura alta **não** significa qualidade alta — é possível ter 90% de cobertura de código com asserções fracas que não verificam nada relevante. O valor real dessa métrica para QA é identificar **lacunas** (o que está em 0% de cobertura) e cruzar isso com risco: uma regra de negócio crítica sem nenhum teste automatizado é uma bandeira vermelha, mesmo que a cobertura geral do sistema pareça boa.

---

## 5. Tempo Médio de Detecção (MTTD) e Tempo Médio de Resolução (MTTR)

### O que é
- **MTTD (Mean Time To Detect):** tempo médio entre o defeito ser introduzido (ou chegar em produção) e ser identificado.
- **MTTR (Mean Time To Resolve/Repair):** tempo médio entre o defeito ser identificado e ser corrigido/publicado em produção.

### O que revela para o QA
MTTD alto costuma apontar para falta de observabilidade ou de teste em produção (monitoramento, alertas, smoke tests pós-deploy). MTTR alto costuma apontar para outro tipo de problema: processo de triagem lento, dificuldade de reproduzir o bug, falta de causa raiz clara, ou medo de fazer deploy fora do ciclo normal. São dois problemas diferentes que pedem ações diferentes — por isso vale medir separado, não só como "tempo total até resolver".

---

## 6. Taxa de Reabertura de Bugs (Bug Reopen Rate)

### O que é
Percentual de bugs que foram marcados como corrigidos, mas voltaram a ser reportados (reabertos) depois.

### Como calcular
```
Bug Reopen Rate = Nº de bugs reabertos / Nº total de bugs corrigidos
```

### O que revela para o QA
É um dos sinais mais fortes de que a causa raiz não está sendo tratada — só o sintoma. Um time que corrige rápido mas reabre muito não tem um problema de velocidade, tem um problema de diagnóstico (veja [`root-causes.md`](./root-causes.md)).

---

## 7. Distribuição de Bugs por Severidade/Prioridade

### O que é
Quantos bugs abertos existem em cada nível de severidade (crítico, alto, médio, baixo) e como essa distribuição muda ao longo do tempo.

### O que revela para o QA
Um backlog de bugs pode "parecer" estável em número total e ainda assim estar piorando, se a proporção de bugs críticos estiver crescendo dentro desse total. Acompanhar só o número absoluto de bugs esconde essa deterioração.

---

## 8. Taxa de Automação e Flakiness Rate

### O que é
- **Taxa de Automação:** percentual de casos de teste (ou de cenários de regressão) que são executados de forma automatizada versus manual.
- **Flakiness Rate:** percentual de execuções de teste automatizado que falham de forma inconsistente, sem relação com um bug real (falso negativo).

### O que revela para o QA
Automação alta com flakiness alta é uma armadilha: o time *parece* coberto, mas na prática ninguém confia nos resultados e passa a ignorar falhas — inclusive as reais. Flakiness é uma métrica de **confiança na suíte de testes**, não só de manutenção técnica.

---

## 9. DORA Metrics

As **DORA Metrics** (DevOps Research and Assessment, hoje parte do Google Cloud) nasceram para medir performance de entrega de software, mas na prática funcionam como um termômetro de qualidade do processo como um todo — porque times que entregam rápido *e* com poucos incidentes são times com um pipeline de qualidade (testes, revisão, observabilidade) bem construído.

> **Fonte oficial:** [dora.dev](https://dora.dev/) — pesquisa contínua do DORA (Google Cloud) sobre as métricas e capacidades que diferenciam times de alta performance. Vale a leitura direta da fonte para acompanhar atualizações do modelo (como a inclusão de Reliability como quinta métrica).

### 9.1 Deployment Frequency (Frequência de Deploy)
**O que é:** com que frequência o time coloca código em produção.
**O que revela para o QA:** deploys menores e mais frequentes são mais fáceis de testar e, se algo der errado, mais fáceis de isolar a causa (menos mudanças acumuladas por deploy = menos variáveis para investigar). Uma frequência de deploy caindo pode ser sintoma de um time com medo de quebrar produção — geralmente por falta de confiança nos testes.

### 9.2 Lead Time for Changes (Tempo entre o commit e produção)
**O que é:** tempo desde que uma mudança é commitada até ela estar rodando em produção.
**O que revela para o QA:** um lead time alto muitas vezes esconde gargalo em etapas de validação manual demoradas ou retrabalho por bugs encontrados tarde no ciclo. É uma métrica que expõe se o teste está acontecendo cedo (shift-left) ou só no fim, represando a entrega.

### 9.3 Change Failure Rate (Taxa de Falha em Mudanças)
**O que é:** percentual de deploys que causam falha em produção (incidente, rollback, hotfix).
**O que revela para o QA:** é, de longe, a métrica DORA mais diretamente ligada à qualidade do processo de teste. Uma Change Failure Rate alta é o equivalente, em nível de deploy, ao Defect Escape Rate — e costuma ser o argumento mais forte para justificar investimento em testes automatizados de regressão antes de um release.

### 9.4 Time to Restore Service (Tempo de Restauração)
**O que é:** tempo médio para restaurar o serviço depois de um incidente em produção (rollback, hotfix, correção de configuração).
**O que revela para o QA:** mede a capacidade do time de reagir quando (não se) algo dá errado. QA pode influenciar diretamente essa métrica ajudando a desenhar testes de rollback, feature flags e smoke tests pós-deploy que aceleram a detecção e a decisão de reverter.

### 9.5 Reliability (Confiabilidade — métrica mais recente do modelo DORA)
**O que é:** o quanto o sistema atende às expectativas de disponibilidade e performance do usuário no dia a dia (não só durante incidentes).
**O que revela para o QA:** conecta qualidade percebida pelo usuário com o trabalho de teste não funcional (performance, disponibilidade) — é o lembrete de que qualidade não é só "não ter bug", é o sistema se comportar bem sob uso real.

---

## Resumo Visual

| Métrica                         | Pergunta que responde                                   | Sinal de alerta                              |
|----------------------------------|-----------------------------------------------------------|-----------------------------------------------|
| Densidade de Defeitos             | Onde os bugs se concentram?                                | Módulo específico muito acima da média         |
| Defect Escape Rate                | Nossos testes estão pegando os bugs antes do cliente?      | Subindo release após release                   |
| Defect Removal Efficiency (DRE)   | Quão eficiente é nosso processo de teste?                  | Abaixo de ~80%                                 |
| Cobertura de Testes               | O que está — e o que não está — protegido por teste?      | Regra crítica com 0% de cobertura              |
| MTTD                              | Quanto tempo até percebermos que algo quebrou?             | Alto → falta de observabilidade                |
| MTTR                              | Quanto tempo até corrigirmos depois de perceber?           | Alto → triagem lenta ou causa raiz mal entendida |
| Bug Reopen Rate                   | Estamos corrigindo a causa ou só o sintoma?                | Acima de ~15-20%                               |
| Distribuição por Severidade       | O backlog está piorando mesmo parecendo estável?          | Proporção de críticos crescendo                |
| Flakiness Rate                    | Dá pra confiar no resultado dos testes automatizados?      | Falhas inconsistentes e ignoradas pelo time    |
| Deployment Frequency (DORA)       | Estamos entregando em lotes pequenos e seguros?            | Caindo por medo de quebrar produção            |
| Lead Time for Changes (DORA)      | Da mudança até produção, onde o tempo está sendo gasto?    | Gargalo em validação manual tardia             |
| Change Failure Rate (DORA)        | Quantos dos nossos deploys quebram algo?                   | Acima de ~15%                                  |
| Time to Restore Service (DORA)    | Quão rápido reagimos quando algo quebra?                   | Horas/dias em vez de minutos                   |

---

> **Dica:** Nenhuma métrica dessa lista deve ser olhada isolada. Um time pode ter Deployment Frequency alta e Change Failure Rate alta ao mesmo tempo — o que significa que está entregando rápido, mas quebrando produção com frequência. A leitura útil está sempre no cruzamento entre métricas, nunca em uma única linha de uma dashboard.
