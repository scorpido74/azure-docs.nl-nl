---
title: Wat is Azure FarmBeats
description: Biedt een overzicht van Azure FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: b067b18985905b226287f9dd10ad4b937fab6df1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76767972"
---
# <a name="overview-of-azure-farmbeats-preview"></a>Overzicht van Azure FarmBeats (voorbeeld)

Azure FarmBeats is een business-to-business-aanbod dat beschikbaar is in Azure Marketplace. Het maakt aggregatie van de landbouw gegevenssets tussen aanbieders. Azure FarmBeats stelt u in staat om kunstmatige intelligentie (AI) of machine learning (ML) modellen te bouwen op basis van gefuseerde gegevenssets. Door Azure FarmBeats te gebruiken, kunnen landbouwbedrijven zich richten op kernwaardetoevoegen in plaats van het ongedifferentieerde zware werk van data engineering.

> [!NOTE]
> Azure FarmBeats is momenteel in openbare preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie. Azure FarmBeats wordt geleverd zonder servicelevelovereenkomst. Gebruik het [Azure FarmBeats-forum](https://aka.ms/FarmBeatsMSDN ) voor ondersteuning.

![Project Farm Beats](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

Met de preview van Azure FarmBeats u:

- Beoordeel de gezondheid van de boerderij aan de hand van vegetatie-index en waterindex op basis van satellietbeelden.
- Krijg aanbevelingen over hoeveel bodemvochtsensoren u moet gebruiken en waar u ze plaatsen.
- Volg de omstandigheden van de boerderij door grondgegevens te visualiseren die zijn verzameld door sensoren van verschillende leveranciers.
- Krijg bodemvocht kaart op basis van de fusie van satelliet-en sensorgegevens.
- Krijg bruikbare inzichten door AI/ML-modellen te bouwen bovenop geaggregeerde gegevenssets.
- Bouw of breid uw digitale landbouwoplossing uit door gezondheidsadviezen op de boerderij te bieden.

## <a name="datahub"></a>Datahub

De Azure FarmBeats Datahub is een API-laag, die aggregatie, normalisatie en contextualisatie van verschillende landbouwgegevenssets tussen providers mogelijk maakt. U Azure FarmBeats gebruiken om:
- **Sensorgegevens** van twee sensorproviders [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/), [Teralytic](https://teralytic.com/), [Pessl Instruments](https://metos.at/)
- **Satellietbeelden** van de [Sentinel-2-satellietmissie](https://sentinel.esa.int/web/sentinel/home) van de Europese Ruimtevaartorganisatie
- **Drone beelden** van drie drone beelden providers [senseFly](https://www.sensefly.com/) , [SlantRange](https://slantrange.com/) , [DJI](https://dji.com/)

Datahub is ontworpen als een uitbreidbaar API-platform. We werken met veel meer providers samen om te integreren met Azure FarmBeats, zodat u meer keuze hebt tijdens het bouwen van uw oplossing.

## <a name="accelerator"></a>Accelerator

De Azure FarmBeats Accelerator is een voorbeeldwebtoepassing die bovenop Datahub is gebouwd. De Accelerator start uw gebruikersinterface en modelontwikkeling. De Azure FarmBeats-versneller maakt gebruik van api's van Azure FarmBeats. Het visualiseert ingenomen sensorgegevens als grafieken en modeloutputs als kaarten. U de versneller bijvoorbeeld gebruiken om snel een boerderij te maken en gemakkelijk een vegetatie-indexkaart of een sensorplaatsingskaart voor die boerderij te krijgen.

## <a name="resources"></a>Resources

Azure FarmBeats wordt gratis aangeboden en u betaalt alleen voor de Azure-resources die u gebruikt. U de onderstaande bronnen gebruiken om meer te weten te komen over het aanbod:

- Blijf op de hoogte van het laatste Azure FarmBeats-nieuws door onze [Azure FarmBeats-blog te](https://aka.ms/farmbeatsblog)bezoeken.
- Zoek hulp door een vraag te plaatsen op ons [Azure FarmBeats-ondersteuningsforum.](https://aka.ms/farmbeatssupport)
- Geef feedback door een functieidee te plaatsen of te stemmen op ons [Azure FarmBeats-feedbackforum.](https://aka.ms/farmbeatsfeedback)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Azure FarmBeats installeren](install-azure-farmbeats.md)
