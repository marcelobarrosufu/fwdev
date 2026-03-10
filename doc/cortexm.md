<!--- cSpell:enable --->

# Histórico da ARM e a Linha Cortex (com foco no Cortex-M4)

> [!NOTE]
> :robot:

## Origens da ARM

O desenvolvimento da arquitetura ARM remonta ao início da década de 1980, em meio a um contexto de efervescência na indústria de computadores pessoais. Em 1983, a empresa britânica Acorn Computers iniciou o projeto de uma nova arquitetura de processadores baseada em princípios RISC (Reduced Instruction Set Computer), buscando um conjunto de instruções mais simples e eficiente em comparação às arquiteturas CISC dominantes na época.

O primeiro protótipo, o ARM1, foi concluído em 1985 e implementado inicialmente como uma ferramenta de validação de conceito. O ARM2, lançado em 1986, foi o primeiro processador comercial a empregar essa arquitetura. A proposta se destacou pelo desempenho competitivo e, sobretudo, pela notável eficiência energética, o que se tornaria uma das principais características da família ARM.

Em 1990, a ARM Ltd. foi fundada como resultado de uma joint venture entre a Acorn, a Apple Computer e a VLSI Technology. A nova companhia adotou um modelo de negócio baseado no licenciamento de propriedade intelectual (IP), permitindo que diversos fabricantes ao redor do mundo produzissem e personalizassem seus próprios chips ARM. Esse modelo de licenciamento impulsionou de forma significativa a disseminação da arquitetura, especialmente em dispositivos móveis durante os anos 1990 e 2000, onde a eficiência de energia era um critério crucial.

Smartphones, PDAs, dispositivos de rede, controles remotos e uma vasta gama de produtos passaram a adotar processadores ARM, tornando a arquitetura uma das mais difundidas do mundo.

## Nascimento da Família Cortex

Com o amadurecimento da arquitetura e o crescimento da demanda por sistemas embarcados com diferentes perfis de desempenho e consumo, a ARM introduziu, em 2005, a família Cortex. Essa nova linha foi organizada de forma a atender três segmentos distintos de aplicação, todos baseados na arquitetura ARMv7:

- Cortex-A (Application): voltado para sistemas operacionais completos, como Android e Linux, com foco em alto desempenho e suporte a MMU (Memory Management Unit).
- Cortex-R (Real-time): destinado a sistemas de tempo real com requisitos rigorosos de confiabilidade e resposta determinística, como em aplicações automotivas e aeroespaciais.
- Cortex-M (Microcontroller): projetado para sistemas embarcados de baixo consumo, baixo custo e alta integração, sem necessidade de MMU, mas com suporte robusto a interrupções e operação em tempo real.

## Lançamento do Cortex-M4

O Cortex-M4 foi introduzido em 2010 como uma extensão da família Cortex-M, posicionado entre o Cortex-M3 (que não possui unidade de ponto flutuante) e o Cortex-M7 (de maior desempenho e lançado posteriormente). O M4 foi concebido especificamente para aplicações que exigem processamento determinístico com capacidades adicionais de processamento digital de sinais (DSP), como controle de motores, filtros digitais, reconhecimento de padrões e medição de sinais.

A arquitetura-base do M4 é a ARMv7E-M, uma extensão da ARMv7-M com instruções DSP e suporte opcional à unidade de ponto flutuante (FPU).

## Principais Recursos do Cortex-M4

### Arquitetura

O Cortex-M4 implementa um pipeline de 3 estágios (busca, decodificação e execução), o que permite operação eficiente e baixa latência na execução de instruções. Utiliza o conjunto de instruções Thumb-2, que combina instruções de 16 e 32 bits, proporcionando uma excelente relação entre densidade de código e desempenho.

Essa arquitetura permite o uso de ferramentas compactas de compilação, e facilita a produção de firmwares otimizados para espaço e consumo.

### Desempenho

O desempenho do Cortex-M4 varia de acordo com a implementação do fabricante, mas normalmente opera com clocks entre 100 MHz e 200 MHz. Seu consumo de energia é projetado para ser baixo, tornando-o ideal para dispositivos alimentados por bateria.

### Unidade de Ponto Flutuante (FPU)

A FPU opcional do M4 implementa operações de ponto flutuante em precisão simples (32 bits), conforme o padrão IEEE 754. Ela permite acelerar algoritmos que exigem matemática complexa sem penalidades de desempenho associadas a emulação em software. A presença da FPU é uma diferença marcante em relação ao Cortex-M3.

### Extensões DSP

A arquitetura ARMv7E-M introduz instruções especializadas para operações típicas de DSP, como:

- MAC (Multiply-Accumulate): comum em filtros digitais e controle vetorial de motores.
- Aritmética saturada: evita overflow em operações matemáticas.
- SIMD (Single Instruction, Multiple Data): permite operações paralelas em múltiplos dados, aumentando a eficiência em aplicações vetoriais.

### NVIC (Nested Vectored Interrupt Controller)

O controlador de interrupções NVIC é parte integrante do Cortex-M4 e viabiliza o gerenciamento eficiente de até 240 fontes de interrupção externas, a depender da implementação do fabricante. Cada interrupção pode ser configurada com até 256 níveis de prioridade, embora nem todos os bits de prioridade estejam necessariamente disponíveis — a granularidade depende do número de bits implementado no campo de prioridade do microcontrolador.

O NVIC suporta o aninhamento automático de interrupções, permitindo que interrupções de maior prioridade interrompam rotinas já em execução, desde que sua prioridade seja superior à da tarefa ou interrupção atual. Esse mecanismo de interrupção por prioridade é conhecido como preempção e é fundamental para aplicações em tempo real, em que eventos críticos precisam ser atendidos com mínima latência e previsibilidade.

Complementando esse controle fino, o registrador BASEPRI permite mascarar interrupções com prioridade igual ou inferior a um determinado nível, sem desabilitar globalmente o sistema de interrupções. Isso possibilita a criação de seções críticas com impacto mínimo sobre a responsividade do sistema. Ao contrário da instrução CPSID, que desabilita todas as interrupções com exceção da NMI e HardFault, o uso de BASEPRI permite preservar a resposta a eventos mais críticos, alinhando segurança e desempenho em sistemas preemptivos com múltiplas tarefas concorrentes.

### Suporte a RTOS

O Cortex-M4 foi projetado com suporte nativo à implementação de sistemas operacionais de tempo real (RTOS), fornecendo mecanismos de hardware otimizados para gerenciamento de tarefas e preempção. Instruções como SVC (Supervisor Call) e PendSV são fundamentais para esse suporte: a primeira permite a invocação de serviços do sistema a partir de tarefas em modo não privilegiado, enquanto a segunda é utilizada de forma programada para realizar a troca de contexto entre tarefas, com prioridade configurável para evitar interferência em interrupções críticas.

Um dos elementos-chave para a robustez em sistemas multitarefa é o registrador BASEPRI, que permite definir um limite inferior de prioridade para interrupções que devem ser mascaradas. Esse mecanismo possibilita a criação de seções críticas — trechos de código que não devem ser interrompidos — sem a necessidade de recorrer ao bloqueio global de todas as interrupções, como ocorreria com a instrução CPSID i. Dessa forma, o sistema mantém a responsividade a eventos de alta prioridade enquanto protege operações sensíveis. A capacidade de bloquear seletivamente interrupções abaixo de um determinado nível de prioridade é essencial para RTOS que operam com multitarefa preemptiva, pois permite preservar a execução de tarefas críticas sem comprometer a resposta a eventos externos urgentes.

Além disso, o Cortex-M4 implementa dois ponteiros de pilha distintos: o MSP (Main Stack Pointer), utilizado por padrão durante o boot e para exceções, e o PSP (Process Stack Pointer), normalmente empregado por tarefas em execução em um RTOS. Essa separação permite isolar os contextos de execução do kernel e das tarefas do usuário, contribuindo para maior segurança e previsibilidade na troca de contexto.

Outro recurso fundamental é o timer de sistema SysTick, um contador de 24 bits decremental, que pode ser configurado para gerar uma interrupção periódica — tipicamente a cada 1 milissegundo. Essa interrupção é amplamente utilizada por sistemas operacionais de tempo real como base para escalonamento temporal, controle de timeouts e execução periódica de tarefas. O SysTick é simples de configurar, possui prioridade própria e pode ser temporizado com base na frequência do clock do sistema, oferecendo alta precisão e previsibilidade. Sua integração direta com o núcleo Cortex-M4 faz dele uma peça central no suporte a escalonadores determinísticos.

Adicionalmente, o Cortex-M4 pode incorporar um recurso opcional chamado MPU (Memory Protection Unit), que permite a definição de regiões protegidas de memória com diferentes atributos de acesso (leitura, escrita, execução). Essa funcionalidade é extremamente útil em ambientes com RTOS, pois permite isolar tarefas de usuário umas das outras e do kernel, prevenindo acessos indevidos, aumentando a robustez do sistema e reduzindo o risco de falhas causadas por bugs ou comportamentos inesperados. O uso do MPU também facilita a certificação de segurança em aplicações críticas, como as encontradas nos setores médico, automotivo e aeroespacial.

### Depuração e Rastreamento

Para facilitar o desenvolvimento e a depuração, o Cortex-M4 inclui:

- SWD (Serial Wire Debug): interface de depuração compacta e eficiente.
- DWT (Data Watchpoint and Trace): permite rastreamento de acessos a memória e contagem de ciclos.
- ITM (Instrumentation Trace Macrocell): canal de debug para envio de mensagens.
- ETM (Embedded Trace Macrocell): rastreamento completo de execução (presente em algumas variantes).

Esses recursos são essenciais para a validação de sistemas embarcados críticos, especialmente em ambientes regulados como os setores médico e automotivo.

O Cortex-M4 representa uma solução de compromisso altamente eficaz entre desempenho, consumo e complexidade. Sua ampla adoção em sistemas embarcados é reflexo direto da combinação de arquitetura otimizada, suporte a DSP, unidade de ponto flutuante, e mecanismos avançados de interrupção e multitarefa, consolidando-o como uma das opções mais populares para projetos que requerem controle em tempo real e cálculo eficiente.

## Processo de Partida do Cortex-M4 (Boot)

O processo de partida de um sistema baseado no núcleo Cortex-M4 segue uma sequência bem definida e padronizada, que ocorre imediatamente após a liberação do sinal de reset. Essa sequência é responsável por configurar os elementos fundamentais da CPU e preparar o sistema para a execução do firmware principal.

### Vetor de Interrupções

A primeira etapa do processo de boot é a leitura da tabela de vetores de interrupção, localizada no início do espaço de memória — geralmente no endereço 0x00000000, embora isso possa ser redirecionado por meio do registrador VTOR (Vector Table Offset Register). Essa tabela consiste em uma lista ordenada de endereços de 32 bits, onde cada entrada representa o ponteiro para uma função de tratamento de exceção ou interrupção.

A primeira entrada da tabela (offset 0) contém o valor inicial do Stack Pointer — mais especificamente, do Main Stack Pointer (MSP). Esse valor é carregado automaticamente no registrador R13 (MSP) pelo hardware do núcleo assim que o reset é liberado.

A segunda entrada da tabela (offset 4) é o endereço da função chamada Reset Handler — o ponto de entrada do programa. Este é o primeiro código de firmware efetivamente executado pelo processador após a inicialização.

### Inicialização do Stack Pointer

Antes mesmo da execução de qualquer instrução, o processador carrega o valor contido no endereço 0x00000000 diretamente no MSP. Este valor é, em geral, o endereço mais alto da RAM disponível, garantindo espaço para crescimento descendente da pilha. Como o Cortex-M4 oferece suporte a duas pilhas — MSP e PSP —, durante a inicialização, o MSP é utilizado como pilha principal, inclusive para o tratamento de interrupções.

A correta configuração desse valor na imagem do firmware (tipicamente via linker script) é essencial, pois qualquer inconsistência poderá causar falhas graves durante a execução, como hard faults ao tentar empilhar dados em regiões inválidas de memória.

### Reset Handler

O Reset Handler é o primeiro trecho de código executado e é responsável pela configuração inicial do sistema. Suas responsabilidades típicas incluem:

- Inicialização do sistema de clock e periféricos básicos.
- Cópia de dados da seção .data da flash para a RAM.
- Inicialização cmo zero da seção .bss (variáveis não inicializadas).
- Eventualmente, configuração da pilha secundária (PSP) e de vetores de interrupção alternativos.
- Chamada da função principal do sistema (main()).

Essa função é geralmente definida em arquivos de startup fornecidos pelo fabricante ou por bibliotecas como CMSIS, sendo marcada com atributos especiais para garantir sua localização correta na tabela de vetores.

### VTOR – Vector Table Offset Register

O registrador VTOR (endereçado em SCB->VTOR, onde SCB é o System Control Block) permite redirecionar a base da tabela de vetores para outro endereço de memória. Esse recurso é útil em sistemas que implementam bootloaders, onde o código inicial pode repassar a execução para uma aplicação principal, ajustando o VTOR para que o processador passe a buscar os vetores da nova região.

O valor de VTOR deve ser múltiplo de 0x200 (512 bytes), conforme exigido pela arquitetura. A configuração adequada desse registrador garante que as exceções e interrupções sejam corretamente roteadas, mesmo após trocas dinâmicas de contexto.

Esse processo, inteiramente gerenciado pelo hardware e com intervenção mínima do programador, contribui significativamente para a previsibilidade, simplicidade e segurança do boot em sistemas baseados em Cortex-M. O uso adequado da tabela de vetores, configuração da pilha e do VTOR é essencial para o funcionamento robusto do firmware desde seus primeiros ciclos.

## Registro de Status do Cortex-M4 (xPSR)

O registrador de status do programa no Cortex-M4 é chamado de xPSR (Program Status Register estendido) e é composto por três sub-regiões lógicas: APSR (Application PSR), IPSR (Interrupt PSR) e EPSR (Execution PSR). Juntas, essas regiões refletem o estado atual da CPU e são automaticamente atualizadas pelo hardware durante a execução de instruções, exceções e interrupções.


A seguir está uma representação do layout interno do registrador xPSR, com destaque para os campos mais relevantes:

```
       31  30  29  28  27  26  25  24  23 .. 20 19   16 15  .. 10  9  8      ..        0
     +---+---+---+---+---+---------------------+-------+---------------------------------+
APSR | N | Z | C | V | Q |                     |   GE  |                                 |
     +---+---+---+---+---+---------------------+-------+-------------+-------------------+
IPSR |                                                               |  Exception number |
     +--------------------+------+---+-----------------+------+------+-------------------+
EPSR |                    |ICI/IT| T |                 |ICI/IT|                          |
     +--------------------+------+---+-----------------+------+--------------------------+
```

Legenda:
- **N, Z, C, V**: Flags de condição (Negative, Zero, Carry, Overflow).
- **T**: Thumb state bit (sempre 1 em Cortex-M).
- **Exception number**: Número da exceção atual ou zero, caso não tenha nenhuma interrupção ativa.

Esses campos são atualizados automaticamente pelo hardware e são cruciais para o comportamento do sistema em tempo real.

### Acessos ao xPSR

O xPSR não é acessado diretamente como um único registrador, mas pode ser consultado por partes utilizando instruções do tipo `MRS` e `MSR`, que interagem com os subcampos específicos:

- **APSR (Application PSR)**: Permite acesso às flags de condição (N, Z, C, V, Q), úteis para lógica condicional e análise de resultados.
- **IPSR (Interrupt PSR)**: Retorna o número da exceção em execução no momento, incluindo interrupções e falhas.
- **EPSR (Execution PSR)**: Contém o bit T (modo Thumb) e o campo IT, associado ao controle de execução condicional.

O entendimento detalhado do xPSR e seus subcampos é essencial em sistemas embarcados, especialmente quando há manipulação direta de contexto em trocas de tarefa (context switch), análise de exceções e depuração em tempo real.

























