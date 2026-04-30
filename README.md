# 📋 Resumo Pré-Prova — Programação Científica P1

> Revisão rápida de 10 min. Cada método tem: fórmula + exemplo numérico curto.
> Calculadora **EM RAD**. Truncar/arredondar em **4 casas decimais**.
> EDO de referência: `y' = y - t² + 1`, `y(0) = 0.5`, `h = 0.2`. Logo `f(t,w) = w - t² + 1`.

---

## 1️⃣ MÉTODO DE EULER (Taylor de 1ª ordem)

### Fórmula
```
w_0 = α
w_{i+1} = w_i + h · f(t_i, w_i)
```

### Exemplo (calcular w_1):
```
i=0: t_0=0, w_0=0.5
w_1 = 0.5 + 0.2·(0.5 - 0² + 1)
    = 0.5 + 0.2·(1.5)
    = 0.5 + 0.3
    = 0.8000
```

### Identificação no formulário
- **Único** que usa só o ponto atual `(t_i, w_i)`.
- Sem k's. Sem derivadas. Sem `w_{i-1}`.

---

## 2️⃣ MÉTODO DE TAYLOR (até 4ª ordem)

### Fórmula
```
w_{i+1} = w_i + h · T^(n)(t_i, w_i)

T^(n) = f + (h/2)·f' + (h²/6)·f'' + (h³/24)·f''' + ...
```
Coeficientes: `1, h/2!, h²/3!, h³/4!` (fatoriais).

### Macete pra calcular f', f'', f'''
Toda vez que aparecer `y'` ao derivar, **substitua por f(t,y)** (lado direito da EDO).

Para `f = w - t² + 1`:
- `f'  = w - t² - 2t + 1`
- `f'' = w - t² - 2t - 1`
- `f'''= w - t² - 2t - 1`  ← igual a f'' nesse caso

### Exemplo (calcular w_1, ordem 4):
```
i=0: t_0=0, w_0=0.5, h=0.2
f    = 1.5      f'   = 1.5
f''  = -0.5     f''' = -0.5

T^(4) = 1.5 + (0.1)·1.5 + (0.04/6)·(-0.5) + (0.008/24)·(-0.5)
      = 1.5 + 0.15 - 0.0033 - 0.0002
      ≈ 1.6465

w_1 = 0.5 + 0.2·1.6465 = 0.8293
```

### ⚠️ Atenção
- A "ordem" significa **precisão**, não iteração. Taylor 4 = use a fórmula de ordem 4 em TODA iteração.
- Identificação: tem `f, f', f''...` e fatoriais (1/2, 1/6, 1/24).

---

## 3️⃣ RUNGE-KUTTA 4 (RK4) — A Fórmula de Ouro 👑

### Fórmula
```
k_1 = h · f(t_i,        w_i)
k_2 = h · f(t_i + h/2,  w_i + k_1/2)
k_3 = h · f(t_i + h/2,  w_i + k_2/2)
k_4 = h · f(t_i + h,    w_i + k_3)

w_{i+1} = w_i + (1/6) · (k_1 + 2·k_2 + 2·k_3 + k_4)
```

### Exemplo (calcular w_1):
```
t_0=0, w_0=0.5, h=0.2

k_1 = 0.2·f(0,    0.5)    = 0.2·(0.5 - 0 + 1)    = 0.2·1.5    = 0.3000
k_2 = 0.2·f(0.1,  0.65)   = 0.2·(0.65 - 0.01 + 1)= 0.2·1.64   = 0.3280
k_3 = 0.2·f(0.1,  0.664)  = 0.2·(0.664 -0.01 +1) = 0.2·1.654  = 0.3308
k_4 = 0.2·f(0.2,  0.8308) = 0.2·(0.8308-0.04 +1) = 0.2·1.7908 = 0.3582

w_1 = 0.5 + (0.3000 + 2·0.3280 + 2·0.3308 + 0.3582)/6
    = 0.5 + 1.9758/6
    = 0.5 + 0.3293
    = 0.8293
```

### ⚠️ Erros comuns
- k_2 e k_3 usam o MESMO `t = t_i + h/2`. Só muda o `w`.
- Pesos finais: **1, 2, 2, 1** (k_2 e k_3 dobram). Dividir por 6.
- Usa `k_1/2` e `k_2/2` (METADE), não inteiro. Mas `k_3` inteiro em `k_4`.

### Identificação no formulário
- 4 chaves k. Combinador final `(1/6)·(k_1 + 2k_2 + 2k_3 + k_4)`.

---

## 4️⃣ RK-FEHLBERG (RKF) — Controle Adaptativo

### Estratégia
Calcula DUAS aproximações no mesmo passo (RK4 e RK5) e usa a diferença como erro.
Tem 6 chaves `k_1 ... k_6` (no formulário).

### Fórmula do fator q (RK)
```
q = 0.84 · ( ε·h / |w̃_{i+1} - w_{i+1}| )^(1/4)
```
- `ε` = tolerância
- `0.84` = constante conservadora (RK)
- raiz **quarta**

### REGRA DE DECISÃO ⭐ (questão teórica provável)

| q | Ação | Próximo h |
|---|---|---|
| **q < 1** | **REJEITA** o passo, recalcula a iteração atual | `q·h` (menor) |
| **q ≥ 1** | **ACEITA** o passo, avança | `q·h` (maior ou igual), na próxima iteração |

Mnemônico: q < 1 = passo CARO (volta); q ≥ 1 = passo BARATO (segue).

### Identificação no formulário
- 6 chaves k. Dois resultados: `w` (4ª) e `w̃` (5ª). Constante **0.84**.

---

## 5️⃣ MÉTODOS DE PASSOS MÚLTIPLOS (Adams)

> Usam VÁRIOS pontos anteriores `w_{i-1}, w_{i-2}, ...` na fórmula.
> Precisam de "valores iniciais" obtidos por RK4/Taylor antes de começar.

### 5a) Adams-Bashforth (EXPLÍCITO) — "Preditor"

**4 passos** (mais usado em prova):
```
w_{i+1} = w_i + (h/24)·[55·f_i - 59·f_{i-1} + 37·f_{i-2} - 9·f_{i-3}]
```
onde `f_j = f(t_j, w_j)`. Precisa de `w_0, w_1, w_2, w_3` iniciais.

Outras versões no formulário:
- 2 passos: `w_{i+1} = w_i + (h/2)·[3·f_i - f_{i-1}]`
- 3 passos: `w_{i+1} = w_i + (h/12)·[23·f_i - 16·f_{i-1} + 5·f_{i-2}]`

### 5b) Adams-Moulton (IMPLÍCITO) — "Corretor"

**3 passos** (par natural do Bashforth-4):
```
w_{i+1} = w_i + (h/24)·[9·f(t_{i+1}, w_{i+1}) + 19·f_i - 5·f_{i-1} + f_{i-2}]
```

⚠️ `w_{i+1}` aparece dos DOIS lados (implícito). Por isso é usado em par com explícito.

### 5c) Preditor-Corretor (Bashforth-4 + Moulton-3)

**Passo a passo:**
```
1. Use RK4 pra obter w_0, w_1, w_2, w_3.

2. PREDITOR (Adams-Bashforth 4):
   w^(0)_{i+1} = w_i + (h/24)·[55·f_i - 59·f_{i-1} + 37·f_{i-2} - 9·f_{i-3}]

3. CORRETOR (Adams-Moulton 3, usando w^(0)_{i+1} no lado direito):
   w^(1)_{i+1} = w_i + (h/24)·[9·f(t_{i+1}, w^(0)_{i+1}) + 19·f_i - 5·f_{i-1} + f_{i-2}]

4. (Opcional) repetir o corretor com w^(1)_{i+1} pra refinar.
```

### Controle de erro em multistep (constante 1.5)

```
q = 1.5 · ( h·ε / |w^(1)_{i+1} - w^(0)_{i+1}| )^(1/4)
```
- Mesma regra de decisão: q < 1 rejeita, q ≥ 1 aceita.
- ⚠️ A constante aqui é **1.5** (não 0.84). É uma das pegadinhas favoritas.

### Identificação no formulário
- Fórmulas com `f(t_{i-1}, w_{i-1})`, `f(t_{i-2}, w_{i-2})`, etc. → multistep.
- Coeficientes inteiros grandes divididos por 12, 24, 720 → Adams.
- `f(t_{i+1}, w_{i+1})` aparecendo do lado direito → IMPLÍCITO (Moulton).
- Sem `f(t_{i+1}, w_{i+1})` → EXPLÍCITO (Bashforth).

---

## 🎯 CHECKLIST DE PROVA

- [ ] Calculadora em **RAD** (R no display).
- [ ] **4 casas decimais** (truncar OU arredondar — leia o enunciado).
- [ ] Antes de calcular, identifique: `f(t,y)`, `a`, `b`, `α`, `h` (ou N).
- [ ] Reconheça o método pelo formato da fórmula (não decore, identifique).

### Fórmula → método
| Aparece... | É... |
|---|---|
| Só `f(t_i, w_i)` | Euler |
| `f, f', f''` + fatoriais | Taylor |
| `k_1, k_2, k_3, k_4`, pesos 1,2,2,1, ÷6 | RK4 |
| `k_1...k_6`, dois resultados, **0.84** | RK-Fehlberg |
| `f(t_{i-1}, w_{i-1})`, `f(t_{i-2}, w_{i-2})`... | Adams (multistep) |
| `f(t_{i+1}, w_{i+1})` no lado direito | Adams-Moulton (implícito) |
| Constante **1.5** no controle de erro | Preditor-corretor multistep |

### Constantes-chave
- RK-Fehlberg: **0.84**
- Multistep preditor-corretor: **1.5**
- Tanto faz q < 1 rejeita, q ≥ 1 aceita (regra é a mesma).

---

## 📌 Erros mortais a evitar

1. Calculadora em DEG quando devia ser RAD (em problemas com sen/cos).
2. RK4: trocar `k_1/2` por `k_1` (lembrar da METADE em k_2 e k_3).
3. RK4: esquecer pesos 2 nos termos do meio.
4. Taylor: aplicar derivadas sem regra da cadeia (esquecer de trocar `y'` por `f`).
5. Multistep: não calcular os valores iniciais `w_0, w_1, ...` por RK4 antes.
6. Fehlberg: usar `1.5` (multistep) em vez de `0.84` (RK).
