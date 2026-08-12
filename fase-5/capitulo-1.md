# 🧠 NCAS — Núcleo Cognitivo da Aurora Siger

Protótipo de sistema de apoio computacional desenvolvido para a **Fase 5** do curso, no contexto da missão **Aurora Siger** — uma colônia marciana que precisa transformar dados dispersos em decisões rápidas e confiáveis.

O NCAS **não substitui a equipe humana**: ele organiza registros, aplica regras lógicas e simula interações com um assistente de IA para apoiar a tomada de decisão da colônia.

---

## 📖 Sobre o projeto

Com o crescimento da base, a quantidade de dados gerados por módulos, sensores e tripulação passou a ser grande demais para ser tratada manualmente. O NCAS foi criado para:

- Armazenar registros importantes da colônia
- Recuperar informações salvas em arquivos
- Organizar dados em formato JSON
- Aplicar validações lógicas em solicitações operacionais
- Simplificar regras booleanas utilizadas no sistema
- Estruturar prompts para simular interações com IA generativa
- Apoiar a geração de respostas organizadas e padronizadas
- Considerar aspectos éticos, sociais e de diversidade no uso da tecnologia

---

## ⚙️ Funcionalidades

- 📁 **Manipulação de arquivos** — leitura, escrita e adição de registros em `.txt`
- 🗂️ **Persistência em JSON** — carregamento e gravação de dados estruturados
- 🔢 **Lógica booleana** — regra de decisão implementada e simplificada (Teoremas de Simplificação / De Morgan)
- 🤖 **Simulação de IA generativa** — prompts *zero-shot*, *few-shot* e saída estruturada
- 🖥️ **Menu interativo no terminal** para cadastrar, consultar e analisar registros

---

## 🗃️ Estrutura do repositório

```
📦 ncas-aurora-siger
├── codigo_fonte.py         # Código principal do sistema (Python)
├── dados_colonia.json      # Dados estruturados utilizados pelo sistema
├── registros_colonia.txt   # Registros/logs salvos pelo sistema
├── regras_logicas.pdf      # Expressão booleana, simplificação e explicação
├── prompts_utilizados.pdf  # Prompts criados e explicação de cada um
├── link_video.txt          # Link do vídeo de apresentação (YouTube, não listado)
└── README.md
```

---

## ▶️ Como executar

Requisitos: **Python 3.x** (sem bibliotecas externas).

```bash
python codigo_fonte.py
```

Ao iniciar, o menu do terminal permite:

1. Cadastrar registros da colônia
2. Consultar registros salvos
3. Carregar dados em JSON
4. Executar uma validação lógica
5. Exibir prompts estruturados
6. Simular uma resposta do assistente inteligente

---

## 🔢 Regra lógica implementada

**Regra original:**
`ALERTA = (FALHA AND CRITICO) OR (FALHA AND NOT CRITICO)`

**Regra simplificada (Teorema de Simplificação):**
`ALERTA = FALHA`

> A simplificação mantém o mesmo resultado lógico, pois o alerta depende apenas da existência de falha — a condição de criticidade se torna redundante quando combinada com sua negação.

---

## 🤖 Exemplos de prompts

- **Zero-shot:** instrução direta, sem exemplos prévios, para resumir um alerta operacional.
- **Few-shot:** exemplos de entrada/saída fornecidos para orientar a classificação de solicitações da tripulação.
- **Saída estruturada:** resposta formatada em JSON para padronizar o retorno ao centro de controle.

Detalhes completos em `prompts_utilizados.pdf`.

---

## ⚖️ Ética, diversidade e responsabilidade

O projeto inclui uma reflexão sobre riscos de respostas enviesadas, a importância da diversidade no desenvolvimento de sistemas, cuidado com linguagem discriminatória, impactos sociais de decisões automatizadas e a responsabilidade humana no uso de IA generativa — aspectos que orientaram as escolhas de linguagem e comportamento do assistente simulado no NCAS.

---

## 🎥 Vídeo de apresentação

Link disponível em [`link_video.txt`](./link_video.txt).
