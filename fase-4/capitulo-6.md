# 📘 Capítulo 6 — A Regressão Logística que Classifica Cenários para Decisões Energéticas

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

- **Regressão Logística** — Conceito, motivação e diferença em relação à regressão linear
- **Fundamentos Matemáticos** — Probabilidade, chances (odds), log-odds e função logística (sigmoide)
- **Estimação** — Função de custo (entropia cruzada) e otimização via gradiente descendente
- **Implementação em Python** — scikit-learn, dados sintéticos, fronteira de decisão e interpretação de coeficientes
- **Avaliação do Modelo** — Acurácia, precisão, recall, F1-score, matriz de confusão, curva ROC/AUC e validação cruzada
- **Aplicações Práticas** — Detecção de acesso suspeito (cibersegurança), churn, fraude, manutenção preditiva

---

## 🧠 Visão Geral

A regressão logística é o modelo estatístico de referência para problemas de **classificação binária**, em que a variável resposta assume apenas dois valores possíveis (0 ou 1). Diferente da regressão linear — que produz valores contínuos e ilimitados —, a regressão logística converte uma combinação linear de variáveis explicativas em uma **probabilidade** no intervalo [0, 1], por meio da função logística (sigmoide). O capítulo percorre toda a base teórica (probabilidade, odds, log-odds, entropia cruzada, gradiente descendente) até a implementação prática em Python com `scikit-learn`, incluindo um estudo de caso completo de detecção de acessos suspeitos.

---

## 📂 Conceitos Fundamentais

### 1️⃣ Por que a Regressão Linear não Resolve Classificação

A regressão linear não é adequada para problemas binários porque:
- Suas previsões **não são limitadas** — podem ser negativas ou maiores que 1
- Sua função de perda **quadrática** não reflete o custo assimétrico dos erros de classificação (errar um positivo ≠ errar um negativo)

A regressão logística resolve isso modelando diretamente `Pr(Y = 1 | X = x)`, sempre entre 0 e 1.

---

### 2️⃣ Probabilidade, Chances (Odds) e Log-odds

| Conceito | Fórmula | Intervalo |
|---|---|---|
| **Probabilidade p(x)** | Pr(Y = 1 \| X = x) | [0, 1] |
| **Chances (odds)** | p / (1 − p) | (0, ∞) |
| **Log-odds (logito)** | ln[p(x) / (1 − p(x))] | (−∞, +∞) |

> O *logito* é o ponto central da regressão logística: ele pode ser modelado como uma combinação **linear** dos preditores, mesmo a probabilidade sendo limitada.

logit(p(x)) = b₀ + b₁x₁ + ... + bₖxₖ

---

### 3️⃣ Função Logística (Sigmoide)

Para recuperar a probabilidade a partir do log-odds, aplica-se a função sigmoide:

p(x) = 1 / (1 + exp[−(b₀ + b₁x₁ + ... + bₖxₖ)])

σ(z) = 1 / (1 + e⁻ᶻ)

> A sigmoide tem formato de "S" e garante que qualquer valor real seja convertido em uma probabilidade válida entre 0 e 1.

---

### 4️⃣ Função de Custo: Entropia Cruzada

Como a função de custo quadrática gera um problema não convexo, a regressão logística usa a **entropia cruzada** (log-verossimilhança negativa):

J(b) = −Σ [ yᵢ·ln(p(xᵢ)) + (1 − yᵢ)·ln(1 − p(xᵢ)) ]

Características principais:
- Penaliza com mais severidade erros cometidos **com alta confiança**
- É **convexa**, garantindo convergência de métodos baseados em gradiente
- Favorece modelos com probabilidades **bem calibradas**

---

### 5️⃣ Otimização via Gradiente Descendente

Os coeficientes são ajustados iterativamente na direção que mais reduz o custo:

bⱼ ← bⱼ − α (∂J / ∂bⱼ)

- **α (taxa de aprendizado)** controla o tamanho do passo
- Passos muito grandes → divergência; passos muito pequenos → ajuste lento
- É a base conceitual de métodos usados em redes neurais

---

### 6️⃣ Implementação em Python (scikit-learn)

```python
from sklearn.linear_model import LogisticRegression

model = LogisticRegression()
model.fit(X_train, y_train)
model.predict(X_test)          # classe estimada
model.predict_proba(X_test)    # probabilidade por classe
```

> Boa prática: observar **sempre as probabilidades primeiro**; o ponto de corte (geralmente 0,5) é aplicado depois, conforme o contexto de negócio.

#### 🔹 Exemplo com dados sintéticos (churn de clientes)

```python
import numpy as np
from sklearn.linear_model import LogisticRegression
from sklearn.model_selection import train_test_split

np.random.seed(42)
X = np.random.rand(200, 2)
y = (0.8 * X[:, 0] - 0.4 * X[:, 1] > 0.2).astype(int)

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3)

model = LogisticRegression()
model.fit(X_train, y_train)
print("Probabilidades:", model.predict_proba(X_test[:5]))
```

---

### 7️⃣ Fronteira de Decisão

Para duas variáveis explicativas, a fronteira de decisão é a linha onde o modelo estima **probabilidade = 0,5**:

b₀ + b₁x₁ + b₂x₂ = 0  →  decision_boundary = -(b0 + b1 * x_values) / b2

- Pontos de um lado → Pr(Y=1|X) > 0,5 (classe positiva)
- Pontos do outro lado → Pr(Y=1|X) < 0,5 (classe negativa)
- Com **3+ variáveis**, fixa-se as demais em valores constantes (ex: média) para projetar a fronteira em 2D

---

### 8️⃣ Interpretação dos Coeficientes

Cada coeficiente bⱼ representa o efeito sobre o **log-odds**. Aplicando exp(bⱼ), obtém-se o fator multiplicativo sobre as **chances (odds)**:

| Exemplo | Interpretação |
|---|---|
| exp(b₁) = 1,5 | Cada unidade a mais em x₁ aumenta as chances do evento em 50% (fator de risco) |
| exp(b₂) = 0,7 | Cada unidade a mais em x₂ reduz as chances do evento em 30% (fator protetivo) |

---

## 📊 Avaliação do Modelo

### 9️⃣ Métricas Fundamentais

| Métrica | O que mede |
|---|---|
| **Acurácia** | Proporção total de acertos |
| **Precisão** | Dos classificados como positivos, quantos realmente eram |
| **Recall (sensibilidade)** | Dos positivos reais, quantos foram identificados |
| **Especificidade** | Capacidade de identificar corretamente os negativos |
| **F1-score** | Equilíbrio entre precisão e recall (útil com classes desbalanceadas) |

```python
from sklearn.metrics import accuracy_score, precision_score, recall_score, f1_score, confusion_matrix

y_pred = model.predict(X_test)
print("Acurácia:", accuracy_score(y_test, y_pred))
print("Precisão:", precision_score(y_test, y_pred))
print("Recall:", recall_score(y_test, y_pred))
print("F1-score:", f1_score(y_test, y_pred))
print("Matriz de confusão:\n", confusion_matrix(y_test, y_pred))
```

### 🔟 Curva ROC, AUC e Validação Cruzada

- **Curva ROC**: relação entre taxa de verdadeiros positivos e falsos positivos em diferentes limiares
- **AUC**: resume a curva em um único valor — próximo de 1 é excelente; próximo de 0,5 é equivalente ao acaso
- **Validação cruzada**: treina/testa o modelo em várias partições dos dados para estimar desempenho de forma mais robusta, reduzindo a influência do acaso

---

## 🔍 Estudo de Caso: Detecção de Acesso Suspeito

Cenário de cibersegurança com três variáveis explicativas:

| Variável | Significado |
|---|---|
| **X₁** | Horário do acesso (próximo de 1 = horário atípico) |
| **X₂** | Distância geográfica do último acesso |
| **X₃** | Proporção recente de falhas de login |

**Y** = 1 (acesso suspeito) ou 0 (acesso normal)

### Resultados com 20 observações (amostra pequena, fins didáticos)
- Acurácia: 66,7% | Precisão: 0,5 | Recall: 1,0 | F1-score: 0,67
- Recall perfeito → nenhum acesso suspeito passou despercebido, mas com alguns falsos positivos

### Resultados com a base completa (643 dados)
- Acurácia: 98,4% | Precisão: 97,6% | Recall: 1,0 | F1-score: 98,8%
- Coeficientes (exp(bⱼ)): X₁ ≈ 54,6 | X₂ ≈ 85,9 | X₃ ≈ 588,9
- **X₃ (falhas recentes de login)** é a variável de maior impacto no risco previsto

> O aumento expressivo na qualidade das métricas entre a amostra pequena e a base completa ilustra como o **tamanho da amostra** impacta a robustez do modelo.

---

## ⚠️ Cuidados Importantes

- A regressão linear **não deve ser usada** para problemas de classificação binária
- A função de custo correta é a **entropia cruzada**, não o erro quadrático médio
- Avaliar sempre **probabilidades antes** de aplicar o ponto de corte de classificação
- **Recall alto não garante boa precisão** — analisar sempre a matriz de confusão como um todo
- Em bases pequenas, métricas podem ser instáveis e pouco generalizáveis — preferir o histórico completo sempre que possível
- A interpretação de exp(bⱼ) só é válida mantendo as demais variáveis constantes (*ceteris paribus*)

---

## 🗂️ Resumo Geral

| Conceito | Definição |
|---|---|
| **Classificação binária** | Problema em que Y assume apenas dois valores (0 ou 1) |
| **Probabilidade p(x)** | Pr(Y = 1 \| X = x) |
| **Odds (chances)** | p / (1 − p) |
| **Log-odds (logito)** | ln(odds) — combinação linear dos preditores |
| **Função logística (sigmoide)** | Converte log-odds em probabilidade válida [0,1] |
| **Entropia cruzada** | Função de custo da regressão logística |
| **Gradiente descendente** | Método iterativo para minimizar a função de custo |
| **Fronteira de decisão** | Conjunto de pontos onde p(x) = 0,5 |
| **exp(bⱼ)** | Fator multiplicativo sobre as chances (odds) por unidade de xⱼ |
| **Acurácia** | Proporção total de acertos |
| **Precisão** | Confiabilidade das previsões positivas |
| **Recall (sensibilidade)** | Capacidade de detectar os casos positivos reais |
| **F1-score** | Equilíbrio entre precisão e recall |
| **AUC/ROC** | Capacidade de discriminação do modelo entre classes |
| **Validação cruzada** | Avaliação robusta usando múltiplas partições dos dados |

---

## 📎 Referências

- BISHOP, C. M. **Pattern Recognition and Machine Learning**. New York: Springer, 2006.
- BLUM, A.; HOPCROFT, J.; KANNAN, R. **Foundations of Data Science**. 2020.
- FARIAS, A. M. L. de. **Probabilidade e Estatística**. 2. ed. São Paulo: Pearson, 2017.
- GOES, G.; YWATA, A. **Introdução à Regressão Logística**. 2019.
- HOMEM, P. L. **Machine Learning – Uma abordagem estatística e computacional**. São Paulo: 2020.
- IZBICKI, R.; SANTOS, T. M. dos. **Aprendizagem de Máquina – Uma abordagem estatística**. 1. ed. Rio de Janeiro: LTC, 2020.
- SIMEONE, O. **A Brief Introduction to Machine Learning for Engineers**. Foundations and Trends in Signal Processing, v. 12, n. 3–4, p. 200–431, 2018.
- THOMAS, M. P. D.; FAUSETT, A. A.; ONG, C. S. **Mathematics for Machine Learning**. Cambridge: Cambridge University Press, 2018.

---

*Resumo feito para fins de estudo — FIAP*
