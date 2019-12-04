---
title: Verbinding maken met Office 365 Outlook
description: E-mail, contact personen en agenda's beheren met Office 365 REST Api's en Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: 858366947fe21a20d6f112fc51899d1533a36472
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789611"
---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Aan de slag met Office 365 Outlook Connector
De Office 365 Outlook-connector maakt interactie mogelijk met Outlook in Office 365. Gebruik deze connector om contact personen en agenda-items te maken, bewerken en bij te werken en e-mail te ontvangen, te verzenden en te beantwoorden.

Met Office 365 Outlook gaat u als volgt te werk:

* Bouw uw werk stroom met behulp van de e-mail-en agenda functies in Office 365. 
* Gebruik triggers om uw werk stroom te starten wanneer er een nieuwe e-mail is, wanneer een agenda-item wordt bijgewerkt en meer.
* Gebruik acties om een e-mail bericht te verzenden, een nieuwe agenda gebeurtenis te maken, en meer. Als er bijvoorbeeld een nieuw object in Sales Force (een trigger) is, stuurt u een e-mail bericht naar uw Office 365 Outlook (een actie). 

In dit artikel wordt beschreven hoe u de Office 365 Outlook-Connector in een logische app kunt gebruiken en worden ook de triggers en acties weer gegeven.

> [!NOTE]
> Deze versie van het artikel is van toepassing op Logic Apps algemene Beschik baarheid (GA).
> 
> 

Zie [Wat zijn logische apps](../logic-apps/logic-apps-overview.md) en [Maak een logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om meer te weten te komen over Logic apps.

## <a name="connect-to-office-365"></a>Verbinding maken met Office 365
Voordat uw logische app toegang kan krijgen tot een service, maakt u eerst een *verbinding* met de service. Een verbinding biedt connectiviteit tussen een logische app en een andere service. Als u bijvoorbeeld verbinding wilt maken met Office 365 Outlook, hebt u eerst een Office 365- *verbinding*nodig. Als u een verbinding wilt maken, voert u de referenties in die u normaal gebruikt voor toegang tot de service waarmee u verbinding wilt maken. Voer, met Office 365 Outlook, de referenties in voor uw Office 365-account om de verbinding te maken.

## <a name="create-the-connection"></a>De verbinding maken
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>Een trigger gebruiken
Een trigger is een gebeurtenis die kan worden gebruikt om de werk stroom te starten die is gedefinieerd in een logische app. Hiermee wordt de service ' Poll ' geactiveerd met een interval en frequentie die u wilt. Meer [informatie over triggers](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. In de logische app typt u ' Office 365 ' om een lijst met triggers op te halen:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. Selecteer **Office 365 Outlook-wanneer een aanstaande gebeurtenis binnenkort wordt gestart**. Als er al een verbinding bestaat, selecteert u een kalender in de vervolg keuzelijst.
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    Als u wordt gevraagd om u aan te melden, voert u de aanmeldings gegevens in om de verbinding te maken. [Maak de verbinding](connectors-create-api-office365-outlook.md#create-the-connection) in dit onderwerp voor een overzicht van de stappen. 
   
   > [!NOTE]
   > In dit voor beeld wordt de logische app uitgevoerd wanneer een agenda gebeurtenis wordt bijgewerkt. Als u de resultaten van deze trigger wilt zien, voegt u een actie toe waarmee u een SMS-bericht ontvangt. Voeg bijvoorbeeld de actie Twilio- *bericht verzenden* toe die u teksteert wanneer de agenda gebeurtenis in 15 minuten wordt gestart. 
   > 
   > 
3. Selecteer de knop **bewerken** en stel de **frequentie** -en **interval** waarden in. Als u bijvoorbeeld wilt dat de trigger om de 15 minuten vraagt, stelt u de **frequentie** in op **minuut**en stelt u het **interval** in op **15**. 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. **Sla** de wijzigingen op (in de linkerbovenhoek van de werk balk). Uw logische app wordt opgeslagen en kan automatisch worden ingeschakeld.

## <a name="use-an-action"></a>Een actie gebruiken
Een actie is een bewerking die wordt uitgevoerd door de werk stroom die is gedefinieerd in een logische app. [Meer informatie over acties](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Selecteer het plus teken. U ziet verschillende opties: **een actie toevoegen**, **een voor waarde toevoegen**of een van de **meer** opties.
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. Kies **een actie toevoegen**.
3. Typ in het tekstvak ' Office 365 ' om een lijst met alle beschik bare acties op te halen.
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. Kies in ons voor beeld **Office 365 Outlook-contact persoon maken**. Als er al een verbinding bestaat, kiest u de **map-id**, de **naam**en andere eigenschappen:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    Als u wordt gevraagd om de verbindings gegevens, voert u de gegevens in om de verbinding te maken. [De verbinding maken](connectors-create-api-office365-outlook.md#create-the-connection) in dit onderwerp beschrijft deze eigenschappen. 
   
   > [!NOTE]
   > In dit voor beeld maken we een nieuwe contact persoon in Office 365 Outlook. U kunt uitvoer van een andere trigger gebruiken om de contact persoon te maken. Voeg bijvoorbeeld het Sales Force toe *Wanneer een object wordt gemaakt* . Voeg vervolgens de actie Office 365 Outlook- *contact persoon maken* toe die gebruikmaakt van de Sales Force-velden voor het maken van de nieuwe contact persoon in Office 365. 
   > 
   > 
5. **Sla** de wijzigingen op (in de linkerbovenhoek van de werk balk). Uw logische app wordt opgeslagen en kan automatisch worden ingeschakeld.

## <a name="connector-specific-details"></a>Connector-specifieke Details

Bekijk de triggers en acties die zijn gedefinieerd in Swagger en Zie ook eventuele limieten in de details van de [connector](/connectors/office365connector/). 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)