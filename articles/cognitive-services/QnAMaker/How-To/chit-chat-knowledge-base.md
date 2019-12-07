---
title: Chit chat toe te voegen aan een kennisdatabase QnA Maker
titleSuffix: Azure Cognitive Services
description: Persoonlijke chit-chat toe te voegen aan uw bot kunt u meer beschrijvende en aantrekkelijke bij het maken van een KB. QnA Maker kunt u eenvoudig een vooraf gevulde set van de bovenste chit-chat, toevoegen aan uw KB.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: a9a14056e6be62fc1c1b5e542c1a3acceb738eac
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74901199"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Chit chat toevoegen aan een kennisdatabase

Chit chat toe te voegen aan uw bot kunt u meer beschrijvende en aantrekkelijke. De functie chit chat in QnA maker kunt u eenvoudig een vooraf gevulde set van de bovenste chit-chat, toevoegen aan uw knowledge base (KB). Dit kan een startpunt voor de persoonlijkheid van uw bot, en u bespaart de tijd en kosten van het schrijven van deze helemaal.  

Deze gegevensset bevat ongeveer 100 scenario's met Chit-Chat in de stem van meerdere personen, zoals Professional, vriendelijk en witty. Kies de persona die van uw bot stem het beste past. De aanvraag voor een gebruiker worden gegeven, probeert QnA Maker moet deze overeenkomen met de meest bekende chit-chat QnA.  

Hieronder vindt u enkele voor beelden van de verschillende persoonlijke wensen. U kunt alle persoons [gegevens sets](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) weer geven samen met details van de persoonlijke gegevens.

Voor de gebruikers query van `When is your birthday?`heeft elke persoonlijkheid een antwoord met de stijl:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Persoonlijkheid|Voorbeeld|
|--|--|
|Professioneel|De leeftijd is niet echt op mij van toepassing.|
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
|Nederlands|
|Frans|
|Duitsland|
|Italiaans|
|Japans|
|Koreaans|
|Portugees|
|Spaans|


## <a name="add-chit-chat-during-kb-creation"></a>Chit chat toevoegen tijdens het maken van KB
Tijdens de ontwikkeling van knowledge base, na het toevoegen van de bron-URL en de bestanden, is er een optie voor het toevoegen van chit chat. Kies de persoonlijkheid die u wilt gebruiken als uw base chit chat. Als u niet wilt toevoegen van chit chatten of als u al chit-chat-ondersteuning in uw gegevensbronnen, kiest u **geen**. 

## <a name="add-chit-chat-to-an-existing-kb"></a>Chit chat toevoegen aan een bestaande KB
Selecteer uw KB, en Ga naar de **instellingen** pagina. Er is een koppeling naar alle chit chat-gegevenssets in de juiste **.tsv** indeling. De persoonlijkheid die u wilt downloaden, en vervolgens uploaden als een bron. Zorg ervoor dat u niet de indeling of de metagegevens bewerken wanneer u downloadt en upload het bestand. 
  
![Chit chat toevoegen aan bestaande KB](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>Bewerk uw chit chat vragen en antwoorden
Wanneer u uw KB bewerkt, ziet u een nieuwe bron voor chit-chat, op basis van de persoonlijkheid die u hebt geselecteerd. U kunt nu gewijzigd vragen toevoegen of bewerken van de reacties, net als met een andere bron. 

![Chit chat vragen en antwoorden supereenvoudig bewerken](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

Als u de meta gegevens wilt bekijken, selecteert u **weergave opties** op de werk balk en selecteert u vervolgens **meta gegevens weer geven**.

## <a name="add-additional-chit-chat-questions-and-answers"></a>Toevoegen van extra chit-chat vragen en antwoorden
U kunt nieuwe chit chat QnA die niet in de vooraf gedefinieerde toevoegen. Zorg ervoor dat u niet een combinatie van QnA die al wordt beschreven in de set chit chat dupliceert. Wanneer u een nieuwe chit chat QnA toevoegt, wordt deze toegevoegd aan uw **redactionele** bron. Om ervoor te zorgen dat de classificatieer weet dat dit Chit is, voegt u de meta gegevens sleutel/waardepaar ' Redactionel: Chitchat ' toe, zoals in de volgende afbeelding wordt weer gegeven:
   
![! [Add Chit-Chat QnAs] (.. /media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png#lightbox)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Chit chat verwijderen uit een bestaande KB
Selecteer uw KB, en Ga naar de **instellingen** pagina. Uw specifieke chit-chat-bron wordt vermeld als een bestand met de naam van de geselecteerde persoonlijkheid. U kunt dit als een bestand verwijderen.

![Chit chat uit KB verwijderen](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een kennisdatabase importeren](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Zie ook 

[Overzicht van QnA Maker](../Overview/overview.md)
