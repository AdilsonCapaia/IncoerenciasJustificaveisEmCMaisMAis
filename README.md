# Incoerências Justificaveis Em C++
Reflexões sobre algumas Incoerências e armadilhas em C++, e tentativas de clarifica-las ou justifica-las

## Índice
  - ## Problemáticas
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
