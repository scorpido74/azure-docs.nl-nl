---
title: Verbinding maken met een Synapse Studio-werkruimte resource vanuit een beperkt netwerk
description: In dit artikel leert u hoe u vanuit een beperkt netwerk verbinding maakt met uw Azure Synapse Studio-werkruimte resources
author: xujxu
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 10/25/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: d94ee3145fb073dae982019fd4096cc2ceb7cd86
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578328"
---
# <a name="connect-to-synapse-studio-workspace-resources-from-a-restricted-network"></a>Verbinding maken met Synapse Studio-werkruimte bronnen vanuit een beperkt netwerk

De doel lezer van dit artikel is de IT-beheerder van het bedrijf die het beperkte netwerk van het bedrijf beheert. De IT-beheerder staat op het punt om de netwerk verbinding tussen Azure Synapse Studio en het werk station binnen dit beperkte netwerk in te scha kelen.

In dit artikel leert u hoe u verbinding kunt maken met uw Azure Synapse-werk ruimte vanuit een beperkte netwerk omgeving. 

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement** : als u nog geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* **Azure Synapse-werk ruimte** : als u geen Synapse Studio hebt, maakt u een Synapse-werk ruimte vanuit Azure Synapse Analytics. De werkruimte naam is vereist in de volgende stap 4.
* **Een beperkt netwerk** : het beperkte netwerk wordt onderhouden door de IT-beheerder van het bedrijf. de IT-beheerder beschikt over de machtiging om het netwerk beleid te configureren. De naam van het virtuele netwerk en het bijbehorende subnet is vereist in de volgende stap 3.


## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>Stap 1: uitgaande beveiligings regels voor het netwerk toevoegen aan het beperkte netwerk

U moet vier regels voor uitgaande netwerk beveiliging toevoegen met vier service tags. Meer informatie over [service Tags Overview](/azure/virtual-network/service-tags-overview) 
* AzureResourceManager
* AzureFrontDoor. front-end
* AzureActiveDirectory
* AzureMonitor (optioneel. Voeg dit type regel alleen toe als u de gegevens wilt delen naar micro soft.)

**Azure Resource Manager** Details van uitgaande regel als hieronder. Wanneer u de andere drie regels maakt, vervangt u de waarde van ' **doel** servicetag ' door de naam van de servicetag ' **AzureFrontDoor.** front-end ', ' **AzureActiveDirectory** ', ' **AzureMonitor** ' te kiezen in de vervolg keuzelijst selectie.

![AzureResourceManager](./media/how-to-connect-to-workspace-from-restricted-network/arm-servicetag.png)


## <a name="step-2-create-azure-synapse-analytics-private-link-hubs"></a>Stap 2: Azure Synapse Analytics (persoonlijke koppelings hubs) maken

U moet een Azure Synapse Analytics (persoonlijke koppelings hubs) maken van Azure Portal. Zoek in ' **Azure Synapse Analytics (private link hubs)** ' door de Azure Portal, vul vervolgens het gewenste veld in en maak het. 

> [!Note]
> De regio moet hetzelfde zijn als de naam waar uw Synapse-werk ruimte zich bevindt.

![Synapse Analytics-hubs voor persoonlijke koppelingen maken](./media/how-to-connect-to-workspace-from-restricted-network/private-links.png)

## <a name="step-3-create-private-endpoint-for-synapse-studio-gateway"></a>Stap 3: persoonlijk eind punt maken voor Synapse Studio gateway

Voor toegang tot de Synapse Studio-gateway moet u een persoonlijk eind punt maken op basis van Azure Portal. Zoek " **persoonlijke koppeling** " via de Azure Portal. Selecteer ' **persoonlijk eind punt maken** ' in het ' **persoonlijke koppelingen centrum** ' en vul vervolgens het gewenste veld in en maak het. 

> [!Note]
> De regio moet hetzelfde zijn als de naam waar uw Synapse-werk ruimte zich bevindt.

![Een persoonlijk eind punt maken voor Synapse Studio 1](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-1.png)

Kies op het volgende tabblad van ' **resource** ' de privécloud, die u in stap 2 hierboven hebt gemaakt.

![Een persoonlijk eind punt maken voor Synapse Studio 2](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-2.png)

Op het volgende tabblad van " **configuratie** " 
* Kies de beperkte naam van het virtuele netwerk dat u hebt voor ' **virtueel netwerk** '.
* Kies het subnet van het beperkte virtuele netwerk voor " **subnet** ". 
* Selecteer **Ja** om te **integreren met een privé-DNS-zone**.

![Een persoonlijk eind punt maken voor Synapse Studio 3](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-3.png)

Nadat het eind punt van de persoonlijke koppeling is gemaakt, kunt u toegang krijgen tot de aanmeldings pagina van Synapse Studio Web Tool. U hebt echter nog geen toegang tot de resources in uw Synapse-werk ruimte totdat u de volgende stap moet volt ooien.

## <a name="step-4-create-private-endpoints-for-synapse-studio-workspace-resource"></a>Stap 4: privé-eind punten maken voor een Synapse Studio-werkruimte resource

Als u toegang wilt krijgen tot de resources in uw Synapse Studio-werkruimte resource, moet u ten minste één persoonlijk koppelings eindpunt maken met ' **dev** ' type ' **target subresource** ' en twee andere optionele persoonlijke koppelings eindpunten met typen ' **SQL** ' of ' **SqlOnDemand** ', afhankelijk van de resources in de Synapse studio-werk ruimte die u wilt gebruiken. Het maken van een persoonlijk koppelings eindpunt voor Synapse studio-werk ruimte is vergelijkbaar met het maken van een eind punt.  

Let op de onderstaande gebieden op het tabblad ' **resource** ':
* Selecteer **micro soft. Synapse/werk ruimten** in het **resource type**.
* Selecteer ' **YourWorkSpaceName** ' in ' **resource** ' die u eerder hebt gemaakt.
* Selecteer het eindpunt type in ' **doel-subresource** ':
  * **SQL** : is voor het uitvoeren van SQL-query's in de SQL-groep.
  * **SqlOnDemand** : is voor de uitvoering van SQL ingebouwde query's.
  * **Dev** : is voor het openen van alle andere binnen Synapse studio-werk ruimten. U moet ten minste een persoonlijk eind punt van de persoonlijke koppeling maken met dit type.

![Een persoonlijk eind punt maken voor Synapse studio-werk ruimte](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)


## <a name="step-5-create-private-endpoints-for-synapse-studio-workspace-linked-storage"></a>Stap 5: persoonlijke eind punten maken voor een gekoppelde opslag ruimte in Synapse Studio Workspace

Voor toegang tot de gekoppelde opslag met Storage Explorer in Synapse studio-werk ruimte moet u een persoonlijk eind punt maken met de vergelijk bare stappen in de bovenstaande stap 3. 

Let op de onderstaande gebieden op het tabblad ' **resource** ':
* Selecteer ' **micro soft. Synapse/Storage accounts** ' bij ' **resource type** '.
* Selecteer ' **YourWorkSpaceName** ' in ' **resource** ' die u eerder hebt gemaakt.
* Selecteer het eindpunt type in ' **doel-subresource** ':
  * **BLOB** : is voor de Azure Blob-opslag.
  * **DFS** : is voor Azure data Lake Storage Gen2.

![Een persoonlijk eind punt maken voor een gekoppelde opslag ruimte in Synapse Studio Workspace](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-storage.png)

U hebt nu toegang tot de gekoppelde opslag resource vanuit Storage Explorer in uw Synapse studio-werk ruimte binnen vNet.

Als uw werk ruimte het ' **beheerde virtuele netwerk inschakelen** ' heeft, zoals hieronder wordt gemaakt,

![Een persoonlijk eind punt maken voor Synapse Studio Workspace gekoppelde opslag 1](./media/how-to-connect-to-workspace-from-restricted-network/ws-network-config.png)

En u wilt dat uw notebook toegang heeft tot de gekoppelde opslag resources onder bepaald opslag account, moet u een **beheerde persoonlijke eind punten** toevoegen onder uw Synapse Studio. De ' **opslag accountnaam** ' moet de naam zijn die uw notitie blok moet gebruiken. Meer informatie over de gedetailleerde stappen [voor het maken van een beheerd persoonlijk eind punt voor uw gegevens bron](./how-to-create-managed-private-endpoints.md).

Nadat dit eind punt is gemaakt, wordt de " **goedkeurings status** " is **in behandeling**. u moet de eigenaar van dit opslag account aanvragen om het te kunnen goed keuren op het tabblad ' **particuliere endpoint Connections** ' van dit opslag account in azure Portal. Nadat de app is goedgekeurd, heeft uw notebook toegang tot de gekoppelde opslag resources onder dit opslag account.

Nu is alle ingesteld. U hebt toegang tot de resource van uw Synapse studio-werk ruimte.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Managed workspace Virtual Network](./synapse-workspace-managed-vnet.md)

Meer informatie over [beheerde privé-eindpunten](./synapse-workspace-managed-private-endpoints.md)
