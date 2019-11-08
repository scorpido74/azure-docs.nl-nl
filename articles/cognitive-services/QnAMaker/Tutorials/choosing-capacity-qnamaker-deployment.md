---
title: Resource capaciteit voor implementatie-QnA Maker
titleSuffix: Azure Cognitive Services
description: Voordat u uw QnA Maker-service maakt, moet u beslissen welke laag van de bovenstaande services het meest geschikt is voor u.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 5cbdb6fcf9fcdf12b54ff1db4b577bb975517131
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73793945"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Capaciteit kiezen voor uw QnA Maker-implementatie

De QnA Maker-service heeft een afhankelijkheid van drie Azure-resources:
1.  App Service (voor de runtime)
2.  Azure Cognitive Search (voor het opslaan en zoeken van QnAs)
3.  App Insights (optioneel, voor het opslaan van chat logboeken en telemetrie)

Voordat u uw QnA Maker-service maakt, moet u beslissen welke laag van de bovenstaande services het meest geschikt is voor u. 

Er zijn doorgaans drie parameters die u moet overwegen:

1. **De door Voer die u nodig hebt van de service**: Selecteer het juiste [app-abonnement](https://azure.microsoft.com/pricing/details/app-service/plans/) voor uw app service op basis van uw behoeften. U kunt de app omhoog of omlaag [schalen](https://docs.microsoft.com/azure/app-service/manage-scale-up) . Dit moet ook van invloed zijn op uw selectie van Azure Cognitive Search SKU. Zie [hier](https://docs.microsoft.com/azure/search/search-sku-tier)voor meer informatie.

1. **Grootte en aantal kennis grondslagen**: Kies de juiste [Azure Search-SKU](https://azure.microsoft.com/pricing/details/search/) voor uw scenario. U kunt N-1 Knowledge bases publiceren in een bepaalde laag, waarbij N de Maxi maal toegestane indexen in de laag is. Controleer ook de maximale grootte en het aantal documenten dat per laag is toegestaan.

    Als uw laag bijvoorbeeld 15 toegestane indexen heeft, kunt u 14 Knowledge bases publiceren (1 index per gepubliceerde kennis basis). De vijftiende index wordt gebruikt voor alle kennis grondslagen voor ontwerpen en testen. 

1. **Aantal documenten als bronnen**: de gratis SKU van de QnA Maker Management-service beperkt het aantal documenten dat u kunt beheren via de portal en de api's tot 3 (van elke grootte van 1 MB). De standaard-SKU heeft geen limieten voor het aantal documenten dat u kunt beheren. Meer informatie vindt u [hier](https://aka.ms/qnamaker-pricing).

De volgende tabel bevat een aantal richt lijnen op hoog niveau.

|                        | QnA Maker beheer | App Service | Azure Cognitive Search | Beperkingen                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Experiment        | Gratis SKU             | Gratis versie   | Gratis versie    | Publiceren tot 2 Kb's, 50 MB aan grootte  |
| Ontwikkel-en test omgeving   | Standaard SKU         | Gedeeld      | Basic        | Maxi maal 14 Kb's publiceren, grootte van 2 GB    |
| Productie omgeving | Standaard SKU         | Basic       | Standard     | Publiceer tot 49 Kb's, grootte van 25 GB |

Zie [uw QnA Maker-service upgraden](../How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker)voor informatie over het upgraden van uw QnA Maker-stack.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een upgrade uitvoeren van uw QnA Maker-service](../How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker)
