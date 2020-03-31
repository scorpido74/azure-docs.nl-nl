---
title: Azure IoT explorer installeren en gebruiken | Microsoft Documenten
description: Installeer het Azure IoT explorer-hulpprogramma en gebruik het om te communiceren met de IoT Plug and Play Preview-apparaten die zijn verbonden met mijn IoT-hub.
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 963421fa1ef06599448c9a4197f0d7a6ad2e142d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159180"
---
# <a name="install-and-use-azure-iot-explorer"></a>Azure IoT explorer installeren en gebruiken

De Azure IoT explorer is een grafisch hulpmiddel voor het communiceren en testen van uw IoT Plug and Play Preview-apparaten. Nadat u het gereedschap op uw lokale machine hebt geïnstalleerd, u deze gebruiken om verbinding te maken met een apparaat. U het hulpprogramma gebruiken om de telemetrie weer te geven die het apparaat verzendt, werkt met apparaateigenschappen en oproepopdrachten.

In dit artikel leest u informatie over:

- Installeer en configureer het Azure IoT explorer-hulpprogramma.
- Gebruik de tool om te communiceren met en test uw apparaten.

## <a name="prerequisites"></a>Vereisten

Als u het azure IoT explorer-hulpprogramma wilt gebruiken, moet u het andere doen:

- Een Azure IoT-hub. Er zijn veel manieren om een IoT-hub toe te voegen aan uw Azure-abonnement, zoals [een IoT-hub maken met de Azure CLI.](../iot-hub/iot-hub-create-using-cli.md) U hebt de verbindingstekenreeks van de IoT-hub nodig om het Azure IoT Explorer-hulpprogramma uit te voeren. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
- Een apparaat dat is geregistreerd in uw IoT-hub. U de volgende opdracht Azure CLI gebruiken om een apparaat te registreren. Zorg ervoor dat `{YourIoTHubName}` `{YourDeviceID}` u de tijdelijke aanduidingen en tijdelijke aanduidingen vervangt door uw waarden:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>Azure IoT explorer installeren

Ga naar [Azure IoT explorer-releases](https://github.com/Azure/azure-iot-explorer/releases) en breid de lijst met elementen uit voor de meest recente versie. Download en installeer de meest recente versie van de applicatie.

## <a name="use-azure-iot-explorer"></a>Azure IoT-verkenner gebruiken

Voor een apparaat u uw eigen apparaat aansluiten of een van onze gesimuleerde apparaten gebruiken. Volg [deze instructies](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples) om het gesimuleerde apparaatvoorbeeld uit te voeren.

### <a name="connect-to-your-hub"></a>Verbinding maken met uw hub

De eerste keer dat u Azure IoT explorer uitvoert, wordt u gevraagd om de verbindingstekenreeks van uw IoT-hub. Nadat u de verbindingstekenreeks hebt toegevoegd, selecteert u **Verbinding maken**. U de instellingen van het hulpprogramma gebruiken om over te schakelen naar een andere IoT-hub door de verbindingstekenreeks bij te werken.

De modeldefinitie voor een IoT Plug and Play-apparaat wordt opgeslagen in de openbare opslagplaats, een bedrijfsopslagplaats of uw verbonden apparaat. Standaard zoekt het hulpprogramma naar uw modeldefinitie in de openbare modelopslagplaats en uw verbonden apparaat. U bronnen toevoegen en verwijderen of de prioriteit van de bronnen configureren in **Instellingen:**

Ga als lid van het werk om een bron toe te voegen:

1. Ga naar **Settings**.
1. Selecteer **Nieuw** en kies uw bron.
1. Als u uw bedrijfsmodelrepository toevoegt, geeft u de verbindingstekenreeks op.

Ga als lid van het werk om een bron te verwijderen:

1. Ga naar **Settings**.
1. Zoek de bron die u wilt verwijderen.
1. Selecteer **X** om het te verwijderen. U de openbare modelopslagplaats niet verwijderen omdat de algemene interfacedefinities afkomstig zijn uit deze opslagplaats.

Wijzig de bronprioriteiten:

U een van de modeldefinitiebronnen slepen en neerzetten naar een andere rangschikking in de lijst. Als er een conflict is, overschrijven definitiebronnen met hogere rankings bronnen met lagere rankings.

### <a name="view-devices"></a>Apparaten weergeven

Nadat de tool verbinding heeft gemaakt met uw IoT-hub, wordt de **lijstpagina Apparaten** weergegeven met de identiteit van het apparaat die is geregistreerd bij uw IoT-hub. U elk item in de lijst uitbreiden om meer informatie te bekijken.

Op de **lijstpagina Apparaten** u:

- Selecteer **Toevoegen** om een nieuw apparaat te registreren met uw hub. Voer vervolgens een apparaat-ID in. Gebruik de standaardinstellingen om automatisch verificatiesleutels te genereren en de verbinding met uw hub in te schakelen.
- Selecteer een apparaat en selecteer **Verwijderen** om een apparaatidentiteit te verwijderen. Controleer de apparaatgegevens voordat u deze actie voltooit om er zeker van te zijn dat u de juiste apparaatidentiteit verwijderd.
- Query `capabilityID` op `interfaceID`en . Voeg uw `capabilityID` `interfaceID` of als parameter toe om uw apparaten op te vragen.

## <a name="interact-with-a-device"></a>Interactie met een apparaat

Selecteer **op** de lijst Apparaten een waarde in de kolom **Apparaat-id** om de detailpagina voor het geregistreerde apparaat weer te geven. Voor elk apparaat zijn er twee secties: **Device** en **Digital Twin**.

### <a name="device"></a>Apparaat

Deze sectie bevat de **tabbladen Apparaatidentiteit**, **Apparaattweeling,** **Telemetrie,** **Directe methode** en **Cloud-to-device-bericht.**

- U de [identiteitsgegevens van](../iot-hub/iot-hub-devguide-identity-registry.md) het apparaat weergeven en bijwerken op het tabblad **Apparaatidentiteit.**
- U hebt toegang tot de twee informatie van het [apparaat](../iot-hub/iot-hub-devguide-device-twins.md) op het tabblad **Device Twin.**
- Als een apparaat is verbonden en actief gegevens verzendt, u de [telemetrie](../iot-hub/iot-hub-devguide-messages-read-builtin.md) bekijken op het tabblad **Telemetrie.**
- U een [directe methode](../iot-hub/iot-hub-devguide-direct-methods.md) op het apparaat aanroepen op het tabblad **Direct-methode.**
- U een [cloud-to-device-bericht](../iot-hub/iot-hub-devguide-messages-c2d.md) verzenden op het tabblad **Cloud-to-device-berichten.**

### <a name="digital-twin"></a>Digitale tweeling

U de tool gebruiken om een digitale twin instance van het apparaat weer te geven. Voor een IoT Plug and Play-apparaat worden alle interfaces die aan het apparaatmogelijkheidmodel zijn gekoppeld, weergegeven in dit gedeelte van het gereedschap. Selecteer een interface om de bijbehorende [IoT Plug and Play-primitieven](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)uit te breiden.

### <a name="interface"></a>Interface

Op de **interfacepagina** u de JSON-definitie van de interface bekijken.

#### <a name="properties"></a>Eigenschappen

U de alleen-lezen eigenschappen bekijken die zijn gedefinieerd in een interface op de pagina **Niet-schrijfbare eigenschappen.** U de schrijfbare eigenschappen bijwerken die zijn gedefinieerd in een interface op de pagina **Schrijfbare eigenschappen:**

1. Ga naar de pagina **Schrijfbare eigenschappen.**
1. Klik op de eigenschap die u wilt bijwerken.
1. Voer de nieuwe waarde voor de eigenschap in.
1. Bekijk een voorbeeld van de payload die naar het apparaat moet worden verzonden.
1. Dien de wijziging in.

Nadat u een wijziging hebt ingediend, u de updatestatus bijhouden: **synchroniseren,** **succes**of **fout**. Wanneer de synchronisatie is voltooid, ziet u de nieuwe waarde van uw eigenschap in de kolom **Gerapporteerde eigenschap.** Als u naar andere pagina's navigeert voordat de synchronisatie is voltooid, wordt u nog steeds op de hoogte gesteld wanneer de update is voltooid. U het meldingscentrum van het hulpprogramma ook gebruiken om de meldingsgeschiedenis te bekijken.

#### <a name="commands"></a>Opdrachten

Als u een opdracht naar een apparaat wilt verzenden, gaat u naar de pagina **Opdrachten:**

1. Vouw in de lijst met opdrachten de opdracht uit die u wilt activeren.
1. Voer de vereiste waarden voor de opdracht in.
1. Bekijk een voorbeeld van de payload die naar het apparaat moet worden verzonden.
1. Dien de opdracht in.

#### <a name="telemetry"></a>Telemetrie

Als u de telemetrie voor de geselecteerde interface wilt weergeven, gaat u naar **de telemetriepagina.**

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u hoe u Azure IoT explorer installeren en gebruiken om te communiceren met uw IoT Plug and Play-apparaten. Een voorgestelde volgende stap is om te leren hoe u [azure CLI-extensie installeert en gebruikt.](./howto-install-pnp-cli.md)
