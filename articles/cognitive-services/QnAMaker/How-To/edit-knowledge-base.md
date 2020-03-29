---
title: Een kennisbank bewerken - QnA Maker
description: Met QnA Maker u de inhoud van uw kennisbank beheren door een gebruiksvriendelijke bewerkingservaring te bieden.
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 223ad3a607adc20bbe608598da9742d56788b2c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131656"
---
# <a name="edit-qna-sets-in-your-knowledge-base"></a>QnA bewerken zet je kennisbank in

Met QnA Maker u de inhoud van uw kennisbank beheren door een gebruiksvriendelijke bewerkingservaring te bieden.

QnA-sets worden toegevoegd vanuit een gegevensbron, zoals een bestand of URL, of toegevoegd als redactionele bron. Een redactionele bron geeft aan dat de QnA-set handmatig is toegevoegd in de QnA-portal. Alle QnA-sets zijn beschikbaar voor bewerking.

## <a name="add-an-editorial-qna-set"></a>Een redactionele QnA-set toevoegen
1. Meld u aan bij de [QnA-portal](https://www.qnamaker.ai/)en selecteer vervolgens de kennisbank waaraan u de QnA-set wilt toevoegen.
1. Selecteer op de **pagina EDIT** van de kennisbank De optie **QnA-set toevoegen** om een nieuwe QnA-set toe te voegen.

1. Voeg in de nieuwe qna-setrij de vereiste **velden Vraag** en **Antwoord** toe. De andere velden zijn optioneel. Alle velden kunnen op elk gewenst moment worden gewijzigd.

1. Voeg eventueel **alternatieve formuleringen**toe . Alternatieve formulering is elke vorm van de vraag die aanzienlijk verschilt van de oorspronkelijke vraag, maar moet hetzelfde antwoord bieden.

    Wanneer uw kennisbasis wordt gepubliceerd en u actief leren hebt ingeschakeld, verzamelt QnA Maker alternatieve formuleringskeuzes die u accepteren. Deze keuzes worden geselecteerd om de nauwkeurigheid van de voorspelling te verhogen.

1. Voeg eventueel **metagegevens**toe . Als u metagegevens wilt weergeven, selecteert u **Weergaveopties** in het contextmenu. Metagegevens bieden filters voor de antwoorden die de clienttoepassing, zoals een chatbot, biedt.

1. Voeg eventueel **vervolgprompts**toe. Vervolgprompts bieden extra gesprekspaden naar de clienttoepassing om aan de gebruiker te presenteren.

1. Selecteer **Opslaan en trainen** om voorspellingen te zien, waaronder de nieuwe QnA-set.

## <a name="edit-a-qna-set"></a>Een QnA-set bewerken

Elk veld in een QnA-set kan worden bewerkt, ongeacht de oorspronkelijke gegevensbron. Sommige velden zijn mogelijk niet zichtbaar vanwege de huidige instellingen **voor weergaveopties,** die te vinden zijn op de contextgereedschapsbalk.

## <a name="delete-a-qna-set"></a>Een QnA-set verwijderen

Als u een QnA wilt verwijderen, klikt u op het **pictogram verwijderen** helemaal rechts van de qna-rij. Dit is een permanente operatie. Het kan niet ongedaan worden gemaakt. Overweeg uw KB te exporteren vanaf de **pagina Publiceren** voordat u sets verwijderde.

![QnA-set verwijderen](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-set-id"></a>De QnA-set-id zoeken

Als u de QnA-set-id wilt vinden, u deze op twee plaatsen vinden:

* Plaats de plaats in het pictogram Verwijderen op de qna-setrij waarin u geïnteresseerd bent. De zwevende tekst bevat de QnA-set-id.
* Exporteer de kennisbank. Elke QnA set in de kennisbank bevat de QnA set ID.

## <a name="add-alternate-questions"></a>Alternatieve vragen toevoegen

Voeg alternatieve vragen toe aan een bestaande QnA-set om de kans op een overeenkomst met een gebruikersquery te verbeteren.

![Alternatieve vragen toevoegen](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-sets"></a>QnA-sets koppelen

Het koppelen van QnA-sets wordt voorzien van [vervolgprompts.](multiturn-conversation.md) Dit is een logische verbinding tussen QnA sets, beheerd op kennisbasis niveau. U vervolgaanwijzingen bewerken in de QnA Maker-portal.

Je QnA-sets niet koppelen aan de metadata van het antwoord.

## <a name="add-metadata"></a>Metagegevens toevoegen

Voeg metagegevenssets toe door eerst **Weergaveopties te**selecteren en vervolgens **metagegevens weergeven te**selecteren . Hiermee wordt de kolom metagegevens weergegeven. Selecteer vervolgens **+** het teken om een metagegevensset toe te voegen. Deze set bestaat uit één sleutel en één waarde.

## <a name="save-changes-to-the-qna-sets"></a>Wijzigingen opslaan in de QnA-sets

Selecteer periodiek **Opslaan en trainen** nadat u bewerkingen hebt aangebracht om te voorkomen dat wijzigingen verloren gaan.

![Metagegevens toevoegen](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Samenwerken aan een knowledge base](./collaborate-knowledge-base.md)

* [Azure-resources beheren die worden gebruikt door QnA Maker](set-up-qnamaker-service-azure.md)