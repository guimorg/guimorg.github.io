---
title: "Data Bytes - Concept Drift"
tags: [machine learning, data]
toc: true
lang: pt_BR
ref: data-bytes-concept-drift
excerpt: Concept Drift - Data Bytes
header:
  image: "/images/data-bytes/concept-drift/post.png"
---

**Data Bytes** é uma série do blog que consiste em pequenas posts (ou *bytes*) que tentam explicar um assunto de uma forma bastante resumida - é quase uma introdução para te incentivar a aprender mais sobre.

Hoje vamos abordar um pouco sobre o *concept drift* - preferi utilizar o termo em inglês pela vasta literatura e para não desconectar o que talvez já seja um conhecimento prévio seu com uma terminologia nova.

## Concept Drift

Antes de começarmos, vamos definir algumas premissas que serão fundamentais para elaborarmos sobre este conceito: primeiro, estaremos olhando sob a pespectiva do aprendizado supervisionado - onde predizemos uma variável de interesse $y$ dado um conjunto de variáveis de entrada $X$.

Para problemas de classificação, onde $y$ é um valor categórico, podemos descrever este cenário utilizado a Teoria Bayesiana de Decisão (DUDA et al, 2001):

$$p(y|X)=\dfrac{p(y)p(X|y)}{p(X)}\\\text{onde}\\p(X)=\sum^{c}_{y=1}p(y)P(X|y)$$

Aqui, nossa probablidade da classificação $p(y\|X)$ é descrita pela probabilidades anteriores das nossas classes $p(y)$ e pelas funções de densidade de probabilidade condicional das classes $p(X\|y)$ (vale a pena ressaltar que na equação acima estamos assumindo os custos de classificações iguais para todas as classes).

Para a maioria dos problemas de Machine Learning (ou talvez para os cenários mais clássicos), nos é dado um conjunto de dados (histórico e marcado, isto é, com instâncias $(X_{n}, y_{n})$) do qual aprendemos e desenvolvemos um modelo (Gama et al (2013) geralmente definem este como o cenário offine). Dentro de um cenário de consumo online para este modelo, novos dados são recebidos um a um, num possível fluxo infinito, para a realização de predições (basicamente fazendo um 'mapeamento' do espaço de entrada $X$ para sua correspondente saída $y$).

Em um situações reais e produtivas, **é esperada a 'evolução' dos dados com o passar do tempo (ou uma mudança nas distribuições)**, em outras palavras um cenário não-estacionário: nosso ambiente é dinâmico e constantemente muda com o tempo.

### O que é Concept Drift?

OK, agora que nós entendemos um pouco o cenário que vamos abordar, podemos prosseguir para entender **o que é o concept drift**.

Gama et al (2013) definem o concept drift como:

$$\exists X:p_{t_{0}}(X,y) \ne p_{t_{1}}(X,y)$$

Aqui, $p_{t_{n}}$ denota a distribuição conjunto no tempo $t_{n}$ entre nossas variáveis de entrada $X$ e nossa saída esperada $y$. Nesta definição, estamos argumentando que por conta de nosso ambiente não-estacionário, existe um tempo $t$ onde a distribuição conjunta $p(X,y)$, aprendida dos nossos dados de treinamento, não corresponse a distribuição atual.

Considerando esta situação, os seguintes eventos podem ocorrer: uma mudança nas nossas probabilidades anteriores $p(y)$ e/ou uma mudança em nossas probabilidades condicionais das classes $p(X\|y)$, que resultam em uma mudança nas probabilidades posteriores das classes $p(y\|X)$, onde nossas predições não são condizentes.

## Tipos de Concept Drift

Existem diferentes tipos de *concept drifts* que podem surgir das mudanças que descrevemos acima:

#### Concept Drift Real

Também conhecido como concept shift ou conditional change, se refere a **mudanças na probabilidade posterior $p(y\|X)$**, que podem surgir com mudanças na distribuição dos dados de entrada $P(X)$ ou não.

Uma coisa interessante de se mencionar é que esta situação de um concept drift real **é muito difícil de ser encontrada, especialmente** se estamos em um cenário onde o **o valor verdadeiro da saída de nossos dados, ou o feedback de nossas respostas é atrasado**, ou seja, onde a só sabemos a resposta verdadeira (ou se nosso modelo 'acertou ou errou' sua predição) depois de um tempo.

#### Drift Virtual

Este pode ser conhecido como feature change, sampling drift ou temporary drift e se refere a **uma mudança na distribuição dos dados de entrada $P(X)$ que não afeta nossa distribuição de probabilidades posteriores $p(Y\|X)$.**

Mudanças na distribuição de entrada são mais fáceis de se detectar, especialmente porque as respostas verdadeiras do modelo (o feedback das saídas) não são necessárias. Podemos, por exemplo, comparar as distribuições de entrada de nossos dados de treinamento e dos dados recebidos de forma online (existem diversas outras maneiras de se detectar o drift virtual).

### Comparação visual

Gama et al (2013) fornecem uma comparação visual muito intuitiva dos dois tipos de concept drift:

![image-20210331105705535](/images/data-bytes/concept-drift/comparison-drifts.png){: .align-center}

Podemos observar que no caso de um concept drift real, o limite de decisão mudou, isto é, as predições obtidas pelo limite de decisão original não são mais condizentes com a realidade; do outro lado podemos observar que no caso de um drift virtual, o limite de decisão original ainda é condinzente, mesmo com a mudança na distribuição dos dados de entrada.

## Natureza da mudanças

Observamos acima que a chave para entender o que é um concept drift (real ou virtual) é entender que os cenários reais não são estacionários, o que quer dizer que mudança vão acontecer.

As mudanças tem muitas formas, e para entendermos estas seria interessante observar suas causas e seu 'ritmo'.

### Causas das Mudanças

Conforme vimos anteriormente, nosso cenário consiste em um modelo que aprende observações anotadas anteriores com um *número finito de variáveis (features)*. Quando desenvolvemos um modelo, geralmente realizamos muitas análises (desde o ponto de vista estatístico até o da natureza do negócio) para entender quais variáveis deveriam ser utilizados para o treinamento do modelo.

No entanto, muitos *variáveis escondidas (ou não visíveis)* podem influenciar nossa variável de interesse (ou saída) e podem estar faltando ao modelo por diferentes motivos, desde não estarem ainda disponíveis (mas serem observáveis) até não serem observáveis. **Uma possível causa pode estar presente quando existe uma mudança na distribuição de uma variável escondida**, que resulta em conceitos diferentes dos que foram aprendidos anteriormente.

Outra possível causa é quando **a natureza de um conceito muda**, isto está mais atrelado a visão de negócio de um problema e pode estar relacionada a mudanças nas políticas, mercado, ou até mesmo na visão de um negócio.

### Ritmo de Mudanças

Este é um tópico extremamente importante, na minha opiniao, principalmente porque existem algumas pequenas confusões na definição de concept drift e como podemos observá-lo. Gama et al (2013) disponibilizaram uma representação virtual de diferentes padrões e ritmos de mudança para dados unidimensionais:

![image-20210331121613093](/images/data-bytes/concept-drift/change-over-time.png){: .align-center}

Aqui conseguimos visualizar diferentes padrões de murança, em alguns casos as mudanças são mais fáceis de detectar (mudança repentinas/abruptas ou graduais), em outros elas podem estar relacionadas a padrões sazonais (conceitos recorrentes), e alguns podem nem mesmo serem realmente drifts (como anomalias ou outliers).

O grande desafio aqui é em **como podemos detectar e diferenciar concept drifts reais de ruídos ou anomalias**.

## Conclusão

Este post do data bytes foi uma pequena introdução ao concept drift e a possiveis desafios atrelados a sua detecção. A verdade é que esta detecção não é trivial e podem ser necessários diferentes tipos de aproximações (ou proxies) para tentar identificar uma possível situação de concept drift - isto é especialmente difícil quando lidamos com um feedback atrasado das respostas do modelo.

O termo de concept drift também é muito abordado dentro de **Aprendizagem Ativa**, e a maioria das referências citadas abaixo foram escritas dentro deste contexto (Joao da Gama é uma das minha maiores referências para este assunto, dê uma olhada no seu livro e na sua pesquisa).

## Referências

LAZARESCU, M.; VENKATESH, S.; BUI, HH. Using multiple windows to track concept drift. 2004. Disponível em: https://www.researchgate.net/publication/220571442_Using_multiple_windows_to_track_concept_drift

GAMA, J.; ZLIOBAITE, I.; BIFET, AL.; PECHENIZKIY, M.; BOUCHACHIA, A. A Survey on Concept Drift Adaptation. 2013. Disponível em: https://dl.acm.org/doi/10.1145/2523813

GAMA, J.; AGUILAR-RUIZ J. S.; KLINKENBERG, R. Knowledge Discovery from Data Streams. 2010. Disponível em: https://www.researchgate.net/publication/220571629_Knowledge_discovery_from_data_streams

PAKA, A. How to detect model drift in ML Monitoring. Disponível em: https://blog.fiddler.ai/2020/08/how-to-detect-data-drift/
