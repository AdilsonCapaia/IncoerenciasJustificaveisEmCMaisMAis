# Incoerências Justificaveis Em C++
Reflexões sobre algumas Incoerências e armadilhas em C++, e tentativas de clarifica-las ou justifica-las

## Índice
  - ## [Termos e Significações](#TS)
  - ## [Problemáticas](#P)
  - ## [1 Incoerências entre declaraões/definições de ponteiros que apontam para *arrays e funções*](#IDAF)
    -   ### [1.1 Breve Introdução de Ponteiros](#BIP)
           - #### [1.1.1 Declarações de Ponteiros normais](#DPN)
    -   ### [1.2 Breve Introdução a funções](#BIF) 
           - #### [1.2.1 Declarações de Funções normais](#DFN)
           - #### [1.2.2 Declarações de Funções com parâmetros normais e/ou com  parâmetros de tipo array ???](#DF-PN-PA)
           - ###  [1.2.3 Declarações de funçoes que retornam arrays ???](#DF-RA)
    -   ### [1.3 Ponteiros que apontam a arrays](#PAA)
           - #### 1.3.1 Incoerência ou  Interpretação intuitiva precipitada ?
           - #### 1.3.1 Forma certa, Justificação/Entendimento do compilador
    -   ### [1.4 Ponteiros que apontam para funções](#PAF)
           - #### 1.4.1 Incoerência ou  Interpretação intuitiva precipitada ?
           - #### 1.4.1 Forma certa, Justificação/Entendimento do compilador
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

Mas estarias errado se seguisses essa intuiçao/padrão para definir **um ponteiro que aponta para um array**. Tentando imititar O padrão a cima, intuitivamente definiriamos um **ponteiro que aponta para uma variável de tipo array** da seguinte forma :
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

# <a name="TS"></a> Termos e Significações

| Termo         | Exemplo       | Outros Significados  |
| ------------- |:-------------:| :-----|
| array         | int aLista[]; | lista, vector |
| variavel      | int numero;   | *numero* é nome de uma variável, ou bjeto |
| objeto        | Coisa co;     | *co* é um objeto ou instancia, ou ainda nome de uma variavél |

# <a name="IDAF"></a> 1 Incoerências entre declaraões/definições de ponteiros que apontam para *arrays e funções*

## <a name="BIP"></a> 1.1 Breve Introdução de Ponteiros
Ponteiros são construct ou tipos de objectos que aponta para o endereço de memória de uma outra variável e que permite manipular o valor contido nesta variável. Isto quer dizer que um ponteiro por si mesmo não tem utilidade, a menos que ele aponta para uma variável. Se uma variável já têm o seu próprio endereço e com o nome da variável pudemos mudar o valor contido dentro, então porquê precisamos de um ponteiro? Boa questão... Aqui vão as razoes
- *Precisamos de ponteiro porque em C++ existem duas formas de passar parâmetros para funções, por valor e por referência, e uma das formas para passar variáveis por referência é através de ponteiros.*
- *Também precisamos de ponteiro porque não existe um outro jeito de passar arrays ou funções como argumento de uma função.*
- *E por último, precisamos de ponteiro porque é único meio que nos permite fazer gestão dinâmica de memória.*
### <a name="DPN"></a> 1.1.1 Declarações de Ponteiros normais
Ja sabemos as necessidades de um ponteiro, mas, como definir e utilizar um ponteiro normal ? A sintaxe para declarar um ponteiro normal é a seguinte *tipo** *nomeDoPonteiro = &nomeDavarievelparaApontar;*
```c++
  int numero = 10; 
  int* pi; // delcarao de um ponteiro que aponta para uma variavel de tipo int
  pi = &numero; // "pi" agora aponta para a varivel "numero", ou para o endereço da varivel "numero"
```
Uma vez definido o ponteiro, e ter lhe dado o endereço de uma variável, dai para frente podemos utilizar este ponteiro ( *pi* ) como se fosse a nossa variável  ( *numero* ) das seguintes maneiras...
```c++
...
   cout<<numero<<endl; // numero é igual a 10
   cout<<*pi<<endl; // "*pi" diz-se desreferenciar o ponteiro "pi" que sera igual a 10
   // modificar o ponteiro "*pi", mudara tambem o valor da variavel "numero" e vice-versa
   *pi = 20;
   cout<<numero<<endl; // "numero" e igual a 20
   cout<<*pi<<endl; // "*pi" é igual a 20
```
Para modificar ou recuperar o valor da variável que o ponteiro aponta devemos desreferenciar o ponteiro com o asterisco **( * )**. Chama-se desreferenciar porque o ponteiro aponta para o endereço da variável. E se utilizamos o ponteiro da maneira a baixo, imprimirá o endereço da variável que o ponteiro aponta
```c++
cout<<pi<<endl; // imprimi o endereço da variavel "numero"
```
Dai a obrigatoriadade de utilizar o asterisco **( * )** para modificar ou recuperar o valor da variavel na qual o ponteiro aponta **( \*p )** , e utilizar o nome do ponteiro sozinho quando queremos afectar-lhe o endereço de uma variável **( pi = &numero)**. Voilà.
