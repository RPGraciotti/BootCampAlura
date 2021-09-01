![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/header.jpg)

# Resumo

> O desafio de detectar a necessidade de uma pessoa ser internada na UTI utilizando os dados vitais do paciente foi uma forma instigante de criar um pipeline de limpeza de dados, análise exploratória, busca de modelos de Machine Learning e hiperparâmetros.

> O conjunto de dados inicial carecia de muitos valores, com cerca de 50% de dados faltantes. Foi necessário um cuidadoso trabalho de curadoria e preenchimento de dados faltantes de forma relevante a não enviesar a detecção de padrões.

> Após a limpeza, a análise exploratória revelou que de fato o maior risco de internação em UTI está associado com maior idade e doenças pré-existentes, assim como hipertensão e imunodeficiência.

> Diversos modelos foram testados buscando-se maximizar métricas que descrevem a performance do modelo em identificar corretamente os casos verdadeiros, e sobretudo a capacidade do modelo não errar os falsos negativos, isto é, prever erroneamente que pessoas em quadros graves da doença não precisariam de internação.

> A performance geral dos modelos foi razoável, explicitando-se a necessidade de maiores parâmetros de busca e avaliação dos modelos, a fim de balancear o efeito de previsões do tipo falso positivo e falso negativo.

> Descrevi possíveis direcionamentos para refinamento dos modelos e busca de parâmetros para que a peformance dos modelos seja melhorada.

# Introdução Geral

A Pandemia de COVID19 marca o início da década de 2020. Uma doença respiratória provocada pelo vírus conhecido como SARS-CoV-2, foi identificada pela primeira vez em Dezembro de 2019, e em Março de 2020 foi considerada uma pandemia pela [Organização Mundial da Saúde](https://www.who.int/director-general/speeches/detail/who-director-general-s-opening-remarks-at-the-media-briefing-on-covid-19---11-march-2020). O nome do vírus deriva de **S**evere **A**cute **R**espiratory **S**yndrome **Co**rona **V**irus, ou: Síndrome Respiratória Aguda Grave causada por Coronavírus. É a segunda doença do tipo causada por um Coronavirus, a primeira teve circulação restrita ao continente asiático durante o [início da década de 2000](https://en.wikipedia.org/wiki/Severe_acute_respiratory_syndrome_coronavirus_1). Entre os sintomas mais comuns da COVID (CoronaVirusDisease) encontram-se febre, cansaço, tosse, dificuldade de respirar, perda de olfato e paladar. Em 14% dos casos, os sintomas evoluem para quadros graves de pneumonia, hipóxia, falência respiratória e falência dos orgãos.
A facilidade de transmissão e severidade dos sintomas de formas mais graves da doença provocou a rápida disseminação do vírus, que hoje (Agosto de 2021) totaliza mais de 216 milhões de casos e 4 milhões de mortes no [mundo todo](https://covid19.who.int/).

Durante os primeiro meses de 2020, a comunidade científica enfrentou vários desafios na busca por compreender os mecanismos de transmissão e prevenção do vírus, a urgência por desenvolvimento de vacinas e tratamentos. A comunidade médica enfrentava a realidade de uma doença que se expande de [forma exponencial](https://www.bbc.com/future/article/20200812-exponential-growth-bias-the-numerical-error-behind-covid-19): cada vez mais pessoas infectadas transmitem com mais facilidade para um número cada vez maior de pessoas. A necessidade de acompanhamento médico em formas mais graves da doença gera uma grande pressão no sistema de saúde: o número de pessoas infectadas e que necessitam de internação em Unidades de Terapia Intensiva (UTI) cresce muito mais do que o sistema de saúde pode suportar. 

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/_111232963_controlled_uncontrolled_transmission_640-nc.png)

**nota: hoje, em 2021, sabemos que as melhores medições de contenção da disseminação do vírus envolvem o uso de máscaras e distanciamento social, não somente as medidas propostas na figura**

A maior preocupação durante os momentos de expansão da doença é justamente prever se os sistema de saúde [terão capacidade de lidar](https://www.bbc.com/portuguese/internacional-51850382) com o crescente número de pessoas infectadas e que necessitam de internação em leitos de UTI. Dessa forma, a equipe médica do Sírio-Libanês compilou dados sobre pacientes que foram atendidos em sua rede com sintomas de covid, e propôs um [desafio de identificar as melhores formas de caracterizar a necessidade ou não de pacientes serem internados em leitos de UTI](https://www.kaggle.com/S%C3%ADrio-Libanes/covid19). Utilizando informações sobre a saúde dos pacientes, o objetivo é desenvolver um modelo de Machine Learning capaz de prever a necessidade um paciente ser internado ou não na UTI.

Os dados possuem variáveis discretas que descrevem os pacientes, como idade absoluta, presença de doenças pré-existentes, gênero, etc.; e também variáveis contínuas de medições de sinais vitais, como temperatura, pressão sistólica e diastólica, níveis de proteínas, etc. Os dados são separados em janelas temporais de 2h em 2h, identificando se naquela janela o paciente foi ou não internado. A recomendação inicial da equipe do Sírio-Libanês é restringir a análise ao uso somente dos dados disponíveis até o momento que a pessoa é internada (se internada) na UTI, a fim de evitar a ambiguidade de quando a última medição foi obtida, e também o objetivo é detecar a necessidade de internação o mais rápido possível. 

# Limpeza e análise exploratória

###### Link de acesso: https://github.com/RPGraciotti/BootCampAlura/blob/main/Projeto_final/Exploratoria.ipynb

Após a primeira inspeção do dataframe, notei que:

1. Há certos tipos de dados que parecem ser do tipo discreto e que não mudam para o mesmo paciente, e dados contínuos, que diferem entre as diferentes leituras de sinais vitais. 
3. Há uma quantidade de dados faltantes nas features contínuas.
4. Há também uma quantidade de dados faltantes nas features discretas.

No total, em ao menos **metade** do nosso dataset não há informação. Um primeiro caminho poderia ser remover todos esses dados. Isso não é o ideal, já que, de acordo com as recomendações da própria equipe do Sírio-Libanês, há algumas etapas que podemos seguir para minimizar a nossa perda de informação.

## Limpeza dos dados

A medida mais imediata para a simplificação e limpeza dos dados foi a remoção de pacientes que deram entrada na UTI na primeira janela de tempo. Essa medida é baseada na recomendação da equipe de utilizar somente os dados disponíveis até o momento da internação.

![resumo dos protocolos indicados pela equipe do Sírio Libanês](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/inbox_1591620_77ca2b4635bc4dd7800e1c777fed9de1_Timeline%20Example%20No.png)

Depois, foi necessário preencher os dados faltantes, utilizando um protocolo que preenche uma célula com dados do momento anterior ou posterior (desde que antes da internação), baseando-se no princípio de que os sinais vitais não variam ou pouco variam nesses curtos intervalos de tempo.

Valores faltantes também aparecem na seguinte situação: se um paciente tem uma lacuna de medição exatamente na janela anterior à internação e possui dados após a internação, estes valores não serão utilizados para preencher a lacuna anterior.

Depois, foi necessário o resumo dos dados utilizando-se somente a informação de que se o paciente deu entrada ou não na UTI. É uma simplificação dos dados que parte do mesmo princípio de que se espera que não haja muita variação entre as medições de sinais vitais. Temos ciência de que pode não ser o cenário ideal para um modelo mais complexo e potencialmente mais informativo, mas essa simplificação torna o processo bastante eficiente: precisamos detectar rapidamente a possibilidade de um paciente ser admitido na UTI.

Por último, encontrei ainda valores faltantes, decorrentes do seguinte cenário: pacientes que só tiveram suas primeiras medidas aferidas após a janela de internação na UTI. Nesse caso, nada mais pôde ser feito a não ser a remoção total desses pacientes.

O cenário final ficou o seguinte: 
```
Data frame original = (1925 linhas, 231 colunas)
Após limpeza = (294 linhas, 231 colunas)
```

## Análise exploratória

### Variáveis categóricas:

Primeiro, observei o número absoluto de pessoas que foram ou não internadas na UTI:
```
0    189
1    105
```
Em dados categóricos, 0 geralmente indica a condição negativa (no nosso caso, não foram internadas na UTI), e 1 a condição positiva (foram internadas). Mas também pode representar outras classes, como veremos adiante.

Temos então que 189 pessoas não foram para UTI e 105 sim, mostrando um resultado relativamente desbalanceado, quase o dobro de pessoas tem a categoria 0.

### Categorias de idade:
Muito se discute se a probabilidade de um paciente ser internado ou não na UTI está associada com a sua idade, seja em valores absolutos, ou se o fato do paciente ser idoso ou não é determinante dessa possibilidade. A primeira variável que carrega essa informação no dataset é a coluna AGE_ABOVE65, que identifica se o paciente tem mais ou menos de 65 anos:

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/exp/fig1.png)

Legenda: gráfico de barras da distribuição de internações na UTI conforme idade maior ou menor que 65 anos. 0 para menor, 1 para maior.

Observei que mesmo não havendo um aumento no número absoluto de internações em pessoas com mais de 65 anos em relação às internações com menos de 65 anos, há um aumento considerável proporcionalmente. Há uma proporção de 1/3 dos pacientes precisando de internação entre < 65, e uma proporção de 1/2 entre os > 65.

Porém, há um outro dado relativo a idade que é a idade absoluta:

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/exp/fig2.png)

Legenda: gráfico de barras da distribuição de internações na UTI conforme percentis de faixa etária. 10th indica pacientes de até 10 anos, e assim sucessivamente.

Percebi que conforme maior a idade, sobretudo nos idosos acima de 70 anos, um maior número de pessoas é internada na UTI em relação às pessoas que não são internadas.

### Gênero:
E dados sobre gênero?

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/exp/fig3.png)

Legenda: gráfico de barras da distribuição de internações na UTI conforme gênero. 0 = masculino, 1 = feminino.

Notei que, proporcionalmente mais homens foram internados na UTI do que mulheres.

### Doenças pré-existentes:
Um outro tipo de dado disponível do dataframe é uma combinação de 6 grupos de doenças pré-existentes em cada paciente. Devido os protocolos de anonimização, não sabemos quais são esses tipos de doenças, mas podemos examinar sua distribuição também:

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/exp/fig4.png)

Legenda: gráficos de barras empilhadas para indicar a distribuição dos grupos de doenças. Atente-se ao fato de que a visualização é um pouco diferente, devido à diferença na natureza dessa variável no dataframe: ele indica a proporção de internações em relação ao total na mesma barra.

Vi que os grupos de doenças 2 e 4 apresentam um alto risco de internação em UTI, quando comparados aos outros grupos que tem uma distribuição mais proporcional.

A próxima variável relativa à doenças é se o paciente apresenta ou não hipertensão:

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/exp/fig5.png)

Legenda: gráfico de barras da distribuição de internações na UTI conforme hipertensão. 0 = não apresenta, 1 = apresenta.

Aparentemente a hipertensão também está associada com risco de internação na UTI.

[Imunodeficiência](https://en.wikipedia.org/wiki/Immunodeficiency) é um tipo de condição clínica em que a habilidade do sistema imune de uma pessoa combater infecções está comprometida ou é completamente ausente.

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/exp/fig6.png)

Legenda: gráfico de barras da distribuição de internações na UTI conforme imunodeficiência. 0 = não apresenta, 1 = apresenta.

Embora o número total de pessoas que apresente imunodeficiência seja relativamente baixo, proporcionalmente mais pessoas com imunodeficiência foram internadas na UTI quando comparadas às pessoas sem imunodeficiência. De forma parecida com o ocorrido para hipertensão.

Uma última categoria abordada no dataframe, para a qual não temos nenhuma informação de sua natureza, chama-se simplesmente "OTHER (outro)". Podemos observá-la:

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/exp/fig7.png)

Legenda: gráfico de barras da distribuição de internações na UTI conforme categoria "OTHER". 0 = não apresenta, 1 = apresenta.

Aparentemente, é uma característica frequente na população, dado que a maior parte a apresenta, mas não parece haver relação com a probabilidade de internação em UTI.

### Variáveis contínuas:

Sabemos que o número de variáveis contínuas é muito maior que o número de variáveis categóricas. A princípio é bastante inviável representar cada variável contínua na forma gráfica como temos feito. Segundo que, mesmo representando-as com uma estatística, nem todas as variáveis contínuas serão informativas, tanto para nossa análise exploratória, como para o desenvolvimento do modelo futuro. A fim de simplificar as visualizções para ter intuições sobre o comportamento dos dados, resumi o conjunto de dados com a variância das variáveis contínuas:

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/exp/fig8.png)

Um conjunto de dados com variâncias muito baixas pode não ser informativo para um modelo de classificação de Machine Learning.

#### Correlações

Um próxima preocupação foi examinar se há alta correlação entre as variáveis ao longo do dataframe. Se muitas variáveis forem altamente correlacionadas, isso também pode [enviesar](https://towardsdatascience.com/why-feature-correlation-matters-a-lot-847e8ba439c4) nosso modelo de machine learning. Examinei o comportamento da matriz de correlação. A matriz de correlação indica o quão correlacionadas são duas variáveis, indo de -1 a 1, com o 0 indicando ausência completa de correlação, valores negativos indicam correlação inversamente proporcional e positivos, correlação diretamente proporcional.

A partir da inspeção inicial da matriz de correlação, alguns pontos sobre os dados ficaram muito claros:

1.   Há variáveis que apresentam alta correlação, inclusive variáveis idênticas, cujo índice de correlação é == 1.
2.   Algumas correlações da matriz superior como "ALBUMIN_DIFF" e "BE_ATERIAL_DIFF" apresentam valores NaN. Teoricamente isso não era esperado, e indica que essa variável na verdade não apresenta variação.

As variáveis "DIFF" parecem não ter variação, indicado por um desvio padrão de 0. Valores com variação muito baixa, ou zero, [devem ser removidas](https://community.dataquest.io/t/feature-selection-features-with-low-variance/2418) de um modelo de Machine Learning, pois não são informativas.

Após a remoção desses dados, o outro problema apontado permanece: as diferentes estatísticas utilizadas para descrever o mesmo tipo de medição são altamente correlacionadas, quando não, idênticas. Mediana, média, mínimo e máximo de uma mesma medição são idênticas.

Prossegui com a remoção de colunas do conjunto de dados que apresentassem ao menos uma correção > 0.95 com qualquer outra variável, que não ela mesma.

Após a remoção dessas colunas, construí um mapa de calor para melhor visualização da correlação final:

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/exp/fig9.png)

Quanto mais escuros os quadrados no gráfico, maiores os valores de correlação. Em geral, temos que as variáveis restante são bastante não correlacionadas, pois a maior parte da distribuição no mapa é de cores claras.

Dessa forma, concluí a limpeza e análise exploratória de variáveis e prossegui para a construção, implementação e teste dos modelos de Machine Learning.

# Busca de modelos

###### Link de acesso: https://github.com/RPGraciotti/BootCampAlura/blob/main/Projeto_final/TPOT.ipynb

# Seleção de modelos por AutoML

Para a definição de quais modelos de ML buscar, eu optei por uma ferramenta de Auto Machine Learning, o [chamado AutoML](https://medium.com/data-hackers/automated-machine-learning-automl-parte-i-1d3219d57d31). Auto Machine Learning compreende uma série de etapas, de métodos e algoritmos desenvolvidos para buscar modelos, pipelines e otimizar parâmetros para um determinado propósito automaticamente. Existem diversas ferramentas que implementam AutoML, acessíveis a qualquer pessoa que faz uso da linguagem python, mesmo quem não tem muita familiaridade (como este que vos fala). 
Podemos citar o [Auto Sklearn](https://automl.github.io/auto-sklearn/master/), [HyperOpt](http://hyperopt.github.io/hyperopt-sklearn/), [LazyPredict](https://lazypredict.readthedocs.io/en/latest/#), [TPOT](http://epistasislab.github.io/tpot/), etc.

Existem diversas vantagens e [também desvantagens](https://www.kdnuggets.com/2019/03/why-automl-wont-replace-data-scientists.html) associadas com o uso de ferramentas de AutoML, e seu uso também não é necessariamente trivial. Porém, são ferramentas extremamente úteis para se determinar um ponto de partida, podem ser utilizadas em etapas iniciais de um projeto para se delimitar seu escopo, e são ótimas ferramentas didáticas. Para este projeto, eu realizei testes com os algoritmos mencionados seguindo alguns [tutoriais](https://machinelearningmastery.com/automl-libraries-for-python/), e por fim preferi apresentar o uso do TPOT (Tree-based Pipeline Optmization Tool).

Os pontos positivos que me atrairam no uso do TPOT foram a facilidade de implementação e exportação dos resultados, assim como a possibilidade de busca do melhor espaço de parâmetros, que as vezes é realizado com bibliotecas à parte do algoritmo de AutoML. Além disso, é possível determinar que a busca e validação dos modelos seja feita de forma bastante personalizada, a fim de atender a demanda do usuário dependendo do objetivo.

O objetivo do TPOT é determinar não somente um modelo de ML a ser aplicado, mas sim o melhor *Pipeline* a ser utilizado, incluindo etapas de pré-processamento. Também é possível buscar os melhores parâmetros daquele pipeline de forma conjunta. O raciocínio da aplicaçao do TPOT pode ser resumida na seguinte figura (disponível na própria [documentação](http://epistasislab.github.io/tpot/)):

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/tpot-ml-pipeline.png)

Na página ["como usar"](http://epistasislab.github.io/tpot/) o TPOT, há uma série de exemplos e recomendações sobre como utilizar um algoritmo de AutoML, quais os cuidados a serem tomados, as limitações, etc. Estando ciente de que uma busca exaustiva envolve tempo e recursos, eu limitei alguns dos parâmetros de busca para que eu pudesse utilizá-lo de forma eficiente em um menor espaço de tempo e recursos computacionais. É possível instalá-lo facilmente pelo Google Colab, mas para uma busca exaustiva essa ferramenta acaba sendo relativamente limitada pelo limite de conexão. Portanto, os resultados apresentados aqui devem ser interpretados à luz de um exercício didático. Feitas as ressalvas necessárias, vou exemplificar o procedimento adotado.

## O que buscar?

A implementação do TPOT permite personalizações interessantes. Além de parâmetros próprios para manipular a permissividade do modelo como ```generations``` e ```population_size```, como dito anteriormente, é possível fazer a validação cruzada de algum parâmetro que se queira maximizar (ou minimizar parâmetros de erro). O meu objetivo aqui foi buscar alguns parâmetros que julguei os mais relevantes para se avaliar os modelos de forma geral, e também olhando mais a fundo alguns indicadores mais específicos relativos ao tipo de modelo e objetivo original (identificar corretamente a necessidade de pacientes serem admitidos em leitos de UTI). Dessa forma, busquei otimizar os parâmetros de Acurácia (accuracy), Precisão (precision), Recall, ROC AUC e F1 score (média harmônica de precisão e recall); pois usarei eles para fazer as avaliações dos modelos.

**Se nenhum score for declarado, por padrão, o TPOT busca maximizar a acurácia. Assim como ele só permite que seja buscado um parâmetro por vez, ao tentar passar uma lista de scores, ele busca automaticamente a acurácia**.


# Avaliação dos modelos

###### Link de acesso: https://github.com/RPGraciotti/BootCampAlura/blob/main/Projeto_final/Avalia%C3%A7%C3%A3o.ipynb

# Introdução - Avaliação dos modelos/pipelines

Após a etapa anterior de busca dos melhores pipelines, é hora de examinar a fundo quais os modelos escolhidos, quais os parâmetros, e determinar se os modelos são de fato bons modelos e qual seria o pipeline escolhido.

A busca inicial foi feita com base em parâmetros de validação de modelos de Machine Learning. Existe uma [vasta literatura](https://scholar.google.com/scholar?hl=pt-BR&as_sdt=0%2C5&q=evaluation+machine+learning+models&btnG) sobre quais as melhores ferramentas, e quais a melhores formas de se avaliar a qualidade de um modelo de Machine Learning, e é sempre um tópico [bastante debatido](https://scholar.google.com/scholar?as_ylo=2021&q=evaluation+machine+learning+models&hl=pt-BR&as_sdt=0,5).

## Métricas

As métricas de avaliação escolhidas derivam do conceito de matriz de confusão. A matriz de confusão é uma matriz simétrica que representa a distribuição de acertos e erros de predição do modelo. A forma como os eixos são organizados pode variar, mas nesse exemplo vemos o mesmo padrão utilizado pelo sklearn: no eixo x temos os valores preditos e no eixo y os valores reais.

![exemplo de matriz de confusão](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/matrix_2.jpeg)

**Explicação detalhada sobre as métricas no notebook de avaliação**

Todas as classes de métricas variam de 0 a 1, com o melhor resultado possível sendo o mais próximo de 1.
Novamente, as métricas escolhidas para realizar a avaliação final foram "acurácia", "precisão", "AUC", "recall" e "F1 score".

[Outra explicação resumida sobre métricas de avaliação](https://towardsdatascience.com/various-ways-to-evaluate-a-machine-learning-models-performance-230449055f15).

Outra forma de avaliar graficamente a performance dos modelos, é a inspeção de duas curvas: a própria [curva ROC e a curva Precision-Recall (PC)](https://machinelearningmastery.com/roc-curves-and-precision-recall-curves-for-classification-in-python/).

**Explicação detalhada sobre as curvas no notebook de avaliação**

É interessante a comparação entre essas duas curvas, [pois ambas acabam descrevendo aspectos diferentes do modelo](https://dl.acm.org/doi/10.1145/1143844.1143874), embora ambas tenham por objetivo caracterizar a performance deste. Em dados desbalanceados, a curva Precision-Recall pode oferecer uma leitura melhor da peformance do modelo em relação à curva ROC. O objetivo é que as curvas representem um comportamento diferente de um modelo nulo, que seleciona completamente ao acaso o resultado entre valores verdadeiros e falsos; enquanto a curva ROC ideal deve apontar para a "esquerda", a curva PC deve apontar para a "direita" do gráfico.

## Modelos de árvores de decisão de floresta aleatória
Uma outra observação importante a ser feita é que todos os modelos selecionados são do mesmo tipo, de "floresta aleatória de árvores de decisão", ou [**Random Forest**](https://www.section.io/engineering-education/introduction-to-random-forest-in-machine-learning/); com apenas diferenças entre os hiperparâmetros ou etapas de pré-processamento. Cabe então descrever o que é um algoritmo desse tipo. Em sua essência, é um método do tipo "ensemble", que combina diversos outros modelos, no caso, modelos individuais de árvores de decisão. Uma árvore de decisão é um modelo composto por 3 elementos: a raíz, nós de decisão, e nós de escolha ("folhas"). A cada passo, os dados são separados de acordo com um critério de decisão baseado nas features do conjunto de dados. Por exemplo, o primeiro nó de decisão pode ser, no nosso caso, "O paciente tem mais de 65 anos?". Os dados serão separados conforme essa feature, e uma nova decisão é tomada, até que não seja mais possível separar os dados e o resultado seja a previsão final (no caso, uma classificação binária). 

![Exemplo de uma árvore de decisão](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/decision-tree-nodes.png)

Os critérios adotados para definir a melhor forma de separar os dados, a ordem com que as features são testadas e a profundidade de árvore (número de etapas) são todos parâmetros do modelo. O que o Random Forest faz é, em essência, criar várias árvores diferentes, com parâmetros diferentes, aleatorizando a forma como as features são ordenadas em cada árvore. Ao final, o algoritmo busca o resultado de cada árvore individualmente como uma "votação" para determinar o resultado final, o mais "votado" é o resultado escolhido:

![Exemplo de Random Forest](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/random-forest-classifier.png)

# Avaliação

## Modelo que maximiza acurácia: m1

A primeira visualização do comportamento do modelo é feita através da matriz de confusão:
![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/results/fig1.png)

**Como interpretar a matriz de confusão normalizada?**

Primeiro, a soma de todos os valores é 1 (a representação gráfica costuma arredondar alguns valores para baixo).

Partindo do exemplo da introdução, lendo os quadrantes linha por linha:

> Primeiro temos o valor de TN, ou seja, quantos valores realmente negativos o modelo prediz como negativos;

> Segundo temos o valor de FP, ou seja, quantos valores foram preditos como positivos mas na realidade são negativos;

> Terceiro temos o valor de FN, ou seja, quantos valores foram preditos como negativos mas na realidade são positivos;

> Quarto temos o valor de TP, ou seja, quantos valores realmente positivos foram preditos como positivos.

O objetivo de um bom modelo é maximizar as taxas de verdadeiros (no caso, a diagonal principal) e minimizar as taxas de falsos. 

**O que temos nesse primeiro exemplo?**

Como padrão geral, veremos que a taxa de verdadeiros negativos é sempre alta. Isso é um bom começo, mas não resume tudo. Neste modelo de busca de melhor acurácia, o desempenho das outras classes é relativamente semelhante. Esse padrão não necessariamente é o ideal, dado que o valor de falsos negativos é bastante alto. Esse é um ponto muito importante do problema em questão: se o modelo tem alto valor de falsos negativos, estamos dizendo que muitas das pessoas que precisariam ser internadas em um leito de UTI, não precisam. E isso é um problema bastante grave que, ao meu ver, deve ser minimizado o máximo possível. 

À primeira inspeção, os valores além de TN não são muito dissimilares entre si, o que indica uma performance razoável de um modelo, com taxas de falsos também não muito altas, quando comparado a alguns padrões que veremos em sequência.

---

A próxima visualização é a implementação de validação cruzada com estratificação repetida. O algoritmo aplica a estratificação em 5 classes, 10 vezes, totalizando 50 avaliações. O resultado de cada métrica é representado então através de um boxplot:

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/results/fig2.png)

Neste gráfico, a idéia é que os resultados tenham distribuições parecidas, preferencialmente centradas em valores altos. E, como teste adicional da performance da busca por parâmetros, esperamos que a métrica buscada seja a de valor mais alto. Podemos ver que as métricas com valores mais altos foram de ROC_AUC e de acurácia. Entretanto, a métrica de Recall é relativamente baixa, empurrando o valor de F1 também para baixo. 

Confirmando o padrão da matriz de confusão, isso indica que este modelo tem uma performance razoável na maioria das vezes, pois incorre em uma taxa não desprezível de falsos negativos, abaixando valor de recall e F1; mas também uma taxa baixa de falsos positivos.

---

Por último, o comportamento das curvas ROC-AUC e Precision-Recall: recapitulando, a performance dos modelos é medida conforme mais distante é a curva do modelo da curva de modelo nulo: quanto mais curvada em direção ao canto superior esquerdo para a curva ROC, e quanto mais curvada em direção ao canto superior direito para a curva Precision-Recall.

Também podemos visualizar o valor de AUC a partir da curva ROC, lembrando que trata-se da área sob essa curva.

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/results/fig3.png)

Podemos observar que, por mais que a curva ROC apresente um bom resultado, a curva Precision-Recall não apresenta a forma esperada, ficando relativamente próxima de uma constante, ainda que acima do modelo nulo. Isso indica que esse modelo é penalizado ao prever preferencialmente a classe majoritaria (0 =  não necessita de internação).

A diferença dos padrões resultantes dessas duas curvas já é um forte indicativo de que a performance deste modelo não é a ideal.

## Modelo que maximiza precisão: m2

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/results/fig4.png)

De cara, podemos observar que esse modelo tem um valor muito alto de falsos negativos, e baixo de falsos e verdadeiros positivos. Nessa escala, esse efeito é completamente indesejado, pois buscamos sempre prever o maior número possível de casos verdadeiros de pessoas que necessitam de UTI.

---

Veremos como o modelo se comporta à luz dos outros parâmetros de teste:

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/results/fig5.png)

Como esperado pela alta taxa de falsos negativos, o Recall e F1 desse modelo são muito baixos. Curiosamente também, os valores de precisão apresentam uma dispersão maior do que o modelo de maximização de acurácia. Este não é um resultado esperado, dado que este modelo deveria otimizar justamente o parâmetro de precisão.

---

Com relação às curvas ROC e PC:

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/results/fig6.png)

A trajetória da curva ROC-AUC mantém-se relativamente semelhante; enquanto que a curva PC na verdade se mostrou melhor que o modelo anterior, não apresentando a queda brusca observada logo no início do outro modelo.

De forma geral, isso poderia representar uma melhora deste modelo em relação ao anterior, mas a distribuição dos parâmetros por validação cruzada indica que a performance geral deste modelo é mais baixa.

## Modelo que maximiza AUC: m3

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/results/fig7.png)

O modelo que busca maximizar o AUC apresenta um resultado muito similar ao modelo anterior. Isso é esperado, dada a sua semelhança: este modelo possui apenas um passo a mais quando comparado ao anterior, sem muitas alterações nos hiperparâmetros.

Novamente, temos um valor muito alto de falsos negativos e baixo de verdadeiros positivos.

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/results/fig8.png)

Novamente, como esperado, o padrão encontrado é de baixa recall e F1, com grande variação na precisão.

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/results/fig9.png)

Aqui ainda vemos o padrão persistente de performance relativamente boa prevista pela curva ROC, com o padrão da curva PC também melhor em relação ao modelo que maximiza acurácia.

## Modelo que maximiza recall: m4

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/results/fig10.png)

Um padrão curioso agora se faz presente. Ao tentar maximizar o recall, obtemos o resultado esperado: diminuimos o valor de falsos negativos e aumentamos o valor de verdadeiros positivos. Porém, também aumentamos o valor de falsos positivos. A longo prazo, esse efeito também é indesejado: a internação de uma pessoa em um leito de UTI é um procedimento muito custoso, tanto para o sistema de saúde quanto para o paciente. Se o modelo prevê que muitos pacientes que não precisam de UTI sejam internados, isso pode gerar uma sobrecarga do sistema de saúde. A médio prazo porém, esse efeito pode ser amenizado pois o paciente pode acabar não ficando muito tempo internado; mas essa interpretação deve ser feita com muita cautela.

Para efeito do exercício, podemos estar mais interessados em um modelo com maior taxa de falsos positivos em relação aos falsos negativos.

---

Vamos examinar os outros resultados do modelo:

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/results/fig11.png)

Como esperado, este modelo apresenta uma melhora significativa nos valores de recall, e, por consequência, de F1. Também apresenta valores razoáveis das outras métricas, com alta acurácia, a custo de uma precisão um pouco menor, mas com menor variação.

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/results/fig12.png)

Esse modelo apresenta uma distorção em torno dos valores médios, de 0.4 para taxa de falso positivo e de recall. É um resultado difícil de ser interpretado, que leva à uma pequena queda na AUC; mas o padrão geral das curva PC é o melhor representado até aqui.

---

Por último, vamos examinar o modelo que maximiza o valor de F1.

## Modelo que maximiza F1: m5

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/results/fig13.png)

Novamente este modelo aponta para o resultado dos modelos inicais: maiores taxas de predição de valores 0, porém sem que a taxa de falsos negativos seja muito mais alta que as demais, como no caso dos modelos de precisão e ROC.

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/results/fig14.png)

As distribuições das métricas também se assemelham ao resultado do modelo de acurácia, mas também há uma variação muito grande da própria métrica F1 e recall relativamente mais baixo.

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/results/fig15.png)

Por fim, o diagnóstico das curvas também indica que uma queda de performance à luz da curva PC, ficando pouco acima do modelo nulo.

# Conclusões gerais

1. Não existe um modelo ideal, e nem sempre o melhor modelo é um excelente modelo. Ao buscar maximizar uma única métrica de avaliação do modelo, o algoritmo de busca por AutoML acabou por penalizar os modelos em direções diferentes: ao tentar diminuir a taxa de falsos negativos, aumenta a taxa de falsos positivos e vice-versa. Os melhores modelos possivelmente residem no meio do caminho entre esses dois compromissos.

2. No nosso caso, temos um objetivo claro: identificar corretamente a necessidade de um paciente ser internado em um leito de UTI com covid. Dessa forma, tanto os modelos que maximizaram a acurácia, F1 e recall atendem a esses propósitos. Se o objetivo for buscar a menor taxa de falsos negativos, o melhor modelo é o que maximiza o **recal**. Se objetivo é pesar o compromisso entre as taxas de falsos negativos e falsos positivos, o modelo que maximiza **acurácia** atende melhor a esse propósito.

Uma ressalva importante a ser observada também é que essa melhor habilidade do modelo recall medida por menor variação dos scores de validação cruzada poder ser resultado do maior tempo de otimização dos hiperparâmetros. Como vimos no procedimento de busca de modelos e hiperparâmetros do TPOT, a recall é a métrica mais sensível, pois apresenta maior variação de ganho de performance, o que me motivou a aplicar mais tempo na busca. Talvez aplicar mais tempo na busca dos outros parâmetros também levasse a resultados melhores para os outros parâmetros.

Outro padrão interessante a ser discutido é como as diferentes métricas de avaliação relevam propriedades diferentes dos modelos. Se examinássemos somente o valor de AUC e curva ROC, provavelmente julgaríamos que todos os modelos seriam bons modelos. Vimos que isso não necessariamente é verdade, pois a curva ROC tende a dar grande importância à taxa de verdadeiros negativos, que, muitas vezes, não é o parâmetro mais informativo de um modelo. No nosso caso, todos os modelos tiveram taxas igualmente boas de verdadeiros negativos, e isso também é uma consequência do desbalanceamento dos nossos dados, com mais classes 0 do que 1. Essa distorção da leitura da curva ROC é [bastante discutida](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4349800/) na literatura, e não é uma discussão [recente](https://dl.acm.org/doi/10.1145/65943.65945). Por isso, olhamos também para a curva PC e a distribuição dos scores de métricas de avaliação por validação cruzada. A vantagem de utilizar a validação cruzada é que o processo foi repetido diversas vezes e os resultados foram examinados de forma a incorporar a aleatoriedade do processo de separação em dados de treino e dados de teste. 

Uma boa avaliação de um modelo depende de vários fatores, assim como uma boa seleção de modelos. A efetividade geral dos modelos finais pode ser melhorada, e muito, a depender da busca de outros parâmetros. Se escolheremos o modelo que maximiza Recall, teremos uma previsão muito boa dos casos positivos, mas podemos acabar superestimando esses valores e prevendo que pessoas que não necessitam de UTI sejam internadas. Esse também não é o resultado mais desejado, pois, como discutimos, gera uma sobrecarga no sistema de saúde. Em situações menos estressantes, modelos com alto recall mas que geram altas taxas de falso positivo podem ser melhor aplicados, em que o custo de um "excesso de cuidado" pode ser mais baixo do que o caso apresentado. A fim de buscar um melhor compromisso entre esses tipos de erro nas previsões, o primeiro passo seria realizar buscas mais exaustivas, tanto de modelos quanto de hiperparamêtros. Como vimos, a própria documentação do TPOT recomenda que a busca seja feita de forma exaustiva. Porém, isso requer uma alocação muito grande de recursos, o que pode limitar o escopo do projeto.

Outras possibilidades incluem o uso de outras ferramentas de AutoML. Afinal, temos uma grande variedade de algoritmos à disposição, e essa busca também pode ser uma busca exaustiva: "qual o melhor algoritmo de AutoML?" não é uma pergunta muito diferente de "qual o melhor modelo?". A separação dos passos de busca de melhor modelo e melhores hiperparâmetros também pode ser frutífera, iniciando-se primeiro a busca por tipos de modelos mais recomendados para o tipo de problema em questão, e depois o teste de hiperparâmetros, com ferramentas como [Grid Search](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.GridSearchCV.html). Ainda uma outra possibilidade que não explorei é a busca por [Feature Importance](https://machinelearningmastery.com/calculate-feature-importance-with-python/), que pode ajudar a revelar quais as características mais importantes dos dados para a previsão. Por exemplo, um estudo de Feature Importance pode ajudar a resolver algumas questões muito debatidas no cenário da COVID: quais são os [grupos de risco](https://coronavirus.saude.mg.gov.br/blog/84-grupos-de-risco-para-covid-19)? O que será que influenciou mais as decisões dos algoritmos de Machine Learning, a idade das pessoas ou doenças pré-existentes? Vimos marginalmente essa discussão na análise exploratória de dados, mas apenas fizemos uma comparação visual dessas categorias, que poderiam ser melhor exploradas com feature importance, um próximo passo interessantes para o projeto.
