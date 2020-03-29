---
title: Release Notes - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Ontvang de laatste informatie over nieuwe releases van het Custom Vision-team.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: anroth
ms.openlocfilehash: b9606c63ec7b53fb0b69918c21bdd9206f34c555
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647562"
---
# <a name="custom-vision-service-release-notes"></a>Opmerkingen voor het vrijgeven van aangepaste Vision-service

## <a name="may-2-2019-and-may-10-2019"></a>2 mei 2019 en 10 mei 2019

- Bugfixes en backend verbeteringen

## <a name="may-23-2019"></a>23 mei 2019

- Verbeterde UX-ervaring met portalen met betrekking tot Azure-abonnementen, waardoor het eenvoudiger wordt om uw Azure-mappen te selecteren.

## <a name="april-18-2019"></a>18 april 2019 

- Added Object Detection export for the Vision AI Dev Kit.
- UI-tweaks, inclusief projectzoeken.

## <a name="april-3-2019"></a>3 april 2019

- Verhoogde limiet voor het aantal selectiekaders per afbeelding tot 200. 
- Bugfixes, inclusief een aanzienlijke prestatie-update voor modellen die naar TensorFlow worden geëxporteerd. 

## <a name="march-26-2019"></a>26 maart 2019

- Custom Vision Service heeft algemene beschikbaarheid ingevoerd op Azure!
- Advanced Training-functie toegevoegd met een nieuwe back-end voor machine learning voor betere prestaties, vooral op uitdagende datasets en fijnkorrelige classificatie. Met geavanceerde training u een rekentijdbudget voor training opgeven en Custom Vision identificeert experimenteel de beste trainings- en augmentatie-instellingen. Voor snelle iteraties u de bestaande snelle training blijven gebruiken.
- Geïntroduceerd 3.0 API's. Aangekondigd komende afschaffing van pre-3.0 API's op 1 oktober 2019. Zie de documentatie snelstarts voor [.Net](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/csharp-tutorial), [Python](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/python-tutorial), [Node](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/node-tutorial), [Java](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/java-tutorial), of [Ga](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/go-tutorial) voor voorbeelden over hoe aan de slag.
- Vervangen door "Standaarditeraties" door Publicatie/Onpublicatie in de 3.0 API's.
- Er zijn nieuwe modelexportdoelen toegevoegd. Dockerfile export is geüpgraded om ARM voor Raspberry Pi 3 te ondersteunen. Exportondersteuning is toegevoegd aan de [Vision AI Dev Kit.](https://visionaidevkit.com/).
- Verhoogde limiet van tags per project tot 500 voor S0-laag. Verhoogde limiet van afbeeldingen per project tot 100.000 voor S0-laag.
- Domein voor volwassenen verwijderd. Algemeen domein wordt in plaats daarvan aanbevolen.
- Aangekondigde [prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) voor algemene beschikbaarheid.  

## <a name="february-25-2019"></a>25 februari 2019

- Kondigde het einde aan van limited trial-projecten (projecten die niet zijn gekoppeld aan een Azure-bron), omdat Custom Vision de voltooiing van de verplaatsing naar azure public preview nadert. Vanaf 25 maart 2019 ondersteunt de CustomVision.ai site alleen het bekijken van projecten die zijn gekoppeld aan een Azure-bron, zoals de gratis Custom Vision-bron. Tot 1 oktober 2019 heb je nog steeds toegang tot je bestaande beperkte proefprojecten via de Custom Vision API's. Dit geeft u de tijd om API-sleutels bij te werken voor alle apps die u met Custom Vision hebt geschreven. Na 1 oktober 2019 worden alle beperkte proefprojecten die u niet naar Azure hebt verplaatst, verwijderd.

## <a name="january-22-2019"></a>22 januari 2019

- Ondersteuning toegevoegd voor nieuwe Azure-regio's: West US 2, Oost-VS, Oost-VS 2, West-Europa, Noord-Europa, Zuidoost-Azië, Australië-Oost, Centraal-India, het Verenigd Koninkrijk Zuid, Japan-Oosten en Noord-Centraal VS. De steun voor Zuid-Centraal-VS blijft.

## <a name="december-12-2018"></a>12 december 2018

- Ondersteuning voor export van objectdetectiemodellen (geïntroduceerd objectdetectiecompact domein).
- Fixed a number of accessibility issues for improved screen reader and keyboard navigation support.
- UX-updates voor image viewer en verbeterde objectdetectie-ervaring voor snellere tagging.  
- Bijgewerkt basismodel voor objectdetectiedomein voor objectdetectie van betere kwaliteit.
- Insectenmoeilijke.

## <a name="november-6-2018"></a>6 november 2018

- Ondersteuning toegevoegd voor Logo Domain in Object Detection.

## <a name="october-9-2018"></a>9 oktober 2018

- Objectdetectie voert een betaalde preview in. U nu objectdetectieprojecten maken met een Azure-bron.
- Toegevoegd "Move to Azure" functie naar de website, om het gemakkelijker te maken om een Limited Trial project te upgraden naar een koppeling naar een Azure. resource gekoppeld project (F0 of S0.) U vindt dit op de pagina Instellingen voor uw product.  
- Export toegevoegd aan ONNX 1.2, ter ondersteuning van de Windows 2018 October Update-versie van Windows ML.
Bugfixes, ook voor ONNX-export met speciale tekens.

## <a name="august-14-2018"></a>14 augustus 2018

- Toegevoegd "Aan de slag" widget om customvision.ai site om gebruikers te begeleiden door middel van project training.
- Verdere verbeteringen aan de machine learning-pijplijn ten behoeve van multilabelprojecten (nieuwe verlieslaag).

## <a name="june-28-2018"></a>28 juni 2018

- Bug fixes & backend improvements.
- Ingeschakelde classificatie van meerdere klassen, voor projecten waarbij afbeeldingen precies één label hebben. In Voorspellingen voor de modus meerklassen zullen de waarschijnlijkheden optellen tot één (alle afbeeldingen worden ingedeeld onder de opgegeven tags).

## <a name="june-13-2018"></a>13 juni 2018

- UX refresh, gericht op gebruiksgemak en toegankelijkheid.
- Verbeteringen in de machine learning-pijplijn ten behoeve van multilabelprojecten met een groot aantal tags.
- Fixed bug in TensorFlow export. Ingeschakeld e-exportmodelversieing, zodat iteraties meerdere dan één keer kunnen worden geëxporteerd.

## <a name="may-7-2018"></a>7 mei 2018

- Preview-functie voor objectdetectie geïntroduceerd voor beperkte proefprojecten.
- Upgrade naar 2.0 API’s
- S0-laag is uitgebreid naar 250 labels en 50.000 afbeeldingen.
- Belangrijke back-endverbeteringen aan de machine learning-pijplijn voor classificatieprojecten voor afbeeldingen. Projecten die zijn getraind na 27 april 2018, profiteren van deze updates.
- Modelexport naar ONNX toegevoegd voor gebruik met Windows ML.
- Modelexport naar DockerFile toegevoegd. Dit stelt u in staat om de artefacten te downloaden om uw eigen Windows- en Linux-containers te bouwen, inclusief een DockerFile, TensorFlow-model en servicecode.
- Voor nieuw getrainde modellen die worden geëxporteerd naar TensorFlow in de algemene (compacte) en historische (compacte) domeinen, [zijn gemiddelde waarden nu (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample)voor consistentie in alle projecten.

## <a name="march-1-2018"></a>1 maart 2018

- Ingevoerd betaalde preview en aan boord op de Azure-portal. Projecten kunnen nu worden gekoppeld aan Azure-resources met een F0-laag (gratis) of een S0-laag (Standard). Introductie van S0-laagprojecten die ruimte bieden voor maximaal 100 labels en 25.000 afbeeldingen.
- Back-endwijzigingen in de machine learning-pijplijn/-normalisatieparameter. Hierdoor hebben klanten meer controle bij afwegingen over de precisie van de resultaten bij het aanpassen van de waarschijnlijkheidsdrempelwaarde. Als onderdeel van deze wijzigingen is de standaardwaarde van de waarschijnlijkheidsdrempel in de CustomVision.ai-portal ingesteld op 50%.

## <a name="december-19-2017"></a>19 december 2017

- Export naar Android (TensorFlow) toegevoegd, naast eerder uitgebrachte export naar iOS (CoreML.) Hierdoor kan de export van een getraind compact model offline worden uitgevoerd in een toepassing.
- Domeinen Detailhandel en Oriëntatiepunt (compact) zijn toegevoegd om modelexports voor deze domeinen in te schakelen.
- Uitgebrachte versie [1.2 Training API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) en [1.1 Prediction API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). Bijgewerkte API’s bieden ondersteuning voor modelexports, nieuwe voorspellingsbewerking waarmee afbeeldingen niet worden opgeslagen in Voorspellingen, en introductie van batchbewerkingen in de Training API.
- UX-aanpassingen, waaronder de mogelijkheid om te zien welk domein is gebruikt om een iteratie te trainen.
- Bijgewerkte [C# SDK en voorbeeld](https://github.com/Microsoft/Cognitive-CustomVision-Windows).