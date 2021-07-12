###### Acesso ao notebook: https://github.com/RPGraciotti/BootCampAlura/blob/main/Projeto_modulo_3/Projeto_modulo_3.ipynb

# Introdução

Sejam bem-vindo e/ou bem-vinda ao meu projeto do módulo 3 do bootcamp de Data Science Alura! 

Neste módulo, aprendemos a analisar séries temporais e fazer modelos de previsão. Utilizamos dados de [COVID-19](https://www.seade.gov.br/coronavirus/) para analisar as dinâmicas de novos casos e óbitos, na cidade de São Paulo; interpretando os padrões de crescimento e periodicidade nessa série temporal. Também realizamos análises de auto-correlação e correlação cruzadas entre esses dados. Por fim, utilizamos a biblioteca [Facebook prophet](https://facebook.github.io/prophet/) para modelar previsões simples sobre o comportamento dessas séries temporais.

Como objetivo do projeto desse módulo, utilizei uma base de dados de COVID19 de escala nacional, contextualizei algumas cidades do estado de São Paulo para analisar e também examinei dados de vacinação dessas cidades. Vem comigo!

# Tema - Mobilidade pendular na Região Metropolitana de São Paulo: dinâmicas da pandemia e vacinação

A cidade de São Paulo, maior metrópole do Brasil, é o centro da também maior [Região Metropolitana](https://pt.wikipedia.org/wiki/Regi%C3%A3o_Metropolitana_de_S%C3%A3o_Paulo) do Brasil,formada por 39 municípios em intenso processo de conurbação.
![](https://raw.githubusercontent.com/RPGraciotti/BootCampAlura/main/Projeto_modulo_3/figs/SaoPaulo_RM_SaoPaulo.svg)

[Mapa](https://pt.wikipedia.org/wiki/Regi%C3%A3o_Metropolitana_de_S%C3%A3o_Paulo#/media/Ficheiro:SaoPaulo_RM_SaoPaulo.svg) *da Região Metropolitana de São Paulo no Estado*

A população da Região Metropolitana de São Paulo (RMSP) é estimada em cerca de [21 milhões de habitantes](https://pt.wikipedia.org/wiki/Regi%C3%A3o_Metropolitana_de_S%C3%A3o_Paulo). Parte desses habitantes realiza a chamada ["Migração Pendular"](https://mundoeducacao.uol.com.br/geografia/migracao-pendular.htm), o deslocamento diário de uma cidade para outra, geralmente para trabalho e/ou estudo. A RSMP concentra além da cidade de São Paulo, as cidades do chamado [Grande ABC](https://pt.wikipedia.org/wiki/Regi%C3%A3o_do_Grande_ABC), com seus polos industriais, e o munícipo de Guarulhos, onde está localizado o [maior aeroporto da América do Sul](https://pt.wikipedia.org/wiki/Aeroporto_Internacional_de_S%C3%A3o_Paulo-Guarulhos). 

Os dados do [censo de 2000](https://www.scielo.br/j/spp/a/tJPPNLfJmTZLWyh7S5KH8BJ/?lang=pt#) indicam que cerca de 1 milhão de habitantes da RMSP realizam deslocamento pendular entre municípios da RMSP, sendo o município de São Paulo o maior [destino](http://produtos.seade.gov.br/produtos/spp/v19n04/v19n04_06.pdf), recebendo cerca de 590 mil pessoas diariamente. Além de maior polo de serviços, as conexões de transporte público entre os municípios limítrofes da RMSP facilitam essa migração diária.

## COVID19 e São Paulo

A evolução da pandemia de COVID19 foi bastante severa na cidade de São Paulo, sendo uma das cidades mais afetadas e com um dois maiores índices de mortes em [2021](https://g1.globo.com/sp/sao-paulo/noticia/2021/05/27/cidade-de-sao-paulo-ja-tem-mais-mortos-por-covid-19-que-nova-york-e-cidade-do-mexico.ghtml). Sendo a maior e mais ativa cidade do país, é de se esperar que fosse uma das regiões de maior dificuldade de controle da pandemia. De fato, mesmo com [antecipações de feriados](https://www.redebrasilatual.com.br/saude-e-ciencia/2021/03/isolamento-social-fase-roxa-emergencial-sao-paulo/), as taxas de isolamento de São Paulo se mantiveram [baixas](https://g1.globo.com/sp/sao-paulo/noticia/2021/04/03/isolamento-social-nao-mudou-e-ficou-abaixo-dos-45percent-nos-feriados-antecipados-na-cidade-de-sp.ghtml). 
Dessa forma, podemos imaginar que o intenso fluxo de pessoas indo e vindo de São Paulo todos os dias, sobretudo trabalhadores essenciais e da saúde, promoveu aumento das infecções por COVID19 nos municípios de intensa migração pendular na RMSP.

Por outro lado, podemos imaginar também que, devidas às proporções do muncípio de São Paulo e seus [gastos com a pandemia](https://www.terra.com.br/noticias/coronavirus/pandemia-tem-custo-de-r-6-bilhoes-para-a-prefeitura-de-sao-paulo,ad0f8a5eac7695384bb95ff45ace6fd2x7hwbz24.html), a sua rede de saúde também é uma das maiores e mais bem equipadas do Brasil. Podemos esperar que, por exemplo, o ritmo de vacinação nos demais municípios da RMSP também seja influenciado pelas dinâmicas do município de São Paulo.

### Hipóteses

Dessa forma, podemos chegar em algumas hipóteses sobre o efeito da migração pendular na dinâmica da pandemia na RMSP:

1.   O intenso fluxo diário de pessoas entre São Paulo e outros municípios da RMSP influenciaram a dinâmica da pandemia nos municipíos de maior migração pendular, aumentando os números de casos e mortes;
2.   Os aportes do sistema de saúde de São Paulo e capilaridade do SUS influencia a dinâmica de vacinação; de forma a observarmos ritmos e taxas de vacinação semelhantes.

Portanto, nesse projeto eu examinei alguns municípios da RMSP, comparando as dinâmicas de casos, mortes e vacinação com o município de São Paulo. Por último, modelei previsões sobre o total de vacinas aplicadas em São Paulo e nos municípios escolhidos.

## Municípios escolhidos

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_modulo_3/figs/1024px-Mapa-RMSP-subregions.svg.png)

[Mapa](https://pt.wikipedia.org/wiki/Regi%C3%A3o_Metropolitana_de_S%C3%A3o_Paulo) *das sub-regiões da RMSP*

**Vermelho: sub-região Norte, Verde: sub-região Leste, Azul-claro: sub-região Sudeste, Azul-escuro: sub-região Sudoeste, Amarelo: sub-região Oeste**

Como estamos falando de 39 municípios, separados em [5 sub-regiões](https://governo-sp.jusbrasil.com.br/legislacao/1028148/lei-complementar-1139-11), selecionei apenas alguns municípios para análise. Me baseei em [um levantamento](http://produtos.seade.gov.br/produtos/spp/v19n04/v19n04_06.pdf) da [SEADE](https://www.seade.gov.br/), que identificou os municípios da RMSP com maior fluxo para São Paulo.

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_modulo_3/figs/munic.png)

*Modificado de: Aranha, São Paulo em Perspectiva v. 19, n.4, pg. 96-109, 2005*

Como podemos ver nos dois mapas acima, podemos destacar os municípios de **Osasco**, **Guarulhos**, **São Bernardo do Campo**, **Francisco Morato** e **Taboão da Serra** entre os municípios com migração de > 20.000 pessoas diariamente ao município de São Paulo. Selecionei estes municípios para as análises por também serem um de cada sub-região.

Importante ressaltar que, no levantamento utilizado como base, há um índice decrescente de migração entre os primeiros colocados: 
Osasco (80.762 indivíduos que realizam migração pendular) > Guarulhos (78.949) > São Bernardo do Campo (37.706). No estudo não há identificação do número de indivíduos que se deslocam de Francisco Morato e Taboão da Serra pra São Paulo.

### Uma breve descrição sobre os conjuntos de dados:

Para os novos casos e mortes de COVID19, utilizei a base do [Brasil.io](https://brasil.io/dataset/covid19/caso_full/), que compila os dados de boletins diários das secretarias de saúde em escala nacional, utilizando especificamente o conjunto "casos_full". Como alguns dos municípios escolhidos apresentaram algumas vezes valores negativos de novos casos e mortes, provavelmente devido a correções de registro de dias anteriores, eu optei por trocar esses valores para 0.

Para os dados de vacinação do estado de São Paulo, que utilizei para nortear as análises de vacinação dos municípios, utilizei a base de dados do Governo do Estado, o [Vacinometro](https://vacinaja.sp.gov.br/vacinometro/) do estado. 

Já para os dados de vacinação de cada município, eu utilizei o sistema do [Ministério da Saúde](https://qsprod.saude.gov.br/extensions/DEMAS_C19Vacina/DEMAS_C19Vacina.html), que permite a pesquisa dos dados de vacinação diária por município individualmente.

Os conjuntos de dados foram manipulados para conter somente os dados até o dia 30 de Junho, pois a cada dia o total é atualizado em todas as bases, e o acesso em datas diferentes pode resultar em totais diferentes.

# Análises

## Casos diários

Vamos primeiro olhar para a dinâmica de novos casos de covid em São Paulo e nos nossos municípios escolhidos. A expectativa é de que as trajetórias sejam parecidas entre os municípios e São Paulo, guardadas as devidas proporções:

![](https://raw.githubusercontent.com/RPGraciotti/BootCampAlura/main/Projeto_modulo_3/figs/fig_1.png)

A dinâmica de São Paulo é marcada pelo aumento de casos nos primeiros meses da pandemia, seguido de estabilidade nos meses de Julho-Agosto de 2020, seguido de queda até o final de 2020. Porém, há uma retomada elevada de novos casos no início de 2021, atingindo picos mais altos durante o mês de Abril. Vemos por último, uma relativa queda e estabilidade em Maio e Junho de 2021.

Podemos ver, portanto, que a dinâmica é de fato muito parecida entre São Paulo e as cidades de maior migração. São Bernado do Campo parece ter respondido melhor a pandemia em 2021 que outras cidades, assim como Taboão da Serra, que apresentaram os maiores picos durante 2020. Francisco Morato é a [menor cidade analisada](https://pt.wikipedia.org/wiki/Lista_de_munic%C3%ADpios_de_S%C3%A3o_Paulo_por_popula%C3%A7%C3%A3o), e de maior distância da capital, mas apresentou dinâmica bastante parecida com São Paulo.

Há uma outra forma de se analisar a correspondência das séries temporais:
Na matemática, a análise de [correlação cruzada](https://en.wikipedia.org/wiki/Cross-correlation), pode ser utilizada para examinar a correspondência entre duas séries temporais. Da página da wiki (tradução livre): 

`Suponha duas séries temporais idênticas, ao calcular-se a integral do produto das duas funções a cada posição, deslocando-se uma série em relação à outra, o produto é máximo quando as duas séries se alinham perfeitamente.`

Portanto, podemos estimar o quanto é necessário "atrasar" uma série temporal que para que ela apresente maior correlação com a outra, em séries temporais não idênticas. Dessa forma, séries temporais de dinâmicas parecidas devem apresentar maior correspondência com menores atrasos, e conforme se distanciam, menor correspondência. Vamos aplicar esse tipo de análise aos nossos dados:

Primeiro, realizei a análise de auto-correlação do município de São Paulo com ele mesmo, depois a correlação cruzada de São Paulo com os outros municípios.

![](https://raw.githubusercontent.com/RPGraciotti/BootCampAlura/main/Projeto_modulo_3/figs/fig_2.png)

Para interpretar esse gráfico, primeiro nos atentemos aos eixos. O eixo x mostra o quanto a segunda série temporal foi deslocada em relação a primeira (no caso, em dias), e o eixo y representa o produto da integral das duas séries (rescalonada para os valores -1,1). Quando a correlação é máxima, temos o valor de 1. 

Tomando a auto correlação de São Paulo como exemplo, vemos que no início há o pico esperado de valor 1, quando as duas séries são idênticas. Ao aumentarmos o deslocamento (lag), temos uma redução desse valor, mas temos um leve aumento mais à frente no tempo. Isso está indicando para nós que a dinâmica do início se repetiu em algum momento. No nosso caso, tivemos de fato uma repetição de alta de casos seguida de uma queda no início de 2021.

Dessa forma, podemos observar então que há grande correlação entre as dinâmicas entre São Paulo e as 3 maiores cidades, Osasco Guarulhos e São Bernardo do Campo, que mantiveram valores altos nos primeiros momentos. Para Taboão da Serra, observamos uma queda mais rápida nos valores de correlação no começo da análise, quando comparado com as outras cidades.

Por outro lado, por mais que a primeira análise tenha revelado padrões similares entre São Paulo e Francisco Morato, a correlação se manteve próxima de 0 na maior parte do tempo. Olhando novamente a dinâmica de novos casos do município, na verdade vemos que Francisco Morato apresentou uma diferença de intensidade muito maior que São Paulo. Por mais que ambas cidades tenham repetido o aumento de casos em 2021, o aumento de novos casos em Francisco Morato foi maior que o aumento de São Paulo em relação ao ano anterior, e isso poderia estar por trás das menores correlações observadas.

## Mortes

Outro aspecto importante para se analisar a dinâmica de transmissão da COVID19 é o número de mortes diárias. 

![](https://raw.githubusercontent.com/RPGraciotti/BootCampAlura/main/Projeto_modulo_3/figs/fig_3.png)

Dessa vez, temos dinâmicas mais similares entre si, pois mesmo em cidades que tiveram menos casos novos diários em 2021, houve aumento no número de mortes em relação ao ano passado. Podemos destacar uma dinâmica mais errática em 2021 para São Bernardo do Campo, evidenciado pela média móvel, mas, no geral, as dinâmicas são bastante parecidas. 

Vamos examinar então as correlações:

### Correlação

![](https://raw.githubusercontent.com/RPGraciotti/BootCampAlura/main/Projeto_modulo_3/figs/fig_4.png)

Como esperado, em geral as correlações se apresentam melhores quando comparadas às correlações de casos diários, a exemplo da correlação São Paulo x Taboão da Serra, um pouco mais expressiva que a anterior. Novamente, as dinâmicas das 3 maiores cidades se mostraram mais próximas, e Francisco Morato manteve valores próximos de 0 por mais tempo. Nesse caso, podemos observar que quase não houve mortes concentradas em períodos próximos no município, à exceção de poucos dias com grandes picos mais espalhados.

# Vacinação

Bom, agora vamos tentar examinar outra hipótese sobre a dinâmica de migração pendular e saúde. Se assumirmos que a proximidade dos municípios da RMSP facilita a integração do sistema de saúde, poderíamos esperar vários fatores sendo influencidados como número de leitos de UTI disponíveis e ocupados, transporte de pacientes da RMSP para à capital, etc. Certamente são dados interessantes de serem analisados e estão disponíveis, mas para este projeto eu decidi focar nos dados de vacinação contra COVID19. 

A vacinação no Brasil teve início no dia 17 de Janeiro de 2021, [marcada por turbulências políticas](https://noticias.uol.com.br/ultimas-noticias/afp/2021/01/17/brasil-aplica-primeira-vacina-contra-a-covid-19-em-meio-a-uma-guerra-politica.htm) e [atraso](https://www.cnnbrasil.com.br/saude/2020/12/24/quais-os-paises-que-ja-comecaram-a-vacinacao-contra-a-covid-19) em relação ao resto do mundo. O foco da campanha inicial foram os trabalhadores da saúde e idosos, em todo o território nacional. Conforme a campanha de vacinação foi avançando, maiores quantidades foram distribuídas, mais vacinas aprovadas, o ritmo de vacinação foi aumentando e novos grupos passaram a ser imunizados. Os calendários de vacinação variam bastante entre estados e municípios, seguindo as demandas de distribuições etárias e grupos prioritários. No momento da redação final deste projeto, por exemplo, Manaus já está vacinando todas as [pessoas maiores de 26 anos](https://g1.globo.com/am/amazonas/noticia/2021/07/02/manaus-vacina-pessoas-a-partir-de-26-anos-contra-a-covid-19.ghtml), enquanto que em Brasília, a faixa etária se encontra dos [46 aos 59 anos](https://www.correiobraziliense.com.br/cidades-df/2021/07/4934863-covid-19-agendamento-para-populacao-com-46-anos-inicia-nesta-quinta.html). 

Outro fator muito importante na dinâmica de vacinação, conforme veremos logo mais, é que a grande maioria das vacinas aplicadas no Brasil requer duas doses para imunização completa. [Já iniciamos a aplicação com a vacina desenvolvida pela farmacêutica Janssen](https://www.uol.com.br/vivabem/noticias/redacao/2021/06/26/vacina-da-janssen-covid-19-sao-paulo.htm), que requer apenas uma dose, mas até o momento da redação do projeto ela não corresponde a um contingente expressivo do total de vacinas aplicadas, e também [não há previsões](https://oglobo.globo.com/sociedade/saude/apos-100-milhoes-de-doses-aplicadas-veja-cronograma-de-chegada-de-vacinas-contra-covid-19-ate-fim-do-ano-25087336) de que ela se torne a vacina mais aplicada no Brasil. A necessidade de duas doses de vacinação, com tempo de espera entre uma dose e outra variando a depender do fabricante, gera maiores dificuldades de análises de comparação e previsões de vacinação como um todo. Vamos exemplificar algumas dessas questões olhando primeiro para os dados do Estado de São Paulo:

*Nota: para essas análises, já havia pelo menos 4 dias de dados sobre a aplicação da vacina da Janssen em São Paulo, mas eu os removi para facilitar a análise do resto.*

![](https://raw.githubusercontent.com/RPGraciotti/BootCampAlura/main/Projeto_modulo_3/figs/fig_5.png)

O que nós podemos observar nesse gráfico? Primeiro, há um "atraso" em relação às aplicações de primeira e segunda dose. As primeiras vacinas aplicadas no estado de São Paulo foram da fabricante Coronavac, que possui 21-28 dias de espera recomendada entre as [duas doses](https://butantan.gov.br/covid/butantan-tira-duvida/tira-duvida-noticias/quais-sao-as-diferencas-entre-as-vacinas-contra-covid-19-que-estao-sendo-aplicadas-no-brasil).

[Logo em seguida](https://www.prefeitura.sp.gov.br/cidade/secretarias/saude/noticias/?p=307888) iniciou-se o uso da vacina fabricada pela AstraZeneca/Oxford, que possui um tempo muito maior previsto entre as duas doses, de 9-12 semanas. Podemos ver que o número de segundas doses aplicadas subiu bastante entre Abril e Maio, provenientes das primeiras doses desse imunizante aplicadas em Janeiro. Houve então um período de queda desse total, mas ao mesmo tempo houve um aumento expressivo de aplicações de primeiras doses. Isso se deve as ampliações de grupos prioritários [(sessão "documentos técnicos")](https://www.prefeitura.sp.gov.br/cidade/secretarias/saude/vigilancia_em_saude/index.php?p=307599), e um grande aporte de novas doses, [sobretudo da fabricante Pfizer](https://g1.globo.com/sp/sao-paulo/noticia/2021/06/11/governo-de-sp-vai-distribuir-vacinas-contra-covid-19-da-pfizer-e-da-janssen-para-todos-os-municipios.ghtml) no meio de Junho.

Essas dinâmicas por si só seriam bastante interesantes de se analisar e modelar, previsões de como o aumento de aplicações de primeiras doses prevê o aumento de segundas doses, por exemplo. Porém, para facilitar a análise dos objetivos propostos nesse projeto, eu considerei focar apenas no **total** de doses aplicadas e taxas diárias, independente de ser segunda ou primeira dose
Não podemos considerar somente a dinâmica de aplições de segundas doses para analisar, pois ele não reflete a real capacidade de vacinação dos sistemas de saúde; devido aos diferentes prazos entre uma dose e outra. Por último, se a maior parte da vacinação ocorrerá com vacinas de duas doses, seria interessante analisar o total de doses tendo em mente que esse número deve corresponder **ao dobro** da população. Levando isso em conta, na prática é como se **dobrassemos** os dados populacionais para as análises.

Tendo tudo isso em mente, vamos examinar a dinâmica do total de doses aplicadas no estado para balizar nossas expectativas com relação aos muncípios.

*Nota: também convém dizer que os dados disponíveis no sistema do SUS para análise corresponde também ao total diário de doses, sem discriminação de primeira ou segunda dose.*

![](https://raw.githubusercontent.com/RPGraciotti/BootCampAlura/main/Projeto_modulo_3/figs/fig_6.png)

Agora fica mais claro observar que a taxa de doses diárias aplicadas está aumentando no estado. 

# Cidades
## Taxas diárias

![](https://raw.githubusercontent.com/RPGraciotti/BootCampAlura/main/Projeto_modulo_3/figs/fig_7.png)

Sem muitas novidades, vemos que o padrão de vacinação dos municípios além de São Paulo reflete bastante o padrão observado do geral para o estado. Em uma das [reportagens](https://g1.globo.com/sp/sao-paulo/noticia/2021/06/11/governo-de-sp-vai-distribuir-vacinas-contra-covid-19-da-pfizer-e-da-janssen-para-todos-os-municipios.ghtml) apresentadas aqui anteriormente, vimos que a vacina da Pfizer nos primeiros dias era aplicada somente na capital. Isso explica o fato da capital não ter apresentado o mesmo aumento significativo nas taxas de vacinação em Junho como apresentando por outras cidades. 

Dado que esse padrão foi observado para o estado como um todo, podemos começar a nos perguntar se é somente a proximidade com a capital que influenciou a dinâmica da RMSP, como propus anteriormente. De qualquer forma, esse resultado é bastante animador.

Cabe ressaltar aqui que análises de correlação cruzada nesse caso não seriam tão interessantes, devido ao volume de dados muito menor (~ 160 dias), e a relativa unanimidade dos ritmos de vacinação em todas as cidades não parece trazer muitas novidades.

Porém, podemos focar agora em outro aspecto, mais importante: o total cumulativo de doses aplicadas. Afinal, não estamos tão interessados no número de doses diárias, mas mais em quantas doses já foram (e serão aplicadas).

## Cumulativo

Primeiro, vamos olhar para o cumulativo de doses totais no Estado de São Paulo, novamente para balizar nossas expectativas com relação ao que poderemos esperar das cidades.

![](https://raw.githubusercontent.com/RPGraciotti/BootCampAlura/main/Projeto_modulo_3/figs/fig_8.png)

Podemos observar ao final do mês de junho aquele aumento na taxa de vacinação diária, e como isso aumentou a forma como o total cumulativo de doses aumentou nos últimos dias. Tendo em mente que precisamos sempre considerar o total de doses tendo como meta o dobro efetivo da popualação, já aplicamos doses o suficiente para vacinar metade do Estado de São Paulo, de população de [46 milhões de pessoas](https://pt.wikipedia.org/wiki/S%C3%A3o_Paulo_%28estado%29).


Vamos olhar agora para o cumulativo das cidades:

![](https://raw.githubusercontent.com/RPGraciotti/BootCampAlura/main/Projeto_modulo_3/figs/fig_9.png)

Como esperado, excetuando-se São Paulo, detectamos a inflexão de aumento da taxa diário também no toal de doses aplicadas em todos os municípios selecionados.

# Previsões

Por fim, o aspecto mais interessante que podemos nos perguntar é: afinal, quando estaremos todos vacinados? 

Pudemos notar que a forma como o total cumulativo de doses cresceu foi muito similar ao esperado em um [crescimento logístico](https://en.wikipedia.org/wiki/Logistic_function). A função logística é muito utilizada em estudos de ecologia populacional, e descreve muito bem a forma como populações crescem. Tendo em vista a forma como a população do Estado de São Paulo, da capital e dos municípios selecionados, podemos **modelar uma previsão** de quando todos, ou, pelo menos uma parte relevante da população estará vacinada. 

Idealmente, claro, para que a imunização seja completa, almejamos alcançar 100% da população vacinada. Porém, devido à [taxa de transmissão](https://www.agazeta.com.br/es/cotidiano/quantas-pessoas-precisam-estar-vacinadas-para-conter-o-coronavirus-0121) do Sars-cov2, podemos estimar que com [70 a 90% da população vacinada](https://coronavirus.ufes.br/conteudo/epidemiologia-no-enfrentamento-da-pandemia-de-coronavirus-parte-9
) já podemos ter um incío de controle da pandemia. Podemos trabalhar com esses valores para fazer inferências sobre a forma como o total de doses aplicados poderá crescer.

---

Para a modelagem de previsões, utilizei a biblioteca [Facebook Prophet](https://facebook.github.io/prophet/). Primeiro, defini a função que vai aplicar a modelagem, já que ela será repetida diversas vezes durante o projeto.

O Prophet é capaz de modelar uma previsão a partir da nossa série temporal, utilizando um modelo de [crescimento logístico](https://facebook.github.io/prophet/docs/saturating_forecasts.html). O modelo logístico necessita de um "teto", também conhecido como "capacidade de suporte", que diz o valor máximo em que a previsão é saturada. Diferente da dinâmica da própria COVID, em que pessoas podem se reinfectar com o vírus, a previsão é de que cada pessoa seja imunizada apenas uma vez (com duas doses quando for o caso). *(Se estuda a possibilidade de uma aplicação de [terceira dose](https://saude.abril.com.br/blog/boa-pergunta/vamos-precisar-de-uma-terceira-dose-da-vacina-contra-a-covid-19/), mas por enquanto não está nos planos)*.

Como discutimos anteriormente, estamos tratando a população efetiva como o dobro, e podemos utilizar esse valor como o "teto", "cap" na notação do Prophet nos nossos modelos. Outra parte importante da modelagem de previsões é que, idealmente, não utilizamos todos os dados para a previsão, a fim de evitar o chamado "overfiting", em que o modelo perde capacidade de generalização. Um cenário plausível é utilizar de 70-80% dos dados para treino do modelo. Tendo em mente que o aumento expressivo das taxas de vacinação diárias aconteceu nos últimos dias de Junho, esses dados não entrariam, usando-se os 80% dos dados. Tenhamos isso mente ao avaliar as previsões.

![](https://raw.githubusercontent.com/RPGraciotti/BootCampAlura/main/Projeto_modulo_3/figs/fig_10_1.png)
![](https://raw.githubusercontent.com/RPGraciotti/BootCampAlura/main/Projeto_modulo_3/figs/fig_10_1.png)
![](https://raw.githubusercontent.com/RPGraciotti/BootCampAlura/main/Projeto_modulo_3/figs/fig_10_2.png)
![](https://raw.githubusercontent.com/RPGraciotti/BootCampAlura/main/Projeto_modulo_3/figs/fig_10_3.png)
![](https://raw.githubusercontent.com/RPGraciotti/BootCampAlura/main/Projeto_modulo_3/figs/fig_10_4.png)
![](https://raw.githubusercontent.com/RPGraciotti/BootCampAlura/main/Projeto_modulo_3/figs/fig_10_5.png)
![](https://raw.githubusercontent.com/RPGraciotti/BootCampAlura/main/Projeto_modulo_3/figs/fig_10_6.png)

Nestes gráficos, a linha azul escura corresponde à previsão e sua incerteza em azul mais claro. O tracejado preto é o limite de 100% da população, os tracejados vermelhos representam os valores de 70 e 90% da população.

O que podemos observar é que, com relativa incerteza, na taxa pré-avanço da vacinação em Junho, apenas São Paulo e Guarulhos atingiriam o mínimo de 70% de vacinação até o final de 2021. É um dado preocupante, mas levemos dois fatores em consideração:

1.   Não condiz com a [expectativa](https://www.saopaulo.sp.gov.br/noticias-coronavirus/sp-apresenta-novo-calendario-para-vacinar-toda-a-populacao-adulta-ate-15-de-setembro/) do Governo do Estado, que prevê vacinar todos os adultos até Setembro.
2.   Porém... considerando justamente o ponto anterior, nosso modelo inclui **toda** a população. A previsão do governo inclui apenas os maiores de 18 anos, e isso apenas a **primeira** dose. 

Portanto, nosso modelo não é tão pessimista quando parece a princípio, pois estamos estimando com uma taxa menor que a atual, quando toda a população, incluindo menores de 18 anos serão vacinados com as duas doses. 

Um desses fatores não podemos corrigir no modelo por enquanto, que é a exclusão da população menor de 18 anos, mas podemos tentar incluir um outro fator. A fim de testar o efeito do último aumento na taxa de vacinação em Junho, realizei uma nova rodada de modelos utilizando todo o conjunto de dados:

![](https://raw.githubusercontent.com/RPGraciotti/BootCampAlura/main/Projeto_modulo_3/figs/fig_11_1.png)
![](https://raw.githubusercontent.com/RPGraciotti/BootCampAlura/main/Projeto_modulo_3/figs/fig_11_2.png)
![](https://raw.githubusercontent.com/RPGraciotti/BootCampAlura/main/Projeto_modulo_3/figs/fig_11_3.png)
![](https://raw.githubusercontent.com/RPGraciotti/BootCampAlura/main/Projeto_modulo_3/figs/fig_11_4.png)
![](https://raw.githubusercontent.com/RPGraciotti/BootCampAlura/main/Projeto_modulo_3/figs/fig_11_5.png)
![](https://raw.githubusercontent.com/RPGraciotti/BootCampAlura/main/Projeto_modulo_3/figs/fig_11_6.png)

Nessa realidade, todos os municípios terão vacinado todas pelo menos 70% da população até o final de Outubro. Uma expectativa mais condizente com o almejado pelo governo.

Por último, podemos analisar algumas métricas simples de validação dos modelos .Uma métrica que podemos aplicar para esse modelo é a [validação cruzada](https://facebook.github.io/prophet/docs/diagnostics.html#cross-validation), que consegue medir a taxa de erro do modelo em diferentes janelas de observação, comparando-se o resultado previsto com o dado real. Dessa forma, podemos estimar como o modelo erra ou não o ajuste, e considerar como isso pode influenciar os resultados de previsão.

Para esse teste, utilizarei apenas os modelo da cidade de São Paulo, já que os parâmetros são idênticos entre as outras cidades. Utilizando uma janela de 7 dias, já que o aumento de vacinação ocorreu muito recentemente nos nossos dados, e iniciando-se mais ou menos no meio do nosso intervalo, podemos calcular uma série de métricas de [performance](https://aditi-mittal.medium.com/cross-validation-and-performance-measures-in-machine-learning-9dabdbed5459) de modelos, como erros médios e erros quadráticos médios. Vamos analisar o erro quadrático médio:

![](https://raw.githubusercontent.com/RPGraciotti/BootCampAlura/main/Projeto_modulo_3/figs/fig_12.png)

Podemos observar que o erro do modelo aumenta ao final do horizonte, padrão que se repete ao final da nossa série temporal, dado o maior intervalo de confiança observado.

# Conclusões

Portanto, observamos que as hipóteses iniciais se concretizaram, mas com possíveis fatores de confusão. De fato, os municípios de Osasco, Guarulhos, São Bernardo do Campo, Francisco Morato e Taboão da Serra apresentaram dinâmicas semelhantes às dinâmicas de São Paulo em diversos aspectos. Algumas cidades conseguiram lidar melhor com o avanço da pandemia em 2021 do que em São Paulo, mas o número de mortes, o dado mais preocupante de todos, segiu alto em todas as cidades. De fato, não conseguiremos distinguir se essas novas mortes nos municípios pendulares foram de indivíduos que realizam a migração pendular, mas sabemos que as medidas de restrição foram mais brandas em 2021.

A dinâmica de vacinação condiz melhor com a hipótese inicial, inclusive vimos isso ser afirmado pelo governo. O maior aporte de vacinas em Junho para municípios além da capital promoveu um aumento expressivo em todos os municípios analisados. Por fim, vimos que esse aporte de vacinas pode ser **fundamental** para que a vacinação avance mais rápido e que consigamos, possivelmente, atingir as metas propostas pelo governo do Estado, com um mês de atraso, mas ainda uma previsão bastante otimista. 

#VacinaJá!

# Referências

https://www.seade.gov.br/coronavirus/

https://facebook.github.io/prophet/

https://pt.wikipedia.org/wiki/Regi%C3%A3o_Metropolitana_de_S%C3%A3o_Paulo

https://mundoeducacao.uol.com.br/geografia/migracao-pendular.htm

https://pt.wikipedia.org/wiki/Aeroporto_Internacional_de_S%C3%A3o_Paulo-Guarulhos

https://www.scielo.br/j/spp/a/tJPPNLfJmTZLWyh7S5KH8BJ/?lang=pt#

https://repositorio.ufmg.br/handle/1843/32033

http://produtos.seade.gov.br/produtos/spp/v19n04/v19n04_06.pdf

https://g1.globo.com/sp/sao-paulo/noticia/2021/05/27/cidade-de-sao-paulo-ja-tem-mais-mortos-por-covid-19-que-nova-york-e-cidade-do-mexico.ghtml

https://g1.globo.com/sp/sao-paulo/noticia/2021/04/03/isolamento-social-nao-mudou-e-ficou-abaixo-dos-45percent-nos-feriados-antecipados-na-cidade-de-sp.ghtml

https://www.redebrasilatual.com.br/saude-e-ciencia/2021/03/isolamento-social-fase-roxa-emergencial-sao-paulo/

https://www.terra.com.br/noticias/coronavirus/pandemia-tem-custo-de-r-6-bilhoes-para-a-prefeitura-de-sao-paulo,ad0f8a5eac7695384bb95ff45ace6fd2x7hwbz24.html

https://governo-sp.jusbrasil.com.br/legislacao/1028148/lei-complementar-1139-11

https://www.seade.gov.br/

https://vacinaja.sp.gov.br/vacinometro/

https://brasil.io/dataset/covid19/caso_full/

https://governo-sp.jusbrasil.com.br/legislacao/1028148/lei-complementar-1139-11

https://pt.wikipedia.org/wiki/Lista_de_munic%C3%ADpios_de_S%C3%A3o_Paulo_por_popula%C3%A7%C3%A3o

https://en.wikipedia.org/wiki/Cross-correlation

https://noticias.uol.com.br/ultimas-noticias/afp/2021/01/17/brasil-aplica-primeira-vacina-contra-a-covid-19-em-meio-a-uma-guerra-politica.htm

https://www.cnnbrasil.com.br/saude/2020/12/24/quais-os-paises-que-ja-comecaram-a-vacinacao-contra-a-covid-19

https://g1.globo.com/am/amazonas/noticia/2021/07/02/manaus-vacina-pessoas-a-partir-de-26-anos-contra-a-covid-19.ghtml

https://www.correiobraziliense.com.br/cidades-df/2021/07/4934863-covid-19-agendamento-para-populacao-com-46-anos-inicia-nesta-quinta.html

https://oglobo.globo.com/sociedade/saude/apos-100-milhoes-de-doses-aplicadas-veja-cronograma-de-chegada-de-vacinas-contra-covid-19-ate-fim-do-ano-25087336

https://www.uol.com.br/vivabem/noticias/redacao/2021/06/26/vacina-da-janssen-covid-19-sao-paulo.htm

https://butantan.gov.br/covid/butantan-tira-duvida/tira-duvida-noticias/quais-sao-as-diferencas-entre-as-vacinas-contra-covid-19-que-estao-sendo-aplicadas-no-brasil

https://www.prefeitura.sp.gov.br/cidade/secretarias/saude/noticias/?p=307888

https://www.prefeitura.sp.gov.br/cidade/secretarias/saude/vigilancia_em_saude/index.php?p=307599

https://g1.globo.com/sp/sao-paulo/noticia/2021/06/11/governo-de-sp-vai-distribuir-vacinas-contra-covid-19-da-pfizer-e-da-janssen-para-todos-os-municipios.ghtml

https://pt.wikipedia.org/wiki/S%C3%A3o_Paulo_(estado)

https://aditi-mittal.medium.com/cross-validation-and-performance-measures-in-machine-learning-9dabdbed5459

https://en.wikipedia.org/wiki/Logistic_function

https://coronavirus.ufes.br/conteudo/epidemiologia-no-enfrentamento-da-pandemia-de-coronavirus-parte-9

https://www.agazeta.com.br/es/cotidiano/quantas-pessoas-precisam-estar-vacinadas-para-conter-o-coronavirus-0121

https://saude.abril.com.br/blog/boa-pergunta/vamos-precisar-de-uma-terceira-dose-da-vacina-contra-a-covid-19/

https://www.saopaulo.sp.gov.br/noticias-coronavirus/sp-apresenta-novo-calendario-para-vacinar-toda-a-populacao-adulta-ate-15-de-setembro/
