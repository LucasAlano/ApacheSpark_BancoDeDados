# Configurando Ambiente PySpark e JupyterLab com Docker

Este repositório fornece instruções para configurar um ambiente de desenvolvimento PySpark e JupyterLab usando Docker.

Acadêmicos: Lucas de Oliveira Alano & João Victor Miontelli Vitali

## PRÉ-REQUISITOS

- Docker instalado em seu sistema.

## PASSO 1: CONSTRUINDO A IMAGEM DOCKER PARA PYSPARK E JUPYTERLAB

1. Crie um arquivo Dockerfile e adicione o seguinte código:

    ```Dockerfile
    # Baixando Imagem Principal do Docker
    FROM jupyter/pyspark-notebook:latest

    # Instale qualquer dependência adicional
    # Exemplo: pandas, numpy, etc.
    RUN pip install pandas numpy

    # Exponha a porta 8888 para acessar o JupyterLab
    EXPOSE 8888
    ```

2. Construa a imagem Docker:

    ```bash
    docker build -t pyspark-jupyter .
    ```

## PASSO 2: INICIANDO A IMAGEM DOCKER

1. Execute o seguinte comando para iniciar a imagem:

    ```bash
    docker run -it --rm -p 8888:8888 pyspark-jupyter
    ```

2. Acesse o JupyterLab no seu navegador utilizando o seguinte link: [http://localhost:8888](http://localhost:8888)

3. Insira o token fornecido pelo Docker no JupyterLab.

    ![Acesso ao JupyterLab](URL_DA_IMAGEM)

## PASSO 3: CONFIGURANDO O AMBIENTE COM APACHE SPARK

1. Crie um arquivo Dockerfile para o Apache Spark e adicione o seguinte código:

    ```Dockerfile
    FROM ubuntu:latest

    # Instalação do Java
    RUN apt-get update && \
        apt-get install -y openjdk-8-jdk

    # Download e instalação do Spark
    RUN apt-get install -y wget && \
        wget -q https://downloads.apache.org/spark/spark-3.2.0/spark-3.2.0-bin-hadoop3.2.tgz && \
        tar xf spark-3.2.0-bin-hadoop3.2.tgz -C /opt && \
        rm spark-3.2.0-bin-hadoop3.2.tgz

    # Configuração das variáveis de ambiente
    ENV JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
    ENV SPARK_HOME=/opt/spark-3.2.0-bin-hadoop3.2
    ENV PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin

    # Configuração do PySpark
    ENV PYSPARK_PYTHON=python3
    ENV PYTHONPATH=$SPARK_HOME/python:$SPARK_HOME/python/lib/py4j-0.10.9-src.zip

    # Expor a porta para a UI do Spark
    EXPOSE 8080 7077 4040

    CMD ["spark-shell"]
    ```

2. Construa a imagem Docker para o Apache Spark:

    ```bash
    docker build -t spark-local .
    ```

3. Execute o contêiner:

    ```bash
    docker run -it spark-local
    ```

## PASSO 4: IMPORTANDO E UTILIZANDO PYSPARK

```python
from pyspark import SparkContext, SparkConf
from pyspark.sql import SparkSession

# Configuração do Spark
conf = SparkConf().setAppName("MySparkApp")
sc = SparkContext(conf=conf)
spark = SparkSession(sc)


# Exemplo de uso
data = [("Java", 20000), ("Python", 100000), ("Scala", 3000)]
rdd = sc.parallelize(data)
df = spark.createDataFrame(rdd, ["Language", "Popularity"])
df.show()

```
Como pode-se perceber acabamos realizando apenas uma parte da pesquisa, já que não conseguimos conectar o Date Lake e o Icebarg
