---
title: Een Twitter-bericht ophalen met Azure-functies | Microsoft Documenten
description: Gebruik de bewegingssensor om schudden te detecteren en Azure-functies te gebruiken om een willekeurige tweet te vinden met een hashtag die u opgeeft
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/07/2018
ms.author: liydu
ms.openlocfilehash: dc4ff35ff04680e8635d54c25212c8ae639ae472
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60779741"
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>Schudden, schudden voor een tweet - Een Twitter-bericht ophalen met Azure-functies

In dit project leert u hoe u de bewegingssensor gebruiken om een gebeurtenis te activeren met Azure-functies. De app haalt een willekeurige tweet op met een #hashtag die u configureert in uw Arduino-schets. De tweet wordt weergegeven op het DevKit-scherm.

## <a name="what-you-need"></a>Wat u nodig hebt

Voltooi de [handleiding aan de slag](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) voor:

* Laat je DevKit verbonden zijn met Wifi.
* Bereid de ontwikkelomgeving voor.

Een actief Azure-abonnement. Als u er geen hebt, u zich registreren via een van de volgende methoden:

* Een [gratis Microsoft Azure-account van 30 dagen activeren](https://azure.microsoft.com/free/)
* Uw [Azure-tegoed claimen](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) als u een MSDN- of Visual Studio-abonnee bent

## <a name="open-the-project-folder"></a>De projectmap openen

Begin met het openen van de projectmap. 

### <a name="start-vs-code"></a>VS-code starten

* Zorg ervoor dat uw DevKit is aangesloten op uw computer.

* Start VS Code.

* Sluit de DevKit aan op uw computer.

   > [!NOTE]
   > Wanneer u VS Code start, ontvangt u mogelijk een foutmelding dat het Arduino IDE- of gerelateerde bordpakket niet kan worden gevonden. Als deze fout optreedt, sluit u VS Code en start u de Arduino IDE opnieuw. VS Code moet nu het Arduino IDE-pad correct lokaliseren.

### <a name="open-the-arduino-examples-folder"></a>De map Arduino Examples openen

Vouw de sectie **ARDUINO-voorbeelden** aan de linkerkant uit, blader naar **Voorbeelden voor MXCHIP AZ3166 > AzureIoT**en selecteer **ShakeShake**. Er wordt een nieuw VS-codevenster geopend waarin de projectmap wordt weergegeven. Als u de sectie MXCHIP AZ3166 niet zien, controleert u of uw apparaat goed is aangesloten en start u Visual Studio Code opnieuw op.  
de ![mini-oplossing-voorbeelden](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

U het voorbeeldproject ook openen vanuit het opdrachtpalet. Klik `Ctrl+Shift+P` op (macOS: `Cmd+Shift+P`) om het opdrachtpalet te openen, **Arduino**te typen en vervolgens Arduino te zoeken en te **selecteren: Voorbeelden**.

## <a name="provision-azure-services"></a>Azure-services inrichten

Voer in het oplossingsvenster `Ctrl+P` uw taak `Cmd+P`uit `task cloud-provision`(macOS: ) door het invoeren van .

In de VS Code-terminal begeleidt een interactieve opdrachtregel u bij het inrichten van de vereiste Azure-services:

![cloudvoorziening](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Als de pagina in de laadstatus blijft hangen wanneer u zich bij Azure wilt aanmelden, raadpleegt u de [stap 'aanmeldingspagina hangt' in de veelgestelde vragen over IoT DevKit.](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure)
 
## <a name="modify-the-hashtag"></a>De #hashtag wijzigen

Open `ShakeShake.ino` en zoek naar deze coderegel:

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

Vervang de `iot` tekenreeks binnen de krullende beugels door de hashtag van uw voorkeur. De DevKit haalt later een willekeurige tweet op met de hashtag die u in deze stap opgeeft.

## <a name="deploy-azure-functions"></a>Azure Functions implementeren

Gebruik `Ctrl+P` (macOS: `Cmd+P`) `task cloud-deploy` om uit te voeren om te beginnen met het implementeren van de Azure-functiecode:

![cloud-deploy](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> Af en toe werkt de Azure-functie mogelijk niet goed. Als u dit probleem wilt oplossen wanneer dit zich voordoet, controleert u het [gedeelte 'compilatiefout' van de veelgestelde vragen over IoT DevKit.](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function)

## <a name="build-and-upload-the-device-code"></a>De apparaatcode bouwen en uploaden

Bouw en upload vervolgens de apparaatcode.

### <a name="windows"></a>Windows

1. Gebruiken `Ctrl+P` om `task device-upload`uit te voeren.

2. De terminal vraagt u om de configuratiemodus in te voeren. Dit doet u als volgt:

   * Ingedrukt houden knop A

   * Druk op de resetknop en laat deze los.

3. Het scherm toont de DevKit ID en 'Configuration'.

### <a name="macos"></a>macOS

1. Zet de DevKit in de configuratiemodus:

   Houd knop A ingedrukt en druk op de resetknop en laat deze los. Op het scherm wordt 'Configuratie' weergegeven.

2. Hiermee `Cmd+P` moet `task device-upload` u de verbindingstekenreeks instellen die `task cloud-provision` uit de stap wordt opgehaald.

### <a name="verify-upload-and-run"></a>Verifiëren, uploaden en uitvoeren

Nu is de verbindingstekenreeks ingesteld, wordt de app geverifieerd en geüpload en vervolgens uitgevoerd. 

1. VS Code begint met het verifiëren en uploaden van de Arduino-schets naar je DevKit:

   ![apparaatupload](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)

2. De DevKit start opnieuw en begint met het uitvoeren van de code.

U een foutmelding Fout: AZ3166: Onbekend pakket krijgen. Deze fout treedt op wanneer de index van het bordpakket niet correct is vernieuwd. Als u dit probleem wilt oplossen, controleert u de [fout 'onbekend pakket' in de Veelgestelde vragen over IoT DevKit.](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development)

## <a name="test-the-project"></a>Test het project

Na de initialisatie van de app klikt u op knop A en laat u deze los en schudt u vervolgens voorzichtig het DevKit-bord. Met deze actie wordt een willekeurige tweet opgehaald, die de hashtag bevat die u eerder hebt opgegeven. Binnen een paar seconden wordt een tweet weergegeven op je DevKit-scherm:

### <a name="arduino-application-initializing"></a>Arduino applicatie initialiseren ...

![Arduino-application-initialiseren](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>Druk op A om te schudden...

![Druk-A-to-shake](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>Klaar om te schudden...

![Kant-en-klaar](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>Verwerking...

![Wordt verwerkt](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>Druk op B om te lezen...

![Druk-B-te-lezen](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>Een willekeurige tweet weergeven...

![Display-a-random-tweet](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- Druk nogmaals op knop A en schud vervolgens voor een nieuwe tweet.
- Druk op knop B om door de rest van de tweet te bladeren.

## <a name="how-it-works"></a>Hoe werkt het?

![schema](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

De Arduino-schets stuurt een gebeurtenis naar de Azure IoT Hub. Met deze gebeurtenis wordt de Azure Functions-app geactiveerd. De Azure Functions-app bevat de logica om verbinding te maken met de API van Twitter en een tweet op te halen. Vervolgens wordt de tweettekst in een C2D-bericht (Cloud-to-device) verpakt en terugnaar het apparaat teruggestuurd.

## <a name="optional-use-your-own-twitter-bearer-token"></a>Optioneel: Gebruik je eigen Twitter-dragertoken

Voor testdoeleinden maakt dit voorbeeldproject gebruik van een vooraf geconfigureerd Twitter-token aan toonder. Er is echter een [tarieflimiet](https://dev.twitter.com/rest/reference/get/search/tweets) voor elk Twitter-account. Als u wilt overwegen uw eigen token te gebruiken, voert u de volgende stappen uit:

1. Ga naar [twitter Developer portal](https://dev.twitter.com/) om een nieuwe Twitter-app te registreren.

2. [Ontvang consumentensleutel en consumentengeheimen](https://support.yapsody.com/hc/en-us/articles/360003291573-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-) van uw app.

3. Gebruik [wat nut](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/) om een Twitter-drager token te genereren van deze twee sleutels.

4. Ga in de [Azure-portal](https://portal.azure.com/){:target="_blank"}naar de **resourcegroep** en zoek de Azure-functie (Type: App Service) voor uw project 'Schudden, schudden'. De naam bevat altijd 'shake...' Tekenreeks.

   ![azure-functie](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. Werk de `run.csx` code voor binnen **functies > shakeshake-cs** met uw eigen token:

   ```csharp
   string authHeader = "Bearer " + "[your own token]";
   ```
  
   ![twitter-token](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. Sla het bestand op en klik op **Uitvoeren**.

## <a name="problems-and-feedback"></a>Problemen en feedback

Problemen oplossen of feedback geven. 

### <a name="problems"></a>Problemen

Een probleem dat je zou kunnen zien als het scherm geeft 'Geen Tweets' terwijl elke stap is uitgevoerd met succes. Deze voorwaarde gebeurt normaal gesproken de eerste keer dat u het voorbeeld implementeert en uitvoert, omdat de functie-app overal van een paar seconden tot wel een minuut nodig heeft om de app koud te starten. 

Of, bij het uitvoeren van de code, zijn er een aantal blips die een herstart van de app veroorzaken. Wanneer deze voorwaarde zich voordoet, kan de apparaat-app een time-out krijgen voor het ophalen van de tweet. In dit geval u een of beide methoden proberen om het probleem op te lossen:

1. Klik op de resetknop op de DevKit om de apparaat-app opnieuw uit te voeren.

2. Zoek in de [Azure-portal](https://portal.azure.com/)de Azure Functions-app die u hebt gemaakt en start deze opnieuw op:

   ![azure-function-opnieuw opstarten](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

### <a name="feedback"></a>Feedback

Als u andere problemen ondervindt, raadpleegt u de Veelgestelde vragen over [IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) of neemt u contact met ons op via de volgende kanalen:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een DevKit-apparaat aansluiten op uw Azure IoT Remote Monitoring-oplossingsversneller en een tweet ophalen, volgen hier de voorgestelde volgende stappen:

* [Overzicht van azure IoT Remote Monitoring-oplossingsversneller](https://docs.microsoft.com/azure/iot-suite/)