# As Estruturas de Memória que Garantem Velocidade às Decisões Computacionais

**Matéria:** Arquitetura de Computadores
**Assunto principal:** Como a informação é representada em bits, como as memórias armazenam e endereçam dados, os tipos de memória (semicondutora, magnética, óptica), a hierarquia de memória e os módulos de entrada/saída (barramentos).

---

## Capítulo 8 - As Estruturas de Memória que Garantem Velocidade às Decisões Computacionais

### Visão geral
O capítulo explica a base de tudo que um computador faz: como a informação vira bits, como os bits são organizados (byte, nibble, word), como esses dados são endereçados e guardados fisicamente (memórias semicondutoras, magnéticas e ópticas), e como isso se organiza em uma hierarquia de velocidade x custo x capacidade (registradores → cache → RAM → Optane → armazenamento secundário). Fecha com como o computador troca dados entre os componentes (barramentos e módulos de E/S).

---

## 1. Memórias e Barramentos — Introdução

Contextualiza a evolução: relês → transistores → computadores pessoais modernos, cada vez mais dependentes de processar e armazenar dados de forma eficiente. Base para entender a relação entre bits e a informação guardada.

---

## 2. Memórias

### Bit, Byte, Nibble e Word
- **Bit** (*binary digit*): menor unidade de informação. Dois estados: 0 ou 1 (lógico) / dois níveis de tensão (físico).
- Com **1 bit** só dá pra representar 2 símbolos. Para representar mais símbolos, combinam-se vários bits: `n` bits → `2ⁿ` combinações possíveis.
  - Exemplo: 2 bits → `2² = 4` combinações (00, 01, 10, 11).
- **Byte** = grupamento de **8 bits**. Virou padrão histórico desde os primeiros computadores de 8 bits.
- **Nibble** = grupamento de **4 bits** (suficiente para representar os 10 dígitos decimais).
- **Word** (palavra) = quantidade de bits/bytes usada para codificar uma informação; padrão criado pela Intel = **16 bits**. Hoje as máquinas manipulam palavras de até 64 bits.
  - Exemplo: código ASCII usa 1 byte por caractere → `a = 0110 0001`
  - Áudio `.wav` → palavra de 16 bits (2 bytes) por amostra de som.
  - Cores de imagem → podem chegar a 64 bits (4 bytes).
- A escolha do tamanho da palavra depende de: (1) quantos elementos preciso representar e (2) custo computacional de processar aquele tamanho.

### 2.1 Origem dos dados
Como a informação em bits chega ao computador:
- **Teclado**: cada tecla pressionada gera uma sequência de bits transmitida via protocolo (ex.: PS/2 usa sinais de *Clock* e *Data*; hoje USB/Bluetooth fazem o mesmo papel). O computador "lê" o bit sempre na descida do sinal de clock. No fim, um byte identifica a tecla (ex.: tecla "a" → `00011100`).
- **Placa de áudio**: um microfone gera um sinal analógico de tensão; o **Conversor Analógico/Digital (A/D)** compara esse sinal com valores de referência (ex.: 0V e 5V) e gera um byte correspondente à intensidade captada.
- **Câmera digital**: sensor (ex.: CMOS) capta a imagem, que é fragmentada em pixels; um conversor A/D associa a cor de cada pixel a um código binário (geralmente 8 bits).

### 2.2 Função da memória
Guardar informação — sempre na forma de bits. A tecnologia de fabricação define fisicamente como o bit (0 ou 1) é representado (carga elétrica, magnetismo, reflexão de luz, etc.).

### 2.3 Endereçamento
Para acessar (ler ou escrever) um dado, o sistema precisa apontar o **endereço** onde ele está — como um endereço de casa para entregar uma encomenda.
- O endereçamento usa uma variável binária de `n` bits → permite `2ⁿ` endereços distintos.
  - 1 bit → 2 endereços | 2 bits → 4 endereços | 4 bits → 16 endereços | 10 bits → 1.024 endereços...

**Pegadinha importante — o "kilo" da informática não é 1.000:**
Em informática, as potências de 2 são convencionalmente aproximadas às unidades do dia a dia porque `2¹⁰ = 1.024` é o valor mais próximo de 1.000.

| Unidade | Símbolo | Equivalência | Cálculo |
|---|---|---|---|
| Kilobyte | KB | 1.024 bytes | 2¹⁰ |
| Megabyte | MB | 1.024 KB | 2²⁰ = 1.048.576 |
| Gigabyte | GB | 1.024 MB | 2³⁰ = 1.073.741.824 |
| Terabyte | TB | 1.024 GB | 2⁴⁰ ≈ 1,1 trilhão |

### 2.4 Classificação das memórias

#### 2.4.1 Quanto à tecnologia/mídia de fabricação
| Tipo | Como representa o bit | Onde é usada |
|---|---|---|
| **Semicondutora** | presença/ausência de carga, corrente ou tensão elétrica | RAM, cache, registradores, pen drives, SSD (e como *buffer* em HDs/CDs) |
| **Magnética** | presença/ausência de magnetismo (dipolos N/S) | HDs, fitas DAT/K7, disquetes (FDD) |
| **Óptica** | padrão de reflexão da luz (laser) | CDs, DVDs, Blu-Ray |

> As memórias semicondutoras dominam o mercado hoje e tendem a dominar cada vez mais, por permitirem miniaturização (transistores menores) e velocidades cada vez maiores.

#### 2.4.2 Quanto à volatilidade
- **Voláteis**: perdem o conteúdo quando a energia é desligada (ex.: RAM, cache, registradores).
- **Não voláteis**: mantêm o conteúdo sem energia (ex.: HD, SSD, CD/DVD).

#### 2.4.3 Quanto à posição/função no sistema
Da mais rápida (e mais cara/menor) para a mais lenta (e mais barata/maior):

1. **Registradores** — dentro da CPU, ao lado da ULA (Unidade Lógica Aritmética). Acesso mais rápido de todos, mas capacidade mínima. Semicondutoras e voláteis. Também chamadas de "memórias de rascunho".

2. **Memória cache** — dentro do processador, organizada em bancos/níveis (L1, L2, L3). L1 é a mais rápida (roda na frequência do processador); as demais são um pouco mais lentas mas ainda muito rápidas. Guarda dados que serão usados imediatamente pelo processador. Semicondutora e volátil. Útil especialmente para quem mantém muitos programas leves abertos ao mesmo tempo.

3. **Memória principal ("RAM")** — guarda os programas/dados em uso ou recém-usados. Semicondutora e volátil.
   - ⚠️ **Pegadinha**: "RAM" (*Random Access Memory* = acesso aleatório, mesmo tempo de acesso independente do endereço) é uma **característica**, não um tipo específico de memória — só que virou apelido popular da memória principal (assim como "Gillette" virou sinônimo de lâmina de barbear).
   - Padrão atual: **DDR5** (mais nova, ainda cara, poucas placas-mãe compatíveis) vs. **DDR4** (ainda o melhor custo-benefício e, em alto desempenho, ainda superior ao DDR5 disponível). DDR3 já está obsoleta.
   - **Frequência de clock**: sinal de tensão que oscila entre 0 e um valor definido pela placa-mãe; medido em Hz (oscilações/segundo). Quanto maior a frequência, mais rápida a RAM.
     - DDR4: 1.866 a 5.266 MHz | DDR5: 3.220 a 6.400 MHz (podendo chegar a 8.400 MHz)
   - **Tensão de alimentação**: DDR4 = 1,2–2,5V | DDR5 = 1,1–1,8V (menor tensão = menor consumo energético). Na DDR5, o regulador de tensão fica no próprio pente (não mais na placa-mãe).
   - **Capacidade do pente**: DDR4 tem limite de 16 GB por pente. Para descobrir a capacidade ideal do pente: quantidade total desejada ÷ número de canais (dual/triple/quad channel).
   - **ECC** (*Error Correction Code*): recurso de memórias de servidor/workstation que corrige dados corrompidos automaticamente.
   - **Buffer/Registered**: memórias com buffer usam menos corrente no barramento, permitindo instalar mais módulos na mesma máquina.
   - Quantidade recomendada: 4 GB (uso básico) | 8 GB (uso intermediário, várias abas/Word/WhatsApp) | 16 GB+ (jogos pesados, design, desenvolvimento).

4. **Memória Optane (Intel)** — tecnologia mais recente, baseada em **3D XPoint**. Fica entre o HD e a memória principal na hierarquia. Não volátil, semicondutora, e mais rápida que SSDs (NAND flash). Guarda os dados/programas mais usados para acelerar o acesso; é transparente ao usuário (não é acessada diretamente). O ganho é modesto se você já tem SSD, mas significativo se ainda usa HD magnético.

5. **Memória secundária (armazenamento em massa)** — acesso mais lento, não volátil, maior capacidade e menor custo por byte. Tipo de acesso **sequencial** (não aleatório como a RAM — o tempo de acesso varia conforme o endereço do dado).
   - **HD (Hard Disk)**: mídia magnética. Bits gravados como dipolos magnéticos (N/S) em pratos giratórios; cabeça de leitura/gravação lê/escreve nos pratos. Características a observar: velocidade de rotação (até 7.200 rpm em PCs, até 15.700 rpm em servidores), memória cache do disco, tamanho (2,5" ou 3,5"), interface (SATA III para PCs, SAS/fibra óptica para servidores), capacidade (em TB).
   - **SSD (Solid State Disk)**: mídia semicondutora tipo *flash*. Sem partes móveis → mais leve, mais rápido, mais resistente a choque, menos consumo de energia. Ainda tem custo por byte maior que HD magnético (mas essa diferença vem diminuindo). Novidade: **SmartSSD** — comprime dados internamente via circuito dedicado para "ganhar" espaço extra sem sobrecarregar o processador.
   - **CD/DVD/Blu-Ray**: mídia óptica, não volátil, usa laser (diodo semicondutor) para ler/gravar. Dados endereçados em setores organizados em espiral a partir do centro do disco. A diferença entre CD, DVD e Blu-Ray está na **cor/comprimento de onda do laser**: quanto menor a trilha de dados (Blu-Ray < DVD < CD), mais dados cabem no disco. Essas mídias estão em desuso, substituídas pela computação em nuvem.

### 2.5 Pirâmide hierárquica de memória
Do topo (mais rápido, mais caro por byte, menor capacidade) para a base (mais lento, mais barato, maior capacidade):

```
        Registradores      (mais rápido, menor capacidade)
           Cache
         Principal (RAM)
          [Optane]          ← camada intermediária opcional
         Secundária         (mais lento, maior capacidade)
```

---

## 3. Módulos de Entrada e Saída (E/S)

Além da CPU e da memória, o terceiro elemento essencial de um sistema computacional. Cada módulo de E/S (também chamado I/O ou GPIO) se conecta ao barramento e controla um ou mais periféricos (placa de vídeo, USB, placa de rede etc.). Um módulo de E/S não é só o conector físico — inclui a lógica de comunicação entre o periférico e o barramento.

### 3.1 Largura de banda
*Bandwidth* = capacidade de transmissão de um meio/conexão/rede — medida em **bits por segundo** (kbps, Mbps), **não** em bytes.

### 3.2 Barramentos
Conjunto de trilhas metálicas que interligam os componentes internos (CPU, memórias, dispositivos de E/S). Historicamente, a memória principal dividia o mesmo barramento de E/S (o que limitava a banda disponível); hoje há barramentos distintos para memória e para E/S, aumentando a performance.
- O **Chipset** (controlador de barramento) organiza a troca de dados entre os componentes externos ao processador.
- Cada barramento segue um **protocolo** (PCI, SCSI etc.) que define como os dispositivos acessam o barramento.
- A **política de arbitramento** decide qual dispositivo tem prioridade de acesso quando há disputa simultânea pelo barramento.

---

## Conclusão do capítulo

As memórias são a base que permite ao computador guardar e recuperar informação. A origem dos dados (teclado, áudio, imagem) depende de dispositivos que convertem sinais físicos em bits, e esses bits são organizados, endereçados e armazenados fisicamente por diferentes tecnologias (semicondutora, magnética, óptica). A hierarquia de memória equilibra velocidade, custo e capacidade, e os módulos de E/S com seus barramentos garantem que toda essa informação circule entre os componentes do computador.

---

## Glossário rápido

| Termo | Definição |
|---|---|
| Bit | Menor unidade de informação (0 ou 1) |
| Byte | Grupamento de 8 bits |
| Nibble | Grupamento de 4 bits |
| Word (palavra) | Quantidade de bits/bytes usada para codificar uma informação (padrão Intel = 16 bits) |
| Endereçamento | Forma de localizar um dado na memória via variável binária de n bits (2ⁿ endereços) |
| Memória volátil | Perde o conteúdo sem energia |
| Memória não volátil | Mantém o conteúdo sem energia |
| RAM | Random Access Memory — acesso com tempo igual independente do endereço (característica, não sinônimo exato de memória principal) |
| Cache (L1/L2/L3) | Memória rápida dentro do processador, guarda dados de uso imediato |
| DDR4/DDR5 | Padrões atuais de memória RAM |
| ECC | Código de correção de erros em memórias de servidor |
| Optane | Memória Intel (3D XPoint), entre RAM e HD, não volátil e mais rápida que SSD |
| HD | Armazenamento magnético (dipolos N/S em pratos) |
| SSD | Armazenamento semicondutor tipo flash, sem partes móveis |
| Largura de banda | Capacidade de transmissão de um meio, medida em bits/segundo |
| Barramento | Conjunto de trilhas que interligam os componentes do computador |
| Chipset | Controlador de barramento |

---

## Minhas anotações
_(vou preenchendo aqui conforme for estudando)_

-

---

**Referências do capítulo:** Beyond Logic (2018), Cantalice (2015), Hannesy & Patterson (2015), Kurose (2013), Monteiro (2007), Souza (2020), Stallings (2010), Tanenbaum (2016).
