# Incoerências Justificaveis Em C++
Reflexões sobre algumas Incoerências e armadilhas em C++, e tentativas de clarifica-las ou justifica-las

## Índice
  - ## [Problemáticas](#P)
  - ## [1 Incoerências entre declaraões/definições de ponteiros que apontam para *arrays e funções*](#IDAF)
    -   ### [1.1 Breve Introdução de Ponteiros](#BIP)
           - #### [1.1.1 Declarações de Ponteiros normais, e ponteiros que apontam para arrays](#D-PN-PA)
           - #### [1.1.2 Declarações de funçoes que retornam arrays ???](#DFRA) 
    -    ### [1.2 Ponteiros que apontam a arrays](#PAA)
           - #### 1.2.1 Incoerência ou  Interpretação intuitiva precipitada ?
           - #### 1.2.1 Forma certa, Justificação/Entendimento do compilador
    -    ### [1.3 Ponteiros que apontam para funções](#PAF)
           - #### 1.3.1 Incoerência ou  Interpretação intuitiva precipitada ?
           - #### 1.3.1 Forma certa, Justificação/Entendimento do compilador
# <a name="P"></a> Problemáticas

Todos sabemos que C++ é uma linguagem complexa, um dos factores que a torna assim são o comportamento, semântica e a sintaxe.
Comportamento e semântica, como programadores não podemos alterar muita coisa, então deixamos para os compiladores. Mas, quanto a sintaxe, visto que é a interface entre o programador e o compilador então acho que a certos esforço a fazer para tornar a linguagem que nos tanto amamos mais compreensível e flexível para o nosso dia dia.

Como programador, tentando masterizar o C++, encontrei e provavelmente continuarei a encontrar alguns conceitos/sintaxes do C++ um pouco incoerentes e um pouco difícil de absorver. Uma dessas incoerências podem ser vistas no momento de declaração/definição de :
- Ponteiros que apontam para **arrays**
- Ponteiros que apontam para **funções**

Ponteiros que apontam para tipos primitivos ( int, double, char, etc ...) e objetos (class, struct) são fáceis de absorver porque suas declarações/definições são uniformes como por exemplo

```c++
struct Coisa { ... } ;
class AlgumaCoisa { ... };
...
int* pi ;         // "pi" é um ponteiro que aponta para uma variavel de tipo inteiro
Coisa* pC         // "pC" é um ponteiro que aponta para um objeto de tipo Coisa
AlgumaCoisa* pAC; // "pAC" é um  ponteiro que aponta para um objeto de tipo AlgumaCoisa

// abaixo um exemplo correcto, mas com incoerencia de sintaxe
int (*pArr)[3];      // ponteiro que aponta para um array de tres elementos
```
Baseando-se na sintaxe do **pi**, **pC** e **pAC** podemos deduzir intuitivamente a sintaxe para definir qualquer tipo de ponteiro com o seguinte padrão( pattern) :

**tipo * nomeDaVarivel ;**

Mas estarias errado se seguisses essa intuiçao/padrão para definir **um ponteiro que aponta para um array**. Tentando imititar O padrão a cima, intuitivamente definiriamos um **ponteiro que aponta para um uma variável de tipo array/vector** da seguinte forma :
```c++
/* 
definiaçao correcta, mas não um ponteiro que aponta para um array
Intuiçao para definir um ponteiro que aponta para um array
*/
int* pArr[2]; // "pArr" é um array que pode conter dois elementos de tipo ponteiros que apontam para variaveis de tipo inteiros
// ou uma outra sintaxe que conseguires deduzir intuitivamente 
```
Como ja disse a cima e repito estariamos errados se seguissemos essa lógica neste caso. 

Lamento dizer-te isto mas C++ não é sobre intuição, mas sim a cerca de conhecer ela intimamente e ceder a suas grandezas.
Esta é uma das armadilhas e incoerências nas sintaxes do C++ que tornam a compreensão da linguagem um pouco complexa e difícil de masterizar.

Este Artigo/projecto serve para abordar e facilitar a compreensão destas incoerências tentando denuncia-las e ao mesmo tempo justifica-las ou propondo sintaxes mais simples. Neste artigo tentarei fazer-te entender a maior parte das sintaxes obscuras do C++ com teoria e exemplos práticos.

# <a name="IDAF"></a> 1 Incoerências entre declaraões/definições de ponteiros que apontam para *arrays e funções*

## <a name="BIP"></a> 1.1 Breve Introdução de Ponteiros
