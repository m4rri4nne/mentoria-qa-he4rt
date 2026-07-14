# Desafio 11 — Primeiro Teste de API Automatizado (Funcional)

[![Dificuldade](https://img.shields.io/badge/Dificuldade-Fácil-brightgreen?style=for-the-badge)]()
[![Tema](https://img.shields.io/badge/Tema-Testes%20de%20API-blueviolet?style=for-the-badge)]()

> **Por que "Fácil":** o foco aqui é automatizar validações funcionais básicas de API — status code e corpo da resposta — na linguagem e no framework que você já conhece ou quer aprender. Testes de carga e performance vêm só no [Desafio 13 (Avançado)](./desafio-avancado.md); aqui é só sobre garantir que a API faz o que diz que faz.

---

## Objetivo

Automatizar testes **funcionais** de API contra um serviço REST real, cobrindo os métodos HTTP mais comuns (GET, POST, PUT, DELETE), validando tanto o **status code** quanto o **corpo da resposta** — na linguagem e no framework que você preferir (RestAssured, Postman/Newman, `requests` + `pytest`, Supertest, Karate, entre outros).

Este projeto vai ser a base dos próximos dois desafios deste módulo — capriche na estrutura, mesmo sendo o desafio "fácil".

---

## Por que isso importa

Testar API é diferente de testar UI: não existe elemento visual, tela ou botão — existe **contrato**. O que a API promete devolver, em qual formato, com qual status code, para qual entrada. Um teste de API que só confere o status code (`200 OK`) e ignora o corpo da resposta valida metade do contrato — é perfeitamente possível uma API retornar `200` com um corpo completamente errado. Aprender a validar os dois juntos, desde o primeiro projeto, evita esse hábito.

---

## A API: Restful Booker

Acesse a documentação: **[https://restful-booker.herokuapp.com/apidoc/index.html](https://restful-booker.herokuapp.com/apidoc/index.html)** — uma API pública, feita especificamente para prática de testes, que simula reservas de um hotel fictício.

Endpoints principais que você vai usar:

| Método | Endpoint | O que faz |
|---|---|---|
| `POST` | `/auth` | Gera um token de autenticação (usuário `admin`, senha `password123`) |
| `POST` | `/booking` | Cria uma reserva |
| `GET` | `/booking/{id}` | Busca uma reserva por ID |
| `PUT` | `/booking/{id}` | Atualiza uma reserva (exige token) |
| `DELETE` | `/booking/{id}` | Remove uma reserva (exige token) |
| `GET` | `/ping` | Health check da API |

---

## A Tarefa

Automatize os seguintes cenários, cada um com **asserção de status code e de corpo da resposta**:

1. **Autenticação bem-sucedida:** `POST /auth` com credenciais válidas → status `200` e corpo contendo um campo `token` não vazio.
2. **Autenticação malsucedida:** `POST /auth` com senha incorreta → valide o que a API realmente retorna. Não assuma o status code antes de checar — algumas APIs (essa incluída) fogem da convenção REST usual para autenticação inválida. Se o status vier diferente do que você esperava, **valide o corpo da resposta mesmo assim** e documente, em um comentário no teste, o que você esperava versus o que a API retornou.
3. **Criar reserva:** `POST /booking` com um payload válido (nome, sobrenome, preço, `depositpaid`, datas de check-in/check-out) → status `200` e corpo contendo um `bookingid` e os mesmos dados enviados.
4. **Buscar reserva existente:** `GET /booking/{id}` usando o `bookingid` criado no passo anterior → status `200` e corpo igual ao que foi criado.
5. **Buscar reserva inexistente:** `GET /booking/999999` (ID que não existe) → valide o status code retornado.
6. **Atualizar reserva:** `PUT /booking/{id}` (com o token do passo 1) alterando algum campo → status `200` e corpo refletindo o valor atualizado.
7. **Remover reserva:** `DELETE /booking/{id}` (com token) → valide o status retornado e, em seguida, repita o passo 5 para confirmar que a reserva não existe mais.

### Requisito extra

Ao final da execução da sua suíte, **nenhuma reserva de teste deve ficar "presa" na API** — toda reserva criada por um teste precisa ser removida (via `DELETE`) ao final dele, mesmo que a asserção do teste falhe no meio do caminho. É uma API pública e compartilhada por muita gente praticando o mesmo desafio — deixe-a como encontrou.

---

## Restrições

- ❌ Nenhum teste pode validar **só** o status code, ignorando o corpo da resposta (ou vice-versa) — os dois fazem parte do contrato.
- ❌ Não deixe reservas de teste órfãs na API — sempre limpe o que seu teste criou.
- ❌ Não escreva os testes numa ordem que dependa de execução sequencial manual (ex: rodar só o teste 4 sem ter rodado o 3 antes deveria falhar de forma clara, e idealmente cada teste deveria conseguir criar seu próprio dado de setup).
- ✅ Pode escolher qualquer linguagem/framework — o critério de avaliação não depende da tecnologia, e sim de como ela foi usada.

---

## Formato de entrega

Crie um repositório público no GitHub (ex: `restful-booker-api-testes`) com a estrutura mínima:

```
restful-booker-api-testes/
├── README.md              ← como instalar e rodar os testes
├── (arquivos de configuração do framework escolhido)
└── tests/ (ou equivalente)
    ├── auth.<ext>
    ├── booking-crud.<ext>
```

> Guarde o link deste repositório — você vai continuar a partir dele no [Desafio 12 (Intermediário)](./desafio-intermediario.md).

---

## Critérios de avaliação

- **Funciona de fato** — os testes rodam do zero (`git clone` + comando do README) sem passos manuais extras?
- **Validação de contrato completa** — status code **e** corpo da resposta são verificados em cada teste?
- **Limpeza de dados** — nenhuma reserva de teste fica órfã na API pública ao final da execução?
- **Observação crítica** — você percebeu e documentou algum comportamento da API que fugiu do que esperava (item 2), em vez de simplesmente ajustar a expectativa sem questionar?

---

## Referência

Veja [`tipos-de-requisito.md`](../../tipos-de-requisitos/tipos-de-requisito.md#9-requisitos-explícitos-vs-implícitos) para relembrar por que vale questionar um comportamento que não bate com o que parecia óbvio, em vez de só se adaptar a ele silenciosamente — é exatamente esse instinto que o cenário 2 deste desafio pede para você praticar.
