---
title: Veelgestelde vragen over connected factory-oplossingen - Azure | Microsoft Documenten
description: Dit artikel beantwoordt de veelgestelde vragen voor de Connected Factory oplossingsversneller. Het bevat links naar de GitHub repository.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: c84452ff71fa34a65b2e56ec753b68bf551c7e35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73826281"
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>Veelgestelde vragen voor Connected Factory oplossingsversneller

Zie ook de algemene [FAQ](iot-accelerators-faq.md) voor IoT-oplossingsversnellers.

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>Waar vind ik de broncode voor de oplossingsversneller?

De broncode wordt opgeslagen in de volgende GitHub-repository:

* [Connected Factory-oplossingsversneller](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>Wat is OPC UA?

OPC Unified Architecture (UA), uitgebracht in 2008, is een platformonafhankelijke, servicegerichte interoperabiliteitsstandaard. OPC UA wordt gebruikt door verschillende industriële systemen en apparaten zoals pc's in de industrie, PLC's en sensoren. OPC UA integreert de functionaliteit van de OPC Classic specificaties in één uitbreidbaar framework met ingebouwde beveiliging. Het is een standaard die wordt aangestuurd door de OPC Foundation. De [OPC Foundation](https://opcfoundation.org/) is een not-for-profit organisatie met meer dan 440 leden. Het doel van de organisatie is om OPC-specificaties te gebruiken om multi-vendor, multi-platform, veilige en betrouwbare interoperabiliteit te vergemakkelijken door:

* Infrastructuur
* Specificaties
* Technologie
* Processen

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Waarom heeft Microsoft opc UA gekozen voor de Connected Factory-oplossingsversneller?

Microsoft koos voor OPC UA omdat het een open, niet-merkgebonden, platformonafhankelijk, industrie-erkende en bewezen standaard is. Het is een vereiste voor Industrie 4.0 (RAMI4.0) referentiearchitectuuroplossingen die de interoperabiliteit tussen een breed scala aan productieprocessen en apparatuur waarborgen. Microsoft ziet de vraag van haar klanten om Industrie 4.0-oplossingen te bouwen. Ondersteuning voor OPC UA helpt klanten de drempel te verlagen om hun doelen te bereiken en biedt hen direct bedrijfswaarde.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Hoe voeg ik een openbaar IP-adres toe aan de simulatie-vm?

U hebt twee opties om het IP-adres toe te voegen:

* Gebruik het PowerShell-script `Simulation/Factory/Add-SimulationPublicIp.ps1` in de [repository](https://github.com/Azure/azure-iot-connected-factory). Geef uw implementatienaam door als parameter. Gebruik voor `<your username>ConnFactoryLocal`een lokale implementatie . Het script drukt het IP-adres van de VM uit.

* Zoek in de Azure-portal de brongroep van uw implementatie. Behalve voor een lokale implementatie heeft de resourcegroep de naam die u hebt opgegeven als oplossing of implementatienaam. Voor een lokale implementatie met behulp van het `<your username>ConnFactoryLocal`buildscript is de naam van de resourcegroep . Voeg nu een nieuwe **bron voor openbaar IP-adres** toe aan de resourcegroep.

> [!NOTE]
> Zorg er in beide gevallen voor dat u de nieuwste patches installeert door de instructies op de [Ubuntu-website](https://wiki.ubuntu.com/Security/Upgrades)te volgen. Houd de installatie up-to-date zolang uw vm toegankelijk is via een openbaar IP-adres.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Hoe verwijder ik het openbare IP-adres naar de simulatie-vm?

U hebt twee opties om het IP-adres te verwijderen:

* Gebruik het PowerShell-script Simulation/Factory/Remove-SimulationPublicIp.ps1 van de [repository](https://github.com/Azure/azure-iot-connected-factory). Geef uw implementatienaam door als parameter. Gebruik voor `<your username>ConnFactoryLocal`een lokale implementatie . Het script drukt het IP-adres van de VM uit.

* Zoek in de Azure-portal de brongroep van uw implementatie. Behalve voor een lokale implementatie heeft de resourcegroep de naam die u hebt opgegeven als oplossing of implementatienaam. Voor een lokale implementatie met behulp van het `<your username>ConnFactoryLocal`buildscript is de naam van de resourcegroep . Verwijder nu de bron **van het ip-adres** openbaar uit de brongroep.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Hoe meld ik me aan bij de simulatie-VM?

Aanmelden bij de simulatie-VM wordt alleen ondersteund als u uw `build.ps1` oplossing hebt geïmplementeerd met het PowerShell-script in de [repository.](https://github.com/Azure/azure-iot-connected-factory)

Als u de oplossing vanuit www.azureiotsolutions.com hebt geïmplementeerd, u zich niet aanmelden bij de VM. U zich niet aanmelden, omdat het wachtwoord willekeurig wordt gegenereerd en u het niet resetten.

1. Voeg een openbaar IP-adres toe aan de virtuele machine. Zie [Hoe voeg ik een openbaar IP-adres toe aan de simulatie-VM?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Maak een SSH-sessie op uw VM met behulp van het IP-adres van de vm.
1. De gebruikersnaam om `docker`te gebruiken is: .
1. Het te gebruiken wachtwoord is afhankelijk van de versie die u hebt gebruikt om te implementeren:
    * Voor oplossingen die vóór 1 juni 2017 zijn geïmplementeerd `Passw0rd`met het script build.ps1, is het wachtwoord: .
    * Voor oplossingen die na 1 juni 2017 zijn geïmplementeerd met het script `<name of your deployment>.config.user` build.ps1, vindt u het wachtwoord in het bestand. Het wachtwoord wordt opgeslagen in de instelling **VmAdminPassword.** Het wachtwoord wordt willekeurig gegenereerd tijdens de implementatie, tenzij u het opgeeft met behulp van de `build.ps1` scriptparameter`-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Hoe stop en start ik alle dockerprocessen in de simulatie-VM?

1. Meld u aan bij de simulatie-VM. Zie [Hoe meld ik me aan bij de simulatie-VM?](#how-do-i-sign-in-to-the-simulation-vm)
1. Voer uit welke containers actief `docker ps`zijn: .
1. Voer alle simulatiecontainers uit `./stopsimulation`om alle simulatiecontainers te stoppen: .
1. Ga als een start voor alle simulatiecontainers:
    * Exporteer een shellvariabele met de naam **IOTHUB_CONNECTIONSTRING**. Gebruik de waarde van de instelling **IotHubOwnerConnectionString** in het `<name of your deployment>.config.user` bestand. Bijvoorbeeld:

        ```sh
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Voer `./startsimulation` uit.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Hoe werk ik de simulatie in de VM bij?

Als u wijzigingen in de simulatie hebt aangebracht, `build.ps1` kunt u het `updatedimulation` PowerShell-script in de [opslagplaats](https://github.com/Azure/azure-iot-connected-factory) gebruiken met behulp van de opdracht. Dit script bouwt alle simulatiecomponenten, stopt de simulatie in de VM, uploadt, installeert en start ze.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Hoe kom ik achter de verbindingsreeks van de IoT-hub die door mijn oplossing wordt gebruikt?

Als u uw oplossing `build.ps1` hebt geïmplementeerd met het script in de [repository,](https://github.com/Azure/azure-iot-connected-factory)is `<name of your deployment>.config.user` de verbindingstekenreeks de waarde van **iotHubOwnerConnectionString** in het bestand.

U de verbindingstekenreeks ook vinden via de Azure-portal. Zoek in de IoT Hub-bron in de resourcegroep van uw implementatie de verbindingstekenreeksinstellingen.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Welke IoT Hub-apparaten gebruikt de Connected Factory-simulatie?

De simulatie registreert zelf de volgende apparaten:

* proxy.beijing.corp.contoso
* proxy.capetown.corp.contoso
* proxy.mumbai.corp.contoso
* proxy.munich0.corp.contoso
* proxy.rio.corp.contoso
* proxy.seattle.corp.contoso
* publisher.beijing.corp.contoso
* uitgever.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Met behulp van de [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) of [de IoT-extensie voor Azure CLI](https://github.com/Azure/azure-iot-cli-extension) u controleren welke apparaten zijn geregistreerd met de IoT-hub die uw oplossing gebruikt. Als u device explorer wilt gebruiken, hebt u de verbindingstekenreeks voor de IoT-hub in uw implementatie nodig. Als u de IoT-extensie voor Azure CLI wilt gebruiken, hebt u de naam van uw IoT-hub nodig.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Hoe kan ik loggegevens krijgen van de simulatiecomponenten?

Alle componenten in de simulatieloginformatie worden aangemeld om bestanden in te loggen. Deze bestanden zijn te vinden in `home/docker/Logs`de VM in de map. Als u de logboeken wilt ophalen, u het PowerShell-script `Simulation/Factory/Get-SimulationLogs.ps1` in de opslagplaats [gebruiken.](https://github.com/Azure/azure-iot-connected-factory)

Dit script moet zich aanmelden bij de VM. Mogelijk moet u referenties voor de aanmelding verstrekken. Zie [Hoe meld ik me aan bij de simulatie-VM?](#how-do-i-sign-in-to-the-simulation-vm)

Het script voegt/verwijdert een openbaar IP-adres aan de VM, als het er nog geen heeft en verwijdert het. Het script plaatst alle logbestanden in een archief en downloadt het archief naar uw ontwikkelingswerkstation.

U ook inloggen op de VM via SSH en de logbestanden tijdens runtime inspecteren.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Hoe kan ik controleren of de simulatie gegevens naar de cloud stuurt?

Met de monitorgebeurtenissen [van DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) of [Azure IoT CLI Extension](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-monitor-events) u de gegevens die vanaf bepaalde apparaten naar IoT Hub worden verzonden, inspecteren. Als u deze hulpprogramma's wilt gebruiken, moet u de verbindingstekenreeks voor de IoT-hub in uw implementatie kennen. Zie [Hoe kom ik achter de verbindingsreeks van de IoT-hub die door mijn oplossing wordt gebruikt?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Controleer de gegevens die worden verzonden door een van de uitgeversapparaten:

* publisher.beijing.corp.contoso
* uitgever.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Als u geen gegevens ziet die naar IoT Hub worden verzonden, is er een probleem met de simulatie. Als eerste analysestap moet u de logbestanden van de simulatiecomponenten analyseren. Zie [Hoe kan ik logboekgegevens krijgen van de simulatiecomponenten?](#how-can-i-get-log-data-from-the-simulation-components) Probeer vervolgens te stoppen en start de simulatie en als er nog steeds geen gegevens worden verzonden, werkt u de simulatie volledig bij. Zie [Hoe werk ik de simulatie in de VM bij?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Hoe schakel ik een interactieve kaart in mijn Connected Factory-oplossing in?

Als u een interactieve kaart wilt inschakelen in uw Connected Factory-oplossing, moet u een Azure Maps-account hebben.

Bij het implementeren vanuit [www.azureiotsolutions.com](https://www.azureiotsolutions.com)voegt het implementatieproces een Azure Maps-account toe aan de brongroep die de oplossingsversnellerservices bevat.

Wanneer u het `build.ps1` script implementeert in de GitHub-repository verbonden fabriek, stelt u de omgevingsvariabele `$env:MapApiQueryKey` in het bouwvenster in op de sleutel van uw Azure [Maps-account.](../azure-maps/how-to-manage-account-keys.md) De interactieve kaart wordt dan automatisch ingeschakeld.

U na de implementatie ook een Azure Maps-accountsleutel toevoegen aan uw oplossingsversneller. Navigeer naar de Azure-portal en krijg toegang tot de App Service-bron in uw verbonden fabrieksimplementatie. Navigeer naar **toepassingsinstellingen**, waar u een sectie **Toepassingsinstellingen**vindt. Stel de **MapApiQueryKey** in op de [sleutel van uw Azure Maps-account.](../azure-maps/how-to-manage-account-keys.md) Sla de instellingen op en navigeer vervolgens naar **Overzicht** en start de App-service opnieuw.

### <a name="how-do-i-create-an-azure-maps-account"></a>Hoe maak ik een Azure Maps-account?

Zie, [Hoe u uw Azure Maps-account en -sleutels beheert.](../azure-maps/how-to-manage-account-keys.md)

### <a name="how-to-obtain-your-azure-maps-account-key"></a>Uw Azure Maps-accountsleutel verkrijgen

Zie, [Hoe u uw Azure Maps-account en -sleutels beheert.](../azure-maps/how-to-manage-account-keys.md)

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Hoe schakel je de interactieve kaart in terwijl je lokaal debugt?

Als u de interactieve kaart wilt inschakelen terwijl u lokaal `MapApiQueryKey` debugt, stelt u de waarde van de instelling in de bestanden `local.user.config` en `<yourdeploymentname>.user.config` in de hoofdmap van uw implementatie in op de waarde van de **querysleutel die** u eerder hebt gekopieerd.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Hoe gebruik ik een andere afbeelding op de startpagina van mijn dashboard?

Als u de statische afbeelding als de startpagina van `WebApp\Content\img\world.jpg`het dashboard wilt wijzigen, vervangt u de afbeelding . Vervolgens u de WebApp opnieuw opbouwen en opnieuw implementeren.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Hoe gebruik ik niet-OPC UA-apparaten met Connected Factory?

Ga als lid van het werk om telemetriegegevens van niet-OPC UA-apparaten naar Connected Factory te verzenden:

1. [Configureer een nieuw station in de topologie van verbonden fabrieks](iot-accelerators-connected-factory-configure.md) in het `ContosoTopologyDescription.json` bestand.

1. Inname van de telemetriegegevens in de INDELING VERBONDEN Fabriek-compatibele JSON-indeling:

    ```json
    [
      {
        "ApplicationUri": "<the_value_of_OpcUri_of_your_station",
        "DisplayName": "<name_of_the_datapoint>",
        "NodeId": "value_of_NodeId_of_your_datapoint_in_the_station",
        "Value": {
          "Value": <datapoint_value>,
          "SourceTimestamp": "<timestamp>"
        }
      }
    ]
    ```

1. Het formaat `<timestamp>` van is:`2017-12-08T19:24:51.886753Z`

1. Start de Verbonden Fabrieksapp-service opnieuw.

### <a name="next-steps"></a>Volgende stappen

U kunt ook enkele van de andere functies en mogelijkheden van de IoT-oplossingsversnellers bekijken:

* [Een overzicht van de oplossingsversneller voor Predictief onderhoud](iot-accelerators-predictive-overview.md)
* [Connected Factory-oplossingsversneller implementeren](quickstart-connected-factory-deploy.md)
* [Fundamentele IoT-beveiliging](/azure/iot-fundamentals/iot-security-ground-up)
