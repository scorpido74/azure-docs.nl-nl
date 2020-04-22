---
title: Zelfstudie voor X.509-beveiliging in Azure IoT-hub | Microsoft Documenten
description: Ga aan de slag met de x.509-gebaseerde beveiliging in uw Azure IoT-hub in een gesimuleerde omgeving.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: amqp
ms.openlocfilehash: ac45cf42ed174d3e9423b4ea39cadf16b84897ef
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759649"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>X. 509-beveiliging instellen in uw Azure IoT-hub

In deze zelfstudie ziet u de stappen die u nodig hebt om uw Azure IoT-hub te beveiligen met behulp van de *X.509-certificaatverificatie.* Ter illustratie gebruiken we de open-sourcetool OpenSSL om certificaten lokaal op uw Windows-machine te maken. We raden u aan deze zelfstudie alleen voor testdoeleinden te gebruiken. Voor de productieomgeving moet u de certificaten kopen bij een *basiscertificaatinstantie (CA).*

## <a name="prerequisites"></a>Vereisten

Deze zelfstudie vereist dat u de volgende bronnen gereed hebt:

* U hebt een IoT-hub gemaakt met uw Azure-abonnement. Zie [Een IoT-hub maken via portal](iot-hub-create-through-portal.md) voor gedetailleerde stappen.

* Visual [Studio 2017 of Visual Studio 2019](https://www.visualstudio.com/vs/) zijn geïnstalleerd.

## <a name="get-x509-ca-certificates"></a>X.509 CA-certificaten

De X.509-certificaatgebaseerde beveiliging in de IoT Hub vereist dat u begint met een [X.509-certificaatketen,](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification)die het basiscertificaat en eventuele tussentijdse certificaten tot aan het bladcertificaat bevat.

U een van de volgende manieren kiezen om uw certificaten te krijgen:

* X.509-certificaten kopen bij een *basiscertificaatinstantie (CA).* Deze methode wordt aanbevolen voor productieomgevingen.

* Maak uw eigen X.509-certificaten met behulp van een tool van derden, zoals [OpenSSL.](https://www.openssl.org/) Deze techniek is prima voor test- en ontwikkelingsdoeleinden. Zie [CA-certificaten voor testen beheren voor voorbeelden en zelfstudies](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) voor informatie over het genereren van CA-testcertificaten met PowerShell of Bash. De rest van deze zelfstudie maakt gebruik van test CA-certificaten die worden gegenereerd door de instructies in [Het beheren van test CA-certificaten voor voorbeelden en zelfstudies](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

* Genereer een [X.509 intermediate CA-certificaat](iot-hub-x509ca-overview.md#sign-devices-into-the-certificate-chain-of-trust) dat is ondertekend door een bestaand basis-CA-certificaat en upload het naar de hub. Zodra het tussentijdse certificaat is geüpload en geverifieerd, zoals hieronder geïnstrueerd, kan het worden gebruikt in de plaats van een root CA-certificaat hieronder vermeld. Tools zoals OpenSSL[(openssl req](https://www.openssl.org/docs/man1.1.0/man1/req.html) en [openssl ca)](https://www.openssl.org/docs/man1.1.0/man1/ca.html)kunnen worden gebruikt om een tussenliggend CA-certificaat te genereren en te ondertekenen.

> [!NOTE]
> Upload de hoofdmap van derden niet als deze niet uniek voor u is, omdat andere klanten van de derde partij hun apparaten kunnen aansluiten op uw IoT-hub.

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>X.509 CA-certificaten registreren bij uw IoT-hub

Met deze stappen u via de portal een nieuwe certificaatautoriteit toevoegen aan uw IoT-hub.

1. Navigeer in de Azure-portal naar uw IoT-hub en selecteer > **Instellingencertificaten** voor de hub. **Settings**

1. Selecteer **Toevoegen** om een nieuw certificaat toe te voegen.

1. Voer **in Certificaatnaam**een vriendelijke weergavenaam in en selecteer het certificaatbestand dat u in de vorige sectie hebt gemaakt op uw computer.

1. Zodra u een melding ontvangt dat uw certificaat is geüpload, selecteert u **Opslaan**.

    ![Certificaat uploaden](./media/iot-hub-security-x509-get-started/iot-hub-add-cert.png)  

   Uw certificaat wordt weergegeven in de lijst met certificaten met de status **van niet-geverifieerd**.

1. Selecteer het certificaat dat u zojuist hebt toegevoegd om **certificaatgegevens**weer te geven en selecteer **vervolgens Verificatiecode genereren**.

   ![Certificaat verifiëren](./media/iot-hub-security-x509-get-started/copy-verification-code.png)  

1. Kopieer de **verificatiecode** naar het klembord. U gebruikt het om het certificaateigendom te valideren.

1. Volg stap 3 in [Het beheren van CA-certificaten voor testen voor voorbeelden en zelfstudies.](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)  Dit proces ondertekent uw verificatiecode met de private key-gekoppeld aan uw X.509 CA-certificaat, dat een handtekening genereert. Er zijn tools beschikbaar om dit ondertekeningsproces uit te voeren, bijvoorbeeld OpenSSL. Dit proces staat bekend als het [bewijs van bezit](https://tools.ietf.org/html/rfc5280#section-3.1).

1. Zoek en open het handtekeningbestand in **certificaatgegevens**onder **verificatiecertificaat .pem of .cer.** Selecteer vervolgens **Verifiëren**.

   De status van uw certificaat wordt gewijzigd **in Geverifieerd.** Selecteer **Vernieuwen** als het certificaat niet automatisch wordt bijgewerkt.

## <a name="create-an-x509-device-for-your-iot-hub"></a>Maak een X.509-apparaat voor uw IoT-hub

1. Navigeer in de Azure-portal naar uw IoT-hub en selecteer **vervolgens Explorers** > **IoT-apparaten**.

1. Selecteer **Nieuw** om een nieuw apparaat toe te voegen.

1. Voer **in apparaat-ID**een vriendelijke weergavenaam in. Kies **bij Verificatie type** **X.509 CA Ondertekend**en selecteer **Opslaan**.

   ![X.509-apparaat maken in portal](./media/iot-hub-security-x509-get-started/new-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Uw X.509-apparaat verifiëren met de X.509-certificaten

Als u uw X.509-apparaat wilt verifiëren, moet u het apparaat eerst ondertekenen met het CA-certificaat. De ondertekening van bladapparaten gebeurt normaal gesproken in de fabriek, waar productiegereedschappen dienovereenkomstig zijn ingeschakeld. Als het apparaat van de ene fabrikant naar de andere gaat, wordt de ondertekeningsactie van elke fabrikant vastgelegd als een tussencertificaat binnen de keten. Het resultaat is een certificaatketen van het CA-certificaat tot het bladcertificaat van het apparaat. Stap 4 in [Het beheren van test CA-certificaten voor voorbeelden en zelfstudies](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) genereert een apparaatcertificaat.

Vervolgens laten we u zien hoe u een C#-toepassing maakt om het X.509-apparaat te simuleren dat is geregistreerd voor uw IoT-hub. We sturen temperatuur- en vochtigheidswaarden van het gesimuleerde apparaat naar uw hub. In deze zelfstudie maken we alleen de apparaattoepassing. Het wordt overgelaten als een oefening aan de lezers om de IoT Hub-servicetoepassing te maken die antwoord stuurt op de gebeurtenissen die door dit gesimuleerde apparaat worden verzonden. De C#-toepassing gaat ervan uit dat u de stappen hebt gevolgd in [Het beheren van ca-certificaten voor testen voor voorbeelden en zelfstudies.](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)

1. Open Visual Studio, selecteer **Een nieuw project maken**en kies vervolgens de projectsjabloon **console-app (.NET Framework).** Selecteer **Next**.

1. Geef in **Uw nieuwe project configureren**een naam aan het project *SimulateX509Device*en selecteer **Vervolgens Maken**.

   ![X.509-apparaatproject maken in Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. Klik in Solution Explorer met de rechtermuisknop op het project **SimulateX509Device** en selecteer **NuGet-pakketten beheren**.

1. Selecteer **in NuGet Package Manager** **Bladeren** en zoeken naar en kies **Microsoft.Azure.Devices.Client**. Selecteer **Installeren**.

   ![Apparaat SDK NuGet-pakket toevoegen in Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    Met deze stap wordt een verwijzing naar het SDK NuGet-pakket voor Azure IoT-apparaten en de afhankelijkheden ervan gedownload, geïnstalleerd en toegevoegd.

1. Voeg aan het begin van het bestand **Program.cs** de volgende `using` instructies toe:

    ```csharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

1. Voeg de volgende velden toe aan de klasse **Programma:**

    ```csharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

    Gebruik de vriendelijke apparaatnaam die u in de vorige sectie hebt gebruikt in plaats van _<your_device_id>_.

1. Voeg de volgende functie toe om willekeurige getallen voor temperatuur en vochtigheid te maken en stuur deze waarden naar de hub:

    ```csharp
    static async Task SendEvent(DeviceClient deviceClient)
    {
        string dataBuffer;
        Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

        for (int count = 0; count < MESSAGE_COUNT; count++)
        {
            temperature = rnd.Next(20, 35);
            humidity = rnd.Next(60, 80);
            dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
            Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
            eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
            Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

            await deviceClient.SendEventAsync(eventMessage);
        }
    }
    ```

1. Voeg ten slotte de volgende regels code toe aan de **hoofdfunctie,** ter vervanging van de _tijdelijke aanduidingen apparaat-id,_ _uw-iot-hub-naam_en _absoluut-pad-naar-uw-apparaat-pfx-bestand_ zoals vereist door uw setup.

    ```csharp
    try
    {
        var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
        var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
        var deviceClient = DeviceClient.Create("<your-iot-hub-name>.azure-devices.net", auth, TransportType.Amqp_Tcp_Only);

        if (deviceClient == null)
        {
            Console.WriteLine("Failed to create DeviceClient!");
        }
        else
        {
            Console.WriteLine("Successfully created DeviceClient!");
            SendEvent(deviceClient).Wait();
        }

        Console.WriteLine("Exiting...\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

   Deze code maakt verbinding met uw IoT-hub door de verbindingstekenreeks voor uw X.509-apparaat te maken. Eenmaal met succes aangesloten, stuurt het vervolgens temperatuur- en vochtigheidsgebeurtenissen naar de hub en wacht op de reactie.

1. Voer de app uit. Omdat deze toepassing toegang heeft tot een *.pfx-bestand,* moet u deze app mogelijk als beheerder uitvoeren.

   1. Bouw de Visual Studio-oplossing.

   1. Open een nieuw opdrachtpromptvenster met **Uitvoeren als beheerder**.  

   1. Navigeer naar de map met uw oplossing en navigeer vervolgens naar het *pad bin/Foutopsporing* in de oplossingsmap.

   1. Voer de toepassing **SimulateX509Device.exe** uit de opdrachtprompt.

   U moet zien dat uw apparaat verbinding maakt met de hub en de gebeurtenissen verzendt.

   ![Apparaat-app uitvoeren](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het beveiligen van uw IoT-oplossing:

* [Aanbevolen procedures voor IoT-beveiliging](../iot-fundamentals/iot-security-best-practices.md)

* [IoT-beveiligingsarchitectuur](../iot-fundamentals/iot-security-architecture.md)

* [Uw IoT-implementatie beveiligen](../iot-fundamentals/iot-security-deployment.md)

Zie:

* [AI implementeren op Edge-apparaten met Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
