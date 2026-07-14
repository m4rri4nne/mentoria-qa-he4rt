# Desafio 09 — Deixe o E2E Robusto, com Relatório e Pipeline

[![Dificuldade](https://img.shields.io/badge/Dificuldade-Intermediária-orange?style=for-the-badge)]()
[![Tema](https://img.shields.io/badge/Tema-Testes%20Automatizados-blueviolet?style=for-the-badge)]()

> **Pré-requisito:** ter concluído o [Desafio 08 (Fácil)](./desafio-facil.md) — este desafio parte **do mesmo projeto**, não começa do zero.
>
> **Por que "Intermediária":** agora envolve decisões de arquitetura de automação (como organizar o projeto para não virar um emaranhado de scripts), configuração de ferramentas externas (reporter, CI) e a exigência de que tudo isso funcione de forma automática, sem depender de alguém rodar na máquina local.

---

## Objetivo

Pegar o projeto E2E do [Desafio 08](./desafio-facil.md) e evoluí-lo em três frentes: tornar a suíte **mais robusta** (mais cenários, melhor estrutura, sem gambiarra de sincronização), adicionar **relatórios de execução com evidência**, e colocar tudo **rodando automaticamente em uma pipeline de CI**.

---

## Por que isso importa

Um teste automatizado que só roda na sua máquina, sem relatório e sem pipeline, tem valor limitado para o time: só você sabe que ele existe, só você sabe se ele passou, e só você lembra de rodá-lo antes de cada release. O valor real da automação aparece quando ela roda sozinha a cada mudança de código e o resultado é visível para qualquer pessoa do time — é isso que permite a um time de Scrum aumentar a frequência de deploy com confiança, em vez de medo (lembra do [Change Failure Rate e Deployment Frequency](../../metricas-de-qualidade/metricas-de-qualidade.md#9-dora-metrics)? Uma suíte E2E rodando em pipeline é uma das formas mais diretas de melhorar essas duas métricas ao mesmo tempo).

---

## A Tarefa

### 1. Torne a suíte mais robusta

- Amplie a cobertura para incluir os usuários `problem_user` e `performance_glitch_user` (veja a tabela de usuários no [Desafio 08](./desafio-facil.md#o-site-saucedemo)) — decida o que faz sentido verificar em cada um (ex: para `problem_user`, algum bug visual esperado no catálogo; para `performance_glitch_user`, que o login eventualmente completa, mesmo mais lento).
- Refatore o projeto para separar **onde estão os elementos e como interagir com eles** de **o que o teste verifica** — use o padrão de organização mais comum no seu framework (Page Object Model é o mais tradicional, mas frameworks mais novos têm variações do mesmo princípio). Isso significa: se o botão de login mudar de seletor amanhã, você deve precisar editar em **um lugar só**, não em cada teste que usa login.
- Transforme os testes de login para os 4 usuários em um teste **orientado a dados** (data-driven) — uma estrutura (array, tabela, fixture) com usuário/senha/resultado esperado, rodando o mesmo teste para cada linha, em vez de 4 testes copiados e colados.
- Trate qualquer ponto de instabilidade que você perceber (principalmente com `performance_glitch_user`) usando espera condicional adequada do framework — não com `sleep` fixo.

### 2. Adicione relatórios

- Configure um gerador de relatório de execução (ex: Allure Report, Playwright HTML Reporter, Mochawesome, ExtentReports, ReportPortal, ou o nativo do seu framework) que mostre: quais testes passaram/falharam, tempo de execução de cada um, e evidência visual (screenshot ou vídeo) pelo menos nos casos de falha.
- Force **propositalmente** um teste a falhar (ex: altere uma asserção para um valor incorreto), gere o relatório, capture um print mostrando a falha e a evidência visual associada, e depois reverta a alteração. Esse print faz parte da sua entrega — é a prova de que o relatório realmente captura falhas, e não só sucessos.

### 3. Rode em uma pipeline

- Configure uma pipeline de CI (recomendado: GitHub Actions, por rodar direto no mesmo repositório) que execute toda a suíte automaticamente a cada `push` ou `pull request`.
- A pipeline deve publicar o relatório gerado como artifact do workflow (ou publicá-lo em uma página, se a ferramenta escolhida suportar isso nativamente).
- A pipeline **precisa falhar** (ficar vermelha) quando um teste falha — não pode terminar como sucesso com um teste quebrado.

---

## Restrições

- ❌ Não "resolva" instabilidade pulando (`skip`) ou removendo o teste instável — o objetivo é tratar a causa da instabilidade (espera inadequada), não escondê-la.
- ❌ Relatório sem a evidência de uma falha capturada (print pedido no item 2) não atende ao requisito — não basta mostrar só execuções 100% verdes.
- ❌ Pipeline que roda mas não falha quando um teste quebra não conta como pipeline funcional.
- ✅ Pode usar qualquer provedor de CI, mas GitHub Actions é o caminho mais direto por já estar integrado ao repositório.

---

## Formato de entrega

Continue no **mesmo repositório** do Desafio 08 (ou um fork dele), adicionando:

```
saucedemo-e2e-basico/
├── README.md                    ← atualizado: como rodar, como ver o relatório, status da pipeline
├── .github/workflows/e2e.yml    ← pipeline de CI
├── pages/ (ou equivalente)      ← Page Objects / estrutura de elementos
├── fixtures/ ou data/           ← dados usados nos testes orientados a dados
├── tests/
└── evidencia-falha-capturada.png ← print pedido no item 2
```

---

## Critérios de avaliação

- **Arquitetura** — a separação entre elementos/interações e verificações está clara? Mudar um seletor exigiria editar em um lugar só?
- **Cobertura ampliada** — os 4 usuários estão cobertos via dados, não via cópia-e-cola de teste?
- **Tratamento de instabilidade** — não há `sleep` fixo disfarçando espera inadequada?
- **Relatório funcional** — mostra tempo de execução, resultado por teste, e a evidência de falha pedida está presente?
- **Pipeline correta** — roda automaticamente, publica o relatório, e falha quando deveria falhar?

> Uma pipeline verde não significa nada se ela também fica verde quando um teste falha. Antes de entregar, force um teste a quebrar e confirme que a pipeline realmente acusa isso — é o tipo de verificação que separa uma pipeline configurada de uma pipeline configurada **corretamente**.

---

## Referência

Revise [`metricas-de-qualidade.md`](../../metricas-de-qualidade/metricas-de-qualidade.md#9-dora-metrics) — em especial Deployment Frequency e Change Failure Rate — para entender por que essa robustez de pipeline importa além do exercício técnico em si. Quando terminar, siga para o [Desafio 10 (Avançado)](./desafio-avancado.md), que consolida este projeto com execução cross-browser e métricas de performance da própria suíte.
