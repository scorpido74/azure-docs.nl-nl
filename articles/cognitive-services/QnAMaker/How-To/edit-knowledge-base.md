---
title: Een kennisbank bewerken - QnA Maker
description: Met QnA Maker u de inhoud van uw kennisbank beheren door een gebruiksvriendelijke bewerkingservaring te bieden.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3cf5dcedd4d2cfa82fda002b71abca1e5bea4b18
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756741"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>QnA-paren bewerken in uw kennisbank

Met QnA Maker u de inhoud van uw kennisbank beheren door een gebruiksvriendelijke bewerkingservaring te bieden.

QnA-paren worden toegevoegd vanuit een gegevensbron, zoals een bestand of URL, of toegevoegd als redactionele bron. Een redactionele bron geeft aan dat het QnA-paar handmatig is toegevoegd aan de QnA-portal. Alle QnA-paren zijn beschikbaar voor bewerking.

## <a name="add-an-editorial-qna-pair"></a>Een redactioneel QnA-paar toevoegen
1. Meld u aan bij de [QnA-portal](https://www.qnamaker.ai/)en selecteer vervolgens de kennisbank om het QnA-paar aan toe te voegen.
1. Selecteer op de **pagina EDIT** van de kennisbank **QnA-paar toevoegen** om een nieuw QnA-paar toe te voegen.

1. Voeg in de nieuwe rij QnA-paar de vereiste **velden Vraag** en **Antwoord** toe. De andere velden zijn optioneel. Alle velden kunnen op elk gewenst moment worden gewijzigd.

1. Voeg eventueel **alternatieve formuleringen**toe . Alternatieve formulering is elke vorm van de vraag die aanzienlijk verschilt van de oorspronkelijke vraag, maar moet hetzelfde antwoord bieden.

    Wanneer uw kennisbasis wordt gepubliceerd en u [actief leren](use-active-learning.md) hebt ingeschakeld, verzamelt QnA Maker alternatieve formuleringskeuzes die u accepteren. Deze keuzes worden geselecteerd om de nauwkeurigheid van de voorspelling te verhogen.

1. Voeg eventueel **metagegevens**toe . Als u metagegevens wilt weergeven, selecteert u **Weergaveopties** in het contextmenu. Metagegevens bieden filters voor de antwoorden die de clienttoepassing, zoals een chatbot, biedt.

1. Voeg eventueel **vervolgprompts**toe. Vervolgprompts bieden extra gesprekspaden naar de clienttoepassing om aan de gebruiker te presenteren.

1. Selecteer **Opslaan en trainen** om voorspellingen te zien, waaronder het nieuwe QnA-paar.

## <a name="edit-a-qna-pair"></a>Een QnA-paar bewerken

Elk veld in een QnA-paar kan worden bewerkt, ongeacht de oorspronkelijke gegevensbron. Sommige velden zijn mogelijk niet zichtbaar vanwege de huidige instellingen **voor weergaveopties,** die te vinden zijn op de contextgereedschapsbalk.

## <a name="delete-a-qna-pair"></a>Een QnA-paar verwijderen

Als u een QnA wilt verwijderen, klikt u op het **pictogram verwijderen** helemaal rechts van de qna-rij. Dit is een permanente operatie. Het kan niet ongedaan worden gemaakt. Overweeg uw KB te exporteren vanaf de **pagina Publiceren** voordat u sets verwijderde.

![QnA-paar verwijderen](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>Zoek de QnA-paar-id

Als u de QnA-paar-id wilt vinden, u deze op twee plaatsen vinden:

* Plaats de plaats in het delete-pictogram op de qna-paarrij waarin u geïnteresseerd bent. De zwevende tekst bevat de QnA-paar-ID.
* Exporteer de kennisbank. Elk QnA-paar in de kennisbank bevat de QnA-paar-ID.

## <a name="add-alternate-questions"></a>Alternatieve vragen toevoegen

Voeg alternatieve vragen toe aan een bestaand QnA-paar om de kans op een overeenkomst met een gebruikersquery te verbeteren.

![Alternatieve vragen toevoegen](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>QnA-paren koppelen

Het koppelen van QnA-paren is voorzien van [follow-upprompts.](multiturn-conversation.md) Dit is een logische verbinding tussen QnA paren, beheerd op het niveau van de kennisbasis. U vervolgaanwijzingen bewerken in de QnA Maker-portal.

Je QnA-paren niet koppelen aan de metadata van het antwoord.

## <a name="add-metadata"></a>Metagegevens toevoegen

Voeg metagegevenssets toe door eerst **Weergaveopties te**selecteren en vervolgens **metagegevens weergeven te**selecteren . Hiermee wordt de kolom metagegevens weergegeven. Selecteer vervolgens **+** het teken om een metagegevensset toe te voegen. Deze set bestaat uit één sleutel en één waarde.

## <a name="save-changes-to-the-qna-pairs"></a>Wijzigingen opslaan in de QnA-paren

Selecteer periodiek **Opslaan en trainen** nadat u bewerkingen hebt aangebracht om te voorkomen dat wijzigingen verloren gaan.

![Metagegevens toevoegen](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Samenwerken aan een knowledge base](./collaborate-knowledge-base.md)

* [Azure-resources beheren die worden gebruikt door QnA Maker](set-up-qnamaker-service-azure.md)