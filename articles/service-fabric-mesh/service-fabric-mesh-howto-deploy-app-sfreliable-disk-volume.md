---
title: Service fabric betrouwbaar schijfvolume met servicefabric mesh
description: Lees hoe u de status in een Azure Service Fabric-mesh-toepassing opslaan door het op servicestructuur gebaseerde volume op basis van de schijf in de container te monteren met behulp van de Azure CLI.
author: ashishnegi
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: asnegi
ms.custom: mvc, devcenter
ms.openlocfilehash: f26fe70afe7d9e2872f06ac6da7143556278b1b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75497970"
---
# <a name="mount-highly-available-service-fabric-reliable-disk-based-volume-in-a-service-fabric-mesh-application"></a>Monteer een hoogst beschikbaar servicefabric betrouwbaar schijfgebaseerd volume in een Service Fabric Mesh-toepassing 
De gebruikelijke methode van blijvende status met container-apps is het gebruik van externe opslag zoals Azure File Storage of database zoals Azure Cosmos DB. Hierdoor wordt een aanzienlijke lees- en schrijfnetwerklatentie naar de externe winkel verbonden.

In dit artikel ziet u hoe u de status opslaan in een zeer beschikbare servicefabric betrouwbare schijf door een volume in de container van een Service Fabric Mesh-toepassing te monteren.
Service Fabric Reliable Disk biedt volumes voor lokale reads met schrijfbewerkingen die zijn gerepliceerd binnen het Cluster Servicefabric voor hoge beschikbaarheid. Hiermee worden netwerkoproepen voor lees- en netwerklatentie voor schrijfbewerkingen verwijderd. Als de container opnieuw wordt opgestart of naar een ander knooppunt wordt verplaatst, wordt hetzelfde volume weergegeven als de oudere container. Zo is het zowel efficiënt als zeer beschikbaar.

In dit voorbeeld heeft de toepassing Teller een ASP.NET Core-service met een webpagina die de tegenwaarde in een browser weergeeft.

De `counterService` periodiek leest een tegenwaarde uit een bestand, verhoogt het en schrijf het terug naar het bestand. Het bestand wordt opgeslagen in een map die is gemonteerd op het volume dat wordt ondersteund door Service Fabric Reliable Disk.

## <a name="prerequisites"></a>Vereisten

U de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken om deze taak te voltooien. Als u azure cli met dit `az --version` artikel `azure-cli (2.0.43)`wilt gebruiken, moet u ervoor zorgen dat het rendement ten minste .  Installeer (of update) de Azure Service Fabric Mesh CLI-extensiemodule door deze instructies te [volgen.](service-fabric-mesh-howto-setup-cli.md)

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure en stel uw abonnement in.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep waarin u de toepassing wilt implementeren. Met de volgende opdracht `myResourceGroup` wordt een resourcegroep genamed op een locatie in het oosten van de Verenigde Staten. Als u de naam van de brongroep hieronder wijzigt, moet u deze wijzigen in alle opdrachten die volgen.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="deploy-the-template"></a>De sjabloon implementeren

De volgende opdracht implementeert een Linux-toepassing met behulp van de [counter.sfreliablevolume.linux.json template](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json). Als u een Windows-toepassing wilt implementeren, gebruikt u de [sjabloon counter.sfreliablevolume.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.windows.json). Houd er rekening mee dat het langer kan duren voordat grotere containerafbeeldingen zijn geïmplementeerd.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/counter/counter.sfreliablevolume.linux.json
```

U ook de status van de implementatie zien met de opdracht

```azurecli-interactive
az group deployment show --name counter.sfreliablevolume.linux --resource-group myResourceGroup
```

Let op de naam van gatewayresource met resourcetype als `Microsoft.ServiceFabricMesh/gateways`. Dit zal worden gebruikt bij het verkrijgen van openbare IP-adres van de app.

## <a name="open-the-application"></a>De toepassing openen

Zodra de toepassing is geïmplementeerd, krijgt u het ipAddress van de gatewaybron voor de app. Gebruik de gatewaynaam die u in de bovenstaande sectie hebt opgemerkt.
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name counterGateway
```

De uitvoer moet `ipAddress` een eigenschap hebben die het openbare IP-adres voor het eindpunt van de service is. Open het vanuit een browser. Het zal een webpagina met de teller waarde wordt bijgewerkt elke seconde weer te geven.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Controleren of de toepassing het volume kan gebruiken

De toepassing maakt `counter.txt` een bestand `counter/counterService` met de naam in de map volume. De inhoud van dit bestand is de tegenwaarde die op de webpagina wordt weergegeven.

## <a name="delete-the-resources"></a>De bronnen verwijderen

Verwijder vaak de resources die u niet meer gebruikt in Azure. Als u de bronnen met betrekking tot dit voorbeeld wilt verwijderen, verwijdert u de brongroep waarin ze zijn geïmplementeerd (die alles wat aan de brongroep is gekoppeld) met de volgende opdracht:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

- Bekijk de voorbeeldtoepassing Betrouwbare volumeschijf servicefabric op [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Zie [Inleiding tot Service Fabric Resource-model](service-fabric-mesh-service-fabric-resources.md) voor meer informatie over het Service Fabric Resource-model.
- Lees [Wat is Service Fabric Mesh?](service-fabric-mesh-overview.md) voor meer informatie over Service Fabric Mesh.
