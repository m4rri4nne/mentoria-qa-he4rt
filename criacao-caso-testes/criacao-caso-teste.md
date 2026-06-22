# Criação de Casos de Teste

## Introdução

Escrever um caso de teste não é só "descrever o que vou testar" — é escolher um **formato** que comunique a intenção certa para a pessoa certa, e usar uma **técnica** que garanta que você está testando o que realmente importa, e não só o que é óbvio.

Esses são dois problemas diferentes:

- **Formato** → *como* o caso de teste é escrito e organizado (Step-by-Step, BDD, checklist...).
- **Metodologia / técnica de design** → *o que* vai dentro de cada caso de teste (quais valores, cenários e combinações testar).

Este material cobre os dois.

---

## 1. Formato Step-by-Step (Passo a Passo)

### O que é
O formato clássico de caso de teste: um documento (ou linha em uma ferramenta como TestRail, Zephyr, Xray) com identificação, pré-condições, passos numerados e resultado esperado.

### Estrutura típica

```
ID: TC-042
Título: Login com credenciais válidas
Pré-condições: Usuário cadastrado e ativo no sistema
Prioridade: Alta

Passos:
1. Acessar a tela de login
2. Inserir e-mail válido no campo "E-mail"
3. Inserir senha correta no campo "Senha"
4. Clicar no botão "Entrar"

Resultado esperado:
Usuário é redirecionado para a tela inicial (dashboard)
e seu nome aparece no canto superior direito.
```

### Quando usar
- Testes manuais de regressão, executados repetidamente por QAs diferentes (a explicitação dos passos remove ambiguidade).
- Times distribuídos ou terceirizados, onde quem escreve o caso não é quem executa.
- Contextos regulados, onde é preciso rastreabilidade clara entre requisito → caso de teste → execução (ex: saúde, financeiro).
- Onboarding de QAs juniores, que ainda precisam de passos explícitos para não deixar cenários passarem.

### Vantagens
- Zero ambiguidade: qualquer pessoa consegue executar sem precisar perguntar "e agora, o que eu clico?".
- Reprodutibilidade alta — essencial para reportar bugs ("seguindo os passos X, Y, Z, o resultado foi diferente do esperado").
- Mapeia bem para ferramentas de gestão de teste e métricas de cobertura/execução.
- Bom para auditoria e compliance.

### Desvantagens
- Caro de manter: qualquer mudança de UI (um botão que muda de nome, uma tela que muda de ordem) obriga a revisar todos os casos relacionados.
- Verboso — times pequenos e ágeis sentem o atrito de escrever isso para tudo.
- Tende a ficar focado em *interface*, não em *comportamento/regra de negócio*. Dois casos podem testar a mesma regra com passos de UI completamente diferentes.
- Pouco legível para PO/negócio, que normalmente não quer saber em qual botão clicar, só qual comportamento é esperado.

---

## 2. Formato BDD (Behavior Driven Development)

### O que é
Casos de teste (cenários) escritos em linguagem natural estruturada — geralmente a sintaxe **Gherkin**, com os blocos **Dado / Quando / Então** (*Given / When / Then*). O foco é descrever **comportamento do sistema do ponto de vista do usuário**, não passos de interface.

### Estrutura típica

```gherkin
Funcionalidade: Login de usuário

  Cenário: Login com credenciais válidas
    Dado que o usuário possui uma conta ativa
    Quando ele informa e-mail e senha corretos
    E clica em "Entrar"
    Então ele deve ser redirecionado para o dashboard
    E seu nome deve aparecer no canto superior direito

  Cenário: Login com senha incorreta
    Dado que o usuário possui uma conta ativa
    Quando ele informa o e-mail correto e uma senha incorreta
    Então o sistema deve exibir a mensagem "E-mail ou senha inválidos"
    E o usuário deve permanecer na tela de login
```

### Quando usar
- Quando QA, Dev e PO precisam construir entendimento **junto**, antes de codar (a prática conhecida como *Three Amigos* / especificação por exemplo).
- Quando o cenário vai ser **automatizado como teste de aceitação** (Cucumber, SpecFlow, Behave) e reaproveitado como documentação viva.
- Quando o público que lê o caso de teste inclui pessoas não técnicas (PO, stakeholders) e a clareza de negócio importa mais que o detalhe de clique.
- Em times que adotam BDD como prática de descoberta de requisitos, não só de teste.

### Vantagens
- Legível por qualquer pessoa do time, técnica ou não — funciona como documentação viva e sempre atualizada (se os testes passam, a doc está correta).
- Foca no comportamento e na regra de negócio, não na implementação de tela — mais resiliente a mudanças de UI.
- Caminho direto para automação: o mesmo texto que documenta o comportamento pode disparar o teste automatizado.
- Fomenta colaboração antecipada entre QA, Dev e PO, reduzindo retrabalho por requisito mal entendido.

### Desvantagens
- Sem disciplina, os cenários ficam genéricos demais (perdem valor de teste) ou viram "scripts" gigantes com Dado/Quando/Então repetidos manualmente (perdem o valor de comunicação).
- Exige investimento inicial: treinamento do time, definição de steps reutilizáveis, ferramenta de automação configurada.
- Step definitions mal organizadas geram duplicação e um "glue code" difícil de manter.
- Para casos muito detalhados de UI (ex: validar 15 variações de máscara de campo), o formato Gherkin fica desproporcionalmente verboso comparado a uma tabela Step-by-Step.

---

## 3. Outros formatos (rápida menção)

- **Checklist** — lista de verificações rápidas, sem passo a passo formal. Bom para smoke test e sanity check, ruim para rastreabilidade.
- **Caso de uso textual (ator → ação → resposta do sistema)** — meio-termo entre Step-by-Step e BDD, comum em especificações de requisitos mais antigas.
- **Charter exploratório** — não é bem um "caso de teste", e sim um objetivo de exploração (ex: *"por 30 minutos, tente quebrar o fluxo de checkout usando dados inválidos"*). Usado em teste exploratório, complementa (não substitui) os formatos acima.

---

## Comparativo: Step-by-Step vs BDD

| Aspecto                          | Step-by-Step                          | BDD / Gherkin                         |
|-----------------------------------|----------------------------------------|----------------------------------------|
| Público principal                | QA, execução manual detalhada          | QA, Dev, PO — colaboração              |
| Legibilidade para negócio         | Baixa                                  | Alta                                   |
| Foco                              | Interface / sequência de cliques       | Comportamento / regra de negócio       |
| Caminho para automação            | Indireto (script separado do caso)     | Direto (mesmo texto vira teste)        |
| Resiliência a mudanças de UI      | Baixa                                  | Alta                                   |
| Custo de manutenção               | Alto                                   | Médio (se bem escrito)                 |
| Risco se mal aplicado             | Vira burocracia desatualizada          | Vira "script" verboso ou genérico demais |

> Na prática, muitos times usam os dois: BDD para os critérios de aceite e fluxos principais (e como base de automação), e Step-by-Step para casos de regressão manual mais granulares ou exploração detalhada de UI.

---

## Metodologias para Criação de Casos de Teste (Técnicas de Design de Teste)

Formato é *como* escrever. Técnica de design é *o que* testar. As técnicas abaixo ajudam a decidir quais cenários, valores e combinações realmente valem um caso de teste — evitando tanto a omissão de cenários críticos quanto o excesso de casos redundantes.

### 1. Particionamento de Equivalência (Equivalence Partitioning)
Agrupa entradas que devem se comportar da mesma forma, testando **um representante de cada grupo** em vez de todos os valores possíveis.
> Exemplo: campo de idade aceita 18–65. Partições: `< 18` (inválida), `18–65` (válida), `> 65` (inválida). Testa-se um valor de cada partição, não os 48 valores do meio.

### 2. Análise de Valor Limite (Boundary Value Analysis)
Complementa a anterior: bugs se concentram nas **bordas** das partições, não no meio.
> Exemplo: para a faixa 18–65, testar 17, 18, 65 e 66 — não 40.

### 3. Tabela de Decisão (Decision Table Testing)
Mapeia combinações de condições de entrada e as ações/resultados esperados para cada combinação. Ótima quando há **múltiplas regras de negócio combinadas** (ex: descontos, permissões).
> Exemplo: desconto = f(é cliente VIP?, cupom válido?, carrinho > R$200?) — a tabela força você a cobrir todas as combinações relevantes, não só as "óbvias".

### 4. Transição de Estado (State Transition Testing)
Modela o sistema como estados e transições, testando mudanças de estado válidas e inválidas.
> Exemplo: pedido passa por `Criado → Pago → Enviado → Entregue`. Testar transição inválida (`Criado → Entregue` direto) é tão importante quanto testar o caminho feliz.

### 5. Teste Baseado em Casos de Uso (Use Case Testing)
Deriva casos de teste a partir do fluxo principal e dos fluxos alternativos/de exceção documentados no caso de uso (ou na user story).
> Exemplo: "Realizar checkout" → fluxo principal (pagamento aprovado), fluxos alternativos (pagamento recusado, cupom expirado, estoque insuficiente).

### 6. Teste Exploratório (Exploratory Testing)
Sem script fixo: o testador explora o sistema guiado por um *charter* (objetivo), usando experiência e intuição para encontrar problemas que cenários pré-definidos não cobririam.
> Bom complemento aos formatos estruturados — não os substitui, cobre o que eles não preveem.

### 7. Teste Baseado em Risco (Risk-Based Testing)
Prioriza o que testar (e com que profundidade) com base no **impacto** de uma falha e na **probabilidade** de ela ocorrer, não em cobrir 100% de tudo igualmente.
> Exemplo: módulo de pagamento recebe mais casos de teste e mais profundidade do que a tela "Sobre nós", porque o risco de falha é muito maior.

### 8. Pairwise / Teste Combinatório
Quando há muitas variáveis combináveis (navegador × sistema operacional × tipo de usuário...), testar todas as combinações é inviável. Pairwise garante que **todo par de valores** seja testado ao menos uma vez, com um número de casos muito menor que o total combinatório.
> Exemplo: 3 navegadores × 3 sistemas operacionais × 2 tipos de usuário = 18 combinações totais, mas pairwise pode cobrir os pares relevantes com ~6-9 casos.

### 9. Example Mapping
Técnica de discovery (geralmente conduzida em conjunto com BDD, na conversa entre QA/Dev/PO) onde se mapeia: **regra de negócio → exemplos concretos → perguntas/dúvidas abertas**. Os exemplos resultantes costumam virar diretamente os cenários Gherkin.
> Liga diretamente a técnica de design ao formato BDD: primeiro mapeia-se os exemplos, depois eles são escritos como `Dado/Quando/Então`.

---

## Resumo Visual

| Técnica                        | Quando aplicar                                         | Formato de saída mais comum |
|---------------------------------|----------------------------------------------------------|------------------------------|
| Particionamento de Equivalência | Campos/entradas com faixas de valores válidos/inválidos  | Step-by-Step                |
| Valor Limite                    | Junto com partição de equivalência, em bordas de faixas  | Step-by-Step                |
| Tabela de Decisão                | Regras de negócio com múltiplas condições combinadas     | Step-by-Step / BDD          |
| Transição de Estado              | Fluxos com estados bem definidos (pedido, ticket, etc.)  | BDD / Step-by-Step          |
| Caso de Uso                      | Funcionalidades com fluxo principal + alternativos       | BDD                         |
| Exploratório                     | Qualquer momento, especialmente pré-release              | Charter (texto livre)       |
| Baseado em Risco                 | Priorização de esforço de teste, projetos com tempo curto| Qualquer                    |
| Pairwise                         | Muitas variáveis combináveis (browser, OS, perfil...)    | Step-by-Step                |
| Example Mapping                  | Descoberta de requisitos junto com Dev/PO                | BDD                         |

---

> **Dica:** formato e técnica são escolhas independentes — você pode (e deve) usar uma técnica como Tabela de Decisão para *descobrir* os cenários, e depois escrevê-los tanto em BDD (para automação/critério de aceite) quanto em Step-by-Step (para regressão manual detalhada), conforme o público que vai consumir aquele caso de teste.
