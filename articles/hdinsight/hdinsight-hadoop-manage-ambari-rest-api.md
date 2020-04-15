---
title: Hadoop bewaken en beheren met Ambari REST API - Azure HDInsight
description: Meer informatie over het gebruik van Ambari om Hadoop-clusters in Azure HDInsight te monitoren en te beheren. In dit document leert u hoe u de Ambari REST API gebruiken die is opgenomen in HDInsight-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: 317d12f6d5dee92d998266d4e9b6d52e6ef9c7a5
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381383"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>HDInsight-clusters beheren met behulp van de Apache Ambari REST API

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Meer informatie over het gebruik van de Apache Ambari REST API voor het beheren en bewaken van Apache Hadoop-clusters in Azure HDInsight.

## <a name="what-is-apache-ambari"></a>Wat is Apache Ambari

[Apache Ambari](https://ambari.apache.org) vereenvoudigt het beheer en de monitoring van Hadoop-clusters door een eenvoudig te gebruiken web-gebruikersinterface te bieden, ondersteund door zijn [REST API's.](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)  Ambari wordt standaard geleverd met HdInsight-clusters op basis van Linux.

## <a name="prerequisites"></a>Vereisten

* Een Hadoop cluster op HDInsight. Zie [Aan de slag met HDInsight op Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* Bash op Ubuntu op Windows 10.  De voorbeelden in dit artikel gebruiken de Bash-shell op Windows 10. Zie [Windows Subsystem for Linux Installation Guide voor Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) voor installatiestappen.  Andere [Unix schelpen](https://www.gnu.org/software/bash/) zal ook werken.  De voorbeelden, met enkele kleine wijzigingen, kunnen werken op een Windows Command prompt.  Of u Windows PowerShell gebruiken.

* jq, een command-line JSON processor.  Zie [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* Windows PowerShell.  Of je [Bash](https://www.gnu.org/software/bash/)gebruiken.

## <a name="base-uniform-resource-identifier-for-ambari-rest-api"></a>Uniforme resource-id baseren voor Ambari Rest-API

 De basis Uniform Resource Identifier (URI) voor de Ambari REST API op HDInsight is `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`, waar `CLUSTERNAME` is de naam van uw cluster.  Clusternamen in URI's zijn **hoofdlettergevoelig**.  Hoewel de clusternaam in het volledig gekwalificeerde domeinnaam (FQDN) deel van de URI (`CLUSTERNAME.azurehdinsight.net`) case-ongevoelig is, zijn andere gebeurtenissen in de URI hoofdlettergevoelig.

## <a name="authentication"></a>Verificatie

Verbinding maken met Ambari op HDInsight vereist HTTPS. Gebruik de naam van het beheerdersaccount (de **standaardbeheerder)** en het wachtwoord dat u tijdens het maken van het cluster hebt opgegeven.

Gebruik voor clusters van `admin`enterprisesecuritypakketten in plaats `username@domain.onmicrosoft.com`van een volledig gekwalificeerde gebruikersnaam zoals .

## <a name="examples"></a>Voorbeelden

### <a name="setup-preserve-credentials"></a>Instellen (Referenties behouden)

Bewaar uw referenties om te voorkomen dat ze opnieuw worden ingevoerd voor elk voorbeeld.  De clusternaam blijft in een afzonderlijke stap behouden.

**A. Bash**  
Bewerk het script hieronder `PASSWORD` door te vervangen door uw werkelijke wachtwoord.  Voer vervolgens de opdracht in.

```bash
export password='PASSWORD'
```  

**B. PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>De juiste clusternaam van de hoofdletters identificeren

De werkelijke behuizing van de clusternaam kan anders zijn dan u verwacht.  De stappen hier tonen de werkelijke behuizing en slaan deze vervolgens op in een variabele voor alle latere voorbeelden.

Bewerk de onderstaande `CLUSTERNAME` scripts om uw clusternaam te vervangen. Voer vervolgens de opdracht in. (De clusternaam voor de FQDN is niet hoofdlettergevoelig.)

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

### <a name="parsing-json-data"></a>Json-gegevens ontleden

In het volgende voorbeeld wordt [jq](https://stedolan.github.io/jq/) of [ConvertFrom-Json](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json) gebruikt om `health_report` het JSON-antwoorddocument te ontleden en alleen de informatie uit de resultaten weer te geven.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" \
| jq '.Clusters.health_report'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```

### <a name="get-the-fqdn-of-cluster-nodes"></a>De FQDN van clusterknooppunten opvragen

Mogelijk moet u de volledig gekwalificeerde domeinnaam (FQDN) van een clusterknooppunt kennen. U de FQDN eenvoudig ophalen voor de verschillende knooppunten in het cluster met behulp van de volgende voorbeelden:

**Alle knooppunten**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```

**Hoofdknooppunten**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" \
| jq -r '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Werkknooppunten**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" \
| jq -r '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Dierenverzorgerknooppunten**

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
| jq -r ".host_components[].HostRoles.host_name"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

### <a name="get-the-internal-ip-address-of-cluster-nodes"></a>Het interne IP-adres van clusterknooppunten opvragen

De IP-adressen die worden geretourneerd door de voorbeelden in deze sectie zijn niet direct toegankelijk via het internet. Ze zijn alleen toegankelijk binnen het Azure Virtual Network dat het HDInsight-cluster bevat.

Zie [Een virtueel netwerk voor HDInsight plannen voor](hdinsight-plan-virtual-network-deployment.md)meer informatie over het werken met HDInsight en virtuele netwerken.

Als u het IP-adres wilt vinden, moet u de interne volledig gekwalificeerde domeinnaam (FQDN) van de clusterknooppunten kennen. Zodra u de FQDN hebt, u vervolgens het IP-adres van de host krijgen. In de volgende voorbeelden wordt ambari voor de FQDN van alle hostknooppunten eerst opgevraagd. Vervolgens vraagt Ambari voor het IP-adres van elke host.

```bash
for HOSTNAME in $(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```  

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" 
$resp = Invoke-WebRequest -Uri $uri -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds -UseBasicParsing
    $hostInfoObj = ConvertFrom-Json $hostInfoResp
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

### <a name="get-the-default-storage"></a>De standaardopslag aanschaffen

HDInsight-clusters moeten een Azure Storage-account of Data Lake Storage gebruiken als standaardopslag. U Ambari gebruiken om deze informatie op te halen nadat het cluster is gemaakt. Bijvoorbeeld als u gegevens wilt lezen/schrijven naar de container buiten HDInsight.

In de volgende voorbeelden wordt de standaardopslagconfiguratie uit het cluster opgehaald:

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq -r '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]  
> In deze voorbeelden wordt de eerste`service_config_version=1`configuratie die op de server ( ) is toegepast en die deze informatie bevat, teruggegeven. Als u een waarde ophaalt die is gewijzigd na het maken van het cluster, moet u mogelijk de configuratieversies weergeven en de laatste toevoegen.

De retourwaarde is vergelijkbaar met een van de volgende voorbeelden:

* `wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net`- Deze waarde geeft aan dat het cluster een Azure Storage-account gebruikt voor standaardopslag. De `ACCOUNTNAME` waarde is de naam van het opslagaccount. Het `CONTAINER` gedeelte is de naam van de blobcontainer in het opslagaccount. De container is de basis van de HDFS-compatibele opslag voor het cluster.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net`- Deze waarde geeft aan dat het cluster Azure Data Lake Storage Gen2 gebruikt voor standaardopslag. De `ACCOUNTNAME` `CONTAINER` waarden en waarden hebben dezelfde betekenis als voor Azure Storage die eerder is vermeld.

* `adl://home`- Deze waarde geeft aan dat het cluster Azure Data Lake Storage Gen1 gebruikt voor standaardopslag.

    Als u de naam van het Data Lake Storage-account wilt zoeken, gebruikt u de volgende voorbeelden:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    De retourwaarde is `ACCOUNTNAME.azuredatalakestore.net`vergelijkbaar `ACCOUNTNAME` met , waar is de naam van het Data Lake Storage-account.

    Als u de map in Data Lake Storage wilt zoeken die de opslag voor het cluster bevat, gebruikt u de volgende voorbeelden:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```  

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    De retourwaarde is `/clusters/CLUSTERNAME/`vergelijkbaar met . Deze waarde is een pad binnen het Data Lake Storage-account. Dit pad is de hoofdmap van het HDFS-compatibele bestandssysteem voor het cluster.  

> [!NOTE]  
> De [Get-AzHDInsightCluster-cmdlet](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) van [Azure PowerShell](/powershell/azure/overview) retourneert ook de opslaggegevens voor het cluster.

### <a name="get-all-configurations"></a>Alle configuraties opgebruiken

Download de configuraties die beschikbaar zijn voor uw cluster.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

In dit voorbeeld wordt een JSON-document geretourneerd met de huidige configuratie voor geïnstalleerde onderdelen. Zie de *tagwaarde.* Het volgende voorbeeld is een fragment uit de gegevens die zijn geretourneerd van een Spark-clustertype.

```json
"jupyter-site" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-client-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
```

### <a name="get-configuration-for-specific-component"></a>Configuratie voor specifieke component opvragen

Download de configuratie voor het onderdeel waarin u geïnteresseerd bent. Vervang in het `INITIAL` volgende voorbeeld de tagwaarde die is geretourneerd uit de vorige aanvraag.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

In dit voorbeeld wordt een JSON-document `livy2-conf` geretourneerd met de huidige configuratie voor de component.

### <a name="update-configuration"></a>Configuratie bijwerken

1. Maak `newconfig.json`.  
   Wijzig en voer de onderstaande opdrachten in:

   * Vervang `livy2-conf` door het nieuwe onderdeel.
   * Vervang `INITIAL` door de werkelijke `tag` waarde die is opgehaald voor [alle configuraties ophalen.](#get-all-configurations)

     **A. Bash**

     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     **B. PowerShell**  
     Het PowerShell-script maakt gebruik van [jq](https://stedolan.github.io/jq/).  Hieronder `C:\HD\jq\jq-win64` bewerken om uw werkelijke pad en versie van [jq](https://stedolan.github.io/jq/)weer te geven.

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     Jq wordt gebruikt om de gegevens die van HDInsight zijn opgehaald om te zetten in een nieuwe configuratiesjabloon. In het bijzonder doen deze voorbeelden de volgende acties:

   * Hiermee wordt een unieke waarde gemaakt die de tekenreeks "versie" en de datum bevat, die is opgeslagen in `newtag`.

   * Hiermee maakt u een hoofddocument voor de nieuwe configuratie.

   * Hiermee wordt de `.items[]` inhoud van de array opgeten en wordt deze toegevoegd onder het **desired_config** element.

   * Hiermee verwijdert `href` `version`u `Config` de , en elementen, omdat deze elementen niet nodig zijn om een nieuwe configuratie in te dienen.

   * Hiermee `tag` voegt u een `version#################`element toe met een waarde van . Het numerieke gedeelte is gebaseerd op de huidige datum. Elke configuratie moet een unieke tag hebben.

     Ten slotte worden de `newconfig.json` gegevens opgeslagen in het document. De documentstructuur moet lijken op het volgende voorbeeld:

     ```json
     {
       "Clusters": {
         "desired_config": {
           "tag": "version1552064778014",
           "type": "livy2-conf",
           "properties": {
             "livy.environment": "production",
             "livy.impersonation.enabled": "true",
             "livy.repl.enableHiveContext": "true",
             "livy.server.csrf_protection.enabled": "true",
               ....
           },
         },
       }
     }
     ```

2. Bewerken `newconfig.json`.  
   Open `newconfig.json` het document en wijzig/voeg waarden toe in het `properties` object. In het volgende voorbeeld `"livy.server.csrf_protection.enabled"` `"true"` wordt `"false"`de waarde van van .

        "livy.server.csrf_protection.enabled": "false",

    Sla het bestand op zodra u klaar bent met het aanbrengen van wijzigingen.

3. Dien in `newconfig.json`.  
   Gebruik de volgende opdrachten om de bijgewerkte configuratie naar Ambari te verzenden.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```  

    Deze opdrachten dienen de inhoud van het **bestand newconfig.json** in bij het cluster als de nieuwe configuratie. De aanvraag retourneert een JSON-document. Het **element versionTag** in dit document moet overeenkomen met de versie die u hebt ingediend en het **object configs** bevat de configuratiewijzigingen die u hebt aangevraagd.

### <a name="restart-a-service-component"></a>Een servicecomponent opnieuw starten

Op dit punt geeft de Ambari-webgebruikersinterface aan dat de Spark-service opnieuw moet worden gestart voordat de nieuwe configuratie van kracht kan worden. Gebruik de volgende stappen om de service opnieuw te starten.

1. Gebruik het volgende om de onderhoudsmodus voor de Spark2-service in te schakelen:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    ```

2. Onderhoudsmodus controleren  

    Met deze opdrachten wordt een JSON-document verzonden naar de server die de onderhoudsmodus inschakelt. U met de volgende aanvraag controleren of de service nu in de onderhoudsmodus staat:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```

    De retourwaarde `ON`is .

3. Gebruik vervolgens het volgende om de Spark2-service uit te schakelen:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
    $resp.Content
    ```

    Het antwoord is vergelijkbaar met het volgende voorbeeld:

    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```

    > [!IMPORTANT]  
    > De `href` waarde die door deze URI wordt geretourneerd, gebruikt het interne IP-adres van het clusterknooppunt. Als u het van buiten `10.0.0.18:8080` het cluster wilt gebruiken, vervangt u het gedeelte door de FQDN van het cluster.  

4. Verzoek verifiëren.  
    Bewerk de onderstaande `29` opdracht door te `id` vervangen door de werkelijke waarde voor geretourneerd uit de vorige stap.  Met de volgende opdrachten wordt de status van het verzoek opgehaald:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    Een antwoord `COMPLETED` van geeft aan dat de aanvraag is voltooid.

5. Zodra de vorige aanvraag is voltooid, gebruikt u het volgende om de Spark2-service te starten.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    $resp.Content
    ```

    De service maakt nu gebruik van de nieuwe configuratie.

6. Gebruik ten slotte de volgende om de onderhoudsmodus uit te schakelen.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'
    ```

## <a name="next-steps"></a>Volgende stappen

Zie [Apache Ambari API Reference V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)voor een volledige referentie van de REST API.  Zie [ook, Gebruikers autoriseren voor Apache Ambari-weergaven](./hdinsight-authorize-users-to-ambari.md)
