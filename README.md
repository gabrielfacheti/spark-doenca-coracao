<!DOCTYPE html>
<html>
		
<body>

<h1>Machine Learning com PySpark: Previsão de doença cardíaca</h1>
<p>Este notebook foi desenvolvido como parte dos meus estudos no ecossistema da Apache.<br><br>
Tudo começa com a criação de uma máquina virtual com sistema CentOS, baseado em Linux. Na sequência, foi feito o download e a configuração dos frameworks que seriam usados no projeto. Neste caso, foi usado o Apache Hadoop em modo pseudo-cluster, isto é, um cluster de uma máquina só. Tal cluster roda com o HDFS (Hadoop Distributed File System), sistema de arquivos distribuídos do Hadoop, e o Yarn, gerenciador de recursos do Hadoop. O sistema, da maneira que foi configurado, roda conforme a imagem abaixo.</p><br>

![image](https://github.com/gabrielfacheti/spark-doenca-coracao/assets/106284497/f9ee0889-0b64-4973-a457-9c42f324f084)

Com o intuito de criar um banco de dados relacional que roda sobre o HDFS, foi instalado e configurado o Apache Hive. Dessa forma, pôde-se criar uma tabela com os dados dos pacientes (disponível neste repositório em arquivo .csv) que alimentaria os algoritmos de machine learning do Apache Spark. Além disso, o serviço metastore do Hive esteve ativo durante todo o tempo por meio do comando <code>hive --service metastore</code>, assim a consulta ao banco de dados pôde ser feita via Spark.<br>
Por fim, o cluster Spark foi configurado de maneira que funcionasse em modo remoto (como pode ser visto pelo IP de acesso ao cluster), assim, qualquer computador dentro da mesma rede poderia acessá-lo. 

<h2>Dataset e Objetivo</h2>
O objetivo do projeto era prever se um paciente terá ou não uma doença cardíaca com base em dados de pacientes presentes no bando de dados Hive.<br>
O dataset é composto por 303 linhas e 9 colunas, com informações médicas dos pacientes.

<h2>Metodologia</h2>

<h3>Exploratory Data Analysis</h3>
Aqui foi feita a separação entre variáveis categóricas e numéricas, para que ambos os tipos de variáveis pudessem ser avaliados separadamente. Uma parte da exploração foi feita diretamente com as funções do PySpark e o restante, por conta das visualizações, foi feita com a partir da criação de um pandas DataFrame. A visualização dos dados foi feita através de histogramas e boxplots para as variáveis numéricas e countplots para as variáveis categóricas. Além disso, a correlação entre as variáveis preditoras e o target foi analisada, a fim de verificar se havia multicolinearidade.

<h3>Pré-Processamento de dados</h3>
Durante o pré-processamento, o Spark exige a criação de um vetor que contém todas as variáveis preditoras. Tal vetor é contruído pela função <code>VectorAssembler</code>. Na sequência, houve a separação dos dados em treino e teste, usando a proporção de 70/30, respectivamente.<br>
Como algumas das colunas apresentavam uma distribuição que não correspondia a Distribuição Normal, foi feita a padronização dos dados com a função <code>StandardScaler</code>, para garantir que todas tivessem uma distribuição parecida (próxima de uma normal).

<h3>Seleção dos modelos</h3>
Cinco algoritmos diferentes – Regressão Logística, Decision Tree, Random Forest, Naive Bayes e Gradient-Boosted Trees (GBT) – foram testados e avaliados em uma validação cruzada (com <code>CrossValidator</code>), usando como métricas a acurácia e o F1 Score. Os resultados foram guardados em um DataFrame para posterior comparação.<br>
Vale ressaltar que o algoritmo de Regressão Logística serviu como modelo de benchmark, ou seja, ele foi avaliado de forma pura, sem qualquer melhoria. O restante dos algoritmos, passou por uma tunagem de hiperparâmetros quando dentro da validação cruzada. Tal processo foi feito para verificar se a tunagem de hiperparâmetros traria melhorias aos modelos em relação ao de Regressão Logística.<br>
No fim, o modelo com os melhores resultados foi o Random Forest, chegando a uma acurácia de 73%.

<h2>Resultados</h2>
Por fim, utilizando o modelo já treinado e com os melhores hiperparâmetros, foi feita uma previsão utilizando dados hipotéticos, que passaram pelos mesmos processamentos que os dados de treino e teste. Nesta análise foi previsto que o paciente deve apresentar doença cardíaca.<br>

</body>
</html>
