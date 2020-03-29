---
title: Chit-chat toevoegen aan een Kennisbank QnA Maker
titleSuffix: Azure Cognitive Services
description: Het toevoegen van persoonlijke chit-chat aan uw bot maakt het meer conversatie en boeiende wanneer u een KB. Met QnA Maker u eenvoudig een vooraf ingevulde set van de top-chit-chat toevoegen aan uw KB.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220708"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Chit-chat toevoegen aan een kennisbank

Het toevoegen van chit-chat aan je bot maakt het meer conversatie en boeiende. De chit-chat functie in QnA maker u gemakkelijk een vooraf ingevulde set van de top chit-chat toe te voegen, in uw kennisbank (KB). Dit kan een uitgangspunt voor de persoonlijkheid van uw bot, en het bespaart u de tijd en de kosten van het schrijven van hen vanaf nul.  

Deze dataset heeft ongeveer 100 scenario's van chit-chat in de stem van meerdere persona's, zoals Professional, Friendly en Witty. Kies de persona die het meest lijkt op de stem van uw bot. Gezien een gebruikersquery probeert QnA Maker deze te matchen met de dichtstbijzijnde bekende chit-chat QnA.  

Enkele voorbeelden van de verschillende persoonlijkheden zijn hieronder. U alle [persoonlijkheiddatasets](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) zien, samen met details van de persoonlijkheden.

Voor de gebruikersquery van `When is your birthday?`, heeft elke persoonlijkheid een gestileerde reactie:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Persoonlijkheid|Voorbeeld|
|--|--|
|Professional|Leeftijd is niet echt van toepassing op mij.|
|Vriendelijke|Ik heb niet echt een leeftijd.|
|Witty|Ik ben leeftijdvrij.|
|Zorg|Ik heb geen leeftijd.|
|Enthousiast|Ik ben een bot, dus ik heb geen leeftijd.|
||


## <a name="language-support"></a>Taalondersteuning

Chit-chat-gegevenssets worden ondersteund in de volgende talen:

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


## <a name="add-chit-chat-during-kb-creation"></a>Chit-chat toevoegen tijdens KB-creatie
Tijdens het maken van kennisbasis, na het toevoegen van uw bron-URL's en bestanden, is er een optie voor het toevoegen van chit-chat. Kies de persoonlijkheid die u wilt als uw chit-chat basis. Als u geen chit-chat wilt toevoegen, of als u al chit-chat-ondersteuning in uw gegevensbronnen hebt, kiest u **Geen**. 

## <a name="add-chit-chat-to-an-existing-kb"></a>Chit-chat toevoegen aan een bestaande KB
Selecteer uw KB en navigeer naar de pagina **Instellingen.** Er is een link naar alle chit-chat datasets in het juiste **.tsv** formaat. Download de gewenste persoonlijkheid en upload deze vervolgens als bestandsbron. Zorg ervoor dat u de indeling of de metagegevens niet bewerkt wanneer u het bestand downloadt en uploadt. 
  
![Chit-chat toevoegen aan bestaande KB](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>Bewerk uw chit-chat vragen en antwoorden
Wanneer u uw KB bewerkt, ziet u een nieuwe bron voor chit-chat, op basis van de persoonlijkheid die u hebt geselecteerd. U nu gewijzigde vragen toevoegen of de antwoorden bewerken, net als bij elke andere bron. 

![Chit-chat QnAs bewerken](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

Als u de metagegevens wilt weergeven, selecteert u **Weergaveopties** op de werkbalk en selecteert u **Metagegevens weergeven**.

## <a name="add-additional-chit-chat-questions-and-answers"></a>Voeg extra chit-chat vragen en antwoorden toe
U nieuwe chit-chat QnA toevoegen die niet in de vooraf gedefinieerde set staat. Zorg ervoor dat u een QnA-paar dat al in de chit-chatset is gedekt, niet dupliceert. Wanneer je een nieuwe chit-chat QnA toevoegt, wordt deze toegevoegd aan je **redactionele** bron. Om ervoor te zorgen dat de ranker begrijpt dat dit chit-chat is, voegt u de metadatasleutel/waardepaar "Editorial: chitchat" toe, zoals te zien is in de volgende afbeelding:
   
![! [Chit-chat QnAs toevoegen] (.. /media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png#lightbox)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Chit-chat verwijderen uit een bestaande KB
Selecteer uw KB en navigeer naar de pagina **Instellingen.** Uw specifieke chit-chat bron wordt vermeld als een bestand, met de geselecteerde persoonlijkheidsnaam. U dit verwijderen als bronbestand.

![Chit-chat verwijderen van KB](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een kennisbank importeren](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Zie ook 

[Overzicht van QnA Maker](../Overview/overview.md)
