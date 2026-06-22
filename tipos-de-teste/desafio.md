# Desafio 02 — Defina a Camada de Testes de um Projeto

[![Dificuldade](https://img.shields.io/badge/Dificuldade-Avançada-red?style=for-the-badge)]()
[![Tema](https://img.shields.io/badge/Tema-Estratégia%20de%20Testes-blueviolet?style=for-the-badge)]()

> **Pré-requisito:** leia [`tipos-de-teste.md`](./tipos-de-teste.md) antes de começar. Este desafio assume que você já conhece os 9 tipos de teste descritos lá.
>
> **Por que "Avançada":** não tem código pra rodar aqui — o trabalho é estratégico. Você vai precisar justificar decisões com base em riscos de negócio, não em "porque o material disse que é importante". É o tipo de raciocínio que normalmente se espera de alguém um pouco mais sênior, e é exatamente por isso que vale praticar desde já.

---

## Objetivo

Colocar você no lugar de quem **decide** a estratégia de testes de um projeto — não só quem executa testes já definidos por outra pessoa.

Você vai receber a especificação de um projeto fictício (com backend e frontend) e vai precisar decidir: quais tipos de teste fazem sentido aplicar, em que momento do ciclo de desenvolvimento, quem deveria executar cada um, e — mais importante — **por quê**, com base nas características e riscos específicos *desse* projeto.

---

## Por que isso importa

Na prática, é muito comum que QAs (principalmente conforme ganham experiência) sejam quem **define a camada de testes junto com o time** — não só quem aperta o botão de "rodar os testes".

Isso significa:

- Entender cada tipo de teste a fundo (o que cobre, o que não cobre, custo de manter)
- Saber explicar para devs, PO e liderança **por que** um determinado tipo de teste é necessário (ou por que não é, naquele contexto)
- Adaptar a estratégia ao risco real do produto, e não aplicar uma "receita de bolo" igual pra todo projeto
- Negociar prioridades quando não há tempo/recurso pra cobrir tudo

Um QA que só executa testes que alguém mandou é facilmente substituível. Um QA que sabe **desenhar** a estratégia de testes de um projeto, e justificar isso para o time, é quem geralmente cresce pra posições de liderança técnica em qualidade.

Esse desafio simula exatamente essa situação.

---

## O Projeto Fictício: MedAgenda

Você foi contratado(a) como **a primeira pessoa de QA** de uma startup que está construindo o **MedAgenda**, uma plataforma que conecta pacientes e médicos para consultas (presenciais e por teleconsulta).

### Visão geral do produto

- Pacientes buscam médicos por especialidade, localização e avaliação, e agendam consultas.
- Médicos cadastram sua agenda de horários disponíveis e atendem pelo painel deles.
- Consultas podem ser **presenciais** ou por **teleconsulta** (vídeo chamada dentro da própria plataforma).
- O pagamento da consulta é feito na própria plataforma (cartão de crédito ou Pix), repassado ao médico com retenção de taxa.
- Depois da consulta, o paciente pode avaliar o médico.
- Existe um painel administrativo interno para a equipe da clínica gerenciar médicos cadastrados e ver relatórios financeiros.

### Funcionalidades (backend + frontend)

| Módulo | Descrição |
|---|---|
| Autenticação | Cadastro/login de pacientes, médicos e administradores. Papéis diferentes têm permissões diferentes. |
| Busca de médicos | Filtro por especialidade, localização, avaliação e disponibilidade. |
| Agendamento | Paciente escolhe horário disponível; sistema impede conflito de horários para o mesmo médico ou paciente. |
| Pagamento | Integração com gateway de pagamento externo (cartão e Pix). Reembolso automático em caso de cancelamento dentro da política. |
| Teleconsulta | Sala de vídeo chamada via SDK de terceiro, integrada à tela da consulta agendada. |
| Notificações | E-mails e SMS (via serviço terceirizado) de confirmação, lembrete (24h e 1h antes) e cancelamento. |
| Avaliações | Paciente avalia o médico (nota + comentário) após a consulta ser concluída. |
| Painel admin | Equipe interna aprova cadastro de novos médicos e visualiza relatórios de faturamento. |

### Stack técnica

- **Backend:** Node.js (NestJS) + PostgreSQL, API REST
- **Frontend:** React (Next.js), consumindo a API
- **Integrações externas:** gateway de pagamento, serviço de SMS/e-mail, SDK de videochamada

### Regras de negócio relevantes

- Cancelamento com mais de 2h de antecedência: sem custo. Com menos de 2h: cobra 30% do valor da consulta.
- Um médico não pode ter dois agendamentos no mesmo horário. Um paciente também não.
- Dados de saúde (histórico, anotações da consulta) e dados de pagamento são armazenados e precisam seguir a **LGPD**.
- Médicos liberam novos horários da semana toda segunda-feira às 8h — historicamente esse é o horário de maior acesso simultâneo à plataforma.
- Parte significativa dos pacientes é de pessoas mais velhas, com pouca familiaridade com tecnologia.

### O time

- 4 devs backend, 3 devs frontend, 1 product owner, 1 designer
- **Você**, a primeira (e por enquanto única) pessoa de QA
- MVP precisa ir ao ar em 3 meses; depois disso, releases incrementais a cada 2 semanas

---

## A Tarefa

Você vai entrar numa reunião com o time (na prática, vai escrever um documento) para apresentar **sua proposta de estratégia de testes** para o MedAgenda. A entrega precisa cobrir:

### 1. Decisão tipo a tipo

Para **cada um dos 9 tipos de teste** descritos em [`tipos-de-teste.md`](./tipos-de-teste.md), decida: esse tipo será aplicado no MedAgenda? Justifique com base em características **específicas** desse projeto — não vale repetir a justificativa genérica do material de referência.

> Exemplo do que **não** queremos: "Teste de segurança é importante porque protege dados sensíveis."
> Exemplo do que **queremos**: "Teste de segurança é crítico aqui porque o MedAgenda armazena histórico médico e dados de pagamento — uma falha de SQL Injection ou de controle de acesso entre papéis (paciente acessando prontuário de outro paciente, por exemplo) é um risco de LGPD e de credibilidade da clínica."

### 2. Quando e quem

Para os tipos que você decidiu aplicar, defina:

- **Quando** no ciclo de desenvolvimento ele entra (ex: a cada PR, antes de cada release quinzenal, sob demanda, continuamente em produção)
- **Quem executa** (dev, você como QA, automação, o próprio cliente/usuário)
- **Com que frequência**

### 3. Priorização

Você não tem time ilimitado. Aponte os **3 tipos de teste mais críticos** para o MedAgenda especificamente, e explique por que eles vêm antes dos outros — a justificativa deve estar ligada a risco real do negócio (segurança de dados de saúde, conflito de agendamento, pico de acesso, pagamento, etc.), não a preferência pessoal.

### 4. Riscos que o checklist padrão não cobre

Identifique **ao menos 2 riscos** do MedAgenda que não seriam bem cobertos só com os 9 tipos de teste do material de referência. Proponha como mitigar — pode ser um tipo de teste adicional (ex: teste de acessibilidade, teste de compatibilidade entre navegadores/dispositivos, teste de conformidade LGPD) ou uma prática complementar (ex: revisão de contrato de API com os times de integração externa).

### 5. Pirâmide de testes adaptada

Desenhe (pode ser em texto, lista ou diagrama simples) uma proporção sugerida de investimento entre os tipos de teste automatizáveis (unitário, integração, sistema/E2E) para esse projeto. Justifique a proporção escolhida.

### 6. Como você comunicaria isso ao time

Em poucas frases: como você apresentaria essa estratégia para devs e PO de forma que eles entendam o "porquê" e não só o "o quê"? O que você faria se um dev discordasse de uma das suas decisões?

---

## Formato de entrega

Crie um repositório público no GitHub (ex: `medagenda-estrategia-testes`) com a seguinte estrutura:

```
medagenda-estrategia-testes/
├── README.md              ← breve descrição do desafio
└── estrategia-de-testes.md ← sua entrega
```

Sugestão de estrutura para a tabela do item 1 dentro de `estrategia-de-testes.md`:

```markdown
| Tipo de teste   | Aplicar? | Por quê (específico ao MedAgenda) | Quando no ciclo | Quem executa |
|-----------------|----------|------------------------------------|------------------|--------------|
| Unitário        | Sim/Não  | ...                                 | ...              | ...          |
| Integração      | Sim/Não  | ...                                 | ...              | ...          |
| Sistema         | Sim/Não  | ...                                 | ...              | ...          |
| Aceitação (UAT) | Sim/Não  | ...                                 | ...              | ...          |
| Regressão       | Sim/Não  | ...                                 | ...              | ...          |
| Performance     | Sim/Não  | ...                                 | ...              | ...          |
| Segurança       | Sim/Não  | ...                                 | ...              | ...          |
| Usabilidade     | Sim/Não  | ...                                 | ...              | ...          |
| Exploratório    | Sim/Não  | ...                                 | ...              | ...          |
```

Os demais itens (2 a 6) podem ser seções de texto livre abaixo da tabela.

---

## Restrições

- ❌ Não copie e cole as descrições de [`tipos-de-teste.md`](./tipos-de-teste.md) — a justificativa precisa ser sobre o MedAgenda, não sobre o conceito em geral.
- ❌ Não marque todos os 9 tipos como "Sim" sem critério. Parte do exercício é saber dizer que um tipo **não** é prioridade agora, e justificar isso também.
- ✅ Pode pesquisar exemplos reais de estratégias de teste em produtos de saúde/fintech para embasar sua resposta.
- ✅ Pode (e deve) usar sua experiência e bom senso para imaginar riscos que não estão explicitamente escritos na especificação do projeto.

---

## Critérios de avaliação

- **Especificidade** — as justificativas são sobre o MedAgenda ou são genéricas o suficiente para servir qualquer projeto?
- **Coerência com o risco** — a priorização do item 3 reflete os riscos reais descritos na especificação (dados de saúde, pagamento, pico de acesso, conflito de agendamento)?
- **Pensamento crítico** — os riscos do item 4 mostram que você foi além do checklist padrão?
- **Viabilidade** — a estratégia proposta é realista para um time pequeno (4 devs backend, 3 frontend, 1 QA) e um MVP de 3 meses, ou é uma lista de "teste tudo, sempre"?
- **Comunicação** — o documento está claro o suficiente para que um dev ou PO leia e entenda o raciocínio, sem precisar perguntar "por quê" a cada linha?

> Uma entrega que marca "sim" pra tudo com justificativas copiadas do material de referência demonstra memorização, não entendimento. O objetivo aqui é simular a decisão real que um QA toma ao entrar num projeto novo.

---

## Referência

Revise [`tipos-de-teste.md`](./tipos-de-teste.md) antes de começar — em especial a tabela "Resumo Visual" no final do arquivo, que serve como ponto de partida (mas não como resposta final) para o item 1 deste desafio.
