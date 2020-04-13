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
           - #### [1.2.3 Funções que retornam arrays normais](#FRA)
    -   ### [1.4 Ponteiros que apontam para arrays multidimensional](#PAA)
           - #### 1.4.1 Incoerência ou  Interpretação intuitiva precipitada ?
           - #### 1.4.2 Forma certa, Justificação/Entendimento do compilador
    -   ### [1.5 Ponteiros que apontam para funções](#PAF)
           - #### 1.5.1 Incoerência ou  Interpretação intuitiva precipitada ?
           - #### 1.5.2 Forma certa, Justificação/Entendimento do compilador
    -   ### [1.6 Funções com parâmetros de tipo funções](#FPF)
           - #### 1.6.1 Incoerência ou  Interpretação intuitiva precipitada ?
           - #### 1.6.2 Forma certa, Justificação/Entendimento do compilador
    -   ### [1.7 Funções que retornam arrays multidimensionais](#FRAM)
           - #### 1.7.1 Incoerência ou  Interpretação intuitiva precipitada ?
           - #### 1.7.2 Forma certa, Justificação/Entendimento do compilador
    -   ### [1.8 Funções que retornam funções](#FRF)
           - #### 1.8.1 Incoerência ou  Interpretação intuitiva precipitada ?
           - #### 1.8.2 Forma certa, Justificação/Entendimento do compilador
# <a name="P"></a> Problemáticas

Todos sabemos que C++ é uma linguagem complexa, um dos factores que a torna assim são: o comportamento, a sua semântica e a sua sintaxe.
Comportamento e semântica, como programadores não podemos alterar muita coisa, então deixamos para os compiladores. Mas, quanto a sintaxe, visto que é a interface entre o programador e o compilador então acho que ha certos esforços a fazer para tornar a linguagem que nós tanto amamos mais compreensível e flexível para nós mesmo no nosso dia dia.

Como programador, tentando masterizar o C++, encontrei e provavelmente continuarei a encontrar alguns conceitos/sintaxes do C++ um pouco incoerentes e um pouco difícil de absorver. Uma dessas incoerências podem ser vistas no momento de declaração/definição de :
- Ponteiros que apontam para **arrays**
- Ponteiros que apontam para **funções**

Neste artigo utilizarei alternativamente as palavras array, lista ou vector para se referir a mesma coisa, um vector unidimensional, exemplo : int li[2] = {2,4};

Também utilizarei alternativamente as palavras declaração e definição para se referir a mesma coisa.

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
| Declaração    | int c;        |  *c* foi declarado mas nao definido|
| Definição     | int a = 10    | a, foi definido, ou declarado |
 
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
Aqui vão várias formas de definir uma função 

```c++

  // funçao que nao retorna nada, e nao recebe nenhum parametro
  void nada(){ // instruçoes vao aqui }

  // funçao que nao retorna nada, mas recebe um texto para imprimir na saida
  void imprime(string frase){  cout<<frase<<endl; }

  // funçao que retorna  um valor de tipo inteiro, e recebe dois parametros de tipo inteiro
  int soma(int a, int b) { cout<< a + b <<endl; }
  
```

Com os exemplos que vimos em cima, da para deduzir que a sintaxe para definir qualquer tipo de função é a seguinte :

*tipoDeRetorno **nomeDaFunçao** (tipoDeDado nomeParemtro1,...) 
{
    //instruções vão aqui
    return …;
}*

Qualquer um pensaria assim intuitivamente, e não os culpo por isto. 

Mais cometerias muitos erros em deduzir que esta sintaxe é suficiente  para definir qualquer tipo de função. Por exemplo funções que retornam arrays, arrays multidimensionais, ou funções que recebem arrays normais, ou arrays multidimensionais como parâmetros,  ou mesmo ainda funções que recebem funções como parâmetros e/ou retornam funções não funcionaria exactamente com esta sintaxe. Esta sintaxe precisa um pouco de adaptação.

Lamento, mas quanto ao comportamento, C++, não é um jogo de adivinha nem de intuição. Apenas tenha Cautela quando quiseres definir funções que retornam e/ou recebem aurgumentos de tipo arrays normais ou multidimensionais. Voltaremos a falar disso nas próximas seções para esclarecer as sintaxes incoerentes mas justificáveis de funções que retornam e/ou recebem aurgumentos de tipo arrays. Também não precisas preocupar-se porque explicar esses problemas é o objectivo deste artigo.

### <a name="DF-PN-PA"></a> 1.2.2 Declarações de Funções com parâmetros de tipo array/lista ???

Funções podem receber argumentos como vimos na secção anterior. Mas as vezes queremos passar argumentos um pouco mais complexos como por exemplo **lista de valores**. É  bem possível fazer isto em C++, a sintaxe é semelhante a definiçoes de funçoes que recebem parâmetros normais, mais o comportamento é  diferente. Aqui vai uma das sintaxes para definir funções que recebem array (lista) como argumentos :

```c++
   // função com parâmetro de tipo ponteiro, e que recebe um argumento de tipo ponteiro
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
Viu ? as duas funções foram definidas com parâmetros de tipos diferentes, mas foi possível passar-lhes argumento do mesmo tipo, no nosso caso passamos uma lista.  Como isto é possível ?  Isto é possível porque na verdade em C++ não é possível copiar uma lista na outra( *lista1 = lista2*). E para resolver este problema o compilador converte qualquer tipo de lista 
em ponteiro ( no contexto de uma função).  

Você ainda deve estar a se perguntar, mas em que momento fizemos cópia de uma lista para outra ?  Quando chamamos as nossas funções nos exemplos acima, exactamente na função computa que tem um parâmetro de tipo lista, quando chamamos a função e lhe passamos um argumento de tipo lista/array *lista*

```c++
  // chamar a função para computar os valores na lista
  computa(lista);
```
Sabemos que em C++ existe dois tipos de passagem de parâmetros. Passagem por valor (copia) e passagem por referência. E por defeito passagem de parâmetros é por cópia. Então quando chamamos a nossa função *computa* e lhe passamos a variável 
lista, exemplo : *computa(lista)* o compilador pega na variável lista e tenta copiar na variável *li* (parâmetro da função *computa*). É neste momento onde o compilador detecta uma inconformidade, porque não é possivel copiar uma lista na outra. Para resolver o problema, o compilador converte o argumento em ponteiro para que tudo corra bem. 

Como o compilador converte todos parâmetros de tipo array/lista em ponteiro, por isso é que a função manipula funciona 
muito bem quando passamos a nossa variável lista, porque no fundo todos parâmetros de tipo lista são convertidos em ponteiros, e neste caso o compilador não precisa fazer operação adicional de conversão, porque o parâmetro da função *manipula* já é um ponteiro ( *manipula(int\* p)* ). 

Dito isto vale apena também acrescentar, que como parâmetros de tipo array/lista não podem ser copiados, então a única forma de passar argumentos de tipo arrays/listas para funções é por intermédio de ponteiro ou referência.

Uma vez que sabemos que arrays sao convertidos em ponteiros quando se trata de uma função, ai vem a questão, como devemos utilizar um argumento de tipo lista dentro da função ? Como ponteiro ou como array/lista ?  Esta é uma boa questão ! Existem pelomenos duas formas de como podemos utilizar dentro de uma função  um lista passada como argumento. Da forma que definimos as nossas funções *manipula* e *computa* não é muito prático trabalhar com listas, porque dentro das funções nao temos noção do tamanho da lista passado como argumento, ou melhor, não sabemos quantos elementos a lista contém. Então as duas opções mais simples são :

- **1° Opção :** Passar o tamanho da lista como argumento extra nas funções *manipula* e *computa*
```c++

  void manipula(double* p, int tamanho)
  { 
    // adicionar 1 a cada elemento da lista
    for(int i = 0; i < tamanho; i++){
        p[i] = p[i] + 1;
    }
  }

  void computa(double li[], int tamanho)
  { 
      // imprimir todos elementos da lista
       for(int i = 0; i < tamanho; i++){
        cout<< li[i] <<endl;
    }
  }
  
int main()
{
  // definição de uma lista de números decimais, com quatro elementos
  double lista[4] = {3.44 , 1.00 , 0.99 , 3.14}; 

  // chamar a função para manipular os valores da lista
  manipula(lista, 4);

  // chamar a função para computar os valores na lista
  computa(lista, 4);
  return 0;
}
  
```
Nos exemplos a cima dá para notar que um ponteiro quando aponta para uma lista se comporta como uma lista normal, e que podemos obter cada elemento da lista utilizando o operador "[]" e a posição em questão ( *[poiscao]* ).

- **2° Opção :** Em vez de passar um ponteiro, passar dois ponteiros como argumentos, um que aponta no primeiro elemento da lista e outro que aponta para o ultimo elemento da lista ( ou melhor, que aponta para o endereço a seguir do ultimo elemento da lista)

Esta opção aplica-se apenas para funções que definem ponteiros como parâmetros, no nosso caso a função manipula
```c++
  void manipula(double* inicio, double* fin)
  { 
    // adicionar 1 a cada elemento da lista
    for(auto pCorrente = inicio; pCorrente != fin; pCorrente++)
    {
        *pCorrente = *pCorrente + 1;
    }
  }
  
int main()
{
  // definição de uma lista de números decimais, com quatro elementos
  double lista[4] = {3.44 , 1.00 , 0.99 , 3.14}; 

  // chamar a função para manipular os valores da lista
  /*
    função begin retorna um ponteiro que aponta no primeiro elemento da lista
    função end retorna um ponteiro que aponta no elemento asseguir do ultimo elemento da lista
  */
  manipula(std::begin(lista) , std::end(lista));  
  
  return 0;
}
```
Qual opção é a mais simples ou facil de absorver, a resposta fica à teu critério :) 

### <a name="FRA"></a> 1.2.3 Funções que retornam arrays normais

Nas duas seções anteriores vimos 80% do comportamento essencial de funções, então espero que nesta secção não haja surpresa.  
Em alguns programas que escreverás enquanto programador, por vezes precisarás passar uma lista como argumento de uma função para manipular os elementos dentro da lista, e por qualquer razão desconhecida necessitarás de retornar esta lista através da função. A questão é,  como fazer uma função retornar uma lista/array ?

Bem, retornar uma variável é como se fosse passar um argumento para uma função, ou é por valor(copia) ou por referência. Também sabemos que arrays não podem ser copiados. Podemos logo concluir que não podemos fazer uma funçao retornar uma lista de tipo array ( *[]* ), mas não fica triste. Também sabemos que funções podem retornar ponteiros, e que arrays podem ser, e são convertidos para ponteiros quando nos encontramos no contexto de uma função. Isto nos leva a reavaliar a nossa conclusão anterior,  e concluir que na verdade podemos fazer uma função retornar uma lista, só devemos nos lembrar que a lista será convertida em ponteiro.  

Para definir uma função que retorna uma lista de tipo array ( *[]* ) , precisamos definir o retorno desta função como um ponteiro, como ilustrado abaixo :

```c++
int* transforma(int arr[])
{
    // faça algo com a lista “arr”
    // retorna a lista
    return arr;
}

int main()
{
    int lista[3] = {3, 5, 7};
    int* p = transforma(lista, 3);
    // imprimir a lista retornada por intermedio de "p"
    for( int i = 0; i < 3; i++)
        cout<< p[i]<<endl;
    return 0;
}
```
Espero que estejas mais confortável com o exemplo acima. Quando chamamos a função  *transforma*, sabemos que ela deve retornar uma lista, mas também não devemos esquecer que esta lista/array será convertido em um ponteiro do mesmo tipo, por isso é necessário que a variável que receberá o retorno seja um ponteiro, no nosso caso o *p*.  Atravez do nosso exemplo acima também dá para observar que depois de recebermos a lista convertida em ponteiro podemos utilizá-la como se fosse um ponteiro ou como se fosse uma lista normal, recuperando os elementos dentro da lista com o operador ( *[]* ).
Se você é como eu, curioso, então deves estar a se perguntar… Como é possível utilizar um ponteiro como se fosse uma lista ? Existem duas razões que permitem um ponteiro se comportar como uma lista : aqui vão elas

 - **1° Razão** : Quando um ponteiro aponta para o primeiro elemento de uma lista de tipo array ( *[]* )
```c++
  double arr[] = { 00.4, 0.99, 1.55};
  double* pd = arr // automaticamente “pd” aponta para o primeiro elemento da lista “arr”
  double* pd2 = &arr[0]; // mesma situação que “pd”, “pd2” aponta para o primeiro elemento da lista arr
```
 - **2° Razão** : Quando há uma conversão de array para ponteiro, no contexto de uma função ( parâmetro ou retorno )


Aqui está a demonstração de como podemos fazer uma função retornar uma lista de tipo array por intermédio de um ponteiro

Aqui foi mais uma explicação das armadilhas do C++. Espero que tenhas entendido este conceito de conversão de lista em ponteiros. Porque voltaremos a falar dela nas próximas sessões.

## <a name="PAA"></a> 1.4 Ponteiros que apontam para arrays multidimensional
Um ponteiro normal é diferente de um ponteiro que aponta para um array multidimensional, mais conhecido como matrix. 
Um ponteiro normal pode apontar para o endereço de uma variável normal, ou para um tipo de array/lista unidimensional, 
por exemplo ( *listaUD[]* ). E um ponteiro que aponta para uma lista que contém outra lista, este serve para apontar em array/lista multidemensional, por exemplo( *listaMD[][]* ), conhecida como matrix.

Antes de irmos em detalhes analisa o exemplo abaixo :
```c++
 //lista unidemensional 
 int lista[2] = { 3 , 0};
 int *p = lista; // “p” pode apontar para uma variável normal, ou lista unidimensional
 // lista multidemensional 
 int matrix[2][2] = { {2,3}, {4,3} }; // obrigatório especificar a segunda dimensão
```
Como definirias um ponteiro que pode apontar para a nossa lista multidimensional *matrix* ? 

### 1.4.1 Incoerência ou  Interpretação intuitiva precipitada ?
Os dois chamam-se ponteiros mas exigem sintaxes diferentes. Existem pelo menos duas formas para definir um ponteiro que apontam para listas multidimensionais ou seja, para matrix. Uma delas é ponteiro de ponteiros com a sintaxe *tipoDeDado\*\**, 
Outra forma e,  ponteiro para listas com uma sintaxe incoerente igual a esta abaixo *tipoDeDado  (\* nomeDoPonteiro)[ NumeroDeElementos] ;*
Tendo visto a sintaxe acima podemos definir um ponteiro que aponta para nossa lista multidimensional *matrix* definida anteriormente
```c++
  int (*pA)[2] ;
  pA = matrix; // “pA” é um ponteiro que aponta para listas multidimensional
```
Para uma lista multidimensional é obrigatório especificar o número de elementos das dimensões posteriores, no nosso caso a segunda dimensão. Tanto no ponteiro como como na lista multidimensional.
*pA* aponta para o primeiro elemento da lista que contém outras listas *matrix*, ou seja, lista multidimensional. E este primeiro elemento é o elemento que se encontra na posição “0” (zero), que ele mesmo também é uma lista que contém dois elementos {2 , 3}. 
O ponteiro *pA* aponta para a primeira posiçao da nossa *matrix*, e vimos também na secção [Funções que retornam arrays](https://github.com/AdilsonCapaia/IncoerenciasJustificaveisEmCMaisMAis#FRA) que esta afirmação é verídica. Quer dizer que também podemos utilizar a sintaxe a seguir para atribuir um valor ao nosso ponteiro *pA* :
```c++
   int (*pA)[2] = &matrix[0];
```
Apenas lembre-se que quando especificarmos uma posição para o ponteiro apontar, deves utilizar o operador de referência
( **&** ).

As duas sintaxes que acabamos de ver são equivalentes porque quando se trata de ponteiros que vão apontar para lista unidimensionais ou multidimensionais, se não especificarmos uma posição de antemão, automaticamente o compilador posiciona o ponteiro na primeira posição da lista ou  matrix em  questão. Por isso as duas sintaxes  **pA = &matrix[0]** e **pA = matrix** são equivalentes.

Deixando este assunto para lá, eu não sei da tua parte, mas eu tenho muitas questões quanto a sintaxe para definirmos um ponteiro que aponta para uma matrix, por exemplo :
- Porque é necessário meter o nome do ponteiro entre parêntesis ?
- Porque é que o parêntesis recto *[]* fica fora do parêntesis curvo ? 

### 1.4.2 Forma certa, Justificação/Entendimento do compilador
Pôr o nome do ponteiro com o símbolo “\*” (asterisco) é obrigatório, porque se não fizermos desta forma estaríamos a sintaxe para definir um outro tipo de estrutura. Por exemplo se tirarmos o ponteiro dentro do parêntesis curvo como mostrado abaixo : 
```c++
  int* pM[2];
```
estaríamos a definir uma um lista que pode conter dois elementos de tipo ponteiro para inteiro. Daí a necessidade de isolar o asterisco e o ponteiro dentro de parêntesis curvo, desta maneira ele forma uma outra sintaxe e o compilador interpreta como um ponteiro que aponta para uma lista multidimensional.
```c++
  int (*pM)[2];
```
Entendo a tua inquietação, e sei que é uma sintaxe estranha, que só pelo facto de ter aumentado um parêntesis curvo que o comportamento muda drasticamente. 

Para melhor entender esta sintaxe deves ler a sintaxe da seguinte forma. Primeiro começa pelo meio, *\*pM*, da para ver que *pM* é um ponteiro, olhando para fora do parêntesis curvo à direita têm um parêntesis recto *[2]*, isto nos dá mais informação sobre *pM*, dizendo que *pM* aponta para uma lista que contém dois elementos. E olhando para esquerda, temos mais uma informação, o tipo int, isto nos diz que a lista contém elementos do tipo inteiros.

Contudo podemos concluir que  ***pM** é um ponteiro que aponta para uma lista de dois elementos de tipo inteiro* . E não é qualquer tipo de lista, uma lista multidimensional.

Aqui está, mais uma sintaxe obscura do C++ desvendada. Pronto para próxima ?

## <a name="PAF"> 1.5 Ponteiros que apontam para funções 
Nas seções passadas vimos que ponteiros podem apontar para o endereço de uma variável, de um array unidimensional ou multidimensional.  Ponteiros não apontam somente para esses tipos de variáveis, ponteiros também podem apontar para o endereço de uma função ou seja, para uma função. 

Dito isto você deve estar a se perguntar porque precisamos de ponteiros que apontam para funções ?  Bem, o uso de ponteiros que apontam para funções não é muito comum, aqui vai uma das razões que te levariam a usar ponteiros para funções :

- *Dar possibilidades ao utilizador do teu programa de personalizar o programa*
- *Desenvolver bibliotecas ou frameworks*
- *Muitas funcionalidades da biblioteca padrão do C++ fazem a utilização massiva de ponteiros para funções*

É pelas razões acima, que aconselho-te a aprender a conhecer um pouco mais como se utiliza ponteiros para funções. 

Se quiseres ver o que é possível fazer com ponteiros  que apontam para funções, aconselho-te a dar uma olhada num pequeno projeto didático aqui : [Plugins e Delegates Em C++ com ponteiros e funções](https://github.com/AdilsonCapaia/PluginsEmCMaisMais)

Como podemos definir um ponteiro que aponta para uma função ? Na secção [Breve Introdução de Ponteiros](https://github.com/AdilsonCapaia/IncoerenciasJustificaveisEmCMaisMAis#BIP) demos uma sintaxe geral para definição de ponteiros de qualquer tipo, infelizmente vimos que aquela sintaxe não funciona para ponteiros que apontam para arrays e também não funcionará para funções. 

### 1.5.1 Incoerência ou  Interpretação intuitiva precipitada ?
A sintaxe que demos na secção [Breve Introdução de Ponteiros](https://github.com/AdilsonCapaia/IncoerenciasJustificaveisEmCMaisMAis#BIP) era a seguinte : **tipoDeDado \* nomeDaVarivel ;**

Como já disse anteriormente, essa sintaxe não funciona para funções.

Para definir um ponteiro que aponta para uma função, primeiro precisamos saber que tipo de função queremos apontar, só assim podemos declarar um ponteiro com as mesmas características.

Por característica quero dizer, “com o mesmo protótipo que os da função que queremos apontar”.

Mas  porquê não podemos seguir  uma sintaxe geral, porquê temos que copiar o protótipo da função em questão ?

### 1.5.2 Forma certa, Justificação/Entendimento do compilador
Devemos copiar o protótipo da função porque um ponteiro aponta para o endereço de um tipo, variável ou objeto. E o endereço de uma variável é a única forma de encontrar uma variável ou tipo na memória física do computador, e o tipo que a variável (objeto ou função) tém, determinará o espaço e o endereço(lugar) onde ela será guardada. 

Resumindo, o ponteiro precisa ter o mesmo protótipo que a função porque assim o compilador consegue prever que este ponteiro está capacitado para aguentar a quantidade de memória(espaço) necessário para chamar e executar as instruções da função em causa. E também, esta política reforça a segurança entre tipos estáticos ( tipos deduzidos em tempo de compilação)

Supondo que existe uma funçao qualquer definida algures, a sintaxe para definir um ponteiro para apontar para esta função hipotética é a seguinte :
**tipoDeRetornoDaFunçaoParaApontar (\*nomeDoPonteiro)(parametrosDaFunçaoParaApontar);**

É importante notar que a sintaxe acima deve ser respeitada ao pé da letra. Por exemplo, o símbolo **\*** asterisco e o nome do ponteiro devem estar dentro do parêntesis curvos. Se não respeitarmos isso, estaríamos a definir uma função que retorna um ponteiro do tipo especificado no retorno , e não um ponteiro que aponta para uma função, capiche ?

Para definir um ponteiro que aponta para uma função é preciso pelo menos duas etapas :
Supondo que temos uma função  definida do jeito abaixo :
```c++
//1° definir a função
void imprimir(string palavra){ cout<<palavra<<endl; }
```
Podemos definir o ponteiro deste jeito :
```c++
//2° definir o ponteiro e afetar uma função
void (*p)(string );  // “p” pode apontar para uma função que tem um parâmetro de tipo string e retorna void
p = imprimir // “p” aponta para função imprimir
p = &imprimir // mesma coisa que a linha de cima

// utilização do ponteiro 
string texto = "Bom dia";
p(texto); // chama a função imprimir e mostra no ecrã “Bom dia”
```
Lembras quando disse que é necessário por o asterisco e nome do ponteiro dentro de parêntesis curvo ? imagina se no exemplo anterior não respeitássemos esta regra ?
```c++
   void* p(string );
```
Praticamente estaríamos a declarar uma função com o nome **p** que retorna um ponteiro de tipo void.  

Depois de definirmos o ponteiro com o mesmo protótipo da função e termos lhe afetado o endereço da função em questão, daí para frente podemos utilizar o nosso ponteiro como se fosse nossa função. Como se fosse um segundo nome para nossa função.

Nao fica assustado por ter dito que *p = imprimir* et *p = **&** imprimir* são equivalentes. Igualmente como nos arrays, em funções se você nao por o operador de referência **&** antes do nome da função, automaticamente o compilador deduzirá por você.

Para um ponteiro conseguir apontar para o endereço  de uma função os seguintes critérios devem ser respeitados na declaração do ponteiro: 
-  Mesmo tipo de retorno que a função 
-  Mesmo número de parâmetros que a função
-  E, os parâmetros do ponteiro devem ter exatamente os mesmos tipo de dado que os parametros da funcao, sem precisar de conversão

Um vez o ponteiro definido e respeitado o protótipo da função, esse ponteiro pode ser utilizado para apontar para outras funções com o mesmo protótipo ( *mesmo tipo de retorno e mesmo número de parâmetros*), mesmo se o nome da função for diferente. Isto é bem possível porque o nome de uma função não faz parte do protótipo da função, apenas o retorno, e os parâmetros fazem parte do protótipo.

Se para um ponteiro apontar para uma função não fosse necessário ter o mesmo protótipo, como desejarias que o código abaixo se comportasse ?
```c++
// definição de uma função que faz soma de dois números inteiros
int soma(int a, int b){ return a + b; }

// definição de um ponteiro que aponta para função função que retorna uma string 
// Mau exemplo, apenas para ilustração
string (*pStr)(string );
pStr = &soma; // “p” aponta para uma função  que retorna um inteiro
string palavra = "Teste";
pStr(palavra);
```
Se este tipo de código fosse permitido, muita coisa poderia correr mal. Quer dizer que o nosso ponteiro *pStr* passaria um argumento de tipo string para a função na qual pStr aponta (função *soma*), enquanto que a nossa função *soma* esperava dois argumentos de tipo inteiro para fazer a soma e retornar o resultado da soma.  

Por isso e que compilador exige que para definir um ponteiro que aponta para uma função o ponteiro deve ter o mesmo protótipo que a função. 

Aqui está, mais um mistério do C++ desvendado, pronto para o próximo ?

## <a name="FPF"> 1.6 Funções com parâmetros de tipo funções
Na seção [1.2 Breve Introdução a funções](https://github.com/AdilsonCapaia/IncoerenciasJustificaveisEmCMaisMAis/blob/master/README.md#BIF) vimos que funções podem ter nenhum ou vários parâmetros de qualquer tipo permitido pelo C++. Por razões que não aprofundaremos neste documento, por vezes você precisará de passar funções como argumento de uma outra função. E para conseguir passar uma função **X** como argumento de uma outra **Y** é preciso declarar a função **Y** com pelo menos um parâmetro de tipo de uma função ???

Porque precisaremos de funções que recebem funções como argumentos ? Precisaremos de funções que recebem funções como argumentos pelas mesmas razões que nos levariam a precisar de ponteiros que apontam para funções, por exemplo: 

- *Dar possibilidades ao utilizador do teu programa de personalizar o programa*
- *Desenvolver bibliotecas ou frameworks*
- *Muitas funcionalidades da biblioteca padrão do C++ fazem a utilização massiva de passagem funções para funções*

Se quiseres ver o que é possível fazer com funções  que apontam para funções, aconselho-te a dar uma olhada num pequeno projeto didático aqui : [Plugins e Delegates Em C++ com ponteiros e funções](https://github.com/AdilsonCapaia/PluginsEmCMaisMais)

Como podemos definir uma função com parâmetro de tipo função ?

### 1.6.1 Incoerência ou  Interpretação intuitiva precipitada ?
Se você leu as seções [1.4 Ponteiros que apontam para arrays multidimensional](https://github.com/AdilsonCapaia/IncoerenciasJustificaveisEmCMaisMAis/blob/master/README.md#PAA)  e [1.5 Ponteiros que apontam para funções](https://github.com/AdilsonCapaia/IncoerenciasJustificaveisEmCMaisMAis/blob/master/README.md#PAF)  então você perceberá a sintaxe de parâmetros de tipo funções.

Na verdade C++ nao consegue propriamente definir uma função que recebe função como argumento, porque se bem te lembras, existem dois tipos de passagem de parâmetros para funções, passagem por valor(copia) e por referência, por defeito passagem de parâmetros é feito por cópia, e C++ não permite a cópia de uma função para outra. Isto quer dizer que nao podemos passar uma função como argumento de uma outra função ? Sim e não, mas estamos a falar de C++ têm sempre um jeito de se virar, basta conhecer bem o compilador. 

Não podemos copiar uma função à outra mas podemos fazer um ponteiro apontar para uma função, e através deste ponteiro, podemos utilizar o ponteiro como se fosse a nossa função.

Existem duas sintaxes para definir um parâmetro  que pode ter o tipo de uma função. E as duas são totalmente equivalentes. 
- A primeira é simplesmente definir o teu parâmetro como se fosse uma função :

*funçaoPrincipal(parametro1,..., **retornoDaFunçaoParametro nomeDaFuncaoParametro(parametros…)** );*
- A segunda sintaxe é definir o parâmetro da função como se fosse um ponteiro que aponta para função :
*funçaoPrincipal(parametro1,..., **retornoDaFunçaoParametro (\*nomeDaFuncaoParametro)(parametros…)** );*
é obrigatório por o símbolo **\*** asterisco e o nome do ponteiro entre parêntesis.

Não estou a ver a tua cara, mas deves estar a pensar, que aberração e  esta ?

Com as duas sintaxes acima podemos definir uma função que recebe uma outra função como argumento da seguinte maneira :
```c++
void mostrar(string algo){ cout<<algo<<endl; }

// função que tem um parâmetro de tipo função
void imprime(string conteudo, void imp(string) )
{  
  // chamar a nossa função que foi passada como argumento que agora se chama "imp" e lhe passamos o parametro conteudo
  imp(conteudo);
}

int main()
{
    string meuTexto = "Boa Noite";
    // Chamar a função imprimir, e lhe passar o primeiro argumento de string e uma função como segundo argumento , no nosso caso função "mostrar"
    imprime(meuTexto, mostrar);
}
```
Também podemos escrever o código acima com a segunda sintaxe , em vez de uma função normal,  o parâmetro tem de ser um ponteiro que aponta para uma função:
```c++
void mostrar(string algo){ cout<<algo<<endl; }
// função que têm um parâmetro de tipo função
void imprime( string conteudo,void (*imp)(string) )
{  
  // chamar a nossa função que foi passada como argumento que agora se chama "imp" e lhe passamos o parametro conteudo
  imp(conteudo);
}

int main()
{
    string meuTexto = "Boa Noite";
    // Chamar a função imprimir, e lhe passar uma função como argumento, no nosso caso função "mostrar"
    imprime(meuTexto, mostrar );
}
```
 
### 1.6.2 Forma certa, Justificação/Entendimento do compilador
Os exemplos acima estão corretos, e respeitaram as regras das sintaxes permitidas. Mas como é que as duas funcionam como desejado ? Como é que as duas sintaxes são equivalentes ?

Lembra o que disse anteriormente ?, não é possível copiar uma função na outra, mas é possível apontar para o endereço de uma função e utilizar esse ponteiro como se fosse a nossa função. é o que está acontecer nos dois exemplos acima. 

Quando definimos a função : *void imprime( string conteudo, void (\*imp)(string) )* devemos interpretar ou ler da seguinte forma :

*imprime* é uma função, se olharmos para esquerda vemos o *void* isso nos diz que a função *imprime* retorna nada. A seguir vamos para direita dentro do parênteses onde se encontra os parâmetros da função,  *conteudo* e *imprime*, e vemos logo que o segundo parâmetro da função *imprime* tem o nome de *imp*, e se olharmos a esquerda desse nome dá para ver o símbolo  **\*** asterisco isso nos diz que o parâmetro *imp* é um ponteiro, se olharmos para a direita do *imp* da para notar que *imp* aponta para uma função que têm uma parâmetro do tipo string, e se olharmos a esquerda do *imp* notamos que a função na qual o *imp* aponta tém um retorno de tipo void, então não retorna nada.

Isto quer dizer simplesmente que *imprime* é uma função que têm dois parâmetros um do tipo string, e outro de tipo ponteiro que aponta para uma função.

E quando utilizamos esta sintaxe : *void imprime(string conteudo, void imp(string) )* simplesmente quer dizer que *imprime* é uma função que têm dois parâmetros um do tipo string, e outro de tipo função mas que na verdade o compilador converte em ponteiro que aponta para uma função, ou seja igual a sintaxe estudada no parágrafo anteriormente.

O compilador converte qualquer parâmetro de tipo função em ponteiro para função porque ele não pode copiar uma função na outra.  Como o compilador converte todo parâmetro de tipo função como ponteiro para função, é por esta razão que *void imprime( string conteudo,void imp(string) )*  e  *void imprime(string conteudo, void (\*imp)(string )* são equivalentes. Porque se o compilador não fizesse a conversão entre função normal, para ponteiro para função então quando chamássemos a nossa função imprime deste jeito :
```c++
 imprime(meuTexto, mostrar);
```
o compilador detetaria um erro, porque estaríamos a dizer ao compilador para copiar a função *mostrar* ao parâmetro *imp* da função *imprime* . Entao Como nao podemos copiar uma função na outra, o compilador transforma *imp* em ponteiro e depois lhe afeta o endereço da função mostrar. O nosso querido amigo compilador só faria esta conversão se o parâmetro no nosso caso *imp* não fosse ponteiro para função.

O quê que acontece exatamente quando chamamos a função *imprime* como neste caso : *imprime(meuTexto, mostrar);*  ?. 

Sabemos que em C++ quando estamos a chamar uma função devemos lhe fornecer os argumentos necessário exigido pela função. Como a função *imprime* exige dois parâmetros um do tipo string, e outro parâmetro de tipo função, então devemos lhe passar uma string e uma função quando chamamos ela, por isso lhe passamos a variável *meuTexto* e a função *mostrar*. E como a função *mostrar* também é uma função que exige parâmetro de tipo string, então dentro da função *imprime* para lhe chamar precisamos lhe passar um argumento, no nosso caso lhe passamos a variável de tipo string chamada *conteudo* que por sua vez contém o valor da variável *meuTexto* que foi passada através da função *imprime*.

Espero que tenhas absorvido mais uma sintaxe obscura do C++, Se chegaste até aqui, considero-te uma campeão. Mas se queres ser campeão dos campeões aconselho-te a continuar com as próximas seções. Pronto ?

## <a name="FRAM"></a> 1.7 Funções que retornam arrays multidimensionais  
Sabemos que funções podem retornar valores de tipo void ( quando nao retorna nada) ou um outro tipo permitido pela linguagem. Por razões desconhecidas neste momento, em algum momento ou projeto você precisará que uma função retorne um array multidimensional. A questão é, qual é a sintaxe para definir uma função que pode retornar um array multidimensional ?

Como o retorno de uma função é feito por cópia, e como não podemos copiar um array no outro, é por esta razão que uma função não pode retornar exatamente um array multidimensional. 

### 1.7.1 Incoerência ou  Interpretação intuitiva precipitada ?
Sabemos a sintaxe para declarar/definir qualquer tipo de função
*tipoRetorno nomeDaFunçao(tipo1 parametro1,...);*

Então, seguindo a sintaxe acima, podemos tentar definir uma função que retorna uma variável de tipo array/lista multidimensional, não achas o mesmo ? intuitivamente seria assim :
```c++
int matrix[2][3]= {{2,4,0},{8,5,20}}; 
// codigo a baixo apenas para ilustração, não é válido em C++
int[][] retornaLista(){  return matrix; }
```
O exemplo acima não é um exemplo válido em C++ para definir uma função que retorna uma lista multidimensional. Isto quer dizer que a sintaxe geral de definição de uma função não funciona para declarar/definir uma função que retorna um array multidimensional.  

O que nos leva na seguinte questão, então qual é a verdadeira sintaxe para declarar/definir uma função que pode retornar um array multidimensional  ?

### 1.7.2 Forma certa, Justificação/Entendimento do compilador
Se você leu a seção [1.2.3 Funções que retornam arrays](https://github.com/AdilsonCapaia/IncoerenciasJustificaveisEmCMaisMAis/blob/master/README.md#FRA) você também viu que funções não podem retornar arrays normais, para o fazer foi preciso definir o retorno da função como um ponteiro. O mesmo deve ser feito com uma função que deve retornar arrays multidimensionais, mas com uma particularidade! qual ? Devemos definir o retorno da  função como um ponteiro que aponta para arrays multidimensionais e não como um ponteiro normal.

Se você leu a seção [1.4 Ponteiros que apontam para arrays multidimensionais](https://github.com/AdilsonCapaia/IncoerenciasJustificaveisEmCMaisMAis/blob/master/README.md#PAA) então você já deve ter uma ideia de como definir o retorno da função, mas neste caso é preciso um pouco de cautela.

A sintaxe para definir uma função que retorna um array multidimensional é a seguinte :

*tipoDosElementoNoArray (\*nomeDaFunçao(parametros,....))[numeroDaSegundaDimensao]{ //corpo da função return arrayMultidiemensional }*

Com a sintaxe acima podemos definir nossa função *retornaLista* definida anteriormente desta forma :
```c++
int matrix[2][3]= {{2,4,0},{8,5,20}}; 

int (*retornaLista())[3]{  return matrix; }

int main()
{
  // afectar o retorno da função "retornaLista" no ponteiro para lista multidimensional "pD"
  int (*pD)[3];
  pD = retornaLista();
  return 0;
}
```
Definimos a nossa função *retornaLista* com a sintaxe correcta, mas sei que tem muita coisa  a se passar por aí, não se preocupe, vou explicar passo por passo o que está a se passar aí em cima.

Primeiro vamos começar com a definição da função *retornaLisa* : int (*retornaLista())[3]{  return matrix; }

Para absorver esta sintaxe é preciso ler da seguinte forma. Começamos pelo nome *retornaLista* olhamos para direita e notamos logo que ele tem parênteses,isso indica que *retornaLista* é uma função, e como dentro do parênteses não tem nada, quer dizer que é uma função sem parâmetros. A seguir olhamos para esquerda para ter mais informações, e notamos que têm o asterisco **\***, isto nos diz que nossa função retorna um ponteiro. Olhando para direita veremos que temos o parênteses recto com um número, isso nos diz que se desreferenciarmos o ponteiro retornado pela nossa função teremos um array de três dimensões. Mas não podemos esquecer que o array retornado é multidimensional. E podemos recuperar todas dimensões do arrays atravessando o ponteiro, por exemplo *( p + i )* onde *i* é a  posição corrente.

É uma sintaxe bruta, mas se leres mais uma vez o parágrafo anterior, tenho a certeza que perceberás.

Outra instrução a observar é esta *pD = retornaLista();*

Aqui simplesmente chamamos a função retornaLista(), e afetamos o valor retornado ao ponteiro *pD*. Mas sabemos que *pD* não é um ponteiro normal, *pD* é um ponteiro que aponta para um array multidimensional. Como sabemos que a função *retornaLista* retorna um array multidimensional, e que queríamos por esse retorno numa variável, por isso foi necessário definir *pD* como um ponteiro para arrays multidimensionais *( int (\*pD)[3]; )* .

Uma vez recebido o nosso array multidimensional retornado, agora podemos utilizar o ponteiro *pD* como se fosse o array multidimensional retornado, ou seja, como se fosse a nossa variável *matrix*.

Acabamos de desvendar mais uma sintaxe obscura do C++, pronto para a próxima ?

## <a name="FRF"></a> 1.8 Funções que retornam funções
Por razões raras que desconhecemos neste momento, em algum momento ou projeto você precisará fazer uma função retornar uma outra função. 

Então como se declara ou define uma função com retorno de tipo função.

Se você leu as seções [1.5 Ponteiros que apontam para funções](https://github.com/AdilsonCapaia/IncoerenciasJustificaveisEmCMaisMAis/blob/master/README.md#PAF), [1.6 Funções com parâmetros de tipo funções](https://github.com/AdilsonCapaia/IncoerenciasJustificaveisEmCMaisMAis/blob/master/README.md#FPF) e [1.7 Funções que retornam arrays multidimensionais](https://github.com/AdilsonCapaia/IncoerenciasJustificaveisEmCMaisMAis/blob/master/README.md#FRAM) então você entenderá como definir uma função que retorna uma outra função.

### 1.8.1 Incoerência ou  Interpretação intuitiva precipitada ?
Sabendo que a  sintaxe para declarar/definir qualquer tipo de função é :

*tipoRetorno nomeDaFunçao(tipo1 parametro1,...);*

Como sempre, estarias errado se utilizasses a fórmula acima para para definir uma função que retorna uma outra função.

### 1.8.2 Forma certa, Justificação/Entendimento do compilador
Não podemos propriamente definir uma função que retorna uma outra função, pelas mesmas razões que nos impedem de definir funções que retornam arrays unidimensionais ou multidimensionais.

Mas, têm sempre uma alternativa, que é através de ponteiros. Não podemos retornar funções porque o compilador não permite a cópia de uma função à outra, mas por outro lado ele permite retornar funções através de ponteiros que apontam para funções (ou referência de funções). Isso quer dizer que em vez de retornar uma função propriamente dita, retornaremos um ponteiro que aponta para uma função e depois utilizaremos o ponteiro como se fosse a função na qual a ponteiro aponta.

A sintaxe para definir uma função que retorna uma outra função é a seguinte :

*tipodeRetorno (\*nomeDaFunçao(parametros,....))(tipo1,tipo2,…){ /\*corpo da função \*/ return funçaoARetornar; }*

Supondo que temos uma função chamada *soma* que recebe dois números inteiros e retorna void :
```c++
void soma(int a, int b) { cout<< a + b<<endl; }
```
Com a sintaxe vista anteriormente, podemos definir uma função que retorna uma outra. Vamos criar uma função chamada *calculo* que poderá retornar uma função do mesmo tipo que a função *soma* : 
```c++
void (*calculo())(int,int)
{
  // código extra …
  return soma; 
}

int main()
{
   // “pf” é um ponteiro que aponta para função retornada pela função “calculo”, que no nosso caso é a função “soma”  
   auto pf =  calculo();

// implicitamente chama a função “soma”
pf(2,4);  // imprime   6 
}
```
Suponho que nesta etapa, sintaxes como esta já não deve mais ser uma aberração para ti. Mas vou dar uma pequena explicação na mesma.

Primeiro vamos começar com a definição da função *calculo* : *void (\*calculo())(int,int){  /\* código extra...\*/  return soma; }*

Para absorver esta sintaxe é preciso ler da seguinte forma. Começamos pelo nome *calculo* olhamos para direita e notamos logo que ele tem um parênteses,isso indica que *calculo* é uma função, e como dentro do parênteses não tem nada, quer dizer que é uma função sem parâmetros. A seguir olhamos para esquerda para ter mais informações, e notamos que têm o asterisco **\***, isto nos diz que a nossa função *caclulo* retorna um ponteiro. Olhando para direita veremos que temos mais parênteses curvos com dois parâmetros do tipo *int*, isso nos diz que se desreferenciarmos o ponteiro retornado pela nossa função *calculo* teremos uma função, e se olharmos mais para à esquerda do **\*** asterisco veremos que a função retornada como ponteiro tem um retorno do tipo *void*. 

Contudo, quer dizer que a função *caclulo* retorna um ponteiro que aponta para uma função que retorna um *void* e que recebe dois argumentos de tipo *int*.

*auto pf =  calculo();*  Afetamos o valor retornado pela função *calculo* para a variável *pf*, como o valor que a função *calculo* retorna é um ponteiro para uma função, então *pf* será automaticamente deduzido como um ponteiro que aponta para uma função, no nosso caso a função é a função *soma*.

Como *pf* aponta na função *soma* então podemos utilizar *pf* como se fosse a nossa função *soma*, exemplo :  *pf(2,4);*

Aqui está, mais uma sintaxe obscura do C++ desvendada.

