---
title: Architectuurconcepten in Azure IoT Central | Microsoft Documenten
description: In dit artikel worden belangrijke concepten geïntroduceerd met betrekking tot de architectuur van Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 11/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 12ad231d81b6c134ebb8d4902b3f95c978e9622d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271640"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central-architectuur



In dit artikel vindt u een overzicht van de Microsoft Azure IoT Central-architectuur.

![Architectuur op het hoogste niveau](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Apparaten

Apparaten wisselen gegevens uit met uw Azure IoT Central-toepassing. Een apparaat kan:

- Stuur metingen zoals telemetrie.
- Synchronisatie-instellingen met uw toepassing.

In Azure IoT Central worden de gegevens die een apparaat met uw toepassing kan uitwisselen, opgegeven in een apparaatsjabloon. Zie [Metagegevensbeheer](#metadata-management)voor meer informatie over apparaatsjablonen.

Zie [Apparaatconnectiviteit](concepts-get-connected.md)voor meer informatie over hoe apparaten verbinding maken met uw Azure IoT Central-toepassing.

## <a name="azure-iot-edge-devices"></a>Azure IoT Edge-apparaten

Naast apparaten die zijn gemaakt met de [Azure IoT SDKs,](https://github.com/Azure/azure-iot-sdks)u azure [IoT Edge-apparaten](../../iot-edge/about-iot-edge.md) ook verbinden met een IoT Central-toepassing. Met IoT Edge u cloudintelligence en aangepaste logica rechtstreeks uitvoeren op IoT-apparaten die worden beheerd door IoT Central. Met de IoT Edge-runtime u:

- Workloads op het apparaat installeren en bijwerken.
- Houd de beveiligingsstandaarden van IoT Edge op het apparaat.
- Ervoor zorgen dat de IoT Edge-modules altijd worden uitgevoerd.
- De status van de module aan de cloud rapporteren voor externe bewaking.
- De communicatie tussen downstream bladknooppuntapparaten en een IoT Edge-apparaat, tussen modules op een IoT Edge-apparaat en tussen een IoT Edge-apparaat en de cloud beheren.

![Azure IoT Central met Azure IoT Edge](./media/concepts-architecture/iotedge.png)

IoT Central maakt de volgende mogelijkheden mogelijk voor IoT Edge-apparaten:

- Apparaatsjablonen om de mogelijkheden van een IoT Edge-apparaat te beschrijven, zoals:
  - Implementatie manifest uploadmogelijkheid, waarmee u een manifest voor een vloot van apparaten beheren.
  - Modules die worden uitgevoerd op het IoT Edge-apparaat.
  - De telemetrie die elke module verzendt.
  - De eigenschappen die elke module rapporteert.
  - De opdrachten waar elke module op reageert.
  - De relaties tussen een IoT Edge-gateway-apparaatcapaciteitsmodel en een downstream-apparaatcapaciteitsmodel.
  - Cloud-eigenschappen die niet zijn opgeslagen op het IoT Edge-apparaat.
  - Aanpassingen, dashboards en formulieren die deel uitmaken van uw IoT Central-toepassing.

  Zie de Connect [Azure IoT Edge-apparaten voor](./concepts-iot-edge.md) meer informatie naar een azure IoT Central-toepassingsartikel.

- De mogelijkheid om IoT Edge-apparaten op schaal in te richten met Azure IoT-apparaatinrichtingsservice
- Regels en acties.
- Aangepaste dashboards en analyses.
- Continue gegevensexport van telemetrie vanaf IoT Edge-apparaten.

### <a name="iot-edge-device-types"></a>IoT Edge-apparaattypen

IoT Central classificert IoT Edge-apparaattypen als volgt:

- Bladapparaten. Een IoT Edge-apparaat kan downstream-bladapparaten hebben, maar deze apparaten zijn niet ingericht in IoT Central.
- Gateway-apparaten met downstream-apparaten. Zowel gateway-apparaten als downstream-apparaten zijn ingericht in IoT Central

![IoT Central met IoT Edge-overzicht](./media/concepts-architecture/gatewayedge.png)

### <a name="iot-edge-patterns"></a>IoT Edge-patronen

IoT Central ondersteunt de volgende IoT Edge-apparaatpatronen:

#### <a name="iot-edge-as-leaf-device"></a>IoT Edge als bladapparaat

![IoT Edge als bladapparaat](./media/concepts-architecture/edgeasleafdevice.png)

Het IoT Edge-apparaat is ingericht in IoT Central en alle downstream-apparaten en hun telemetrie wordt weergegeven als afkomstig van het IoT Edge-apparaat. Downstream-apparaten die zijn aangesloten op het IoT Edge-apparaat zijn niet ingericht in IoT Central.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity"></a>IoT Edge-gatewayapparaat verbonden met downstream-apparaten met identiteit

![IoT Edge met downstream-apparaatidentiteit](./media/concepts-architecture/edgewithdownstreamdeviceidentity.png)

Het IoT Edge-apparaat is ingericht in IoT Central, samen met de downstream-apparaten die zijn aangesloten op het IoT Edge-apparaat. Runtime-ondersteuning voor het inrichten van downstream-apparaten via de gateway wordt momenteel niet ondersteund.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity-provided-by-the-iot-edge-gateway"></a>IoT Edge-gatewayapparaat verbonden met downstream-apparaten met identiteit die wordt geleverd door de IoT Edge-gateway

![IoT Edge met downstream-apparaat zonder identiteit](./media/concepts-architecture/edgewithoutdownstreamdeviceidentity.png)

Het IoT Edge-apparaat is ingericht in IoT Central, samen met de downstream-apparaten die zijn aangesloten op het IoT Edge-apparaat. Runtime-ondersteuning voor gatewaydie identiteit biedt aan downstream-apparaten en het inrichten van downstream-apparaten wordt momenteel niet ondersteund. Als u uw eigen module voor identiteitsvertaling meeneemt, kan IoT Central dit patroon ondersteunen.

## <a name="cloud-gateway"></a>Cloudgateway

Azure IoT Central gebruikt Azure IoT Hub als cloudgateway die apparaatconnectiviteit mogelijk maakt. IoT Hub maakt:

- Gegevensopname op schaal in de cloud.
- Apparaatbeheer.
- Veilige apparaatconnectiviteit.

Zie [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/)voor meer informatie over IoT Hub.

Zie [Apparaatconnectiviteit](concepts-get-connected.md)voor meer informatie over apparaatconnectiviteit in Azure IoT Central.

## <a name="data-stores"></a>Gegevenswinkels

Azure IoT Central slaat toepassingsgegevens op in de cloud. Opgeslagen toepassingsgegevens omvatten:

- Apparaatsjablonen.
- Apparaatidentiteiten.
- Apparaatmetagegevens.
- Gebruikers- en rolgegevens.

Azure IoT Central gebruikt een tijdreeksarchief voor de meetgegevens die vanaf uw apparaten worden verzonden. Tijdreeksgegevens van apparaten die door de analyseservice worden gebruikt.

## <a name="analytics"></a>Analyse

De analyseservice is verantwoordelijk voor het genereren van de aangepaste rapportagegegevens die de toepassing weergeeft. Een operator kan de analyses die in de toepassing worden weergegeven [aanpassen.](howto-create-analytics.md) De analyseservice is gebouwd bovenop [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) en verwerkt de meetgegevens die vanaf uw apparaten worden verzonden.

## <a name="rules-and-actions"></a>Regels en acties

[Regels en acties](tutorial-create-telemetry-rules.md) werken nauw samen om taken binnen de toepassing te automatiseren. Een bouwer kan regels definiëren op basis van apparaattelemetrie, zoals de temperatuur die een gedefinieerde drempelwaarde overschrijdt. Azure IoT Central gebruikt een streamprocessor om te bepalen wanneer aan de regelvoorwaarden is voldaan. Wanneer aan een regelvoorwaarde is voldaan, wordt een actie geactiveerd die door de bouwer is gedefinieerd. Een actie kan bijvoorbeeld een e-mail sturen om een technicus te laten weten dat de temperatuur in een apparaat te hoog is.

## <a name="metadata-management"></a>Metagegevensbeheer

In een Azure IoT Central-toepassing definiëren apparaatsjablonen het gedrag en de mogelijkheden van typen apparaten. Een sjabloon voor een koelkastapparaat geeft bijvoorbeeld de telemetrie op die een koelkast naar uw toepassing verzendt.

![Sjabloonarchitectuur](media/concepts-architecture/template-architecture.png)

In een IoT Central-toepassingsapparaatsjabloon bevat:

- **Apparaatcapaciteitsmodellen** geven de mogelijkheden van een apparaat op, zoals de telemetrie die het verzendt, de eigenschappen die de apparaatstatus definiëren en de opdrachten waarop het apparaat reageert. Apparaatmogelijkheden zijn ingedeeld in een of meer interfaces. Zie de [iot-plug and play -documentatie (preview) voor](../../iot-pnp/overview-iot-plug-and-play.md) meer informatie over apparaatcapaciteitsmodellen.
- **Cloudeigenschappen** geven de eigenschappen op die IoT Central voor een apparaat opslaat. Deze eigenschappen worden alleen opgeslagen in IoT Central en worden nooit naar een apparaat verzonden.
- **Weergaven** geven de dashboards en formulieren op die de bouwer maakt om de operator de apparaten te laten controleren en beheren.
- **Met aanpassingen** kan de bouwer een aantal definities in het apparaatcapaciteitsmodel overschrijven om ze relevanter te maken voor de IoT Central-toepassing.

Een toepassing kan een of meer gesimuleerde en echte apparaten hebben op basis van elke apparaatsjabloon.

## <a name="data-export"></a>Gegevensexport

In een Azure IoT Central-toepassing u [uw gegevens continu exporteren](howto-export-data.md) naar uw eigen Azure Event Hubs en Azure Service Bus-exemplaren. U uw gegevens ook periodiek exporteren naar uw Azure Blob-opslagaccount. IoT Central kan metingen, apparaten en apparaatsjablonen exporteren.

## <a name="batch-device-updates"></a>Updates van batch-apparaten

In een Azure IoT Central-toepassing u [taken maken en uitvoeren](howto-run-a-job.md) om verbonden apparaten te beheren. Met deze taken u bulkupdates uitvoeren voor apparaateigenschappen of -instellingen of opdrachten uitvoeren. U bijvoorbeeld een taak maken om de ventilatorsnelheid voor meerdere gekoelde automaten te verhogen.

## <a name="role-based-access-control-rbac"></a>Toegangsbeheer op basis van rollen (RBAC)

Een [beheerder kan toegangsregels](howto-manage-users-roles.md) voor een Azure IoT Central-toepassing definiëren met behulp van een van de vooraf gedefinieerde rollen of door een aangepaste rol te maken. Rollen bepalen tot welke gebieden van de toepassing een gebruiker toegang heeft en tot welke acties hij of zij kan uitvoeren.

## <a name="security"></a>Beveiliging

Beveiligingsfuncties binnen Azure IoT Central zijn onder andere:

- Gegevens worden versleuteld tijdens het transport en in rust.
- Verificatie wordt geleverd door Azure Active Directory of Microsoft Account. Tweestapsverificatie wordt ondersteund.
- Volledige huurder isolatie.
- Beveiliging op apparaatniveau.

## <a name="ui-shell"></a>UI-shell

De UI-shell is een moderne, responsieve, HTML5-browsergebaseerde toepassing.
Een beheerder kan de gebruikersinterface van de toepassing aanpassen door aangepaste thema's toe te passen en de helpkoppelingen te wijzigen om naar uw eigen aangepaste helpbronnen te verwijzen. Zie Het Azure [IoT Central UI-artikel aanpassen](howto-customize-ui.md) voor meer informatie over aanpassing aan de gebruikersinterface van de gebruikersinterface.

Een operator kan gepersonaliseerde applicatiedashboards maken. U verschillende dashboards hebben die verschillende gegevens weergeven en er tussen schakelen.

## <a name="next-steps"></a>Volgende stappen

Nu u meer te weten bent gekomen over de architectuur van Azure IoT Central, is de voorgestelde volgende stap om meer te weten te komen over [apparaatconnectiviteit](concepts-get-connected.md) in Azure IoT Central.
