---
title: Azure-resources verplaatsen naar een andere regio
description: Hierin wordt een overzicht gegeven van het verplaatsen van Azure-resources tussen Azure-regio's.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 22d8bcee96b4ac52641d4f0841267195f44fe15a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75485206"
---
# <a name="moving-azure-resources-across-regions"></a>Azure-resources verplaatsen tussen regio's

Dit artikel bevat informatie over het verplaatsen van Azure-resources tussen Azure-regio's.

Azure-geografi,-regio's en-Beschikbaarheidszones vormen de basis van de wereld wijde Azure-infra structuur. Azure- [geografi](https://azure.microsoft.com/global-infrastructure/geographies/) bevatten doorgaans twee of meer [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/). Een regio is een gebied binnen een geografie met Beschikbaarheidszones en meerdere data centers. 

Na het implementeren van resources in een specifieke Azure-regio, zijn er een aantal redenen waarom u resources naar een andere regio wilt verplaatsen.

- **Uitlijnen met een regio start**: Verplaats uw resources naar een nieuw geïntroduceerde Azure-regio die voorheen niet beschikbaar was.
- **Uitlijnen voor Services/onderdelen**: resources verplaatsen om te kunnen profiteren van services of functies die beschikbaar zijn in een bepaalde regio.
- **Reageer op bedrijfs ontwikkelingen**: Verplaats resources naar een regio in antwoord op bedrijfs wijzigingen, zoals fusies of acquisities.
- **Uitlijnen voor nabijheid**: resources verplaatsen naar een regio die lokaal is voor uw bedrijf.
- **Voldoen aan de vereisten voor gegevens**: Verplaats resources om uit te lijnen met vereisten voor gegevens locatie of gegevens classificatie behoeften. [Meer informatie](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **Reageren op implementatie vereisten**: Verplaats resources die zijn geïmplementeerd, of verplaats als reactie op capaciteits behoeften. 
- **Reageren op buiten gebruik stellen**: resources verplaatsen vanwege het buiten gebruik stellen van regio's.

## <a name="move-process"></a>Proces verplaatsen

Het daad werkelijke verplaatsings proces is afhankelijk van de resources die u wilt verplaatsen. Er zijn echter enkele veelvoorkomende belang rijke stappen:

- **Vereisten controleren**: vereisten zijn onder andere het maken van de resources die u nodig hebt in de doel regio, het controleren of u voldoende quota hebt en te controleren of uw abonnement toegang heeft tot de doel regio.
- **Afhankelijkheden analyseren**: uw resources hebben mogelijk afhankelijkheden van andere resources. Voordat u gaat verplaatsen, moet u afhankelijkheden bepalen, zodat de verplaatste resources na de verplaatsing blijven functioneren zoals verwacht.
- **Voor bereiding voor verplaatsen**: Dit zijn de stappen die u in uw primaire regio hebt uitgevoerd vóór de verplaatsing. U moet bijvoorbeeld een Azure Resource Manager sjabloon exporteren of resources van de bron naar het doel repliceren.
- **De resources verplaatsen**: hoe u resources verplaatst, is afhankelijk van wat ze zijn. Mogelijk moet u een sjabloon in de doel regio implementeren of de resources mislukken voor het doel.
- **Doel resources negeren**: nadat u resources hebt verplaatst, kunt u de resources nu in de doel regio bekijken en besluiten of er iets wat u niet nodig hebt.
- **De verplaatsing door voeren**: nadat u resources in de doel regio hebt gecontroleerd, is het mogelijk dat er voor sommige resources een laatste doorvoer actie is vereist. In een doel regio die nu de primaire regio is, moet u mogelijk herstel na nood geval instellen voor een nieuwe secundaire regio. 
- **De bron opschonen**: tot slot kunt u de resources die u hebt gemaakt voor het verplaatsen en resources in uw primaire regio, opschonen en uit bedrijf nemen in de nieuwe regio.



## <a name="next-steps"></a>Volgende stappen

Zie [ondersteuning voor het verplaatsen van resources voor bronnen](region-move-support.md)voor een lijst met resources die kunnen worden verplaatst tussen regio's.
