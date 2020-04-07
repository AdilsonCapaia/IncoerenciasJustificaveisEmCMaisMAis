# Incoerências Justificaveis Em C++
Reflexões sobre algumas Incoerências e armadilhas em C++, e tentativas de clarifica-las ou justifica-las

## Índice
  - ## [Termos e Significações](#TS)
  - ## [Problemáticas](#P)
  - ## [1 Incoerências entre declaraões/definições de ponteiros que apontam para *arrays e/ou funções*](#IDAF)
    -   ### [1.1 Breve Introdução de Ponteiros](#BIP)
           - #### [1.1.1 Declarações de Ponteiros normais](#DPN)
    -   ### [1.2 Breve Introdução a funções](#BIF) 
           - #### [1.2.1 Declarações de Funções normais](#DFN)
           - #### [1.2.2 Declarações de Funções com parâmetros de tipo array ???](#DF-PN-PA)
           - ###  [1.2.3 Declarações de funçoes que retornam arrays ???](#DF-RA)
    -   ### [1.3 Ponteiros que apontam a arrays](#PAA)
           - #### 1.3.1 Incoerência ou  Interpretação intuitiva precipitada ?
           - #### 1.3.1 Forma certa, Justificação/Entendimento do compilador
    -   ### [1.4 Ponteiros que apontam para funções](#PAF)
           - #### 1.4.1 Incoerência ou  Interpretação intuitiva precipitada ?
           - #### 1.4.1 Forma certa, Justificação/Entendimento do compilador
# <a name="P"></a> Problemáticas

Todos sabemos que C++ é uma linguagem complexa, um dos factores que a torna assim são: o comportamento, a sua semântica e a sua sintaxe.
Comportamento e semântica, como programadores não podemos alterar muita coisa, então deixamos para os compiladores. Mas, quanto a sintaxe, visto que é a interface entre o programador e o compilador então acho que ha certos esforços a fazer para tornar a linguagem que nós tanto amamos mais compreensível e flexível para nós mesmo no nosso dia dia.

Como programador, tentando masterizar o C++, encontrei e provavelmente continuarei a encontrar alguns conceitos/sintaxes do C++ um pouco incoerentes e um pouco difícil de absorver. Uma dessas incoerências podem ser vistas no momento de declaração/definição de :
- Ponteiros que apontam para **arrays**
- Ponteiros que apontam para **funções**

Ponteiros que apontam para tipos primitivos como ( int, double, char, etc ...) e objetos (class, struct) são fáceis de absorver porque suas declarações/definições são uniformes, como por exemplo :

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

**tipoDeDado * nomeDaVarivel ;**

Mas, estarias errado se seguisses essa intuiçao/padrão para definir **um ponteiro que aponta para um array**. Tentando imititar O padrão a cima para definir ***intuitivamente*** um **ponteiro que aponta para uma variável de tipo array** poderiamos fazer o seguinte :
```c++
/* 
definiaçao correcta, mas não é um ponteiro que aponta para um array
*/
int* pArr[2]; // "pArr" é um array que pode conter dois elementos de tipo ponteiros de tipo int

```
ou ainda se quiseres mais exemplos, voce poderia dar um exemplo intuitivo da sua forma seguindo o padrão a cima.

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

# <a name="IDAF"></a> 1 Incoerências entre declaraões/definições de ponteiros que apontam para *arrays e/ou funções*

## <a name="BIP"></a> 1.1 Breve Introdução de Ponteiros
Ponteiros são construct ou tipos de objectos que apontam para o endereço de memória de uma outra variável e que permitem manipular o valor contido nesta variável. Isto quer dizer que um ponteiro por si mesmo não tem utilidade, a menos que ele aponta para uma variável. Se uma variável já têm o seu próprio endereço e com o nome da variável pudemos manipular o valor contido dentro, então porquê precisamos de um ponteiro ? Boa questão... Aqui vão as razões
- *Precisamos de ponteiro porque em C++ existem duas formas de passar parâmetros para funções, por valor e por referência, e uma das formas para passar variáveis por referência é através de ponteiros.*
- *Também precisamos de ponteiro porque não existe um outro jeito de passar arrays ou funções como argumento de uma função.*
- *E por último, precisamos de ponteiro porque é único meio que nos permite fazer gestão dinâmica de memória.*
### <a name="DPN"></a> 1.1.1 Declarações de Ponteiros normais
Ja sabemos as necessidades de um ponteiro, mas, como definir e utilizar um ponteiro normal ? A sintaxe para declarar um ponteiro normal é a seguinte *tipo** *nomeDoPonteiro = &nomeDavarievelparaApontar;*
```c++
  // declarao de uma varivel de tipo inteiro
  int numero = 10; 
  int* pi; // declaracao de um ponteiro que pode apontar para uma variavel de tipo int
  pi = &numero; // "pi" agora aponta para a varivel "numero", ou para o endereço da varivel "numero"
  
  //definiçao de um avariavel de tipo texto
  string palavra = "Bom dia";
  // ou declarao e affectaçao de um endereço ao mesmo tempo
  string* outroP = &palavra //declaracao de um ponteiro que pode apontar para uma variavel de tipo string
```
Uma vez definido o ponteiro, e ter lhe dado o endereço de uma variável, dai para frente podemos utilizar este ponteiro, no nosso caso ( *pi* ) como se fosse a nossa variável  ( *numero* ) das seguintes maneiras...
```c++
...
   cout<<numero<<endl; // numero é igual a 10
   cout<<*pi<<endl; // "*pi" diz-se desreferenciar o ponteiro "pi" que sera igual a 10
   // modificar o ponteiro "*pi", mudara tambem o valor da variavel "numero" e vice-versa
   *pi = 20;
   cout<<numero<<endl; // "numero" e igual a 20
   cout<<*pi<<endl; // "*pi" é igual a 20
```
Para modificar ou recuperar o valor da variável que o ponteiro aponta devemos desreferenciar o ponteiro com o asterisco 
**( * )**. Chama-se desreferenciar porque o ponteiro aponta para o endereço da variável. E se utilizamos o ponteiro da maneira a baixo, imprimirá o endereço da variável que o ponteiro aponta, e não o valor contido na variável
```c++
cout<<pi<<endl; // imprimi o endereço da variavel "numero"
```
Dai a obrigatoriadade de utilizar o asterisco **( * )** para modificar ou recuperar o valor da variavel na qual o ponteiro aponta **( \*p )** , e utilizar o nome do ponteiro sozinho quando queremos afectar-lhe o endereço de uma variável 
**( pi = &numero)**. Voilà.

## <a name="BIF"></a> 1.2 Breve Introdução a funções  
Funções são estruturas que nos possibilitam reagrupar uma sequência lógica de instruções que podem ser executadas repetidas vezes com a mesma sequência lógica.
Porqué precisamos de funções ? Precisamos de funções para manter os nossos programas organizados, legível,  e também para facilitar a manutenção dos nossos programas. 

### <a name="DFN"></a> 1.2.1 Declarações de Funções normais
Aqui vão varias formas de definir uma função 

```c++

  // funçao que nao retorna nada, e nao recebe nenhum parametro
  void nada(){ // instruçoes vao aqui }

  // funçao que nao retorna nada, mas recebe um texto para imprimir na saida
  void imprime(string frase){  cout<<frase<<endl; }

  // funçao que retorna  o valor de tipo inteiro, e recebe dois parametros de tipo inteiros
  int soma(int a, int b) { cout<< a + b <<endl; }
  
```

Com os exemplos que vimos em cima, da para deduzir que a sintaxe para definir qualquer tipo de função é a seguinte :

*tipoDeRetorno **nomeDaFunçao** (tipo nomeParemtro1,...) 
{
    //instruções vão aqui
    return …;
}*

Qualquer um pensaria assim intuitivamente, e não os culpo por isto. 

Mais cometerias muitos erros em deduzir que esta sintaxe é suficiente  para definir qualquer tipo de função, por exemplo 
até  funções que retornam arrays, arrays multidimensionais. Ou funções que recebem arrays normais, ou arrays multidimensionais como parâmetros.  Ou mesmo ainda funções que recebem funções como parâmetros e/ou retornam funções. 

Lamento, mas quanto ao comportamento, C++, não é um jogo de adivinha nem de intuição. Tenha Cautela quando quiseres definir funções que retornam e/ou recebem aurgumentos de tipo arrays normais ou multidimensionais. Voltaremos a falar disso nas próximas seções para esclarecer as sintaxes incoerentes mais justificáveis. Também não precisas preocupar-se porque explicar esses problemas e o objectivo deste artigo.

### <a name="DF-PN-PA"></a> 1.2.2 Declarações de Funções com parâmetros de tipo array/lista ???

Funções podem receber argumentos, como vimos na secção anterior. Mas as vezes queremos passar argumentos um pouco mais complexos como lista de valores. É  bem possível fazer isto em C++, a sintaxe é quase a mesma que definir funçoes que 
recebem parâmetros normais, mais o comportamento é  diferente. Aqui vai uma das sintaxe para definir funções que recebem array (lista) como argumentos :

```c++
   // função com parâmetro de tipo ponteiro, e que recebe um argumento de tipo ponteiro/array
  void manipula(double* p){ // faz alguma coisa com o ponteiro}

  // função com parâmetro de tipo lista, e que recebe um argumento de  tipo lista 
  void computa(double li[]){ // faz alguma coisa com a lista}

```
Praticamente as duas funções definidas acima podem ser utilizada para o mesmo propósito. As duas podem receber um argumento do tipo array/lista. Você de estar a se perguntar como é possível ? Antes de responder esta questão  vamos ver um exemplo de como estas funções poderiam ser utilizadas 

```c++
  // definição de uma lista de números decimais, com quatro elementos
  double lista[4] = {3.44 , 1.00 , 0.99 , 3.14}; 

  // chamar a função para manipular os valores da lista
  manipula(lista);

  // chamar a função para computar os valores na lista
  computa(lista);

```
Viu ? as duas funções foram definidas com parâmetros de tipos diferentes, mas foi possível passar-lhes argumento do mesmo tipo no nosso caso passamos uma lista.  Como isto é possível ?  Isto é possível porque na verdade em C++ não é possível copiar uma lista na outra( *lista1 = lista2*). E para resolver este problema o compilador converte qualquer tipo de lista 
em ponteiro.  

Você ainda deve estar a se perguntar, mas em que momento fazemos cópia de uma lista para outra ?  Quando chamamos as nossas funções nos exemplos acima, exactamente na função computa que tem um parâmetro de tipo lista. E quando chamamos a função e lhe passamos um argumento lista

```c++
  // chamar a função para computar os valores na lista
  computa(lista);
```
Sabemos que em C++ tem dois tipos de passagem de parâmetros. Passagem por valor (copia) e passagem por referência. E por defeito passagem de parâmetros e por cópia. Então quando chamamos a nossa função *computa* e lhe passamos a variável 
lista *computa(lista)* o compilador pega na variável lista e tenta copiar na variável *li* (parâmetro da função *computa*. E neste momento onde o compilador detecta uma inconformidade, para resolver o problema, converte o argumento em ponteiro para que tudo corra bem.

Como o compilador converte todos parâmetros de tipo array/lista em ponteiro, por isso é que a função manipula funciona 
muito bem quando passamos a nossa variável lista, porque no fundo todos parâmetros de tipo lista são convertidos em ponteiros, e neste caso o compilador não precisa fazer operação adicional de conversão, por o parâmetro da função já é um ponteiro. 

Aqui foi mais uma explicação das armadilhas do C++. Espero que tenhas entendido este conceito de conversão de lista em ponteiros. Porque voltaremos a falar dela nas próximas sessões.

