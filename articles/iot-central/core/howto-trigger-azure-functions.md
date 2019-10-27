---
title: Azure Functions met webhooks in azure activeren IoT Central
description: Maak een functie-app die wordt uitgevoerd telkens wanneer een regel wordt geactiveerd in azure IoT Central.
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: a9590e5554956418859a07b43fb57724783343fe
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952847"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Azure Functions met webhooks in azure activeren IoT Central

*Dit onderwerp is van toepassing op bouwers en beheerders.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Gebruik Azure Functions om serverloze code uit te voeren op de webhook-uitvoer van IoT Central regels. U hoeft geen virtuele machine in te richten of een web-app te publiceren om Azure Functions te gebruiken, maar u kunt deze code serverloos uitvoeren. Gebruik Azure Functions om de nettolading van de webhook te transformeren voordat u deze naar de uiteindelijke bestemming verzendt, zoals een SQL database of Event Grid.

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="how-to-connect-azure-functions"></a>Verbinding maken Azure Functions

1. [Maak een nieuwe functie-app in de Azure Portal](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).

    ![Een nieuwe functie-app maken in de Azure Portal](media/howto-trigger-azure-functions/createfunction.png)

2. Vouw de functie-app uit en selecteer de **+-knop** naast functies. Als deze functie de eerste is in uw functie-app, selecteert u **in portal** als ontwikkel omgeving en selecteert u **door gaan**.

    ![Kies aangepaste functie in functie-app](media/howto-trigger-azure-functions/customfunction.png)

3. Kies **webhook + API** -sjabloon en selecteer **maken**. In dit onderwerp wordt de functie .NET gebaseerd op Azure gebruikt.

    ![Trigger voor generieke webhook selecteren](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. Selecteer in de nieuwe functie **</> functie-URL ophalen**, kopieer de waarde en sla deze op. U gebruikt deze waarde om de webhook te configureren.

    ![De URL van de functie ophalen](media/howto-trigger-azure-functions/getfunctionurl.png)

4. Ga in IoT Central naar de regel die u wilt verbinden met uw functie-app.

5. Voeg een webhook-actie toe. Voer een **weergave naam** in en plak de functie-URL die u eerder hebt gekopieerd naar **call back-URL**.

    ![Voer de functie-URL in het veld call back-URL in](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. Sla de regel op. Wanneer de regel wordt geactiveerd, roept de webhook nu de functie-app aan die moet worden uitgevoerd. In de functie-app kunt u **monitor** selecteren om de aanroep geschiedenis van de functie weer te geven. U kunt app Insights of de klassieke weer gave gebruiken om de geschiedenis te bekijken.

    ![De aanroep geschiedenis van de functie bewaken](media/howto-trigger-azure-functions/monitorfunction.PNG)

Ga voor meer informatie naar het Azure Functions artikel over het [maken van een functie die wordt geactiveerd door een algemene webhook](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function).

## <a name="next-steps"></a>Volgende stappen
Nu u hebt geleerd hoe u webhooks kunt instellen en gebruiken, is de voorgestelde volgende stap het [maken van werk stromen in Microsoft flow](howto-add-microsoft-flow.md)verkennen.
