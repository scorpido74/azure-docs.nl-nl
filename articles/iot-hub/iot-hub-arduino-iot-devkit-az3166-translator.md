---
title: Maak vertaler w/Azure-functies, cognitieve services, IoT-devKit
description: Gebruik de microfoon op een IoT DevKit om een spraakbericht te ontvangen en gebruik Azure Cognitive Services om het in het Engels te verwerken tot vertaalde tekst
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: liydu
ms.openlocfilehash: 8e840a1ae7161ea3e7b370889a1f0fb648ca120e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953360"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>IoT DevKit AZ3166 gebruiken met Azure-functies en cognitieve services om een taalvertaler te maken

In dit artikel leert u hoe u IoT DevKit als taalvertaler maken met azure [cognitive services.](https://azure.microsoft.com/services/cognitive-services/) Het neemt uw stem op en vertaalt deze naar Engelse tekst die wordt weergegeven op het DevKit-scherm.

De [MXChip IoT DevKit](https://aka.ms/iot-devkit) is een alles-in-één Arduino-compatibel bord met rijke randapparatuur en sensoren. U hiervoor ontwikkelen met [Azure IoT Device Workbench](https://aka.ms/iot-workbench) of [Azure IoT Tools](https://aka.ms/azure-iot-tools) extension pack in Visual Studio Code. De [projectcatalogus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) bevat voorbeeldtoepassingen om u te helpen iot-oplossingen te prototypen.

## <a name="before-you-begin"></a>Voordat u begint

Voer eerst de volgende taken uit om de stappen in deze zelfstudie uit te voeren:

* Bereid uw DevKit voor door de stappen te volgen in [Connect IoT DevKit AZ3166 in azure IoT Hub in de cloud.](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)

## <a name="create-azure-cognitive-service"></a>Azure Cognitive Service maken

1. Klik in de Azure-portal op **Een bron maken** en zoeken naar **Spraak**. Vul het formulier in om de spraakservice te maken.
  ![Speech Service](media/iot-hub-arduino-iot-devkit-az3166-translator/speech-service.png)

1. Ga naar de spraakservice die u zojuist hebt gemaakt, klik op de sectie **Sleutels** om de **Key1** voor DevKit-toegang tot de service te kopiëren en op te schrijven.
  ![Toetsen kopiëren](media/iot-hub-arduino-iot-devkit-az3166-translator/copy-keys.png)

## <a name="open-sample-project"></a>Voorbeeldproject openen

1. Zorg ervoor dat uw IoT DevKit **niet is verbonden met** uw computer. Start VS Code eerst en sluit de DevKit aan op uw computer.

1. Klik `F1` hierom het opdrachtpalet te openen, de **Werkbank voor Azure IoT-apparaat te**typen en te selecteren: Voorbeelden openen... . Selecteer vervolgens **IoT DevKit** als bord.

1. Zoek op de pagina Voorbeeld van IoT-werkbank **devKit-vertaler** en klik op **Voorbeeld openen**. Hiermee selecteert u het standaardpad om de voorbeeldcode te downloaden.
  ![Voorbeeld openen](media/iot-hub-arduino-iot-devkit-az3166-translator/open-sample.png)

## <a name="use-speech-service-with-azure-functions"></a>Spraakservice gebruiken met Azure-functies

1. Klik in VS-code op `F1` **Azure IoT Device Workbench: Azure Services inrichten...**. ![Azure-services inrichten](media/iot-hub-arduino-iot-devkit-az3166-translator/provision.png)

1. Volg de stappen om de inrichting van Azure IoT Hub en Azure-functies af te ronden.
   ![Voorzieningsstappen](media/iot-hub-arduino-iot-devkit-az3166-translator/provision-steps.png)

   Noteer de naam van het Azure IoT Hub-apparaat dat u hebt gemaakt.

1. Open `Functions\DevKitTranslatorFunction.cs` en werk de volgende regels code bij met de apparaatnaam en spraakservicesleutel die u hebt genoteerd.
   ```csharp
   // Subscription Key of Speech Service
   const string speechSubscriptionKey = "";

   // Region of the speech service, see https://docs.microsoft.com/azure/cognitive-services/speech-service/regions for more details.
   const string speechServiceRegion = "";

   // Device ID
   const string deviceName = "";
   ```

1. Klik `F1`op **Azure IoT Device Workbench,** typ en selecteer deze: implementeren in Azure... . Als VS Code om bevestiging voor herschikking vraagt, klikt u op **Ja**.
   ![Waarschuwing implementeren](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-warning.png)

1. Controleer of de implementatie is geslaagd.
   ![Succes implementeren](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-success.png)

1. Ga in azure-portal naar de sectie **Apps-functies** en zoek de zojuist gemaakte Azure-functie-app. Klik `devkit_translator`op **</> Functie-URL ophalen** om de URL te kopiëren.
   ![URL van functie kopiëren](media/iot-hub-arduino-iot-devkit-az3166-translator/get-function-url.png)

1. Plak de `azure_config.h` URL in het bestand.
   ![Azure config](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-config.png)

   > [!NOTE]
   > Als de functie-app niet goed werkt, controleert u deze [veelgestelde vragen](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) om deze op te lossen.

## <a name="build-and-upload-device-code"></a>Apparaatcode bouwen en uploaden

1. Schakel de DevKit over naar **de configuratiemodus** door:
   * Ingedrukt houden knop **A**.
   * Druk op de **knop Reset** en laat deze vrijgeven.

   U ziet het scherm toont de DevKit ID en **configuratie**.

   ![DevKit-configuratiemodus](media/iot-hub-arduino-iot-devkit-az3166-translator/devkit-configuration-mode.png)

1. Klik `F1`op , typ en selecteer **Azure IoT Device Workbench: Device Settings configureren... > Config Device Connection String**. Selecteer **De verbindingstekenreeks van IoT-hub-apparaat selecteren** om deze te configureren naar de DevKit.
   ![Verbindingstekenreeks configureren](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string.png)

1. U ziet de melding zodra deze is uitgevoerd.
   ![Succes van verbindingstekenreeksconfigureren](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string-success.png)

1. Klik `F1` nogmaals, typ en selecteer **Azure IoT Device Workbench: Device Code uploaden**. Het begint met compileren en uploaden van de code naar DevKit.
   ![Apparaatupload](media/iot-hub-arduino-iot-devkit-az3166-translator/device-upload.png)

## <a name="test-the-project"></a>Test het project

Volg na de initialisatie van de app de instructies op het DevKit-scherm. De standaardbrontaal is Chinees.

Ga als een andere taal voor vertaling naar een andere taal:

1. Druk op knop A om de installatiemodus in te voeren.

2. Druk op knop B om door alle ondersteunde brontalen te bladeren.

3. Druk op knop A om uw keuze van de brontaal te bevestigen.

4. Houd tijdens het spreken druk op knop B ingedrukt en laat vervolgens knop B los om de vertaling te starten.

5. De vertaalde tekst in het Engels verschijnt op het scherm.

![Schuiven om taal te selecteren](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Vertaalresultaat](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

Op het scherm met het vertaalresultaat u het als:

- Druk op knoppen A en B om te scrollen en selecteer de brontaal.

- Druk op de B-knop om te praten. Als u de stem wilt verzenden en de vertaaltekst wilt krijgen, laat u de B-knop los.

## <a name="how-it-works"></a>Hoe werkt het?

![mini-oplossing-voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

De IoT DevKit registreert uw stem en plaatst vervolgens een HTTP-verzoek om Azure-functies te activeren. Azure Functions roept de API voor cognitieve servicespraakom de vertaling uit te voeren. Nadat Azure Functions de vertaaltekst heeft ontvangen, wordt een C2D-bericht naar het apparaat verzonden. Vervolgens wordt de vertaling op het scherm weergegeven.

## <a name="problems-and-feedback"></a>Problemen en feedback

Als u problemen ondervindt, raadpleegt u de Veelgestelde vragen over [IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) of neem contact met ons op via de volgende kanalen:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u de IoT DevKit als vertaler gebruiken met Azure-functies en cognitieve services. In deze how-to, leerde je hoe:

> [!div class="checklist"]
> * De taak Visual Studio Code gebruiken om cloudbepalingen te automatiseren
> * Azure IoT-verbindingstekenreeks configureren
> * De Azure-functie implementeren
> * De vertaling van het spraakbericht testen

Ga naar de andere tutorials om te leren:

> [!div class="nextstepaction"]
> [Sluit IoT DevKit AZ3166 aan op Azure IoT Remote Monitoring solution accelerator](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)
