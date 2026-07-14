# Desafio 08 — Primeiro Teste E2E Automatizado

[![Dificuldade](https://img.shields.io/badge/Dificuldade-Fácil-brightgreen?style=for-the-badge)]()
[![Tema](https://img.shields.io/badge/Tema-Testes%20Automatizados-blueviolet?style=for-the-badge)]()

> **Por que "Fácil":** o foco aqui é só sair do zero — ter um teste E2E automatizado rodando de ponta a ponta, na linguagem e no framework que você já conhece ou quer aprender. Nada de relatório bonito, pipeline ou múltiplos browsers ainda: isso vem nos próximos desafios ([Intermediário](./desafio-intermediario.md) e [Avançado](./desafio-avancado.md)), que reaproveitam o projeto que você criar aqui.

---

## Objetivo

Automatizar, do zero, um fluxo completo de ponta a ponta (E2E) em um site real, usando a **linguagem de programação e o framework de automação que você se sentir mais confortável** — Playwright, Cypress, Selenium WebDriver, WebdriverIO, TestCafe, Robot Framework, ou qualquer outro que suporte o navegador.

Este projeto vai ser a base dos próximos dois desafios deste módulo — capriche na estrutura, mesmo sendo o desafio "fácil".

---

## Por que isso importa

Escrever casos de teste manuais (como no [Desafio 03](../../criacao-caso-testes/desafio-iniciante.md)) e automatizá-los são habilidades relacionadas, mas não iguais. Automação exige pensar em seletores estáveis, esperas (waits) corretas, isolamento entre testes e uma estrutura de projeto que não vire um `arquivo_de_testes.js` gigante e ilegível depois do décimo teste. É uma base técnica que todo QA que trabalha com automação precisa construir cedo — e é mais fácil construir bem desde o primeiro projeto do que refatorar depois.

---

## O Site: SauceDemo

Acesse: **[https://www.saucedemo.com](https://www.saucedemo.com)** — o mesmo site usado nos desafios de [CSS Selectors](../../css-selectors/desafio.md) e [Casos de Teste](../../criacao-caso-testes/desafio-iniciante.md).

| Usuário | Senha | Comportamento |
|---|---|---|
| `standard_user` | `secret_sauce` | Funciona normalmente |
| `locked_out_user` | `secret_sauce` | Login bloqueado, exibe erro |
| `problem_user` | `secret_sauce` | Login funciona, mas há bugs visuais no catálogo |
| `performance_glitch_user` | `secret_sauce` | Login funciona, mas com lentidão proposital |

---

## A Tarefa

Automatize **os 2 fluxos abaixo**, cada um como um teste independente (não dependa da ordem de execução entre eles):

### 1. Fluxo positivo — Compra completa
1. Fazer login com `standard_user`
2. Adicionar 2 produtos ao carrinho
3. Verificar que o ícone do carrinho mostra a contagem "2"
4. Ir para o carrinho e prosseguir para o checkout
5. Preencher os dados do checkout (nome, sobrenome, CEP) e finalizar
6. Verificar que a mensagem de confirmação do pedido ("Thank you for your order!") aparece

### 2. Fluxo negativo — Login bloqueado
1. Tentar fazer login com `locked_out_user`
2. Verificar que a mensagem de erro de usuário bloqueado é exibida
3. Verificar que o usuário **não** é redirecionado para a tela de produtos

### Requisitos mínimos do projeto

- Cada teste deve ter **asserções reais** (verificações de texto, contagem, URL ou elemento visível) — não vale só "clicar em tudo" sem verificar nada no final.
- Os dois testes devem rodar de forma independente (rodar só o teste 2 não pode depender do teste 1 ter rodado antes).
- Inclua um `README.md` explicando como instalar dependências e rodar os testes localmente (comando exato).

---

## Restrições

- ❌ Não use `sleep`/espera fixa em milissegundos como estratégia principal de sincronização (ex: `sleep(3000)`) — use os mecanismos de espera do próprio framework (esperar por elemento visível, esperar por navegação, etc.). Frameworks modernos (Playwright, Cypress, WebdriverIO) fazem isso automaticamente na maioria dos casos.
- ❌ Não deixe credenciais ou dados sensíveis hardcoded se você pretende reaproveitar este projeto publicamente além do desafio — não há dado sensível real aqui, mas é uma boa prática para carregar desde o primeiro projeto.
- ✅ Pode escolher qualquer linguagem/framework — o critério de avaliação não depende da tecnologia escolhida, e sim de como ela foi usada.

---

## Formato de entrega

Crie um repositório público no GitHub (ex: `saucedemo-e2e-basico`) com a estrutura mínima:

```
saucedemo-e2e-basico/
├── README.md              ← como instalar e rodar os testes
├── (arquivos de configuração do framework escolhido)
└── tests/ (ou equivalente)
    ├── compra-completa.<ext>
    └── login-bloqueado.<ext>
```

> Guarde o link deste repositório — você vai continuar a partir dele no [Desafio 09 (Intermediário)](./desafio-intermediario.md).

---

## Critérios de avaliação

- **Funciona de fato** — os testes rodam do zero (`git clone` + comando do README) sem passos manuais extras não documentados?
- **Asserções reais** — cada teste verifica algo relevante, não só executa ações?
- **Independência** — os testes rodam em qualquer ordem, sem depender de estado deixado por outro?
- **Legibilidade** — outra pessoa do time conseguiria entender o que cada teste faz sem rodar o código?

---

## Referência

Veja [`tipos-de-teste.md`](../../tipos-de-teste/tipos-de-teste.md#3-teste-de-sistema-system-testing) para relembrar onde o teste E2E se encaixa entre os tipos de teste, e [`criacao-caso-teste.md`](../../criacao-caso-testes/criacao-caso-teste.md) se quiser revisar a lógica dos cenários antes de automatizá-los.
