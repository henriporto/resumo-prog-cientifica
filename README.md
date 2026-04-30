# Resumo Pré-Prova — Programação Científica P1

> **Como usar:** revisão rápida (~10 min) antes da prova. Cada método tem: ideia + fórmula + exemplo numérico resolvido + identificação no formulário + erros comuns.
>
> **Configuração obrigatória:**
> - Calculadora em **RAD** (não DEG).
> - **4 casas decimais** (arredondar ou truncar — leia o enunciado).
>
> **EDO de referência usada nos exemplos:**
> $$y' = y - t^2 + 1, \quad y(0) = 0.5, \quad h = 0.2$$
> Logo, $f(t, w) = w - t^2 + 1$.

---

## Conceitos Fundamentais

### Problema de Valor Inicial (PVI)

Queremos uma função $y(t)$ desconhecida, sabendo:

$$\frac{dy}{dt} = f(t, y), \quad a \le t \le b, \quad y(a) = \alpha$$

Ou seja, conhecemos a **velocidade de mudança** ($f$) e o **ponto de partida** ($\alpha$).

### Pontos de Malha

Dividimos o intervalo $[a, b]$ em $N$ pedaços iguais de tamanho $h$:

$$h = \frac{b - a}{N}, \quad t_i = a + i \cdot h$$

A solução numérica é uma sequência $w_0, w_1, w_2, \ldots, w_N$ onde **cada $w_i$ aproxima $y(t_i)$**.

### O que muda entre os métodos

Todos os métodos têm a forma genérica:
$$w_{i+1} = w_i + h \cdot \phi(t_i, w_i, h)$$

A função $\phi$ é o que distingue Euler de Taylor de Runge-Kutta. Quanto mais sofisticado o $\phi$, mais preciso (mas mais conta).

---

## 1. Método de Euler (Taylor de 1ª ordem)

### Ideia
A aproximação mais simples possível: "estou em $(t_i, w_i)$, conheço a inclinação $f(t_i, w_i)$, ando em linha reta um passo $h$ pra frente."

### Fórmula

$$\boxed{w_{i+1} = w_i + h \cdot f(t_i, w_i)}$$

### Exemplo resolvido (calcular $w_1$)

Dados: $t_0 = 0$, $w_0 = 0.5$, $h = 0.2$.

$$
\begin{aligned}
w_1 &= w_0 + h \cdot f(t_0, w_0) \\
    &= 0.5 + 0.2 \cdot (0.5 - 0^2 + 1) \\
    &= 0.5 + 0.2 \cdot 1.5 \\
    &= 0.5 + 0.3 \\
    &= 0.8000
\end{aligned}
$$

Próxima iteração ($w_2$): $t_1 = 0.2$, $w_1 = 0.8$:

$$w_2 = 0.8 + 0.2 \cdot (0.8 - 0.04 + 1) = 0.8 + 0.352 = 1.1520$$

### Identificação no formulário
- Fórmula **mais simples** de todas.
- Usa **só** o ponto atual $(t_i, w_i)$.
- Sem $k$'s, sem derivadas $f', f''$, sem $w_{i-1}$.

---

## 2. Método de Taylor (até 4ª ordem)

### Ideia
Em vez de aproximar a curva por uma reta (Euler), usamos um polinômio de grau maior. Mais derivadas = mais precisão.

### Fórmula geral

$$\boxed{w_{i+1} = w_i + h \cdot T^{(n)}(t_i, w_i)}$$

onde
$$T^{(n)}(t_i, w_i) = f + \frac{h}{2} f' + \frac{h^2}{6} f'' + \frac{h^3}{24} f''' + \cdots + \frac{h^{n-1}}{n!} f^{(n-1)}$$

Os coeficientes são $\frac{h^{k-1}}{k!}$ — fatoriais crescentes.

### Comparação rápida

| Ordem | $T^{(n)}$ | É equivalente a... |
|-------|-----------|---------------------|
| 1     | $f$       | Euler |
| 2     | $f + \frac{h}{2}f'$ | Ponto Médio (RK2) |
| 3     | $f + \frac{h}{2}f' + \frac{h^2}{6}f''$ | — |
| 4     | $f + \frac{h}{2}f' + \frac{h^2}{6}f'' + \frac{h^3}{24}f'''$ | — |

> ⚠️ "Ordem" significa **precisão**, não iteração. Taylor 4 = use a fórmula de 4ª ordem em **TODA** iteração ($w_1, w_2, w_3, \ldots$).

### Macete: como calcular $f', f'', f'''$ corretamente

Como $y$ depende de $t$, ao derivar você precisa usar **regra da cadeia**: toda vez que aparecer $y'$ na expressão, **substitua por $f(t, y)$** (o lado direito da EDO original).

Para $f(t, y) = y - t^2 + 1$:

$$
\begin{aligned}
f' &= y' - 2t = (y - t^2 + 1) - 2t = y - t^2 - 2t + 1 \\
f'' &= y' - 2t - 2 = (y - t^2 + 1) - 2t - 2 = y - t^2 - 2t - 1 \\
f''' &= y' - 2t - 2 = y - t^2 - 2t - 1 \quad (\text{igual a } f'' \text{ neste caso!})
\end{aligned}
$$

### Exemplo resolvido (Taylor 4ª ordem, calcular $w_1$)

Dados: $t_0 = 0$, $w_0 = 0.5$, $h = 0.2$.

**Avaliando as derivadas em $(0, 0.5)$:**

| | Expressão | Valor |
|---|---|---|
| $f$    | $0.5 - 0 + 1$       | $1.5$  |
| $f'$   | $0.5 - 0 - 0 + 1$   | $1.5$  |
| $f''$  | $0.5 - 0 - 0 - 1$   | $-0.5$ |
| $f'''$ | $0.5 - 0 - 0 - 1$   | $-0.5$ |

**Calculando os coeficientes (constantes pra todo o problema):**

$$\frac{h}{2} = 0.1, \quad \frac{h^2}{6} \approx 0.006667, \quad \frac{h^3}{24} \approx 0.000333$$

**Montando $T^{(4)}$:**

$$
\begin{aligned}
T^{(4)} &= 1.5 + (0.1)(1.5) + (0.006667)(-0.5) + (0.000333)(-0.5) \\
        &= 1.5 + 0.15 - 0.003333 - 0.000167 \\
        &\approx 1.6465
\end{aligned}
$$

**Resultado final:**

$$w_1 = 0.5 + 0.2 \cdot 1.6465 = 0.8293$$

### Identificação no formulário
- Aparecem $f, f', f'', f'''$ (derivadas explícitas).
- Coeficientes envolvendo **fatoriais**: $\frac{1}{2}, \frac{1}{6}, \frac{1}{24}, \ldots$
- Não tem $k_1, k_2$.

---

## 3. Runge-Kutta 4 (RK4) — A Fórmula de Ouro

### Ideia
Atinge a precisão de Taylor 4 **sem calcular nenhuma derivada**. Em vez disso, avalia $f$ em **4 pontos espertos** dentro do intervalo $[t_i, t_{i+1}]$ e faz uma média ponderada.

### Fórmula

$$
\begin{aligned}
k_1 &= h \cdot f(t_i, w_i) \\
k_2 &= h \cdot f\!\left(t_i + \tfrac{h}{2},\; w_i + \tfrac{k_1}{2}\right) \\
k_3 &= h \cdot f\!\left(t_i + \tfrac{h}{2},\; w_i + \tfrac{k_2}{2}\right) \\
k_4 &= h \cdot f(t_i + h,\; w_i + k_3) \\[4pt]
\boxed{w_{i+1} = w_i + \tfrac{1}{6}(k_1 + 2k_2 + 2k_3 + k_4)}
\end{aligned}
$$

**Pesos finais:** $1, 2, 2, 1$ (os do meio dobram). Divisor $6$.

### Tabela de "onde avalia"

| Chave | Tempo $t$ | Valor $w$ |
|---|---|---|
| $k_1$ | $t_i$ (início) | $w_i$ |
| $k_2$ | $t_i + h/2$ (meio) | $w_i + k_1/2$ |
| $k_3$ | $t_i + h/2$ (meio) | $w_i + k_2/2$ |
| $k_4$ | $t_i + h$ (fim) | $w_i + k_3$ |

> $k_2$ e $k_3$ usam o **mesmo** $t = t_i + h/2$. Só muda o $w$.

### Exemplo resolvido (calcular $w_1$)

Dados: $t_0 = 0$, $w_0 = 0.5$, $h = 0.2$.

**Cálculo das chaves:**

$$
\begin{aligned}
k_1 &= 0.2 \cdot f(0, 0.5) = 0.2 \cdot (0.5 - 0 + 1) = 0.2 \cdot 1.5 = 0.3000 \\[4pt]
k_2 &= 0.2 \cdot f(0.1,\; 0.5 + 0.15) = 0.2 \cdot f(0.1, 0.65) \\
    &= 0.2 \cdot (0.65 - 0.01 + 1) = 0.2 \cdot 1.64 = 0.3280 \\[4pt]
k_3 &= 0.2 \cdot f(0.1,\; 0.5 + 0.164) = 0.2 \cdot f(0.1, 0.664) \\
    &= 0.2 \cdot (0.664 - 0.01 + 1) = 0.2 \cdot 1.654 = 0.3308 \\[4pt]
k_4 &= 0.2 \cdot f(0.2,\; 0.5 + 0.3308) = 0.2 \cdot f(0.2, 0.8308) \\
    &= 0.2 \cdot (0.8308 - 0.04 + 1) = 0.2 \cdot 1.7908 = 0.3582
\end{aligned}
$$

**Combinando:**

$$
\begin{aligned}
w_1 &= 0.5 + \tfrac{1}{6}(0.3000 + 2 \cdot 0.3280 + 2 \cdot 0.3308 + 0.3582) \\
    &= 0.5 + \tfrac{1}{6}(0.3000 + 0.6560 + 0.6616 + 0.3582) \\
    &= 0.5 + \tfrac{1.9758}{6} \\
    &= 0.5 + 0.3293 \\
    &= 0.8293
\end{aligned}
$$

### Estratégia de calculadora (HP 10s+)

1. Calcule $k_1$ e guarde: `STO A`.
2. Calcule $k_2$ e guarde: `STO B`.
3. Calcule $k_3$ e guarde: `STO C`.
4. Calcule $k_4$ e guarde: `STO D`.
5. Combinador final: `0.5 + ( ALPHA A + 2 × ALPHA B + 2 × ALPHA C + ALPHA D ) ÷ 6 =`.

### Erros comuns
- Trocar $k_1/2$ por $k_1$ (esqueceu da metade).
- Esquecer os **pesos 2** nos termos do meio.
- Esquecer de dividir por **6** no final.
- Usar $t_i$ no $k_4$ em vez de $t_i + h$.

### Identificação no formulário
- 4 chaves $k_1, k_2, k_3, k_4$.
- Combinador final exato: $\frac{1}{6}(k_1 + 2k_2 + 2k_3 + k_4)$.
- Sem derivadas $f'$. Sem $w_{i-1}$.

---

## 4. Runge-Kutta-Fehlberg (RKF) — Controle Adaptativo

### Ideia
Em vez de usar $h$ fixo, **ajusta automaticamente o tamanho do passo** durante a iteração: aumenta quando o erro é pequeno, diminui quando é grande.

### Estratégia
A cada passo, calcula DUAS aproximações usando 6 chaves $k_1, \ldots, k_6$:
- $w_{i+1}$ → RK de **4ª ordem** (menos preciso)
- $\tilde{w}_{i+1}$ → RK de **5ª ordem** (mais preciso)

A diferença $|\tilde{w}_{i+1} - w_{i+1}|$ é a **estimativa do erro local**.

### Fórmula do fator $q$

$$\boxed{q = 0.84 \cdot \left( \frac{\varepsilon \cdot h}{|\tilde{w}_{i+1} - w_{i+1}|} \right)^{1/4}}$$

onde:
- $\varepsilon$ = tolerância especificada
- $0.84$ = fator de segurança (constante específica do RK-Fehlberg)
- expoente $1/4$ = porque é RK de 4ª ordem

### Regra de decisão (questão teórica provável)

| Caso | Decisão | Próximo passo |
|------|---------|---------------|
| $q < 1$ | **REJEITA** o passo atual e recalcula a iteração com novo $h$ | $h_{\text{novo}} = q \cdot h$ (menor) |
| $q \ge 1$ | **ACEITA** o $w_{i+1}$ calculado | $h_{\text{novo}} = q \cdot h$ (maior ou igual), usado na próxima iteração |

**Mnemônico:** $q < 1$ = passo CARO (volta atrás); $q \ge 1$ = passo BARATO (segue).

### Exemplo de questão teórica

> "Aplicando RK-Fehlberg, obteve-se $q = 0.7$. O passo é aceito ou rejeitado?"

**Resposta:** Como $q = 0.7 < 1$, o passo é **rejeitado**. A iteração atual deve ser recalculada com $h_{\text{novo}} = 0.7 \cdot h$.

> "Obteve-se $q = 1.3$. Qual o próximo passo?"

**Resposta:** Como $q \ge 1$, o passo é **aceito**. Para a próxima iteração usa-se $h_{\text{novo}} = 1.3 \cdot h$.

### Identificação no formulário
- 6 chaves $k_1, k_2, \ldots, k_6$.
- Dois resultados ($w$ de 4ª ordem e $\tilde{w}$ de 5ª ordem).
- Constante **$0.84$**.

---

## 5. Métodos de Passos Múltiplos (Adams)

### Ideia
Métodos anteriores eram **passo simples**: usavam só $(t_i, w_i)$ pra calcular $w_{i+1}$. Multistep usa **vários pontos passados** ($w_{i-1}, w_{i-2}, w_{i-3}, \ldots$). Mais informação = mais precisão.

> ⚠️ **Não consegue arrancar do zero!** Os primeiros valores $w_0, w_1, w_2, w_3$ devem ser obtidos com **RK4 ou Taylor** antes de começar Adams.

### 5a. Adams-Bashforth (EXPLÍCITO)

$w_{i+1}$ depende **só** de pontos passados — calcula direto.

**Adams-Bashforth de 4 passos** (mais cobrado):

$$\boxed{w_{i+1} = w_i + \frac{h}{24}\left[55 f_i - 59 f_{i-1} + 37 f_{i-2} - 9 f_{i-3}\right]}$$

onde $f_j \equiv f(t_j, w_j)$. Coeficientes: $\{55, -59, 37, -9\}$. **Atenção aos sinais.**

Outras versões (no formulário, distratoras):

| Passos | Fórmula |
|---|---|
| 2 | $w_{i+1} = w_i + \frac{h}{2}[3 f_i - f_{i-1}]$ |
| 3 | $w_{i+1} = w_i + \frac{h}{12}[23 f_i - 16 f_{i-1} + 5 f_{i-2}]$ |
| 4 | $w_{i+1} = w_i + \frac{h}{24}[55 f_i - 59 f_{i-1} + 37 f_{i-2} - 9 f_{i-3}]$ |
| 5 | $w_{i+1} = w_i + \frac{h}{720}[1901 f_i - 2774 f_{i-1} + 2616 f_{i-2} - 1274 f_{i-3} + 251 f_{i-4}]$ |

### 5b. Adams-Moulton (IMPLÍCITO)

Usa também $f(t_{i+1}, w_{i+1})$ no lado direito — ou seja, $w_{i+1}$ aparece **dos dois lados** da equação. Não dá pra resolver direto.

**Adams-Moulton de 3 passos** (par natural do Bashforth-4):

$$\boxed{w_{i+1} = w_i + \frac{h}{24}\left[9 f(t_{i+1}, w_{i+1}) + 19 f_i - 5 f_{i-1} + f_{i-2}\right]}$$

Coeficientes: $\{9, 19, -5, 1\}$.

Outras versões:

| Passos | Fórmula |
|---|---|
| 2 | $w_{i+1} = w_i + \frac{h}{12}[5 f(t_{i+1}, w_{i+1}) + 8 f_i - f_{i-1}]$ |
| 3 | $w_{i+1} = w_i + \frac{h}{24}[9 f(t_{i+1}, w_{i+1}) + 19 f_i - 5 f_{i-1} + f_{i-2}]$ |
| 4 | $w_{i+1} = w_i + \frac{h}{720}[251 f(t_{i+1}, w_{i+1}) + 646 f_i - 264 f_{i-1} + 106 f_{i-2} - 19 f_{i-3}]$ |

### 5c. Preditor-Corretor

Combina o explícito (preditor) com o implícito (corretor).

**Algoritmo (Bashforth-4 + Moulton-3):**

1. **Inicialização:** Use RK4 pra obter $w_0, w_1, w_2, w_3$.

2. **PREDITOR** (Adams-Bashforth 4) — chute inicial:
   $$w_{i+1}^{(0)} = w_i + \frac{h}{24}\left[55 f_i - 59 f_{i-1} + 37 f_{i-2} - 9 f_{i-3}\right]$$

3. **CORRETOR** (Adams-Moulton 3, **substituindo** $w_{i+1}$ por $w_{i+1}^{(0)}$ no lado direito):
   $$w_{i+1}^{(1)} = w_i + \frac{h}{24}\left[9 f(t_{i+1}, w_{i+1}^{(0)}) + 19 f_i - 5 f_{i-1} + f_{i-2}\right]$$

4. $w_{i+1}^{(1)}$ é a aproximação final. Avança para a próxima iteração.

### Exemplo resolvido (Preditor-Corretor)

Dados (assumindo que RK4 já forneceu os valores iniciais):

| $i$ | $t_i$ | $w_i$  | $f_i = w_i - t_i^2 + 1$ |
|-----|-------|--------|--------------------------|
| 0 | 0.0 | 0.5000 | 1.5000 |
| 1 | 0.2 | 0.8293 | 1.7893 |
| 2 | 0.4 | 1.2141 | 2.0541 |
| 3 | 0.6 | 1.6489 | 2.2889 |

**Objetivo:** calcular $w_4$ em $t_4 = 0.8$.

**Constante útil:** $\frac{h}{24} = \frac{0.2}{24} \approx 0.008333$.

**Passo 1 — Preditor (Bashforth-4):**

$$
\begin{aligned}
w_4^{(0)} &= w_3 + \tfrac{h}{24}\left[55 f_3 - 59 f_2 + 37 f_1 - 9 f_0\right] \\
&= 1.6489 + 0.008333 \cdot [55(2.2889) - 59(2.0541) + 37(1.7893) - 9(1.5000)] \\
&= 1.6489 + 0.008333 \cdot [125.8895 - 121.1919 + 66.2041 - 13.5000] \\
&= 1.6489 + 0.008333 \cdot 57.4017 \\
&\approx 1.6489 + 0.4783 \\
&\approx 2.1272
\end{aligned}
$$

**Avaliando $f$ no preditor:**

$$f(t_4, w_4^{(0)}) = f(0.8, 2.1272) = 2.1272 - 0.64 + 1 = 2.4872$$

**Passo 2 — Corretor (Moulton-3):**

$$
\begin{aligned}
w_4^{(1)} &= w_3 + \tfrac{h}{24}\left[9 f(t_4, w_4^{(0)}) + 19 f_3 - 5 f_2 + f_1\right] \\
&= 1.6489 + 0.008333 \cdot [9(2.4872) + 19(2.2889) - 5(2.0541) + 1.7893] \\
&= 1.6489 + 0.008333 \cdot [22.3848 + 43.4891 - 10.2705 + 1.7893] \\
&= 1.6489 + 0.008333 \cdot 57.3927 \\
&\approx 2.1272
\end{aligned}
$$

**Resultado final:** $w_4 \approx 2.1272$.

### Controle de erro em multistep (constante 1.5)

$$\boxed{q = 1.5 \cdot \left( \frac{h \cdot \varepsilon}{|w_{i+1}^{(1)} - w_{i+1}^{(0)}|} \right)^{1/4}}$$

> ⚠️ Constante **$1.5$** (não $0.84$ como no RK-Fehlberg). **Pegadinha favorita do professor.**

A regra de decisão é IDÊNTICA à do RKF:
- $q < 1$: **rejeita**, recalcula com $h_{\text{novo}} = q \cdot h$.
- $q \ge 1$: **aceita**, usa $h_{\text{novo}} = q \cdot h$ na próxima.

### Identificação no formulário

| Aparece... | É... |
|---|---|
| $f(t_{i-1}, w_{i-1})$, $f(t_{i-2}, w_{i-2})$ | Multistep (Adams) |
| Coeficientes inteiros divididos por 12, 24, 720 | Adams |
| **Sem** $f(t_{i+1}, w_{i+1})$ no lado direito | EXPLÍCITO (Bashforth) |
| **Com** $f(t_{i+1}, w_{i+1})$ no lado direito | IMPLÍCITO (Moulton) |
| Constante $1.5$ no controle de erro | Multistep adaptativo |

---

## Tabela-Resumo: Como Identificar o Método

Olhe a fórmula no formulário e use essa árvore de decisão:

| Sinal característico | Método |
|---|---|
| Só $f(t_i, w_i)$, nada mais | **Euler** |
| Aparecem $f', f''$ + fatoriais $(1/2, 1/6, 1/24)$ | **Taylor** |
| 4 chaves $k_1, k_2, k_3, k_4$ + pesos $1,2,2,1$ + divisor $6$ | **RK4** |
| 6 chaves $k_1,\ldots,k_6$ + dois resultados $w$ e $\tilde{w}$ + constante $0.84$ | **RK-Fehlberg** |
| $f(t_{i-1}, w_{i-1})$, $f(t_{i-2}, w_{i-2})$, sem $f(t_{i+1},\cdot)$ | **Adams-Bashforth** |
| $f(t_{i+1}, w_{i+1})$ aparece à direita | **Adams-Moulton** |
| Constante $1.5$ no controle de erro | **Preditor-Corretor multistep** |

---

## Constantes-Chave (não confundir!)

| Método | Constante de $q$ | Expoente |
|---|---|---|
| RK-Fehlberg | **$0.84$** | $1/4$ |
| Multistep (preditor-corretor) | **$1.5$** | $1/4$ |

**Regra do $q$ é a mesma nos dois:** $q < 1$ rejeita, $q \ge 1$ aceita; próximo $h = q \cdot h$.

---

## Checklist de Prova

- [ ] Calculadora ligada e em **RAD** (símbolo R no display).
- [ ] **4 casas decimais** (verificar se enunciado pede truncar ou arredondar).
- [ ] Antes de calcular, **identificar o método** pelo formato da fórmula.
- [ ] Anotar: $f(t,y)$, $a$, $b$, $\alpha$, $h$ (ou $N$ — calcular $h$).
- [ ] Conferir sinais nos coeficientes (especialmente em Adams).
- [ ] Em multistep: **inicializar com RK4 antes**.
- [ ] No fator $q$: usar $0.84$ (RK) ou $1.5$ (multistep) — não trocar!

---

## Erros Mortais a Evitar

1. **Calculadora em DEG** quando devia ser RAD (problemas com $\sin, \cos$).
2. **RK4:** trocar $k_1/2$ por $k_1$ — sempre METADE em $k_2$ e $k_3$.
3. **RK4:** esquecer pesos $2$ nos termos do meio ou esquecer dividir por $6$.
4. **Taylor:** derivar sem regra da cadeia — sempre **substituir $y'$ por $f(t,y)$**.
5. **Taylor:** confundir "ordem" com "iteração" — Taylor 4 = use 4ª ordem em **TODAS** as iterações.
6. **Multistep:** pular a inicialização — sem $w_0, w_1, w_2, w_3$ por RK4, não dá pra começar.
7. **Multistep:** errar **sinais** dos coeficientes ($55, -59, 37, -9$ no Bashforth-4).
8. **Adaptativo:** usar $1.5$ (multistep) em vez de $0.84$ (RK) ou vice-versa.
9. **Controle de erro:** confundir a regra — $q < 1$ **REJEITA**, $q \ge 1$ **ACEITA**.

---

## Referências

- `notas_de_aula.pdf` — material principal do professor.
- `formulario_passos_multiplos.pdf` — formulário oficial da prova.
- `mapeamento_temas_da_p1_para_notas_de_aula.md` — guia rápido de páginas por tópico.
