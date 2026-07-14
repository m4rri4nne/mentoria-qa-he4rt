# Tipos de Requisitos de Software

## Introdução

Requisitos são a base de tudo que um time constrói: definem **o que** o sistema deve fazer, **como** ele deve se comportar e **sob quais condições** ele é considerado pronto. Boa parte dos bugs que chegam em produção não vêm de código mal escrito — vêm de requisitos mal entendidos, incompletos ou nunca ditos em voz alta.

Para um QA, conhecer os tipos de requisito não é exercício acadêmico. É o que permite, numa reunião de refinamento, perceber que uma história de usuário só descreveu o caminho feliz funcional e esqueceu completamente de mencionar segurança, performance ou uma regra de negócio que só existe "porque sempre foi assim". Saber nomear o tipo de requisito que está faltando é o primeiro passo para saber **perguntar** por ele.

---

## 1. Requisito de Negócio (Business Requirement)

### O que é
Descreve o objetivo de alto nível que a organização quer alcançar com o sistema — o "porquê" do projeto existir. Geralmente vem de stakeholders, diretoria ou do próprio modelo de negócio, não de um usuário específico.

### Características
- Não fala de telas, campos ou fluxos — fala de **resultado de negócio**
- Costuma aparecer em documentos de visão de produto, OKRs ou pitch do projeto
- É o requisito que justifica todos os outros: se uma funcionalidade não conecta com nenhum requisito de negócio, vale questionar por que ela existe

### Exemplo Prático
> **Cenário:** Uma clínica quer lançar uma plataforma de telemedicina.

```
Requisito de negócio: Reduzir em 30% o absenteísmo em consultas presenciais
oferecendo a opção de teleconsulta como alternativa.
```

Note que isso não diz nada sobre "botão de vídeo chamada" — isso vem depois, como requisito funcional derivado deste objetivo.

---

## 2. Requisito de Usuário (User Requirement)

### O que é
Descreve, na perspectiva de quem usa o sistema, o que essa pessoa precisa conseguir fazer. É a ponte entre o requisito de negócio (abstrato) e o requisito funcional (técnico).

### Características
- Costuma ser escrito como user story: "Como [persona], quero [ação], para [benefício]"
- Foca na necessidade, não na solução técnica específica
- É a fonte mais comum de ambiguidade, porque geralmente é escrito de forma curta e assume contexto que nem sempre está explícito

### Exemplo Prático
```
Como paciente, quero agendar uma teleconsulta pelo aplicativo,
para não precisar me deslocar até a clínica.
```

---

## 3. Requisito Funcional (Functional Requirement)

### O que é
Especifica **o que o sistema deve fazer** — uma função, cálculo, comportamento ou fluxo específico e verificável.

### Características
- É o tipo de requisito mais fácil de identificar e o que mais aparece explicitamente escrito em critérios de aceite
- Costuma ser testável de forma direta (entrada → saída esperada)
- Sozinho, não garante qualidade — um sistema pode fazer tudo que foi pedido funcionalmente e ainda assim ser lento, inseguro ou inutilizável

### Exemplo Prático
> **Cenário:** Critério de aceite de uma história de agendamento.

```
DADO que o paciente selecionou um médico e um horário disponível
QUANDO ele confirmar o agendamento
ENTÃO o sistema deve reservar aquele horário e impedir que outro
paciente agende o mesmo horário com o mesmo médico
```

---

## 4. Requisito Não Funcional (Non-Functional Requirement)

### O que é
Descreve **como** o sistema deve se comportar, e não o que ele faz — qualidades e restrições que atravessam todas as funcionalidades. É o tipo de requisito mais frequentemente omitido em refinamentos, porque ninguém "lembra" de dizer em voz alta.

### Subtipos mais comuns
- **Performance:** tempo de resposta, throughput (ex: "a busca deve responder em até 2s com 1000 usuários simultâneos")
- **Segurança:** autenticação, autorização, criptografia, proteção de dados sensíveis
- **Usabilidade:** facilidade de uso, acessibilidade, curva de aprendizado
- **Confiabilidade/Disponibilidade:** uptime esperado, comportamento em caso de falha
- **Escalabilidade:** capacidade de crescer em volume de dados ou usuários sem degradar
- **Compatibilidade:** navegadores, dispositivos, versões de sistema operacional suportadas
- **Manutenibilidade:** facilidade de estender ou corrigir o sistema no futuro

### Importância para QA
Requisitos não funcionais raramente aparecem escritos por completo num critério de aceite. É comum o time discutir "isso precisa ser rápido" ou "isso é sensível, precisa ser seguro" de forma verbal, sem nunca formalizar um número ou critério verificável. Um QA atento identifica essa lacuna e pergunta: *"rápido o suficiente é quanto tempo?"*, *"seguro contra o quê, especificamente?"*.

### Exemplo Prático
> **Cenário:** Mesma história de agendamento do exemplo anterior, agora com o não funcional que faltava.

```
Requisito funcional (explícito): reservar horário e impedir conflito.

Requisito não funcional (implícito, não dito): a liberação de novos
horários acontece toda segunda-feira às 8h, horário de pico de acesso —
o sistema precisa suportar esse pico sem apresentar erro ou permitir
condição de corrida (dois pacientes reservando o mesmo horário ao
mesmo tempo).
```

---

## 5. Requisito de Sistema / Técnico (System Requirement)

### O que é
Detalha, em nível técnico, como um requisito funcional ou não funcional deve ser implementado — geralmente voltado para times de desenvolvimento e arquitetura, e não para o usuário final.

### Características
- Fala a linguagem técnica: API, banco de dados, protocolo, integração
- É a tradução técnica de uma necessidade de negócio ou usuário
- QA se beneficia de conhecer esse nível porque muitos bugs de integração vivem exatamente aqui

### Exemplo Prático
```
O sistema deve expor um endpoint REST POST /agendamentos que valida,
de forma atômica (com lock ou transação no banco), a disponibilidade
do horário antes de confirmar a reserva.
```

---

## 6. Regra de Negócio (Business Rule)

### O que é
Uma condição, política ou restrição que a lógica do sistema deve respeitar — independente de tela ou fluxo específico. Não é bem um requisito no sentido de "o sistema deve fazer X", mas uma **restrição que qualquer funcionalidade relacionada precisa obedecer**.

### Características
- Costuma ser a parte que "todo mundo do time sabe" mas ninguém escreveu formalmente
- É uma fonte riquíssima de requisitos implícitos — aparece em comentários de reunião do tipo "ah, mas nesse caso a regra é..."
- Mudanças de regra de negócio afetam múltiplas funcionalidades ao mesmo tempo

### Exemplo Prático
```
Regra de negócio: cancelamento com mais de 2h de antecedência não
tem custo. Com menos de 2h, cobra-se 30% do valor da consulta.

Isso afeta: tela de cancelamento, cálculo de reembolso, notificação
ao paciente, relatório financeiro do admin — tudo precisa respeitar
a mesma regra, mesmo que só uma tela tenha sido mencionada no
refinamento.
```

---

## 7. Restrição (Constraint)

### O que é
Um limite imposto ao projeto que não é negociável e geralmente não parte de uma necessidade funcional — vem de orçamento, prazo, tecnologia já decidida ou política interna.

### Características
- Não é sobre o que o sistema faz, é sobre **dentro de quais limites** ele precisa ser construído
- Muitas vezes vem de fora do time de produto (jurídico, infraestrutura, contratos)

### Exemplo Prático
```
Restrição: a videochamada precisa usar o SDK de terceiro já
contratado pela empresa — não é opção avaliar outro fornecedor,
mesmo que tecnicamente melhor, por causa do contrato vigente.
```

---

## 8. Requisito Regulatório / Compliance

### O que é
Exigência imposta por lei, norma ou regulamentação externa ao negócio. Diferente de uma regra de negócio, não pode ser flexibilizada por decisão do time ou do cliente.

### Características
- Costuma ter peso legal — não seguir pode gerar multa ou processo, não só bug
- Frequentemente exige rastreabilidade e evidência de teste (auditoria)

### Exemplo Prático
```
Requisito regulatório: dados de histórico médico do paciente devem
ser armazenados de forma criptografada e o acesso a eles deve ser
registrado em log de auditoria, conforme LGPD.
```

---

## 9. Requisitos Explícitos vs. Implícitos

Essa não é uma categoria de requisito no mesmo sentido das anteriores — é uma **dimensão que atravessa todas elas**, e é a mais relevante para o dia a dia de um QA em refinamento.

- **Explícito:** foi escrito, dito com clareza e todo mundo concorda que existe. Ex: "o desconto do cupom BEMVINDO10 é de 10%".
- **Implícito:** é assumido como óbvio por quem escreveu a história, mas nunca foi verbalizado nem formalizado. Ex: ninguém disse o que acontece se dois cupons forem aplicados ao mesmo tempo, ou se o mesmo cliente pode usar o cupom mais de uma vez.

Requisitos implícitos não são "menos importantes" — eles só são mais fáceis de esquecer, porque moram na cabeça de quem já entende o domínio (o PO, um dev sênior, o próprio negócio) e nunca precisaram ser ditos em voz alta para *aquela* pessoa. Para alguém novo no time, porém, nada disso é óbvio.

> É exatamente por isso que uma das habilidades mais valiosas de um QA júnior a desenvolver é a de **perguntar** durante o refinamento — não para parecer que está sem entender, mas porque essa pergunta é o mecanismo que transforma um requisito implícito em explícito, antes que ele vire bug em produção.

---

## Resumo Visual

| Tipo de Requisito         | Responde à pergunta...              | Quem costuma trazer          | Explícito com frequência? |
|----------------------------|--------------------------------------|-------------------------------|----------------------------|
| Negócio                    | Por que estamos construindo isso?    | Stakeholders, diretoria       | Às vezes                   |
| Usuário                    | O que a persona precisa conseguir?   | PO, pesquisa de usuário        | Geralmente sim             |
| Funcional                  | O que o sistema faz?                 | PO, time                       | Geralmente sim             |
| Não Funcional              | Como o sistema deve se comportar?    | Arquitetura, QA, negócio        | Raramente                  |
| Sistema / Técnico          | Como isso é implementado?            | Dev, arquitetura                | Sim (mas só entre devs)    |
| Regra de Negócio           | Que condição sempre se aplica?       | Negócio, PO, dev sênior          | Raramente                  |
| Restrição                  | Dentro de quais limites?             | Jurídico, infra, contratos       | Às vezes                   |
| Regulatório / Compliance   | O que a lei exige?                   | Jurídico, compliance             | Deveria ser sempre, nem sempre é |
| Implícito (qualquer tipo acima) | O que todo mundo assume mas ninguém disse? | Ninguém — é isso que o torna implícito | Não, por definição |

---

> **Dica:** Em um refinamento, quanto mais sênior alguém é no domínio, mais requisitos implícitos essa pessoa carrega sem perceber. Um QA júnior que ainda não tem esse contexto acumulado está, paradoxalmente, em ótima posição para perguntar o óbvio — porque para ele, aquilo genuinamente não é óbvio ainda. Essa pergunta "ingênua" é frequentemente a que revela a regra de negócio ou o requisito não funcional que ninguém tinha formalizado.
