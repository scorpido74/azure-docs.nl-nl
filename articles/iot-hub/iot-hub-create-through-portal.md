---
title: De Azure-portal gebruiken om een IoT-hub te maken | Microsoft Documenten
description: Azure IoT-hubs maken, beheren en verwijderen via de Azure-portal. Bevat informatie over prijsniveaus, schalen, beveiliging en berichtenconfiguratie.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: robinsh
ms.openlocfilehash: c43c142b22709d42416b2dd14dfc78812970916a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284731"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Een IoT-hub maken met de Azure-portal

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

In dit artikel wordt beschreven hoe u IoT-hubs maakt en beheert met behulp van de [Azure-portal.](https://portal.azure.com)

Als u de stappen in deze zelfstudie wilt gebruiken, hebt u een Azure-abonnement nodig. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="change-the-settings-of-the-iot-hub"></a>De instellingen van de IoT-hub wijzigen

U de instellingen van een bestaande IoT-hub wijzigen nadat deze is gemaakt vanuit het deelvenster IoT-hub.

![Schermafbeelding van de instellingen voor de IoT-hub](./media/iot-hub-create-through-portal/iot-hub-settings-panel.png)

Hier volgen enkele eigenschappen die u instellen voor een IoT-hub:

**Prijzen en schaal:** U deze eigenschap gebruiken om naar een andere laag te migreren of het aantal IoT-hub-eenheden in te stellen. 

**Operations monitoring:** Schakel de verschillende monitoringcategorieën in of uit, zoals logboekregistratie voor gebeurtenissen met betrekking tot device-to-cloud-berichten of cloud-to-device-berichten.

**IP-filter:** geef een reeks IP-adressen op die worden geaccepteerd of afgewezen door de IoT-hub.

**Eigenschappen:** hiermee wordt de lijst met eigenschappen weergegeven die u elders kopiëren en gebruiken, zoals de bron-id, resourcegroep, locatie, enzovoort.

### <a name="shared-access-policies"></a>Gedeeld toegangsbeleid

U ook de lijst met beleid voor gedeelde toegang weergeven of wijzigen door te klikken op **Beleid voor gedeelde toegang** in de sectie **Instellingen.** Met dit beleid worden de machtigingen gedefinieerd voor apparaten en services om verbinding te maken met IoT Hub. 

Klik **op Toevoegen** om het **beleidsblad Voor gedeelde toegang toevoegen** te openen.  U de nieuwe beleidsnaam en de machtigingen invoeren die u aan dit beleid wilt koppelen, zoals in de volgende afbeelding wordt weergegeven:

![Schermafbeelding van het toevoegen van een beleid voor gedeelde toegang](./media/iot-hub-create-through-portal/iot-hub-add-shared-access-policy.png)

* Het **register lezen** en register **schrijven** beleid verlenen lees-en schrijftoegangsrechten aan het identiteitsregister. Deze machtigingen worden gebruikt door back-end cloudservices om apparaatidentiteiten te beheren. Als u de schrijfoptie kiest, kiest u automatisch de leesoptie.

* Met het **serviceconnect-beleid** wordt toestemming verleend voor toegang tot serviceeindpunten. Deze toestemming wordt gebruikt door back-end cloudservices om berichten van apparaten te verzenden en te ontvangen, evenals om dubbele gegevens van apparaten en modules bij te werken en te lezen.

* Met het beleid **Apparaatverbinding** worden machtigingen verleend voor het verzenden en ontvangen van berichten met behulp van de eindpunten aan de ie-kant van het IoT Hub-apparaat. Deze toestemming wordt door apparaten gebruikt om berichten te verzenden en te ontvangen vanaf een IoT-hub, dubbele gegevens voor apparaten bij te werken en te lezen en bestandsuploads uit te voeren.

Klik **op Maken** om dit nieuw gemaakte beleid toe te voegen aan de bestaande lijst.

Zie [IoT Hub-machtigingen](./iot-hub-devguide-security.md#iot-hub-permissions)voor meer gedetailleerde informatie over de toegang die wordt verleend door specifieke machtigingen.

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="message-routing-for-an-iot-hub"></a>Berichtroutering voor een IoT-hub

Klik op **Berichtroutering** onder **Berichten** om het deelvenster Berichtroutering te bekijken, waarin u routes en aangepaste eindpunten voor de hub definieert. [Met berichtroutering](iot-hub-devguide-messages-d2c.md) u beheren hoe gegevens van uw apparaten naar uw eindpunten worden verzonden. De eerste stap is het toevoegen van een nieuwe route. Vervolgens u een bestaand eindpunt aan de route toevoegen of een nieuw een van de ondersteunde typen maken, zoals blobopslag. 

![Deelvenster Berichtroutering](./media/iot-hub-create-through-portal/iot-hub-message-routing.png)

### <a name="routes"></a>Routes

Routes is het eerste tabblad in het deelvenster Berichtroutering. Als u een nieuwe route wilt toevoegen, klikt u op +**Toevoegen**. U ziet het volgende scherm. 

![Schermafbeelding van het toevoegen van een nieuwe route](./media/iot-hub-create-through-portal/iot-hub-add-route-storage-endpoint.png)

Noem je hub. De naam moet uniek zijn in de lijst met routes voor die hub. 

Voor **Eindpunt**u er een selecteren in de vervolgkeuzelijst of een nieuwe toevoegen. In dit voorbeeld zijn al een opslagaccount en container beschikbaar. Als u ze als eindpunt wilt toevoegen, klikt u op +**Toevoegen** naast de vervolgkeuzelijst Endpoint en selecteert u **Blob-opslag**. In het volgende scherm ziet u waar het opslagaccount en de container zijn opgegeven.

![Schermafbeelding van het toevoegen van een opslageindpunt voor de routeringsregel](./media/iot-hub-create-through-portal/iot-hub-routing-add-storage-endpoint.png)

Klik **op Een container kiezen** om het opslagaccount en de container te selecteren. Wanneer u deze velden hebt geselecteerd, wordt deze weer weergegeven in het deelvenster Eindpunt. Gebruik de standaardinstellingen voor de rest van de velden en **Maak** om het eindpunt voor het opslagaccount te maken en toe te voegen aan de routeringsregels.

Selecteer Apparaattelemetrieberichten voor **gegevensbron.** 

Voeg vervolgens een routeringsquery toe. In dit voorbeeld worden de berichten met `level` een toepassingseigenschap `critical` die wordt aangeroepen met een waarde die gelijk is aan, doorgestuurd naar het opslagaccount.

![Schermafbeelding van het opslaan van een nieuwe routeringsregel](./media/iot-hub-create-through-portal/iot-hub-add-route.png)

Klik **op Opslaan** om de routeringsregel op te slaan. U keert terug naar het deelvenster Berichtroutering en uw nieuwe routeringsregel wordt weergegeven.

### <a name="custom-endpoints"></a>Aangepaste eindpunten

Klik op het tabblad **Aangepaste eindpunten.** U ziet alle aangepaste eindpunten die al zijn gemaakt. Vanaf hier u nieuwe eindpunten toevoegen of bestaande eindpunten verwijderen. 

> [!NOTE]
> Als u een route verwijdert, worden de eindpunten die aan die route zijn toegewezen, niet verwijderd. Als u een eindpunt wilt verwijderen, klikt u op het tabblad Aangepaste eindpunten, selecteert u het eindpunt dat u wilt verwijderen en klikt u op Verwijderen.
>

U meer lezen over aangepaste eindpunten in [Referentie - IoT-hubeindpunten](iot-hub-devguide-endpoints.md).

U maximaal 10 aangepaste eindpunten definiëren voor een IoT-hub. 

Zie [Berichtroutering met IoT Hub](tutorial-routing.md)voor een volledig voorbeeld van het gebruik van aangepaste eindpunten met routering.

## <a name="find-a-specific-iot-hub"></a>Een specifieke IoT-hub zoeken

Hier volgen twee manieren om een specifieke IoT-hub in uw abonnement te vinden:

1. Als u de resourcegroep kent waartoe de IoT-hub behoort, klikt u op **Resourcegroepen**en selecteert u de brongroep in de lijst. Het scherm van de brongroep toont alle bronnen in die groep, inclusief de IoT-hubs. Klik op de hub waar u naar op zoek bent.

2. Klik op **Alle resources**. In het deelvenster **Alle resources** is er een `All types`vervolgkeuzelijst die standaard wordt weergegeven in . Klik op de vervolgkeuzelijst `Select all`en schakel het selectievakje uit. Zoek `IoT Hub` en controleer het. Klik op het keuzelijstje om het te sluiten en de items worden gefilterd, waarop alleen uw IoT-hubs worden weergegeven.

## <a name="delete-the-iot-hub"></a>De IoT-hub verwijderen

Als u een Iot-hub wilt verwijderen, zoekt u de IoT-hub die u wilt verwijderen en klikt u op de knop **Verwijderen** onder de naam van de IoT-hub.

## <a name="next-steps"></a>Volgende stappen

Volg deze koppelingen voor meer informatie over het beheer van Azure IoT Hub:

* [Berichtroutering met IoT Hub](tutorial-routing.md)
* [IoT Hub-statistieken](iot-hub-metrics.md)
* [Controle van bewerkingen](iot-hub-operations-monitoring.md)