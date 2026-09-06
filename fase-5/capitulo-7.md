# A Otimização de Modelos que Eleva a Precisão das Ações da IA

**Matéria:** Inteligência Artificial
**Assunto principal:** Otimização de modelos lineares em Machine Learning — como ajustar parâmetros para minimizar erro e melhorar a capacidade de generalização do modelo.

---

## Capítulo 7 - A Otimização de Modelos que Eleva a Precisão das Ações da IA

### Visão geral
Otimizar um modelo não é só "diminuir o erro no treino". É um conjunto de decisões que envolve: escolher a função de custo certa, escolher como minimizar esse custo (analítico x iterativo), controlar a complexidade do modelo (regularização) e escolher quais variáveis realmente importam. O capítulo conecta tudo isso com um estudo de caso real (estimativa de custos de plano de saúde).

---

## 1. Otimização de Modelos

Treinar um modelo supervisionado = resolver um problema de otimização: encontrar os parâmetros que minimizam o erro entre previsão e valor real. Essa medida de erro é a **função de custo** (ou função objetivo/perda).

### 1.1.1 Erro Quadrático Médio (MSE)
- Fórmula: `MSE = (1/n) * Σ(yᵢ - ŷᵢ)²`
- Calcula a média dos erros ao quadrado → penaliza mais os erros grandes.
- Base estatística: minimizar o MSE = método dos mínimos quadrados = maximizar a verossimilhança (assumindo erros normais, média zero).
- **RMSE** (`√MSE`) é o MSE na mesma unidade da variável resposta — mais fácil de interpretar (ex.: RMSE = 5 → previsão erra em média 5 unidades).
- Serve para comparar modelos, detectar *overfitting* (erro baixo no treino e alto no teste) e é sensível a outliers (por isso às vezes se usa o MAE como complemento).

```python
from sklearn.metrics import mean_squared_error
mse = mean_squared_error(y_true, y_pred)

# ou manualmente com NumPy:
import numpy as np
mse = np.mean((y_true - y_pred)**2)
```

### 1.1.2 Gradiente Descendente
- Algoritmo iterativo para minimizar a função de custo (alternativa à solução analítica).
- Baseado em derivada: o **gradiente** aponta a direção de maior crescimento do erro. Atualiza-se o parâmetro na direção **oposta** ao gradiente.
- Fórmula de atualização: `θ(t+1) = θ(t) - η ∇J(θ(t))`, onde `η` é a **taxa de aprendizado** (tamanho do passo).
- Variações (não são comandos diferentes, e sim estratégias de uso dos dados):
  - **Batch Gradient Descent** → usa todo o conjunto de treino a cada atualização (mais estável, mais caro computacionalmente).
  - **Stochastic Gradient Descent (SGD)** → usa uma observação por vez (mais rápido, mais oscilante).
  - **Mini-batch Gradient Descent** → usa pequenos subconjuntos (equilíbrio entre os dois).
- Taxa de aprendizado muito alta → oscila/diverge. Muito baixa → convergência lenta.

```python
from sklearn.linear_model import SGDRegressor

modelo = SGDRegressor(
    loss="squared_error",
    learning_rate="constant",
    eta0=0.01,
    max_iter=1000,
    random_state=42
)
modelo.fit(X_train, y_train)
```

---

## 2. Regularização

Um modelo pode ter erro baixo no treino e ainda assim não generalizar bem (**overfitting**). No outro extremo, um modelo simples demais **não** captura os padrões (**underfitting**). Isso é o clássico *trade-off* viés x variância.

**Regularização** = adicionar um termo de penalização à função de custo para controlar a magnitude dos coeficientes e reduzir a complexidade do modelo.

| Técnica | Penalização | Efeito | Uso típico |
|---|---|---|---|
| **Ridge (L2)** | soma dos coeficientes ao quadrado | encolhe os coeficientes, mas não zera | dados com multicolinearidade |
| **Lasso (L1)** | soma dos valores absolutos | pode zerar coeficientes → seleciona variáveis | alta dimensionalidade |
| **Elastic Net** | combinação L1 + L2 | equilibra estabilidade (Ridge) e esparsidade (Lasso) | muitas variáveis correlacionadas |

```python
from sklearn.linear_model import Ridge, Lasso, ElasticNet

modelo_ridge = Ridge(alpha=1.0)
modelo_lasso = Lasso(alpha=0.1)
modelo_en = ElasticNet(alpha=0.1, l1_ratio=0.5)  # l1_ratio controla proporção L1/L2
```

`alpha` controla a intensidade da penalização (equivalente ao λ da teoria). Esse valor costuma ser escolhido por **validação cruzada**, não arbitrariamente.

---

## 3. Seleção de Variáveis

Diferente da regularização (que controla continuamente a magnitude dos coeficientes), a seleção de variáveis faz escolhas **discretas**: incluir ou excluir cada atributo.

- **Forward selection** → começa vazio e vai adicionando a variável que mais melhora o modelo.
- **Backward elimination** → começa com tudo e vai removendo a menos relevante.
- **Stepwise** → combina os dois (inclui e exclui ao longo do processo).
- **RFE (Recursive Feature Elimination)** → remove progressivamente as variáveis com menor coeficiente, de forma iterativa.

```python
from sklearn.feature_selection import RFE
from sklearn.linear_model import LinearRegression

modelo = LinearRegression()
rfe = RFE(estimator=modelo, n_features_to_select=5)
rfe.fit(X_train, y_train)
X_selecionado = rfe.transform(X_train)
```

Importante: o **Lasso** já faz seleção de variáveis "de graça" (implicitamente), porque zera coeficientes de variáveis pouco relevantes:

```python
coeficientes = modelo_lasso.coef_  # valores == 0 → variável excluída
```

⚠️ Interpretar coeficientes só é confiável se as variáveis estiverem **padronizadas** e exige cautela quando há correlação entre elas.

---

## 4. Implementação Computacional

Passo a passo prático para aplicar tudo isso com `scikit-learn`:

1. **Padronizar os dados** (essencial para regularização e gradiente descendente funcionarem corretamente — evita que a escala de uma variável distorça a penalização):
```python
from sklearn.preprocessing import StandardScaler
scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.transform(X_test)
```

2. **Ajustar os modelos regularizados** já com os dados padronizados (Ridge, Lasso, Elastic Net — como mostrado na seção 2).

3. **Escolher o hiperparâmetro `alpha`/λ por validação cruzada** (não no "chute").

4. **Visualizar o efeito da penalização**: gráficos da trajetória dos coeficientes conforme λ varia mostram esparsidade (Lasso) ou encolhimento contínuo (Ridge).

5. **Comparar modelo regularizado x não regularizado** olhando RMSE em treino e teste — a redução do gap entre os dois é o sinal de que o overfitting diminuiu.

---

## 5. Estudo de Caso: Estimativa de Custos em Saúde

Exemplo prático que junta tudo: prever o custo anual de um beneficiário de plano de saúde a partir de dados como idade, IMC, dependentes, se fuma, sexo e região (dataset **Medical Cost Personal Dataset**, do Kaggle).

**Pipeline aplicado:**
1. Separar `X` (variáveis explicativas) e `y` (`charges`, o custo) → `train_test_split`.
2. Pré-processar: `StandardScaler` nas numéricas (idade, IMC, dependentes) + `OneHotEncoder` nas categóricas (sexo, fumante, região), via `ColumnTransformer`.
3. Ajustar modelo baseline: **Regressão Linear simples** (sem regularização) → serve de referência (RMSE treino vs. teste).
4. Ajustar com **SGDRegressor** (gradiente descendente) → mostra o treinamento iterativo.
5. Ajustar com **Ridge (L2)** → compara RMSE treino/teste para ver se a regularização reduziu o overfitting (erros mais próximos entre treino e teste = modelo mais estável).

Conclusão do caso: nenhuma técnica isolada resolve tudo — é a combinação (função de custo certa + método de otimização + regularização bem calibrada + validação em dados de teste) que gera um modelo robusto.

---

## 6. Conclusão do capítulo

Otimizar um modelo linear vai muito além de "achar o menor erro no treino". Envolve equilibrar três coisas:
- **Qualidade de ajuste** (o modelo aprende os padrões dos dados?)
- **Controle de complexidade** (regularização evita overfitting)
- **Capacidade de generalização** (desempenho em dados novos/teste)

Otimização = decisões metodológicas em cada etapa, não aplicação mecânica de algoritmo.

---

## Glossário rápido

| Termo | Definição |
|---|---|
| MSE | Média dos quadrados dos erros de previsão |
| RMSE | Raiz do MSE, na mesma unidade da variável resposta |
| Gradiente | Vetor de derivadas parciais da função de custo |
| Gradiente descendente | Algoritmo iterativo que ajusta parâmetros na direção oposta ao gradiente |
| SGD | Gradiente descendente com atualização por observação/pequeno lote |
| Hiperparâmetro | Parâmetro definido antes do treino (ex.: alpha, taxa de aprendizado) |
| Ridge (L2) | Regularização que encolhe coeficientes (não zera) |
| Lasso (L1) | Regularização que pode zerar coeficientes (seleção de variáveis) |
| Elastic Net | Combinação de L1 e L2 |
| Overfitting | Ajuste excessivo ao treino, ruim em dados novos |
| Underfitting | Modelo simples demais, não captura os padrões |
| Padronização | Reescalar variáveis para média 0 e desvio padrão 1 |

---

## Minhas anotações
_(vou preenchendo aqui conforme for estudando)_

-

---

**Referências do capítulo:** Bishop (2006), Bussab & Morettin (2010), Hastie, Tibshirani & Friedman (2009), James, Witten, Hastie & Tibshirani (2013), Simeone (2018).
