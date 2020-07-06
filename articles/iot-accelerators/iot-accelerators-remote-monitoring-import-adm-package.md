---
title: Pakket voor het importeren van oplossingen voor externe bewaking-Azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een automatisch Apparaatbeheer-pakket importeert in de oplossings versneller voor externe controle
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 8100914e9a1d1489cb80de55a689e17f6d28a941
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "61443330"
---
# <a name="import-an-automatic-device-management-package-into-your-remote-monitoring-solution-accelerator"></a>Een apparaat voor automatische Apparaatbeheer importeren in uw oplossings versneller voor externe controle

Een automatische configuratie van Apparaatbeheer definieert de configuratie wijzigingen die moeten worden geïmplementeerd op een groep apparaten. In dit artikel wordt ervan uitgegaan dat een ontwikkelaar in uw organisatie al een automatische configuratie voor Apparaatbeheer heeft gemaakt. Voor meer informatie over hoe een ontwikkelaar een configuratie maakt, raadpleegt u een van de volgende IoT Hub procedures:

- [IoT-apparaten op schaal configureren en bewaken met behulp van de Azure Portal](../iot-hub/iot-hub-auto-device-config.md)
- [IoT-apparaten op schaal configureren en bewaken met behulp van de Azure CLI](../iot-hub/iot-hub-auto-device-config-cli.md)

Een ontwikkelaar maakt en test een automatische configuratie van Apparaatbeheer in een ontwikkel omgeving. Wanneer u klaar bent, kunt u de configuratie importeren in uw oplossings versneller voor externe controle.

## <a name="export-a-configuration"></a>Een configuratie exporteren

Gebruik de Azure Portal voor het exporteren van de automatische configuratie van Apparaatbeheer vanuit uw ontwikkel omgeving:

1. Navigeer in het Azure Portal naar de IoT-hub die u gebruikt voor het ontwikkelen en testen van uw IoT-apparaten. Klik op **IOT-apparaatconfiguratie**:

    [![Configuratie van IoT-apparaat](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-expanded.png#lightbox)

1. Klik op de configuratie die u wilt gebruiken. De pagina **Details van apparaatconfiguratie** wordt weer gegeven:

    [![Details van IoT-apparaatconfiguratie](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-expanded.png#lightbox)
1. Klik op **configuratie bestand downloaden**:

    [![Configuratie bestand downloaden](./media/iot-accelerators-remote-monitoring-import-adm-package/download-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/download-expanded.png#lightbox)

1. Sla het JSON-bestand op als een lokaal bestand met de naam **configuration.jsop**.

U hebt nu een bestand dat de configuratie voor automatisch Apparaatbeheer bevat. In de volgende sectie importeert u deze configuratie als een pakket in de oplossing voor externe controle.

## <a name="import-a-package"></a>Een pakket importeren

Volg de onderstaande stappen om een configuratie voor automatische Apparaatbeheer te importeren als een pakket in uw oplossing:

1. Ga naar de pagina **pakketten** op de pagina externe bewaking webinterface: ![ pakketten](media/iot-accelerators-remote-monitoring-import-adm-package/packagepage.png)

1. Klik op **+ nieuw pakket**, kies **configuratie** als pakket type en klik op **Bladeren** om deconfiguration.jste selecteren **voor** het bestand dat u in de vorige sectie hebt opgeslagen:

    ![Configuratie selecteren](media/iot-accelerators-remote-monitoring-import-adm-package/uploadpackage.png)

1. Klik op **uploaden** om het pakket toe te voegen aan uw oplossing voor controle op afstand:

    ![Pakket geüpload](media/iot-accelerators-remote-monitoring-import-adm-package/uploadedpackage.png)

U hebt nu een automatische configuratie voor Apparaatbeheer geüpload als een pakket. Op de pagina **implementaties** kunt u dit pakket implementeren op de verbonden apparaten.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een configuratie pakket maakt en importeert in vanuit de oplossing voor controle op afstand, is de volgende stap informatie over het [beheren van apparaten die zijn verbonden met externe bewaking in bulk](iot-accelerators-remote-monitoring-bulk-configuration-update.md).
