# Teste de Caixa Branca

**SENAI 2026**

| | |
|---|---|
| **Aluno** | Murilo Jordan Cezar |
| **Nº** | 24 |
| **Turma** | 3-A |
| **Data** | 30/09/2026 |

---

## Sumário

1. [Contextualização](#1-contextualização-sobre-teste-de-caixa-branca)
2. [Análise das Estruturas de Decisão](#2-análise-das-estruturas-de-decisão)
3. [Fluxo Geral do Sistema](#3-fluxo-geral-do-sistema)
4. [Erros Encontrados](#4-erros-encontrados)
5. [Tabela dos Casos de Teste](#5-tabela-dos-casos-de-teste)
6. [Resultados dos Testes](#6-resultados-dos-testes)
7. [Comparação das Correções](#7-comparação-das-correções)
8. [Análise dos Resultados](#8-análise-dos-resultados)
9. [Conclusão](#9-conclusão)
10. [Principais Trechos do Código Corrigido](#10-principais-trechos-do-código-corrigido)

---

## 1. Contextualização sobre Teste de Caixa Branca

O teste de caixa branca é uma forma de testar um sistema olhando também para o que acontece dentro do código. Nesse tipo de teste, não analisamos apenas se o resultado está correto, mas também verificamos as condições, decisões e caminhos que o programa percorre para chegar até esse resultado.

Nesta atividade foi analisado um sistema simples de pedidos desenvolvido em **HTML, CSS e JavaScript**. Nele, o usuário pode escolher um produto, informar a quantidade, adicionar um cupom de desconto e escolher o tipo de frete. Depois disso, o sistema calcula o valor final do pedido.

Durante a análise do código, foram encontrados **seis erros de lógica**. A maioria deles está relacionada aos limites utilizados nas condições, principalmente com os operadores `>`, `>=`, `<` e `<=`.

Para encontrar os erros, foram utilizados principalmente testes de valores-limite, análise das decisões e acompanhamento dos valores das variáveis durante a execução.

## 2. Análise das Estruturas de Decisão

Durante a leitura do código, foram encontradas várias estruturas condicionais que podem mudar o caminho que o programa segue:

- Validação da quantidade;
- Verificação do estoque;
- Aplicação dos cupons;
- Cálculo do frete;
- Desconto por quantidade;
- Desconto para pedidos de alto valor;
- Classificação final do pedido.

Essas condições são importantes porque uma pequena alteração em um operador pode fazer o sistema seguir um caminho diferente do esperado.

Por exemplo:

```javascript
if (qtd < 0) {
    resultado.innerHTML = "<p>Quantidade inválida.</p>";
    return;
}
```

e

```javascript
if (qtd <= 0) {
```

parecem muito semelhantes, mas possuem comportamentos diferentes quando a quantidade é exatamente 0. Por isso, os valores próximos aos limites foram utilizados nos testes.

## 3. Fluxo Geral do Sistema

```text
INÍCIO
  ↓
Entrada dos dados
  ↓
Verifica a quantidade
  ↓
Verifica o estoque
  ↓
Calcula o subtotal
  ↓
Verifica o cupom
  ↓
Calcula o frete
  ↓
Calcula o total
  ↓
Verifica descontos adicionais
  ↓
Classifica o pedido
  ↓
Mostra o resultado
  ↓
FIM
```

## 4. Erros Encontrados

### Erro 1 — Quantidade igual a zero

**Nível:** Fácil

**Trecho do código:**

```javascript
if (qtd < 0) {
  resultado.innerHTML = "<p>Quantidade inválida.</p>";
  return;
}
```

**Comportamento esperado:** A quantidade 0 não deveria ser aceita, porque não existe um pedido com zero produtos.

**Dados utilizados no teste:**

- Produto: Mouse
- Quantidade: 0
- Cupom: nenhum
- Frete: Normal

**Caminho percorrido:**

```text
Quantidade = 0
↓
qtd < 0?
↓
FALSO
↓
Continua o processamento
↓
Subtotal = R$ 0,00
↓
Frete = R$ 30,00
↓
Total = R$ 30,00
```

**Resultado esperado:** O sistema deveria mostrar: "Quantidade inválida."

**Resultado apresentado:** O sistema continua o processamento e apresenta um pedido com Total: R$ 30,00.

**Erro identificado:** O problema está na condição `qtd < 0`. Ela verifica somente números negativos e acaba permitindo o valor 0.

**Correção realizada:**

```javascript
if (qtd <= 0) {
  resultado.innerHTML = "<p>Quantidade inválida.</p>";
  return;
}
```

**Resultado após a correção:** Ao informar quantidade 0, o sistema passa a apresentar "Quantidade inválida."

**Técnica utilizada:** Análise de valores-limite.

**Fluxograma do caminho analisado:**

```text
Quantidade = 0
↓
Quantidade <= 0?
↙ SIM                 NÃO ↘
Quantidade inválida   Continua pedido
```

---

### Erro 2 — Quantidade igual ao estoque

**Nível:** Fácil

**Trecho do código:**

```javascript
if (qtd >= estoque[produtoSelecionado]) {
  resultado.innerHTML = "<p>Quantidade indisponível em estoque.</p>";
  return;
}
```

**Comportamento esperado:** Se existem exatamente 20 mouses no estoque e o cliente quer comprar os 20, o pedido deve ser aceito.

**Dados utilizados no teste:**

- Produto: Mouse
- Estoque: 20
- Quantidade: 20
- Cupom: nenhum
- Frete: Normal

**Caminho percorrido:**

```text
Quantidade = 20
↓
Estoque = 20
↓
20 >= 20?
↓
VERDADEIRO
↓
Quantidade indisponível
↓
Fim
```

**Resultado esperado:** O sistema deveria aceitar o pedido. Subtotal: R$ 1.600,00. Frete: R$ 0,00. Total: R$ 1.600,00.

**Resultado apresentado:** O sistema informa: "Quantidade indisponível em estoque."

**Erro identificado:** A condição usa `>=`. Dessa forma, quando a quantidade é exatamente igual ao estoque, o sistema entende que não há disponibilidade.

**Correção realizada:**

```javascript
if (qtd > estoque[produtoSelecionado]) {
```

**Resultado após a correção:** Com 20 mouses disponíveis e quantidade solicitada igual a 20, o pedido é aceito normalmente.

**Técnica utilizada:** Análise de valores-limite.

**Fluxograma do caminho analisado:**

```text
Quantidade = 20
↓
Quantidade > estoque?
↙ NÃO              SIM ↘
Continua           Erro
```

---

### Erro 3 — Cupom SENAI20 no limite

**Nível:** Médio

**Trecho do código:**

```javascript
if (codigo === "SENAI20" && subtotal >= 1000) {
  return subtotal * 0.20;
}
```

**Comportamento esperado:** Considerando a regra utilizada no sistema, o cupom SENAI20 deve ser aplicado somente quando o subtotal for **maior** que R$ 1.000,00.

**Dados utilizados no teste:**

Teste direto da função:

```javascript
calcularDesconto(1000, "SENAI20");
```

**Caminho percorrido:**

```text
Subtotal = R$ 1.000
↓
Cupom = SENAI20
↓
Subtotal >= 1000?
↓
VERDADEIRO
↓
Aplica 20% de desconto
```

**Resultado esperado:** Como o valor é exatamente R$ 1.000, o desconto não deveria ser aplicado. Desconto esperado: R$ 0,00.

**Resultado apresentado:** O programa aplica 20%: Desconto = R$ 200,00.

**Erro identificado:** O operador `>=` faz com que o desconto seja aplicado também no valor exato do limite.

**Correção realizada:**

```javascript
if (codigo === "SENAI20" && subtotal > 1000) {
```

**Resultado após a correção:** Com subtotal de R$ 1.000: desconto de R$ 0,00. Com subtotal de R$ 1.001: o cupom passa a ser aplicado.

**Técnica utilizada:** Cobertura de condições e análise de valores-limite.

**Fluxograma do caminho analisado:**

```text
Cupom SENAI20
↓
Subtotal > 1000?
↙ NÃO              SIM ↘
Sem desconto       20% de desconto
```

---

### Erro 4 — Frete grátis no limite

**Nível:** Médio

**Trecho do código:**

```javascript
if (subtotal >= 500) {
  return 0;
}
```

**Comportamento esperado:** Considerando a regra analisada, o frete grátis deve ser aplicado quando o subtotal for **maior** que R$ 500,00.

**Dados utilizados no teste:**

Teste direto da função:

```javascript
calcularFrete("normal", 500);
```

**Caminho percorrido:**

```text
Tipo de frete = normal
↓
É retirada?
↓
NÃO
↓
É expresso?
↓
NÃO
↓
Subtotal >= 500?
↓
SIM
↓
Frete = R$ 0,00
```

**Resultado esperado:** Para um subtotal exatamente igual a R$ 500, o frete normal deveria continuar sendo cobrado. Frete esperado: R$ 30,00.

**Resultado apresentado:** Frete: R$ 0,00.

**Erro identificado:** A condição `subtotal >= 500` inclui o próprio valor de R$ 500.

**Correção realizada:**

```javascript
if (subtotal > 500) {
```

**Resultado após a correção:** Subtotal de R$ 500: frete R$ 30,00. Subtotal de R$ 501: frete R$ 0,00.

**Técnica utilizada:** Análise de valores-limite e cobertura de decisões.

**Fluxograma do caminho analisado:**

```text
Subtotal = R$ 500
↓
Subtotal > 500?
↙ NÃO               SIM ↘
Frete R$ 30,00      Frete grátis
```

---

### Erro 5 — Desconto para cinco unidades

**Nível:** Difícil

**Trecho do código:**

```javascript
if (qtd > 5) {
  total = total - subtotal * 0.05;
}
```

**Comportamento esperado:** A partir de 5 unidades, o cliente deve receber o desconto de 5%.

**Dados utilizados no teste:**

- Produto: Mouse
- Quantidade: 5
- Cupom: nenhum
- Frete: Normal

**Caminho percorrido:**

```text
Quantidade = 5
↓
Quantidade > 5?
↓
FALSO
↓
Não aplica desconto
↓
Total = R$ 430,00
```

**Resultado esperado:** Subtotal = R$ 400,00. Frete = R$ 30,00. Desconto = R$ 20,00. Total esperado = R$ 410,00.

**Resultado apresentado:** Total = R$ 430,00.

**Erro identificado:** A condição utiliza `> 5`. Por isso, exatamente 5 unidades não recebem o desconto.

**Correção realizada:**

```javascript
if (qtd >= 5) {
```

**Resultado após a correção:** Com 5 mouses, o desconto passa a ser aplicado e o total fica em R$ 410,00.

**Técnica utilizada:** Análise de valores-limite e rastreamento de variáveis.

**Fluxograma do caminho analisado:**

```text
Quantidade = 5
↓
Quantidade >= 5?
↙ SIM              NÃO ↘
Aplica 5%          Sem desconto
↓
Calcula total
```

---

### Erro 6 — Pedido de alto valor

**Nível:** Difícil

**Trecho do código:**

```javascript
if (total > 3000) {
  total = total * 0.95;
}
```

**Comportamento esperado:** Pedidos com valor de R$ 3.000 ou mais devem receber o tratamento de alto valor.

**Dados utilizados no teste:**

- Produto: Notebook
- Quantidade: 1
- Cupom: nenhum
- Frete: Normal

**Caminho percorrido:**

```text
Notebook = R$ 3.000
↓
Subtotal = R$ 3.000
↓
Frete grátis
↓
Total = R$ 3.000
↓
Total > 3000?
↓
FALSO
↓
Não aplica desconto
↓
Total >= 3000?
↓
VERDADEIRO
↓
Pedido de alto valor
```

**Resultado esperado:** Aplicação do desconto de 5%: R$ 3.000 × 0,95 = R$ 2.850,00.

**Resultado apresentado:** Total = R$ 3.000,00. Mesmo assim, o sistema mostra "Pedido de alto valor."

**Erro identificado:** As duas condições utilizam limites diferentes: `total > 3000` e `total >= 3000`. Assim, um pedido de exatamente R$ 3.000 é classificado como alto valor, mas não recebe o desconto correspondente.

**Correção realizada:**

```javascript
if (total >= 3000) {
  total = total * 0.95;
}
```

**Resultado após a correção:** Com total inicial de R$ 3.000, o desconto é R$ 150,00 e o total final fica em R$ 2.850,00.

**Técnica utilizada:** Análise de valores-limite, cobertura de decisões e rastreamento de variáveis.

**Fluxograma do caminho analisado:**

```text
Total = R$ 3.000
↓
Total >= 3000?
↙ SIM              NÃO ↘
Aplica 5%          Mantém
↓
Total = R$ 2.850
```

## 5. Tabela dos Casos de Teste

| Teste | Dados principais | Erro analisado | Resultado esperado | Resultado original |
| --- | --- | --- | --- | --- |
| CT01 | Mouse / qtd. 0 | Validação | Quantidade inválida | Pedido calculado |
| CT02 | Mouse / qtd. 20 | Estoque | Pedido aceito | Estoque indisponível |
| CT03 | Subtotal R$ 1.000 / SENAI20 | Cupom | Sem desconto | 20% de desconto |
| CT04 | Subtotal R$ 500 / normal | Frete | R$ 30 de frete | Frete grátis |
| CT05 | Mouse / qtd. 5 | Desconto quantidade | Total R$ 410 | Total R$ 430 |
| CT06 | Notebook / qtd. 1 | Alto valor | Total R$ 2.850 | Total R$ 3.000 |

## 6. Resultados dos Testes

| Teste | Resultado esperado | Resultado obtido | Situação |
| --- | --- | --- | --- |
| CT01 | Quantidade inválida | Pedido calculado | ❌ FALHA |
| CT02 | Pedido aceito | Estoque indisponível | ❌ FALHA |
| CT03 | Sem desconto | Desconto de R$ 200 | ❌ FALHA |
| CT04 | Frete de R$ 30 | Frete grátis | ❌ FALHA |
| CT05 | Total de R$ 410 | Total de R$ 430 | ❌ FALHA |
| CT06 | Total de R$ 2.850 | Total de R$ 3.000 | ❌ FALHA |

Depois das correções, os seis testes foram executados novamente e passaram a apresentar o comportamento esperado.

## 7. Comparação das Correções

| Erro | Antes | Depois |
| --- | --- | --- |
| Quantidade | `qtd < 0` | `qtd <= 0` |
| Estoque | `qtd >= estoque` | `qtd > estoque` |
| Cupom SENAI20 | `subtotal >= 1000` | `subtotal > 1000` |
| Frete grátis | `subtotal >= 500` | `subtotal > 500` |
| Desconto por quantidade | `qtd > 5` | `qtd >= 5` |
| Pedido de alto valor | `total > 3000` | `total >= 3000` |

## 8. Análise dos Resultados

Os testes mostraram que os seis erros estavam relacionados a condições que utilizavam limites de forma incorreta.

Os primeiros testes foram mais simples, pois bastava analisar a condição e utilizar um valor que estivesse exatamente no limite. Já os testes mais difíceis exigiram acompanhar o valor das variáveis durante várias etapas do programa.

Um ponto que chamou atenção foi o erro do pedido de alto valor. Nesse caso, foi necessário comparar duas partes diferentes do código. Uma condição verificava `total > 3000`, enquanto outra utilizava `total >= 3000`. Isso fazia o sistema apresentar uma mensagem de pedido de alto valor, mas não aplicar o desconto correspondente.

A utilização dos valores-limite foi importante para encontrar os erros. Testar apenas valores muito acima ou muito abaixo dos limites poderia fazer o programa parecer correto.

Depois das correções, os mesmos testes foram executados novamente e os resultados passaram a corresponder ao comportamento esperado.

## 9. Conclusão

Com a realização da atividade, foi possível entender melhor como o teste de caixa branca pode ser utilizado para analisar um programa por dentro.

Através da leitura do código e da execução dos casos de teste, foram encontrados seis erros de lógica. Os problemas estavam principalmente relacionados ao uso dos operadores `>`, `>=`, `<` e `<=`.

Também foi possível perceber a importância de testar valores próximos aos limites das condições. Em alguns casos, uma única diferença no operador fazia o sistema aceitar ou rejeitar uma entrada incorretamente.

Após a identificação dos problemas, as condições foram corrigidas e os testes foram realizados novamente para verificar se o comportamento havia sido ajustado.

Dessa forma, a atividade mostrou na prática que analisar somente o resultado final não é suficiente. É importante entender o caminho que o programa percorre e verificar se cada decisão está levando ao resultado esperado.

## 10. Principais Trechos do Código Corrigido

```javascript
if (qtd <= 0) {
  resultado.innerHTML = "<p>Quantidade inválida.</p>";
  return;
}

if (qtd > estoque[produtoSelecionado]) {
  resultado.innerHTML = "<p>Quantidade indisponível em estoque.</p>";
  return;
}

function calcularDesconto(subtotal, codigo) {
  if (codigo === "SENAI10") {
    return subtotal * 0.10;
  }

  if (codigo === "SENAI20" && subtotal > 1000) {
    return subtotal * 0.20;
  }

  return 0;
}

function calcularFrete(tipo, subtotal) {
  if (tipo === "retirada") {
    return 0;
  }

  if (tipo === "expresso") {
    return 60;
  }

  if (subtotal > 500) {
    return 0;
  }

  return 30;
}

if (qtd >= 5) {
  total = total - subtotal * 0.05;
}

if (total >= 3000) {
  total = total * 0.95;
}
```
