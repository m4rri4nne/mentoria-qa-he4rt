# Tipos de Testes de Software

## Introdução

Testes de software são atividades fundamentais para garantir a qualidade, confiabilidade e funcionamento correto de um sistema. Conhecer os diferentes tipos de testes permite que equipes de QA apliquem a estratégia certa no momento certo, reduzindo retrabalho e aumentando a confiança nas entregas.

---

## 1. Teste Unitário (Unit Testing)

### O que é
Testa a menor unidade de código de forma isolada — geralmente uma função, método ou classe.

### Importância
- Detecta erros logo no início do desenvolvimento
- Facilita refatoração com segurança
- Serve como documentação viva do comportamento esperado
- Rápido de executar e barato de manter

### Exemplo Prático
> **Cenário:** Uma função `calcularDesconto(preco, percentual)` deve retornar o valor com desconto aplicado.

```javascript
// Função
function calcularDesconto(preco, percentual) {
  return preco - (preco * percentual / 100);
}

// Teste unitário
test('deve aplicar 10% de desconto em R$100', () => {
  expect(calcularDesconto(100, 10)).toBe(90);
});
```

---

## 2. Teste de Integração (Integration Testing)

### O que é
Verifica se diferentes módulos ou serviços funcionam corretamente quando combinados entre si.

### Importância
- Garante que as partes do sistema se comunicam corretamente
- Detecta problemas de interface entre componentes
- Valida fluxos que envolvem banco de dados, APIs externas e serviços

### Exemplo Prático
> **Cenário:** Testar se o serviço de pedidos consegue salvar um pedido no banco de dados e notificar o serviço de e-mail.

```
DADO que um usuário realiza um pedido
QUANDO o sistema processa o pedido
ENTÃO o pedido deve ser salvo no banco de dados
E um e-mail de confirmação deve ser enviado
```

---

## 3. Teste de Sistema (System Testing)

### O que é
Avalia o sistema completo e integrado, verificando se ele atende aos requisitos especificados.

### Importância
- Valida o comportamento end-to-end do sistema
- Garante que todos os componentes funcionam juntos como esperado
- Simula o ambiente real de uso

### Exemplo Prático
> **Cenário:** Fluxo completo de compra em um e-commerce.

```
1. Usuário acessa o site
2. Busca por um produto
3. Adiciona ao carrinho
4. Realiza login
5. Finaliza o pagamento
6. Recebe confirmação do pedido
→ Todos os passos devem funcionar de ponta a ponta
```

---

## 4. Teste de Aceitação (Acceptance Testing / UAT)

### O que é
Valida se o sistema atende às expectativas e necessidades do usuário ou do cliente. Geralmente executado pelo próprio cliente ou usuários finais.

### Importância
- Confirma que o produto entregue está de acordo com o que foi solicitado
- Reduz o risco de rejeição do produto em produção
- Alinha expectativas entre time técnico e negócio

### Exemplo Prático
> **Cenário:** O cliente pediu que usuários pudessem redefinir a senha por e-mail.

```
DADO que o usuário esqueceu sua senha
QUANDO clicar em "Esqueci minha senha" e informar o e-mail
ENTÃO deve receber um link de redefinição em até 2 minutos
E o link deve expirar após 30 minutos
```

---

## 5. Teste de Regressão (Regression Testing)

### O que é
Garante que novas alterações no código não quebraram funcionalidades já existentes.

### Importância
- Essencial após correções de bugs ou novas features
- Protege funcionalidades já entregues e validadas
- Pode ser automatizado para maior eficiência

### Exemplo Prático
> **Cenário:** Após implementar o pagamento com Pix, testar se o pagamento com cartão de crédito ainda funciona corretamente.

```
Funcionalidades a revalidar após nova entrega:
✅ Login e autenticação
✅ Carrinho de compras
✅ Pagamento com cartão de crédito
✅ Geração de boleto
✅ Histórico de pedidos
```

---

## 6. Teste de Performance (Performance Testing)

### O que é
Avalia o comportamento do sistema sob determinadas condições de carga, velocidade e estabilidade.

### Subtipos principais
- **Load Testing:** comportamento com carga esperada
- **Stress Testing:** comportamento além do limite esperado
- **Spike Testing:** comportamento com picos repentinos de acesso

### Importância
- Previne falhas em momentos de alta demanda
- Garante boa experiência do usuário
- Identifica gargalos antes de ir para produção

### Exemplo Prático
> **Cenário:** Simular 1.000 usuários acessando simultaneamente a página de checkout durante uma Black Friday.

```
Ferramenta: k6 / JMeter
Meta: Tempo de resposta < 2s para 95% das requisições
Resultado esperado: Sistema estável sem erros 5xx
```

---

## 7. Teste de Segurança (Security Testing)

### O que é
Identifica vulnerabilidades, brechas e riscos de segurança no sistema.

### Importância
- Protege dados sensíveis de usuários e da empresa
- Previne ataques como SQL Injection, XSS e CSRF
- Garante conformidade com regulamentações (LGPD, GDPR)

### Exemplo Prático
> **Cenário:** Testar se o sistema é vulnerável a SQL Injection no campo de login.

```
Input malicioso: ' OR '1'='1
Resultado esperado: Sistema deve rejeitar a entrada
                    e não retornar dados do banco
```

---

## 8. Teste de Usabilidade (Usability Testing)

### O que é
Avalia a facilidade de uso e a experiência do usuário ao interagir com o sistema.

### Importância
- Garante que o produto é intuitivo e acessível
- Reduz a taxa de abandono e aumenta a satisfação
- Identifica fluxos confusos antes do lançamento

### Exemplo Prático
> **Cenário:** Observar usuários reais tentando realizar um cadastro no sistema sem nenhuma instrução prévia.

```
Métrica avaliada:
- Tempo para completar o cadastro
- Número de erros cometidos
- Nível de satisfação (escala 1-5)
- Pontos de confusão identificados
```

---

## 9. Teste Exploratório (Exploratory Testing)

### O que é
Teste sem script predefinido, onde o testador explora o sistema livremente com base em sua experiência e intuição.

### Importância
- Descobre bugs que testes scripted não encontrariam
- Exige menos tempo de preparação
- Simula o comportamento real e imprevisível dos usuários

### Exemplo Prático
> **Cenário:** Testar um formulário de cadastro explorando combinações incomuns.

```
- Inserir emojis no campo de nome
- Copiar e colar texto com formatação especial
- Submeter o formulário múltiplas vezes rapidamente
- Deixar campos obrigatórios em branco de formas variadas
```

---

## Resumo Visual

| Tipo de Teste       | Quando aplicar                        | Quem executa         |
|---------------------|---------------------------------------|----------------------|
| Unitário            | Durante o desenvolvimento             | Desenvolvedor        |
| Integração          | Após integrar módulos                 | Dev / QA             |
| Sistema             | Sistema completo montado              | QA                   |
| Aceitação (UAT)     | Antes do go-live                      | Cliente / QA         |
| Regressão           | Após qualquer alteração               | QA / Automação       |
| Performance         | Antes de lançamentos críticos         | QA / Engenharia      |
| Segurança           | Regularmente e antes de releases      | QA / Security        |
| Usabilidade         | Durante e após o design               | UX / QA              |
| Exploratório        | A qualquer momento                    | QA                   |

---

> **Dica:** Uma boa estratégia de testes combina múltiplos tipos ao longo de todo o ciclo de desenvolvimento, seguindo a pirâmide de testes: base sólida de testes unitários, camada intermediária de integração e testes de sistema no topo.