# Resumo

# Introdução Geral

# Limpeza e análise exploratória

###### Link de acesso: https://github.com/RPGraciotti/BootCampAlura/blob/main/Projeto_final/Exploratoria.ipynb

Após a primeira inspeção do dataframe, notamos que:

1. Há diversos tipos de dados como floats (números decimais), ints (números inteiros) e objects (objetos de outros tipos). 
2. Há certos tipos de dados que parecem ser do tipo discreto e que não mudam para o mesmo paciente, e dados contínuos, que diferem entre as diferentes leituras de sinais vitais. 
3. Há uma quantidade de dados faltantes nas features contínuas.
4. Há também uma quantidade de dados faltantes nas features discretas.

No total, em ao menos **metade** do nosso dataset não há informação. Um primeiro caminho poderia ser remover todos esses dados. Isso não é o ideal, já que, de acordo com as recomendações da própria equipe do Sírio Libanês, há algumas etapas que podemos seguir para minimizar a nossa perda de informação.

## Limpeza dos dados

Antes de mais nada, o primeiro passo deve ser remover de prontidão todos os pacientes que deram entrada na UTI na primeira janela de tempo. Essa medida é baseada na recomendação da equipe de utilizar somente os dados disponíveis até o momento da internação.

![resumo dos protocolos indicados pela equipe do Sírio Libanês](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/inbox_1591620_77ca2b4635bc4dd7800e1c777fed9de1_Timeline%20Example%20No.png)

Depois, aplicaremos a função ```fill_table```, o cerne dessa preparação de dados. Essa função irá preencher os dados faltantes utilizando-se das medições anteriores ou posteriores à janela de tempo em questão, baseando-se no princípio de que os sinais vitais não variam ou pouco variam nesses curtos intervalos de tempo. A função irá:

1. Identificar primeiro as linhas vazias de cada paciente anterior ao momento da internação, pois não utilizará os dados obtidos somente após a internação.
2. Preencher o dado faltante primeiro com a medição do intervalo **posterior (backfill)**, desde que o paciente ainda não tenha dado entrada na UTI e, quando necessário, preencher com a medição do intervalo **anterior (forwardfill)**.

Essa diferenciação se faz necessária porque a princípio não temos informação sobre o momento exato das medidas aferidas na janela de internação da UTI: elas podem ter sido tomadas logo antes ou logo após a admissão na UTI.

Após a aplicação do conjunto de funções anteriores, o valor de dados faltantes caiu para 9%.

Valores faltantes também aparecem na seguinte situação: se um paciente tem uma lacuna de medição exatamente na janela anterior à internação e possui dados após a internação, estes valores não serão utilizados para preencher a lacuna anterior.

O que podemos fazer agora é aplicar uma nova função que determinará o seguinte: seguindo as recomendações no Kaggle, o que precisamos é resumir os dados utilizando somente a informação se o paciente deu entrada ou não na UTI, e utilizar os dados somente da primeira janela. É uma simplificação dos dados que parte do mesmo princípio de que se espera que não haja muita variação entre as medições de sinais vitais. Temos ciência de que pode não ser o cenário ideal para um modelo mais complexo e potencialmente mais informativo, mas essa simplificação torna o processo bastante eficiente: precisamos detectar rapidamente a possibilidade de um paciente ser admitido na UTI.

É necessário fazer essa etapa somente depois de preencher dados faltantes, pois muitos pacientes podem ter medidas faltantes em janelas iniciais, mas ainda assim terem medidas em momentos anteriores à internação. Se fizeremos o resumo dos dados antes, essas medidas seriam completamente perdidas e potencialmente alguns pacientes ficariam de fora do modelo final. Lembrando então agora que teremos um dataset resumido, com apenas uma linha por paciente.

Por último, ainda resta 6% de dados faltantes, decorrentes do seguinte cenário: pacientes que só tiveram suas primeiras medidas aferidas após a janela de internação na UTI. Nesse caso, nada mais pode ser feito a não ser a remoção total desses pacientes.

O cenário final ficou o seguinte: 
```
Data frame original = (1925 linhas, 231 colunas)
Após limpeza = (294 linhas, 231 colunas)
```

## Análise exploratória

### Variáveis categóricas:

Primeiro, vamos observar qual o número absoluto de pessoas que foram ou não internadas na UTI:
```
0    189
1    105
```
Em dados categóricos, 0 geralmente indica a condição negativa (no nosso caso, não foram internadas na UTI), e 1 a condição positiva (foram internadas). Mas também pode representar outras classes, como veremos adiante.

Temos então que 189 pessoas não foram para UTI e 105 sim, mostrando um resultado relativamente desbalanceado, quase o dobro de pessoas tem a categoria 0.

Categorias de idade:
Muito se discute se a probabilidade de um paciente ser internado ou não na UTI está associada com a sua idade, seja em valores absolutos, ou se o fato do paciente ser idoso ou não é determinante dessa possibilidade. A primeira variável que carrega essa informação no dataset é a coluna AGE_ABOVE65, que identifica se o paciente tem mais ou menos de 65 anos:

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/exp/fig1.png)

Legenda: gráfico de barras da distribuição de internações na UTI conforme idade maior ou menor que 65 anos. 0 para menor, 1 para maior.

Podemos observar que mesmo não havendo um aumento no número absoluto de internações em pessoas com mais de 65 anos em relação às internações com menos de 65 anos, há um aumento considerável proporcionalmente. Há uma proporção de 1/3 dos pacientes precisando de internação entre < 65, e uma proporção de 1/2 entre os > 65.

Porém, há um outro dado relativo a idade que é a idade absoluta:

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/exp/fig2.png)

Legenda: gráfico de barras da distribuição de internações na UTI conforme percentis de faixa etária. 10th indica pacientes de até 10 anos, e assim sucessivamente.

Podemos perceber que conforme maior a idade, sobretudo nos idosos acima de 70 anos, um maior número de pessoas é internada na UTI em relação às pessoas que não são internadas.

Gênero:
E dados sobre gênero?

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/exp/fig3.png)

Legenda: gráfico de barras da distribuição de internações na UTI conforme gênero. 0 = masculino, 1 = feminino.

Podemos ver que, proporcionalmente mais homens foram internados na UTI do que mulheres.

Doenças pré-existentes:
Um outro tipo de dado disponível do dataframe é uma combinação de 6 grupos de doenças pré-existentes em cada paciente. Devido os protocolos de anonimização, não sabemos quais são esses tipos de doenças, mas podemos examinar sua distribuição também:

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/exp/fig4.png)

Legenda: gráficos de barras empilhadas para indicar a distribuição dos grupos de doenças. Atente-se ao fato de que a visualização é um pouco diferente, devido à diferença na natureza dessa variável no dataframe: ele indica a proporção de internações em relação ao total na mesma barra.

Vemos que os grupos de doenças 2 e 4 apresentam um alto risco de internação em UTI, quando comparados aos outros grupos que tem uma distribuição mais proporcional.

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

Sabemos que o número de variáveis contínuas é muito maior que o número de variáveis categóricas. A princípio é bastante inviável representar cada variável contínua na forma gráfica como temos feito. Segundo que, mesmo representando-as com uma estatística, nem todas as variáveis contínuas serão informativas, tanto para nossa análise exploratória, como para o desenvolvimento do modelo futuro. Uma primeira abordagem é resumir as medições em alguma estatística de interesse. Eu escolhi olhar primeiro a variância das nossas variáveis contínuas, para identificar como se comportam as várias variáveis que temos. Um conjunto de dados com variâncias muito baixas pode não ser informativo para um modelo de classificação de Machine Learning.

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/exp/fig8.png)

Resumidamente, isso indica que muitas variáveis apresentam pouca variação, o que pode enviesar o algoritmo de classificação.

#### Correlações

Um próxima preocupação deve ser examinar se há alta correlação entre as variáveis ao longo do dataframe. Se muitas variáveis forem altamente correlacionadas, isso também pode [enviesar](https://towardsdatascience.com/why-feature-correlation-matters-a-lot-847e8ba439c4) nosso modelo de machine learning. Podemos examinar essa correlação através da uma matriz de correlação.

A matriz de correlação indica o quão correlacionadas são duas variáveis, indo de -1 a 1, com o 0 indicando ausência completa de correlação, valores negativos indicam correlação inversamente proporcional e positivos, correlação diretamente proporcional.

A partir da inspeção inicial da matriz de correlação, dois pontos explicitados anteriomente ficam muito claros:

1.   Há variáveis que apresentam alta correlação, inclusive variáveis idênticas, cujo índice de correlação é == 1.
2.   Algumas correlações da matriz superior como "ALBUMIN_DIFF" e "BE_ATERIAL_DIFF" apresentam valores NaN. Teoricamente isso não era esperado, e indica que essa variável na verdade não apresenta variação.

As variáveis "DIFF" parecem não ter variação, indicado pelo desvio padrão de 0.
Valores com variação muito baixa, ou zero, [devem ser removidas](https://community.dataquest.io/t/feature-selection-features-with-low-variance/2418) de um modelo de Machine Learning, pois não são informativas.

Após a remoção desses dados, o outro problema apontado permanece: as diferentes estatísticas utilizadas para descrever o mesmo tipo de medição são altamente correlacionadas, quando não, idênticas. Mediana, média, mínimo e máximo de uma mesma medição são idênticas.

O que fazer? Pode ser interessante então aplicar a remoção de colunas com um valor de corte de alta correlação, por exemplo, > 95%, pois vamos remover também as variáveis idênticas.

Após a remoção dessas colunas, construí um mapa de calor para melhor visualização da correlação final:

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_final/figs/exp/fig9.png)

Quanto mais escuros os quadrados no gráfico, maiores os valores de correlação. Em geral, temos que as variáveis restante são bastante não correlacionadas, pois a maior parte da distribuição no mapa é de cores claras.

Dessa forma, concluímos então a limpeza e análise exploratória de variáveis. Seguiremos no próximo caderno para a construção, implementação e teste dos modelos de Machine Learning.

# Busca de modelos

###### Link de acesso: https://github.com/RPGraciotti/BootCampAlura/blob/main/Projeto_final/TPOT.ipynb

# Avaliação dos modelos

###### Link de acesso: https://github.com/RPGraciotti/BootCampAlura/blob/main/Projeto_final/Avalia%C3%A7%C3%A3o.ipynb

# Conclusão Final

Não existe um modelo ideal, e nem sempre o melhor modelo é um excelente modelo. Ao buscar maximizar uma única métrica de avaliação do modelo, o algoritmo de busca por AutoML acabou por penalizar os modelos em direções diferentes: ao tentar diminuir a taxa de falsos negativos, aumenta a taxa de falsos positivos e vice-versa. Os melhores modelos possivelmente residem no meio do caminho entre esses dois compromissos.

No nosso caso, temos um objetivo claro: identificar corretamente a necessidade de um paciente ser internado em um leito de UTI com covid. Dessa forma, tanto os modelos que maximizaram a acurácia, F1 e recall atendem a esses propósitos. De forma geral, o modelo que maximiza a acurácia é o que melhor distribui os resultados entre as classes não verdadeiro negativo da matriz de confusão. O modelo que maximiza F1 apresenta resultados um pouco mais consistentes à luz das curvas ROC e PR, com resultados praticamente idênticos na distribuição dos scores de métricas por validação cruzada. Por fim, o modelo que maximiza recall é penalizado por uma taxa maior de falso positivo, mas é o que tem a maior taxa de verdadeiros positivos, e distribuições dos scores de validação cruzada com médias mais altas, com menor variação.

Uma ressalva importante a ser observada também é que essa melhor habilidade do modelo recall medida por menor variação dos scores de validação cruzada poder ser resultado do maior tempo de otimização dos hiperparâmetros. Como vimos no procedimento de busca de modelos e hiperparâmetros do TPOT, a recall é a métrica mais sensível, pois apresenta maior variação de ganho de performance, o que me motivou a aplicar mais tempo na busca. Talvez aplicar mais tempo na busca dos outros parâmetros também levasse a resultados melhores para os outros parâmetros.

Outro padrão interessante a ser discutido é como as diferentes métricas de avaliação relevam propriedades diferentes dos modelos. Se examinássemos somente o valor de AUC e curva ROC, provavelmente julgaríamos que todos os modelos seriam bons modelos. Vimos que isso não necessariamente é verdade, pois a curva ROC tende a dar grande importância à taxa de verdadeiros negativos, que, muitas vezes, não é o parâmetro mais informativo de um modelo. No nosso caso, todos os modelos tiveram taxas igualmente boas de verdadeiros negativos, e isso também é uma consequência do desbalanceamento dos nossos dados, com mais classes 0 do que 1. Essa distorção da leitura da curva ROC é [bastante discutida](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4349800/) na literatura, e não é uma discussão [recente](https://dl.acm.org/doi/10.1145/65943.65945). Por isso, olhamos também para a curva PC e a distribuição dos scores de métricas de avaliação por validação cruzada. A vantagem de utilizar a validação cruzada é que o processo foi repetido diversas vezes e os resultados foram examinados de forma a incorporar a aleatoriedade do processo de separação em dados de treino e dados de teste. 

Uma boa avaliação de um modelo depende de vários fatores, assim como uma boa seleção de modelos. A efetividade geral dos modelos finais pode ser melhorada, e muito, a depender da busca de outros parâmetros. Se escolheremos o modelo que maximiza Recall, teremos uma previsão muito boa dos casos positivos, mas podemos acabar superestimando esses valores e prevendo que pessoas que não necessitam de UTI sejam internadas. Esse também não é o resultado mais desejado, pois, como discutimos, gera uma sobrecarga no sistema de saúde. Em situações menos estressantes, modelos com alto recall mas que geram altas taxas de falso positivo podem ser melhor aplicados, em que o custo de um "excesso de cuidado" pode ser mais baixo do que o caso apresentado. A fim de buscar um melhor compromisso entre esses tipos de erro nas previsões, o primeiro passo seria realizar buscas mais exaustivas, tanto de modelos quanto de hiperparamêtros. Como vimos, a própria documentação do TPOT recomenda que a busca seja feita de forma exaustiva. Porém, isso requer uma alocação muito grande de recursos, o que pode limitar o escopo do projeto.

Outras possibilidades incluem o uso de outras ferramentas de AutoML. Afinal, temos uma grande variedade de algoritmos à disposição, e essa busca também pode ser uma busca exaustiva: "qual o melhor algoritmo de AutoML?" não é uma pergunta muito diferente de "qual o melhor modelo?". A separação dos passos de busca de melhor modelo e melhores hiperparâmetros também pode ser frutífera, iniciando-se primeiro a busca por tipos de modelos mais recomendados para o tipo de problema em questão, e depois o teste de hiperparâmetros, com ferramentas como [Grid Search](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.GridSearchCV.html). Ainda uma outra possibilidade que não explorei é a busca por [Feature Importance](https://machinelearningmastery.com/calculate-feature-importance-with-python/), que pode ajudar a revelar quais as características mais importantes dos dados para a previsão. Por exemplo, um estudo de Feature Importance pode ajudar a resolver algumas questões muito debatidas no cenário da COVID: quais são os [grupos de risco](https://coronavirus.saude.mg.gov.br/blog/84-grupos-de-risco-para-covid-19)? O que será que influenciou mais as decisões dos algoritmos de Machine Learning, a idade das pessoas ou doenças pré-existentes? Vimos marginalmente essa discussão na análise exploratória de dados, mas apenas fizemos uma comparação visual dessas categorias, que poderiam ser melhor exploradas com feature importance, um próximo passo interessantes para o projeto.
