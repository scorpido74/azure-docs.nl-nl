---
title: Azure Kubernetes gebruiken met Azure Cosmos DB
description: Meer informatie over het opstarten van een Kubernetes-cluster op Azure met Azure Cosmos DB (preview)
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: sngun
ms.openlocfilehash: 9dbbc914580d8d80a3f9b7d730574e24b44827c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70093733"
---
# <a name="how-to-use-azure-kubernetes-with-azure-cosmos-db-preview"></a>Azure Kubernetes gebruiken met Azure Cosmos DB (preview)

Met de geëetcd-API in Azure Cosmos DB u Azure Cosmos DB gebruiken als backendstore voor Azure Kubernetes. Azure Cosmos DB implementeert het etcd-draadprotocol, waarmee de API-servers van het hoofdknooppunt Azure Cosmos DB kunnen gebruiken, net zoals het toegang zou krijgen tot een lokaal geïnstalleerde etcd. etcd API in Azure Cosmos DB is momenteel in preview. Wanneer u Azure Cosmos etcd API gebruikt als back-upvoor Kubernetes, krijgt u de volgende voordelen: 

* Het is niet nodig om etcd handmatig te configureren en te beheren.
* Hoge beschikbaarheid van etcd, gegarandeerd door Cosmos (99,99% in één regio, 99,999% in meerdere regio's).
* Elastische schaalbaarheid van etcd.
* Beveilig standaard & enterprise ready.
* Toonaangevende, uitgebreide SLA's.

Zie het [overzichtsartikel](etcd-api-introduction.md) voor meer informatie over de ETCD API in Azure Cosmos DB. In dit artikel ziet u hoe u [Azure Kubernetes Engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md) (aks-engine) gebruiken om een Kubernetes-cluster op Azure op te start zetten dat Azure Cosmos [DB](https://docs.microsoft.com/azure/cosmos-db/) gebruikt in plaats van een lokaal geïnstalleerde en geconfigureerde geë-erop. 

## <a name="prerequisites"></a>Vereisten

1. Installeer de nieuwste versie van [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). U Azure CLI specifiek voor uw besturingssysteem downloaden en installeren.

1. Installeer de [nieuwste versie](https://github.com/Azure/aks-engine/releases) van Azure Kubernetes Engine. De installatie-instructies voor verschillende besturingssystemen zijn beschikbaar op de azure [Kubernetes Engine-pagina.](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install-aks-engine) U hoeft alleen maar de stappen van **installeren AKS Engine** sectie van de gekoppelde doc. Haal na het downloaden het zip-bestand eruit.

   De Azure Kubernetes Engine **(aks-engine)** genereert Azure Resource Manager-sjablonen voor Kubernetes-clusters op Azure. De invoer naar aks-engine is een clusterdefinitiebestand dat het gewenste cluster beschrijft, inclusief orchestrator, functies en agents. De structuur van de invoerbestanden is vergelijkbaar met de openbare API voor Azure Kubernetes Service.

1. De geëetcd-API in Azure Cosmos DB is momenteel in preview. Meld u aan om de https://aka.ms/cosmosetcdapi-signuppreview-versie te gebruiken op: . Nadat u het formulier hebt ingediend, wordt uw abonnement op de witte lijst gewhitelistom de Azure Cosmos etcd API te gebruiken. 

## <a name="deploy-the-cluster-with-azure-cosmos-db"></a>Het cluster implementeren met Azure Cosmos DB

1. Open een opdrachtpromptvenster en meld u aan bij Azure met de volgende opdracht:

   ```azurecli-interactive
   az login 
   ```

1. Als u meer dan één abonnement hebt, schakelt u over naar het abonnement dat op de witte lijst is gezet voor Azure Cosmos DB etcd API. U met de volgende opdracht overschakelen naar het vereiste abonnement:

   ```azurecli-interactive
   az account set --subscription "<Name of your subscription>"
   ```
1. Maak vervolgens een nieuwe brongroep waarin u de resources implementeert die vereist zijn voor het Azure Kubernetes-cluster. Zorg ervoor dat u de resourcegroep in de regio 'centralus' maakt. Het is niet verplicht voor de resourcegroep om zich in de regio 'centralus' te bevinden, maar Azure Cosmos etcd API is momenteel alleen beschikbaar om te implementeren in de regio 'centralus'. Dus het is het beste om de Kubernetes cluster te worden samenmet de Cosmos etcd instantie:

   ```azurecli-interactive
   az group create --name <Name> --location "centralus"
   ```

1. Maak vervolgens een serviceprincipal voor het Azure Kubernetes-cluster, zodat het kan communiceren met de bronnen die deel uitmaken van dezelfde brongroep. U een serviceprincipal maken met Azure CLI, PowerShell of Azure-portal, in dit voorbeeld wilt u CLI maken.

   ```azurecli-interactive
   az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<Your_Azure_subscription_ID>/resourceGroups/<Your_resource_group_name>"
   ```
   Met deze opdracht worden de details van een serviceprincipal uitgevoerd, bijvoorbeeld:
   
   ```cmd
   Retrying role assignment creation: 1/36
   {
     "appId": "8415a4e9-4f83-46ca-a704-107457b2e3ab",
     "displayName": "azure-cli-2019-04-19-19-01-46",
     "name": "http://azure-cli-2019-04-19-19-01-46",
     "password": "102aecd3-5e37-4f3d-8738-2ac348c2e6a7",
     "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
   }
   ```
   
   Noteer de **appId** en de **wachtwoordvelden,** omdat u deze parameters in de volgende stappen gebruikt. 

1. Navigeer vanuit de opdrachtprompt naar de map waar de uitvoerbare Azure Kubernetes Engine zich bevindt. Op de opdrachtprompt u bijvoorbeeld naar de map navigeren als:

   ```cmd
   cd "\aks-engine-v0.36.3-windows-amd64\aks-engine-v0.36.3-windows-amd64"
   ```

1. Open een teksteditor naar keuze en definieer een Resource Manager-sjabloon waarmee het Azure Kubernetes-cluster wordt geïmplementeerd met Azure Cosmos DB etcd API. Kopieer de volgende JSON-definitie naar uw `apiModel.json`teksteditor en sla het bestand op als:

   ```json

   {
    "apiVersion": "vlabs",
    "properties": {
        "orchestratorProfile": {
            "orchestratorType": "Kubernetes",
            "kubernetesConfig": {
                "useManagedIdentity": false
            }
        },
        "masterProfile": {
            "count": 1,
            "dnsPrefix": "",
            "vmSize": "Standard_D2_v3",
            "cosmosEtcd": true
        },
        "agentPoolProfiles": [
            {
                "name": "agent",
                "count": 1,
                "vmSize": "Standard_D2_v3",
                "availabilityProfile": "AvailabilitySet"
            }
        ],
        "linuxProfile": {
            "adminUsername": "azureuser",
            "ssh": {
                "publicKeys": [
                    {
                        "keyData": ""
                    }
                ]
            }
        }
    }
   }
   ```

   In het JSON/clusterdefinitiebestand is de belangrijkste parameter om op te merken **"cosmosEtcd": true**. Deze parameter bevindt zich in de eigenschappen "masterProfile" en geeft de implementatie aan om Azure Cosmos etcd API te gebruiken in plaats van gewone etcd. 

1. Implementeer het Azure Kubernetes-cluster dat Azure Cosmos DB gebruikt met de volgende opdracht:

   ```cmd
   aks-engine deploy \
     --subscription-id <Your_Azure_subscription_ID> \
     --client-id <Service_ principal_appId> \
     --client-secret <Service_ principal_password> \
     --dns-prefix <Region_unique_dns_name > \
     --location centralus \
     --resource-group <Resource_Group_Name> \
     --api-model <Fully_qualified_path_to_the_template_file>  \
     --force-overwrite
   ```

   Azure Kubernetes Engine gebruikt een clusterdefinitie die de gewenste vorm, grootte en configuratie van de Azure Kubernetes schetst. Er zijn verschillende functies die kunnen worden ingeschakeld via de clusterdefinitie. In dit voorbeeld gebruikt u de volgende parameters:

   * **abonnement-id:** Azure-abonnements-ID met Azure Cosmos DB etcd API ingeschakeld.
   * **client-id:** AppId van de serviceprincipal. Het `appId` werd teruggegeven als output in stap 4.
   * **Klantgeheim:** Het wachtwoord van de serviceprincipal of een willekeurig gegenereerd wachtwoord. Deze waarde is geretourneerd als uitvoer in de parameter 'wachtwoord' in stap 4. 
   * **dnsPrefix:** Een regio-unieke DNS-naam. Deze waarde maakt deel uit van de hostnaam (voorbeeldwaarden zijn- myprod1, staging).
   * **locatie:**  Locatie waar het cluster moet worden ingezet, momenteel wordt alleen "centralus" ondersteund.

   > [!Note]
   > Azure Cosmos etcd API is momenteel alleen beschikbaar voor implementatie in de regio 'centralus'. 
 
   * **api-model:** Volledig gekwalificeerd pad naar het sjabloonbestand.
   * **force-overwrite:** Deze optie wordt gebruikt om bestaande bestanden in de uitvoermap automatisch te overschrijven.
 
   In de volgende opdracht wordt een voorbeeldimplementatie weergegeven:

   ```cmd
   aks-engine deploy \
     --subscription-id 1234fc61-1234-1234-1234-be1234d12c1b \
     --client-id 1234a4e9-4f83-46ca-a704-107457b2e3ab \
     --client-secret 123aecd3-5e37-4f3d-8738-2ac348c2e6a7 \
     --dns-prefix aks-sg-test \
     --location centralus \
     --api-model "C:\Users\demouser\Downloads\apiModel.json"  \
     --force-overwrite
   ```

## <a name="verify-the-deployment"></a>De implementatie controleren

De implementatie van de sjabloon duurt enkele minuten. Nadat de implementatie is voltooid, ziet u de volgende uitvoer in de opdrachtprompt:

```cmd
WARN[0006] apimodel: missing masterProfile.dnsPrefix will use "aks-sg-test"
WARN[0006] --resource-group was not specified. Using the DNS prefix from the apimodel as the resource group name: aks-sg-test
INFO[0025] Starting ARM Deployment (aks-sg-test-546247491). This will take some time...
INFO[0587] Finished ARM Deployment (aks-sg-test-546247491). Succeeded
```

De brongroep bevat nu bronnen zoals virtuele machine, Azure Cosmos-account(etcd API), virtueel netwerk, beschikbaarheidsset en andere resources die vereist zijn door het Kubernetes-cluster. 

De naam van het Azure Cosmos-account komt overeen met het opgegeven DNS-voorvoegsel dat is toegevoegd aan k8's. Uw Azure Cosmos-account wordt automatisch ingericht met een database met de naam **EtcdDB** en een container met de naam **EtcdData.** De container slaat alle etcd gerelateerde gegevens op. De container is ingericht met een bepaald aantal aanvraageenheden en u de doorvoer op basis van uw werkbelasting [schalen (verhogen/verkleinen).](scaling-throughput.md) 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [werken met Azure Cosmos-database, containers en items](databases-containers-items.md)
* Meer informatie over het [optimaliseren van ingerichte doorvoerkosten](optimize-cost-throughput.md)

