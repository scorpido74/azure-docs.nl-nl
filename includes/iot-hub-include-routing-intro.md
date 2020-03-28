---
title: bestand opnemen
description: bestand opnemen
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 9a20dca71727e83db98c4c97567949bd127fc7fb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77111192"
---
[Berichtroutering](../articles/iot-hub/iot-hub-devguide-messages-d2c.md) maakt het mogelijk telemetriegegevens van uw IoT-apparaten te verzenden naar ingebouwde Event Hub-compatibele eindpunten of aangepaste eindpunten zoals blob-opslag, servicebuswachtrijen, servicebusonderwerpen en gebeurtenishubs. Als u de aangepaste berichtroutering wilt configureren, maakt u [routeringsquery's](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md) om de route aan te passen die overeenkomt met een bepaalde voorwaarde. Zodra u deze hebt ingesteld, worden de inkomende gegevens automatisch door de IoT Hub doorgestuurd naar de eindpunten. Als een bericht niet overeenkomt met een van de gedefinieerde routeringsquery's, wordt het doorgestuurd naar het standaardeindpunt.

In deze 2-delige zelfstudie leert u hoe u deze aangepaste routeringsquery's instellen en gebruiken met IoT Hub. U stuurt berichten van een IoT-apparaat naar een van de meerdere eindpunten, waaronder blobopslag en een wachtrij voor servicebussen. Berichten in de wachtrij van de servicebus worden opgehaald door een Logic App en via e-mail verzonden. Berichten die geen aangepaste berichtroutering hebben gedefinieerd, worden verzonden naar het standaardeindpunt, vervolgens opgepikt door Azure Stream Analytics en weergegeven in een Power BI-visualisatie.

Als u de delen 1 en 2 van deze zelfstudie wilt voltooien, voert u de volgende taken uit:

**Deel I: Resources maken, berichtroutering instellen**
> [!div class="checklist"]
> * Maak de resources: een IoT-hub, een opslagaccount, een servicebuswachtrij en een gesimuleerd apparaat. Dit kan met behulp van de Azure-portal, een Azure Resource Manager-sjabloon, de Azure CLI of Azure PowerShell.
> * Configureer de eindpunten en berichtroutes in IoT Hub voor het opslagaccount en de wachtrij voor servicebussen.

**Deel II: Berichten verzenden naar de hub, routeresultaten bekijken**
> [!div class="checklist"]
> * Maak een logische app die wordt geactiveerd en een e-mailbericht verzendt wanneer een bericht wordt toegevoegd aan de Service Bus-wachtrij.
> * Download een app die een IoT-apparaat aanzet tot het verzenden van berichten naar de hub voor de verschillende routeringsopties en voer deze uit.
> * Maak een Power BI-visualisatie voor gegevens die naar het standaardeindpunt worden verzonden.
> * Bekijk de resultaten...
> * .. .in de Service Bus-wachtrij en e-mailberichten.
> * ...in het opslagaccount.
> * ...in de Power BI-visualisatie.

## <a name="prerequisites"></a>Vereisten

* Voor deel 1 van deze tutorial:
  - U hebt een abonnement op Azure nodig. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

* Voor deel 2 van deze tutorial:
  - U moet deel 1 van deze zelfstudie hebben voltooid en de resources nog beschikbaar hebben.
  - Installeer [Visual Studio](https://www.visualstudio.com/).
  - Heb toegang tot een Power BI-account om de streamanalyses van het standaardeindpunt te analyseren. ([Probeer Power BI gratis uit](https://app.powerbi.com/signupredirect?pbi_source=web).)
  - Heb een Office 365-account voor het verzenden van e-mails met meldingen.
  - Zorg ervoor dat poort 8883 is geopend in uw firewall. Het voorbeeld in deze zelfstudie maakt gebruik van mqtt-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](../articles/iot-hub/iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]
