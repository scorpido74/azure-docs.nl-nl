---
title: Bestanden openen en beheren in Microsoft OneDrive
description: Bestanden uploaden en beheren in OneDrive door geautomatiseerde werkstromen te maken in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: edfbf090c3409d583cda6fd2c9957c37be5dfb7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75378429"
---
# <a name="access-and-manage-files-in-onedrive-connector-by-using-azure-logic-apps"></a>Bestanden openen en beheren in OneDrive-connector met Azure Logic Apps

Met [Azure Logic Apps](../logic-apps/logic-apps-overview.md) en de [OneDrive-connector](/connectors/onedriveconnector/)u geautomatiseerde taken en werkstromen maken om uw bestanden te beheren, waaronder uploaden, bestanden verwijderen en meer. Met OneDrive u de volgende taken uitvoeren:

* Bouw uw werkstroom op door bestanden op te slaan in OneDrive of bestaande bestanden bij te werken in OneDrive. 
* Gebruik triggers om uw werkstroom te starten wanneer een bestand wordt gemaakt of bijgewerkt in uw OneDrive.
* Gebruik acties om een bestand te maken, een bestand te verwijderen en meer. Wanneer bijvoorbeeld een nieuwe Office 365-e-mail wordt ontvangen met een bijlage (een trigger), maakt u een nieuw bestand in OneDrive (een actie).

In dit artikel ziet u hoe u de OneDrive-connector in een logische app gebruikt en worden ook de triggers en acties weergegeven.

Zie Wat zijn logische [apps](../logic-apps/logic-apps-overview.md) en [maak een logische app voor](../logic-apps/quickstart-create-first-logic-app-workflow.md)meer informatie over Logische Apps.

## <a name="connect-to-onedrive"></a>Verbinden met OneDrive

Voordat uw logische app toegang heeft tot een service, maakt u eerst een *verbinding met* de service. Een verbinding biedt connectiviteit tussen een logische app en een andere service. Als u bijvoorbeeld verbinding wilt maken met OneDrive, hebt u eerst een *OneDrive-verbinding*nodig. Als u een verbinding wilt maken, voert u de referenties in waarmee u normaal gesproken toegang wilt krijgen tot de service waarmee u verbinding wilt maken. Voer met OneDrive de referenties in van uw OneDrive-account om de verbinding te maken.

### <a name="create-the-connection"></a>De verbinding maken

[!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]

## <a name="use-a-trigger"></a>Een trigger gebruiken

Een trigger is een gebeurtenis die kan worden gebruikt om de werkstroom te starten die is gedefinieerd in een logische app. Hiermee wordt de service 'poll' geactiveerd met een gewenste interval en frequentie. [Meer informatie over triggers](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Typ in de Logic `onedrive` App Designer een lijst met de triggers:  

   ![](./media/connectors-create-api-onedrive/onedrive-1.png)

2. Selecteer **Wanneer een bestand wordt gewijzigd**. Als er al een verbinding bestaat, selecteert u de knop Kiezer weergeven om een map te selecteren.

   ![](./media/connectors-create-api-onedrive/sample-folder.png)

   Als u wordt gevraagd zich aan te melden, voert u de aanmeldingsgegevens in om de verbinding te maken. De verbinding in dit artikel [maken,](connectors-create-api-onedrive.md#create-the-connection) bevat de stappen.

   In dit voorbeeld wordt de logische app uitgevoerd wanneer een bestand in de door u gekozen map wordt bijgewerkt. Als u de resultaten van deze trigger wilt zien, voegt u een andere actie toe die u een e-mail stuurt. Voeg bijvoorbeeld de Office 365 Outlook *Toe Stuur een e-mailactie* toe die u e-mails stuurt wanneer een bestand wordt bijgewerkt.

3. Selecteer de knop **Bewerken** en stel de **waarden Frequentie** en **Interval** in. Als u bijvoorbeeld wilt dat de trigger elke 15 minuten wordt gepeild, stelt u de **frequentie** in op **Minute**en stelt u het **interval** in op **15**. 

   ![](./media/connectors-create-api-onedrive/trigger-properties.png)

4. **Sla** de wijzigingen op (linkerbovenhoek van de werkbalk). Uw logische app wordt opgeslagen en kan automatisch worden ingeschakeld.

## <a name="use-an-action"></a>Een actie gebruiken

Een actie is een bewerking die wordt uitgevoerd door de werkstroom die is gedefinieerd in een logische app. [Meer informatie over acties](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Selecteer het plusteken. U ziet verschillende opties: **Een actie toevoegen**, Een voorwaarde **toevoegen**of een van de opties **Meer.**

   ![](./media/connectors-create-api-onedrive/add-action.png)

2. Kies **Een actie toevoegen**.

3. Typ in het `onedrive` zoekvak een lijst met alle beschikbare acties.

   ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 

4. Kies in ons voorbeeld **OneDrive - Bestand maken**. Als er al een verbinding bestaat, selecteert u het **mappad** om het bestand te plaatsen, voert u de **bestandsnaam**in en kiest u de **gewenste bestandsinhoud:**  

   ![](./media/connectors-create-api-onedrive/sample-action.png)

   Als u wordt gevraagd om de verbindingsgegevens, voert u de details in om de verbinding te [maken zoals beschreven](#create-the-connection) in dit onderwerp.

   In dit voorbeeld maakt u een nieuw bestand in een OneDrive-map. U uitvoer van een andere trigger gebruiken om het OneDrive-bestand te maken. Voeg bijvoorbeeld de Office 365 Outlook toe *wanneer een nieuwe e-mail wordt geactiveerd.* Voeg vervolgens de *bestandsactie Voor* OneDrive maken toe waarmee de velden Bijlagen en Inhoudstype in een ForEach worden gebruikt om het nieuwe bestand in OneDrive te maken.

   ![](./media/connectors-create-api-onedrive/foreach-action.png)

5. **Sla** de wijzigingen op (linkerbovenhoek van de werkbalk). Uw logische app wordt opgeslagen en kan automatisch worden ingeschakeld.

## <a name="connector-specific-details"></a>Connector-specifieke details

Bekijk alle triggers en acties die in de branie zijn gedefinieerd en zie ook eventuele limieten in de [connectordetails.](/connectors/onedriveconnector/)

## <a name="next-steps"></a>Volgende stappen

* [Connectors voor Azure Logic Apps](apis-list.md)