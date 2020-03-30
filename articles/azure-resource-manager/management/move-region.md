---
title: Azure-bronnen verplaatsen naar een andere regio
description: Biedt een overzicht van het verplaatsen van Azure-resources in Azure-regio's.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 22d8bcee96b4ac52641d4f0841267195f44fe15a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485206"
---
# <a name="moving-azure-resources-across-regions"></a>Azure-resources verplaatsen tussen regio's

In dit artikel vindt u informatie over het verplaatsen van Azure-resources in Azure-regio's.

Azure-regio's, regio's en beschikbaarheidszones vormen de basis van de wereldwijde Azure-infrastructuur. [Azure-regio's](https://azure.microsoft.com/global-infrastructure/geographies/) bevatten doorgaans twee of meer [Azure-regio's.](https://azure.microsoft.com/global-infrastructure/regions/) Een regio is een gebied binnen een geografie, met beschikbaarheidszones en meerdere datacenters. 

Nadat u resources hebt geïmplementeerd in een specifiek Azure-gebied, zijn er een aantal redenen waarom u resources naar een andere regio wilt verplaatsen.

- **Uitlijnen op een regiostart:** Verplaats uw resources naar een nieuw geïntroduceerdAzure-gebied dat nog niet eerder beschikbaar was.
- **Uitlijnen voor services/functies:** verplaats resources om te profiteren van services of functies die beschikbaar zijn in een specifieke regio.
- **Reageren op bedrijfsontwikkelingen**: Verplaats resources naar een regio als reactie op bedrijfsveranderingen, zoals fusies of overnames.
- **Uitlijnen voor nabijheid:** Resources verplaatsen naar een regio die lokaal is voor uw bedrijf.
- **Voldoen aan gegevensvereisten**: Resources verplaatsen om af te stemmen op de vereisten voor gegevensresidentie of vereisten voor gegevensclassificatie. [Meer informatie](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **Reageren op implementatievereisten:** Resources verplaatsen die ten onrechte zijn geïmplementeerd of verplaatsen in reactie op capaciteitsbehoeften. 
- **Reageren op ontmanteling**: Resources verplaatsen als gevolg van ontmanteling van regio's.

## <a name="move-process"></a>Proces verplaatsen

Het werkelijke verplaatsingsproces is afhankelijk van de resources die u verplaatst. Er zijn echter enkele gemeenschappelijke belangrijke stappen:

- **Vereisten verifiëren:** vereisten omvatten ervoor zorgen dat de resources die u nodig hebt beschikbaar zijn in het doelgebied, controleren of u voldoende quota hebt en controleren of uw abonnement toegang heeft tot het doelgebied.
- **Afhankelijkheden analyseren:** uw resources kunnen afhankelijk zijn van andere bronnen. Voordat u gaat, u afhankelijkheden berekenen, zodat verplaatste resources blijven functioneren zoals verwacht na de verhuizing.
- **Voorbereiden op verhuizing:** dit zijn de stappen die u in uw primaire regio neemt voordat de verhuizing wordt verplaatst. Het kan bijvoorbeeld nodig zijn om een Azure Resource Manager-sjabloon te exporteren of resources van bron naar doel te repliceren.
- **De resources verplaatsen:** Hoe u resources verplaatst, is afhankelijk van wat ze zijn. Mogelijk moet u een sjabloon implementeren in het doelgebied of resources naar het doel halen.
- **Doelresources weggooien:** Na het verplaatsen van resources wilt u misschien een kijkje nemen naar de resources die nu in het doelgebied zijn en beslissen of er iets is dat u niet nodig hebt.
- **De stap vastleggen**: Na verificatie van resources in het doelgebied, vereisen sommige resources mogelijk een definitieve commit-actie. In een doelregio die nu de primaire regio is, moet u bijvoorbeeld herstel bij rampen instellen in een nieuwe secundaire regio. 
- **De bron opruimen:** Ten slotte u, nadat alles in de nieuwe regio is uitgevoerd, resources die u hebt gemaakt voor de verhuizing en resources in uw primaire regio opschonen en ontmantelen.



## <a name="next-steps"></a>Volgende stappen

Zie Ondersteuning voor de exploitatie van [resources](region-move-support.md)verplaatsen voor een lijst met resources voor een lijst met resources .
