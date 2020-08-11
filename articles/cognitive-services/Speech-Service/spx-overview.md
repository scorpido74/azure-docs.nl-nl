---
title: De Azure speech CLI
titleSuffix: Azure Cognitive Services
description: De speech CLI is een opdracht regel programma voor het gebruik van de spraak service zonder code te schrijven. De spraak-CLI vereist een minimale installatie, en het is eenvoudig om meteen te experimenteren met de belangrijkste functies van de speech-service om te zien of aan uw gebruiks aanvragen kan worden voldaan.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: trbye
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6b852186834fba858e8a049a8230b38f3d69164d
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067423"
---
# <a name="what-is-the-speech-cli"></a>Wat is de Speech-CLI?

De speech CLI is een opdracht regel programma voor het gebruik van de spraak service zonder code te schrijven. Voor de spraak-CLI zijn minimale instellingen vereist. het is gemakkelijk om meteen te experimenteren met de belangrijkste functies van de speech-service om te zien of aan uw gebruiks aanvragen kan worden voldaan. Binnen enkele minuten kunt u eenvoudige test werk stromen uitvoeren zoals batch Speech-Recognition van een map met bestanden, of tekst-naar-spraak op een verzameling teken reeksen uit een bestand. Naast eenvoudige werk stromen is de speech CLI gereed voor productie en kan deze worden uitgebreid om grotere processen uit te voeren met geautomatiseerde `.bat` of shell-scripts.

Het meren deel van de primaire functies in de Speech SDK is beschikbaar in de speech CLI en sommige geavanceerde functies en aanpassingen zijn vereenvoudigd in de speech-CLI. Houd rekening met de volgende richt lijnen om te bepalen wanneer u de speech CLI of de Speech SDK wilt gebruiken.

Gebruik de speech CLI wanneer:
* U wilt experimenteren met functies van de spraak service met minimale setup en zonder code
* U hebt relatief eenvoudige vereisten voor een productie toepassing met behulp van de speech-service

Gebruik de Speech SDK wanneer:
* U de functionaliteit van de spraak service wilt integreren in een specifieke taal of een specifiek platform (bijvoorbeeld C#, Python, C++)
* U hebt complexe vereisten waarvoor geavanceerde service aanvragen nodig zijn of aangepaste gedragingen ontwikkelen, waaronder het streamen van reacties

## <a name="core-features"></a>Kern functies

* Spraak herkenning: Converteer spraak naar tekst van audio bestanden of rechtstreeks vanuit een microfoon of transcribeer een opgenomen gesprek.

* Spraak-synthese: Converteer tekst naar spraak met behulp van invoer uit tekst bestanden of voer rechtstreeks vanaf de opdracht regel in. Pas de kenmerken van spraak uitvoer aan met behulp van [SSML-configuraties](speech-synthesis-markup.md)en [standaard-of Neural stemmen](speech-synthesis-markup.md#standard-neural-and-custom-voices).

* Spraak omzetting: Vertaal audio in een bron taal naar tekst of audio in een doel taal.

* Uitvoeren op Azure Compute-resources-spraak-CLI-opdrachten verzenden om uit te voeren op een Azure Remote Compute-resource met behulp van `spx webjob` .

## <a name="get-started"></a>Aan de slag

Zie het [artikel basis beginselen](spx-basics.md)om aan de slag te gaan met de spraak-cli. In dit artikel leest u hoe u enkele basis opdrachten uitvoert, en ziet u ook iets meer geavanceerde opdrachten voor het uitvoeren van batch bewerkingen voor spraak naar tekst en tekst naar spraak. Na het lezen van het artikel over de basis beginselen moet u voldoende kennis hebben van de syntaxis om te beginnen met het schrijven van enkele aangepaste opdrachten, of het automatiseren van eenvoudige spraak service bewerkingen.

## <a name="next-steps"></a>Volgende stappen

- [Basisbeginselen van de Speech CLI](spx-basics.md)
- Als uw gebruiks voorbeeld complexer is, kunt u [de spraak-SDK downloaden](speech-sdk.md)
