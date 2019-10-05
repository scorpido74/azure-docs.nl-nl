---
title: De query actie toevoegen in Logic apps | Microsoft Docs
description: Overzicht van de query actie voor het uitvoeren van acties zoals filter matrix.
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: 34e702c7-f9e5-4885-9266-fc7404adecfe
ms.service: logic-apps
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2016
ms.author: jehollan
ms.openlocfilehash: 10332b95e0d385d7155003efcc52b4bae3969313
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973754"
---
# <a name="get-started-with-the-query-action"></a>Aan de slag met de query actie
Met de query actie kunt u werken met batches en matrices om werk stromen uit te voeren voor het volgende:

* Maak een taak voor alle records met hoge prioriteit uit een Data Base.
* Sla alle PDF-bijlagen voor e-mail berichten op in een Azure-Blob.

Zie [een logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md)om aan de slag te gaan met de query actie in een logische app.

## <a name="use-the-query-action"></a>De query actie gebruiken
Een actie is een bewerking die wordt uitgevoerd door de werk stroom die is gedefinieerd in een logische app. 
[Meer informatie over acties](../connectors/apis-list.md).  

De query actie heeft momenteel één bewerking, die de filter matrix wordt genoemd, die wordt weer gegeven in de ontwerp functie. Hierdoor kunt u een query uitvoeren op een matrix en een set gefilterde resultaten retour neren.

U kunt dit als volgt toevoegen aan een logische app:

1. Selecteer de knop **nieuwe stap** .
2. Kies **een actie toevoegen**.
3. Typ in het zoekvak van de actie **filter** om de actie **filter matrix** weer te geven.
   
    ![De query actie selecteren](./media/connectors-native-query/using-action-1.png)
4. Selecteer een matrix om te filteren. (De volgende scherm afbeelding toont de matrix met resultaten van een Twitter-zoek opdracht.)
5. Maak een voor waarde om te evalueren voor elk item. (De volgende scherm afbeelding filtert tweets van gebruikers die meer dan 100 volgers hebben.)
   
    ![De query actie volt ooien](./media/connectors-native-query/using-action-2.png)
   
    Met deze actie wordt een nieuwe matrix uitgevoerd die alleen resultaten bevat die voldoen aan de filter vereisten.
6. Klik op de linkerbovenhoek van de werk balk om op te slaan, en uw logische app kan zowel opslaan als publiceren (activeren).

\* als u een HTTP-eind punt aanroept en een JSON-antwoord ontvangt, gebruikt u de actie _JSON parseren_ om het JSON-antwoord te parseren. Als u deze stap niet uitvoert, ziet u in de _filter matrix_ alleen de hoofd tekst en begrijpt u niet de structuur van de JSON-nettolading.

## <a name="query-action"></a>Query actie
Hier vindt u de Details voor de actie die door deze connector wordt ondersteund. De connector heeft een mogelijke actie.

| Action | Description |
| --- | --- |
| Matrix filteren |Evalueert een voor waarde voor elk item in een matrix en retourneert de resultaten |

## <a name="action-details"></a>Actiedetails
De query actie wordt geleverd met een mogelijke actie. De volgende tabellen beschrijven de vereiste en optionele invoer velden voor de actie en de bijbehorende uitvoer details die zijn gekoppeld aan het gebruik van de actie.

### <a name="filter-array"></a>Matrix filteren
Hieronder ziet u de invoer velden voor de actie, waarmee een HTTP-uitgaande aanvraag wordt gemaakt.
Een * betekent dat het een verplicht veld is.

| `Display name` | Naam van eigenschap | Description |
| --- | --- | --- |
| Na |from |De matrix die moet worden gefilterd |
| Regeling |waarbij |De voor waarde die voor elk item moet worden geëvalueerd |

<br>

### <a name="output-details"></a>Uitvoer Details
Hieronder vindt u informatie over de uitvoer van het HTTP-antwoord.

| Naam van eigenschap | Gegevenstype | Description |
| --- | --- | --- |
| Gefilterde matrix |array |Een matrix die een object bevat voor elk gefilterd resultaat |

## <a name="next-steps"></a>Volgende stappen
Probeer nu het platform uit en [Maak een logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md). U kunt de andere beschik bare connectors in Logic apps verkennen door de [lijst met api's](apis-list.md)te bekijken.

