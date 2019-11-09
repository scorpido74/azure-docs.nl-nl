---
title: Een Azure IoT Central-toepassing maken | Microsoft Docs
description: Maak een nieuwe Azure IoT Central-toepassing. Maak een proefversie of betalen per gebruik-toepassing met behulp van een toepassingssjabloon.
author: lmasieri
ms.author: lmasieri
ms.date: 10/24/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: c639cb059d773042b7f45160dea18bfc2130cae9
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73893807"
---
# <a name="create-an-azure-iot-central-application-preview-features"></a>Een Azure IoT Central-toepassing maken (preview-functies)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

In deze Quick start ziet u hoe u een Azure IoT Central-toepassing kunt maken die preview-functies, zoals IoT Plug en Play, bevat.

> [!WARNING]
> De IoT Plug en Play-mogelijkheden in azure IoT Central zijn momenteel beschikbaar als open bare preview. Gebruik geen IoT Plug en Play ingeschakelde IoT Central toepassing voor werk belastingen voor de productie. Voor productie omgevingen wordt een IoT Central-toepassing gebruikt die is gemaakt op basis van een huidige, algemeen beschik bare, toepassings sjabloon.

## <a name="create-an-application"></a>Een app maken

Ga naar de [Azure IOT Central build](https://aka.ms/iotcentral) -site. Meld u vervolgens aan met een persoonlijk, werk-of school account van micro soft.

U maakt een nieuwe toepassing in de lijst met branchespecifieke IoT Central sjablonen waarmee u snel aan de slag kunt gaan, of helemaal met de sjabloon voor **aangepaste apps** .

![Een toepassings pagina maken in azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate-templates-pnp.png)

Een nieuwe Azure IoT Central-toepassing maken:

1. Als u een nieuwe Azure IoT Central-toepassing wilt maken op basis van een *branche sjabloon*, selecteert u een toepassings sjabloon in de lijst met beschik bare sjablonen onder een van de branches. U kunt ook beginnen met het kiezen van een *aangepaste app*.
1. In azure IoT Central wordt automatisch een **toepassings naam** voorgesteld op basis van de toepassings sjabloon die u hebt geselecteerd. U kunt deze naam gebruiken of uw eigen beschrijvende toepassings naam invoeren.
1. Azure IoT Central genereert ook een uniek voor voegsel voor de **toepassings-URL** , op basis van de naam van de toepassing. U gebruikt deze URL om toegang te krijgen tot uw toepassing. Als u wilt, kunt u dit URL-voor voegsel wijzigen in iets eenvoudiger te onthouden.

    ![Een toepassings pagina maken in azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate-industry-pnp.png)

    > [!NOTE]
    > Als u de aangepaste app-sjabloon gebruikt, ziet u een vervolg keuzelijst voor de **toepassings sjabloon** . Hier kunt u scha kelen tussen preview-en algemeen beschik bare sjablonen. Mogelijk ziet u ook andere sjablonen die beschikbaar zijn gesteld voor uw organisatie.

1. Kies of u deze toepassing wilt maken met een gratis proef versie van 7 dagen of gebruik een abonnement voor betalen per gebruik.
    - **Proef** toepassingen zijn zeven dagen gratis en ondersteunen Maxi maal vijf apparaten. Voordat ze verlopen, kunnen ze op elk gewenst moment worden geconverteerd naar betalen per gebruik. Als u een proef toepassing maakt, moet u uw contact gegevens invoeren en kiezen of u informatie en tips van micro soft wilt ontvangen.
    - **Betalen naar gebruik** -toepassingen worden per apparaat in rekening gebracht, waarbij de eerste twee apparaten gratis zijn. Meer informatie over [IOT Central prijzen](https://aka.ms/iotcentral-pricing). Als u een betalen per gebruik-toepassing maakt, moet u uw *adres lijst*, het *Azure-abonnement*en de *regio*selecteren:
        - *Directory* is de Azure Active Directory (Aad) waarin u uw toepassing maakt. Een Azure AD bevat gebruikers identiteiten, referenties en andere informatie over de organisatie. Als u geen Azure AD hebt, wordt er een voor u gemaakt wanneer u een Azure-abonnement maakt.
        - Als u een *Azure-abonnement* hebt, kunt u instanties van Azure-services maken. IoT Central resources in uw abonnement. Als u geen Azure-abonnement hebt, kunt u er een maken via de [Azure-aanmeldingspagina](https://aka.ms/createazuresubscription). Nadat u het Azure-abonnement hebt gemaakt, gaat u terug naar de pagina **een toepassing maken** . Uw nieuwe abonnement wordt weer gegeven in de vervolg keuzelijst van het **Azure-abonnement** .
        - *Regio* is de fysieke locatie waar de gegevens van uw apparaten worden opgeslagen. Normaal gesp roken kiest u de regio die zich het dichtst in de buurt bevindt van uw apparaten om optimale prestaties te krijgen en om ervoor te zorgen dat de gegevens soevereiniteit wordt nageleefd. Wanneer u een regio hebt gekozen, kunt u uw toepassing niet meer naar een andere regio verplaatsen.

        > [!NOTE]
        > Tijdens de open bare preview zijn de enige beschik bare regio's voor **Preview-toepassingen** **Europa-Noord** en **Centraal VS**.

1. Bekijk de voor waarden en selecteer **maken** onder aan de pagina.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een IoT Central-toepassing gemaakt. Dit is de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Een gesimuleerd apparaat toevoegen aan uw IoT Central-toepassing](./quick-create-pnp-device.md)
