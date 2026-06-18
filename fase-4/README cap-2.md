# 📘 Capítulo 2 — A Manipulação de Dados que Sustenta as Análises Energéticas da Colônia

> Resumo do capítulo para a disciplina de **Lógica de Programação / Python** — FIAP  
> Autor do material: Prof. Edson de Oliveira  

---
## 👤 Informações do Aluno

- **Nome:** Isabelle Caroline de Camargo Francisco  
- **RM:** 572096  
- **Curso:** Ciência da Computação - EAD  
- **Disciplina:** Lógica de Programação / Python  

---
## 📌 Assuntos Abordados

- **Python** — Linguagem principal utilizada no capítulo
- **Estruturas de Dados** — Vetores, Matrizes, Listas, Tuplas, Dicionários e Tabelas
- **Subalgoritmos / Funções** — Programação modular com `def`
- **Lógica de Programação** — Laços, condicionais, manipulação de memória

---

## 🧠 Visão Geral

O capítulo ensina como armazenar **múltiplas informações em uma única variável** utilizando as estruturas de dados do Python. O problema central é: uma variável simples só guarda **um valor por vez** — quando reatribuída, o valor anterior é perdido. Para resolver isso, usamos **Estruturas de Dados**.

---

## 📂 Estruturas de Dados Abordadas

### 1️⃣ Variáveis Indexadas — Vetores e Matrizes

#### 🔹 Vetor (Array Unidimensional)
- Estrutura com **uma linha e N colunas**
- Cada posição tem um **índice** (começa em `0`) e uma **célula** (conteúdo)
- Conteúdo **homogêneo** (todos os elementos do mesmo tipo)
- Tamanho **fixo**, definido pelo programador
- Em Python, simulado com **listas**

```python
# Inicializando um vetor com valores
vetor = [45, 89, 32, 12, 33]

# Acessando elemento pelo índice
print(vetor[2])        # Saída: 32

# Modificando um elemento
vetor[0] = -55
print(vetor)           # Saída: [-55, 89, 32, 12, 33]
```

**Características do Vetor:**
| Característica | Descrição |
|---|---|
| Tamanho | Fixo, definido na criação |
| Conteúdo | Homogêneo (mesmo tipo) |
| Índice | Começa no zero |
| Acesso | Sempre via colchetes `[]` |

---

#### 🔹 Matriz (Array Bidimensional)
- Estrutura **L linhas × C colunas** (ambos maiores que 1)
- Acesso via **dois pares de colchetes**: `matriz[linha][coluna]`
- Simulação de **tabela ou planilha** na memória

```python
# Criando uma matriz 3x2
matriz = [
    [89, 32],
    [-8, 93],
    [12, 54],
]

# Exibindo em formato de grade
for linha in range(3):
    for coluna in range(2):
        print(f"{matriz[linha][coluna]}\t", end="")
    print()
```

---

### 2️⃣ Listas

#### 🔹 Diferenças entre Lista e Vetor
| Característica | Vetor | Lista |
|---|---|---|
| Tipo dos dados | Homogêneo | Heterogêneo |
| Tamanho | Fixo | Dinâmico |
| Manipulação | Por índice | Por métodos específicos |

#### 🔹 Principais Métodos

```python
lista = []                      # lista vazia

lista.append("elemento")        # insere no final
lista.insert(2, "Engenharia")   # insere na posição 2
lista.pop(1)                    # remove pelo índice
lista.clear()                   # apaga todos os elementos
del(lista)                      # remove a lista da memória

# Concatenar listas
l3 = l1 + l2                    # cria nova lista
l1.extend(l2)                   # adiciona l2 dentro de l1
```

#### 🔹 Percorrendo uma Lista

```python
# Forma clássica (por índice)
for i in range(0, len(lista), 1):
    print(lista[i])

# Forma pythônica (recomendada)
for elem in lista:
    print(elem)
```

---

### 3️⃣ Tuplas

- Estrutura com células **imutáveis** — somente o programador define o conteúdo
- Delimitada por **parênteses** `()`
- Não pode ser preenchida dinamicamente pelo usuário

```python
tupla = (12, 56, "@", True)
print(tupla)   # Saída: (12, 56, '@', True)
```

#### 🔹 Exemplo Prático — Cálculo do Imposto de Renda

```python
# Tuplas com dados da tabela do IR 2022
tuplaBaseCalculo = (0, 1903.98, 2826.65, 3751.05, 4664.68)
tuplaAliquota    = (0, 7.50,    15.00,   22.50,   27.50)
tuplaDeducao     = (0, 142.80,  354.80,  636.13,  869.36)

def retornaFaixa(tbc: tuple, sal: float) -> int:
    for faixa in range(len(tbc) - 1, -1, -1):
        if sal > tbc[faixa]:
            return faixa
    return 0

def calculoIr(f: int, sal: float, ta: tuple, td: tuple) -> float:
    return sal * (ta[f] / 100) - td[f]
```

**Resultados para diferentes salários:**
| Salário | IR Calculado |
|---|---|
| R$ 1.000,00 | R$ 0,00 (isento) |
| R$ 2.500,00 | R$ 44,70 |
| R$ 3.500,00 | R$ 170,20 |
| R$ 4.500,00 | R$ 376,37 |
| R$ 10.000,00 | R$ 1.880,64 |

---

### 4️⃣ Dicionários

- Estrutura que usa **keys (chaves)** em vez de índices numéricos
- Semelhante a um registro de banco de dados na memória
- Delimitado por **chaves** `{}`

```python
# Criando dicionário com valores
contato = {
    'cpf': 2345678900,
    'nome': 'Edson de Oliveira',
    'celular': '1194837363',
    'email': 'prof@fiap.com.br',
}

# Acessando valores por key
print(contato['nome'])     # Edson de Oliveira

# Preenchendo via input
contato['nome'] = input("Nome: ")
```

**Componentes do Dicionário:**
| Termo | Significado |
|---|---|
| **Key** | Chave/campo (ex: `'nome'`) |
| **Value** | Conteúdo da key (ex: `'Edson'`) |
| **Item** | Par key + value |

---

### 5️⃣ Tabelas — Lista de Dicionários

- Combinação de **lista + dicionário** para simular uma tabela na memória
- A **lista** armazena vários registros (linhas)
- O **dicionário** representa cada registro (colunas/campos)

```python
tabela = list()    # representa a tabela
contato = dict()   # representa um registro

def preenche_registro(t: list, reg: dict) -> None:
    reg['cpf']     = input("CPF: ")
    reg['nome']    = input("Nome: ")
    reg['celular'] = input("Celular: ")
    reg['email']   = input("E-mail: ")
    t.append(reg.copy())   # .copy() é essencial aqui!

def exibe_registro(t: list, i: int) -> None:
    print(f"CPF: {t[i]['cpf']}")
    print(f"Nome: {t[i]['nome']}")
```

> ⚠️ **Atenção:** usar `.copy()` ao dar `append` é essencial para que cada registro seja independente na memória.

---

## 🏗️ Padrão de Código Utilizado no Capítulo

O capítulo usa **subalgoritmos (funções e procedimentos)** para organizar o código:

```python
# Estrutura padrão dos programas do capítulo

# 1. Definição dos subalgoritmos
def minha_funcao(parametro: tipo) -> tipo_retorno:
    # lógica aqui
    return resultado

# 2. Inicialização das estruturas de dados
minha_lista = list()

# 3. Programa principal
while True:
    opcao = int(input("Opção: "))
    match opcao:
        case 0: break
        case 1: minha_funcao(minha_lista)
```

---

## ⚠️ Cuidados Importantes

- **Python não tem vetor nativo** — vetores são simulados com listas
- **Tuplas são imutáveis** — não use onde o usuário precisar alterar dados
- **`del()` ≠ `clear()`** — `del` remove a variável da memória; `clear` só esvazia
- **`.copy()`** — necessário ao inserir dicionários em listas para evitar que todos os registros apontem para o mesmo endereço de memória
- **Listas heterogêneas** — cuidado ao operar matematicamente, pois tipos misturados geram `TypeError`

---

## 🗂️ Resumo Geral das Estruturas

| Estrutura | Delimitador | Tipo | Tamanho | Imutável? | Indexação |
|---|---|---|---|---|---|
| Vetor | `[]` | Homogêneo | Fixo | Não | Por índice numérico |
| Matriz | `[[]]` | Homogêneo | Fixo | Não | Por linha e coluna |
| Lista | `[]` | Heterogêneo | Dinâmico | Não | Por índice numérico |
| Tupla | `()` | Heterogêneo | Fixo | **Sim** | Por índice numérico |
| Dicionário | `{}` | Heterogêneo | Dinâmico | Não | Por **key** (string) |

---

## 📎 Referência

- LEOA BLOG. **Tabela do IRRF: Imposto de Renda 2022**. Disponível em: [leoa.com.br](https://www.leoa.com.br/blog/tabela-irrf-2022)

---

*Resumo feito para fins de estudo — FIAP*
