---
title: Een Knowledge Base bewerken-QnA Maker
description: Met QnA Maker kunt u de inhoud van uw Knowledge Base beheren door een gebruiks vriendelijke ervaring te bieden.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3cf5dcedd4d2cfa82fda002b71abca1e5bea4b18
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80756741"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>QnA-paren in uw Knowledge Base bewerken

Met QnA Maker kunt u de inhoud van uw Knowledge Base beheren door een gebruiks vriendelijke ervaring te bieden.

QnA-paren worden toegevoegd uit een gegevens bron, zoals een bestand of URL, of worden toegevoegd als een redactionele bron. Een redactionele bron geeft aan dat het QnA paar hand matig is toegevoegd in de QnA-Portal. Alle QnA-paren zijn beschikbaar om te bewerken.

## <a name="add-an-editorial-qna-pair"></a>Een redactionel QnA-paar toevoegen
1. Meld u aan bij de [QnA-Portal](https://www.qnamaker.ai/)en selecteer vervolgens de Knowledge Base waaraan u het QnA-paar wilt toevoegen.
1. Selecteer op de pagina **bewerken** van de Knowledge Base **QnA paar toevoegen** om een nieuw QnA-paar toe te voegen.

1. Voeg in de rij nieuw QnA paar de vereiste **vraag** -en **antwoord** velden toe. De andere velden zijn optioneel. Alle velden kunnen op elk gewenst moment worden gewijzigd.

1. Voeg eventueel **alternatieve formule ring**toe. Alternatieve formule ring is een vorm van de vraag die significant afwijkt van de oorspronkelijke vraag, maar moet hetzelfde antwoord geven.

    Wanneer uw Knowledge Base is gepubliceerd en u [actief leren](use-active-learning.md) hebt ingeschakeld, QnA maker u alternatieve opties voor formule ring verzamelen die u kunt accepteren. Deze opties worden geselecteerd om de nauw keurigheid van de voor spelling te verg Roten.

1. Voeg eventueel **meta gegevens**toe. Selecteer **weergave opties** in het context menu om meta gegevens weer te geven. Meta gegevens bieden filters voor de antwoorden die de client toepassing, zoals een chat-bot, biedt.

1. U kunt eventueel **opvolgings aanwijzingen**toevoegen. Bij opvolgings prompts worden extra conversatie paden naar de client toepassing voor de gebruiker weer gegeven.

1. Selecteer **opslaan en trainen** om voor spellingen te zien, met inbegrip van het nieuwe QnA-paar.

## <a name="edit-a-qna-pair"></a>Een QnA-paar bewerken

Elk veld in een QnA paar kan worden bewerkt, ongeacht de oorspronkelijke gegevens bron. Sommige velden zijn mogelijk niet zichtbaar vanwege de instellingen voor de huidige **weergave opties** , die u vindt in de context hulpprogramma balk.

## <a name="delete-a-qna-pair"></a>Een QnA-paar verwijderen

Als u een QnA wilt verwijderen, klikt u op het pictogram **verwijderen** helemaal rechts van de rij QnA. Dit is een permanente bewerking. Deze kan niet ongedaan worden gemaakt. U kunt uw KB exporteren vanaf de pagina **publiceren** voordat u sets verwijdert.

![QnA paar verwijderen](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>De QnA-paar-ID zoeken

Als u de QnA-paar-ID moet vinden, kunt u deze op twee plaatsen vinden:

* Beweeg de muis aanwijzer over het pictogram verwijderen op de QnA die u interesseren. De aanwijs tekst bevat de QnA-paar-ID.
* De Knowledge Base exporteren. Elk QnA-paar in de Knowledge Base bevat de QnA-paar ID.

## <a name="add-alternate-questions"></a>Alternatieve vragen toevoegen

Voeg alternatieve vragen toe aan een bestaand QnA-paar om de kans te verg Roten dat er een overeenkomst is met een gebruikers query.

![Alternatieve vragen toevoegen](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>QnA-paren koppelen

Het koppelen van QnA-paren wordt bij [opvolgings aanwijzingen](multiturn-conversation.md)gegeven. Dit is een logische verbinding tussen QnA-paren die worden beheerd op het niveau van de kennis base. U kunt opvolgings prompts bewerken in de QnA Maker Portal.

U kunt geen QnA-paren koppelen in de meta gegevens van het antwoord.

## <a name="add-metadata"></a>Meta gegevens toevoegen

Voeg sets meta gegevens toe door eerst **weergave opties**te selecteren en vervolgens **meta gegevens weer geven**te selecteren. Hiermee wordt de kolom meta gegevens weer gegeven. Selecteer vervolgens het **+** teken om een set meta gegevens toe te voegen. Deze set bestaat uit één sleutel en één waarde.

## <a name="save-changes-to-the-qna-pairs"></a>Wijzigingen in de QnA-paren opslaan

Selecteer regel matig **opslaan en trainen** na het maken van bewerkingen om te voor komen dat wijzigingen verloren gaan.

![Meta gegevens toevoegen](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Samenwerken aan een knowledge base](./collaborate-knowledge-base.md)

* [Azure-resources beheren die worden gebruikt door QnA Maker](set-up-qnamaker-service-azure.md)