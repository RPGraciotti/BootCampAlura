###### Acesso ao notebook: https://github.com/RPGraciotti/BootCampAlura/blob/main/Projeto_modulo_1/notebooks/Projeto_modulo_1.ipynb
# Introdução
Seja bem-vindo e/ou muito bem-vinda ao meu notebook do projeto/super-desafio do módulo 1!

Nesse módulo aprendemos a fazer buscas em um banco de dados do governo brasileiro, o [**Datasus**](https://http://www2.datasus.gov.br/DATASUS/index.php?area=02), para acessar dados sobre o Sistema Único de Saúde Brasileiro (SUS). O Datasus usa uma ferramenta chamada **tabnet** para tabular os dados de forma a torná-los acessíveis e utilizáveis em análises. Uma ferramenta bastante útil para pesquisas na área da saúde, e, por que não, na área de ciência de dados?

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_modulo_1/figs/abertura.png)
**Tela inicial do Data Sus**

## Tema: Vacinas
Para este projeto, escolhi trabalhar com um assunto que está muito em alta atualmente, vacinação! Mas, por enquanto, não estou falando da vacinação contra COVID-19 (#VacinaJá!). A crise provocada pela pandemia de COVID-19 fez ressurgir o interesse pelo assunto vacinação, acompanhado de infelizes notícias falsas e teorias conspiratórias desincentivando o ato de se vacinar. Isso infelizmente tem levado [à quedas nos índices de vacinação](https://saude.abril.com.br/medicina/por-que-as-pessoas-estao-tomando-menos-vacina/), Brasil e mundo afora. Claro, [existem outros fatores socioeconômicos não menos importantes, como o horário limitado dos postos de saúde e rotinas mais intensas de trabalho, que podem ter levado a esse fenômeno de queda.](https://revistapesquisa.fapesp.br/as-razoes-da-queda-na-vacinacao/)

Para o meu projeto, resolvi então analisar os dados de vacinação disponíveis no Datasus. Primeiro, olharemos para o padrão mais geral de vacinação no Brasil, depois examinaremos a nível de região, seguindo por análises mais detalhadas sobre a cobertura vacinal.

Os conjuntos de dados obtidos do Datasus são os seguintes:

*   total_reg_ano = Conjunto total de doses aplicadas, por região e por ano no Brasil;
*   total_reg_ano_ob = Como veremos mais para frente, há alguns problemas com alguns identificadores de pesquisa dos dados de vacina no Datasus, e, portanto, foi necessário refinar a pesquisa com outros parâmetros;
*   total_imuno = total de doses com identificador do imunizante;
*   total_imuno_ob = total de doses com identificador do imunizante da pesquisa refinada;
*   cobertura_reg_ano = dados de cobertura vacinal por região e ano;
*   cobertura_imuno_ano = dados de cobertura vacinal por imunizante e ano.

A série temporal dos dados de vacinação disponíveis no Datasus tem início no ano de 1994, e vai até os meses mais recentes de 2021 (no momento da confecção deste notebook: Mai/2021). Eu optei por não incluir o ano de 2021 pois a escala da minha pesquisa é por ano, e o ano de 2021 ainda está correndo e não teríamos dados completos. Outro motivo que me fez descartar o ano de 2021 é que a campanha atual de vacinação contra COVID-19 pode estar afetando os indíces de vacinação das outras vacinas, seja por maior alocamento de recursos ou menores taxas de vacinação geradas pelo isolamento social. Importante ressaltar que o isolamento social também pode ter influenciado os dados de vacinação de 2020.

## Análises de tendências gerais:

### Total

Vamos primeiro então, analisar o padrão geral do total de milhões de doses aplicadas de 1994 até 2020:

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_modulo_1/figs/fig_1.png)

Podemos observar 3 pontos importantes neste gráfico: 


*   Há um aumento expressivo no início da série temporal, o que pode ser devido a diversos fatores não apenas no número absoluto (pessoas se vacinaram mais), mas também aprimoramentos nos sistemas de cadastro de vacinas, maiores e mais informativas campanhas de vacinação, etc.;
*   Há uma certa ciclicidade: há alguns picos seguidos de queda mas em equilíbrio dinâmico;
*   Há uma tendência de queda 2010 para cá, em que os valores em média não parecem atingir o patamar da década de 2000.

Seria essa queda observada de fato resultado de menos pessoas estarem se vacinando, como discutido anteriormente? Será que essa tendência se repete nas diferentes regiões do Brasil? Vamos separar esse gráfico pelas regiões.

**Como a escala do total é muito discrepante entre as regiões, resolvi plotar cada região separadamente**

### Por regiões:
#### **Total de doses aplicadas em cada região do Brasil**
![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_modulo_1/figs/fig_2_1.png)
![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_modulo_1/figs/fig_2_2.png)
![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_modulo_1/figs/fig_2_3.png)
![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_modulo_1/figs/fig_2_4.png)
![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_modulo_1/figs/fig_2_5.png)

Podemos observar que, embora todas as regiões brasileiras apresentem tendência de queda, as regiões Norte e Nordeste parecem apresentar maior queda em relação ao seu própio passado da década de 2000. Não obstante, são as [regiões mais pobres do brasil](https://pt.wikipedia.org/wiki/Lista_de_unidades_federativas_do_Brasil_por_incid%C3%AAncia_da_pobreza). Isso levanta um alerta vermelho: se os índices de vacinação estão caindo de forma geral, doenças erradicadas ou de [potencial endêmico](https://portalarquivos2.saude.gov.br/images/pdf/2019/setembro/25/boletim-especial-21ago19-web.pdf) podem voltar a circular. 

Porém, como discutido no início, há problemas associados com as pesquisas no portal Datasus. Ao pedir para que o Datasus retorne todos os tipos de imunizantes presentes no banco de dados, o resultado é o seguinte:

`Index(['BCG (BCG)', 'BCG - Hanseníase (BCG)', 'Febre Amarela (FA)',
       'Febre Tifóide (FT)', 'Haemophilus influenzae tipo b (Hib)',
       'Hepatite A (HA)', 'Hepatite B (HB)', 'Hepatite B não soroconversão',
       'Influenza (INF)', 'Influenza (Campanha) (INF)',
       'Influenza (Gestantes) (INF)', 'Meningococo A/C (MnAC)',
       'Meningococo B/C (MnBC)', 'Raiva Humana',
       'Raiva - Cultivo Celular/Diplóide (RC)',
       'Raiva - Cultivo Celular/Vero (RV)',
       'Raiva - Cultivo Celular/Embrionário (RG)', 'Rubéola', 'Sarampo',
       'Varicela', 'Dupla Adulto (dT)', 'Dupla Infantil (DT)',
       'Dupla Viral (rotina) (SR)', 'Dupla Viral (mulheres idade fértil) (SR)',
       'Dupla Viral (mulheres idade fértil campanha) (SR)',
       'Dupla Viral (homens) (SR)', 'Dupla Viral (homens campanha) (SR)',
       'Hexavalente (HX)', 'Poliomielite inativada (VIP)',
       'Meningocócica Conjugada - C (MncC)', 'Oral Poliomielite (VOP)',
       'Oral Poliomielite 1ª etapa campanha  (VOP)',
       'Oral Poliomielite 2ª etapa campanha  (VOP)',
       'Oral de Rotavírus Humano (VORH)', 'Pentavalente (DTP+HB+Hib) (PENTA)',
       'Pneumocócica 10valente', 'Pneumocócica Conjugada 7 Valente (Pnc7)',
       'Pneumocócica Polissacarídica 23 Valente (Pn23)',
       'Pneumocócica 13 valente', 'Tetravalente (DTP/Hib) (TETRA)',
       'Toxóide Tetânico', 'Tríplice Acelular (DTPa)',
       'Tríplice Bacteriana (DTP)', 'Tríplice Viral (SCR)',
       'Tríplice Viral (campanha) (SCR)',
       'Tríplice Viral (homens campanha) (SCR)',
       'Tríplice Viral (implantação) (SRC)',
       'Tríplice Viral (mulheres idade fértil camp) (SCR)',
       'Tríplice Viral (seguimento) (SRC)',
       'Imunoglobulina humana anti-Hepatite B (IGHAHB)',
       'Imunoglobulina humana anti-Rábica (IGHR)',
       'Imunoglobulina humana anti-Tetânica (IGHAT)',
       'Imunoglobulina humana anti-Varicela Zóster (IGHVZ',
       'Soro anti-Aracnídico (AC)', 'Soro anti-Botrópico (BO)',
       'Soro anti-Botrópico-Crotálico (BC)',
       'Soro anti-Botrópico-Laquético (BL)', 'Soro anti-Botulínico (BTU)',
       'Soro anti-Crotálico (CR)', 'Soro anti-Diftérico (SAD)',
       'Soro anti-Elapídico (LP)', 'Soro anti-Escorpiônico (ES)',
       'Soro anti-Laquético', 'Soro anti-Latrodectus (LT)',
       'Soro anti-Lonomia (LN)', 'Soro anti-Loxocélico (LX)',
       'Soro anti-Rábico (RB)', 'Soro anti-Tetânico (SAT)', 'Esq.Seq. VIP/VOP',
       'Penta inativada (DTPa/Hib/Vip)',
       'Tetraviral (sarampo, rubéola, caxumbae varicela)',
       'HPV Quadrivalente - Feminino', 'HPV Quadrivalente - Masculino',
       'HEPATITE A, B RECOMBINANTE', 'Rotavírus pentavalente',
       'Quadrupla viral', 'Meningocócica ACYW1325', 'HPV', 'dTpa', 'Ignorado',
       'Total'],
      dtype='object', name='Imunobiológicos')`
      
Percebe como parece haver uma certa quantidade de valores redundantes ou não corretamente especificados, como "Hepatite (A)", "HEPATITE A, B RECOMBINANTE", diversos tipos de vacina contra raiva (não obrigatórias), soros antiofídicos e antirábicos (não são vacinas), separações por sexo e campanhas (não informativas), e uma seção "ignorados" ? Para lidar com esse conjunto de dados, eu limitei um pouco o resultado da busca para listar somente os imunizantes com melhor informação sobre a categoria do imunizante, ou apenas os imunizantes considerados obrigatórios e parte dos programas nacionais de imunização. Links: [[1]](https://bvsms.saude.gov.br/bvs/publicacoes/cart_vac.pdf), [[2]](https://revistapesquisa.fapesp.br/wp-content/uploads/2018/08/Calend%C3%A1rio_Vacina%C3%A7%C3%A3o_270.pdf), [[3]](https://www.agazeta.com.br/es/cotidiano/veja-quais-sao-as-vacinas-obrigatorias-em-todas-as-fases-da-vida-0920).

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_modulo_1/figs/pesquisa.png)
**print screen da ferramenta de busca por imunizante**

`Index(['BCG (BCG)', 'BCG - Hanseníase (BCG)', 'Febre Amarela (FA)',
       'Hepatite A (HA)', 'Hepatite B (HB)', 'Influenza (INF)', 'Varicela',
       'Dupla Adulto (dT)', 'Dupla Infantil (DT)',
       'Poliomielite inativada (VIP)', 'Meningocócica Conjugada - C (MncC)',
       'Oral Poliomielite (VOP)', 'Oral de Rotavírus Humano (VORH)',
       'Pentavalente (DTP+HB+Hib) (PENTA)', 'Pneumocócica 10valente',
       'Tetravalente (DTP/Hib) (TETRA)', 'Tríplice Bacteriana (DTP)',
       'Tríplice Viral (SCR)',
       'Tetraviral (sarampo, rubéola, caxumbae varicela)',
       'HPV Quadrivalente - Feminino', 'HPV Quadrivalente - Masculino', 'HPV',
       'Total'],
      dtype='object', name='Imunobiológicos')`
      
Também me baseei nos dados de [cobertura vacinal](http://tabnet.datasus.gov.br/tabdata/LivroIDB/2edrev/f13.pdf), que é outro conjunto de dados que examineramos mais a fundo em breve, mas tentei identificar os mesmos imunizantes listados na sessão de cobertura vacinal:

`Index(['BCG', 'Hepatite B  em crianças até 30 dias', 'Rotavírus Humano',
       'Meningococo C', 'Hepatite B', 'Penta', 'Pneumocócica', 'Poliomielite',
       'Poliomielite 4 anos', 'Febre Amarela', 'Hepatite A',
       'Pneumocócica(1º ref)', 'Meningococo C (1º ref)',
       'Poliomielite(1º ref)', 'Tríplice Viral  D1', 'Tríplice Viral  D2',
       'Tetra Viral(SRC+VZ)', 'DTP', 'DTP REF (4 e 6 anos)',
       'Tríplice Bacteriana(DTP)(1º ref)', 'Sarampo',
       'Haemophilus influenzae b', 'Dupla adulto e tríplice acelular gestante',
       'dTpa gestante', 'Tetravalente (DTP/Hib) (TETRA)', 'Ignorado', 'Total'],
      dtype='object', name='Imuno')`
      
### Por imunizantes da pesquisa refinada:

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_modulo_1/figs/fig_3.png)

Curioso! Agora, parece que não temos mais uma tendência de queda tão intensa quanto notada anteriormente, ao longo da última década. Há uma tendência de queda nos últimos 4,5 anos apenas. Essa queda poderia ser explicada pelas variações cíclicas que observamos ao longo de toda a série temporal? E se olharmos para as regiões novamente?

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_modulo_1/figs/fig_4_1.png)
![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_modulo_1/figs/fig_4_2.png)
![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_modulo_1/figs/fig_4_3.png)
![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_modulo_1/figs/fig_4_4.png)
![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_modulo_1/figs/fig_4_5.png)

Certo. Os padrões ainda parecem se manter como no geral, uma tendência de queda apenas nos últimos 4, 5 anos, e isso se repete na maioria das regiões do Brasil.

## Cobertura vacinal

### Total

Até agora vimos que o campo "imunizante" do banco de dados do total de doses parece levar a resultados conflitantes. Por um lado, pode ser interessante analisar o total de todas as doses de todas as vacinas aplicadas no Brasil; por outro, parece haver redundância e informações incompletas no conjunto de dados. Por mais que seja feito um esforço para tornar compatível o campo de imunizantes com os imunizantes obrigatórios, há um dado mais interssante que já começamos a explorar: a cobertura vacinal.

A cobertura vacinal é [calculada](http://tabnet.datasus.gov.br/tabdata/LivroIDB/2edrev/f13.pdf) como o percentual de crianças vacinadas com **esquema completo** de vacinação , e os dados disponíveis do Datasus parecem respeitar as vacinas obrigatórias do plano nacional de imunização. Vamos então, analisar esses dados:

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_modulo_1/figs/fig_5.png)

Agora temos dados mais interessantes para trabalhar. A cobertura vacinal diz mais a respeito sobre sobre indicadores da qualidade e atingimento das metas de vacinação do que o número total de doses. Será que uma parcela significativa das crianças está sendo vacinada corretamente? Qual é essa meta? As metas costumam variar de [80 a 100%](https://antigo.saude.gov.br/images/pdf/2017/agosto/17/AACOBERTURAS-VACINAIS-NO-BRASIL---2010-2014.pdf) de crianças vacinadas, a depender do tipo de [imunizante](https://revistapesquisa.fapesp.br/as-razoes-da-queda-na-vacinacao/). Os valores de 80% são associadas com as vacinas de HPV e Influenza, vacinas recentemente desenvolvidas e de público alvo menos amplo. Podemos considerar um mímino de 90% de meta de cobertua vacinal "unificada", e olhar esse valor no gráfico:

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_modulo_1/figs/fig_6.png)

Agora fica mais claro ainda que nos últimos anos tivemos uma queda grave na cobertura vacinal. A tendência dos últimos 5 anos, com um **forte** declínio no ano de 2016.

### Por região:

Novamente, podemos olhar para esses valores por região.

**Devido à escala, agora fica mais fácil de visualizar todas regiões no mesmo gráfico:**

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_modulo_1/figs/fig_7.png)

Novamente, não parece haver muita distinção entre regiões, demonstrando uma certa unidade nos valores de cobertura vacinal, mas não no total de doses, por região.

### Por imunizante
A pesquisa por cobertura vacinal também permite discriminar os diferentes tipos de imunizantes. Podemos conferir quais os imunizantes mais aplicados, quais deles estão abaixo ou acima da meta:

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_modulo_1/figs/fig_8.png)

**Nota: os valores da BCG e Triplíce viral ultrapassam 100% e não encontrei informações sobre o porquê. Imagino que, como a cobertura é calculada pelo N crianças vacinadas/N crianças na idade alvo, a mortalidade infantil por outras causas pode levar à morte de crianças, já vacinadas, diminuindo o denominador da equação.**

Considerando essa informação, parece que apenas a BCG, Tríplice viral e Poliomielite estão alcançando as metas de cobertura vacinal mínima de 90%! Um dado bastante alarmente, já [denunciado](https://saude.abril.com.br/medicina/por-que-as-pessoas-estao-tomando-menos-vacina/) pelos [veículos de imprensa](https://revistapesquisa.fapesp.br/as-razoes-da-queda-na-vacinacao/) que discutimos anteriormente. Do outro lado do espectro, a vacina com menor cobertura temos "Haemophilus influenzae b". Essa vacina na verdade faz parte da combinação pentavalente, uma vacina mais recente que [substitui](https://www.aredacao.com.br/vida-e-saude/15700/vacina-pentavalente-substitui-a-tetravalente-e-esta-a-disposicao) a tetravalente; o que explica simultaneamente os baixos valores de TETRA e Hib. As vacinas destinadas a gestantes, do tipo dTpa também são vacinas [recentes](https://www.saude.sc.gov.br/index.php/documentos/informacoes-gerais/redes-de-atencao-a-saude-2/rede-aten-a-saude-materna-e-infantil-rede-cegonha/acervo-e-e-books/10411-nota-tecnica-vacinacao-para-gestantes-dtpa-prevencao-de-coqueluche-em-menores-de-1-ano/file), o que pode explicar sua ainda baixa adesão. 

A baixa adesão vacina da febre amarela é uma das mais preocupantes, devido aos [recentes surtos](https://pt.wikipedia.org/wiki/Surto_de_febre_amarela_no_Brasil_em_2016-2017) da doença no Brasil, uma doença de potencial endêmico. Nos Estados de [São Paulo](https://www.nationalgeographicbrasil.com/animais/2018/05/bugios-febre-amarela-epidemia-macacos-extincao) e, mais recente ainda, no [Rio Grande do Sul](https://gauchazh.clicrbs.com.br/pioneiro/geral/noticia/2021/04/mortes-de-bugios-por-febre-amarela-colocam-16-cidades-em-alerta-maximo-na-serra-cko367uvw003i0180rs8o4rx0.html), o surto causou alta mortalidade de macacos bugios. Os bugios não transmitem a doença para seres humanos, mas são muito suscetíveis aos surtos da doença, agindo como indicadores da expansão de febre amarela.

Porém, é imporante ressaltar também que esses valores refletem o total, de 1994 para cá. Podemos novamente olhar para a evolução da cobertura por imunizante ao longo dos anos. Como nossa série temporal inclui 26 anos de dados, limitarei essa análise aos últimos 5 anos, focando no período que observamos a queda acentuada na cobertura vacinal.

### Por imunizante e por ano

![](https://github.com/RPGraciotti/BootCampAlura/raw/main/Projeto_modulo_1/figs/fig_9.png)

A partir desse gráfico, podemos confirmar a tendência de queda nos últimos anos para grande parte dos imunizantes, mantendo-se abaixo dos mínimos 90%. Os espectros de imunizantes mais e menos aplicados são os mesmos observados anteriormente, com a total substituição da TETRAvalente e Hib pela PENTAvalente, ligeira expansão da dTpa, e ausência da vacina contra sarampo sepadaramente (fazendo parte da tríplice e tetraviral).

## Conclusões

A vacinação é essencial para a saúde da população, uma arma poderosa capaz de erradicar completamente doenças. Vimos que a vacinação evoluiu muito no Brasil nas décadas de 1990 e 2000, para atingir alarmantes níveis de queda na última década. O Brasil é um país [referência](https://gizmodo.uol.com.br/como-o-brasil-se-tornou-referencia-em-vacinacao-e-por-que-este-legado-esta-indo-para-o-lixo/) em vacinação, e temos um enorme legado que não podemos deixar se apagar. Infelizmente em nossa análise conseguimos observar as tendências reportadas de queda das imunizações. É importante ressaltar que a pandemia de COVID-19 e a necessidade de isolamento social em 2020 pode ter reduzido a cobertura vacinal de grande parte da população, que poderia não frequentar os postos de saúde com a frequência necessária. Ironicamente, situação gerada por uma doença viral, que já pode e deve ser combatida com a vacinação. Será interessante olhar no futuro o quanto a vacinação de COVID-19 em 2021 influenciou a cobertura de outras vacinas. 

Identificamos também alguns problemas relacionados ao acesso de um grande banco de dados público. Informações incompletas (grande parte do conjunto de dados não estava disponível na escala de Mês/Ano por exemplo) ou errôneas, como indentificadores ambíguos, redundantes e não relacionados ao tema atrapalham na hora de identificar corretamente os dados. É dever do profissional que vá trabalhar com esses dados estudar corretamente as medidas a serem tomadas, conversar com especialistas e identificar quais os problemas de nomenclatura com o qual não está familiarizado, etc. As vezes a etapa mais difícil de uma análise de dados é justamente a obtenção e tratamento dos dados.

# Referências: 

https://antigo.saude.gov.br/images/pdf/2017/agosto/17/AACOBERTURAS-VACINAIS-NO-BRASIL---2010-2014.pdf

https://saude.abril.com.br/medicina/por-que-as-pessoas-estao-tomando-menos-vacina/

http://www2.datasus.gov.br/DATASUS/index.php?area=02

https://revistapesquisa.fapesp.br/as-razoes-da-queda-na-vacinacao/

https://revistapesquisa.fapesp.br/wp-content/uploads/2018/08/Calend%C3%A1rio_Vacina%C3%A7%C3%A3o_270.pdf

https://pt.wikipedia.org/wiki/Lista_de_unidades_federativas_do_Brasil_por_incid%C3%AAncia_da_pobreza

https://portalarquivos2.saude.gov.br/images/pdf/2019/setembro/25/boletim-especial-21ago19-web.pdf

https://www.agazeta.com.br/es/cotidiano/veja-quais-sao-as-vacinas-obrigatorias-em-todas-as-fases-da-vida-0920

https://bvsms.saude.gov.br/bvs/publicacoes/cart_vac.pdf

http://tabnet.datasus.gov.br/tabdata/LivroIDB/2edrev/f13.pdf

https://www.aredacao.com.br/vida-e-saude/15700/vacina-pentavalente-substitui-a-tetravalente-e-esta-a-disposicao

https://www.saude.sc.gov.br/index.php/documentos/informacoes-gerais/redes-de-atencao-a-saude-2/rede-aten-a-saude-materna-e-infantil-rede-cegonha/acervo-e-e-books/10411-nota-tecnica-vacinacao-para-gestantes-dtpa-prevencao-de-coqueluche-em-menores-de-1-ano/file

https://pt.wikipedia.org/wiki/Surto_de_febre_amarela_no_Brasil_em_2016-2017

https://www.nationalgeographicbrasil.com/animais/2018/05/bugios-febre-amarela-epidemia-macacos-extincao

https://gauchazh.clicrbs.com.br/pioneiro/geral/noticia/2021/04/mortes-de-bugios-por-febre-amarela-colocam-16-cidades-em-alerta-maximo-na-serra-cko367uvw003i0180rs8o4rx0.html

https://gizmodo.uol.com.br/como-o-brasil-se-tornou-referencia-em-vacinacao-e-por-que-este-legado-esta-indo-para-o-lixo/
