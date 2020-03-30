---
title: Apache Oozie-werkstromen & Enterprise Security - Azure HDInsight
description: Beveilig Apache Oozie-werkstromen met behulp van het Azure HDInsight Enterprise Security Package. Meer informatie over het definiëren van een Oozie-werkstroom en het indienen van een Oozie-taak.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seodec18
ms.date: 12/09/2019
ms.openlocfilehash: 9ef54707f7fac3dd1328e29f6d05f62c1dee2561
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194900"
---
# <a name="run-apache-oozie-in-hdinsight-hadoop-clusters-with-enterprise-security-package"></a>Apache Oozie uitvoeren in HDInsight Hadoop-clusters met Enterprise Security Package

Apache Oozie is een workflow- en coördinatiesysteem dat Apache Hadoop-taken beheert. Oozie is geïntegreerd met de Hadoop stack en ondersteunt de volgende taken:

- Apache MapReduce
- Apache Varken
- Apache Bijenkorf
- Apache Sqoop

U Oozie ook gebruiken om taken te plannen die specifiek zijn voor een systeem, zoals Java-programma's of shellscripts.

## <a name="prerequisite"></a>Vereiste

Een Azure HDInsight Hadoop-cluster met Enterprise Security Package (ESP). Zie [HDInsight-clusters configureren met ESP](./apache-domain-joined-configure-using-azure-adds.md).

> [!NOTE]  
> Zie [Apache Oozie-werkstromen gebruiken in Op Linux gebaseerde Azure HDInsight](../hdinsight-use-oozie-linux-mac.md)voor gedetailleerde instructies over het gebruik van Oozie op niet-ESP-clusters.

## <a name="connect-to-an-esp-cluster"></a>Verbinding maken met een ESP-cluster

Zie [Connect to HDInsight (Hadoop) met SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie over Secure Shell (SSH).

1. Maak verbinding met het HDInsight-cluster met SSH:

    ```bash
    ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
    ```

1. Als u succesvolle Kerberos-verificatie wilt verifiëren, gebruikt u de `klist` opdracht. Als dit `kinit` niet het zo is, gebruikt u Kerberos-verificatie.

1. Meld u aan bij de HDInsight-gateway om het OAuth-token te registreren dat vereist is om toegang te krijgen tot Azure Data Lake Storage:

    ```bash
    curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
    ```

    Een statusresponscode van **200 OK** geeft een succesvolle registratie aan. Controleer de gebruikersnaam en het wachtwoord als er een ongeautoriseerd antwoord is ontvangen, zoals 401.

## <a name="define-the-workflow"></a>De werkstroom definiëren

Oozie workflow definities zijn geschreven in Apache Hadoop Process Definition Language (hPDL). hPDL is een XML-procesdefinitietaal. Volg de volgende stappen om de werkstroom te definiëren:

1. De werkruimte van een domeingebruiker instellen:

   ```bash
   hdfs dfs -mkdir /user/<DomainUser>
   cd /home/<DomainUserPath>
   cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
   tar -xvf oozie-examples.tar.gz
   hdfs dfs -put examples /user/<DomainUser>/
   ```

   Vervang `DomainUser` door de domeinnaam.
   Vervang `DomainUserPath` door het thuismappad voor de domeingebruiker.
   Vervang `ClusterVersion` door de versie van het clustergegevensplatform.

2. Gebruik de volgende instructie om een nieuw bestand te maken en te bewerken:

   ```bash
   nano workflow.xml
   ```

3. Nadat de nano-editor is geopend, voert u de volgende XML in als inhoud van het bestand:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <workflow-app xmlns="uri:oozie:workflow:0.4" name="map-reduce-wf">
       <credentials>
          <credential name="metastore_token" type="hcat">
             <property>
                <name>hcat.metastore.uri</name>
                <value>thrift://<active-headnode-name>-<clustername>.<Domain>.com:9083</value>
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

4. Vervang `clustername` door de naam van het cluster.

5. Als u het bestand wilt opslaan, selecteert u **Ctrl+X**. Voer **Y**in . Selecteer vervolgens **Enter**.

    De werkstroom is verdeeld in twee delen:

   - **Referentie.** In deze sectie worden de referenties ingenomen die worden gebruikt voor het verifiëren van Oozie-acties:

     In dit voorbeeld wordt verificatie gebruikt voor Hive-acties. Zie [Actieverificatie](https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html)voor meer informatie.

     Met de referentieservice kunnen Oozie-acties zich voordoen als de gebruiker voor toegang tot Hadoop-services.

   - **Actie.** In deze sectie worden drie acties uitgevoerd: map-reduce, Hive server 2 en Hive server 1:

     - Met de actie kaartverminderen wordt een voorbeeld uitgevoerd van een Oozie-pakket voor kaartverkleinen dat het geaggregeerde aantal woorden uitvoert.

     - Met de acties Hive server 2 en Hive server 1 wordt een query uitgevoerd op een voorbeeldtabel van Hive met HDInsight.

     De Hive-acties gebruiken de referenties die zijn gedefinieerd in `cred` de sectie referenties voor verificatie met behulp van het trefwoord in het actie-element.

6. Gebruik de volgende opdracht `workflow.xml` om `/user/<domainuser>/examples/apps/map-reduce/workflow.xml`het bestand te kopiëren naar:

    ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
    ```

7. Vervang `domainuser` door uw gebruikersnaam voor het domein.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Het eigenschappenbestand voor de taak Oozie definiëren

1. Gebruik de volgende instructie om een nieuw bestand voor taakeigenschappen te maken en te bewerken:

    ```bash
    nano job.properties
    ```

2. Nadat de nano-editor is geopend, gebruikt u de volgende XML als de inhoud van het bestand:

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
   jdbcPrincipal=hive/<active-headnode-name>.<Domain>.com@<Domain>.COM
   jdbcURL=[jdbcurlvalue]
   hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
   hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
   ```

   - Gebruik `adl://home` de URI `nameNode` voor de eigenschap als u Azure Data Lake Storage Gen1 hebt als primaire clusteropslag. Als u Azure Blob Storage gebruikt, `wasb://home`wijzigt u dit in . Als u Azure Data Lake Storage Gen2 gebruikt, wijzigt u dit in `abfs://home`.
   - Vervang `domainuser` door uw gebruikersnaam voor het domein.  
   - Vervang `ClusterShortName` de korte naam voor het cluster. Als de clusternaam bijvoorbeeld https:// *[voorbeeldkoppeling]* `clustershortname` sechadoopcontoso.azurehdisnight.net is, zijn de eerste zes tekens van het cluster: **sechad**.  
   - Vervang `jdbcurlvalue` de JDBC-URL uit de Hive-configuratie. Een voorbeeld is jdbc:hive2://headnodehost:10001/;transportMode=http.
   - Als u het bestand wilt opslaan, selecteert u Ctrl+X, voert u Enter in `Y`en selecteert u **Enter**.

   Dit eigenschappenbestand moet lokaal aanwezig zijn bij het uitvoeren van Oozie-taken.

## <a name="create-custom-hive-scripts-for-oozie-jobs"></a>Aangepaste Hive-scripts maken voor Oozie-taken

U de twee Hive-scripts voor Hive-server 1 en Hive-server 2 maken, zoals in de volgende secties wordt weergegeven.

### <a name="hive-server-1-file"></a>Hive-server 1-bestand

1. Een bestand maken en bewerken voor Hive-server 1-acties:

    ```bash
    nano countrowshive1.hql
    ```

2. Maak het script:

    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    select devicemake from hivesampletable limit 2;
    ```

3. Sla het bestand op in Apache Hadoop Distributed File System (HDFS):

    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

### <a name="hive-server-2-file"></a>Hive server 2-bestand

1. Een veld maken en bewerken voor Hive-server 2-acties:

    ```bash
    nano countrowshive2.hql
    ```

2. Maak het script:

    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3. Sla het bestand op in HDFS:

    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submit-oozie-jobs"></a>Oozie-vacatures indienen

Het indienen van Oozie-taken voor ESP-clusters is als het indienen van Oozie-taken in niet-ESP-clusters.

Zie Apache Oozie gebruiken met Apache Hadoop voor meer informatie [om een werkstroom op Het op Linux gebaseerde Azure HDInsight te definiëren en uit te voeren.](../hdinsight-use-oozie-linux-mac.md)

## <a name="results-from-an-oozie-job-submission"></a>Resultaten van een Oozie vacature

Oozie-taken worden uitgevoerd voor de gebruiker. Dus zowel Apache Hadoop YARN en Apache Ranger audit logs tonen de banen worden uitgevoerd als de nagebootste gebruiker. De opdrachtregelinterface-uitvoer van een Oozie-taak ziet eruit als de volgende code:

```output
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

De Ranger-controlelogboeken voor Hive-server 2-acties tonen oozie die de actie voor de gebruiker uitvoert. De Ranger- en YARN-weergaven zijn alleen zichtbaar voor de clusterbeheerder.

## <a name="configure-user-authorization-in-oozie"></a>Gebruikersautorisatie configureren in Oozie

Oozie heeft op zichzelf een gebruikersautorisatieconfiguratie die kan voorkomen dat gebruikers taken van andere gebruikers stoppen of verwijderen. Als u deze configuratie `oozie.service.AuthorizationService.security.enabled` `true`wilt inschakelen, stelt u de instelling in op . 

Zie [Apache Oozie Installatie en configuratie voor](https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html)meer informatie.

Voor componenten zoals Hive server 1 waar de Ranger-plug-in niet beschikbaar of ondersteund is, is alleen een grofkorrelige HDFS-autorisatie mogelijk. Fijnkorrelige autorisatie is alleen beschikbaar via Ranger plug-ins.

## <a name="get-the-oozie-web-ui"></a>Download de Oozie web UI

De Oozie web-gebruikersinterface biedt een webgebaseerde weergave van de status van Oozie-taken op het cluster. Ga als volgt te werk om de webgebruikersinterface te krijgen:

1. Voeg een [randknooppunt](../hdinsight-apps-use-edge-node.md) toe en schakel [SSH Kerberos-verificatie](../hdinsight-hadoop-linux-use-ssh-unix.md)in.

2. Volg de [oozie web-gebruikersinterfacestappen](../hdinsight-use-oozie-linux-mac.md) om SSH-tunneling naar het randknooppunt mogelijk te maken en toegang te krijgen tot de web-gebruikersinterface.

## <a name="next-steps"></a>Volgende stappen

- [Gebruik Apache Oozie met Apache Hadoop om een werkstroom op Linux-gebaseerde Azure HDInsight te definiëren en uit te voeren.](../hdinsight-use-oozie-linux-mac.md)
- [Maak verbinding met HDInsight (Apache Hadoop) met Behulp van SSH](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
