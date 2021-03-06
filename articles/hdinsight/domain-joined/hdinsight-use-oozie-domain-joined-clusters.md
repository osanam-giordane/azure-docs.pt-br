---
title: Fluxos de trabalho do Hadoop Oozie em clusters HDInsight ingressados no domínio
description: Use o Hadoop Oozie no Enterprise Security Package ingressado no domínio do HDInsight baseado no Linux. Saiba como definir um fluxo de trabalho do Oozie e enviar um trabalho do Oozie.
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d7603471-5076-43d1-8b9a-dbc4e366ce5d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: omidm
ms.openlocfilehash: 928f6adbb348683a110f7da9b20efaae998290ca
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972206"
---
#<a name="run-apache-oozie-in-domain-joined-hdinsight-hadoop-clusters"></a>Executar o Apache Oozie em clusters HDInsight Hadoop ingressados no domínio
O Oozie é um sistema de fluxo de trabalho e coordenação que gerencia trabalhos do Hadoop. O Oozie é integrado à pilha do Hadoop e dá suporte aos seguintes trabalhos:
- Apache MapReduce
- Apache Pig
- Apache Hive
- Apache Sqoop

Você também pode usar o Oozie para agendar trabalhos específicos para um sistema, como programas Java ou scripts de shell.

##<a name="prerequisites"></a>Pré-requisitos:
- Um cluster HDInsight Hadoop ingressado no domínio. Confira [Configurar clusters HDInsight ingressados no domínio](./apache-domain-joined-configure-using-azure-adds.md)

    > [!NOTE]
    > Para ver instruções detalhadas sobre como usar o Oozie em clusters não ingressados no domínio, confira [isto](../hdinsight-use-oozie-linux-mac.md)

##<a name="connecting-to-domain-joined-cluster"></a>Conectando-se ao cluster ingressado no domínio
Para obter mais informações sobre o SSH, confira [Autenticação: HDInsight ingressado no domínio](../hdinsight-hadoop-linux-use-ssh-unix.md).
- Conecte-se ao cluster HDInsight usando SSH:
     ```bash
    ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
    ```
Para verificar se a autenticação Kerberos foi bem-sucedida, use o comando `klist`. Caso contrário, use `kinit` para iniciar a autenticação Kerberos.

- Fazer logon no gateway de HDInsight para registrar o token OAuth necessário para acessar o ADLS (Azure Data Lake Store)
     ```bash
     curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
     ```

    Um código de resposta de status _200 OK_ indica um registro bem-sucedido. Verifique o nome de usuário e a senha, caso uma resposta não autorizada seja recebida (401).

## <a name="define-the-workflow"></a>Definir o fluxo de trabalho
As definições de fluxo de trabalho do Oozie são codificadas em hPDL (linguagem de definição de processo do Hadoop), que é uma linguagem de definição de processo XML. Use as etapas a seguir para definir o fluxo de trabalho:

-   Configurando o espaço de trabalho de um usuário de domínio:
 ```bash
hdfs dfs -mkdir /user/<DomainUser>
cd /home/<DomainUserPath>
cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
tar -xvf oozie-examples.tar.gz
hdfs dfs -put examples /user/<DomainUser>/
 ```
Substitua `DomainUser` pelo nome de usuário de domínio. Substitua `DomainUserPath` pelo caminho do diretório base do usuário de domínio. Substitua `ClusterVersion` por sua versão HDP do cluster.

-   Use a instrução a seguir para criar e editar um novo arquivo:
 ```bash
nano workflow.xml
 ```

- Quando o editor nano for aberto, insira o seguinte XML como conteúdo do arquivo:
 ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <workflow-app xmlns="uri:oozie:workflow:0.4" name="map-reduce-wf">
       <credentials>
          <credential name="metastore_token" type="hcat">
             <property>
                <name>hcat.metastore.uri</name>
                <value>thrift://hn0-<clustername>.<Domain>.com:9083</value>
             </property>
             <property>
                <name>hcat.metastore.principal</name>
                <value>hive/_HOST@<Domain>.COM</value>
             </property>
          </credential>
          <credential name="hs2-creds" type="hive2">
             <property>
                <name>hive2.server.principal</name>
                <value>${jdbcPrincipal}</value>
             </property>
             <property>
                <name>hive2.jdbc.url</name>
                <value>${jdbcURL}</value>
             </property>
          </credential>
       </credentials>
       <start to="mr-test" />
       <action name="mr-test">
          <map-reduce>
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <prepare>
                <delete path="${nameNode}/user/${wf:user()}/examples/output-data/mrresult" />
             </prepare>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
                <property>
                   <name>mapred.mapper.class</name>
                   <value>org.apache.oozie.example.SampleMapper</value>
                </property>
                <property>
                   <name>mapred.reducer.class</name>
                   <value>org.apache.oozie.example.SampleReducer</value>
                </property>
                <property>
                   <name>mapred.map.tasks</name>
                   <value>1</value>
                </property>
                <property>
                   <name>mapred.input.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/input-data/text</value>
                </property>
                <property>
                   <name>mapred.output.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/output-data/mrresult</value>
                </property>
             </configuration>
          </map-reduce>
          <ok to="myHive2" />
          <error to="fail" />
       </action>
       <action name="myHive2" cred="hs2-creds">
          <hive2 xmlns="uri:oozie:hive2-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <jdbc-url>${jdbcURL}</jdbc-url>
             <script>${hiveScript2}</script>
             <param>hiveOutputDirectory2=${hiveOutputDirectory2}</param>
          </hive2>
          <ok to="myHive" />
          <error to="fail" />
       </action>
       <action name="myHive" cred="metastore_token">
          <hive xmlns="uri:oozie:hive-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
             </configuration>
             <script>${hiveScript1}</script>
             <param>hiveOutputDirectory1=${hiveOutputDirectory1}</param>
          </hive>
          <ok to="end" />
          <error to="fail" />
       </action>
       <kill name="fail">
          <message>Oozie job failed, error message[${wf:errorMessage(wf:lastErrorNode())}]</message>
       </kill>
       <end name="end" />
    </workflow-app>
 ```
Substitua `clustername` pelo nome do cluster. 

Para salvar o arquivo, selecione Ctrl+X, digite `Y`e selecione **Enter**.

O fluxo de trabalho é dividido em duas partes:
*   Seção de Credenciais: a seção de credenciais usa as credenciais que serão usadas para autenticar as ações do Oozie:

    Neste exemplo, a autenticação para as ações do Hive é usada. Para saber mais, confira [isto]( https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html).

    O serviço de credenciais permite que as ações do Oozie representem o usuário para acessar os serviços do Hadoop.

*   Seção da Ação: temos três ações aqui – map-reduce, uma ação do servidor do Hive 2 e uma ação do servidor do Hive 1:

    O map-reduce executa um exemplo do pacote do Oozie para o map-reduce, que gera a contagem de palavras agregadas.

    As ações do servidor do Hive 1 e do servidor do Hive 2 executam uma consulta simples na tabela hivesample fornecida com o HDInsight.

    As ações do Hive usam as credenciais definidas na seção de credenciais para autenticação usando a palavra-chave `cred` no elemento action

- Use o seguinte comando para copiar o arquivo `workflow.xml` para `/user/<domainuser>/examples/apps/map-reduce/workflow.xml`:
     ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
     ```

    Substitua `domainuser` por seu nome de usuário para o domínio.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Definir o arquivo de propriedades para o trabalho do Oozie

1.  Use a seguinte instrução para criar e editar as propriedades de trabalho de um novo arquivo:
     ```bash
    nano job.properties
     ```

2.   Quando o editor nano for aberto, use o seguinte XML como conteúdo do arquivo:

    ```bash
        nameNode=adl://home
        jobTracker=headnodehost:8050
        queueName=default
        examplesRoot=examples
        oozie.wf.application.path=${nameNode}/user/[domainuser]/examples/apps/map-reduce/workflow.xml
        hiveScript1=${nameNode}/user/${user.name}/countrowshive1.hql
        hiveScript2=${nameNode}/user/${user.name}/countrowshive2.hql
        oozie.use.system.libpath=true
        user.name=[domainuser]
        jdbcPrincipal=hive/hn0-<ClusterShortName>.<Domain>.com@<Domain>.COM
        jdbcURL=[jdbcurlvalue]
        hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
        hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
    ```
    

   * Substitua `domainuser` por seu nome de usuário para o domínio.
   * Substitua `ClusterShortName` pelo nome curto do cluster. Se for o nome do cluster for https://sechadoopcontoso.azurehdisnight.net, o `clustershortname` será as seis primeiras letras do cluster: sechad
   * Substitua `jdbcurlvalue` pela URL do JDBC da configuração do Hive. Por exemplo, jdbc:hive2://headnodehost:10001/;transportMode=http.
    
   * Para salvar o arquivo, selecione Ctrl+X, digite `Y`e selecione **Enter**.

   * Esse arquivo de propriedades precisa estar presente localmente durante a execução de trabalhos do Oozie

## <a name="creating-custom-hive-scripts-for-the-oozie-job"></a>Criando scripts personalizados do Hive para o trabalho do Oozie
Os dois scripts do Hive para o servidor do Hive 1 e o servidor do Hive 2 podem ser criados da seguinte maneira:
-   Arquivo do Servidor do Hive 1:
1.  Use a seguinte instrução para criar e editar a ação do servidor do Hive 1 em um arquivo:
    ```bash
    nano countrowshive1.hql
    ```

2.  Criar o script
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemake from hivesampletable limit 2;
    ```

3.  Salvar o arquivo no HDFS
    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

-   Arquivo do Servidor do Hive 2:
1.  Use a seguinte instrução para criar e editar a ação do servidor do Hive 2 em um campo:
    ```bash
    nano countrowshive2.hql
    ```

2.  Criar o script
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3.  Salvar o arquivo no HDFS
    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submission-of-oozie-jobs"></a>Envio de trabalhos do Oozie
O envio de trabalhos do Oozie para clusters ingressados no domínio é semelhante ao envio de trabalhos do Oozie em clusters não ingressados no domínio.

Para obter mais informações, confira [Enviar e gerenciar o trabalho](../hdinsight-use-oozie-linux-mac.md).

## <a name="results-from-oozie-job-submission"></a>Resultados do envio de trabalhos do Oozie
Como os trabalhos do Oozie são executados em nome do usuário, os logs de auditoria do YARN e do Ranger mostram os trabalhos que estão sendo executados como o usuário representado. A saída da CLI do trabalho do Oozie é semelhante ao mostrado abaixo:


```bash
    Job ID : 0000015-180626011240801-oozie-oozi-W
    ------------------------------------------------------------------------------------------------
    Workflow Name : map-reduce-wf
    App Path      : adl://home/user/alicetest/examples/apps/map-reduce/wf.xml
    Status        : SUCCEEDED
    Run           : 0
    User          : alicetest
    Group         : -
    Created       : 2018-06-26 19:25 GMT
    Started       : 2018-06-26 19:25 GMT
    Last Modified : 2018-06-26 19:30 GMT
    Ended         : 2018-06-26 19:30 GMT
    CoordAction ID: -
    
    Actions
    ------------------------------------------------------------------------------------------------
    ID                      Status  Ext ID          ExtStatus   ErrCode
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@:start:    OK  -           OK      -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@mr-test    OK  job_1529975666160_0051  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@myHive2    OK  job_1529975666160_0053  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@myHive OK  job_1529975666160_0055  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@end    OK  -           OK      -
    -----------------------------------------------------------------------------------------------
```

*    Os logs de auditoria do Ranger para as ações do servidor do Hive 2 mostram o Oozie executando a ação em nome do usuário. A exibição do Ranger e do YARN só está visível para o administrador de cluster.

## <a name="configuration-of-user-authorization-in-oozie"></a>Configuração da autorização de usuário no Oozie
O Oozie por si só tem uma configuração de Autorização do Usuário, que pode impedir a interrupção dos usuários, encerrando os trabalhos de outro usuário. Isso é habilitado com a definição de `oozie.service.AuthorizationService.security.enabled` para `true`. 

Encontre mais detalhes sobre isso na seção de documentação do Oozie – [Configuração de autorização do usuário]( https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html):

Para componentes, como o servidor do Hive 1, no qual o plug-in do Ranger não está disponível/é compatível, é possível apenas realizar a autorização de alta granularidade do HDFS. A autorização refinada só está disponível por meio dos plug-ins do Ranger.

## <a name="oozie-web-ui"></a>Interface do usuário da Web do Oozie
A IU da Web do Oozie fornece um modo de exibição baseado na web sobre o status dos trabalhos do Oozie no cluster. Em clusters ingressados no domínio, você precisa:

1. Adicionar um [nó de borda](../hdinsight-apps-use-edge-node.md) e habilitar a [autenticação SSH Kerberos](../hdinsight-hadoop-linux-use-ssh-unix.md)

2. Siga as etapas da [interface do usuário da Web do Oozie](../hdinsight-use-oozie-linux-mac.md) para habilitar o túnel SSH no nó de borda e acessar a interface do usuário da Web.

## <a name="next-steps"></a>Próximas etapas
* [Usar o Oozie com o Hadoop para definir e executar um fluxo de trabalho no Azure HDInsight baseado no Linux](../hdinsight-use-oozie-linux-mac.md)
* [Usar coordenador do Oozie baseado em tempo](../hdinsight-use-oozie-coordinator-time.md)
* [Executando consultas do Hive usando o SSH em clusters HDInsight ingressados no domínio](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
