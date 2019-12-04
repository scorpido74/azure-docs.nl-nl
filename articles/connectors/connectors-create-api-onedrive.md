---
title: Verbinding maken met OneDrive
description: Bestanden uploaden en beheren met REST-Api's van OneDrive en Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: 93528e257ab45644a79c58fbf600dca10317eb0b
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789675"
---
# <a name="get-started-with-the-onedrive-connector"></a>Aan de slag met de OneDrive-connector

Maak verbinding met OneDrive om uw bestanden te beheren, met inbegrip van het uploaden, ophalen, verwijderen van bestanden en meer. Met OneDrive kunt u de volgende taken uitvoeren:

* Bouw uw werk stroom door bestanden op te slaan in OneDrive of bestaande bestanden in OneDrive bij te werken. 
* Gebruik triggers om uw werk stroom te starten wanneer een bestand wordt gemaakt of bijgewerkt in uw OneDrive.
* Gebruik acties voor het maken van een bestand, het verwijderen van een bestand en meer. Als er bijvoorbeeld een nieuwe e-mail van Office 365 wordt ontvangen met een bijlage (een trigger), maakt u een nieuw bestand in OneDrive (een actie).

Dit artikel laat u zien hoe u de OneDrive-connector kunt gebruiken in een logische app, en ook een lijst met de triggers en acties.

Zie [Wat zijn logische apps](../logic-apps/logic-apps-overview.md) en [Maak een logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om meer te weten te komen over Logic apps.

## <a name="connect-to-onedrive"></a>Verbinding maken met OneDrive

Voordat uw logische app toegang kan krijgen tot een service, maakt u eerst een *verbinding* met de service. Een verbinding biedt connectiviteit tussen een logische app en een andere service. Als u bijvoorbeeld verbinding wilt maken met OneDrive, hebt u eerst een OneDrive- *verbinding*nodig. Als u een verbinding wilt maken, voert u de referenties in die u normaal gebruikt voor toegang tot de service waarmee u verbinding wilt maken. In OneDrive voert u de referenties voor uw OneDrive-account in om de verbinding te maken.

### <a name="create-the-connection"></a>De verbinding maken

[!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]

## <a name="use-a-trigger"></a>Een trigger gebruiken

Een trigger is een gebeurtenis die kan worden gebruikt om de werk stroom te starten die is gedefinieerd in een logische app. Hiermee wordt de service ' Poll ' geactiveerd met een interval en frequentie die u wilt. Meer [informatie over triggers](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. In de logische app typt u ' onedrive ' om een lijst met triggers op te halen:  

   ![](./media/connectors-create-api-onedrive/onedrive-1.png)

2. Selecteren **Wanneer een bestand wordt gewijzigd**. Als er al een verbinding bestaat, selecteert u de knop kiezer weer geven om een map te selecteren.

   ![](./media/connectors-create-api-onedrive/sample-folder.png)

   Als u wordt gevraagd om u aan te melden, voert u de aanmeldings gegevens in om de verbinding te maken. [Maak de verbinding](connectors-create-api-onedrive.md#create-the-connection) in dit artikel voor een overzicht van de stappen.

   In dit voor beeld wordt de logische app uitgevoerd wanneer een bestand in de map die u kiest, wordt bijgewerkt. Als u de resultaten van deze trigger wilt zien, voegt u een andere actie toe waarmee u een e-mail ontvangt. Voeg bijvoorbeeld de actie Office 365 Outlook een e-mail verzenden toe die u e *-mailt* wanneer een bestand wordt bijgewerkt.

3. Selecteer de knop **bewerken** en stel de **frequentie** -en **interval** waarden in. Als u bijvoorbeeld wilt dat de trigger om de 15 minuten vraagt, stelt u de **frequentie** in op **minuut**en stelt u het **interval** in op **15**. 

   ![](./media/connectors-create-api-onedrive/trigger-properties.png)

4. **Sla** de wijzigingen op (in de linkerbovenhoek van de werk balk). Uw logische app wordt opgeslagen en kan automatisch worden ingeschakeld.

## <a name="use-an-action"></a>Een actie gebruiken

Een actie is een bewerking die wordt uitgevoerd door de werk stroom die is gedefinieerd in een logische app. [Meer informatie over acties](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Selecteer het plus teken. U ziet verschillende opties: **een actie toevoegen**, **een voor waarde toevoegen**of een van de **meer** opties.

   ![](./media/connectors-create-api-onedrive/add-action.png)

2. Kies **een actie toevoegen**.

3. Typ in het tekstvak ' onedrive ' om een lijst met alle beschik bare acties weer te geven.

   ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 

4. In ons voor beeld kiest u **OneDrive-bestand maken**. Als er al een verbinding bestaat, **selecteert u het mappad om het** bestand te plaatsen, voert u de **Bestands naam**in en kiest u de gewenste **Bestands inhoud** :  

   ![](./media/connectors-create-api-onedrive/sample-action.png)

   Als u wordt gevraagd om de verbindings gegevens, voert u de gegevens in om [de verbinding te maken, zoals wordt beschreven](#create-the-connection) in dit onderwerp.

   In dit voor beeld maakt u een nieuw bestand in een OneDrive-map. U kunt de uitvoer van een andere trigger gebruiken om het OneDrive-bestand te maken. Voeg bijvoorbeeld Office 365 Outlook toe *wanneer er een nieuwe e-mail binnenkomt* . Voeg vervolgens de actie OneDrive *maken bestand* toe die gebruikmaakt van de velden bijlagen en inhouds typen in een foreach om het nieuwe bestand in OneDrive te maken.

   ![](./media/connectors-create-api-onedrive/foreach-action.png)

5. **Sla** de wijzigingen op (in de linkerbovenhoek van de werk balk). Uw logische app wordt opgeslagen en kan automatisch worden ingeschakeld.

## <a name="connector-specific-details"></a>Connector-specifieke Details

Bekijk de triggers en acties die zijn gedefinieerd in Swagger en Zie ook eventuele limieten in de details van de [connector](/connectors/onedriveconnector/).

## <a name="next-steps"></a>Volgende stappen

* [Connectors voor Azure Logic Apps](apis-list.md)