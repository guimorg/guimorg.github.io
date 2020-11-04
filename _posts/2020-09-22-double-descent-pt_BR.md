---
title: "Data Bytes - Double Descent"
tags: [machine learning]
toc: true
lang: pt_BR
ref: data-bytes-double-descent
excerpt: Você já ouviu falar do fenômeno do Double Descent?
header:
  image: "/images/data-bytes/double-rainbow.jpeg"
---

Você se lembra de quando aprendeu sobre o tema de viés e variância em Machine Learning? É possível que você se lembre da famosa "Compensação de viés-variância" (Bias-Variance Tradeoff), ou o conflito existente quando tentamos minimizar viés e variância para obter um melhor poder de generalização no aprendizado supervisionado. 

Mas e se este 'sacrifício', ou de viés ou de variância, não fosse obrigatório para todos os algoritmos? Seria possível diminuir tanto viés como variância ao mesmo tempo? Neste pequeno texto vamos ver um pouco sobre o fenômeno do *Double Descent*, uma descoberta no campo de Deep Learning e no Machine Learning moderno que contradiz as expectativas da estatística tradicional para esta temática.

## Refrescando a memória

Antes de entrar na explicação deste fenômeno, talvez seja interessante refrescar a memória sobre viés e variância bem rapidamente (se você quiser se aprofundar mais, ao final do post existem ótimas referências).

Dentro do paradigma do aprendizado supervisionado estamos interessados em aprender uma função $f$ que "mapeie" um conjunto de features $X$ para uma saída esperada $Y$ (nosso "alvo"). Através de um modelo matemático/estatístico, que possui uma série de funções $h\in H$, durante o processo de treinamento "escolhemos" um $h_{n}$ que minimize o erro entre os valores esperados e os valores fornecidos "esperando" que esta função se aproxime da função verdadeira para novos $X$ ainda não vistos pelo modelo, ou, que o poder de generalização do modelo seja bom o suficiente. Para isso medimos tanto o erro das predições do modelo dentro do conjunto ($E_{in}$) de treinamento, mas também dentro do conjunto de testes ($E_{out}$), que contém dados não fornecidos ao modelo durante o treinamento.

Então, quando mencionamos a existência de um determinado modelo matemático/estatístico estamos nos referindo ao conjunto de funções (chamamos de hipóteses) contidas em $H$. E para isso, finalmente, entramos numa primeira noção de viés e variância relacionada ao conjunto de hipóteses (BELKIN et al., 2019):

1. Se $H$ for muito pequeno, entraremos em um cenário de 'risco empírico' onde as hipóteses não consigam aprender suficientemente com os dados (*underfitting*), podendo resultar em uma performance ruim tanto para predições em dados vistos como não vistos;
2. No entanto, se $H$ for muito grande minimizamos o 'risco empírico', mas arriscamos entrar no cenário em que aprendemos padrões espúrios no conjunto de treinamento (*overfitting*), podendo resultar em uma má generalização para as predições em dados não vistos.

Com esta primeira visão, já conseguimos começar a entender a "Compensação do Viés e Variância" e até mesmo a entender os tipos de decisão que tomamos quando treinamos um modelo sejam nos modelos escolhidos, quanto em técnicas de regularização que entram justamente no conjunto de hipóteses $H$. Então, a visão de viés e variância nesse contexto pode ser vista assim:

- Para um $H$ muito pequeno, temos uma pequena variância, mas grande viés, enfrentamos o risco de *underfitting*
- Para um $H$ muito grande, temos uma grande variância, mas pequeno viés, enfrentamos o risco de *overfitting*

Esta figura ilustra bem o conhecimento dentro de ML clássico para essas 'duas forças' e como tentamos chegar no ponto ideal levando em conta a capacidade (ou, na minha terrível simplificação, no conjunto) de $H$:

![image-20200923174738868](/Users/guamorim/git/guimorg.github.io/images/data-bytes/1/vies-variancia.png)

Resumidamente, conforme a capacidade de $H$ aumenta os erros dentro do conjunto de treinamento diminuem junto com o erro para o conjunto de testes (dados não vistos pelo modelo), no entanto, chega um momento em que começamos a aprender informações espúrias, ou ruídos, e falhamos em generalizar para fora do conjunto de treinamento, que resulta no aumento dos erros no conjunto de teste mesmo com a diminuição dos erros no conjunto de treinamento. 

Espero que nenhum estatístico ou pesquisador tenha infarto lendo essa pequena, extremamente simples e inocente descrição, mas o foco deste artigo não é explicar o processo de aprendizado *per-se*, mas apenas refrescar a memória nas idéias principais necessárias para entender o fenômeno do *double descent*.

## Machine Learning Moderno

No entanto, nos tempos modernos de ML e com uma maior acessibilidade para ferramentas computacionais mais poderosas, modelos mais complexos puderam ser explorados e treinados; entra a era do *Deep Learning* e começamos a ter relatos que desafiavam o entendimento anterior da capacidade de $H$ e, bem como, da observação dos erros de treinamento e de teste:

- Belkin, Ma e Mandal (2018) relatam em seu artigo sobre os casos dentro de Deep Learning e Kernel Learning onde apesar de obterem erros de treinamento zero em um conjunto com alto ruído, modelos continuavam tendo uma ótima performance no conjunto de testes;
- Wyner et al. (2017) explicam o sucesso dos classificadores Adaboost e Random Forest, um dos pontos levantados é sobre o fenômeno de quando o erro de treinamento zero atrelados a diminuição do erro de teste.

E então? Onde entrariam as explicações para este fenômento dentro do ML moderno e até mesmo do ML clássico (Random Forest e Adaboost)?

## Double Descent

O fenômeno do Double Descent, na realidade, já tinha sido observado algumas décadas atrás, conforme reportam Loog et al. (2020) em uma correspondência ("*A Brief Prehistory of Double Descent*") extremamente interesante sobre a (pré)história do *double descent* e da necessidade de uma maior compreensão deste comportamento "rudimentar" se aprendizagem.

Este fenômeno pode ser visualizado nesta figura abaixo:

![image-20200923185736264](/Users/guamorim/git/guimorg.github.io/images/data-bytes/1/double-descent.png)

Aqui, conseguimos observar o "regime clássico" descrito anteriormente, mas também um ponto, o limiar de interpolação, em que embora tenhamos um erro zero no conjunto de treinamento, começamos a observar a diminuição de erro no conjunto de testes (mesmo com o aumento da capacidade de $H$!).

A primeira coisa a se observar é a importância de se "encontrar" o limiar de interpolação:

- Um dos métodos utilizados para diminuir a variância é a utilização de técnicas de regularização que pode inviabilizar a chegada até o limiar e certamente diminuem a capacidade de $H$;
- Dentro deste contexto são necessários modelos que complexidade arbitrária (o que aumenta a capacidade de $H$), ao contrário de alguns modelos do ML clássico.

Existem dois bons exemplos que podem ser utilizados que explicam um pouco sobre o pensamento do viés e variância antigo e da 