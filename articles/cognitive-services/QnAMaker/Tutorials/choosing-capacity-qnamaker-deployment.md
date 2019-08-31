---
title: Capaciteit van de resource voor de implementatie - QnA Maker
titleSuffix: Azure Cognitive Services
description: Voordat u uw QnA Maker-service maakt, moet u bepalen welke laag van de bovenstaande services is geschikt voor u.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 2d8f0fce3cb8f1cd8fdb596cb4e238a79d6cee4c
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193497"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Capaciteit voor uw implementatie QnA Maker kiezen

De QnA Maker-service heeft een afhankelijkheid op drie Azure-resources:
1.  App Service (voor de runtime)
2.  Azure Search (voor het opslaan en zoeken van QnAs)
3.  App Insights (optioneel, voor het opslaan van chatlogs en telemetrie)

Voordat u uw QnA Maker-service maakt, moet u bepalen welke laag van de bovenstaande services is geschikt voor u. 

Er zijn doorgaans drie parameters die u moet overwegen:

1. **De door Voer die u nodig hebt via de service**: Selecteer het juiste [app-abonnement](https://azure.microsoft.com/pricing/details/app-service/plans/) voor uw app service op basis van uw behoeften. U kunt [omhoog schalen](https://docs.microsoft.com/azure/app-service/manage-scale-up) of u de App. Dit moet ook van invloed zijn op uw Azure Search-SKU-selectie, Zie meer informatie [hier](https://docs.microsoft.com/azure/search/search-sku-tier).

1. **Grootte en aantal kennis grondslagen**: Kies de juiste [Azure Search-SKU](https://azure.microsoft.com/pricing/details/search/) voor uw scenario. U kunt N-1 knowledge bases publiceren in een bepaalde laag, waarbij N staat voor het maximale aantal indexen toegestaan in de laag. Controleer ook de maximale grootte en het aantal documenten toegestaan per laag.

    Als uw laag bijvoorbeeld 15 toegestane indexen heeft, kunt u 14 Knowledge bases publiceren (1 index per gepubliceerde kennis basis). De vijftiende index wordt gebruikt voor alle kennis grondslagen voor ontwerpen en testen. 

1. **Aantal documenten als bronnen**: De gratis SKU van de QnA Maker Management-service beperkt het aantal documenten dat u kunt beheren via de portal en de Api's tot 3 (van elke grootte van 1 MB). De standaard SKU heeft geen limieten voor het aantal documenten die u kunt beheren. Meer informatie [hier](https://aka.ms/qnamaker-pricing).

De volgende tabel biedt richtlijnen op hoog niveau.

|                        | QnA Maker-Management | App Service | Azure Search | Beperkingen                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Experiment        | Gratis SKU             | Gratis versie   | Gratis versie    | Maximaal 2 kB's, 50 MB's publiceren  |
| Dev/Test-omgeving   | Standaard SKU         | Gedeeld      | Basic        | Publiceren tot 14 kB's, grootte van 2 GB    |
| Productie-omgeving | Standaard SKU         | Basic       | Standard     | Maximaal 49 kB's, 25 GB grootte publiceren |

Zie voor het upgraden van uw stack QnA Maker, [uw QnA Maker-service upgraden](../How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Upgrade uw QnA Maker-service](../How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker)
