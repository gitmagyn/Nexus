# Nexus Programming Language

 <!-- Sugestão: Crie um logo simples para o projeto -->

Bem-vindo ao **Nexus**, uma linguagem de programação dinâmica, compilada para bytecode e executada em uma máquina virtual (VM) própria. Nexus é projetada com foco em simplicidade, desempenho e extensibilidade, sendo especialmente otimizada para tarefas que exigem manipulação de dados de baixo nível, como a criação de *Chess Engines*.

## Filosofia

*   **Simplicidade e Familiaridade:** A sintaxe é inspirada em C e JavaScript, facilitando a aprendizagem para programadores experientes.
*   **Desempenho Onde Importa:** Compilação para bytecode e uma VM em C fornecem um desempenho significativamente melhor do que linguagens interpretadas.
*   **Extensibilidade:** A arquitetura permite a fácil integração de funções C nativas, permitindo que gargalos de desempenho sejam otimizados fora da linguagem.

## Visão Geral da Linguagem (Nexus v1.0)

### 1. Tipos de Dados

Nexus é dinamicamente tipada. As variáveis não têm tipos declarados, mas os valores que elas contêm sim.

*   **Números:**
    *   **Inteiros:** `long long` de 64 bits. Ex: `42`, `-100`.
    *   **Ponto Flutuante:** `double` de 64 bits. Ex: `3.14`.
*   **Booleanos:** `true` e `false`.
*   **Nulo:** `nil`. Representa a ausência de valor.
*   **Strings:** Imutáveis, criadas com aspas duplas. Ex: `"hello, world"`.
*   **Arrays:** Coleções ordenadas de valores. Ex: `[1, "dois", true]`.
*   **Classes e Instâncias:** Suporte básico a programação orientada a objetos.
*   **Mapas:** Coleções de chave-valor (atualmente via função nativa `make_map`).

### 2. Variáveis

As variáveis são declaradas com `var` e as constantes com `const`. Todas as declarações devem terminar com ponto e vírgula.

```javascript
var x = 10;
const PI = 3.14159;

x = x + 1; // Válido
// PI = 3; // Inválido, causará um erro
```

### 3. Estruturas de Controle

*   **Condicionais `if/else if/else`:**
    ```javascript
    if (x > 10) {
        print("Maior que 10");
    } else if (x == 10) {
        print("Igual a 10");
    } else {
        print("Menor que 10");
    }
    ```
    **Nota:** Operadores lógicos como `&&` e `||` ainda não são suportados. Use `if`s aninhados.

*   **Loop `while`:**
    ```javascript
    var i = 0;
    while (i < 5) {
        print(i);
        i = i + 1;
    }
    ```
    **Nota:** Palavras-chave como `break` e `continue` ainda não são suportadas. Controle o fluxo com variáveis de flag.

### 4. Operadores

Nexus suporta um rico conjunto de operadores:

*   **Aritméticos:** `+`, `-`, `*`, `/`
*   **Comparação:** `>`, `<`, `==`, `!=`, `>=`, `<=`
*   **Bitwise:** `&` (AND), `|` (OR), `^` (XOR), `~` (NOT), `<<` (Left Shift), `>>` (Right Shift)
*   **Lógicos:** `!` (Negação)

### 5. Funções

Funções são cidadãos de primeira classe, declaradas com a palavra-chave `func`.

```javascript
func soma(a, b) {
    return a + b;
}

var resultado = soma(5, 3);
print(resultado); // Imprime 8
```

### 6. Strings

Strings suportam a propriedade `.length` e indexação por colchetes `[]`, que retorna o valor ASCII do caractere.

```javascript
var texto = "Nexus";
print(texto.length); // Imprime 5

var primeiro_char_ascii = texto[0];
print(primeiro_char_ascii); // Imprime 78 (código ASCII de 'N')
```

### 7. Arrays

Arrays são criados com literais `[]` e suportam indexação para leitura e escrita.

```javascript
var meu_array = [10, 20, 30];
print(meu_array[1]); // Imprime 20

meu_array[1] = 25;
print(meu_array[1]); // Imprime 25
```

### 8. Funções Nativas

Nexus vem com um conjunto de funções nativas úteis:
*   `print(...)`: Imprime um valor seguido por uma nova linha.
*   `print_uci(...)`: Imprime um valor sem adicionar uma nova linha (útil para protocolos como UCI).
*   `input()`: Lê uma linha de texto da entrada padrão e a retorna como uma string.
*   `clock()`: Retorna o tempo de execução do programa em segundos.
*   `make_map(key1, val1, key2, val2, ...)`: Cria um objeto do tipo Mapa.

### 9. Pré-processador

Nexus suporta uma diretiva `!include` para incluir o conteúdo de outro arquivo, permitindo a modularização do código.

```javascript
// Em main.nx
!include "utils.nx"

// ... código que usa funções de utils.nx
```

## Como Executar

### Executando um Códigos Nexus

1.  **Executar diretamente:**
    ```bash
    ./nexus main.nx
    ```
    Executa diretamente o código. 

2.  **Compilar para Bytecode:**
    ```bash
    ./nexus -b main.nx
    ```
    Isso gerará um arquivo `main.nxvm`.

    Ele conterá o bytecode que Nexus executará.

    É útil para velocidade e distribuição de programas.

3.  **Executar Bytecode:**
    ```bash
    ./nexus -vm main.nxvm
    ```
    Executa o bytecode gerado pelo comando 2.

## O que é a NVM (Nexus Virtual Machine)

A NVM é o coração da linguagem Nexus. É um programa em C de alto desempenho responsável por executar o código Nexus. Em vez de interpretar o código-fonte diretamente (o que seria lento), a NVM executa um conjunto de instruções de baixo nível chamado **bytecode**, que é gerado pelo compilador Nexus.

Essa arquitetura de duas fases (compilação -> execução) permite que a Nexus alcance um desempenho muito superior ao de linguagens puramente interpretadas.

### Arquitetura Principal: Máquina de Pilha

A NVM é uma **máquina de pilha** (*stack-based*). Isso significa que a maioria das operações é realizada em uma estrutura de dados LIFO (Last-In, First-Out) chamada "pilha de valores".

O funcionamento é simples e eficiente. Para executar uma expressão como `2 + 3`, a VM segue estes passos:
1.  O compilador traduz `2 + 3` em uma sequência de instruções de bytecode.
2.  A NVM lê a primeira instrução, que diz para **empilhar** (`push`) o valor `2`.
3.  Lê a segunda instrução, que diz para **empilhar** (`push`) o valor `3`.
4.  Lê a terceira instrução, `OP_ADD`, que diz para:
    a.  **Desempilhar** (`pop`) os dois valores do topo da pilha (3 e 2).
    b.  Somá-los.
    c.  **Empilhar** (`push`) o resultado (`5`) de volta na pilha.

O valor no topo da pilha é sempre o resultado da última expressão avaliada.

### O Ciclo de Execução

O núcleo da NVM é um loop infinito que realiza o ciclo "Fetch-Decode-Execute":
1.  **Fetch (Buscar):** A VM lê o próximo byte de instrução (opcode) do código.
2.  **Decode (Decodificar):** A VM usa uma grande estrutura `switch` para determinar qual ação corresponde àquele opcode.
3.  **Execute (Executar):** A VM executa a ação, que pode ser empilhar um valor, realizar uma operação matemática, pular para outra parte do código ou chamar uma função.

Este ciclo se repete até que a instrução `OP_RETURN` do script principal seja encontrada.

### Componentes Chave da NVM

A NVM é composta por várias estruturas de dados importantes que trabalham em conjunto:

*   **Chunks de Bytecode:** O código-fonte Nexus não é executado diretamente. O compilador o traduz para um ou mais "Chunks". Cada `Chunk` contém:
    *   O array de instruções de **bytecode**.
    *   Um array de **números de linha** correspondentes a cada instrução (para relatar erros em tempo de execução).
    *   Uma **Tabela de Constantes**.

*   **Tabela de Constantes (Constant Pool):** Para manter o bytecode compacto e rápido, valores que não mudam (como números, strings, ou até mesmo definições de funções) não são embutidos diretamente no bytecode. Em vez disso, eles são armazenados em uma tabela dentro do Chunk. O bytecode então contém apenas um pequeno índice (`OP_CONSTANT 2`) que aponta para o valor na tabela. Com as últimas correções, a Nexus suporta tanto índices de 1 byte (`OP_CONSTANT`) quanto de 2 bytes (`OP_CONSTANT_LONG`), permitindo até 65.536 constantes por chunk.

*   **Pilha de Valores (Value Stack):** A pilha principal onde todas as operações e valores locais são armazenados.

*   **Pilha de Chamadas (Call Stack):** Para suportar chamadas de função e recursão, a NVM mantém uma pilha separada de `CallFrame`s. Cada vez que uma função é chamada, um novo `CallFrame` é empilhado, contendo:
    *   Um ponteiro para a função que está sendo executada.
    *   O "ponteiro de instrução" (`ip`) que aponta para a próxima instrução de bytecode a ser executada.
    *   Um ponteiro para a base da pilha de valores, indicando onde os valores locais daquela função começam.

*   **Tabela de Globais:** Variáveis globais são armazenadas em uma tabela hash separada, permitindo acesso e modificação a partir de qualquer lugar do código.

### Gerenciamento de Memória

A NVM gerencia a alocação e desalocação de objetos (como strings, funções, classes, etc.) automaticamente. Todos os objetos criados são rastreados em uma lista encadeada. Ao final da execução do programa, a NVM percorre essa lista e libera toda a memória alocada, prevenindo vazamentos de memória (*memory leaks*).

### Diagrama de Fluxo

Um resumo visual do processo:

```
+--------------+    +------------+    +-----------+    +------------+
| CÓDIGO-FONTE | -> | COMPILADOR | -> | BYTECODE  | -> |    NVM     |
|  (main.nx)   |    |    (C)     |    | (Opcodes) |    | (Execução) |
+--------------+    +------------+    +-----------+    +------------+
```


## O Futuro do Nexus (Roadmap)

Nexus é um projeto em evolução. Futuramente conterá JIT e outras coisas essenciais para uma Linguagem de Programação completa, mas nunca saindo do principal objetivo: a criação de `Chess Engines`.

## Licença

Este projeto é distribuído sob a Licença de Uso.

## Código de Exemplo com as Funções

```javascript
// =================
//       Nexus
// =================

// --- Variáveis, Constantes e Tipos de Dados ---
const ANO_ATUAL = 2025;
var nome = "User";
var idade = 0;
var is_member = false;
var permision = nil;

// Arrays podem conter tipos mistos
var dados = ["Nexus v1.0", ANO_ATUAL, true];


// --- Funções ---

// Uma função que recebe um nome e retorna uma saudação
func criar_saudacao(nome) {
    var saudacao = "Ola, ";
    return saudacao + nome + "!";
}

// Uma função que demonstra loops e operadores
func contagem_regressiva(inicio) {
    print("Contagem regressiva...");
    var i = inicio;
    while (i > 0) {
        print(i);
        
        // Demonstração de if/else e operadores bitwise
        if ((i & 1) == 0) {
            print_uci(" (par)");
        } else {
            print_uci(" (impar)");
        }
        print("");
        
        i = i - 1;
    }
    print("Fim!");
}


// --- Classes e Instâncias ---
class Usuario {
    // (Em versões futuras, métodos seriam definidos aqui)
}

func user_create(nome, idade) {
    var newUser = Usuario(); // Cria uma instância da classe
    newUser.nome = nome;    // Define propriedades
    newUser.idade = idade;
    newUser.nascimento = ANO_ATUAL - idade;
    return newUser;
}


// --- Execução Principal ---

print("===== Bem-vindo ao Nexus =====");

// Usando a função nativa input()
print_uci("Por favor, digite seu nome: ");
user_name = input();

var saudacao_p = criar_saudacao(user_name);
print(saudacao_p);

// Demonstração de string.length e indexação (retorna valor ASCII)
var tam_nome = user_name.length;
print("Seu nome tem " + tam_nome + " caracteres.");
if (tam_nome > 0) {
    print("A primeira letra do seu nome (ASCII) e: " + user_name[0]);
}

// Demonstração de loop
contagem_regressiva(3);

// Demonstração de instância de classe
var user_obj = user_create(user_name, 30);
print("Objeto de usuario criado:");
print(" - Nome: " + user_obj.nome);
print(" - Ano de Nascimento (aprox.): " + user_obj.nascimento);

print("==============================");
```
