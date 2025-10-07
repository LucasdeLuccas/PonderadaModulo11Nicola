# 💻 Ponderada de CPU de 8 Bits

Essa ponderada apresenta a implementação dos componentes de uma **CPU de 8 bits funcional**, com foco na sua **Unidade Lógica e Aritmética (ULA)**.

## 🧱 1. Blocos Fundamentais da Aritmética

A base para qualquer operação aritmética em uma CPU começa com a capacidade de somar bits. A partir de componentes simples, construímos progressivamente circuitos mais complexos capazes de realizar cálculos com palavras de 8 bits.

### Half Adder (Meio Somador)

<img width="721" height="178" alt="half-adder" src="https://github.com/user-attachments/assets/f4eada12-bb63-40bc-b106-aa042f9714a7" />

O **Half Adder** é o circuito mais elementar para a soma binária. Ele recebe dois bits como entrada (A e B) e produz duas saídas: a **Soma (Sum)** e o **Transporte (Carry)**[351, 352, 353]. A soma é o resultado da operação XOR entre as entradas, e o transporte é o resultado da operação AND[354, 355].

### Full Adder (Somador Completo)
<img width="2296" height="566" alt="full_adder" src="https://github.com/user-attachments/assets/2a81ede5-12c6-40c0-8e1a-f115ca4d71e4" />

Para construir somadores maiores, é preciso de um componente que lide com um bit de transporte vindo de uma soma anterior. O **Full Adder** é construído a partir de dois Half Adders e uma porta OR, somando três bits (A, B e um Carry-In) para produzir uma Soma e um Carry-Out[1, 2, 3, 4, 5].

### 8-bit Adder (Somador de 8 bits)
<img width="2996" height="1596" alt="8bit_adder" src="https://github.com/user-attachments/assets/3c38f810-555b-4aa6-9f8c-f53f71ff2ded" />


Com o Full Adder como bloco de construção, é criado um somador de 8 bits conectando oito deles em cascata[ 57, 58]. O Carry-Out de um estágio alimenta o Carry-In do próximo[52, 53, 54, 55, 56]. Este circuito é a base para a operação de soma na ULA.

### Subtrator de 8 bits
<img width="408" height="119" alt="8bit_subtractor" src="https://github.com/user-attachments/assets/03389074-9623-488e-90ec-783906c7b682" />


A operação de subtração (`A - B`) é implementada usando a técnica do **complemento de dois**, reutilizando o somador de 8 bits. O operando `B` é invertido (NOT) e o `Carry-In` inicial do somador é definido como `1`, resultando em `A + (~B) + 1`[: 358, 359, 360, 361, 362].

### Multiplicador e Divisor (Operandos de 5 bits)
  * **5 bit Adder**
  <img width="1668" height="1416" alt="adder_5bit" src="https://github.com/user-attachments/assets/9f057c19-c766-4448-98aa-1c6d9d14bb08" />

  
  * **Multiplicador:** Utiliza uma matriz de portas AND para gerar produtos parciais e, em seguida, uma rede de somadores para combiná-los, gerando o resultado final de 8 bits[: 166, 167, 168, 169].
  * <img width="3216" height="1576" alt="multiplier" src="https://github.com/user-attachments/assets/926129f4-debc-48b9-8c92-14db8bd5e0d7" />

  * **Divisor:** Implementa a lógica de divisão binária para calcular um **Quociente (Q)** e um **Resto (R)** a partir de dois operandos de 4 bits[: 89, 90, 91, 92, 93]. É construído modularmente a partir de componentes `divide_step`[: 93, 94].
  * <img width="1782" height="998" alt="divisor" src="https://github.com/user-attachments/assets/715aa6a4-cd8e-4276-9f76-8930d1e72dee" />


*Detalhe do componente `divide_step` usado no divisor:*
<img width="2350" height="478" alt="divide_step" src="https://github.com/user-attachments/assets/137fb206-28bb-4540-bc08-55203c096150" />


### Outras Operações Aritméticas

Os circuitos para deslocamento de bits, incremento e decremento também foram implementados como módulos dedicados.

*Diagrama do Shifter, Incrementador e Decrementador:*

<img width="389" height="99" alt="8bit_decrementor" src="https://github.com/user-attachments/assets/9a45e8f8-eadd-468d-87b7-84810e3fd110" />
<img width="429" height="99" alt="8bit_incrementor" src="https://github.com/user-attachments/assets/b4036476-a980-45f2-9306-631c97425c6d" />
<img width="388" height="185" alt="shifter_left_8bit" src="https://github.com/user-attachments/assets/689ccb43-d2b2-4094-b9b2-0b72f691d216" />



## 🧠 2. A ULA (Unidade Lógica e Aritmética)
<img width="3358" height="1354" alt="ULA" src="https://github.com/user-attachments/assets/773b5c16-3507-4c54-904f-4b47629e5171" />


A **Unidade Lógica e Aritmética (ULA)** é o coração computacional da CPU. Ela executa as operações de cálculo e lógicas. A ULA (`ULA.dig`) foi projetada para realizar um conjunto completo de operações, selecionando o resultado apropriado com base em um sinal de controle `OP[3:0]`[120].

### Integração e Funcionamento

O circuito final da ULA reúne todas as unidades operacionais. Um **Multiplexador (MUX)** de 16 para 1 é o componente chave, selecionando qual resultado será a saída final (`RES`) com base no opcode `OP`[126].

Além do resultado, a ULA gera a **`Flag Zero (Z)`**[141]. Esta saída se torna `1` somente quando o resultado da operação é exatamente zero. Isso é feito com uma porta NOR de 8 entradas[139].

### Conjunto de Operações da ULA

| OP (Decimal) | Mnemônico | Operação | Descrição | Componente Utilizado |
| :--- | :--- | :--- | :--- | :--- |
| 0 | `SOMA` | `RES = A + B` | Soma os operandos A e B[: 127]. | `8bit_adder.dig` |
| 1 | `SUB` | `RES = A - B` | Subtrai o operando B de A[: 128]. | `8bit_subtractor.dig` |
| 2 | `MUL` | `RES = A[3:0] * B[3:0]` | Multiplica os 4 bits menos significativos de A e B[131]. | `multiplier.dig` |
| 3 | `SHL` | `RES = A << 1` | Desloca os bits de A uma posição para a esquerda[127]. | `shifter_left_8bit.dig` |
| 4 | `DIV` | `RES = R[7:4] | Q[3:0]` | Divide A[3:0] por B[3:0]. O resultado combina Resto e Quociente[135]. | `divisor.dig` |
| 5 | `INC A` | `RES = A + 1` | Incrementa o operando A[:135]. | `8bit_incrementor.dig` |
| 6 | `DEC A` | `RES = A - 1` | Decrementa o operando A[136]. | `8bit_decrementor.dig` |

-----

## 🏗️ 3. Arquitetura e Caminho de Dados (Datapath)

Enquanto a ULA é o centro de cálculo, a arquitetura define como os dados se movem e são armazenados. O Caminho de Dados é o conjunto de registradores, barramentos (bus) e memória que formam o "sistema circulatório" da CPU.

### Memória (`memory.dig`)

O bloco de memória armazena tanto as instruções do programa quanto os dados. Para acessar uma posição, seu endereço é primeiro colocado no barramento de endereços. A memória então disponibiliza o dado contido nesse endereço em sua saída. Para este projeto, foi utilizada uma ROM para que o programa não se perca entre as simulações.

<img width="758" height="243" alt="memory" src="https://github.com/user-attachments/assets/af848cdd-a2b3-403a-8cbe-49cae5ef16e8" />


### Program Counter (`ProgramCounter.dig`)

O **Program Counter (PC)** é um registrador especial que tem uma função crucial: apontar para o endereço da **próxima instrução** a ser executada na memória. A cada ciclo de busca (fetch), o PC é incrementado. Em operações de desvio (Jumps), um novo endereço pode ser carregado diretamente no PC, alterando o fluxo de execução do programa.

<img width="635" height="198" alt="ProgramCounter" src="https://github.com/user-attachments/assets/2d7ac783-f6ee-4495-a83a-29d3a19d7a87" />

### Registradores (`ResettableRegister.dig`)

Os registradores são a memória de acesso mais rápido da CPU. O componente `ResettableRegister.dig` serve como base para os registradores essenciais do nosso datapath:

  * **Acumulador (Registrador A):** Armazena o resultado da maioria das operações da ULA.
  * **Registrador B:** Armazena o segundo operando para a ULA.
  * **Registrador de Instrução (IR):** Armazena a instrução que está sendo executada. O output deste registrador é dividido: uma parte (opcode) vai para a Unidade de Controle e a outra (operando/endereço) retorna ao datapath.

<img width="1764" height="560" alt="ResettableRegister" src="https://github.com/user-attachments/assets/4b9eec61-8a30-4887-95f0-d95e7c1f4166" />

## 🕹️ 4. A Unidade de Controle (Control Unit)

A Unidade de Controle (`ControlUnit.dig`) é o "cérebro" da CPU. Ela não processa dados, mas gera os sinais de controle que dizem a todos os outros componentes o que fazer e quando, orquestrando o fluxo de dados para executar as instruções.

### Sequenciador (`sequenciator.dig`)

O tempo é a base para o funcionamento da UC. O **Sequenciador** é um circuito que gera uma sequência de pulsos de tempo (estados `T1`, `T2`, `T3`...). A cada pulso de clock, ele avança para o próximo estado. Cada estado `T` corresponde a um passo em um ciclo da CPU, permitindo que as instruções sejam divididas em etapas menores e sincronizadas.

<img width="854" height="355" alt="sequenciator" src="https://github.com/user-attachments/assets/cf630068-4dae-413b-a991-0efc61e835ab" />

### Lógica de Controle e Ciclo de Instrução

A lógica principal do **Controlador** combina duas informações: o **Opcode** da instrução atual (vindo do IR) e o **estado de tempo** atual (vindo do Sequenciador). Com base nisso, um circuito de lógica combinacional ativa os sinais de controle corretos para o datapath. Esse processo ocorre em dois ciclos:

1.  **Ciclo de Busca (Fetch):** É a primeira fase e é idêntica para todas as instruções.

      * **T1:** O endereço do PC é colocado no barramento e carregado no registrador de endereço da memória (MAR).
      * **T2:** O dado apontado pelo MAR (a instrução) é carregado no Registrador de Instrução (IR).
      * **T3:** O PC é incrementado para apontar para a próxima instrução.

2.  **Ciclo de Execução (Execute):** Após o fetch, a UC decodifica o opcode e executa a ação específica da instrução, ativando os sinais corretos na ULA, registradores e memória nos tempos subsequentes (T4, T5, etc.).

<img width="6458" height="1406" alt="ControlUnit" src="https://github.com/user-attachments/assets/31ba3e4a-10f6-47a8-bbdb-31d30eb56e8b" />

## 🚀 5. Integração Final da CPU (`CPU.dig`)

Finalmente, todos os componentes modulares são conectados para formar a CPU completa. O diagrama principal (`CPU.dig`) mostra a união do **Caminho de Dados** com a **Unidade de Controle**. O clock principal (`CLK`) sincroniza todas as operações, e um sinal de `Reset` inicializa todos os registradores.

Neste nível, a Unidade de Controle envia seus sinais para governar o PC, a Memória e os Registradores, garantindo que as instruções sejam buscadas e executadas em uma sequência ordenada e precisa.

<img width="1996" height="1556" alt="newCPU" src="https://github.com/user-attachments/assets/dc0bcc10-bbd9-476b-8775-69b0a2573252" />


# Demonstração Prática

https://youtu.be/mwKPHa01x3Y
 
## ⚙️ Como Executar o Projeto

1.  **Pré-requisito:** Baixe e instale um emulador de hardware como **Digital** ou **Logisim-Evolution**.
2.  **Clone o Repositório:**
    ```bash
    git clone https://github.com/LucasdeLuccas/PonderadaModulo11Nicola.git
    ```
3.  **Abra o Projeto:** No emulador, abra o arquivo principal do circuito, **`CPU.dig`**.
4.  **Teste:** Inicie o clock para começar a execução do programa na memória.

## 📂 Estrutura de Arquivos

O projeto foi organizado de forma modular para facilitar o desenvolvimento e o teste.

```
/
├── CPU.dig                   # Circuito principal da CPU integrada
├── ControlUnit.dig           # Unidade de Controle
├── ULA.dig                   # Unidade Lógica e Aritmética
├── ProgramCounter.dig
├── ResettableRegister.dig
├── memory.dig
├── sequenciator.dig
├── 8bit_adder.dig
├── 8bit_subtractor.dig
├── multiplier.dig
├── divisor.dig
├── ... (demais componentes)
```
