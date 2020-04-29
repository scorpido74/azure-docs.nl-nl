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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77111192"
---
Met [bericht routering](../articles/iot-hub/iot-hub-devguide-messages-d2c.md) kunnen telemetriegegevens van uw IOT-apparaten worden verzonden naar ingebouwde Event hub-compatibele eind punten of aangepaste eind punten, zoals BLOB storage, service bus wachtrijen, service bus onderwerpen en Event hubs. Als u aangepaste bericht routering wilt configureren, maakt u [routerings query's](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md) om de route aan te passen die overeenkomt met een bepaalde voor waarde. Zodra u deze hebt ingesteld, worden de inkomende gegevens automatisch door de IoT Hub doorgestuurd naar de eindpunten. Als een bericht niet overeenkomt met een van de gedefinieerde routerings query's, wordt het doorgestuurd naar het standaard eindpunt.

In deze tweedelige zelf studie leert u hoe u deze aangepaste routerings query's kunt instellen en gebruiken met IoT Hub. U stuurt berichten van een IoT-apparaat naar een van de verschillende eind punten, met inbegrip van Blob-opslag en een Service Bus wachtrij. Berichten naar de Service Bus wachtrij worden door een logische app opgehaald en via e-mail verzonden. Berichten waarvoor geen aangepaste bericht routering is gedefinieerd, worden verzonden naar het standaard eindpunt, vervolgens opgehaald door Azure Stream Analytics en weer gegeven in een Power BI visualisatie.

Als u de onderdelen 1 en 2 van deze zelf studie wilt volt ooien, voert u de volgende taken uit:

**Deel I: resources maken, bericht routering instellen**
> [!div class="checklist"]
> * Maak de resources--een IoT-hub, een opslag account, een Service Bus wachtrij en een gesimuleerd apparaat. U kunt dit doen met behulp van de Azure Portal, een Azure Resource Manager sjabloon, de Azure CLI of Azure PowerShell.
> * Configureer de eind punten en bericht routes in IoT Hub voor het opslag account en de Service Bus wachtrij.

**Deel II: berichten verzenden naar de hub, gerouteerde resultaten weer geven**
> [!div class="checklist"]
> * Maak een logische app die wordt geactiveerd en een e-mailbericht verzendt wanneer een bericht wordt toegevoegd aan de Service Bus-wachtrij.
> * Download een app die een IoT-apparaat aanzet tot het verzenden van berichten naar de hub voor de verschillende routeringsopties en voer deze uit.
> * Maak een Power BI-visualisatie voor gegevens die naar het standaardeindpunt worden verzonden.
> * Bekijk de resultaten...
> * .. .in de Service Bus-wachtrij en e-mailberichten.
> * ...in het opslagaccount.
> * ...in de Power BI-visualisatie.

## <a name="prerequisites"></a>Vereisten

* Voor deel 1 van deze zelf studie:
  - U hebt een abonnement op Azure nodig. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

* Voor deel 2 van deze zelf studie:
  - U moet deel 1 van deze zelf studie hebben voltooid. de resources zijn nog steeds beschikbaar.
  - Installeer [Visual Studio](https://www.visualstudio.com/).
  - Toegang hebben tot een Power BI-account om de stream Analytics van het standaard eindpunt te analyseren. ([Probeer Power BI gratis uit](https://app.powerbi.com/signupredirect?pbi_source=web).)
  - Een Office 365-account hebben voor het verzenden van e-mail berichten.
  - Zorg ervoor dat poort 8883 is geopend in uw firewall. Het voor beeld in deze zelf studie maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs-en educatieve netwerk omgevingen. Zie [verbinding maken met IOT hub (MQTT)](../articles/iot-hub/iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]
