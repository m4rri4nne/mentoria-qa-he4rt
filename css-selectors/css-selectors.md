# CSS Selectors para QA

## O que são CSS Selectors?

CSS Selectors são padrões usados para identificar elementos HTML em uma página. Originalmente criados para estilização com CSS, eles se tornaram uma das principais estratégias de localização de elementos em testes automatizados com ferramentas como Selenium, Playwright e Cypress.

Ao contrário do XPath, os CSS selectors têm sintaxe mais enxuta, melhor performance em browsers modernos e são amplamente suportados por todas as ferramentas de automação relevantes.

---

## Tipos de CSS Selectors

### 1. Seletor por Tag (Type Selector)

Seleciona todos os elementos de um determinado tipo HTML.

```css
input
button
a
```

**Exemplo:**
```css
input   /* seleciona todos os inputs da página */
```

**Estabilidade:** ⚠️ Baixa — tags são genéricas demais. Em páginas com muitos inputs ou buttons, o seletor não é específico o suficiente para identificar um único elemento com segurança.

---

### 2. Seletor por ID (`#`)

Seleciona o elemento que possui um atributo `id` específico.

```css
#login-button
#email-input
```

**Exemplo no HTML:**
```html
<button id="login-button">Entrar</button>
```

**Seletor:**
```css
#login-button
```

**Estabilidade:** ✅ Alta — IDs deveriam ser únicos por página. No entanto, muitas aplicações geram IDs dinamicamente (ex: `#input-3f9a2b`), o que torna o seletor frágil. Prefira quando o ID for estático e semântico.

---

### 3. Seletor por Classe (`.`)

Seleciona todos os elementos que possuem uma determinada classe CSS.

```css
.btn-primary
.form-input
.error-message
```

**Exemplo no HTML:**
```html
<button class="btn btn-primary">Enviar</button>
```

**Seletor:**
```css
.btn-primary
```

**Estabilidade:** ⚠️ Média — classes são frequentemente reutilizadas para estilização e podem mudar quando o layout muda. Classes utilitárias (ex: Tailwind `.text-red-500`) são especialmente instáveis.

---

### 4. Seletor por Atributo (`[attr]`)

Seleciona elementos baseado em um atributo e/ou seu valor.

```css
[type="submit"]
[placeholder="Buscar"]
[href="/home"]
[data-testid="submit-btn"]
[aria-label="Fechar modal"]
```

**Exemplos no HTML:**
```html
<input type="email" placeholder="seu@email.com" />
<button data-testid="submit-btn">Enviar</button>
```

**Seletores:**
```css
input[type="email"]
[data-testid="submit-btn"]
[aria-label="Fechar modal"]
```

**Estabilidade:** ✅ Alta a ⚠️ Média, depende do atributo:

| Atributo | Estabilidade | Motivo |
|---|---|---|
| `data-testid` / `data-cy` / `data-qa` | ✅ Muito alta | Criado especificamente para testes |
| `aria-label`, `aria-role` | ✅ Alta | Atributos de acessibilidade, raramente mudam |
| `name` | ✅ Alta | Geralmente semântico e estável |
| `type` | ✅ Alta | Define comportamento, raramente muda |
| `placeholder` | ⚠️ Média | Pode variar com internacionalização |
| `class` | ⚠️ Baixa a média | Frequentemente alterado para estilização |
| `id` gerado dinamicamente | ❌ Muito baixa | Muda a cada renderização |

---

### 5. Seletor Descendente (espaço)

Seleciona um elemento que está dentro de outro, em qualquer nível de profundidade.

```css
form input
nav a
.modal button
```

**Exemplo:**
```html
<form>
  <div>
    <input type="text" />
  </div>
</form>
```

**Seletor:**
```css
form input
```

**Estabilidade:** ⚠️ Média — funciona bem combinado com seletores estáveis no pai. Se o pai mudar de tag ou classe, quebra.

---

### 6. Seletor Filho Direto (`>`)

Seleciona apenas filhos diretos (um nível abaixo).

```css
ul > li
form > input
.nav > a
```

**Estabilidade:** ⚠️ Média a Baixa — altamente sensível a mudanças de estrutura HTML. Se um `div` extra for adicionado entre o pai e o filho, o seletor quebra.

---

### 7. Seletor de Irmão Adjacente (`+`)

Seleciona o elemento imediatamente após outro.

```css
label + input
.error + button
```

**Exemplo:**
```html
<label>Email</label>
<input type="email" />
```

**Seletor:**
```css
label + input
```

**Estabilidade:** ❌ Baixa — dependente da posição exata no DOM. Qualquer elemento inserido entre os dois quebra o seletor.

---

### 8. Seletor de Irmão Geral (`~`)

Seleciona todos os irmãos posteriores de um elemento.

```css
h2 ~ p
.title ~ .description
```

**Estabilidade:** ❌ Baixa — mesmas fragilidades do irmão adjacente, com escopo maior.

---

### 9. Pseudo-classes (`:`)

Selecionam elementos com base em estado ou posição.

```css
:hover
:focus
:checked
:disabled
:nth-child(2)
:first-child
:last-child
:not(.disabled)
```

**Exemplos úteis em QA:**
```css
input:checked            /* checkbox marcado */
button:disabled          /* botão desabilitado */
li:nth-child(3)          /* terceiro item de uma lista */
.item:not(.selected)     /* itens não selecionados */
```

**Estabilidade:**

| Pseudo-classe | Estabilidade em testes | Observação |
|---|---|---|
| `:checked`, `:disabled`, `:focus` | ✅ Alta | Refletem estado real do elemento |
| `:not()` | ✅ Alta | Útil para excluir elementos |
| `:nth-child(n)` | ⚠️ Média | Frágil se a ordem mudar |
| `:first-child`, `:last-child` | ⚠️ Média | Depende da estrutura da lista |
| `:hover` | ❌ Não usar | Estado de mouse — difícil de simular consistentemente |

---

### 10. Pseudo-elementos (`::`)

Selecionam partes de um elemento (ex: `::before`, `::after`). **Não são usados em automação de testes** — ferramentas como Selenium e Playwright não conseguem interagir com pseudo-elementos.

---

## Hierarquia de Estabilidade

Do mais estável para o menos estável em contexto de automação:

```
data-testid / data-cy / data-qa   ← ideal, criado para testes
aria-label / role                 ← excelente, semântico e acessível
name / type / value               ← bom, geralmente estático
ID semântico (#submit-btn)        ← bom quando não gerado dinamicamente
Combinação de tag + atributo      ← razoável
Classe CSS                        ← frágil, muda com redesigns
Posição no DOM (nth-child, >)     ← muito frágil
ID gerado dinamicamente           ← evitar sempre
```

---

## Boas Práticas para QA

**Prefira atributos semânticos e de acessibilidade.** Um `[aria-label="Enviar formulário"]` descreve a intenção do elemento — é menos provável que mude do que a classe CSS que o estiliza.

**Evite seletores muito longos.** `div.container > section > form > div.row > input` quebra com qualquer refatoração de layout.

**Combine seletores para aumentar precisidade.** `input[type="email"][name="user-email"]` é mais estável do que apenas `input`.

**Quando não há data-testid**, prefira nesta ordem: `aria-label` → `name` → `id` estático → combinação de tag+atributo → classe.

**Documente o raciocínio.** Um seletor sem explicação do porquê foi escolhido é difícil de manter quando quebra.
