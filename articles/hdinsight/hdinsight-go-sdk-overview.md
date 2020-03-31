---
title: Azure HDInsight SDK voor onderweg
description: Referentiemateriaal voor het gebruik van Azure HDInsight SDK for Go- en Apache Hadoop-clusters
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 01/03/2020
ms.openlocfilehash: 292496c4d458621213fe62105149ac845d78891e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479583"
---
# <a name="hdinsight-sdk-for-go-preview"></a>HDInsight SDK for Go (Preview)

## <a name="overview"></a>Overzicht
De HDInsight SDK for Go biedt klassen en functies waarmee u uw HDInsight-clusters beheren. Het bevat bewerkingen voor het maken, verwijderen, bijwerken, aanbieden, vergroten, scriptacties uitvoeren, monitoren, eigenschappen van HDInsight-clusters opdoen en meer.

> [!NOTE]  
>GoDoc referentiemateriaal voor deze SDK is ook [hier beschikbaar.](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight)

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een [ `go get` hulpmiddel.](https://github.com/golang/go/wiki/GoGetTools)
* [Ga.](https://golang.org/dl/)

## <a name="sdk-installation"></a>SDK-installatie

Uitvoeren vanaf uw GOPATH-locatie`go get github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight`

## <a name="authentication"></a>Authentication

De SDK moet eerst worden geverifieerd met uw Azure-abonnement.  Volg het onderstaande voorbeeld om een serviceprincipal te maken en deze te gebruiken om te verifiëren. Nadat dit is gedaan, hebt u `ClustersClient`een instantie van een , die veel functies bevat (beschreven in onderstaande secties) die kunnen worden gebruikt om beheerbewerkingen uit te voeren.

> [!NOTE]  
> Er zijn andere manieren om te verifiëren naast het onderstaande voorbeeld dat mogelijk beter geschikt is voor uw behoeften. Alle functies worden hier beschreven: [verificatiefuncties in de Azure SDK for Go](https://docs.microsoft.com/azure/go/azure-sdk-go-authorization)

### <a name="authentication-example-using-a-service-principal"></a>Voorbeeld van verificatie met behulp van een serviceprincipal

Log eerst in bij [Azure Cloud Shell](https://shell.azure.com/bash). Controleer of u momenteel het abonnement gebruikt waarin u de serviceprincipal wilt maken.

```azurecli-interactive
az account show
```

Uw abonnementsgegevens worden weergegeven als JSON.

```json
{
  "environmentName": "AzureCloud",
  "id": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "isDefault": true,
  "name": "XXXXXXX",
  "state": "Enabled",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "user": {
    "cloudShellID": true,
    "name": "XXX@XXX.XXX",
    "type": "user"
  }
}
```

Als u niet bent aangemeld bij het juiste abonnement, selecteert u het juiste abonnement door het juiste abonnement uit te voeren: 
```azurecli-interactive
az account set -s <name or ID of subscription>
```

> [!IMPORTANT]  
> Als u de HDInsight Resource Provider nog niet hebt geregistreerd via een andere functie (bijvoorbeeld door een HDInsight-cluster te maken via de Azure-portal), moet u dit één keer doen voordat u verifiëren. Dit kan vanuit de [Azure Cloud Shell](https://shell.azure.com/bash) door de volgende opdracht uit te voeren:
>```azurecli-interactive
>az provider register --namespace Microsoft.HDInsight
>```

Kies vervolgens een naam voor uw serviceprincipal en maak deze met de volgende opdracht:

```azurecli-interactive
az ad sp create-for-rbac --name <Service Principal Name> --sdk-auth
```

De hoofdinformatie van de service wordt weergegeven als JSON.

```json
{
  "clientId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "clientSecret": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "subscriptionId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Kopieer het onderstaande `TENANT_ID`fragment `CLIENT_ID` `CLIENT_SECRET`en `SUBSCRIPTION_ID` vul in , en met de tekenreeksen van de JSON die is geretourneerd nadat de opdracht is uitgevoerd om de serviceprincipal te maken.

```golang
package main

import (
    "context"
    "github.com/Azure/go-autorest/autorest/azure/auth"
    hdi "github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight"
    "github.com/Azure/go-autorest/autorest/to"
)

func main() {
    var err error

    // Tenant ID for your Azure Subscription
    var TENANT_ID = ""
    // Your Service Principal App Client ID
    var CLIENT_ID = ""
    // Your Service Principal Client Secret
    var CLIENT_SECRET = ""
    // Azure Subscription ID
    var SUBSCRIPTION_ID = ""

    var credentials = auth.NewClientCredentialsConfig(CLIENT_ID, CLIENT_SECRET, TENANT_ID)
    var client = hdi.NewClustersClient(SUBSCRIPTION_ID)

    client.Authorizer, err = credentials.Authorizer()
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
```

## <a name="cluster-management"></a>Clusterbeheer

> [!NOTE]  
> In deze sectie wordt ervan uitgegaan dat `ClusterClient` u een instantie al `client`hebt geverifieerd en gebouwd en deze hebt opgeslagen in een variabele die wordt genoemd . Instructies voor het verifiëren en `ClusterClient` verkrijgen van een u vinden in de sectie Verificatie hierboven.

### <a name="create-a-cluster"></a>Een cluster maken

Een nieuw cluster kan `client.Create()`worden gemaakt door aan te roepen . 

#### <a name="example"></a>Voorbeeld

In dit voorbeeld wordt uitgelegd hoe u een [Apache Spark-cluster](https://spark.apache.org/) maakt met twee hoofdknooppunten en één werkknooppunt.

> [!NOTE]  
> U moet eerst een resourcegroep en opslagaccount maken, zoals hieronder wordt uitgelegd. Als u deze al hebt gemaakt, u deze stappen overslaan.

##### <a name="creating-a-resource-group"></a>Een resourcegroep maken

U een resourcegroep maken met de [Azure Cloud Shell](https://shell.azure.com/bash) door

```azurecli-interactive
az group create -l <Region Name (i.e. eastus)> --n <Resource Group Name>
```

##### <a name="creating-a-storage-account"></a>Een opslagaccount maken

U een opslagaccount maken met de [Azure Cloud Shell](https://shell.azure.com/bash) door het uitvoeren van:

```azurecli-interactive
az storage account create -n <Storage Account Name> -g <Existing Resource Group Name> -l <Region Name (i.e. eastus)> --sku <SKU i.e. Standard_LRS>
```

Voer nu de volgende opdracht uit om de sleutel voor uw opslagaccount op te halen (u hebt dit nodig om een cluster te maken):

```azurecli-interactive
az storage account keys list -n <Storage Account Name>
```

---
In het onderstaande Fragment Go wordt een Spark-cluster gemaakt met twee hoofdknooppunten en één werkknooppunt. Vul de lege variabelen in zoals uitgelegd in de opmerkingen en voel je vrij om andere parameters te wijzigen om aan uw specifieke behoeften te voldoen.

```golang
// The name for the cluster you are creating
var clusterName = "";
// The name of your existing Resource Group
var resourceGroupName = "";
// Choose a username
var username = "";
// Choose a password
var password = "";
// Replace <> with the name of your storage account
var storageAccount = "<>.blob.core.windows.net";
// Storage account key you obtained above
var storageAccountKey = "";
// Choose a region
var location = "";
var container = "default";

var parameters = hdi.ClusterCreateParametersExtended {
    Location: to.StringPtr(location),
    Tags: make(map[string]*string),
    Properties: &hdi.ClusterCreateProperties {
        ClusterVersion: to.StringPtr("3.6"),
        OsType: hdi.Linux,
        ClusterDefinition: &hdi.ClusterDefinition {
            Kind: to.StringPtr("spark"),
            Configurations: map[string]map[string]interface{}{
                "gateway": {
                    "restAuthCredential.isEnabled": "True",
                    "restAuthCredential.username":  username,
                    "restAuthCredential.password":  password,
                },
            },
        },
        Tier: hdi.Standard,
        ComputeProfile: &hdi.ComputeProfile {
            Roles: &[]hdi.Role {
                hdi.Role {
                    Name: to.StringPtr("headnode"),
                    TargetInstanceCount: to.Int32Ptr(2),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
                hdi.Role {
                    Name: to.StringPtr("workernode"),
                    TargetInstanceCount: to.Int32Ptr(1),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
            },
        },
        StorageProfile: &hdi.StorageProfile {
            Storageaccounts: &[]hdi.StorageAccount {
                hdi.StorageAccount {
                    Name: to.StringPtr(storageAccount),
                    Key: to.StringPtr(storageAccountKey),
                    Container: to.StringPtr(container),
                    IsDefault: to.BoolPtr(true),
                },
            },
        },
    },
}
client.Create(context.Background(), resourceGroupName, clusterName, parameters)
```

### <a name="get-cluster-details"></a>Clustergegevens opvragen

Ga als lid van het nieuwe cluster naar eigenschappen voor een bepaald cluster:

```golang
client.Get(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Voorbeeld

U kunt `get` ervoor zorgen dat u uw cluster hebt gemaakt.

```golang
cluster, err := client.Get(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
fmt.Println(*cluster.Name)
fmt.Println(*cluster.ID
```

De uitvoer moet eruit zien als:

```
<Cluster Name>
/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<Resource Group Name>/providers/Microsoft.HDInsight/clusters/<Cluster Name>
```

### <a name="list-clusters"></a>Lijstclusters

#### <a name="list-clusters-under-the-subscription"></a>Clusters onder het abonnement aanbieden

```golang
client.List()
```

#### <a name="list-clusters-by-resource-group"></a>Clusters op resourcegroep weergeven

```golang
client.ListByResourceGroup("<Resource Group Name>")
```

> [!NOTE]  
> Beide `List()` `ListByResourceGroup()` en `ClusterListResultPage` terug een struct. Om de volgende pagina te `Next()`krijgen, u bellen. Dit kan worden `ClusterListResultPage.NotDone()` `false`herhaald tot het rendement, zoals weergegeven in het onderstaande voorbeeld.

#### <a name="example"></a>Voorbeeld

In het volgende voorbeeld worden de eigenschappen van alle clusters voor het huidige abonnement afgedrukt:

```golang
page, err := client.List(context.Background())
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, cluster := range page.Values() {
        fmt.Println(*cluster.Name)
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="delete-a-cluster"></a>Een cluster verwijderen

Een cluster verwijderen:

```golang
client.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

### <a name="update-cluster-tags"></a>Clustertags bijwerken

U de tags van een bepaald cluster zo bijwerken:

```golang
client.Update(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterPatchParameters{<map[string]*string} of Tags>)
```

#### <a name="example"></a>Voorbeeld

```golang
client.Update(context.Background(), "SDKTestRG", "SDKTest", hdi.ClusterPatchParameters{map[string]*string{"tag1Name" : to.StringPtr("tag1Value"), "tag2Name" : to.StringPtr("tag2Value")}})
```

### <a name="resize-cluster"></a>Formaat van het formaat wijzigen

U het formaat van het aantal werknemersknooppunten van een bepaald cluster wijzigen door een nieuwe grootte op te geven:

```golang
client.Resize(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterResizeParameters{<Num of Worker Nodes (int)>})
```

## <a name="cluster-monitoring"></a>Clusterbewaking

De HDInsight Management SDK kan ook worden gebruikt om de monitoring op uw clusters te beheren via de Operations Management Suite (OMS).

Net als bij `ClusterClient` de manier waarop u hebt gemaakt `ExtensionClient` om te gebruiken voor beheerbewerkingen, moet u een te gebruiken voor bewakingsbewerkingen maken. Zodra u de bovenstaande sectie Verificatie hebt `ExtensionClient` voltooid, u een vind-ik-leukmaken:

```golang
extClient := hdi.NewExtensionsClient(SUBSCRIPTION_ID)
extClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> De onderstaande monitoring voorbeelden gaan ervan `ExtensionClient` `extClient` uit dat `Authorizer` u al een aangeroepen geparafeerd en zijn zoals hierboven weergegeven.

### <a name="enable-oms-monitoring"></a>OMS-bewaking inschakelen

> [!NOTE]  
> Als u OMS-controle wilt inschakelen, moet u over een bestaande Log Analytics-werkruimte beschikken. Als u er nog geen hebt gemaakt, u hier leren hoe u dat doen: [Een werkruimte logboekanalyse maken in de Azure-portal.](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace)

Ga als volgt te werk om OMS-controle op uw cluster in te schakelen:

```golang
extClient.EnableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name", hdi.ClusterMonitoringRequest {WorkspaceID: to.StringPtr("<Workspace Id>")})
```

### <a name="view-status-of-oms-monitoring"></a>Status van OMS-monitoring weergeven

Ga als een te meer metde overdeslag voor DE S.A.S.

```golang
extClient.GetMonitoringStatus(context.Background(), "<Resource Group Name", "Cluster Name")
```

### <a name="disable-oms-monitoring"></a>OMS-bewaking uitschakelen

Ga als een schakelnaar met EEN OMS in uw cluster:

```golang
extClient.DisableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name")
```

## <a name="script-actions"></a>Scriptacties

HDInsight biedt een configuratiefunctie genaamd scriptacties die aangepaste scripts aanroepen om het cluster aan te passen.

> [!NOTE]  
> Meer informatie over het gebruik van scriptacties vindt u hier: [Linux-gebaseerde HDInsight-clusters aanpassen met behulp van scriptacties](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)

### <a name="execute-script-actions"></a>Scriptacties uitvoeren

U scriptacties uitvoeren op een bepaald cluster, zoals:

```golang
var scriptAction1 = hdi.RuntimeScriptAction{Name: to.StringPtr("<Script Name>"), URI: to.StringPtr("<URL To Script>"), Roles: <&[]string of roles>} //valid roles are "headnode", "workernode", "zookeepernode", and "edgenode"
client.ExecuteScriptActions(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ExecuteScriptActionParameters{PersistOnSuccess: to.BoolPtr(true), ScriptActions: &[]hdi.RuntimeScriptAction{scriptAction1}}) //add more RuntimeScriptActions to the list to execute multiple scripts
```

Voor de bewerkingen 'Scriptactie verwijderen' en 'Lijst blijvende `ScriptActionsClient`scriptacties' moet u `ClusterClient` een , vergelijkbaar met de manier waarop u hebt gemaakt om te gebruiken voor beheerbewerkingen maken. Zodra u de bovenstaande sectie Verificatie hebt `ScriptActionsClient` voltooid, u een dergelijke maken:

```golang
scriptActionsClient := hdi.NewScriptActionsClient(SUBSCRIPTION_ID)
scriptActionsClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> De onderstaande scriptacties voorbeelden aannemen dat `ScriptActionsClient` `scriptActionsClient` u al `Authorizer` een aangeroepen hebt geinitialiseerd en zijn hebt ingesteld zoals hierboven weergegeven.

### <a name="delete-script-action"></a>Scriptactie verwijderen

Ga als lid van het nieuwe script als een uitgevoerd script actie op een bepaald cluster:

```golang
scriptActionsClient.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>", "<Script Name>")
```

### <a name="list-persisted-script-actions"></a>Doorgaande scriptacties weerhouden

> [!NOTE]  
> Beide `ListByCluster()` keren `ScriptActionsListPage` terug een struct. Om de volgende pagina te `Next()`krijgen, u bellen. Dit kan worden `ClusterListResultPage.NotDone()` `false`herhaald tot het rendement, zoals weergegeven in het onderstaande voorbeeld.

Ga als lid van het nieuwe cluster naar een lijst met alle aanhoudende scriptacties voor het opgegeven cluster:
```golang
scriptActionsClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Voorbeeld

```golang
page, err := scriptActionsClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="list-all-scripts-execution-history"></a>De uitvoeringsgeschiedenis van alle scripts weergeven

Voor deze bewerking moet u `ScriptExecutionHistoryClient`een , vergelijkbaar `ClusterClient` met hoe u hebt gemaakt om te gebruiken voor beheerbewerkingen maken. Zodra u de bovenstaande sectie Verificatie hebt `ScriptActionsClient` voltooid, u een dergelijke maken:

```golang
scriptExecutionHistoryClient := hdi.NewScriptExecutionHistoryClient(SUBSCRIPTION_ID)
scriptExecutionHistoryClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> De onderstaande gaat ervan uit `ScriptExecutionHistoryClient` `scriptExecutionHistoryClient` dat u `Authorizer` al een aangeroepen hebt geparafeerd en zijn zoals hierboven weergegeven hebt ingesteld.

Ga als bedoeld als u de uitvoeringsgeschiedenis van alle scripts voor het opgegeven cluster wilt weergeven:

```golang
scriptExecutionHistoryClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Voorbeeld

In dit voorbeeld worden alle details voor alle eerdere scriptuitvoeringen afgedrukt.

```golang
page, err := scriptExecutionHistoryClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

## <a name="next-steps"></a>Volgende stappen

Ontdek het [GoDoc-referentiemateriaal](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight). De GoDocs bieden referentiedocumentatie voor alle functies in de SDK.
