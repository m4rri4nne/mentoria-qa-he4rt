# Root Cause Analysis (RCA) — Causa Raiz de Defeitos

## Introdução

Quando um bug aparece em produção, existem duas perguntas possíveis: "como eu corrijo isso agora?" e "por que isso aconteceu, de verdade?". A primeira resolve o incidente. A segunda é a única que evita que o mesmo tipo de bug volte a acontecer na semana seguinte, em outra tela, com outro nome.

**Root Cause Analysis (RCA)**, ou Análise de Causa Raiz, é o processo de ir além do sintoma até encontrar a origem real de um problema. Para um time de QA, RCA não é burocracia pós-incidente — é a ferramenta que transforma "apagar incêndio" em "reduzir o número de incêndios", e é uma das formas mais concretas de conectar o trabalho de teste com [métricas de qualidade](./metricas-de-qualidade.md) que o time realmente acompanha.

---

## Sintoma vs. Causa Raiz

- **Sintoma:** o que o usuário ou o time percebeu. Ex: "o cálculo de reembolso está errado para consultas canceladas".
- **Causa raiz:** a razão estrutural por trás do sintoma. Ex: "a regra de negócio de cancelamento foi alterada no card anterior, mas ninguém atualizou o cálculo de reembolso, porque essa dependência entre os dois fluxos não estava documentada nem mapeada como impacto".

Corrigir o sintoma (ajustar o cálculo daquele caso específico) resolve o bug de hoje. Corrigir a causa raiz (mapear dependências entre regras de negócio no refinamento, ou criar um teste de regressão que cubra as duas juntas) reduz a chance de o **próximo** card quebrar a mesma coisa de novo.

---

## Técnicas comuns de RCA

### 1. Os 5 Porquês (5 Whys)
Pergunte "por quê" repetidamente (geralmente 5 vezes) até chegar em algo estrutural, não superficial.

```
Bug: cliente foi cobrado duas vezes pelo mesmo pedido.

1. Por que o cliente foi cobrado duas vezes?
   → Porque o botão de "Finalizar compra" foi clicado duas vezes
     e as duas chamadas foram processadas.

2. Por que as duas chamadas foram processadas?
   → Porque o backend não tem nenhuma validação de idempotência
     na criação do pedido.

3. Por que não existe essa validação?
   → Porque o requisito não funcional de idempotência nunca foi
     levantado quando a funcionalidade de checkout foi especificada.

4. Por que não foi levantado?
   → Porque o time não tem um checklist de requisitos não funcionais
     para revisar em refinamentos de funcionalidades de pagamento.

5. Por que não existe esse checklist?
   → Porque, até agora, nenhum incidente tinha exposto essa lacuna
     de processo.

Causa raiz: ausência de checklist de requisitos não funcionais para
fluxos de pagamento — o bug do botão duplo é só o primeiro sintoma
visível disso.
```

### 2. Diagrama de Ishikawa (Espinha de Peixe)
Organiza possíveis causas em categorias (pessoas, processo, tecnologia, ambiente, dados, comunicação) para evitar o viés de parar na primeira explicação óbvia. Útil quando o bug não tem uma causa linear clara, mas parece resultado de vários fatores combinados.

### 3. Análise de Pareto
Ao acumular vários bugs ao longo do tempo, categorize por causa raiz e conte a frequência de cada categoria. Na prática, uma parcela pequena de causas costuma responder pela maior parte dos bugs (a regra dos "80/20") — e é ali que o esforço de melhoria de processo deve ser direcionado primeiro.

---

## Categorias comuns de causa raiz em software

| Categoria | Exemplo |
|---|---|
| **Requisito mal entendido ou ambíguo** | Critério de aceite não deixava claro o que acontecia num caso de borda, e cada dev implementou uma suposição diferente. |
| **Lógica de implementação incorreta** | Fórmula de cálculo de desconto foi implementada com a ordem de operações errada. |
| **Impacto não mapeado (regressão)** | Uma mudança em um módulo quebrou silenciosamente outro módulo que dependia dele, e ninguém tinha mapeado essa dependência. |
| **Falha de comunicação entre áreas** | Marketing mudou uma regra de cupom sem avisar o time técnico. |
| **Falta de cobertura de teste automatizado** | O caminho que quebrou nunca teve um teste de regressão associado — o bug já tinha acontecido antes e foi corrigido "na mão". |
| **Dados ou integração externa** | Um serviço de terceiros mudou o formato de resposta da API e o sistema não tratou o caso. |
| **Configuração de ambiente** | Bug só acontece em produção porque uma variável de ambiente está diferente da usada em homologação. |
| **Dívida técnica** | Um workaround "temporário" de meses atrás não suporta o volume ou o caso de uso atual. |

Vale reforçar: **"impacto não mapeado" e "lógica incorreta" não são a mesma causa raiz**, mesmo que ambas gerem bugs parecidos. A primeira é um problema de processo de refinamento/revisão (ninguém percebeu que X afetava Y); a segunda é um problema de implementação (o dev entendeu a regra, mas o código não faz o que a regra pede). As ações para resolver cada uma são completamente diferentes — por isso classificar corretamente a causa raiz importa tanto quanto encontrá-la.

---

## Como isso gera valor para o time

- **Prioriza o esforço certo:** sem RCA, o time reage a cada bug individualmente. Com RCA acumulada ao longo de sprints, fica claro se o problema real é de refinamento, de cobertura de teste, de comunicação entre squads ou de dívida técnica — e o time para de "chutar" onde investir.
- **Muda a conversa de retro:** em vez de "tivemos 12 bugs esse sprint", a conversa vira "7 desses 12 bugs vieram de impacto não mapeado em regras de cancelamento — isso é um padrão, não azar".
- **Conecta com métricas de forma objetiva:** categorizar causa raiz permite acompanhar ao longo do tempo se uma ação específica (ex: checklist de impacto no PR) está de fato reduzindo aquela categoria de bug — sem isso, qualquer ação de melhoria vira opinião, não dado.
- **Tira o foco de "quem errou" e coloca em "o que no processo permitiu esse erro":** um bug de lógica incorreta não é sobre "o dev que escreveu errado" — é sobre por que a revisão, o teste ou o refinamento não pegaram isso antes. RCA bem feita é sistêmica, não pessoal.
- **Evita o "bug fantasma que sempre volta":** causas raiz não tratadas tendem a gerar bugs reabertos ou variações do mesmo problema em outras telas — métrica que aparece diretamente na [Taxa de Reabertura de Bugs](./metricas-de-qualidade.md#6-taxa-de-reabertura-de-bugs-bug-reopen-rate).

---

> **Dica:** RCA só gera valor se virar ação — categorizar causa raiz e nunca revisitar esses dados é o mesmo que não ter feito a análise. O ciclo completo é: identificar causa raiz → agrupar por padrão → agir no processo → medir com uma métrica se a ação funcionou. É exatamente esse ciclo que o desafio deste módulo pede para você praticar.
