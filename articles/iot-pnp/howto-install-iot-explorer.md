---
title: Azure IoT Explorer installeren en gebruiken | Microsoft Docs
description: Installeer het hulp programma Azure IoT Explorer en gebruik dit om te communiceren met de IoT-Plug en Play preview-apparaten die zijn verbonden met mijn IoT hub.
author: miagdp
ms.author: miag
ms.date: 07/02/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: c7d8b946fc587d58f13e16c1d7d806390e712104
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879550"
---
# <a name="install-and-use-azure-iot-explorer"></a>Azure IoT Explorer installeren en gebruiken

Azure IoT Explorer is een grafisch hulp programma voor interactie met en het testen van uw IoT Plug en Play preview-apparaten. Nadat u het hulp programma hebt geïnstalleerd op uw lokale computer, kunt u het gebruiken om verbinding te maken met een apparaat. U kunt het hulp programma gebruiken om de telemetrie weer te geven die het apparaat verzendt, te werken met apparaateigenschappen en opdrachten aan te roepen.

In dit artikel wordt beschreven hoe u:

- Installeer en configureer het Azure IoT Explorer-hulp programma.
- Gebruik het hulp programma om te communiceren met en uw apparaten te testen.

## <a name="prerequisites"></a>Vereisten

Als u het hulp programma Azure IoT Explorer wilt gebruiken, hebt u het volgende nodig:

- Een Azure IoT hub. Er zijn veel manieren om een IoT-hub toe te voegen aan uw Azure-abonnement, zoals [het maken van een IOT-hub met behulp van de Azure cli](../iot-hub/iot-hub-create-using-cli.md). U hebt de IoT hub-connection string nodig om het hulp programma Azure IoT Explorer uit te voeren. Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
- Een apparaat dat is geregistreerd in uw IoT-hub. U kunt de volgende Azure CLI-opdracht gebruiken om een apparaat te registreren. Zorg ervoor dat u de `{YourIoTHubName}` `{YourDeviceID}` tijdelijke aanduidingen vervangt door de waarden:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>Azure IoT Explorer installeren

Ga naar [Azure IOT Explorer](https://github.com/Azure/azure-iot-explorer/releases) en vouw de lijst met assets uit voor de meest recente release. Down load en installeer de meest recente versie van de toepassing.

## <a name="use-azure-iot-explorer"></a>Azure IoT Explorer gebruiken

Voor een apparaat kunt u een verbinding maken met uw eigen apparaat of een van de gesimuleerde voor beelden van apparaten gebruiken. Volg [deze instructies](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples) om het gesimuleerde voor beeld van het apparaat uit te voeren.

### <a name="connect-to-your-hub"></a>Verbinding maken met uw hub

De eerste keer dat u Azure IoT Explorer uitvoert, wordt u gevraagd om de connection string van de IoT-hub. Nadat u de connection string hebt toegevoegd, selecteert u **verbinding maken**. U kunt de instellingen van het hulp programma gebruiken om over te scha kelen naar een andere IoT-hub door de connection string bij te werken.

De model definitie voor een IoT-Plug en Play apparaat wordt opgeslagen in de open bare opslag plaats, in een bedrijfs opslagplaats of op het aangesloten apparaat. Het hulp programma zoekt standaard naar uw model definitie in de open bare model opslagplaats en op het aangesloten apparaat. U kunt bronnen toevoegen en verwijderen of de prioriteit van de bronnen in **instellingen**configureren:

Een bron toevoegen:

1. Ga naar **instellingen**.
1. Selecteer **Nieuw** en kies uw bron.
1. Als u uw bedrijfs model opslagplaats toevoegt, geeft u de connection string op.

Een bron verwijderen:

1. Ga naar **instellingen**.
1. Zoek de bron die u wilt verwijderen.
1. Selecteer **X** om deze te verwijderen. U kunt de open bare model opslagplaats niet verwijderen omdat de algemene interface definities afkomstig zijn uit deze opslag plaats.

Wijzig de bron prioriteiten:

U kunt een van de model definitie bronnen slepen en neerzetten naar een andere classificatie in de lijst. Als er een conflict optreedt, overschrijven de definitie bronnen met hogere classificaties bronnen met lagere classificaties.

### <a name="overview-page"></a>Overzichts pagina

#### <a name="device-overview"></a>Overzicht van apparaten

Wanneer het hulp programma verbinding maakt met uw IoT-hub, wordt een overzichts pagina weer gegeven met een lijst van alle apparaat-id's die zijn geregistreerd bij uw Azure IoT hub. Selecteer een apparaat om meer details weer te geven.

#### <a name="device-management"></a>Apparaatbeheer

- Als u een nieuw apparaat bij uw hub wilt registreren, selecteert u **toevoegen**. Voer een apparaat-ID in. Gebruik de standaard instellingen om automatisch verificatie sleutels te genereren en de verbinding met uw hub in te scha kelen.
- Selecteer **verwijderen**als u de identiteit van een apparaat wilt verwijderen. Controleer de details van het apparaat voordat u deze actie voltooit, zodat u zeker weet dat u de juiste apparaat-id wilt verwijderen.
- Het hulp programma ondersteunt query's door `capabilityID` en `interfaceID`. Voeg uw `capabilityID` of `interfaceID` als een para meter toe om een query op uw apparaten uit te kunnen zetten.

## <a name="interact-with-a-device"></a>Interactie met een apparaat

Dubbel klik op een apparaat op de pagina overzicht om het volgende detail niveau weer te geven. Er zijn twee secties: **Apparaat** en **digitale dubbele**.

### <a name="device"></a>Apparaat

Deze sectie bevat de tabbladen **apparaat-id**, telemetrie en **device dubbele** .

- U kunt de gegevens van de apparaat-id weer geven en bijwerken op het tabblad **apparaat-id** .
- Als een apparaat is verbonden en actief gegevens verzendt, kunt u de telemetrie weer geven op het tabblad telemetrie.
- U kunt de dubbele gegevens van het apparaat openen op het tabblad **apparaat dubbele** .

### <a name="digital-twin"></a>Digitale dubbele

U kunt het hulp programma gebruiken om een digitale dubbele instantie van het apparaat weer te geven. In dit artikel worden alle interfaces weer gegeven die zijn gekoppeld aan het mogelijkheidsprofiel voor een IoT-Plug en Play apparaat. Selecteer een interface om de bijbehorende [IOT-Plug en Play primitieven](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)uit te vouwen.

#### <a name="properties"></a>properties

U kunt de alleen-lezen eigenschappen die zijn gedefinieerd in een interface op de pagina **Eigenschappen** weer geven. U kunt de Beschrijf bare eigenschappen die in een interface zijn gedefinieerd, bijwerken op de pagina **Schrijf bare eigenschappen** .

1. Ga naar de pagina met **Beschrijf bare eigenschappen** .
1. Klik op de eigenschap die u wilt bijwerken.
1. Voer de nieuwe waarde voor de eigenschap in.
1. Bekijk een voor beeld van de payload die naar het apparaat moet worden verzonden.
1. Verzend de wijziging.

Nadat u een wijziging hebt ingediend, kunt u de status van de update volgen: **synchronisatie**, **geslaagd**of **fout**. Wanneer de synchronisatie is voltooid, ziet u de nieuwe waarde van uw eigenschap in de kolom **gemelde eigenschap** . Als u naar andere pagina's navigeert voordat de synchronisatie is voltooid, ontvangt het hulp programma nog steeds een melding wanneer de update is voltooid. U kunt ook het meldingen centrum van het hulp programma gebruiken om de meldings geschiedenis te bekijken.

#### <a name="commands"></a>Opdrachten

Als u een opdracht wilt verzenden naar een apparaat, gaat u naar de pagina **opdrachten** :

1. Vouw in de lijst met opdrachten de opdracht uit die u wilt activeren.
1. Voer de vereiste waarden voor de opdracht in.
1. Bekijk een voor beeld van de payload die naar het apparaat moet worden verzonden.
1. Dien de opdracht in.

#### <a name="telemetry"></a>Telemetrie

Als u de telemetrie voor de geselecteerde interface wilt weer geven , gaat u naar de pagina telemetrie.

## <a name="next-steps"></a>Volgende stappen

In dit procedure-artikel hebt u geleerd hoe u Azure IoT Explorer kunt installeren en gebruiken om te communiceren met uw IoT Plug en Play-apparaten. Een voorgestelde volgende stap is informatie over het [beheren van modellen](./howto-manage-models.md).
