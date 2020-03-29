---
title: Schaalbaarheid en prestaties - Personalizer
titleSuffix: Azure Cognitive Services
description: 'Krachtige en krachtige en drukke websites en toepassingen hebben twee belangrijke factoren waarmee u rekening moet houden met Personalizer voor schaalbaarheid en prestaties: latentie en trainingsdoorvoer.'
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: 5ac9a870cb05328f040febd0f8161a97f0982e09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73490791"
---
# <a name="scalability-and-performance"></a>Schaalbaarheid en prestaties

Krachtige en drukke websites en toepassingen met veel verkeer hebben twee belangrijke factoren waarmee u rekening moet houden met Personalizer voor schaalbaarheid en prestaties:

* Lage latentie houden bij het maken van Rank API-aanroepen
* Ervoor zorgen dat de trainingsdoorvoer gelijke tred houdt met de invoer van gebeurtenissen

Personalisatie kan een rang snel retourneren, waarbij het grootste deel van de gespreksduur is gewijd aan communicatie via de REST API. Azure schaalt de mogelijkheid om snel op verzoeken te reageren automatisch op.

##  <a name="low-latency-scenarios"></a>Scenario's met lage latentie

Sommige toepassingen vereisen lage latencies bij het retourneren van een rang. Lage latencies zijn noodzakelijk:

* Om te voorkomen dat de gebruiker een merkbare hoeveelheid tijd wacht voordat de gerangschikte inhoud wordt weergegeven.
* Om te helpen een server die extreme verkeer te voorkomen dat koppelverkoop schaarse rekentijd en netwerkverbindingen.


## <a name="scalability-and-training-throughput"></a>Schaalbaarheid en trainingsdoorvoer

Personalizer werkt door een model bij te werken dat wordt aangepast op basis van berichten die asynchroon worden verzonden door Personalizer na rank- en belonings-API's. Deze berichten worden verzonden via een Azure EventHub voor de toepassing.

 Het is onwaarschijnlijk dat de meeste toepassingen de maximale verbindings- en trainingsdoorvoer van Personalizer zullen bereiken. Hoewel het bereiken van dit maximum de toepassing niet vertraagt, zou dit betekenen dat wachtrijen voor gebeurtenishub intern sneller worden gevuld dan ze kunnen worden opgeschoond.

## <a name="how-to-estimate-your-throughput-requirements"></a>Hoe u uw doorvoervereisten inschatten

* Schat het gemiddelde aantal bytes per rankinggebeurtenis en voeg de lengtes van de context- en actie-JSON-documenten toe.
* Deel 20MB/sec door deze geschatte gemiddelde bytes.

Als uw gemiddelde payload bijvoorbeeld 500 functies heeft en elk is naar schatting 20 tekens, dan is elke gebeurtenis ongeveer 10 kb. Met deze schattingen, 20.000.000 / 10.000 = 2.000 gebeurtenissen per seconde, dat is ongeveer 173 miljoen evenementen per dag. 

Als u deze limieten bereikt, neem dan contact op met ons ondersteuningsteam voor architectuuradvies.

## <a name="next-steps"></a>Volgende stappen

[Personaliser maken en configureren](how-to-settings.md).