---
title: Beschikbaarheid van de regio StorSimple
description: Hiermee wordt uitgelegd in azure-regio's waarin de verschillende StorSimple-apparaatmodellen beschikbaar zijn.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: a2f04d6faa7770eef7768437ae0e624b76713f98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275114"
---
# <a name="available-regions-for-your-storsimple"></a>Beschikbare regio's voor uw StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Overzicht

De Azure-datacenters werken in meerdere regio's over de hele wereld om te voldoen aan de eisen van de klant op het gebied van prestaties, vereisten en voorkeuren met betrekking tot gegevenslocatie. Een Azure-geografie is een gedefinieerd gebied van de wereld dat ten minste één Azure-regio bevat. Een Azure-gebied is een gebied binnen een geografie dat een of meer datacenters bevat.

Het kiezen van een Azure-regio is erg belangrijk en de keuze van de regio wordt beïnvloed door factoren zoals gegevensresidentie en soevereiniteit, beschikbaarheid van services, prestaties, kosten en redundantie. Ga voor meer informatie over het kiezen van een regio naar [welke Azure-regio geschikt is voor mij?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

Voor de StorSimple-oplossing wordt de keuze van de regio specifiek bepaald door de volgende factoren:

- Regio's waar de StorSimple Device Manager-service beschikbaar is.
- De landen/regio's waar het fysieke, cloud- of virtuele apparaat van StorSimple beschikbaar is.
- De regio's waar de opslagaccounts die StorSimple-gegevens opslaan, moeten worden gelokaliseerd voor optimale prestaties.

In deze zelfstudie wordt de beschikbaarheid van de regio beschreven voor de StorSimple Device Manager-service, de on-premises fysieke en de cloudapparaten. De informatie in dit artikel is van toepassing op StorSimple 8000 en 1200 serie apparaten.

## <a name="region-availability-for-storsimple-device-manager-service"></a>Beschikbaarheid in de regio voor StorSimple Device Manager-service

De StorSimple Device Manager-service wordt momenteel ondersteund in 12 openbare regio's en 2 Azure Government-regio's.

U definieert een regio of locatie wanneer u de StorSimple Device Manager-service voor het eerst maakt. In het algemeen wordt een locatie gekozen die het dichtst bij het geografische gebied ligt waar het apparaat wordt geïmplementeerd. Maar het apparaat en de service kunnen ook op verschillende locaties worden geïmplementeerd.

Hier is een lijst met regio's waar de StorSimple Device Manager-service beschikbaar is voor azure public cloud en kan worden geïmplementeerd.

![storsimple-device-manager-service-regio's](./media/storsimple-region/storsimple-device-manager-service-regions.png)

Voor azure-overheidscloud is de StorSimple Device Manager-service beschikbaar in datacenters van us Gov Iowa en US Gov Virginia.

## <a name="region-availability-for-data-stored-in-storsimple"></a>Beschikbaarheid van regio's voor gegevens die zijn opgeslagen in StorSimple

StorSimple-gegevens worden fysiek opgeslagen in Azure-opslagaccounts en deze accounts zijn beschikbaar in alle Azure-regio's. Wanneer u een Azure-opslagaccount maakt, wordt de primaire locatie van het opslagaccount gekozen en bepaalt u het gebied waar de gegevens zich bevinden.

Wanneer u voor het eerst een StorSimple Device Manager-service maakt en er een opslagaccount aan koppelt, kunnen uw StorSimple Device Manager-service en Azure-opslag zich op twee afzonderlijke locaties bevinden. In dat geval moet u de StorSimple--apparaatbeheerfunctie en het Azure Storage-account afzonderlijk maken.

Kies in het algemeen de dichtstbijzijnde regio voor uw service voor uw opslagaccount. Het dichtstbijzijnde Microsoft Azure-gebied is echter mogelijk niet de regio met de laagste latentie. Het is de latentie die de prestaties van de netwerkservice en dus de prestaties van de oplossing dicteert. Dus als u een opslagaccount kiest in een andere regio, is het belangrijk om te weten wat de latencies zijn tussen uw service en de regio die is gekoppeld aan uw opslagaccount.

Als u een StorSimple Cloud Appliance gebruikt, raden we u aan dat de service en het bijbehorende opslagaccount zich in dezelfde regio bevinden. Opslagaccounts in een andere regio kunnen leiden tot slechte prestaties.

## <a name="availability-of-storsimple-device"></a>Beschikbaarheid van StorSimple-apparaat

Afhankelijk van het model kunnen de StorSimple-apparaten beschikbaar zijn in verschillende regio's of landen/regio's.

### <a name="storsimple-physical-device-models-81008600"></a>StorSimple fysiek apparaat (Modellen 8100/8600)

Als u een StorSimple 8100- of 8600-fysiek apparaat gebruikt, is het apparaat beschikbaar in de volgende landen/regio's.

| #  | Land/regio        | #  | Land/regio     | #  | Land/regio      | #  | Land/regio             |
|----|-----------------------|----|--------------------|----|---------------------|----|----------------------------|
| 1  | Australië             | 16 | Hongkong SAR      | 31 | Nieuw-Zeeland         | 46 | Zuid-Afrika               |
| 2  | Oostenrijk               | 17 | Hongarije            | 32 | Nigeria             | 47 | Zuid-Korea                |
| 3  | Bahrein               | 18 | IJsland            | 33 | Noorwegen              | 48 | Spanje                      |
| 4  | België               | 19 | India              | 34 | Peru                | 49 | Sri Lanka                  |
| 5  | Brazilië                | 20 | Indonesië          | 35 | Filipijnen         | 50 | Zweden                     |
| 6  | Canada                | 21 | Ierland            | 36 | Polen              | 51 | Zwitserland                |
| 7  | Chili                 | 22 | Israël             | 37 | Portugal            | 52 | Taiwan                     |
| 8  | Colombia              | 23 | Italië              | 38 | Puerto Rico         | 53 | Thailand                   |
| 9  | Tsjechië        | 24 | Japan              | 39 | Qatar               | 54 | Turkije                     |
| 10 | Denemarken               | 25 | Kenia              | 40 | Roemenië             | 55 | Oekraïne                    |
| 11 | Egypte                 | 26 | Koeweit             | 41 | Rusland              | 56 | Verenigde Arabische Emiraten       |
| 12 | Finland               | 27 | Macau SAR          | 42 | Saoedi-Arabië        | 57 | Verenigd Koninkrijk             |
| 13 | Frankrijk                | 28 | Maleisië           | 43 | Singapore           | 58 | Verenigde Staten              |
| 14 | Duitsland               | 29 | Mexico             | 44 | Slowakije            | 59 | Vietnam                    |
| 15 | Griekenland                | 30 | Nederland        | 45 | Slovenië            | 60 | Kroatië                    |

Deze lijst wordt gewijzigd naarmate er meer landen/regio's worden toegevoegd. Ga voor een meest actuele lijst van de regio's naar de bijlage opslagarraytermen in de [producttermen](https://www.microsoft.com/en-us/licensing/product-licensing/products).

Microsoft kan fysieke hardware verzenden en hardwareonderdelen vervangen voor StorSimple leveren aan de regio's in de voorgaande lijst.

> [!IMPORTANT]
> Plaats geen StorSimple fysiek apparaat in een regio waar StorSimple niet wordt ondersteund. Microsoft kan geen vervangende onderdelen verzenden naar landen/regio's waar StorSimple niet wordt ondersteund.

### <a name="storsimple-cloud-appliance-models-80108020"></a>StorSimple Cloud Appliance (modellen 8010/8020)

Als u een StorSimple Cloud Appliance 8010 of 8020 gebruikt, wordt het apparaat ondersteund en beschikbaar in alle regio's waar de onderliggende VM wordt ondersteund. De 8010 maakt gebruik van een _Standard_A3_ VM die wordt ondersteund in alle Azure-regio's.

De 8020 maakt gebruik van premium opslag en _Standard_DS3_ VM om een cloudtoestel te maken. De 8020 wordt ondersteund in Azure-regio's die Premium-opslag ondersteunen en _Standard_DS3_ Azure VM's. Gebruik [deze lijst](https://azure.microsoft.com/regions/services/) om te kijken of zowel **Virtuele Machines > DS-serie** als **Opslag > Schijfruimte** beschikbaar is in uw regio.

### <a name="storsimple-virtual-array-model-1200"></a>StorSimple Virtual Array (Model 1200)

Als u een StorSimple Virtual Array uit de 1200-serie gebruikt, wordt de virtuele schijfafbeelding in alle Azure-regio's ondersteund.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [prijzen voor verschillende StorSimple-modellen.](https://azure.microsoft.com/pricing/calculator/#storsimple2)
* Meer informatie over [het beheren van uw StorSimple-opslagaccount](storsimple-8000-manage-storage-accounts.md).
* Meer informatie over het [gebruik van de StorSimple Device Manager-service om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)
