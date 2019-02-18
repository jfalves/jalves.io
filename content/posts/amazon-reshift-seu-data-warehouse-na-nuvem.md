---
draft: false
title: "Amazon Redshift, seu data warehouse nas nuvens"
date: 2019-02-17T10:53:32-03:00
authors:
  - Jonathan Alves
---
Uma das tendências tecnologicas de mais destaque atualmente é a ter uma operação cada vez mais focada na resolução dos problemas de negócio e consequentemente, menos ativa em seus "_efeitos colaterais_" como a administração de servidores e serviços, infraestrutura, etc.

<center>
    ![Amazon Redshift](/images/21-amazon-redshift.jpg)</br>
    _Amazon Redshift, seu data warehouse na nuvem_
</center>

Dito isso, empresas como [Google](https://cloud.google.com/products/), [Microsoft](https://azure.microsoft.com/pt-br/services/) e [Amazon](https://aws.amazon.com/pt/products/) têm lançado produtos completos que atendem as companias sem a necessidade de um conhecimento profundo em áreas que não são o coração do negócio. Esse é o caso da [Amazon Redshift](https://aws.amazon.com/pt/redshift/), plataforma de data warehouse na nuvem oferecida pela Amazon Web Services e da qual irei falar um pouco sobre.

## Sobre a tecnologia
A plataforma foi lançada em 2012 e seu principal objetivo foi agregar os serviços já oferecidos pela compania como por exemplo a [Amazon RDS](https://aws.amazon.com/pt/rds/), porém, focado no processamento de alto volumes de dados analíticos. Aliás performance é uma das caracteristicas fortes do serviço, a arquitetura é baseada na tecnologia Massively Parallel Processing (MPP – Processamento paralelo em massa), desenvolvida pela [ParAccel](https://www.actian.com/), que distribui os dados entre máquinas a fim de aumentar a velocidade de acesso, algo parecido com o [MapReduce](https://pt.wikipedia.org/wiki/MapReduce), entretanto, transparente ao usuário na forma de consultas SQL padrão. Outra característica do serviço é o armazenamento colunar, típico de bancos analíticos e que é baseado em uma modificação do Postgres 8.0.2 para esse propósito. Também, é possível aplicar vários algoritmos de compactação nos dados, diminuindo espaço em disco e tráfego de rede.

<center>
    ![Arquitetura do Redshift](/images/22-amazon-redshift-architecture.jpg)</br>
    _Arquitetura básica de um cluster no Redshift._
</center>

Comparado a outras tecnologias, o Redshift é extremamente fácil de se configurar. Todas as operações podem ser feitas no [AWS Console](https://aws.amazon.com/pt/console/) ou também, é possível alterar por uma API através de um [_Client_](https://aws.amazon.com/pt/cli/) oferecido pela AWS. Ajustes de performance também são simples, sendo eles na sua maioria ou aumento da capacidade do cluster, ou mudanças nas chaves de distribuição e chaves de classificação. As chaves de distribuição indicam como o dado vai ser distribuído entre as máquinas de um _cluster_ enquanto que as chaves de classificação estão relacionadas a como o dado está ordenado, facilitando a busca nos discos/máquinas. Como qualquer banco de dados SQL, é possível analisar as consultas mais demoradas através do comando **_EXPLAIN_**.

<center>
    ![AWS Console](/images/23-aws-console.jpg)</br>
    _No AWS Console é possível fazer qualquer alteração em relação ao seu cluster._
</center>

Existem várias formas de se carregar dados na plataforma, algumas delas incluem S3, RDS, Data Pipeline, Kinesis ou até mesmo SSH, sendo essa última a mais indicada e performatica de todas. Por fim, acredito que segurança seja o tópico mais relevante para a decisão de migração de dados para o Redshift. A plataforma inclui várias opções de criptografia, tanto de disco quanto de transmissão, podendo ser altamente personalizada. Um ótimo caso de uso foi da [NASDAQ](https://www.nasdaq.com/), que migrou seus dados para a nuvem da Amazon e mesmo assim passou por todas várias auditorias de segurança da informação com sucesso.  

## Vale a pena?
Como dito no começo do artigo, utilizar serviços na nuvem por si só já é uma grande vantagem competitiva uma vez que precisa-se de menos mão de obra para cuidar dos "_efeitos colaterais_" e ainda sim atingir o mesmo resultado. Outra vantagem é o custo de operação que segue em linhas gerais a regra: Pague somente o que precisar e quando precisar. Por poder ajustar o provisionamento de máquinas a qualquer momento, não precisar manter sob sua infraestrutura máquinas com um determinado poder computacional, mesmo quando ociososas, já é uma vantagem e tanto. Para se ter uma ideia, o custo de operação dos clusters atuais, do menor para o maior, variam de 0,25 à 6,80 dólares à hora.

Podemos considerar como ponto negativo processamento de dados de forma intensiva, que mesmo sob um contrato anual pode ser custoso sendo melhor manter uma infraestrutura interna. Outro ponto negativo pode ser a flexibilidade do provisionamento de máquinas pois os usuários devem estar cientes que há um custo por tempo de análise, trazendo novas resposabilidades orçamentarias para o usuário final.  

## Leitura adicional
- [[1]](https://www.zdnet.com/article/amazon-redshift-paraccel-in-costly-appliances-out/) Amazon Redshift: ParAccel in, costly appliances out.
- [[2]](https://datascience.stackexchange.com/questions/305/does-amazon-redshift-replace-hadoop-for-1xtb-data) Does Amazon RedShift replace Hadoop for ~1XTB data?
- [[3]](https://docs.aws.amazon.com/pt_br/redshift/latest/dg/c_high_level_system_architecture.html) Arquitetura de sistema do data warehouse.
- [[4]](https://docs.aws.amazon.com/pt_br/redshift/latest/dg/c_designing-tables-best-practices.html) Melhores práticas do Amazon Redshift para projetar tabelas.
- [[5]](https://www.youtube.com/watch?v=O4wAH5FQjS8) AWS re:Invent 2014 | (FIN401) Seismic Shift: Nasdaq's Migration to Amazon Redshift.
- [[6]](https://aws.amazon.com/pt/redshift/pricing/) Definição de preço do Amazon Redshift.
