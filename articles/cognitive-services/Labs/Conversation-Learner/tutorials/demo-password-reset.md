---
title: Voorbeeld Conversation Learner model, wacht woord opnieuw instellen-Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Meer informatie over het maken van een demo Conversation Learner model.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 51eab34f32a20a86445da0ac44d94a31d6694b40
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703397"
---
# <a name="demo-password-reset"></a>Demo: Wachtwoord opnieuw instellen
In deze zelf studie wordt een eenvoudige technische ondersteunings bot getoond die u kan helpen bij het opnieuw instellen van wacht woorden van Conversation Learner. Het model van de bot kan worden leren over niet-getriviale dialoog stromen en reeksen met meerdere modellen, met inbegrip van een out-of-Domain-klasse. De taak kan zonder code of entiteiten worden uitgevoerd.

## <a name="video"></a>Video

[![Voor beeld van demo wachtwoord](https://aka.ms/cl_Tutorial_v3_DemoPassword_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPassword)

## <a name="requirements"></a>Vereisten
Voor deze zelf studie moet de bot voor het opnieuw instellen van het wacht woord worden uitgevoerd

    npm run demo-password

### <a name="open-the-demo"></a>De demo openen

Klik in de lijst model van de Web-UI op zelf studie voor het opnieuw instellen van een demo. 

### <a name="actions"></a>Acties

Het model bevat een reeks acties die zijn ontworpen om gebruikers te helpen veelvoorkomende wachtwoord problemen op te lossen.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>Trainings dialoogvensters

Het model bevat ook verschillende trein dialoogvensters, waaronder enkele voor beelden van de training van domein klassen. Bijvoorbeeld gebruikers die als een route richting kunnen aanvragen. De voor beeld-bot is getraind voor demonstratie doeleinden en er wordt gewoon gereageerd op ' kan niet helpen met dat. ' De lijst met bestaande trainer dialoog vensters vindt u in het linkerdeel venster.

![](../media/tutorial_pw_reset_entities.PNG)

1. Klik in het linkerdeel venster op ' dialoog vensters trainen ' en vervolgens op de knop Nieuw trainen dialoog venster.
2. In het deel venster chat, waar het bericht ' Typ uw boodschap... ', typt u ' Ik ben mijn wacht woord kwijt. '
3. Klik op de knop ' Score acties '.
4. Selecteer de reactie "is dat voor uw lokale account of Microsoft-account?"
5. In het deel venster chat, waar het bericht ' Typ uw boodschap... ', typt u ' lokaal account '
6. Klik op de knop ' Score acties '.
7. Selecteer de reactie ' welke versie van Windows hebt u? '
8. In het deel venster chatten, waar het bericht ' Typ uw boodschap... ' typt u ' Windows XP '
9. Klik op de knop ' Score acties '.
10. Klik op de knop + actie.
11. In het antwoord van de bot... Typ "oplossing: Wacht woord opnieuw instellen op Windows XP...
12. Klik op de knop maken.

### <a name="training-dialogs-for-out-of-domain-scenarios"></a>Trainings dialoogvensters voor Scenario's met buiten het domein

1. Klik in het linkerdeel venster op ' dialoog vensters trainen ' en vervolgens op het bestaande trein venster van speelgoed winkels.
2. Klik in het chat venster op de "speelgoed winkels" utterance.
3. In de "alternatieve invoer toevoegen..." het veld, typ ' zoeken op Internet ' en druk op ENTER.
4. In de "alternatieve invoer toevoegen..." het veld, typ "vlucht boeking" en druk op ENTER.
5. Klik op de knop Wijzigingen opslaan.
6. Klik op de knop bewerken opslaan.
7. Klik in het linkerdeel venster op dialoog vensters logboeken en vervolgens op de knop nieuw logboek.
8. In het deel venster chat, waar het bericht ' Typ uw boodschap... ', typt u ' Ik kan mijn wacht woord niet vinden '
9. In het deel venster chatten, waar het bericht ' Typ uw boodschap... ' typt u ' Microsoft-account '
10. In het deel venster chatten, waar het bericht ' Typ uw boodschap... ' typt u ' hartelijk dank '
11. Klik op de knop testen is voltooid.
12. Klik op het dialoog venster ' Ik kan mijn wacht woord niet vinden ' in de raster weergave.
13. Klik in het chat venster op de onjuist gerenderde oplossing: Hoe kan ik het wacht woord van een micro soft-account opnieuw instellen?
14. Klik op de knop + actie.
15. In het antwoord van de bot... typt u ' u bent welkom '
16. Klik op de knop maken.
17. Klik op de knop Opslaan als trein venster.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Demo-pizza volg orde](./demo-pizza-order.md)
