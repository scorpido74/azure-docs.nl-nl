---
title: bestand opnemen
description: bestand opnemen
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: ca4bd3d3b40934323bab8036f3ce72e9281f1de4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176471"
---
> [!div class="op_single_selector"]
> * [C op Windows](../articles/iot-accelerators/iot-accelerators-connecting-devices.md)
> * [C op Linux](../articles/iot-accelerators/iot-accelerators-connecting-devices-linux.md)
> * [C op Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-c.md)
> * [Node.js (algemeen)](../articles/iot-accelerators/iot-accelerators-connecting-devices-node.md)
> * [Node.js op Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-node.md)
> * [MXChip IoT DevKit](../articles/iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md)

In deze zelfstudie implementeert u een **Chiller-apparaat** dat de volgende telemetrie naar de versneller met externe [bewakingsoplossing](../articles/iot-accelerators/about-iot-accelerators.md)verzendt:

* Temperatuur
* Druk
* Vochtigheid

Voor de eenvoud genereert de code voorbeeldtelemetriewaarden voor de **Chiller.** U het monster uitbreiden door echte sensoren op uw apparaat aan te sluiten en echte telemetrie te verzenden.

Het voorbeeldapparaat ook:

* Hiermee stuurt u metagegevens naar de oplossing om de mogelijkheden ervan te beschrijven.
* Reageert op acties die zijn geactiveerd vanaf de pagina **Apparaten** in de oplossing.
* Reageert op configuratiewijzigingen die vanaf de pagina **Apparaten** in de oplossing worden verzonden.

U hebt een actief Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.

## <a name="before-you-start"></a>Voordat u begint

Voordat u code voor uw apparaat schrijft, implementeert u de accelerator voor remote monitoring-oplossingen en voegt u een nieuw echt apparaat toe aan de oplossing.

### <a name="deploy-your-remote-monitoring-solution-accelerator"></a>Uw remote monitoring oplossingsversneller implementeren

Het **Chiller-apparaat** dat u in deze zelfstudie maakt, verzendt gegevens naar een instantie van de versneller met [de oplossing voor externe bewaking.](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md) Zie De versneller van de oplossing voor externe bewaking implementeren in uw Azure-account als u de versneller voor externe [bewaking-oplossingen](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md) nog niet hebt ingericht

Wanneer het implementatieproces voor de oplossing voor externe bewaking is voltooid, klikt u op **Starten** om het oplossingsdashboard in uw browser te openen.

![Het oplossingsdashboard](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>Uw apparaat toevoegen aan de oplossing voor externe bewaking

> [!NOTE]
> Als u al een apparaat in uw oplossing hebt toegevoegd, u deze stap overslaan. Voor de volgende stap is echter de tekenreeks van de verbinding met uw apparaat vereist. U de verbindingstekenreeks van een apparaat ophalen uit de [Azure-portal](https://portal.azure.com) of het [AZ-iot](https://docs.microsoft.com/cli/azure/iot?view=azure-cli-latest) CLI-hulpprogramma gebruiken.

Als een apparaat verbinding wil maken met de oplossingsversneller, moet het zich identificeren met IoT Hub met behulp van geldige referenties. U hebt de mogelijkheid om de tekenreeks voor apparaatverbinding op te slaan die deze referenties bevat wanneer u het apparaat aan de oplossing toevoegt. U neemt de tekenreeks voor apparaatverbinding later in deze zelfstudie op in uw clienttoepassing.

Voer de volgende stappen uit op de pagina **Device Explorer** in de oplossing om een apparaat toe te voegen aan uw oplossing voor externe bewaking:

1. Kies **+ Nieuw apparaat**en kies Vervolgens **Echt** als **apparaattype:**

    ![Echt apparaat toevoegen](media/iot-suite-selector-connecting/devicesprovision.png)

1. Voer **de fysieke koeler** in als de apparaat-id. Kies de **opties Symmetrische sleutel** en Automatisch genereren van **sleutels:**

    ![Apparaatopties kiezen](media/iot-suite-selector-connecting/devicesoptions.png)

1. Kies **Toepassen**. Noteer vervolgens de primaire kernwaardewaarden **apparaat-id,** **primaire sleutel**en **verbindingstekenreeks:**

    ![Referenties ophalen](media/iot-suite-selector-connecting/credentials.png)

U hebt nu een echt apparaat toegevoegd aan de remote monitoring oplossingsversneller en de tekenreeks van de apparaatverbinding genoteerd. In de volgende secties implementeert u de clienttoepassing die de tekenreeks voor apparaatverbinding gebruikt om verbinding te maken met uw oplossing.

De clientapplicatie implementeert het **Chiller** ingebouwde Chiller-apparaatmodel. Een oplossingsversnellerapparaatmodel geeft het volgende aan over een apparaat:

* De eigenschappen die het apparaat rapporteert aan de oplossing. Een **Chiller-apparaat** rapporteert bijvoorbeeld informatie over de firmware en locatie.
* De soorten telemetrie die het apparaat naar de oplossing stuurt. Een **koelapparaat** verzendt bijvoorbeeld temperatuur-, vochtigheids- en drukwaarden.
* De methoden die u plannen vanuit de oplossing om op het apparaat uit te voeren. Een **Chiller-apparaat** moet bijvoorbeeld **opnieuw opstarten,** **firmware-,** **noodklepontgrendelingsmethoden**en **increasepressure-methoden** implementeren.
