---
title: Overzicht van runtime van Azure Functions
description: Overzicht van de Runtime Preview van Azure Functions
author: apwestgarth
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: ab04aa4ca7f54e8de120d078a313c3096a350aa5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74226635"
---
# <a name="azure-functions-runtime-overview-preview"></a>Overzicht van runtime van Azure-functies (voorbeeld)

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

De Runtime van Azure Functions (preview) biedt u een nieuwe manier om te profiteren van de eenvoud en flexibiliteit van het on-premises azure-functies-programmeermodel. Azure Functions Runtime is gebaseerd op dezelfde open source-wortels als Azure-functies en wordt on-premises geïmplementeerd om een bijna identieke ontwikkelervaring te bieden als de cloudservice.

![Runtime Preview-portal voor Azure-functies][1]

De Azure Functions Runtime biedt u een manier om Azure-functies te ervaren voordat u zich verbindt met de cloud. Op deze manier kunnen de codeassets die u bouwt, vervolgens bij het migreren met u naar de cloud worden gebracht.  De runtime opent ook nieuwe opties voor u, zoals het gebruik van de reservekracht van uw on-premises computers om batchprocessen 's nachts uit te voeren. U apparaten binnen uw organisatie ook gebruiken om gegevens voorwaardelijk naar andere systemen te verzenden, zowel on-premises als in de cloud.

De Runtime azure-functies bestaat uit twee delen:

* Runtime Management-rol azure-functies
* Runtime Worker-rol azure-functies

## <a name="azure-functions-management-role"></a>Azure-functiebeheerrol

De functie Azure-functiesbeheer biedt een host voor het beheer van uw on-premises functies. Met deze rol worden de volgende taken uitgevoerd:

* Hosting van de Azure Functions Management Portal, dezelfde portal die u ziet in de [Azure-portal.](https://portal.azure.com) De portal biedt een consistente ervaring waarmee u uw functies op dezelfde manier ontwikkelen als in de Azure-portal.
* Functies distribueren over meerdere functiewerkers.
* Het verstrekken van een publicatieeindpunt zodat u uw functies rechtstreeks vanuit Microsoft Visual Studio publiceren door het publicatieprofiel te downloaden en te importeren.

## <a name="azure-functions-worker-role"></a>Werknemerrol azure-functies

De Functierollen azure-functies worden geïmplementeerd in Windows-containers en worden uitgevoerd in de plaats waar uw functiecode wordt uitgevoerd.  U meerdere worker rollen implementeren in uw hele organisatie en deze optie is een belangrijke manier waarop klanten gebruik kunnen maken van reserverekenkracht.  Een voorbeeld van waar reserve compute bestaat in veel organisaties is machines ingeschakeld voortdurend, maar niet wordt gebruikt voor langere tijd.

## <a name="minimum-requirements"></a>Minimumeisen

Om aan de slag te gaan met de Runtime azure functions, moet u een machine hebben met Windows Server 2016 of Windows 10 Creators Update met toegang tot een SQL Server-exemplaar.

## <a name="next-steps"></a>Volgende stappen

De [voorbeeldvan De runtime-voorvertoning van Azure Functions installeren](https://aka.ms/azafrdoc)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
