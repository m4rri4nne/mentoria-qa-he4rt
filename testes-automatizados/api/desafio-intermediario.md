# Desafio 12 — Deixe o Teste de API Robusto, com Relatório e Pipeline

[![Dificuldade](https://img.shields.io/badge/Dificuldade-Intermediária-orange?style=for-the-badge)]()
[![Tema](https://img.shields.io/badge/Tema-Testes%20de%20API-blueviolet?style=for-the-badge)]()

> **Pré-requisito:** ter concluído o [Desafio 11 (Fácil)](./desafio-facil.md) — este desafio parte **do mesmo projeto**, não começa do zero.
>
> **Por que "Intermediária":** agora entram validação de contrato mais rigorosa (schema), design de casos com técnica de teste (não só o caminho feliz) e a exigência de que tudo rode sozinho, com relatório e pipeline — sem depender de alguém rodar na sua máquina.

---

## Objetivo

Pegar o projeto de testes de API do [Desafio 11](./desafio-facil.md) e evoluí-lo em três frentes: tornar a suíte **mais robusta** (validação de schema, mais cenários de erro, técnica de design aplicada), adicionar **relatórios de execução**, e colocar tudo **rodando automaticamente em uma pipeline de CI**.

---

## Por que isso importa

Validar campo por campo no corpo da resposta funciona, mas não escala — e não protege contra mudanças de **tipo** de um campo (um `totalprice` que devia ser número virar string, por exemplo, sem que nenhuma asserção de valor específico perceba). Validação de schema resolve isso testando a **forma** do contrato, não só valores pontuais. Combinada com relatório e pipeline, uma suíte de API bem construída é uma das formas mais baratas de um time detectar quebra de contrato antes que ela chegue no time que consome aquela API — muitas vezes um time diferente, numa empresa diferente.

---

## A Tarefa

### 1. Torne a suíte mais robusta

- **Validação de schema:** adicione validação de JSON Schema para o corpo da resposta de `GET /booking/{id}` e `POST /booking` — garantindo tipo, obrigatoriedade e formato de cada campo (`firstname`/`lastname`: string; `totalprice`: número; `depositpaid`: booleano; `bookingdates.checkin`/`checkout`: data no formato esperado), não só a presença deles.
- **Aplique Particionamento de Equivalência e Valor Limite** (veja [`tecnicas-de-teste.md`](../../tecnicas-de-teste/tecnicas-de-teste.md#1-teste-de-caixa-preta-black-box-testing)) para o campo `totalprice` do `POST /booking`: teste valor negativo, zero, e um valor muito alto — documente o que a API faz em cada caso (aceita? rejeita? trunca?).
- **Teste datas inconsistentes:** crie uma reserva com `checkout` anterior ao `checkin` e documente o comportamento da API — ela valida essa regra de negócio, ou aceita qualquer combinação de datas?
- **Teste autenticação ausente/inválida:** tente `PUT` e `DELETE` sem token, e com um token inválido/expirado — valide o status code e o corpo retornado nos dois casos.
- **Organize um "cliente de API" reutilizável:** separe a construção de requisições (URL, headers, payload) das asserções dos testes — se a base da URL ou o header de autenticação mudar, você deve precisar editar em **um lugar só**.

### 2. Adicione relatórios

- Configure um gerador de relatório (ex: Newman HTML Reporter, Allure, ExtentReports, `pytest-html`, ReportPortal — o que for compatível com seu framework) que mostre: quais testes passaram/falharam, o tempo de resposta de cada chamada, e o payload/response de cada requisição.
- Force **propositalmente** um teste a falhar (ex: altere uma validação de schema para exigir um campo que não existe), gere o relatório, capture um print mostrando a falha, e depois reverta a alteração. Esse print faz parte da sua entrega.

### 3. Rode em uma pipeline

- Configure uma pipeline de CI (recomendado: GitHub Actions) que rode toda a suíte automaticamente a cada `push` ou `pull request`.
- A pipeline deve publicar o relatório gerado como artifact do workflow.
- A pipeline **precisa falhar** quando um teste falha.

---

## Restrições

- ❌ Validação de schema não substitui validação de valor onde valor específico importa (ex: o `bookingid` retornado precisa ser o mesmo usado na busca) — uma complementa a outra, não vale trocar uma pela outra.
- ❌ Não deixe reservas de teste órfãs na API (mesma regra do Desafio 11) — reforce isso especialmente nos novos cenários de erro, que são os que mais fácil "esquecem" o teardown.
- ❌ Relatório sem a evidência de falha capturada (print pedido no item 2) não atende ao requisito.
- ❌ Pipeline que roda mas não falha quando um teste quebra não conta como pipeline funcional.

---

## Formato de entrega

Continue no **mesmo repositório** do Desafio 11, adicionando:

```
restful-booker-api-testes/
├── README.md                       ← atualizado: como rodar, como ver o relatório, status da pipeline
├── .github/workflows/api-tests.yml ← pipeline de CI
├── schemas/                        ← JSON Schemas usados na validação
├── client/ (ou equivalente)        ← camada reutilizável de requisições
├── tests/
└── evidencia-falha-capturada.png   ← print pedido no item 2
```

---

## Critérios de avaliação

- **Validação de schema** — está corretamente configurada e realmente pegaria uma quebra de tipo/contrato?
- **Técnica de design aplicada** — os casos de `totalprice` e datas inconsistentes usam de fato Particionamento de Equivalência/Valor Limite, com fronteiras corretas?
- **Cobertura de erro** — autenticação ausente/inválida está coberta, com asserção de status e corpo?
- **Estrutura** — existe uma camada reutilizável de cliente de API, ou cada teste monta a requisição do zero?
- **Relatório e pipeline** — funcionam como descrito, com a evidência de falha exigida?

> Descobrir que a API aceita um `totalprice` negativo (se for o caso) não é "a API estar quebrada para o seu teste" — é um achado real. Documentar isso com clareza é tão parte da entrega quanto o teste em si.

---

## Referência

Revise [`tecnicas-de-teste.md`](../../tecnicas-de-teste/tecnicas-de-teste.md) para as técnicas de Caixa Preta usadas neste desafio, e [`metricas-de-qualidade.md`](../../metricas-de-qualidade/metricas-de-qualidade.md#9-dora-metrics) para relembrar por que pipeline e relatório visível importam além do exercício técnico. Quando terminar, siga para o [Desafio 13 (Avançado)](./desafio-avancado.md), que consolida este projeto com testes de carga e performance.
