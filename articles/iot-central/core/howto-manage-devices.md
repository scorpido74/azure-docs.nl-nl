---
title: De apparaten beheren in uw Azure IoT Central-toepassing | Microsoft Documenten
description: Lees als operator hoe u apparaten in uw Azure IoT Central-toepassing beheert.
author: sarahhubbard
ms.author: sahubbar
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 8725a822c575ce80b9810d56bfd072241ded4c86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157939"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Apparaten beheren in uw Azure IoT Central-toepassing



In dit artikel wordt beschreven hoe u als operator apparaten in uw Azure IoT Central-toepassing beheren. Als operator u:

- Gebruik de pagina **Apparaten** om apparaten die zijn verbonden met uw Azure IoT Central-toepassing weer te geven, toe te voegen en te verwijderen.
- Houd een up-to-date voorraad van uw apparaten bij.
- Houd de metagegevens van uw apparaat up-to-date door de waarden die zijn opgeslagen in de apparaateigenschappen te wijzigen vanuit uw weergaven.
- Bedien het gedrag van uw apparaten door een instelling op een specifiek apparaat bij te werken vanuit uw weergaven.

## <a name="view-your-devices"></a>Uw apparaten weergeven

Ga als een mogelijk overzicht van een afzonderlijk apparaat:

1. Kies **Apparaten** in het linkerdeelvenster. Hier ziet u een lijst met alle apparaten en van uw apparaatsjablonen.

1. Kies een apparaatsjabloon.

1. In het rechterdeelvenster van de pagina **Apparaten** ziet u een lijst met apparaten die zijn gemaakt met die apparaatsjabloon. Kies een afzonderlijk apparaat om de pagina met apparaatgegevens voor dat apparaat te bekijken:

    ![Pagina Apparaatgegevens](./media/howto-manage-devices/devicelist.png)


## <a name="add-a-device"></a>Een apparaat toevoegen

Ga als lid van het nieuwe bedrijf naar een apparaat toe aan uw Azure IoT Central-toepassing:

1. Kies **Apparaten** in het linkerdeelvenster.

1. Kies de apparaatsjabloon waaruit u een apparaat wilt maken.

1. Kies + **Nieuw**.

1. Schakel de **gesimuleerde** schakelin in **op In-** of **Uit.** Een echt apparaat is voor een fysiek apparaat dat u verbinding maakt met uw Azure IoT Central-toepassing. Op een gesimuleerd apparaat worden voorbeeldgegevens gegenereerd door Azure IoT Central.

1. Klik **op Maken**.

1. Dit apparaat wordt nu weergegeven in de lijst met apparaten voor deze sjabloon. Selecteer het apparaat om de pagina met apparaatdetails te zien die alle weergaven voor het apparaat bevat.

## <a name="import-devices"></a>Apparaten importeren

Als u een groot aantal apparaten met uw toepassing wilt verbinden, u apparaten uit een CSV-bestand bulkimporteren. Het CSV-bestand moet de volgende kolommen en kopteksten bevatten:

* **IOTC_DeviceID** - de apparaat-ID moet alle kleine letters.
* **IOTC_DeviceName** - deze kolom is optioneel.

Apparaten in uw toepassing in bulk registreren:

1. Kies **Apparaten** in het linkerdeelvenster.

1. Kies in het linkerdeelvenster de apparaatsjabloon waarvoor u de apparaten wilt maken.

    > [!NOTE]
    > Als u nog geen apparaatsjabloon hebt, u apparaten importeren onder **Alle apparaten** en deze registreren zonder sjabloon. Nadat apparaten zijn geïmporteerd, u ze vervolgens migreren naar een sjabloon.

1. Selecteer **Importeren**.

    ![Actie importeren](./media/howto-manage-devices/bulkimport1a.png)


1. Selecteer het CSV-bestand met de lijst met apparaat-id's die moeten worden geïmporteerd.

1. Het importeren van apparaten wordt gestart nadat het bestand is geüpload. U de importstatus bijhouden in het deelvenster Apparaatbewerkingen. Dit paneel verschijnt automatisch nadat de import is gestart of u het openen via het belpictogram in de rechterbovenhoek.

1. Zodra de import is voltooid, wordt een succesbericht weergegeven in het deelvenster Apparaatbewerkingen.

    ![Succes importeren](./media/howto-manage-devices/bulkimport3a.png)


Als de bewerking voor het importeren van het apparaat mislukt, ziet u een foutbericht in het deelvenster Apparaatbewerkingen. Een logbestand dat alle fouten vastlegt, wordt gegenereerd die u downloaden.

**Apparaten migreren naar een sjabloon**

Als u apparaten registreert door de import te starten onder **Alle apparaten,** worden de apparaten gemaakt zonder dat er een apparaatsjabloonwordt associatie. Apparaten moeten worden gekoppeld aan een sjabloon om de gegevens en andere details over het apparaat te verkennen. Volg deze stappen om apparaten aan een sjabloon te koppelen:

1. Kies **Apparaten** in het linkerdeelvenster.

1. Kies op het linkerdeelvenster **Alle apparaten:**

    ![Niet-gekoppelde apparaten](./media/howto-manage-devices/unassociateddevices1a.png)


1. Gebruik het filter op het raster om te bepalen of de waarde in de kolom **Apparaatsjabloon** 'Niet-gekoppeld' is voor een van uw apparaten.

1. Selecteer de apparaten die u aan een sjabloon wilt koppelen:

1. Selecteer **Migreren:**

    ![Apparaten koppelen](./media/howto-manage-devices/unassociateddevices2a.png)


1. Kies de sjabloon in de lijst met beschikbare sjablonen en selecteer **Migreren**.

1. De geselecteerde apparaten zijn gekoppeld aan de apparaatsjabloon die u hebt gekozen.


## <a name="export-devices"></a>Exportapparaten

Als u een echt apparaat wilt aansluiten op IoT Central, hebt u de verbindingstekenreeks nodig. U apparaatgegevens in bulk exporteren om de informatie te krijgen die u nodig hebt om apparaatverbindingstekenreeksen te maken. Met het exportproces wordt een CSV-bestand met de apparaatidentiteit, de apparaatnaam en de sleutels voor alle geselecteerde apparaten.

Ga als een bulkexportapparaat uit uw toepassing:

1. Kies **Apparaten** in het linkerdeelvenster.

1. Kies in het linkerdeelvenster de apparaatsjabloon waaruit u de apparaten wilt exporteren.

1. Selecteer de apparaten die u wilt exporteren en selecteer vervolgens de actie **Exporteren.**

    ![Exporteren](./media/howto-manage-devices/export1a.png)


1. Het exportproces begint. U de status bijhouden via het deelvenster Apparaatbewerkingen.

1. Wanneer de export is voltooid, wordt een succesbericht weergegeven, samen met een link om het gegenereerde bestand te downloaden.

1. Selecteer de koppeling **Bestand downloaden** om het bestand te downloaden naar een lokale map op de schijf.

    ![Export succes](./media/howto-manage-devices/export2a.png)


1. Het geëxporteerde CSV-bestand bevat de volgende kolommen: apparaat-id, apparaatnaam, apparaatsleutels en duimafdrukken met X509-certificaat:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Zie [Apparaatconnectiviteit in Azure IoT Central](concepts-get-connected.md)voor meer informatie over verbindingstekenreeksen en het verbinden van echte apparaten met uw IoT Central-toepassing.

## <a name="delete-a-device"></a>Een apparaat verwijderen

Ga als lid van uw Azure IoT Central-toepassing als lid van uw Azure IoT Central-toepassing:

1. Kies **Apparaten** in het linkerdeelvenster.

1. Kies de apparaatsjabloon van het apparaat dat u wilt verwijderen.

1. Gebruik de filtergereedschappen om uw apparaten te filteren en te zoeken. Schakel het selectievakje naast de apparaten in om te verwijderen.

1. Kies **Verwijderen**. U de status van deze verwijdering bijhouden in het deelvenster Apparaatbewerkingen.

## <a name="change-a-property"></a>Een eigenschap wijzigen

Cloudeigenschappen zijn de apparaatmetagegevens die aan het apparaat zijn gekoppeld, zoals de plaats en het serienummer. Schrijfbare eigenschappen bepalen het gedrag van een apparaat. Met andere woorden, ze stellen u in staat om ingangen aan uw apparaat te leveren.  Apparaateigenschappen worden ingesteld door het apparaat en zijn alleen-lezen binnen IoT Central. U eigenschappen weergeven en bijwerken in de weergaven **Apparaatdetails** voor uw apparaat.

1. Kies **Apparaten** in het linkerdeelvenster.

1. Kies de apparaatsjabloon van het apparaat waarvan u de eigenschappen wilt wijzigen en selecteer het doelapparaat.

1. Kies de weergave die eigenschappen voor uw apparaat bevat, met deze weergave u waarden invoeren en **Opslaan** boven aan de pagina selecteren. Hier ziet u de eigenschappen die uw apparaat heeft en hun huidige waarden. Cloudeigenschappen en schrijfbare eigenschappen hebben bewerkbare velden, terwijl apparaateigenschappen alleen-lezen zijn. Voor schrijfbare eigenschappen u hun synchronisatiestatus onder aan het veld zien. 

1. Wijzig de eigenschappen in de waarden die u nodig hebt. U meerdere eigenschappen tegelijk wijzigen en ze allemaal tegelijk bijwerken.

1. Kies **Opslaan**. Als u schrijfbare eigenschappen hebt opgeslagen, worden de waarden naar uw apparaat verzonden. Wanneer het apparaat de wijziging voor de eigenschap schrijfbaar bevestigt, keert de status terug naar **gesynchroniseerd**. Als u een cloudeigenschap hebt opgeslagen, wordt de waarde bijgewerkt.


## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u apparaten in uw Azure IoT Central-toepassing beheren, volgt de volgende stap:

> [!div class="nextstepaction"]
> [Apparaatgroepen gebruiken](tutorial-use-device-groups.md)

<!-- Next how-tos in the sequence -->
