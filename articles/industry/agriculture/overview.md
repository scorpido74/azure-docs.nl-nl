---
title: Overzicht van Azure FarmBeats
description: Biedt een overzicht van Azure FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 12882860b8a9beadd4e56cbb151d670fac4da43b
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73798695"
---
# <a name="overview-of-azure-farmbeats"></a>Overzicht van Azure FarmBeats

Azure FarmBeats is een verzameling Azure-Services en-functies waarmee u snel intelligente gegevensgestuurde oplossingen in de land bouw kunt bouwen. Azure FarmBeats is een Azure Marketplace-aanbieding waarmee u aan de land bouw gerelateerde gegevens kunt verkrijgen, verzamelen en verwerken uit verschillende bronnen, zoals Sens oren, drones, camera's, satellieten zonder investeren in diep gaande data engineering resources.

> [!NOTE]
> Azure FarmBeats is momenteel beschikbaar als open bare preview. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie. Azure FarmBeats wordt zonder service level agreement gegeven. Gebruik het [Azure FarmBeats-forum](https://aka.ms/FarmBeatsMSDN ) voor ondersteuning.

Met Azure FarmBeats kunt u gegevens ophalen uit verschillende bronnen, zoals Sens oren, Satellite, drones, allemaal in de context van een farm (een geografisch gebied van belang).

U kunt het volgende doen:

- Agrarische gegevens sets van verschillende providers verzamelen
- Bouw snel een kunst matige intelligentie/machine language (AI/ML)-modellen door andere gegevens sets te weigeren

Azure FarmBeats biedt u een robuuste en gemakkelijke manier om het volgende te doen:

- Maak een kaart voor de farm met behulp van een geojson-bestand.
- Evalueer de farm status met behulp van de vegetatie-index en de water index op basis van satelliet afbeelding.
- Krijg aanbevelingen over het aantal Sens oren dat moet worden gebruikt en waar u deze plaatst.
- Houd de status van de farm bij door de massa gegevens te visualiseren die door Sens oren van verschillende sensor leveranciers worden verzameld.
- Kaart voor bodem vocht ophalen op basis van het fusie van satelliet-en sensor gegevens.
- Krijg inzicht in de bewerkings acties door AI/ML-modellen boven op geaggregeerde gegevens sets te bouwen.
- Bouw of verbeter uw oplossing voor digitale land bouwers door de farm status adviezen te bieden.

Azure FarmBeat-onderdelen worden besproken in de volgende secties van dit artikel.

## <a name="data-hub"></a>Data hub

Een API-laag, waarmee aggregatie, normalisatie en contextualization van verschillende agrarische gegevens sets tussen providers worden ingeschakeld. Met deze preview-versie kunt u twee Sens oren gebruiken: [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/), [Teralytic](https://teralytic.com/), één satelliet installatie kopie van de provider [Sentinel-2](https://sentinel.esa.int/web/sentinel/home), en twee drone imagey-providers [senseFly](https://www.sensefly.com/) , [SlantRange](https://slantrange.com/). Data hub is ontworpen als een API-platform en we werken met veel meer providers om te integreren met Azure FarmBeats, zodat u meer keuze hebt tijdens het bouwen van uw oplossing.

## <a name="accelerator"></a>Snelle

Een voor beeld van een oplossing, gebouwd op basis van de data hub, die uw gebruikers interface en model ontwikkeling op de hoogte heeft. Deze webtoepassing maakt gebruik van Api's om visualisatie van opgenomen sensor gegevens te demonstreren als grafieken en visualisatie van model uitvoer als kaarten. U kunt bijvoorbeeld de Accelerator gebruiken om snel een farm te maken en eenvoudig een vegetatie-index toewijzing of een sensor plaatsings kaart voor die Farm te verkrijgen.

## <a name="resources"></a>Bronnen

Ga naar FarmBeats- [blog](https://aka.ms/AzureFarmBeats) en [forums](https://aka.ms/FarmBeatsMSDN).

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan met Azure FarmBeats, gaat u naar [Azure Marketplace](https://aka.ms/FarmBeatsMarketplace) om te implementeren.
