# Técnicas de Teste: Caixa Preta, Caixa Branca e Caixa Cinza

## Introdução

Antes de escolher *qual* caso de teste escrever, existe uma decisão anterior: **quanto do funcionamento interno do sistema eu vou usar para decidir o que testar?** Essa é a diferença entre Caixa Preta, Caixa Branca e Caixa Cinza — não são "tipos de teste" como unitário ou de integração (veja [`tipos-de-teste.md`](../tipos-de-teste/tipos-de-teste.md)), e sim **níveis de visibilidade** que moldam como você projeta os casos de teste, em qualquer nível que for aplicado.

Um mesmo teste de API pode ser feito no estilo caixa preta (só olhando request e response) ou caixa branca (sabendo exatamente qual `if` do código está sendo exercitado). Entender essa diferença ajuda a escolher a abordagem certa para cada situação — e também a perceber quando vale a pena pedir para um dev mostrar o código antes de fechar o conjunto de testes.

---

## 1. Teste de Caixa Preta (Black Box Testing)

### O que é
Testa o sistema **sem conhecimento do código-fonte ou da estrutura interna** — só a partir de entradas e saídas observáveis. A pergunta é sempre "dado esse input, o que eu, como usuário ou consumidor da API, deveria ver como resultado?".

### Quando usar
- É a abordagem padrão de QA em testes funcionais, de sistema e de aceitação.
- Ótima para validar comportamento do ponto de vista do usuário, sem acoplar o teste a detalhes de implementação que podem mudar.
- É a abordagem que você já pratica ao escrever [casos de teste](../criacao-caso-testes/criacao-caso-teste.md) a partir de critérios de aceite.

### Técnicas comuns
- **Particionamento de Equivalência:** agrupar entradas que deveriam se comportar da mesma forma (ex: "qualquer e-mail sem `@` é inválido") e testar um representante de cada grupo, não todos.
- **Análise de Valor Limite:** testar os extremos de uma faixa aceita (ex: um campo que aceita de 1 a 100 → testar 0, 1, 100 e 101).
- **Tabela de Decisão:** mapear combinações de condições de entrada e as ações/resultados esperados para cada combinação.
- **Transição de Estados:** testar mudanças de estado do sistema (ex: pedido "criado" → "pago" → "enviado") e também transições inválidas (tentar "enviar" um pedido que nunca foi pago).
- **Teste de Casos de Uso:** testar fluxos completos de ponta a ponta como o usuário realmente os executaria.

### Vantagens
- Não exige conhecimento técnico de programação — qualquer QA, mesmo júnior, consegue aplicar.
- Testa o sistema pela perspectiva de quem realmente vai usá-lo.
- Os testes não quebram só porque o código foi refatorado por dentro (desde que o comportamento externo não mude).

### Desvantagens
- Pode deixar passar caminhos de código que nunca são exercitados por nenhuma combinação de entrada óbvia (código morto, exceptions silenciosas, lógica condicional escondida).
- Sem saber a estrutura interna, é fácil duplicar testes que, por dentro, exercitam exatamente a mesma coisa.

### Exemplo Prático
> **Cenário:** Campo de cupom de desconto que aceita códigos alfanuméricos de 5 a 10 caracteres.

```
Particionamento de Equivalência:
- Código válido cadastrado (ex: "PROMO10")
- Código com formato válido mas não cadastrado (ex: "ABCDE")
- Código com caracteres inválidos (ex: "PROMO@10")

Valor Limite (tamanho 5 a 10):
- 4 caracteres → deve rejeitar
- 5 caracteres → deve aceitar (limite inferior)
- 10 caracteres → deve aceitar (limite superior)
- 11 caracteres → deve rejeitar
```

Nada disso exige saber como o código valida o cupom por dentro — só o comportamento esperado da entrada.

---

## 2. Teste de Caixa Branca (White Box Testing)

### O que é
Testa o sistema **com conhecimento total do código-fonte** — a estrutura interna, os condicionais, os laços, os caminhos possíveis. A pergunta muda para "quais linhas, decisões e caminhos do código eu preciso exercitar para ter confiança de que ele está correto?".

### Quando usar
- Principalmente em testes unitários, escritos por quem desenvolveu (ou por QAs com forte conhecimento técnico/SDET).
- Quando é preciso garantir cobertura de lógica complexa, com muitas condições e combinações — algo que caixa preta sozinha dificilmente vai alcançar de forma sistemática.
- Para justificar, com dado objetivo, o quanto do código está de fato sendo exercitado (cobertura de teste).

### Técnicas comuns
- **Cobertura de Instrução (Statement Coverage):** garantir que toda linha do código seja executada ao menos uma vez.
- **Cobertura de Decisão/Branch (Branch Coverage):** garantir que cada condição (`if`, `else`) seja exercitada tanto no caminho verdadeiro quanto no falso.
- **Cobertura de Condição (Condition Coverage):** quando uma decisão tem múltiplas condições combinadas (`if (a && b)`), garantir que cada condição individual seja testada como verdadeira e falsa.
- **Cobertura de Caminho (Path Coverage):** garantir que combinações específicas de caminhos através do código sejam testadas — a mais rigorosa e mais cara de alcançar por completo.

### Vantagens
- Encontra código morto, condições que nunca são verdadeiras, exceptions nunca tratadas.
- Dá uma métrica objetiva de cobertura (veja [Cobertura de Testes](../metricas-de-qualidade/metricas-de-qualidade.md#4-cobertura-de-testes-test-coverage)).
- Excelente para lógica de cálculo, regras de negócio complexas e validações com muitas combinações.

### Desvantagens
- Exige conhecimento de programação e leitura de código — nem todo QA júnior começa com essa habilidade, e tudo bem.
- Cobertura alta não garante ausência de bugs de requisito: o código pode estar 100% coberto e ainda assim implementar a regra de negócio errada, se o requisito foi mal entendido (veja [`root-causes.md`](../metricas-de-qualidade/root-causes.md)).
- Testes ficam mais acoplados à implementação — uma refatoração interna, mesmo sem mudar comportamento, pode exigir reescrever testes.

### Exemplo Prático
> **Cenário:** Função que calcula o frete de um pedido.

```javascript
function calcularFrete(peso, distanciaKm, clienteVip) {
  let frete = distanciaKm * 0.5;

  if (peso > 20) {
    frete += 15;
  }

  if (distanciaKm > 100) {
    frete *= 1.2;
  }

  if (clienteVip) {
    frete *= 0.5;
  }

  return frete < 10 ? 10 : frete;
}
```

Para cobertura de decisão completa, cada um dos 4 `if`/condição precisa ser exercitado como verdadeiro **e** como falso ao menos uma vez ao longo do conjunto de testes — não em um único teste, mas na suíte como um todo:

| Teste | peso | distanciaKm | clienteVip | Branch coberta |
|---|---|---|---|---|
| 1 | 25 | 50 | false | `peso > 20` = true |
| 2 | 10 | 50 | false | `peso > 20` = false |
| 3 | 10 | 150 | false | `distanciaKm > 100` = true |
| 4 | 10 | 50 | true | `clienteVip` = true |
| 5 | 5 | 5 | false | `frete < 10` = true (frete mínimo) |

---

## 3. Teste de Caixa Cinza (Gray Box Testing)

### O que é
Um meio-termo: o testador tem **conhecimento parcial** da estrutura interna — sabe, por exemplo, que existe um cache, que o banco tem uma constraint de unicidade, que a validação acontece tanto no front quanto no back, ou qual é o contrato de uma API — mas não está lendo linha a linha do código como na caixa branca.

### Quando usar
- Testes de API, quando você conhece o contrato/schema mas não a implementação interna do endpoint.
- Testes de integração, quando você sabe que existem componentes por trás (cache, fila, banco) que podem se comportar de forma diferente do que a interface sugere.
- Quando você participa do refinamento e da conversa técnica (como visto em [`tipos-de-requisito.md`](../tipos-de-requisitos/tipos-de-requisito.md)) e absorve informação suficiente sobre a arquitetura para direcionar melhor seus testes, sem precisar ser quem escreve o código.

### Exemplos de conhecimento parcial usado
- Saber que existe uma constraint de unicidade no banco → testar tentativa de criar dois registros iguais em paralelo (condição de corrida), não só pela tela.
- Saber que existe cache → testar se uma alteração aparece imediatamente ou só depois de expirar/invalidar o cache.
- Saber que a validação existe tanto no frontend quanto no backend → testar direto na API, contornando o frontend, para garantir que o backend não confia cegamente na validação da tela.
- Saber que um valor vem de uma integração externa → simular respostas de erro ou timeout dessa integração, mesmo sem acesso ao código de quem implementou a chamada.

### Vantagens
- Combina o melhor dos dois mundos: continua testando comportamento observável (como caixa preta), mas direciona o esforço para os pontos de maior risco, guiado pelo conhecimento da arquitetura (como caixa branca).
- Não exige ler/entender o código-fonte inteiro — é a abordagem mais realista para o dia a dia de QA em times ágeis, que participam de refinamento e de conversas técnicas mas não necessariamente escrevem o código de produção.
- Ótima para encontrar bugs de integração entre camadas (UI, API, banco, cache, serviços externos).

### Desvantagens
- Depende de comunicação com o time — se ninguém compartilhar o conhecimento técnico (schema, arquitetura, dependências), o QA fica preso em caixa preta sem saber.
- Corre o risco de basear teste em uma suposição desatualizada sobre a arquitetura, se essa informação não for confirmada com o time.

### Exemplo Prático
> **Cenário:** Você sabe, por ter participado do refinamento técnico, que o sistema de agendamento do MedAgenda usa uma trava (lock) no banco para impedir dois agendamentos simultâneos no mesmo horário.

```
Teste de caixa preta (não usaria esse conhecimento):
- Agendar um horário disponível e verificar que foi confirmado.

Teste de caixa cinza (usa o conhecimento da trava):
- Disparar duas requisições de agendamento para o mesmo horário
  ao mesmo tempo (condição de corrida) e verificar que só uma
  é confirmada e a outra recebe erro de conflito — validando
  que a trava realmente funciona sob concorrência, não só
  que a regra "não pode duplicar" existe na tela.
```

---

## Resumo Visual

| | Caixa Preta | Caixa Branca | Caixa Cinza |
|---|---|---|---|
| Conhecimento interno usado | Nenhum | Total (código-fonte) | Parcial (arquitetura, contrato, schema) |
| Quem costuma aplicar | QA (qualquer nível) | Dev ou QA/SDET com forte conhecimento técnico | QA com contexto técnico do time |
| Foco principal | Comportamento observável | Estrutura e caminhos do código | Pontos de risco na integração entre camadas |
| Nível mais comum | Funcional, sistema, aceitação | Unitário | Integração, API |
| Exemplo de técnica | Particionamento de Equivalência, Valor Limite | Cobertura de Branch/Path | Teste de condição de corrida, teste direto na API |

---

> **Dica:** Nenhuma das três é "superior" às outras — elas respondem a perguntas diferentes. Um bug de regra de negócio mal entendida provavelmente só aparece em caixa preta (o comportamento observável está errado). Um bug de condição nunca testada só aparece em caixa branca (cobertura revela o gap). Um bug de integração entre camadas geralmente só aparece em caixa cinza (é preciso saber que a camada existe para pensar em testá-la). Um bom QA não escolhe uma — sabe quando aplicar cada uma.
