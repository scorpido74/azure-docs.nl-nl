---
title: Een Azure API Management-exemplaar upgraden en schalen | Microsoft Documenten
description: In dit onderwerp wordt beschreven hoe u een Azure API Management-exemplaar upgraden en schalen.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2018
ms.author: apimpm
ms.openlocfilehash: 64649c86dbd3c3469247308bfc4dd0ed12e06949
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70018238"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Een Azure API Management-exemplaar upgraden en schalen  

Klanten kunnen een APIM-exemplaar (Azure API Management) schalen door eenheden toe te voegen en te verwijderen. Een **eenheid** bestaat uit speciale Azure-resources en heeft een bepaalde draagcapaciteit, uitgedrukt als een aantal API-aanroepen per maand. Dit nummer vertegenwoordigt geen aanroeplimiet, maar eerder een maximale doorvoerwaarde om ruwe capaciteitsplanning mogelijk te maken. De werkelijke doorvoer en latentie variëren in grote lijnen afhankelijk van factoren zoals het aantal en de snelheid van gelijktijdige verbindingen, het soort en het aantal geconfigureerde beleidsregels, de aanvraag- en antwoordgrootte en de latentie van backend.

De capaciteit en de prijs van elke eenheid zijn afhankelijk van de **laag** waarin de eenheid bestaat. U kiezen uit vier lagen: **Ontwikkelaar**, **Basic**, **Standaard**, **Premium**. Als u de capaciteit voor een service binnen een laag moet vergroten, moet u een eenheid toevoegen. Als de laag die momenteel is geselecteerd in uw APIM-exemplaar het toevoegen van meer eenheden niet toestaat, moet u upgraden naar een laag op een hoger niveau.

De prijs van elke eenheid en de beschikbare functies (bijvoorbeeld implementatie met meerdere regio's) is afhankelijk van de laag die u hebt gekozen voor uw APIM-exemplaar. De [prijzen details](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) artikel, legt de prijs per eenheid en functies die je krijgt in elke laag. 

>[!NOTE]
>Het [artikel met prijsdetails](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) toont geschatte aantallen eenheidscapaciteit in elke laag. Om nauwkeurigere getallen te krijgen, moet u kijken naar een realistisch scenario voor uw API's. Zie de capaciteit van een instantieartikel [voor Azure API Management.](api-management-capacity.md)

## <a name="prerequisites"></a>Vereisten

Als u de stappen uit dit artikel wilt volgen, moet u het volgende doen:

+ Een actief Azure-abonnement hebben.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Een APIM-exemplaar hebben. Zie [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md)voor meer informatie.

+ Inzicht in het concept [capaciteit van een Azure API Management-instantie.](api-management-capacity.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>Upgraden en de schaal aanpassen  

U kiezen uit vier lagen: **Ontwikkelaar,** **Basic,** **Standard** en **Premium.** De laag **Ontwikkelaars** moet worden gebruikt om de service te evalueren; het mag niet worden gebruikt voor productie. De laag **Ontwikkelaars** heeft geen SLA en u deze laag niet schalen (eenheden toevoegen/verwijderen). 

**Basic,** **Standard** en **Premium** zijn productielagen met SLA en kunnen worden geschaald. De **Basic-laag** is de goedkoopste laag die SLA heeft en kan worden opgeschaald naar 2 eenheden, **Standaard** laag kan worden geschaald tot maximaal vier eenheden. U een willekeurig aantal eenheden toevoegen aan de **premiumlaag.**

Met de **Premium-laag** u één Azure API Management-exemplaar distribueren over een willekeurig aantal gewenste Azure-regio's. Wanneer u in eerste instantie een Azure API Management-service maakt, bevat de instantie slechts één eenheid en bevindt zich in één Azure-gebied. Het oorspronkelijke gebied wordt aangewezen als de **primaire** regio. Extra regio's kunnen eenvoudig worden toegevoegd. Wanneer u een regio toevoegt, geeft u het aantal eenheden op dat u wilt toewijzen. U bijvoorbeeld één eenheid in de **primaire** regio hebben en vijf eenheden in een andere regio. U het aantal eenheden afstemmen op het verkeer dat u in elke regio hebt. Zie Een Azure API Management-serviceinstantie implementeren voor meer informatie [naar meerdere Azure-regio's](api-management-howto-deploy-multi-region.md).

U upgraden en downgraden naar en van elke laag. Houd er rekening mee dat upgraden of downgraden bepaalde functies kan verwijderen, bijvoorbeeld VNET's of implementatie in meerdere regio's, wanneer u downgraden naar Standaard of Basic van de Premium-laag.

> [!NOTE]
> Het upgrade- of schaalproces kan 15 tot 45 minuten duren om toe te passen. U krijgt een melding wanneer dit gebeurt.

> [!NOTE]
> API-beheerservice in de **verbruikslaag** wordt automatisch geschaald op basis van het verkeer.

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>De Azure-portal gebruiken om te upgraden en te schalen

![APIM schalen in Azure-portal](./media/upgrade-and-scale/portal-scale.png)

1. Navigeer naar uw APIM-exemplaar in de [Azure-portal.](https://portal.azure.com/)
2. Selecteer **Schalen en prijzen** in het menu.
3. Kies de gewenste laag.
4. Geef het aantal **eenheden** op dat u wilt toevoegen. U de schuifregelaar gebruiken of het aantal eenheden typen.  
    Als u de **Premium-laag** kiest, moet u eerst een regio selecteren.
5. Klik op **Opslaan**.

## <a name="downtime-during-scaling-up-and-down"></a>Downtime tijdens het opschalen en omlaag
Als u van of naar de laag Ontwikkelaars schaalt, is er downtime. Anders is er geen downtime. 


## <a name="next-steps"></a>Volgende stappen

- [Exemplaar van Azure API Management-service implementeren in meerdere Azure-regio's](api-management-howto-deploy-multi-region.md)
- [Een Azure API Management-service-instantie automatisch schalen](api-management-howto-autoscale.md)
