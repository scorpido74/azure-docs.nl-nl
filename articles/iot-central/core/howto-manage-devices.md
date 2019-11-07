---
title: De apparaten in uw Azure IoT Central-toepassing beheren | Microsoft Docs
description: Als operator vindt u informatie over het beheren van apparaten in uw Azure IoT Central-toepassing.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: adcaa29ed894f2d61871f467369bcdd05f8cc593
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73601594"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Apparaten beheren in uw Azure IoT Central-toepassing

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

In dit artikel wordt beschreven hoe u als een operator apparaten beheert in uw Azure IoT Central-toepassing. Als operator kunt u het volgende doen:

- Gebruik de pagina **apparaten** om apparaten weer te geven, toe te voegen en te verwijderen die zijn verbonden met uw Azure IOT Central-toepassing.
- Onderhoud van een actuele inventaris van uw apparaten.
- De meta gegevens van uw apparaat up-to-date houden door de waarden te wijzigen die zijn opgeslagen in de apparaateigenschappen.
- U bepaalt het gedrag van uw apparaten door een instelling op een specifiek apparaat op de pagina **instellingen** bij te werken.

## <a name="view-your-devices"></a>Uw apparaten weergeven

Een afzonderlijk apparaat weer geven:

1. Kies **apparaten** in het linkerdeel venster. Hier ziet u een lijst met de [sjablonen](howto-set-up-template.md)van uw apparaten.

1. Kies een sjabloon in de lijst **sjablonen** .

1. In het rechterdeel venster van de pagina **apparaten** ziet u een lijst met apparaten die zijn gemaakt op basis van die apparaataccount. Kies een afzonderlijk apparaat om de pagina met details van het apparaat voor dat apparaat weer te geven:

    ![Pagina Details van apparaat](./media/howto-manage-devices/devicelist.png)

## <a name="add-a-device"></a>Een apparaat toevoegen

Een apparaat toevoegen aan uw Azure IoT Central-toepassing:

1. Kies **apparaten** in het linkerdeel venster.

1. Kies de sjabloon van het apparaat waaruit u een apparaat wilt maken.

1. Kies + **Nieuw**.

1. Kies **werkelijk** of **gesimuleerd**. Een echt apparaat is voor een fysiek apparaat waarmee u verbinding maakt met uw Azure IoT Central-toepassing. Voor een gesimuleerd apparaat zijn voorbeeld gegevens gegenereerd door Azure IoT Central.

## <a name="import-devices"></a>Apparaten importeren

Als u een groot aantal apparaten wilt verbinden met uw toepassing, kunt u apparaten bulksgewijs importeren uit een CSV-bestand. Het CSV-bestand moet de volgende kolommen en kopteksten hebben:

* **IOTC_DeviceID** -de apparaat-id mag alleen kleine letters bevatten.
* **IOTC_DeviceName** -deze kolom is optioneel.

Apparaten in uw toepassing bulksgewijs registreren:

1. Kies **apparaten** in het linkerdeel venster.

1. Kies in het linkerdeel venster de sjabloon voor het apparaat waarvoor u de apparaten bulksgewijs wilt maken.

    > [!NOTE]
    > Als u nog geen sjabloon voor het apparaat hebt, kunt u apparaten importeren onder niet- **gekoppelde apparaten** en deze zonder sjabloon registreren. Nadat de apparaten zijn geïmporteerd, kunt u ze koppelen aan een sjabloon.

1. Selecteer **importeren**.

    ![Import actie](./media/howto-manage-devices/bulkimport1a.png)

1. Selecteer het CSV-bestand met de lijst met apparaat-Id's die moeten worden geïmporteerd.

1. Het importeren van apparaten wordt gestart zodra het bestand is geüpload. U kunt de import status boven aan het raster van het apparaat volgen.

1. Zodra het importeren is voltooid, wordt er een bericht weer gegeven op het raster van het apparaat.

    ![Import geslaagd](./media/howto-manage-devices/bulkimport3a.png)

Als de Importeer bewerking van het apparaat mislukt, ziet u een fout bericht op het raster van het apparaat. Een logboek bestand waarin alle fouten worden vastgelegd, wordt gegenereerd dat u kunt downloaden.

**Apparaten koppelen aan een sjabloon**

Als u apparaten registreert door het importeren te starten onder niet- **gekoppelde apparaten**, worden de apparaten gemaakt zonder een sjabloon koppeling van het apparaat. Apparaten moeten worden gekoppeld aan een sjabloon om de gegevens en andere details over het apparaat te kunnen verkennen. Volg deze stappen om apparaten te koppelen aan een sjabloon:

1. Kies **apparaten** in het linkerdeel venster.

1. Kies in het linkerdeel venster niet- **gekoppelde apparaten**:

    ![Niet-gekoppelde apparaten](./media/howto-manage-devices/unassociateddevices1a.png)

1. Selecteer de apparaten die u wilt koppelen aan een sjabloon:

1. Selecteer **koppelen**:

    ![Apparaten koppelen](./media/howto-manage-devices/unassociateddevices2a.png)

1. Kies de sjabloon in de lijst met beschik bare sjablonen en selecteer **koppelen**.

1. De geselecteerde apparaten zijn gekoppeld aan de apparaatprofiel die u hebt gekozen.

> [!NOTE]
> Nadat een apparaat is gekoppeld aan een sjabloon, kan het niet worden gekoppeld aan of gekoppeld aan een andere sjabloon.

## <a name="export-devices"></a>Apparaten exporteren

Als u een echt apparaat wilt verbinden met IoT Central, hebt u het connection string. U kunt de details van het apparaat in bulk exporteren om de informatie te verkrijgen die u nodig hebt om verbindings reeksen voor apparaten te maken. Het export proces maakt een CSV-bestand met de apparaat-id, de apparaatnaam en de sleutels voor alle geselecteerde apparaten.

Apparaten bulksgewijs exporteren vanuit uw toepassing:

1. Kies **apparaten** in het linkerdeel venster.

1. Kies in het linkerdeel venster de sjabloon van waaruit u de apparaten wilt exporteren.

1. Selecteer de apparaten die u wilt exporteren en selecteer vervolgens de **export** actie.

    ![Exporteren](./media/howto-manage-devices/export1a.png)

1. Het export proces wordt gestart. U kunt de status boven aan het raster volgen.

1. Wanneer het exporteren is voltooid, wordt een bericht weer gegeven samen met een koppeling om het gegenereerde bestand te downloaden.

1. Selecteer het **succes bericht** om het bestand te downloaden naar een lokale map op de schijf.

    ![Het exporteren is voltooid](./media/howto-manage-devices/export2a.png)

1. Het geëxporteerde CSV-bestand bevat de volgende kolommen: apparaat-ID, apparaatnaam, Apparaatsets en x509-certificaat vingerafdrukken:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Zie [connectiviteit van apparaten in Azure IOT Central](concepts-connectivity.md)voor meer informatie over verbindings reeksen en het verbinden van echte apparaten met uw IOT Central-toepassing.

## <a name="delete-a-device"></a>Een apparaat verwijderen

Een echt of gesimuleerd apparaat verwijderen uit uw Azure IoT Central-toepassing:

1. Kies **apparaten** in het linkerdeel venster.

1. Kies de sjabloon van het apparaat dat u wilt verwijderen.

1. Schakel het selectie vakje in naast het apparaat dat u wilt verwijderen.

1. Kies **verwijderen**.

## <a name="change-a-device-setting"></a>De instelling van een apparaat wijzigen

Instellingen bepalen het gedrag van een apparaat. Met andere woorden, ze stellen u in staat om invoer te bieden aan uw apparaat. U kunt de Apparaatinstellingen weer geven en bijwerken op de pagina met details van het **apparaat** .

1. Kies **apparaten** in het linkerdeel venster.

1. Kies de Device-sjabloon van het apparaat waarvan u de instellingen wilt wijzigen.

1. Kies het tabblad **instellingen** . Hier ziet u alle instellingen die uw apparaat heeft en de huidige waarden. Voor elke instelling kunt u zien of het apparaat nog steeds wordt gesynchroniseerd.

1. Wijzig de instellingen in de waarden die u nodig hebt. U kunt meerdere instellingen tegelijk wijzigen en deze allemaal op hetzelfde moment bijwerken.

1. Kies **bijwerken**. De waarden worden naar uw apparaat verzonden. Wanneer het apparaat de wijzigings instelling bevestigt, wordt de status van de instelling teruggestuurd naar **gesynchroniseerd**.

## <a name="change-a-property"></a>Een eigenschap wijzigen

Eigenschappen zijn de meta gegevens van het apparaat die zijn gekoppeld aan het apparaat, zoals City en serie nummer. U kunt eigenschappen weer geven en bijwerken op de pagina met details van het **apparaat** .

1. Kies **apparaten** in het linkerdeel venster.

1. Kies de Device-sjabloon van het apparaat waarvan u de eigenschappen wilt wijzigen.

1. Kies het tabblad **Eigenschappen** , waar u alle eigenschappen ziet.

1. Wijzig de eigenschappen van de toepassing in de waarden die u nodig hebt. U kunt meerdere eigenschappen tegelijk wijzigen en ze allemaal tegelijk bijwerken. Kies **bijwerken**.

> [!NOTE]
> U kunt de waarde van eigenschappen van het _apparaat_niet wijzigen. Apparaateigenschappen worden ingesteld door het apparaat en zijn alleen-lezen in de Azure IoT Central-toepassing.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u apparaten beheert in uw Azure IoT Central-toepassing, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Apparaatsets gebruiken](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
