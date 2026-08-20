# Capítulo 4 — Os Comandos Inteligentes que Ativam o Núcleo Cognitivo da IA

> Material de estudo (FIAP) sobre *Large Language Models* (LLMs): fundamentos históricos, arquitetura Transformer, treinamento, capacidades emergentes, limitações e uso prático via API.

## 📖 Sobre

Este capítulo constrói a base conceitual dos LLMs — a "camada cognitiva" que sustenta sistemas como o **NCAS (Núcleo Cognitivo da Aurora Siger)**. Cobre desde a evolução histórica do PLN até a implementação prática de prompts (zero-shot, few-shot) com modelos open source e proprietários.

## 📑 Conteúdo

### 1. Introdução — Contexto histórico
Evolução do PLN: sistemas baseados em regras (1950–1980) → modelos estatísticos e n-gramas (1990s) → HMMs → word embeddings (Word2Vec, GloVe) → RNNs/LSTMs/GRUs → **Transformer** ("Attention Is All You Need", 2017) → ELMo, GPT-1, BERT → LLMs e *foundation models*.

### 2. O que são Large Language Models
- Definição formal: modelo estatístico que aprende P(tokenₜ | token₁...tokenₜ₋₁)
- O que torna um modelo "large": nº de parâmetros, volume de dados, **capacidades emergentes**
- Tokens e sub-palavras (Byte Pair Encoding)
- LLMs vs. chatbots tradicionais (baseados em regras/fluxos)

### 3. Arquitetura Transformer: fundamentos essenciais
- **Embeddings de entrada** — vetores densos semânticos
- **Positional Encoding** — captura a ordem das palavras
- **Self-Attention** — mecanismo Query (Q), Key (K), Value (V)
- **Multi-Head Attention** — múltiplas "cabeças" de atenção em paralelo
- **Camadas Feed-Forward**, **Residual Connections** e **Layer Normalization**
- **Encoder vs. Decoder**; Transformers autorregressivos (GPT-like) vs. bidirecionais (BERT-like)

### 4. Pré-treinamento de LLMs
- Aprendizado auto-supervisionado — *next token prediction* (objetivo de linguagem causal)
- **Máscara causal** — impede o modelo de "olhar para o futuro"
- Função de perda de **entropia cruzada** (cross-entropy)
- Escala de dados, custo computacional/energético e **scaling laws**

### 5. Fine-tuning, Instruction Tuning e Alinhamento
- **Fine-tuning supervisionado** — adapta o modelo pré-treinado a tarefas específicas
- **Instruction tuning** — treina o modelo a seguir instruções gerais (pares instrução–resposta)
- **RLHF** (Reinforcement Learning from Human Feedback): política → avaliação humana → *reward model* → otimização via PPO
- Modelo base vs. modelo instruído/alinhado

### 6. Uso avançado de LLMs — capacidades emergentes
- **In-Context Learning** (zero-shot e few-shot learning)
- **Chain-of-Thought Prompting** — raciocínio passo a passo
- Geração de código, tradução multilíngue, transferência entre domínios

### 7. Limitações, riscos e desafios
- **Alucinações** — geração de conteúdo plausível mas incorreto
- Falta de *grounding* no mundo real
- Viés e discriminação herdados dos dados de treinamento
- Privacidade e dados sensíveis
- Dependência excessiva (*automation bias*)
- Limitações de contexto (janela finita) e falhas de raciocínio lógico
- Conhecimento desatualizado (recorte temporal)

### 8. Prompt Engineering, RAG e Agentes
- Prompt como "mecanismo de controle comportamental", não apenas texto de entrada
- **RAG (Retrieval Augmented Generation)** — injeta conhecimento externo atualizado no contexto
- **Arquiteturas de agentes** — o LLM como "cérebro"/orquestrador cognitivo que planeja e usa ferramentas externas (APIs, bancos de dados, buscadores)
- Analogia: LLM = motor; Prompt Engineering = como dirigir; RAG = sensores/mapas; Agentes = veículo autônomo completo

### 9. Laboratório: LLMs na prática
- **9.1 LLM Opensource (`gpt-oss-20b` via Ollama)** — instalação, servidor local, classificação de sentimentos com zero-shot e few-shot prompting
- **9.2 LLM proprietário (`gpt-4o-mini` via API OpenAI)** — três tipos de prompt: `system`, `user`, `assistant`
- **9.3 (Re)construindo o ChatGPT** — loop de conversa mantendo histórico de mensagens (contexto)

## 💡 Principais pontos de atenção

- LLMs **não entendem** linguagem no sentido humano — são preditores estatísticos de tokens.
- O *self-attention* resolve dependências de longo alcance e permite paralelismo (diferente de RNNs/LSTMs).
- **Pré-treinamento ≠ alinhamento**: o pré-treinamento dá conhecimento linguístico bruto; RLHF/instruction tuning ensinam o modelo a ser útil e seguro.
- Prompts com `system`/`user`/`assistant` simulam histórico de conversa — é assim que o "contexto" é mantido.
- RAG existe justamente para compensar as limitações estruturais dos LLMs (conhecimento desatualizado, alucinação, ausência de grounding).

## 🔗 Aplicação no projeto

Os conceitos deste capítulo (prompt engineering, zero-shot/few-shot, simulação de LLM) sustentam diretamente a etapa de **prompts_utilizados.pdf** e a simulação de interpretação de comandos do NCAS — Núcleo Cognitivo da Aurora Siger.

## 📚 Referências

- GOODFELLOW, Ian; BENGIO, Yoshua; COURVILLE, Aaron. *Deep learning*. Cambridge: MIT Press, 2016.
- JAMES, Gareth; WITTEN, Daniela; HASTIE, Trevor; TIBSHIRANI, Robert. *An introduction to statistical learning: with applications in R*. New York: Springer, 2014.
- OPENAI. *Documentação da plataforma OpenAI*. Disponível em: <https://platform.openai.com/login>. Acesso em: 18 maio 2026.
- RUSSELL, Stuart; NORVIG, Peter. *Artificial intelligence: a modern approach*. 4. ed. Harlow: Pearson, 2021.
- VASWANI, Ashish et al. *Attention is all you need*. In: INTERNATIONAL CONFERENCE ON NEURAL INFORMATION PROCESSING SYSTEMS, 31., 2017. Red Hook, NY: Curran Associates Inc., 2017. p. 6000–6010.
