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
ms.date: 05/10/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 1450d6b41d9a193f8069509aacd3343ae646c6ac
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307025"
---
# <a name="edit-a-knowledge-base-in-qna-maker"></a>Een kennisdatabase in QnA Maker bewerken

QnA Maker kunt u de inhoud van uw knowledge base beheren door op te geven van een bewerkingservaring eenvoudig te gebruiken.

<a name="add-datasource"></a>

## <a name="edit-your-knowledge-base-content"></a>Uw knowledge base-inhoud bewerken

1.  Selecteer **mijn knowledge bases** in de bovenste navigatiebalk. 

    Ziet u alle services die u hebt gemaakt of gedeeld met u gesorteerd in aflopende volgorde van de **het laatst is gewijzigd** datum.

    ![Mijn Knowledge Bases](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Selecteer een bepaalde kennisdatabase wijzigingen aanbrengen.
 
1. Selecteer **instellingen**. Hier kunt u verplicht veld zijn servicenaam bewerken.
  
    |Doel|Bewerking|
    |--|--|
    |URL toevoegen|U kunt nieuwe URL's om toe te voegen nieuwe veelgestelde vragen-inhoud met Knowledge base door te klikken op toevoegen **kennisdatabase beheren -> ' + -URL toevoegen '** koppeling.|
    |URL verwijderen|U kunt bestaande URL's verwijderen door het verwijderingspictogram te selecteren, de Prullenbak.|
    |URL inhoud vernieuwen|Als u wilt dat uw knowledge base voor het verkennen van de meest recente inhoud van bestaande URL's, selecteert u de **vernieuwen** selectievakje. Hiermee wordt de knowledge base bijgewerkt met de meest recente inhoud van de URL.|
    |Bestand toevoegen|U kunt een document ondersteund bestand door te selecteren als onderdeel van een kennisdatabase toevoegen **kennisdatabase beheren**, vervolgens de optie **+ bestand toevoegen**|
    |Importeren|U kunt ook een bestaande kennisdatabase importeren door te selecteren **Ímport Knowledge base** knop. |
    |Update|Bijwerken van het knowledge base afhankelijk **management prijscategorie** gebruikt tijdens het maken van QnA Maker-service die is gekoppeld aan uw knowledge base. U kunt ook de beheerlaag van Azure-portal bijwerken indien nodig.

1. Wanneer u klaar bent wijzigingen aanbrengen in de knowledge base, selecteer **opslaan en trainen** in de rechterbovenhoek van de pagina om de wijzigingen te behouden.    

    ![Opslaan en trainen](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!CAUTION]
    >Als u deze pagina voordat u selecteert verlaten **opslaan en trainen**, alle wijzigingen gaan verloren.

## <a name="add-a-qna-pair"></a>Een QnA-set toevoegen

Selecteer op de pagina **bewerken** de optie **QnA paar toevoegen** om een nieuwe rij toe te voegen aan de Knowledge Base-tabel.

![QnA paar toevoegen](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Een paar QnA verwijderen

Als u wilt een QnA verwijderen, klikt u op de **verwijderen** pictogram aan de rechterkant van de QnA-rij. Dit is een permanente bewerking. Het kan niet ongedaan worden gemaakt. Houd rekening met het exporteren van uw KB van de **publiceren** pagina voordat u paren verwijdert. 

![QnA paar verwijderen](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Alternatieve vragen toevoegen

Alternatieve vragen toevoegen aan een bestaande QnA-paar voor het verbeteren van de kans op een overeenkomst aan een gebruikersquery.

![Alternatieve vragen toevoegen](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>metagegevens toevoegen

Voeg eerst de **Opties voor**het toevoegen van meta gegevens toe, en selecteer vervolgens **meta gegevens weer geven**. Hiermee wordt de kolom meta gegevens weer gegeven. Selecteer vervolgens het **+** teken om een meta gegevens paar toe te voegen. Dit paar bestaat uit één sleutel en één waarde.

![Metagegevens toevoegen](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Zorg ervoor dat u regelmatig opslaan en de kennisdatabase trainen na het aanbrengen van wijzigingen om te voorkomen dat wijzigingen verloren gaan.

## <a name="manage-large-knowledge-bases"></a>Beheren van grote knowledge bases

* **Gegevens bron groepen**: De QnAs worden gegroepeerd op de gegevens bron van waaruit ze zijn geëxtraheerd. U kunt uitvouwen of samenvouwen van de gegevensbron.

    ![Gebruik de QnA Maker gegevens bron balk om vragen en antwoorden over de gegevens bron samen te vouwen en uit te breiden](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Zoek Knowledge Base**: U kunt de Knowledge Base doorzoeken door in het tekstvak boven aan de Knowledge Base-tabel te typen. Klik op enter om te zoeken op de vraag, antwoord of de metagegevens van inhoud. Klik op het pictogram X om de search-filter te verwijderen.

    ![Gebruik het zoekvak van QnA Maker boven de vragen en antwoorden om de weer gave te verkleinen zodat alleen items kunnen worden gefilterd](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Paginering**: Snel door gegevens bronnen bladeren om grote kennis slagen te beheren

    ![Gebruik de QnA Maker paginerings functies boven de vragen en antwoorden om door pagina's van vragen en antwoorden te bladeren](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Knowledge bases verwijderen

Verwijderen van een knowledge base (KB) is een permanente bewerking. Het kan niet ongedaan worden gemaakt. Voordat u verwijdert een knowledge base, moet u de knowledge base van exporteren de **instellingen** pagina van de QnA Maker-portal. 

Als u uw KB met deelt [samenwerkers](collaborate-knowledge-base.md) vervolgens verwijderd, maar iedereen toegang tot de KB verliest. 

## <a name="delete-azure-resources"></a>Azure-resources verwijderen 

Als u een van de Azure-resources die worden gebruikt voor de QnA Maker knowledge bases verwijdert, wordt niet langer knowledge bases werken. Voordat u alle resources te verwijderen, zorg ervoor dat u uw knowledge bases van exporteert de **instellingen** pagina. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Samenwerken aan een kennisdatabase](./collaborate-knowledge-base.md)
