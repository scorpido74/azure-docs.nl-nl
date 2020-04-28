---
title: Azure Kubernetes gebruiken met Azure Cosmos DB
description: Meer informatie over het Boots trapen van een Kubernetes-cluster op Azure dat gebruikmaakt van Azure Cosmos DB (preview-versie)
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: sngun
ms.openlocfilehash: 9dbbc914580d8d80a3f9b7d730574e24b44827c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "70093733"
---
# <a name="how-to-use-azure-kubernetes-with-azure-cosmos-db-preview"></a>Azure Kubernetes gebruiken met Azure Cosmos DB (preview-versie)

Met de etcd-API in Azure Cosmos DB kunt u Azure Cosmos DB gebruiken als back-end-Archief voor Azure Kubernetes. Azure Cosmos DB implementeert het etcd wire-protocol, waarmee de API-servers van het Master-knoop punt kunnen worden gebruikt Azure Cosmos DB net zoals een lokaal geïnstalleerd etcd. etcd-API in Azure Cosmos DB is momenteel beschikbaar als preview-versie. Wanneer u Azure Cosmos etcd API gebruikt als de back-upopslag voor Kubernetes, krijgt u de volgende voor delen: 

* U hoeft etcd niet hand matig te configureren en te beheren.
* Hoge Beschik baarheid van etcd, gegarandeerd door Cosmos (99,99% in één regio, 99,999% in meerdere regio's).
* Elastische schaal baarheid van etcd.
* Standaard beveiligd & Enter prise Ready.
* Toonaangevende, uitgebreide service overeenkomsten.

Zie het artikel [overzicht](etcd-api-introduction.md) voor meer informatie over de ETCD-API in azure Cosmos db. Dit artikel laat u zien hoe u met behulp van de [Azure Kubernetes-engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md) (AKS-Engine) een Kubernetes-cluster op Azure kunt Boots trapen dat gebruikmaakt van [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) in plaats van een lokaal geïnstalleerde en geconfigureerde etcd. 

## <a name="prerequisites"></a>Vereisten

1. Installeer de nieuwste versie van [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest). U kunt Azure CLI specifiek voor uw besturings systeem downloaden en installeren.

1. Installeer de [nieuwste versie](https://github.com/Azure/aks-engine/releases) van de Azure Kubernetes-engine. De installatie-instructies voor verschillende besturings systemen zijn beschikbaar op de pagina [Azure Kubernetes engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install-aks-engine) . U hebt alleen de stappen nodig van de sectie **AKS-Engine installeren** van het gekoppelde document. Pak het zip-bestand uit nadat het is gedownload.

   De Azure Kubernetes-Engine (**AKS-engine**) genereert Azure Resource Manager sjablonen voor Kubernetes-clusters in Azure. De invoer voor AKS-engine is een cluster definitie bestand waarin het gewenste cluster wordt beschreven, met inbegrip van orchestrator, functies en agents. De structuur van de invoer bestanden is vergelijkbaar met de open bare API voor de Azure Kubernetes-service.

1. De etcd-API in Azure Cosmos DB is momenteel beschikbaar als preview-versie. Meld u aan voor gebruik van de preview- https://aka.ms/cosmosetcdapi-signupversie op:. Nadat u het formulier hebt verzonden, wordt uw abonnement white list om de Azure Cosmos etcd-API te gebruiken. 

## <a name="deploy-the-cluster-with-azure-cosmos-db"></a>Het cluster implementeren met Azure Cosmos DB

1. Open een opdracht prompt venster en meld u aan bij Azure met de volgende opdracht:

   ```azurecli-interactive
   az login 
   ```

1. Als u meer dan één abonnement hebt, gaat u naar het abonnement dat is white list voor Azure Cosmos DB etcd-API. U kunt overschakelen naar het vereiste abonnement met behulp van de volgende opdracht:

   ```azurecli-interactive
   az account set --subscription "<Name of your subscription>"
   ```
1. Maak vervolgens een nieuwe resource groep waarin u de resources implementeert die vereist zijn voor het Azure Kubernetes-cluster. Zorg ervoor dat u de resource groep in de regio ' centraal ' hebt gemaakt. Het is niet verplicht om de resource groep in de regio ' centraal ' te hebben, maar Azure Cosmos etcd API is momenteel alleen beschikbaar voor implementatie in de regio ' centraal '. Het is dus het beste om het Kubernetes-cluster te laten staan met de Cosmos etcd-instantie:

   ```azurecli-interactive
   az group create --name <Name> --location "centralus"
   ```

1. Maak vervolgens een service-principal voor het Azure Kubernetes-cluster zodat deze kan communiceren met de resources die deel uitmaken van dezelfde resource groep. U kunt een service-principal maken met behulp van Azure CLI, Power shell of Azure Portal, in dit voor beeld u hebt CLI om deze te maken.

   ```azurecli-interactive
   az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<Your_Azure_subscription_ID>/resourceGroups/<Your_resource_group_name>"
   ```
   Met deze opdracht worden de details van een Service-Principal uitgevoerd, bijvoorbeeld:
   
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
   
   Noteer de velden **AppID** en **wacht woord** , omdat u deze para meters in de volgende stappen gaat gebruiken. 

1. Ga vanaf de opdracht prompt naar de map waarin het uitvoer bare bestand van de Azure Kubernetes-engine zich bevindt. U kunt bijvoorbeeld in de opdracht prompt naar de map navigeren als:

   ```cmd
   cd "\aks-engine-v0.36.3-windows-amd64\aks-engine-v0.36.3-windows-amd64"
   ```

1. Open een tekst editor naar keuze en definieer een resource manager-sjabloon die het Azure Kubernetes-cluster implementeert met Azure Cosmos DB etcd-API. Kopieer de volgende JSON-definitie naar uw tekst editor en sla het bestand `apiModel.json`op als:

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

   In het JSON/cluster-definitie bestand is de sleutel parameter **' cosmosEtcd ': True**. Deze para meter bevindt zich in de eigenschappen masterProfile en geeft de implementatie aan voor het gebruik van de Azure Cosmos etcd-API in plaats van normale etcd. 

1. Implementeer het Azure Kubernetes-cluster dat gebruikmaakt van Azure Cosmos DB met de volgende opdracht:

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

   De Azure Kubernetes-engine gebruikt een cluster definitie die de gewenste vorm, grootte en configuratie van de Azure-Kubernetes omlijnt. Er zijn verschillende functies die kunnen worden ingeschakeld via de cluster definitie. In dit voor beeld gebruikt u de volgende para meters:

   * **abonnement-id:** Azure-abonnements-ID waarop Azure Cosmos DB etcd-API is ingeschakeld.
   * **client-id:** De appId van de Service-Principal. De `appId` is geretourneerd als uitvoer in stap 4.
   * **Client-geheim:** Het wacht woord van de service-principal of een wille keurig gegenereerd wacht woord. Deze waarde is geretourneerd als uitvoer in de para meter ' wacht woord ' in stap 4. 
   * **dnsPrefix:** Een unieke DNS-naam voor een regio. Deze waarde maakt deel uit van de hostnaam (voorbeeld waarden zijn-myprod1, fase ring).
   * **Locatie:**  De locatie waar het cluster moet worden geïmplementeerd, momenteel alleen ' centraalus ' wordt ondersteund.

   > [!Note]
   > Azure Cosmos etcd API is momenteel alleen beschikbaar voor implementatie in de regio ' centraal '. 
 
   * **API-model:** Volledig gekwalificeerde pad naar het sjabloon bestand.
   * **geforceerd-overschrijven:** Deze optie wordt gebruikt voor het automatisch overschrijven van bestaande bestanden in de uitvoermap.
 
   Met de volgende opdracht wordt een voorbeeld implementatie weer gegeven:

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

Het duurt enkele minuten voordat de implementatie van de sjabloon is voltooid. Nadat de implementatie is voltooid, wordt de volgende uitvoer weer gegeven in de opdracht prompt:

```cmd
WARN[0006] apimodel: missing masterProfile.dnsPrefix will use "aks-sg-test"
WARN[0006] --resource-group was not specified. Using the DNS prefix from the apimodel as the resource group name: aks-sg-test
INFO[0025] Starting ARM Deployment (aks-sg-test-546247491). This will take some time...
INFO[0587] Finished ARM Deployment (aks-sg-test-546247491). Succeeded
```

De resource groep bevat nu resources zoals virtuele machine, Azure Cosmos-account (etcd-API), virtueel netwerk, beschikbaarheidsset en andere resources die vereist zijn voor het Kubernetes-cluster. 

De naam van het Azure Cosmos-account komt overeen met het opgegeven DNS-voor voegsel dat is toegevoegd met K8S. Uw Azure Cosmos-account wordt automatisch ingericht met een Data Base met de naam **EtcdDB** en een container met de naam **EtcdData**. In de container worden alle etcd-gerelateerde gegevens opgeslagen. De container is ingericht met een bepaald aantal aanvraag eenheden en u kunt [de door Voer schalen (verg Roten/verkleinen)](scaling-throughput.md) op basis van uw werk belasting. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [werken met Azure Cosmos data base, containers en items](databases-containers-items.md)
* Meer informatie over het [optimaliseren van ingerichte doorvoer kosten](optimize-cost-throughput.md)

