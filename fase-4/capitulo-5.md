# 📘 Capítulo 5 — Os Ajustes Lineares que Refinam as Previsões de Autonomia

> Resumo do capítulo para a disciplina de **Matemática / Estatística Aplicada** — FIAP  
> Autor do material: Prof. (elaborado em 2026)
---

## 👤 Informações do Aluno

- **Nome:** Isabelle Caroline de Camargo Francisco  
- **RM:** 572096  
- **Curso:** Ciência da Computação EAD  
- **Instituição:** FIAP  
- **Disciplina:** Matemática / Estatística Aplicada  

---

## 📌 Assuntos Abordados

- **Regressão Linear Múltipla** — Conceito, expansão e aplicações práticas
- **Representação Matricial** — Vetores Y, β, ε e Matriz X
- **Estimação e Inferência** — Mínimos quadrados, teste t e teste F
- **Diagnóstico do Modelo** — Resíduos, outliers, alavancagem e VIF
- **Implementação em Python** — NumPy, pandas, scikit-learn e statsmodels

---

## 🧠 Visão Geral

A regressão linear múltipla é uma técnica estatística que estuda como uma variável resposta se relaciona com várias variáveis explicativas simultaneamente. Enquanto a regressão simples usa um único fator, a versão múltipla considera vários preditores ao mesmo tempo — o que aproxima o modelo de fenômenos reais em ciência de dados, computação e engenharia. O capítulo cobre desde a formulação matemática até a implementação computacional em Python, passando pela validação estatística dos resultados.

---

## 📂 Conceitos Fundamentais

### 1️⃣ Regressão Linear Múltipla

O modelo geral com k variáveis explicativas é escrito como:

Y = β₀ + β₁X₁ + β₂X₂ + ... + βₖXₖ + ε

Onde:
- **β₀** é o intercepto — valor esperado de Y quando todas as variáveis explicativas valem zero
- **β₁, β₂, ..., βₖ** são os coeficientes de cada variável explicativa
- **ε** é o erro aleatório — parte de Y não explicada pelo modelo

> Cada coeficiente βᵢ indica quanto Y tende a variar quando Xᵢ aumenta uma unidade, **mantendo todas as demais variáveis constantes** (princípio *ceteris paribus*).

#### 🔹 Exemplo prático: desempenho de um servidor

| i | X₁ (usuários) | X₂ (dados_MB) | Y (tempo_ms) |
|---|---|---|---|
| 1 | 10 | 2 | 120 |
| 2 | 20 | 4 | 150 |
| 3 | 30 | 6 | 180 |
| 4 | 40 | 8 | 210 |
| 5 | 50 | 10 | 240 |
| 6 | 60 | 12 | 270 |
| 7 | 70 | 14 | 300 |
| 8 | 80 | 16 | 330 |

Modelo ajustado: **tempo_ms = 90 + 2,88(n_usuarios) + 0,58(dados_MB)**

Interpretação dos coeficientes:
- **β₀ ≈ 90** → tempo base do servidor (custo fixo de funcionamento em ms)
- **β₁ ≈ 2,88** → cada usuário adicional aumenta ~2,88 ms no tempo de resposta
- **β₂ ≈ 0,58** → cada 1MB adicional por requisição aumenta ~0,58 ms

---

### 2️⃣ Representação Matricial

O modelo completo em forma matricial é: **Y = Xβ + ε**

Essa notação organiza todo o modelo em uma única equação e é a base das bibliotecas Python (NumPy, scikit-learn, statsmodels).

#### 🔹 Componentes do modelo

| Componente | Descrição | Dimensão |
|---|---|---|
| **Vetor Y** | Variável resposta (coluna) | n × 1 |
| **Matriz X** | Coluna de 1s + variáveis explicativas | n × (k+1) |
| **Vetor β** | Coeficientes a estimar [β₀, β₁, ..., βₖ]ᵗ | (k+1) × 1 |
| **Vetor ε** | Erros/resíduos [ε₁, ε₂, ..., εₙ]ᵗ | n × 1 |

> ⚠️ A primeira coluna da Matriz X é sempre composta por 1s — ela representa o intercepto β₀ e é indispensável para o modelo.

#### 🔹 Construção em Python (NumPy + pandas)

```python
import numpy as np
import pandas as pd

dados = pd.DataFrame({
    "n_usuarios": [10, 20, 30, 40, 50, 60, 70, 80],
    "dados_MB":   [2,  4,  6,  8,  10, 12, 14, 16],
    "tempo_ms":   [120,150,180,210,240,270,300,330]
})

Y = dados["tempo_ms"].values.reshape(-1, 1)

n = len(dados)
coluna_ones = np.ones((n, 1))
X = np.hstack([coluna_ones, dados[["n_usuarios", "dados_MB"]].values])
```

---

### 3️⃣ Estimação dos Coeficientes

O método dos **Mínimos Quadrados Ordinários (OLS)** encontra o vetor β̂ que minimiza a soma dos erros quadráticos:

β̂ = (XᵀX)⁻¹ XᵀY

> ⚠️ Na prática, calcular a inversa diretamente pode gerar instabilidade numérica. Usa-se `np.linalg.lstsq`, que resolve o problema de forma robusta sem inverter matrizes.

#### 🔹 Via NumPy (mínimos quadrados direto)

```python
beta_hat, residuals, rank, s = np.linalg.lstsq(X, Y, rcond=None)
print("Coeficientes estimados (β̂):", beta_hat)
```

#### 🔹 Via scikit-learn (forma mais comum em ML)

```python
from sklearn.linear_model import LinearRegression

X_pred = dados[["n_usuarios", "dados_MB"]].values
Y_resp = dados["tempo_ms"].values

modelo = LinearRegression()  # fit_intercept=True por padrão
modelo.fit(X_pred, Y_resp)

print("Intercepto (β0):", modelo.intercept_)
print("Coeficientes (β1, β2):", modelo.coef_)
```

> Na implementação com scikit-learn, **não é necessário criar manualmente a coluna de 1s** — o modelo faz isso internamente quando `fit_intercept=True`.

---

## 📊 Estimação e Inferência

### 4️⃣ Teste t e Teste F

Após estimar os coeficientes, verificamos se eles são **estatisticamente significativos** ou podem ter surgido apenas por acaso.

| Teste | O que avalia | Pergunta central |
|---|---|---|
| **Teste t** | Cada coeficiente individualmente | "A variável Xᵢ contribui significativamente para explicar Y?" |
| **Teste F** | O modelo como um todo | "O conjunto de variáveis melhora o ajuste em relação ao modelo nulo?" |

#### 🔹 Interpretação dos resultados

- **P-valor < 0,05** → coeficiente ou modelo estatisticamente significativo
- **R²** → proporção da variância de Y explicada pelo modelo (quanto mais próximo de 1, melhor)
- **R² ajustado** → penaliza pelo número de variáveis; mais confiável para comparar modelos

#### 🔹 Implementação com statsmodels

```python
import statsmodels.api as sm

X_pred = dados[["n_usuarios", "dados_MB"]]
X_pred = sm.add_constant(X_pred)  # adiciona coluna de 1s automaticamente

Y_resp = dados["tempo_ms"]

modelo = sm.OLS(Y_resp, X_pred).fit()
print(modelo.summary())
```

O `summary()` retorna: estimativas de β, erro padrão, valor t, p-valor, intervalo de confiança, estatística F e p-valor do F.

---

## 🔍 Diagnóstico do Modelo

### 5️⃣ Análise de Resíduos

Resíduo: **εᵢ = yᵢ − ŷᵢ** (valor real − valor previsto)

Para um modelo adequado, os resíduos devem:
- Ter **média próxima de zero** (ausência de viés)
- Apresentar **variância constante** (homocedasticidade)
- Não exibir **padrões ou tendências** nas observações
- Seguir distribuição **aproximadamente normal** (para fins de inferência)

---

### 6️⃣ Outliers e Pontos de Alavancagem

| Tipo | Definição | Impacto |
|---|---|---|
| **Outlier** | Observação que foge do padrão em Y | Distorce o ajuste e os coeficientes |
| **Ponto de alavancagem** | Valor extremo nas variáveis X | "Puxa" a reta de regressão em sua direção |

Medidas para detecção:
- **Alavancagem (hᵢᵢ)** — contribuição de cada ponto no ajuste
- **Distância de Cook** — impacto da remoção de cada observação
- **Resíduos studentizados** — detecção de valores anômalos em Y

---

### 7️⃣ Multicolinearidade e VIF

**Multicolinearidade** ocorre quando duas ou mais variáveis explicativas são altamente correlacionadas. Consequências:
- Coeficientes instáveis (sensíveis a pequenas mudanças nos dados)
- Sinais inesperados nos coeficientes
- Dificuldade em interpretar efeitos individuais
- Aumento da variância dos estimadores

O **Fator de Inflação da Variância (VIF)** mede o grau de multicolinearidade:

VIFⱼ = 1 / (1 − Rⱼ²)

| VIF | Interpretação |
|---|---|
| **< 5** | Multicolinearidade baixa — situação aceitável |
| **5 a 10** | Atenção necessária |
| **> 10** | Forte multicolinearidade — reavaliar variáveis |

> ⚠️ No exemplo do servidor, X₂ = 0,2X₁ — relação perfeita entre os preditores, causando VIF extremamente elevado. O modelo prevê bem, mas é **interpretativamentef rágil**.

---

## 🐍 Implementação em Python

### 8️⃣ Métricas de Desempenho

As três métricas principais para avaliar modelos de regressão:

| Métrica | Fórmula | Interpretação |
|---|---|---|
| **MSE** | Média dos erros ao quadrado | Penaliza mais erros grandes |
| **MAE** | Média das diferenças absolutas | Mais robusto a outliers |
| **R²** | Proporção da variância explicada | Quanto mais próximo de 1, melhor |

```python
from sklearn.linear_model import LinearRegression
from sklearn.metrics import mean_squared_error, mean_absolute_error, r2_score

X_pred = dados[["n_usuarios", "dados_MB"]].values
Y_resp = dados["tempo_ms"].values

modelo = LinearRegression()
modelo.fit(X_pred, Y_resp)
Y_pred = modelo.predict(X_pred)

print("MSE:", mean_squared_error(Y_resp, Y_pred))
print("MAE:", mean_absolute_error(Y_resp, Y_pred))
print("R²:",  r2_score(Y_resp, Y_pred))
```

---

### 9️⃣ Análise Gráfica

Gráficos essenciais para validação visual do modelo:

- **Dispersão (Y real × Y previsto)** — avalia a qualidade geral do ajuste
- **Resíduos × valores previstos** — detecta padrões, heterocedasticidade e erros estruturais
- **Histograma dos resíduos** — verifica normalidade
- **Learning curves** — identifica overfitting ou underfitting

---

## ⚠️ Cuidados Importantes

- **Ceteris paribus**: a interpretação de βᵢ só é válida assumindo todas as outras variáveis constantes
- **Multicolinearidade** compromete a interpretação dos coeficientes mesmo quando o modelo prevê bem
- **Calcular a inversa de (XᵀX) diretamente** pode gerar instabilidade numérica — prefira `lstsq`
- **R² alto não garante modelo correto** — analisar sempre os resíduos e o diagnóstico completo
- **Outliers e pontos de alavancagem** podem distorcer coeficientes e conclusões

---

## 🗂️ Resumo Geral

| Conceito | Definição |
|---|---|
| **Variável resposta (Y)** | Grandeza que se quer prever ou explicar |
| **Variável explicativa (X)** | Fator que influencia Y no modelo |
| **Intercepto (β₀)** | Valor base de Y quando todas as Xᵢ valem zero |
| **Coeficiente (βᵢ)** | Variação esperada em Y por unidade de Xᵢ, com demais fixas |
| **Resíduo (ε)** | Diferença entre valor observado e valor previsto |
| **OLS** | Método dos Mínimos Quadrados — minimiza a soma dos resíduos ao quadrado |
| **Teste t** | Testa a significância individual de cada coeficiente |
| **Teste F** | Testa a significância do modelo como um todo |
| **R²** | Proporção da variância de Y explicada pelo modelo |
| **VIF** | Fator de Inflação da Variância — mede multicolinearidade |
| **Multicolinearidade** | Alta correlação entre variáveis explicativas |
| **Outlier** | Observação com valor Y muito distante do padrão |
| **Ponto de alavancagem** | Observação com valor X extremo que influencia a regressão |

---

## 📎 Referências

- BUSSAB, W. O.; MORETTIN, P. A. **Estatística Básica**. 8. ed. Saraiva, 2015.
- GONZALEZ, M. **Estatística Aplicada à Engenharia e à Computação**. 3. ed. Pearson, 2018.
- GUIMARÃES, P. R. B. **Análise de Regressão**. UFPR, 2011.
- IZBICKI, R.; MENDONÇA, T. **Machine Learning sob a ótica estatística**. UFSCar/Insper, 2017.
- MAIA, A. G. **Econometria: conceitos e aplicações**. Editora Unicamp, 2017.
- MONTGOMERY, D. C.; RUNGER, G. C. **Estatística Aplicada e Probabilidade para Engenheiros**. 6. ed. LTC, 2016.

---

*Resumo feito para fins de estudo — FIAP*
