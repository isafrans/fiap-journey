# Capítulo 3 — Os Dados Estruturados que Alimentam as Decisões da Inteligência Central

> Material de estudo (FIAP) sobre manipulação de arquivos texto e arquivos JSON em Python, aplicado ao contexto do projeto **NCAS — Núcleo Cognitivo da Aurora Siger**.

## 📖 Sobre

Este capítulo cobre a gravação, leitura e edição de dados em disco — a base para o armazenamento persistente usado no NCAS (`dados_colonia.json`, `registros_colonia.txt`). Aborda desde os conceitos de arquivo texto até a serialização de dados em JSON.

## 📑 Conteúdo

### 1. Manipulação de arquivos texto e JSON
- Por que armazenar dados (RAM vs. gravação em meio magnético)
- Quando vale a pena gravar um arquivo (nem toda aplicação precisa)

### 2. Manipulação de arquivos texto
- **Função `open()`** — sintaxe `open("<arquivo>", "<modo>")`
- **Modos de abertura:**

  | Caractere | Significado |
  |---|---|
  | `'w'` | Escrita — recria o arquivo, apaga conteúdo anterior |
  | `'r'` | Leitura |
  | `'x'` | Escrita exclusiva — erro se o arquivo já existir |
  | `'a'` | Append — insere no final sem apagar o conteúdo |
  | `'+'` | Leitura + escrita combinadas (`w+`, `r+`) |

- **`seek()`** — reposiciona o cursor no arquivo (essencial para reler após escrever)
- **Métodos de leitura:** `read()`, `readline()`, `readlines()`
- **Método de escrita em lote:** `writelines()`
- **Gerenciador de contexto `with`** — fecha o arquivo automaticamente, mesmo em caso de erro

### 3. Utilização de arquivos `.JSON`
- Comparativo XML vs. JSON
- Modelagem de dados como dicionário Python
- `import json`
- `json.dumps()` (com `indent` para formatação legível)
- Gravação e leitura de arquivos `.json` com `with open(...)`
- `json.loads()` para reconstruir o dicionário a partir do arquivo
- Exibição formatada dos dados (`for k, v in dicionario.items()`)

## 💡 Principais pontos de atenção

- `'w'` **sobrescreve** o arquivo inteiro; `'a'` só adiciona ao final.
- Após escrever com `'w+'`/`'r+'`, é preciso usar `arq.seek(0)` antes de ler — senão o cursor já está no final e `read()` retorna vazio.
- `print()` já quebra linha sozinho; usar `end=''` ou `.strip()` com `readline()` evita linhas duplas.
- Chaves e valores em JSON exigem aspas duplas (diferente de dicionários Python, que aceitam aspas simples).
- `with` elimina a necessidade de `close()` manual e evita corrupção de arquivo em caso de falha.

## 🔗 Aplicação no projeto

Os conceitos deste capítulo sustentam diretamente a camada de persistência do NCAS:
- `registros_colonia.txt` → leitura/escrita com `open()`, `readlines()`, `with`
- `dados_colonia.json` → serialização/desserialização com `json.dumps()` / `json.loads()`

## 📚 Referências

- PYTHON. *Built-in Functions*. 2022. Disponível em: <https://docs.python.org/3/library/functions.html#open>. Acesso em: 16 jan. 2024.
- *Uma introdução a ASCII e Unicode*. Treina Web, 2021. Disponível em: <https://www.treinaweb.com.br/blog/uma-introducao-a-ascii-e-unicode>. Acesso em: 16 jan. 2024.
