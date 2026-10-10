# 🐾 CasaScript 2.0 — Compilador Completo de Automação para Pets

> **Desafio CP06 — Tema: Automação Residencial para Pets (Pet Tech)**
> Compilador de ponta a ponta (*Front-End*, *Back-End*, Otimizador e Máquina Virtual) para a linguagem **CasaScript 2.0**, construído com [Lark](https://lark-parser.readthedocs.io/) e interface interativa em `ipywidgets`.

[![Python](https://img.shields.io/badge/Python-3.11-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![Lark](https://img.shields.io/badge/Lark-1.3-8A2BE2)](https://lark-parser.readthedocs.io/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?logo=jupyter&logoColor=white)](https://jupyter.org/)
[![Google Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/)

---

### 👥 Integrantes

| Nome | RA |
|---|---|
| Diego Azevedo Dias Ignacio | 2392072 |
| Gabriella Pereira Rodrigues | 2334425 |
| Giovanna Bispo Da Silva | 2367880 |
| José Henrique Guimarães Galvão Silva | 2499948 |

---

## 📌 Sobre o Projeto

O mercado de **Pet Tech** exige automação residencial focada na saúde e bem-estar dos animais de estimação. A **CasaScript 2.0** permite programar automações com suporte a horários, sensores de presença, ambiente e atuadores com controle de borda de subida (`ENTAO`) e descida (`SENAO`):

```casascript
# Programa completo com os 6 sensores da casa inteligente para Pets
QUANDO relogio >= 08:00 E relogio <= 09:00 ENTAO
    dispensar(comedouro, 100);
    notificar("Hora do café do pet!")
SENAO
    notificar("Fora do horário de refeição")
FIM

QUANDO temperatura > 25 ENTAO
    ajustar(climatizador, 22)
SENAO
    desligar(climatizador)
FIM

QUANDO presenca_pet ENTAO
    ligar(camera)
SENAO
    desligar(camera)
FIM

QUANDO nivel_agua < 20 ENTAO
    trocar_agua(fonte_agua);
    notificar("Água trocada automaticamente!")
FIM

QUANDO peso_racao < 100 ENTAO
    dispensar(comedouro, 150)
FIM

QUANDO status_pet == "brincando" ENTAO
    notificar("Pet em atividade!")
FIM
```

Abaixo, a interface interativa do **CasaPet Studio 2.0** executando o controle em tempo real dos dispositivos:

<img src="docs/01-casapet-studio.png" alt="Interface Interativa CasaPet Studio 2.0">

---

## ⚙️ Arquitetura do Compilador

```text
Código-Fonte (.casascript)
       │
       ▼
 1. Análise Léxica          --> Tokens + Validação de Horário (HH:MM)
       │
       ▼
 2. Análise Sintática       --> Árvore de Sintaxe Concreta (LALR(1))
       │
       ▼
 3. Construção da AST       --> Árvore Sintática Abstrata
       │
       ▼
 4. Análise Semântica       --> Tabela de Símbolos Pet + Análise de Tipos + Avisos
       │
       ▼
 5. Otimizador de AST       --> Constant Folding + Identidades + Eliminação de Dupla Negação
       │
       ▼
 6. Gerador de Bytecode     --> Código para Máquina de Pilha (Condição, ENTAO, SENAO)
       │
       ▼
 7. Máquina Virtual (VM)    --> Runtime Orientado a Eventos (Borda de Subida e Descida)
```

Inspeção da Árvore Sintática Abstrata (AST) otimizada e do Bytecode gerados no painel da IDE:

<img src="docs/021-ast.png" alt="Inspeção da Árvore Sintática Abstrata (AST)">

<img src="docs/02-bytecode.png" alt="Inspeção do Bytecode Gerado">

---

## 📂 Estrutura do Notebook

| Seção | Conteúdo |
|---|---|
| Cabeçalho Markdown | Apresentação do tema, integrantes e exemplo no domínio Pet Tech |
| 1) Preparação do ambiente | Instalação e importações (`lark`, `ipywidgets`, `dataclasses`) |
| 2) Funções de apresentação | Funções auxiliares HTML (`tabela`, `caixa`) |
| 3) Gramática do compilador | **RF1/RF2**: Gramática LALR(1) com `HORARIO.2` e bloco `SENAO` |
| 4) Construção da AST | **RF1/RF2**: Transformer da AST e nó `Regra` expandido |
| 5) Análise Semântica | **RF3**: Tabela de símbolos do cenário Pet, verificação de tipos e avisos |
| 6) Otimização | **RF4**: Eliminador de dupla negação, identidades e *constant folding* |
| 7) Geração de Bytecode | **RF5**: Tradutor de AST para bytecode de máquina de pilha (3 blocos) |
| 8) Máquina Virtual & CasaPet | **RF5**: Processador de pilha e simulação física do hardware dos pets |
| 9) Central de Automação | **RF5**: Runtime orientado a eventos com disparo em borda de subida/descida |
| 10) Compilador Completo | Função `compilar_pet()` com integração de todas as fases |
| 11) Bateria de Testes | **RF6**: 14 casos de teste automáticos (válidos, erros e avisos) |
| 12) Simulação do Dia | **RF6**: Execução contínua de 11 eventos simulados na casa |
| 13) CasaPet Studio 2.0 | Interface interativa gráfica em `ipywidgets` com abas e cartões de estado |

---

## 🔤 Tabela de Símbolos do Cenário Pet (RF3)

A linguagem suporta **6 sensores** (4 tipos de dados diferentes) e **5 dispositivos** (3 categorias distintas).

### 🌡️ Sensores
| # | Sensor | Tipo | Faixa Válida / Valores Aceitos | Descrição |
|---|---|:---:|:---:|---|
| 1 | `peso_racao` | `numero` | `0` a `2000` (g) | Quantidade de ração disponível no reservatório |
| 2 | `nivel_agua` | `numero` | `0` a `100` (%) | Nível do reservatório da fonte do pet |
| 3 | `temperatura` | `numero` | `-10` a `50` (°C) | Temperatura ambiente do recinto |
| 4 | `relogio` | `horario` | `00:00` a `23:59` | Horário atual (convertido em minutos no dia) |
| 5 | `presenca_pet` | `logico` | `VERDADEIRO` / `FALSO` | Sensor infravermelho/presença do animal |
| 6 | `status_pet` | `texto` | `"dormindo"`, `"brincando"`, `"comendo"`, `"ausente"` | Estado de atividade reconhecido por IA |

### 🛠️ Dispositivos e Atuadores
| # | Dispositivo | Categoria | Ações Aceitas | Parâmetros |
|---|---|:---:|---|---|
| 1 | `comedouro` | `comedouro` | `dispensar` | `dispositivo, numero` (10g a 500g) |
| 2 | `fonte_agua` | `fonte` | `trocar_agua`, `ligar`, `desligar` | `dispositivo` |
| 3 | `climatizador` | `climatizador` | `ajustar`, `ligar`, `desligar` | `dispositivo, numero` (16°C a 30°C) |
| 4 | `camera` | `camera` | `ligar`, `desligar` | `dispositivo` |
| 5 | `tigela_agua` | `tigela` | `trocar_agua` | `dispositivo` |

---

## ⚖️ Prioridades e Conflitos do Lexer (RF1)

| Prioridade | Terminais | Por quê |
|:---:|---|---|
| 2 | `HORARIO.2` | Vence `NUMERO` (evita que `08:00` seja lido como `NUMERO(08)` seguido de erro em `:`) |
| 1 | `NUMERO` | Literal numérico inteiro ou decimal genérico (`/\d+(\.\d+)?/`) |
| 1 | `TEXTO` | Literal delimitado entre aspas duplas (`/"[^"\r\n]*"/`) |
| 1 | `NOME`, `OP_COMP` | Identificadores, nomes de sensores/dispositivos e comparadores |

---

## 📖 Diário de Ambiguidade e Desafios de Compilação

### 1. Conflito `HORARIO` vs `NUMERO` (RF1)
No formato `08:00`, a primeira tentativa do lexer sem prioridade definia `08` como `NUMERO` e falhava no caractere `:`. A solução foi aplicar a prioridade **`HORARIO.2`** sobre o `NUMERO`, garantindo que toda a cadeia `HH:MM` seja consumida como **um único token de horário**. No Transformer, o horário é convertido para **minutos desde a meia-noite** ($08:00 \rightarrow 480$), permitindo comparações numéricas rápidas no bytecode.

### 2. Recursão em Regras Aritméticas e Lógicas (Prevenção de `IndexError`)
Na análise sintática, atribuir o mesmo alias (`-> op_arit`) a regras isoladas (ex: `soma : produto`) causava erro `IndexError` no Transformer ao receber apenas 1 filho. A gramática foi estruturada de forma recursiva:
```ebnf
?soma    : produto | soma OP_SOMA produto -> op_arit
?produto : atomo   | produto OP_MULT atomo -> op_arit
```
Isto permite que elementos isolados sejam simplificados automaticamente sem invocar a transformação binária de operadores.

### 3. Avaliação de Borda no Runtime (RF5)
Para evitar que o comedouro liberte ração continuamente a cada ciclo enquanto a condição permanecer verdadeira, o runtime da `CentralAutomaçaoPet` armazena o estado anterior de cada regra:
* **Borda de Subida** ($FALSO \rightarrow VERDADEIRO$): Dispara o bloco `ENTAO`.
* **Borda de Descida** ($VERDADEIRO \rightarrow FALSO$): Dispara o bloco `SENAO`.

---

## 🚨 Tratamento de Erros

O compilador categoriza e trata erros em todas as fases, fornecendo linha, coluna e contexto amigável:

| Fase do Erro | Exemplo de Entrada | Mensagem de Saída do Compilador |
|:---:|---|---|
| **Léxica** | `relogio == 25:00` | `ERRO LÉXICO na linha 1, coluna 21: caractere inválido ':'.` |
| **Sintática** | `QUANDO presenca_pet ENTAO ligar(camera)` | `ERRO SINTÁTICO na linha 1, coluna 39: encontrei '', esperava: FIM, SEMICOLON` |
| **Sintática** | `QUANDO nivel_agua < 10 ENTAO trocar_agua(fonte_agua) SENAO FIM` | `ERRO SINTÁTICO na linha 1, coluna 60: encontrei 'FIM', esperava: um nome` |
| **Semântica** | `QUANDO nivel_bateria < 10 ENTAO notificar("OK") FIM` | `Linha 1, col 8: sensor 'nivel_bateria' não existe. Você quis dizer 'nivel_agua'?` |
| **Semântica** | `QUANDO presenca_pet ENTAO dispensar(camera, 100) FIM` | `Linha 1, col 47: dispositivo 'camera' não aceita a ação 'dispensar'.` |

Visualização das caixas de alertas e mensagens de erros semânticos:

<img src="docs/05-erros-semanticos.png" alt="Mensagens de Erro e Avisos Semânticos">

---

## 🧪 Casos de Teste (RF6)

A bateria de testes automáticos executa **14 cenários**, alcançando **100% de aprovação (`14/14 aprovados`)**:

```text
========================================================================================
 Bateria de Testes: 14/14 aprovados (100% Sucesso)
========================================================================================
 ✔  1. Regra simples com Horário e SENAO              | Esperado: ok        | Obtido: ok
 ✔  2. Controle de Climatizador e Iluminação          | Esperado: ok        | Obtido: ok
 ✔  3. Verificação de Status do Pet                   | Esperado: ok        | Obtido: ok
 ✔  4. Nível de água e troca de água                  | Esperado: ok        | Obtido: ok
 ✔  5. Símbolo inválido (@)                           | Esperado: léxica    | Obtido: léxica
 ✔  6. Horário inválido (25:00)                       | Esperado: léxica    | Obtido: léxica
 ✔  7. Faltou o fechamento FIM                        | Esperado: sintática | Obtido: sintática
 ✔  8. Bloco SENAO sem nenhuma ação                   | Esperado: sintática | Obtido: sintática
 ✔  9. Comparador malformado                          | Esperado: sintática | Obtido: sintática
 ✔ 10. Sensor inexistente                             | Esperado: semântica | Obtido: semântica
 ✔ 11. Dispositivo incompatível dentro do SENAO       | Esperado: semântica | Obtido: semântica
 ✔ 12. Comparação de Tipos Incompatíveis              | Esperado: semântica | Obtido: semântica
 ✔ 13. Aviso: Ações repetidas na mesma regra          | Esperado: ok        | Obtido: ok
 ✔ 14. Aviso: Condição duplicada entre regras         | Esperado: ok        | Obtido: ok
========================================================================================
```

Resultado visual da bateria de testes no Google Colab:

<img src="docs/03-bateria-testes.png" alt="Bateria de Testes com 14/14 aprovados">

---

## 📊 Simulação do Dia a Dia no Pet House (RF6)

A Célula 12 simula **11 eventos contínuos** na casa, demonstrando os disparos por borda de subida e descida:

| Evento no Sensor | Regras Disparadas | Ações Executadas no Sistema Pet |
|---|:---:|---|
| `relogio = 07:00` | - | *(Nenhuma ação executada)* |
| `relogio = 08:00` | `#1 (ENTAO)` | `[08:00] DISPENSAR: 100g de ração em 'comedouro'` |
| `presenca_pet = True` | `#3 (ENTAO)` | `[08:00] LIGAR: 'camera'` |
| `temperatura = 28` | `#2 (ENTAO)` | `[08:00] AJUSTAR: 'climatizador' para 22°C` |
| `temperatura = 23` | `#2 (SENAO)` | `[08:00] DESLIGAR: 'climatizador'` *(Borda de Descida)* |
| `relogio = 09:10` | `#1 (SENAO)` | `[09:10] NOTIFICAÇÃO: Fora do horário de refeição` *(Borda de Descida)* |
| `presenca_pet = False` | `#3 (SENAO)` | `[09:10] DESLIGAR: 'camera'` *(Borda de Descida)* |
| `status_pet = comendo` | - | *(Nenhuma ação executada)* |
| `nivel_agua = 15` | `#4 (ENTAO)` | `[09:10] TROCAR ÁGUA: Drenando e reabastecendo 'fonte_agua'` |
| `temperatura = 29` | `#2 (ENTAO)` | `[09:10] AJUSTAR: 'climatizador' para 22°C` |
| `temperatura = 21` | `#2 (SENAO)` | `[09:10] DESLIGAR: 'climatizador'` *(Borda de Descida)* |

Tabela interativa gerada após a simulação de 1 dia na casa simulada:

<img src="docs/04-simulacao-dia.png" alt="Tabela de Simulação de Eventos do Dia a Dia">

---

## ⭐ CasaPet Studio 2.0 (Interface Interativa)

A Célula 13 disponibiliza um mini-IDE visual integrado:

- **Aba IDE Compilador**: Edição de regras com botão de compilação e abas para inspeção da **AST Otimizada**, **Análise Semântica (Avisos)**, **Relatório do Otimizador** e **Bytecode Gerado**.
- **Aba CasaPet Simulador**: Controle em tempo real através de *sliders* (Relógio, Temperatura, Água, Ração), seletores de status e cartões visuais para cada equipamento, acompanhados pelo **Diário de Eventos da Casa**.

---

## ✅ Cobertura dos Critérios de Avaliação

| Critério de Avaliação | Pontuação | Localização no Projeto |
|---|:---:|---|
| **RF1 — Léxico (HORARIO)** | 1,5 | Célula 3 (Gramática com `HORARIO.2`) e Célula 4 (Minutos) |
| **RF2 — Sintático (SENAO)** | 1,5 | Célula 3 (Regra EBNF) e Célula 4 (Estrutura da Regra) |
| **RF3 — Semântico (Tabela Pet + 2 avisos)** | 2,0 | Célula 5 (`SENSORES_PET`, `ACOES_PET` e avisos) |
| **RF4 — Otimização (2 técnicas novas)** | 1,5 | Célula 6 (Dupla negação e identidades aritméticas) |
| **RF5 — Geração de Código e Runtime** | 1,5 | Célula 7 (Bytecode), Célula 8 (VM) e Célula 9 (Bordas) |
| **RF6 — Testes e Simulação** | 1,5 | Célula 11 (14 testes) e Célula 12 (11 eventos simulados) |
| **README e Organização do Código** | 0,5 | Arquivo `README.md` completo e código limpo |
| **Total** | **10,0** | **100% dos Requisitos Concluídos** |

---

<sub>Os exemplos e tabelas demonstrados neste README referem-se à execução direta do notebook no Google Colab através do pipeline de compilação da linguagem CasaScript 2.0.</sub>
