---
title: Een kennisdatabase - QnA Maker bewerken
titleSuffix: Azure Cognitive Services
description: QnA Maker kunt u de inhoud van uw knowledge base beheren door op te geven van een bewerkingservaring eenvoudig te gebruiken.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: cc4ead968a0ee2c9890c1cd24a6b70516b2b2e74
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326788"
---
# <a name="edit-a-knowledge-base-in-qna-maker"></a>Een kennisdatabase in QnA Maker bewerken

QnA Maker kunt u de inhoud van uw knowledge base beheren door op te geven van een bewerkingservaring eenvoudig te gebruiken.

<a name="add-datasource"></a>

## <a name="edit-your-knowledge-base-content"></a>Uw knowledge base-inhoud bewerken

1.  Selecteer **mijn Knowledge bases** in de bovenste navigatie balk. 

    U kunt alle services zien die u hebt gemaakt of die met u zijn gedeeld, gesorteerd in aflopende volg orde van de **Laatst gewijzigd** op.

    ![Mijn Knowledge Bases](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Selecteer een bepaalde kennisdatabase wijzigingen aanbrengen.
 
1. **Instellingen**selecteren. Hier kunt u verplicht veld zijn servicenaam bewerken.
  
    |Doel|Actie|
    |--|--|
    |URL toevoegen|U kunt nieuwe Url's toevoegen om nieuwe veelgestelde vragen over inhoud toe te voegen aan de Knowledge Base door te klikken op **Knowledge Base beheren-> koppeling + URL toevoegen** .|
    |URL verwijderen|U kunt bestaande URL's verwijderen door het verwijderingspictogram te selecteren, de Prullenbak.|
    |Inhoud vernieuwen|Als u de meest recente inhoud van bestaande Url's door de Knowledge Base wilt laten verkennen, selecteert u het selectie vakje **vernieuwen** . Hiermee wordt de Knowledge Base één keer bijgewerkt met de nieuwste URL-inhoud. Dit is niet een regel matig schema van updates.|
    |Bestand toevoegen|U kunt een ondersteund bestands document toevoegen om deel uit te maken van een Knowledge Base door **Knowledge Base beheren**te selecteren en vervolgens **+ bestand toevoegen** te selecteren|
    |Importeren|U kunt ook een bestaande Knowledge Base importeren door de **Ímport-knop Knowledge Base** te selecteren. |
    |Update|Het bijwerken van de kennis basis is afhankelijk van de **prijs categorie voor beheer** die wordt gebruikt bij het maken van QnA Maker-service die aan uw Knowledge Base is gekoppeld. U kunt ook de beheerlaag van Azure-portal bijwerken indien nodig.

1. Wanneer u klaar bent met het aanbrengen van wijzigingen in de Knowledge Base, selecteert u **opslaan en trainen** in de rechter bovenhoek van de pagina om de wijzigingen te behouden.    

    ![Opslaan en trainen](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!CAUTION]
    >Als u de pagina verlaat voordat u **opslaan en trainen**selecteert, gaan alle wijzigingen verloren.

## <a name="add-a-qna-pair"></a>Een QnA-set toevoegen

Selecteer op de pagina **bewerken** de optie **QnA paar toevoegen** om een nieuwe rij toe te voegen aan de Knowledge Base-tabel.

![QnA paar toevoegen](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Een paar QnA verwijderen

Als u een QnA wilt verwijderen, klikt u op het pictogram **verwijderen** helemaal rechts van de rij QnA. Dit is een permanente bewerking. Het kan niet ongedaan worden gemaakt. U kunt uw KB exporteren vanaf de pagina **publiceren** voordat u paren verwijdert. 

![QnA paar verwijderen](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Alternatieve vragen toevoegen

Alternatieve vragen toevoegen aan een bestaande QnA-paar voor het verbeteren van de kans op een overeenkomst aan een gebruikersquery.

![Alternatieve vragen toevoegen](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>metagegevens toevoegen

Voeg eerst de **Opties voor**het toevoegen van meta gegevens toe, en selecteer vervolgens **meta gegevens weer geven**. Hiermee wordt de kolom meta gegevens weer gegeven. Selecteer vervolgens het **+** -teken om een meta gegevens paar toe te voegen. Dit paar bestaat uit één sleutel en één waarde.

![Metagegevens toevoegen](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Zorg ervoor dat u regelmatig opslaan en de kennisdatabase trainen na het aanbrengen van wijzigingen om te voorkomen dat wijzigingen verloren gaan.

## <a name="manage-large-knowledge-bases"></a>Beheren van grote knowledge bases

* **Gegevens bron groepen**: de QnAs worden gegroepeerd op de gegevens bron van waaruit ze zijn geëxtraheerd. U kunt uitvouwen of samenvouwen van de gegevensbron.

    ![Gebruik de QnA Maker gegevens bron balk om vragen en antwoorden over de gegevens bron samen te vouwen en uit te breiden](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Zoek Knowledge Base**: u kunt de Knowledge Base doorzoeken door in het tekstvak boven aan de Knowledge Base-tabel te typen. Klik op enter om te zoeken op de vraag, antwoord of de metagegevens van inhoud. Klik op het pictogram X om de search-filter te verwijderen.

    ![Gebruik het zoekvak van QnA Maker boven de vragen en antwoorden om de weer gave te verkleinen zodat alleen items kunnen worden gefilterd](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Paginering**: snel door gegevens bronnen bladeren om grote kennis slagen te beheren

    ![Gebruik de QnA Maker paginerings functies boven de vragen en antwoorden om door pagina's van vragen en antwoorden te bladeren](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Knowledge bases verwijderen

Verwijderen van een knowledge base (KB) is een permanente bewerking. Het kan niet ongedaan worden gemaakt. Voordat u een Knowledge Base verwijdert, moet u de Knowledge Base exporteren via de pagina **instellingen** van de QnA Maker Portal. 

Als u uw KB [deelt met mede werkers en vervolgens](collaborate-knowledge-base.md) deze verwijdert, verliest iedereen de toegang tot de KB. 

## <a name="delete-azure-resources"></a>Azure-resources verwijderen 

Als u een van de Azure-resources die worden gebruikt voor de QnA Maker knowledge bases verwijdert, wordt niet langer knowledge bases werken. Voordat u een resource verwijdert, moet u de kennis bases exporteren vanaf de pagina **instellingen** . 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Samen werken aan een kennis database](./collaborate-knowledge-base.md)
