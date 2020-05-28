---
title: Chit-Chat toevoegen aan een QnA Maker Knowledge Base
titleSuffix: Azure Cognitive Services
description: Door persoonlijke Chit toe te voegen aan uw bot kunt u meer praten en oefenen wanneer u een KB maakt. Met QnA Maker kunt u eenvoudig een vooraf gevulde set van de bovenste Chit-Chat toevoegen aan uw KB.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: e06cad8e8ca62330b09e173cf6058905b49f19e4
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83992902"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Chit-Chat toevoegen aan een kennis database

Door Chit toe te voegen aan uw bot kunt u meer praten en aantrekkelijker maken. Met de Chit-Chat functie in QnA Maker kunt u eenvoudig een vooraf gevulde set van de top Chit-Chat toevoegen aan uw Knowledge Base (KB). Dit kan een begin punt zijn voor de persoonlijkheid van uw bot en bespaart u de tijd en kosten voor het schrijven van deze bestanden.

Deze gegevensset bevat ongeveer 100 scenario's met Chit-Chat in de stem van meerdere personen, zoals Professional, vriendelijk en witty. Kies de persoon die het meest overeenkomt met de stem van uw bot. Op basis van een gebruikers query probeert QnA Maker deze te vergelijken met de dichtstbijzijnde bekende Chit-Chat QnA.

Hieronder vindt u enkele voor beelden van de verschillende persoonlijke wensen. U kunt alle persoons [gegevens sets](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) weer geven samen met details van de persoonlijke gegevens.

Voor de gebruikers query van `When is your birthday?` heeft elke persoonlijkheid een antwoord met de stijl:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Persoonlijkheid|Voorbeeld|
|--|--|
|Professional|De leeftijd is niet echt op mij van toepassing.|
|Weergave|Ik heb geen leeftijd.|
|Witty|Ik ben gratis.|
|Caring|Ik heb geen leeftijd.|
|Enthousiast|Ik ben een bot, dus ik heb geen leeftijd.|
||


## <a name="language-support"></a>Taalondersteuning

Chit-Chat gegevens sets worden ondersteund in de volgende talen:

|Taal|
|--|
|Chinees|
|Engels|
|Frans|
|Duitsland|
|Italiaans|
|Japans|
|Koreaans|
|Portugees|
|Spaans|


## <a name="add-chit-chat-during-kb-creation"></a>Chit-Chat toevoegen tijdens het maken van KB
Tijdens het maken van de Knowledge Base hebt u na het toevoegen van uw bron-Url's en-bestanden een optie voor het toevoegen van Chit-Chat. Kies de gewenste persoonlijkheid als uw Chit-Chat base. Als u geen chit-chat wilt toevoegen of als u al Chit-Chat ondersteuning hebt in uw gegevens bronnen, kiest u **geen**.

## <a name="add-chit-chat-to-an-existing-kb"></a>Chit-Chat toevoegen aan een bestaande KB
Selecteer uw KB en navigeer naar de pagina **instellingen** . Er is een koppeling naar alle Chit-Chat gegevens sets in de juiste **. TSV** -indeling. Down load de gewenste persoonlijkheid en upload deze als een bestands bron. Zorg ervoor dat u de indeling of de meta gegevens niet bewerkt wanneer u het bestand downloadt en uploadt.

![Chit-Chat toevoegen aan bestaande KB](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>Uw chit-chat vragen en-antwoorden bewerken
Wanneer u uw KB bewerkt, ziet u een nieuwe bron voor Chit-Chat, op basis van de door u geselecteerde persoonlijkheid. U kunt nu gewijzigde vragen toevoegen of de antwoorden bewerken, net als bij elke andere bron.

![Chit-Chat QnAs bewerken](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

Als u de meta gegevens wilt bekijken, selecteert u **weergave opties** op de werk balk en selecteert u vervolgens **meta gegevens weer geven**.

## <a name="add-additional-chit-chat-questions-and-answers"></a>Aanvullende chit-chat vragen en-antwoorden toevoegen
U kunt een nieuw Chit-Chat QnA-paar toevoegen dat zich niet in de vooraf gedefinieerde gegevensset bevindt. Zorg ervoor dat u geen QnA-paar dupliceert dat al wordt behandeld in de Chit-Chat set. Wanneer u een nieuwe Chit-Chat QnA toevoegt, wordt deze toegevoegd aan uw **redactionele** bron. Om ervoor te zorgen dat de classificatieer weet dat dit Chit is, voegt u de meta gegevens sleutel/waardepaar ' Redactionel: Chitchat ' toe, zoals in de volgende afbeelding wordt weer gegeven:

![! [Add Chit-Chat QnAs] (.. /media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png#lightbox)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Chit verwijderen van een bestaande KB
Selecteer uw KB en navigeer naar de pagina **instellingen** . Uw specifieke Chit-Chat bron wordt weer gegeven als een bestand met de naam van de geselecteerde persoonlijkheid. U kunt dit als een bron bestand verwijderen.

![Chit verwijderen-chatten vanuit KB](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een Knowledge Base importeren](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Zie ook

[Overzicht van QnA Maker](../Overview/overview.md)
