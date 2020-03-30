---
title: Apache Hadoop-clusters maken met Azure REST API - Azure
description: Meer informatie over het maken van HDInsight-clusters door Azure Resource Manager-sjablonen in te dienen bij de Azure REST API.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/10/2019
ms.openlocfilehash: 2680304bd73bdbae35b29b89f38ae2665615f5e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239923"
---
# <a name="create-apache-hadoop-clusters-using-the-azure-rest-api"></a>Apache Hadoop-clusters maken met behulp van de Azure REST API

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Meer informatie over het maken van een HDInsight-cluster met behulp van een Azure Resource Manager-sjabloon en de Azure REST API.

Met de Azure REST API u beheerbewerkingen uitvoeren op services die worden gehost in het Azure-platform, inclusief het maken van nieuwe bronnen zoals HDInsight-clusters.

> [!NOTE]  
> De stappen in dit document gebruiken de [krul (hulpprogrammahttps://curl.haxx.se/) ](https://curl.haxx.se/) om te communiceren met de Azure REST API.

## <a name="create-a-template"></a>Een sjabloon maken

Azure Resource Manager-sjablonen zijn JSON-documenten die een **resourcegroep** en alle bronnen daarin beschrijven (zoals HDInsight.) Met deze op sjablonen gebaseerde benadering u de resources definiëren die u nodig hebt voor HDInsight in één sjabloon.

Het volgende JSON-document is een fusie [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password)van de sjabloon- en parametersbestanden van , waardoor een op Linux gebaseerd cluster wordt gemaakt met behulp van een wachtwoord om het SSH-gebruikersaccount te beveiligen.

   ```json
   {
       "properties": {
           "template": {
               "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
               "contentVersion": "1.0.0.0",
               "parameters": {
                   "clusterType": {
                       "type": "string",
                       "allowedValues": ["hadoop",
                       "hbase",
                       "storm",
                       "spark"],
                       "metadata": {
                           "description": "The type of the HDInsight cluster to create."
                       }
                   },
                   "clusterName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the HDInsight cluster to create."
                       }
                   },
                   "clusterLoginUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                       }
                   },
                   "clusterLoginPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "sshUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to remotely access the cluster."
                       }
                   },
                   "sshPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "clusterStorageAccountName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the storage account to be created and be used as the cluster's storage."
                       }
                   },
                   "clusterWorkerNodeCount": {
                       "type": "int",
                       "defaultValue": 4,
                       "metadata": {
                           "description": "The number of nodes in the HDInsight cluster."
                       }
                   }
               },
               "variables": {
                   "defaultApiVersion": "2015-05-01-preview",
                   "clusterApiVersion": "2015-03-01-preview"
               },
               "resources": [{
                   "name": "[parameters('clusterStorageAccountName')]",
                   "type": "Microsoft.Storage/storageAccounts",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('defaultApiVersion')]",
                   "dependsOn": [],
                   "tags": {

                   },
                   "properties": {
                       "accountType": "Standard_LRS"
                   }
               },
               {
                   "name": "[parameters('clusterName')]",
                   "type": "Microsoft.HDInsight/clusters",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('clusterApiVersion')]",
                   "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                   "tags": {

                   },
                   "properties": {
                       "clusterVersion": "3.6",
                       "osType": "Linux",
                       "clusterDefinition": {
                           "kind": "[parameters('clusterType')]",
                           "configurations": {
                               "gateway": {
                                   "restAuthCredential.isEnabled": true,
                                   "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                   "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                               }
                           }
                       },
                       "storageProfile": {
                           "storageaccounts": [{
                               "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                               "isDefault": true,
                               "container": "[parameters('clusterName')]",
                               "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                           }]
                       },
                       "computeProfile": {
                           "roles": [{
                               "name": "headnode",
                               "targetInstanceCount": "2",
                               "hardwareProfile": {
                                   "vmSize": "{}" 
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           },
                           {
                               "name": "workernode",
                               "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                               "hardwareProfile": {
                                   "vmSize": "{}"
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           }]
                       }
                   }
               }],
               "outputs": {
                   "cluster": {
                       "type": "object",
                       "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                   }
               }
           },
           "mode": "incremental",
           "Parameters": {
               "clusterName": {
                   "value": "newclustername"
               },
               "clusterType": {
                   "value": "hadoop"
               },
               "clusterStorageAccountName": {
                   "value": "newstoragename"
               },
               "clusterLoginUserName": {
                   "value": "admin"
               },
               "clusterLoginPassword": {
                   "value": "changeme"
               },
               "sshUserName": {
                   "value": "sshuser"
               },
               "sshPassword": {
                   "value": "changeme"
               }
           }
       }
   }
   ```

Dit voorbeeld wordt gebruikt in de stappen in dit document. Vervang de *voorbeeldwaarden* in de sectie **Parameters** door de waarden voor uw cluster.

> [!IMPORTANT]  
> De sjabloon gebruikt het standaardaantal werknemersknooppunten (4) voor een HDInsight-cluster. Als u van plan bent op meer dan 32 werkknooppunten, moet u een hoofdknooppuntgrootte selecteren met ten minste 8 cores en 14 GB ram.
>
> Zie [Prijsdetails voor Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) voor meer informatie over knooppuntgrootten en de bijbehorende kosten.

## <a name="sign-in-to-your-azure-subscription"></a>Aanmelden bij uw Azure-abonnement

Volg de stappen die zijn gedocumenteerd in [Aan de slag met Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) en maak verbinding met uw abonnement met de `az login` opdracht.

## <a name="create-a-service-principal"></a>Een service-principal maken

> [!NOTE]  
> Deze stappen zijn een verkorte versie van de principal van de *service maken met het wachtwoordgedeelte* van het Azure CLI gebruiken om een [serviceprincipal te maken om toegang te krijgen tot het](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md) document resources. Met deze stappen wordt een serviceprincipal gemaakt die wordt gebruikt om te verifiëren voor de Azure REST API.

1. Gebruik vanuit een opdrachtregel de volgende opdracht om uw Azure-abonnementen weer te geven.

   ```azurecli
   az account list --query '[].{Subscription_ID:id,Tenant_ID:tenantId,Name:name}'  --output table
   ```

    Selecteer in de lijst het abonnement dat u wilt gebruiken en noteer de **Subscription_ID** en __Tenant_ID__ kolommen. Sla deze waarden op.

2. Gebruik de volgende opdracht om een toepassing te maken in Azure Active Directory.

   ```azurecli
   az ad app create --display-name "exampleapp" --homepage "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your password> --query 'appId'
   ```

    Vervang de waarden `--display-name` `--homepage`voor `--identifier-uris` de , en door uw eigen waarden. Geef een wachtwoord op voor het nieuwe Active Directory-item.

   > [!NOTE]  
   > De `--home-page` `--identifier-uris` waarden en waarden hoeven niet te verwijzen naar een echte webpagina gehost op het internet. Het moeten unieke URI's zijn.

   De waarde die wordt geretourneerd uit deze opdracht is de __app-id__ voor de nieuwe toepassing. Bespaar deze waarde.

3. Gebruik de volgende opdracht om een serviceprincipal te maken met de **App-id**.

   ```azurecli
   az ad sp create --id <App ID> --query 'objectId'
   ```

     De waarde die wordt geretourneerd uit deze opdracht is de __object-id__. Bespaar deze waarde.

4. Wijs de rol **Eigenaar** toe aan de serviceprincipal met de waarde **Object-id.** Gebruik de **abonnement-ID** die u eerder hebt verkregen.

   ```azurecli
   az role assignment create --assignee <Object ID> --role Owner --scope /subscriptions/<Subscription ID>/
   ```

## <a name="get-an-authentication-token"></a>Een verificatietoken opgebruiken

Gebruik de volgende opdracht om een verificatietoken op te halen:

```bash
curl -X "POST" "https://login.microsoftonline.com/$TENANTID/oauth2/token" \
-H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=$APPID" \
--data-urlencode "grant_type=client_credentials" \
--data-urlencode "client_secret=$PASSWORD" \
--data-urlencode "resource=https://management.azure.com/"
```

Instellen `$TENANTID` `$APPID`, `$PASSWORD` en op de waarden die eerder zijn verkregen of gebruikt.

Als dit verzoek succesvol is, ontvangt u een antwoord van 200-serie en bevat de antwoordinstantie een JSON-document.

Het JSON-document dat door deze aanvraag wordt geretourneerd, bevat een element met de naam **access_token**. De waarde van **access_token** wordt gebruikt voor verificatieaanvragen voor de REST-API.

```json
{
    "token_type":"Bearer",
    "expires_in":"3599",
    "expires_on":"1463409994",
    "not_before":"1463406094",
    "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
}
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Gebruik het volgende om een resourcegroep te maken.

* Stel `$SUBSCRIPTIONID` in op de abonnements-id die u hebt ontvangen tijdens het maken van de serviceprincipal.
* Stel `$ACCESSTOKEN` in op het toegangstoken dat in de vorige stap is ontvangen.
* Vervang `DATACENTERLOCATION` door het datacenter waarin u de resourcegroep en resources wilt maken. Bijvoorbeeld 'South Central US'.
* Stel `$RESOURCEGROUPNAME` in op de naam die u voor deze groep wilt gebruiken:

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME?api-version=2015-01-01" \
    -H "Authorization: Bearer $ACCESSTOKEN" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DATACENTERLOCATION"
}'
```

Als dit verzoek succesvol is, ontvangt u een antwoord van 200 reeksen en bevat de antwoordinstantie een JSON-document met informatie over de groep. Het `"provisioningState"` element bevat `"Succeeded"`een waarde van .

## <a name="create-a-deployment"></a>Een implementatie maken

Gebruik de volgende opdracht om de sjabloon te implementeren in de resourcegroep.

* Stel `$DEPLOYMENTNAME` in op de naam die u voor deze implementatie wilt gebruiken.

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [!NOTE]  
> Als u de sjabloon in een bestand hebt opgeslagen, u de volgende opdracht gebruiken in plaats van: `-d "{ template and parameters}"`
>
> `--data-binary "@/path/to/file.json"`

Als deze aanvraag is geslaagd, ontvangt u een antwoord van 200 reeksen en bevat de antwoordgroep een JSON-document met informatie over de implementatiebewerking.

> [!IMPORTANT]  
> De implementatie is ingediend, maar is nog niet voltooid. Het kan enkele minuten duren, meestal rond 15, voor de implementatie te voltooien.

## <a name="check-the-status-of-a-deployment"></a>De status van een implementatie controleren

Als u de status van de implementatie wilt controleren, gebruikt u de volgende opdracht:

```bash
curl -X "GET" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json"
```

Met deze opdracht wordt een JSON-document geretourneerd met informatie over de implementatiebewerking. Het `"provisioningState"` element bevat de status van de implementatie. Als dit element een `"Succeeded"`waarde van , bevat, is de implementatie voltooid.

## <a name="troubleshoot"></a>Problemen oplossen

Zie [Vereisten voor toegangsbeheer](./hdinsight-hadoop-customize-cluster-linux.md#access-control) als u problemen ondervindt met het maken van HDInsight-clusters.

## <a name="next-steps"></a>Volgende stappen

Nu u een HDInsight-cluster hebt gemaakt, gebruikt u het volgende om te leren hoe u met uw cluster werken.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop clusters

* [Apache Hive gebruiken met HDInsight](hadoop/hdinsight-use-hive.md)
* [MapReduce gebruiken met HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase clusters

* [Aan de slag met Apache HBase op HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Java-toepassingen ontwikkelen voor Apache HBase op HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm clusters

* [Ontwikkel Java-topologieën voor Apache Storm op HDInsight](storm/apache-storm-develop-java-topology.md)
* [Python-componenten gebruiken in Apache Storm op HDInsight](storm/apache-storm-develop-python-topology.md)
* [Topologieën implementeren en monitoren met Apache Storm op HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
