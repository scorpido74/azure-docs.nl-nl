---
title: Gegevensopslag - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS slaat gegevens op die zijn versleuteld in een Azure-gegevensarchief dat overeenkomt met de regio die door de sleutel is opgegeven.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: e3028a9f046edb4013d8a2d6ec88d8c3cb2ca489
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220015"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Gegevensopslag en -verwijdering in language understanding (LUIS) Cognitive Services
LUIS slaat gegevens op die zijn versleuteld in een Azure-gegevensarchief dat overeenkomt met de regio die door de sleutel is opgegeven. Deze gegevens worden 30 dagen bewaard. 

## <a name="export-and-delete-app"></a>App exporteren en verwijderen
Gebruikers hebben volledige controle over [het exporteren](luis-how-to-start-new-app.md#export-app) en [verwijderen van](luis-how-to-start-new-app.md#delete-app) de app. 

## <a name="utterances"></a>Utterances

Uitingen kunnen op twee verschillende plaatsen worden opgeslagen. 

* Tijdens **het ontwerpproces**worden uitingen gemaakt en opgeslagen in de intentie. Uitingen in intents zijn vereist voor een succesvolle LUIS-app. Zodra de app is gepubliceerd en query's op het eindpunt ontvangt, bepaalt de querytekenreeks `log=false`van het eindpuntverzoek of de eindpuntutterance is opgeslagen. Als het eindpunt wordt opgeslagen, wordt het onderdeel van de actieve leeruitingen in het gedeelte **Bouwen** van de portal in de sectie **Eindpuntuitingen controleren.** 
* Wanneer u **eindpuntuitingen controleert**en een utterance toevoegt aan een intentie, wordt de utterance niet langer opgeslagen als onderdeel van de eindpuntuitingen die moeten worden gecontroleerd. Het wordt toegevoegd aan de bedoelingen van de app. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Voorbeelduitingen uit een intentie verwijderen

Voorbeelduitingen verwijderen die worden gebruikt voor het trainen [van LUIS](luis-reference-regions.md). Als u een voorbeelduiting uit uw LUIS-app verwijdert, wordt deze verwijderd uit de LUIS-webservice en is deze niet beschikbaar voor export.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Uitingen verwijderen in beoordeling van actief leren

U uitingen verwijderen uit de lijst met gebruikersuitingen die LUIS voorstelt op de **[pagina Eindpuntuitingen controleren.](luis-how-to-review-endpoint-utterances.md)** Als u uitingen uit deze lijst verwijdert, wordt deze niet voorgesteld, maar worden ze niet uit logboeken verwijderd.

Als u geen actieve leeruitingen wilt, u [actief leren uitschakelen.](luis-how-to-review-endpoint-utterances.md#disable-active-learning) Als u actief leren uitschakelt, wordt logboekregistratie ook uitgeschakeld.

### <a name="disable-logging-utterances"></a>Logboeken uitschakelen
[Het uitschakelen van actief leren](luis-how-to-review-endpoint-utterances.md#disable-active-learning) is het uitschakelen van logboekregistratie.


<a name="accounts"></a>

## <a name="delete-an-account"></a>Een account verwijderen
Als u een account verwijdert, worden alle apps verwijderd, samen met hun voorbeelduitingen en logboeken. De gegevens worden 60 dagen bewaard voordat het account en de gegevens permanent worden verwijderd.

Het verwijderen van een account is beschikbaar op de pagina **Instellingen.** Selecteer uw accountnaam op de navigatiebalk rechtsboven om naar de pagina **Instellingen** te gaan.

## <a name="data-inactivity-as-an-expired-subscription"></a>Inactiviteit van gegevens als verlopen abonnement
Met het oog op het bewaren en verwijderen van gegevens kan een inactieve LUIS-app _naar eigen goeddunken_ van Microsoft worden behandeld als een verlopen abonnement. Een app wordt als inactief beschouwd als deze voldoet aan de volgende criteria van de afgelopen 90 dagen: 

* Heeft **geen** telefoontjes gepleegd.
* Is niet gewijzigd.
* Heeft geen huidige sleutel toegewezen aan het.
* Heeft geen gebruiker aanmelden bij het.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het exporteren en verwijderen van een app](luis-how-to-start-new-app.md)
