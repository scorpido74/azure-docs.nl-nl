---
title: E-mail verzenden wanneer de deur wordt geopend met Azure-functies
description: Controleer de magnetische sensor om te detecteren wanneer een deur wordt geopend en gebruik Azure-functies om een e-mailmelding te verzenden.
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: 6bebe8ac6b9869466938600d6267fd0062c84477
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75977305"
---
# <a name="door-monitor----using-azure-functions-and-sendgrid-send-email-when-a-door-is-opened"></a>Deurmonitor - Met Azure Functions en SendGrid e-mail verzenden wanneer een deur wordt geopend           

De MXChip IoT DevKit bevat een ingebouwde magnetische sensor. In dit project detecteer je de aanwezigheid of afwezigheid van een nabijgelegen sterk magnetisch veld -- in dit geval afkomstig van een kleine, permanente magneet.

## <a name="what-you-learn"></a>Wat u leert

In dit project leert u:
- Hoe de magnetische sensor van de MXChip IoT DevKit te gebruiken om de beweging van een nabijgelegen magneet te detecteren.
- De SendGrid-service gebruiken om een melding naar uw e-mailadres te sturen.

> [!NOTE]
> Voer voor een praktisch gebruik van dit project de volgende taken uit:
> - Monteer een magneet aan de rand van een deur.
> - Monteer de DevKit op de deurklem dicht bij de magneet. Het openen of sluiten van de deur zal de sensor activeren, wat resulteert in het ontvangen van een e-mail melding van de gebeurtenis.

## <a name="what-you-need"></a>Wat u nodig hebt

Voltooi de [handleiding aan de slag](iot-hub-arduino-iot-devkit-az3166-get-started.md) voor:

* Uw DevKit hebben aangesloten op Wi-Fi
* De ontwikkelomgeving voorbereiden

Een actief Azure-abonnement. Als u er geen hebt, u zich registreren via een van deze methoden:

* Activeer een [gratis Microsoft Azure-account van 30 dagen.](https://azure.microsoft.com/free/)
* Claim uw [Azure-tegoed](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) als u een MSDN- of Visual Studio-abonnee bent.

## <a name="deploy-the-sendgrid-service-in-azure"></a>De SendGrid-service implementeren in Azure

[SendGrid](https://sendgrid.com/) is een cloudgebaseerd e-maildeliveryplatform. Deze service wordt gebruikt om e-mailmeldingen te verzenden.

> [!NOTE]
> Als u al een SendGrid-service hebt geïmplementeerd, u rechtstreeks overgaan tot [Deploy IoT Hub in Azure.](#deploy-iot-hub-in-azure)

### <a name="sendgrid-deployment"></a>SendGrid-implementatie

Als u Azure-services wilt inrichten, gebruikt u de knop **Implementeren naar Azure.** Met deze knop u uw open-sourceprojecten snel en eenvoudig implementeren naar Microsoft Azure.

Klik hieronder op de knop **Implementeren naar Azure.** 

[![Implementeren naar Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Als u nog niet bent aangemeld bij uw Azure-account, meldt u zich nu aan. 

U ziet nu het aanmeldingsformulier SendGrid.

![SendGrid-implementatie](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

Vul het aanmeldingsformulier in:

   * **Resourcegroep:** Maak een resourcegroep om de SendGrid-service te hosten of gebruik een bestaande groep. Zie [Resourcegroepen gebruiken om uw Azure-resources te beheren.](../azure-resource-manager/management/manage-resource-groups-portal.md)

   * **Naam:** de naam voor uw SendGrid-service. Kies een unieke naam, die verschilt van andere services die u mogelijk hebt.

   * **Wachtwoord**: De service vereist een wachtwoord, dat niet zal worden gebruikt voor iets in dit project.

   * **E-mail:** De SendGrid-service stuurt verificatie naar dit e-mailadres.

Controleer de optie **Vastmaken aan dashboard** om deze toepassing in de toekomst gemakkelijker te vinden en klik vervolgens op **Kopen** om het aanmeldingsformulier in te dienen.
 
### <a name="sendgrid-api-key-creation"></a>SendGrid API-sleutel maken

Nadat de implementatie is voltooid, klikt u erop en klikt u op de knop **Beheren.** De pagina SendGrid-account wordt weergegeven, waar u uw e-mailadres moet verifiëren.

![SendGrid beheren](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

Klik op de pagina SendGrid op **Instellingen** > **API-sleutels** > **API-sleutel maken**.

![SendGrid Create API First](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

Voer op de pagina **API-sleutel maken** de **API-sleutelnaam** in en klik op **& weer maken**.

![SendGrid Create API Second](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

Uw API-sleutel wordt slechts één keer weergegeven. Zorg ervoor dat u het veilig kopieert en opslaat, omdat het in de volgende stap wordt gebruikt.

## <a name="deploy-iot-hub-in-azure"></a>IoT-hub implementeren in Azure

De volgende stappen bieden andere Azure IoT-gerelateerde services en implementeren Azure-functies voor dit project.

Klik hieronder op de knop **Implementeren naar Azure.** 

[![Implementeren naar Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

Het aanmeldingsformulier wordt weergegeven.

![IoTHub-implementatie](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

Vul de velden op het aanmeldingsformulier in.

   * **Resourcegroep:** Maak een resourcegroep om de SendGrid-service te hosten of gebruik een bestaande groep. Zie [Resourcegroepen gebruiken om uw Azure-resources te beheren.](../azure-resource-manager/management/manage-resource-groups-portal.md)

   * **Iot Hub Naam:** De naam voor uw IoT hub. Kies een unieke naam, die verschilt van andere services die u mogelijk hebt.

   * **Iot Hub Sku**: F1 (beperkt tot één per abonnement) is gratis. Meer prijsinformatie vindt u op de [prijspagina.](https://azure.microsoft.com/pricing/details/iot-hub/)

   * **Van e-mail:** dit veld moet hetzelfde e-mailadres zijn dat u hebt gebruikt bij het instellen van de SendGrid-service.

Controleer de optie **Vastmaken aan dashboard** om deze toepassing in de toekomst gemakkelijker te vinden en klik vervolgens op **Kopen** wanneer u klaar bent om door te gaan naar de volgende stap.
 
## <a name="build-and-upload-the-code"></a>De code bouwen en uploaden

Laad vervolgens de voorbeeldcode in VS-code en informeer de benodigde Azure-services.

### <a name="start-vs-code"></a>VS-code starten

- Zorg ervoor dat uw DevKit **niet** is verbonden met uw computer.
- Start VS Code.
- Sluit de DevKit aan op uw computer.

> [!NOTE]
> Wanneer u VS Code start, ontvangt u mogelijk een foutbericht waarin staat dat het Arduino IDE- of gerelateerde bordpakket niet kan worden gevonden. Als u deze fout ontvangt, sluit u VS-code, start u de Arduino IDE opnieuw en moet VS-code het Arduino IDE-pad correct lokaliseren.

### <a name="open-arduino-examples-folder"></a>Map Arduino Examples openen

Vouw de sectie **ARDUINO-voorbeelden** aan de linkerkant uit, blader naar **Voorbeelden voor MXCHIP AZ3166 > AzureIoT**en selecteer **DoorMonitor**. Met deze actie wordt een nieuw VS-codevenster geopend met een projectmap erin.

![mini-oplossing-voorbeelden](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

U de voorbeeld-app ook openen vanuit het opdrachtpalet. Gebruik `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) om het opdrachtpalet te openen, **Arduino**te typen en vervolgens Arduino te zoeken en te **selecteren: Voorbeelden**.

### <a name="provision-azure-services"></a>Azure-services inrichten

Voer in het oplossingsvenster de taak voor het inrichten van de cloud uit:
- Typ `Ctrl+P` (macOS: `Cmd+P`).
- Voer `task cloud-provision` het opgegeven tekstvak in.

In de VS Code-terminal begeleidt een interactieve opdrachtregel u bij het inrichten van de vereiste Azure-services. Selecteer alle dezelfde items in de gevraagde lijst die u eerder hebt ingericht in [Deploy IoT Hub in Azure](#deploy-iot-hub-in-azure).

![Cloudvoorziening](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Als de pagina in de laadstatus blijft hangen wanneer u zich bij Azure wilt aanmelden, raadpleegt u het [gedeelte 'pagina hanges bij het inloggen' van de veelgestelde vragen over IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) om dit probleem op te lossen. 

### <a name="build-and-upload-the-device-code"></a>De apparaatcode bouwen en uploaden

Upload vervolgens de code voor het apparaat.

#### <a name="windows"></a>Windows

1. Gebruiken `Ctrl+P` om `task device-upload`uit te voeren.

2. De terminal vraagt u om de configuratiemodus in te voeren. Houd hiervoor knop A ingedrukt en druk op de resetknop en laat deze los. Het scherm geeft het DevKit-identificatienummer en het woord *Configuratie weer.*

#### <a name="macos"></a>macOS

1. Zet de DevKit in de configuratiemodus: houd knop A ingedrukt en druk op de resetknop en laat deze los. Op het scherm wordt 'Configuratie' weergegeven.

2. Klik `Cmd+P` om `task device-upload`uit te voeren .

#### <a name="verify-upload-and-run-the-sample-app"></a>De voorbeeld-app verifiëren, uploaden en uitvoeren

De verbindingstekenreeks die wordt opgehaald uit de stap [Azure-services voor voorziening](#provision-azure-services) is nu ingesteld. 

VS Code begint vervolgens met het verifiëren en uploaden van de Arduino-schets naar de DevKit.

![apparaatupload](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

De DevKit start opnieuw en begint met het uitvoeren van de code.

> [!NOTE]
> Af en toe ontvangt u een foutmelding "Fout: AZ3166: Onbekend pakket". Deze fout treedt op wanneer de index van het bordpakket niet correct wordt vernieuwd. Als u deze fout wilt oplossen, raadpleegt u het [ontwikkelgedeelte van de Veelgestelde vragen over IoT-DevKit.](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development)

## <a name="test-the-project"></a>Test het project

Het programma initialiseert eerst wanneer de DevKit in aanwezigheid van een stabiel magnetisch veld is.

Na de `Door closed` initialisatie wordt deze weergegeven op het scherm. Wanneer er een verandering in het magnetisch `Door opened`veld is, verandert de toestand in . Elke keer dat de deurstatus verandert, ontvangt u een e-mailmelding. (Het kan tot vijf minuten duren voordat deze e-mailberichten zijn ontvangen.)

![Magneten dicht bij de sensor: Deur gesloten](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "Magneten dicht bij de sensor: Deur gesloten")

![Magneet verwijderd van de sensor: Deur geopend](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "Magneet verwijderd van de sensor: Deur geopend")

## <a name="problems-and-feedback"></a>Problemen en feedback

Als u problemen ondervindt, raadpleegt u de Veelgestelde vragen over [IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) of maakt u verbinding via de volgende kanalen:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u een DevKit-apparaat aansluiten op uw Azure IoT Remote Monitoring-oplossingsversneller en hebt de SendGrid-service gebruikt om een e-mail te verzenden. Hier volgen de voorgestelde volgende stappen:

* [Overzicht van azure IoT Remote Monitoring-oplossingsversneller](https://docs.microsoft.com/azure/iot-suite/)
* [Een MXChip IoT DevKit-apparaat verbinden met uw Azure IoT Central-toepassing](/azure/iot-central/core/howto-connect-devkit)
