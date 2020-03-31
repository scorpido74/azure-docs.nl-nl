---
title: Importpakket voor externe bewaking-oplossingen - Azure | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u een automatisch apparaatbeheerpakket importeert in de versneller van de oplossing op afstand
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 8100914e9a1d1489cb80de55a689e17f6d28a941
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61443330"
---
# <a name="import-an-automatic-device-management-package-into-your-remote-monitoring-solution-accelerator"></a>Een automatisch apparaatbeheerpakket importeren in uw remote monitoring oplossingsversneller

Een automatische configuratie van apparaatbeheer definieert de configuratiewijzigingen die moeten worden geïmplementeerd op een groep apparaten. In dit artikel wordt ervan uitgegaan dat een ontwikkelaar in uw organisatie al een automatische configuratie voor apparaatbeheer heeft gemaakt. Zie een van de volgende handleidingen van IoT Hub voor meer informatie over hoe een ontwikkelaar een configuratie maakt:

- [IoT-apparaten op schaal configureren en bewaken met behulp van de Azure-portal](../iot-hub/iot-hub-auto-device-config.md)
- [IoT-apparaten op schaal configureren en bewaken met azure cli](../iot-hub/iot-hub-auto-device-config-cli.md)

Een ontwikkelaar maakt en test een automatische configuratie van apparaatbeheer in een ontwikkelomgeving. Wanneer u er klaar voor bent, u de configuratie importeren in uw remote monitoring-oplossingsversneller.

## <a name="export-a-configuration"></a>Een configuratie exporteren

Gebruik de Azure-portal om de automatische configuratie van apparaatbeheer te exporteren vanuit uw ontwikkelomgeving:

1. Navigeer in de Azure-portal naar de IoT-hub die u gebruikt om uw IoT-apparaten te ontwikkelen en te testen. Klik **op IoT-apparaatconfiguratie:**

    [![Configuratie van IoT-apparaten](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-expanded.png#lightbox)

1. Klik op de configuratie die u wilt gebruiken. Op de pagina **Details van apparaatconfiguratie** wordt het gewenste aantal weergegeven:

    [![Gegevens over de configuratie van IoT-apparaten](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-expanded.png#lightbox)
1. Klik **op Configuratiebestand downloaden:**

    [![Configuratiebestand downloaden](./media/iot-accelerators-remote-monitoring-import-adm-package/download-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/download-expanded.png#lightbox)

1. Sla het JSON-bestand op als een lokaal bestand genaamd **configuration.json**.

Nu hebt u een bestand dat de automatische configuratie van apparaatbeheer bevat. In het volgende gedeelte importeert u deze configuratie als een pakket in de oplossing voor externe bewaking.

## <a name="import-a-package"></a>Een pakket importeren

Volg de onderstaande stappen om een automatische configuratie van apparaatbeheer als pakket in uw oplossing te importeren:

1. Navigeren naar de pagina **Pakketten** op de ![pagina WebGebruikersinterface voor externe controle: pakketten](media/iot-accelerators-remote-monitoring-import-adm-package/packagepage.png)

1. Klik **op + Nieuw pakket,** kies **Configuratie** als pakkettype en klik op **Bladeren** om het **bestand configuration.json** te selecteren dat u in de vorige sectie hebt opgeslagen:

    ![Configuratie selecteren](media/iot-accelerators-remote-monitoring-import-adm-package/uploadpackage.png)

1. Klik **op Uploaden** om het pakket toe te voegen aan uw oplossing voor externe bewaking:

    ![Geüpload pakket](media/iot-accelerators-remote-monitoring-import-adm-package/uploadedpackage.png)

U hebt nu een automatische configuratie van apparaatbeheer als pakket geüpload. Op de pagina **Implementaties** u dit pakket implementeren op uw verbonden apparaten.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een configuratiepakket maken en importeren in de oplossing voor externe bewaking, is de volgende stap om te leren hoe [u apparaten die zijn aangesloten op externe bewaking in bulk beheren.](iot-accelerators-remote-monitoring-bulk-configuration-update.md)
