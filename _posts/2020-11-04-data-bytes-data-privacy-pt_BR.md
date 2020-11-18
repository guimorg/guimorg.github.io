---
title: "Data Bytes - Data Privacy"
tags: [machine learning, data, privacy, ethics]
toc: true
lang: pt_BR
ref: data-bytes-data-privacy
excerpt: Um pouco de privacidade de Dados
header:
  image: "/images/data-bytes/data-privacy/theme.png"
---

(Imagem tirada [daqui](https://www.bigcommerce.com/blog/consumer-data-privacy/))

A Lei de Proteção de Dados no Brasil já é uma realidade. Em vigor desde o dia [18 de setembro deste ano](https://agenciabrasil.ebc.com.br/geral/noticia/2020-09/entenda-o-que-muda-com-a-lei-geral-de-protecao-de-dados), a LGPD define um conjunto de normas para definir limites, condições de coleta, guarda e tratamento de informações pessoais. Um dos pilares da [LGPD](http://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/lei/L13709.htm) é a **privacidade de dados**, conforme o Art. 1o: *"com o objetivo de proteger os direitos fundamentais de liberdade e de privacidade"* e também o inciso I do Art. 2o: *"A disciplina da proteção de dados pessoais tem como fundamentos: o respeito à privacidade"*.

Com isso, o tema de privacidade tem sido muito discutido em diversas mídias e também em organizações (principalmente por conta das consequências legais e riscos de imagem); neste pequeno artigo apresentarei alguns conceitos relacionados a privacidade e também minha visão sobre o tema para incitar a discussão e a busca de mais conhecimento.

## O que é Privacidade? (ou o que Privacidade não é)

Um bom lugar para começar a discussão é definir o que é privacidade; esta definição por si só já irá separar as melhores soluções e métodos para garantir a privacidade nos dados.

### Privacidade e Anonimização

É muito importante ter em mente que os conceitos de privacidade e anonimização não são idênticos e que dados anonimizados não correspondem à dados privados (ou privacidade). Este ponto nem sempre é muito óbvio porque se tomamos um *dataset* (um conjunto de dados) como independente de outros, em um vácuo, a anonimização pode parecer suficiente para garantia de privacidade.

No entanto, a anonimização falha em privacidade quando tomamos outras fontes de dados para identificar um indivíduo; isto é dizer que mesmo que um *dataset* esteja anonimizado, informações contidas em outro *dataset* podem ser utilizadas para identificar indivíduos.

Um bom exemplo disso é o caso do *Netflix Data Challenge*, que disponibilizou dados anonimizados para melhorar o sistema de recomendações em 2006 (contendo apenas IDs fictícios e notas dadas aos filmes pelos usuários); [dois pesquisadores da Universidade do Texas](https://www.cs.cornell.edu/~shmat/shmat_oak08netflix.pdf), Arvind Narayanan e Vitaly Shmatikov, no entanto, conseguiram identificar indivíduos dentro desde *dataset* anonimizado utilizando dados públicos do [Internet Movie Database](http://www.imdb.com/).

> Demonstramos que um adversário que sabe um pouco sobre algum assinante pode facilmente identificar seu registro, se estiver presente no conjunto de dados, ou, pelo menos, identificar um pequeno conjunto de registros que inclui o registro do assinante.
>
> [**Robust De-anonymization of Large Sparse Datasets**](https://www.cs.cornell.edu/~shmat/shmat_oak08netflix.pdf)

O principal ponto de falha da anonimização em privacidade é que nós, indíviduos, somos identificáveis utilizando um pequeno número de informações únicas -   que não necessariamente seriam encaradas como sensíveis, como os últimos produtos que você comprou no Mercado Livre, ou os últimos filmes que você assistiu.

Este é um dos pontos levantados por Cynthia Dwork, uma das responsáveis pelo conceito da Privacidade Diferencial:

> a anonimização de dados não é [igual a privacidade] - ou os dados não são realmente anônimos ou tanto já foi removido que não podem mais ser chamados de dados
>
> [The Algorithmic Foundations of Differential Privacy](https://www.cis.upenn.edu/~aaroth/Papers/privacybook.pdf)

### Privacidade e Criptografia

Outro ponto muito levantado é que a criptografia pode ser utilizada dentro do contexto da privacidade. Na verdade, a criptografia de dados é a solução de um problema diferente da privacidade: ela resolve o problema de *segurança do dados*.

Para a LGPD, este é um tema muito importante, pois lida com possíveis incidentes de exposição de dados, que sob a criptografia ficam "seguros".

A melhor forma de entender a diferença destes dois elementos é que:

> A criptografia protege dados, a privacidade protege a identidade
>
> [Data Privacy vs. Data Security: What is the Core Difference](https://www.tokenex.com/blog/data-privacy-vs-security)

### Mas afinal, o que é Privacidade?

A melhor definição, ou a que melhor me satisfaz, é a definição diferencial de privacidade. Esta é uma formalização matemática da intuição ou definição de privacidade e por isso é tão importante para o contexto de Dados - em que lidamos com Aprendizagem de Máquina, Big Data e Algoritmos -, porque ela pode ser traduzida para a linguagem de máquina.

Os detalhes da Privacidade Diferencial estão no livro [The Algorithmic Foundations of Differential Privacy](https://www.cis.upenn.edu/~aaroth/Papers/privacybook.pdf) disponível online (uma ótima referência), mas algumas ideias que nos levam a intuição deste conceito:

- O conceito de privacidade separa o indivíduo do conhecimento aprendido (obtido através) do indíviduo;
- Uma consulta não deveria revelar se um indivíduo se encontra ou não dentro de um *dataset*;
- Do ponto de vista de um indivíduo, existe a garantia de que uma mesma consulta seria obtida com ou sem sua informação em um *dataset*;
- Em outras palavras, tudo que pode ser aprendido sobre um indivíduo utilizando o *dataset*, deve poder ser aprendido sem acesso ao *dataset*.

Em outras palavras, a privacidade é encarada como a independência de decisão ou de consulta de um indivíduo em específico. Este *[blog post](https://robertovitillo.com/differential-privacy-for-dummies/)* explica um pouco melhor sobre a Privacidade Diferencial em alto nível, outra ótima referência sobre o tema é o primeiro capítulo do livro ["The Ethical Algorithm: The Science of Socially Aware Algorithm Design"](https://www.amazon.com/Ethical-Algorithm-Science-Socially-Design/dp/0190948205) que também apresenta as **consequências** e **desvantagens** da privacidade diferencial.

## Soluções para a Privacidade

Buscando obter alguma forma de privacidade, existem diversas soluções que foram adotadas ao longo do tempo e encaram a privacidade de forma diferente. Porque resolvemos buscar na Privacidade Diferencial o conceito de Privacidade, estas técnicas mostrarão diversas falhas em manter a identidade dos indíviduos dentro de um *dataset*.

- Retirar dados sensíveis: acho que essa é uma das soluções mais comuns levantadas para a privacidade; e talvez a que mais falha em atingir a privacidade. O principal problema é que ela assume que nós, indíviduos, somos identificáveis **somente** por dados sensíveis ou pelos dados removidos (o que não é verdade).
- Usar apenas dados agregados: essa é outra prática bastante comum e consiste em usar dados agregados em grupos de indivíduos. Aqui temos a falha em assumir que dados agregados não conseguem recuperar informações de indivíduos ou recuperar o *dataset* não agregado (algumas referências [aqui](https://iapp.org/news/a/aggregated-data-provides-a-false-sense-of-security/) e [aqui](https://tozny.com/blog/10-unnerving-privacy-fails-thru-data-aggregation/))
- Anonimizar dados: este ponto foi introduzido acima com um exemplo prático do caso do Netflix. A principal falha aqui resta no fato de que informações contidas em outros *datasets* podem ser usadas em conjunto para identificar indivíduos; e sim, isto é da responsabilidade da companhia dona dos dados "anonimizados".
- Privacidade Diferencial: esta também é uma técnica que utiliza a aleatoriedade para atingir um certo nível de privacidade. Uma das falhas desta técnica é que a simples existência de um indivíduo dentro de um *dataset* **já é informativa e identificável**.

Existem outras soluções para a privacidade fora desta lista (Privacidade Sintética, por exemplo), mas elas se encontram muitas vezes fora da praticidade ou usabilidade. [Este artigo](https://pdfs.semanticscholar.org/7c6a/bddbd791dddd281c5764dbe859c55ba2e019.pdf?_ga=2.170032995.299723572.1604518308-1165209624.1601912032) detalha muitas destas técnicas e até mesmo outras definições de privacidade. Vale a pena a leitura!

## Wrap Up

Neste artigo, tentei apresentar um pouco sobre o conceito de privacidade e também algumas soluções para a privacidade. Como sempre, não existe uma bala de prata para o problema da privacidade e cada uma das técnicas possui falhas e também consequências do ponto de vista de informação (um modelo de Machine Learning, por exemplo, sofrerá com a acurácia quando a privacidade diferencial é utilizada).

Espero que possa de alguma forma incitar a discussão e a busca de novas referências para esse tema tão importante dentro de **Ethical AI** e tão relevante para estes dias.

## Referências

De Capitani Di Vimercati S, Foresti S, Livraga G, Samarati P. **Data Privacy: Definitions and Techniques**. International Journal of Uncertainty, Fuzziness and Knowledge-Based Systems. 2012;20(06):793-817.

Kearns M, Roth A. **The ethical algorithm**. Oxford: Oxford University Press; 2020.

Tozny. **10 Unnerving Privacy Fails Thru Data Aggregation**. Disponível em: https://tozny.com/blog/10-unnerving-privacy-fails-thru-data-aggregation/

Dwork C, Roth A. **The Algorithmic Foundations of Differential Privacy**. Foundations and Trends® in Theoretical Computer Science. 2013;9(3-4):211-407.

Narayanan A, Shmatikov V. **Robust De-anonymization of Large Sparse Datasets**. 2008 IEEE Symposium on Security and Privacy (sp 2008). 2008;.

Arbuckle L. **Aggregated data provides a false sense of security**. Disponível em: https://iapp.org/news/a/aggregated-data-provides-a-false-sense-of-security/

Valente J. **Entenda o que muda com a Lei Geral de Proteção de Dados**. Disponível em: https://agenciabrasil.ebc.com.br/geral/noticia/2020-09/entenda-o-que-muda-com-a-lei-geral-de-protecao-de-dados

Phillips D. **Data Privacy vs. Data Security: What is the Core Difference?**. Disponível em: https://www.tokenex.com/blog/data-privacy-vs-security

Vitillo R. **Differential Privacy for Dummies**. Disponível em: https://robertovitillo.com/differential-privacy-for-dummies/

Harvard University Privacy Tools Project. **Differential Privacy.** Disponível em: https://privacytools.seas.harvard.edu/differential-privacyc
