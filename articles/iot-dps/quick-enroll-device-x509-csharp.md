---
title: X. 509-apparaat inschrijven bij Azure Device Provisioning Service met behulp vanC#
description: In deze quickstart wordt gebruikgemaakt van groepsregistraties. In deze Snelstartgids schrijft u X. 509-apparaten in voor Azure IoT Hub Device Provisioning Service (DPS) C#met.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 1986f51b834f177a7c8d70392cc532cbfadb7170
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974730"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-c"></a>Snelstart: X.509-apparaten registreren bij Device Provisioning Service met behulp van C#

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

In deze snelstart wordt uitgelegd hoe u met C# programmatisch een [registratiegroep](concepts-service.md#enrollment-group) kunt maken die gebruikmaakt van tussenliggende of hoofd-CA X.509-certificaten. De registratie groep wordt gemaakt met behulp van de [Microsoft Azure IOT SDK voor .net](https://github.com/Azure/azure-iot-sdk-csharp) en C# een voor beeld van een .net core-toepassing. Een registratiegroep beheert de toegang tot de inrichtingsservice voor apparaten die een gemeenschappelijk handtekeningcertificaat in hun certificaatketen delen. Zie [Apparaattoegang tot de inrichtingsservice beheren met X.509-certificaten](./concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates) voor meer informatie. Zie [Overzicht van beveiliging op basis van X.509-CA-certificaten](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview) voor meer informatie over het gebruik vanop X.509-certificaten gebaseerde Public Key Infrastructure (PKI) met Azure IoT Hub en Device Provisioning Service. 

Deze Snelstartgids verwacht dat u al een IoT hub en Device Provisioning service-exemplaar hebt gemaakt. Als u deze resources nog niet hebt gemaakt, voltooit u de [Set IOT hub Device Provisioning Service met de Azure Portal](./quick-setup-auto-provision.md) Snelstartgids voordat u verdergaat met dit artikel.

Hoewel de stappen in dit artikel op zowel Windows-als Linux-computers werken, wordt in dit artikel een Windows-ontwikkel computer gebruikt.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Installeer [Visual Studio 2019](https://www.visualstudio.com/vs/).
* Installeer [.net core SDK](https://www.microsoft.com/net/download/windows).
* Installeer [Git](https://git-scm.com/download/).

## <a name="prepare-test-certificates"></a>Testcertificaten voorbereiden

Voor deze snelstart hebt u een .pem- of een .cer-bestand met het openbare gedeelte van een tussenliggend of hoofd-CA x.509-certificaat nodig. Dit certificaat moet worden geüpload naar uw inrichtingsservice en door de service worden geverifieerd.

De [Azure IOT C SDK](https://github.com/Azure/azure-iot-sdk-c) bevat hulp middelen waarmee u een X. 509-certificaat keten kunt maken, een basis-of tussen certificaat van die keten moet uploaden en het bewijs van eigendom met de service om het certificaat te verifiëren.

> [!CAUTION]
> Gebruik certificaten die zijn gemaakt met de SDK-hulpprogram ma's voor alleen ontwikkel tests.
> Gebruik deze certificaten niet in productie.
> Ze bevatten in code vastgelegde wacht woorden, zoals *1234*, die na 30 dagen verlopen.
> Zie [Een x.509-CA-certificaat ophalen](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) in de documentatie van Azure IoT Hub voor meer informatie over het verkrijgen van certificaten die geschikt zijn voor productiegebruik.
>

Voer de volgende stappen uit om deze test tool te gebruiken voor het genereren van certificaten:

1. Open een opdracht prompt venster of git bash shell en wijzig de werkmap op uw computer. Voer de volgende opdracht uit om de [Azure IOT C SDK](https://github.com/Azure/azure-iot-sdk-c) github-opslag plaats te klonen:

   ```cmd/sh
   git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
   ```

   Deze bewerking kan enkele minuten in beslag nemen.

   De testhulpmiddelen bevinden zich in de *azure-iot-sdk-c/tools/CACertificates* van de opslagplaats die u hebt gekloond.

1. Volg de stappen in [Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) (CA-testcertificaten voor voorbeelden en zelfstudies beheren).

Naast de hulp middelen in de C-SDK, laat het voor beeld van een [verificatie van groeps certificaten](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample) in de *Microsoft Azure IOT SDK voor .net* zien hoe u het bewijs C# van zijn bezit met een bestaand X. 509 tussenliggende of basis-CA-certificaat.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>De verbindingsreeks voor de inrichtingsservice ophalen

Voor het voorbeeld in deze snelstart hebt u de verbindingsreeks voor de inrichtingsservice nodig.

1. Meld u aan bij de Azure Portal, selecteer **alle resources**en vervolgens uw Device Provisioning-Service.

1. Selecteer **beleid voor gedeelde toegang**en kies vervolgens het toegangs beleid dat u wilt gebruiken om de eigenschappen te openen. Kopieer en sla de primaire sleutel connection string op in het **toegangs beleid**.

    ![Verbindingsreeks voor de inrichtingsservice ophalen uit de portal](media/quick-enroll-device-x509-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-enrollment-group-sample"></a>Een voorbeeld van een registratiegroep maken 

In deze sectie wordt beschreven hoe u een .NET Core-Console-app maakt waarmee een registratie groep wordt toegevoegd aan uw inrichtings service. Met enkele aanpassingen kunt u deze stappen ook volgen om een [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot) console-app te maken om aan de registratiegroep toe te voegen. Zie de [Windows IoT Core-documentatie voor ontwikkelaars](https://docs.microsoft.com/windows/iot-core/) voor meer informatie over ontwikkelen met IoT-Core.

1. Open Visual Studio en selecteer **een nieuw project maken**. Kies in **een nieuw project maken**de **console-app (.net core)** voor C# project sjabloon en selecteer **volgende**.

1. Geef het project de naam *CreateEnrollmentGroup*en druk vervolgens op **maken**.

    ![Klassiek C# Windows-bureau blad-project configureren](media//quick-enroll-device-x509-csharp/configure-app-vs2019.png)

1. Wanneer de oplossing wordt geopend in Visual Studio, klikt u in het deel venster **Solution Explorer** met de rechter muisknop op het project **CreateEnrollmentGroup** en selecteert u vervolgens **NuGet-pakketten beheren**.

1. Selecteer in **NuGet package manager** **Bladeren**, zoek naar en kies **micro soft. Azure. devices. provisioning. service**en druk vervolgens op **install**.

    ![Sluit het venster Nuget Package Manager.](media//quick-enroll-device-x509-csharp/add-nuget.png)

   Met deze stap wordt een verwijzing naar het [Azure IOT Provisioning Service client SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) NuGet-pakket en de bijbehorende afhankelijkheden gedownload, geïnstalleerd en toegevoegd.

1. Voeg de volgende `using`-instructies toe achter de andere `using`-instructies boven aan `Program.cs`:

   ```csharp
   using System.Security.Cryptography.X509Certificates;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Voeg de volgende velden toe aan de klasse `Program` en breng de weer gegeven wijzigingen aan.  

   ```csharp
   private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
   private static string EnrollmentGroupId = "enrollmentgrouptest";
   private static string X509RootCertPath = @"{Path to a .cer or .pem file for a verified root CA or intermediate CA X.509 certificate}";
   ```

   * Vervang de waarde van de tijdelijke aanduiding `ProvisioningServiceConnectionString` door de connection string van de inrichtings service waarvoor u de inschrijving wilt maken.

   * Vervang de waarde van de tijdelijke aanduiding `X509RootCertPath` door het pad naar een. pem-of CER-bestand. Dit bestand vertegenwoordigt het open bare deel van een tussenliggend of basis-CA X. 509-certificaat dat eerder is geüpload en geverifieerd met uw inrichtings service.

   * U kunt eventueel de `EnrollmentGroupId` waarde wijzigen. De tekenreeks mag alleen kleine letters en afbreekstreepjes bevatten.

   > [!IMPORTANT]
   > Voor productiecode dient u op de hoogte te zijn van de volgende beveiligingsoverwegingen:
   >
   > * Hard-coding van de verbindingsreeks voor de inrichtingsservicebeheerder is in strijd met de aanbevolen beveiligingsprocedures. In plaats daarvan moet de verbindingsreeks op een veilige manier worden ondergebracht, zoals in een beveiligd configuratiebestand of in het register.
   > * Upload alleen het openbare deel van het handtekeningcertificaat. Upload nooit .pfx- (PKCS12) of .pem-bestanden met persoonlijke sleutels naar de inrichtingsservice.

1. Voeg de volgende methode toe aan de klasse `Program`. Deze code maakt een vermelding voor de registratie groep en roept vervolgens de methode `CreateOrUpdateEnrollmentGroupAsync` aan op `ProvisioningServiceClient` om de registratie groep toe te voegen aan de inrichtings service.

   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");
 
       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new enrollmentGroup config
           Console.WriteLine("\nCreating a new enrollmentGroup...");
           var certificate = new X509Certificate2(X509RootCertPath);
           Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);
           EnrollmentGroup enrollmentGroup =
                   new EnrollmentGroup(
                           EnrollmentGroupId,
                           attestation)
                   {
                       ProvisioningStatus = ProvisioningStatus.Enabled
                   };
           Console.WriteLine(enrollmentGroup);
           #endregion
 
           #region Create the enrollmentGroup
           Console.WriteLine("\nAdding new enrollmentGroup...");
           EnrollmentGroup enrollmentGroupResult =
               await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);
           Console.WriteLine("\nEnrollmentGroup created with success.");
           Console.WriteLine(enrollmentGroupResult);
           #endregion
 
       }
   }
   ```

1. Vervang tot slot de hoofd tekst van de `Main` methode door de volgende regels:

   ```csharp
   RunSample().GetAwaiter().GetResult();
   Console.WriteLine("\nHit <Enter> to exit ...");
   Console.ReadLine();
   ```

1. Bouw de oplossing.

## <a name="run-the-enrollment-group-sample"></a>Het voorbeeld van de registratiegroep uitvoeren
  
Voer het voorbeeld uit in Visual Studio om de registratiegroep te maken. Er wordt een opdracht prompt venster weer gegeven en u kunt de bevestigings berichten weer geven. Wanneer het maken is voltooid, worden in het opdracht prompt venster de eigenschappen van de nieuwe registratie groep weer gegeven.

U kunt controleren of de registratie groep is gemaakt. Ga naar de Device Provisioning Service-samen vatting en selecteer **inschrijvingen beheren**en selecteer vervolgens **registratie groepen**. U ziet nu een nieuwe registratievermelding die overeenkomt met de registratie-ID die u in het voorbeeld hebt gebruikt.

![Eigenschappen van de inschrijving in de portal](media/quick-enroll-device-x509-csharp/verify-enrollment-portal-vs2019.png)

Selecteer de vermelding om de vinger afdruk van het certificaat en andere eigenschappen voor de vermelding te controleren.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent het C# service voorbeeld te verkennen, moet u de resources die u in deze Quick Start hebt gemaakt, niet opschonen. Gebruik anders de volgende stappen om alle resources te verwijderen die door deze Quick start zijn gemaakt.

1. Sluit het C# voorbeeld venster uitvoer op de computer.

1. Navigeer naar Device Provisioning Service in het Azure Portal, selecteer **inschrijvingen beheren**en selecteer vervolgens **registratie groepen**. Selecteer de *registratie-id* voor de registratie vermelding die u hebt gemaakt met behulp van deze Quick Start en druk op **Delete**.

1. Selecteer in de Azure Portal van uw Device Provisioning Service de optie **certificaten**, kies het certificaat dat u voor deze Quick Start hebt geüpload en druk op **Delete** aan de bovenkant van de **certificaat gegevens**.  

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een registratie groep voor een X. 509-tussenliggend of basis-CA-certificaat gemaakt met behulp van de Azure-IoT Hub Device Provisioning Service. Voor meer informatie over device provisioning, gaat u verder met de zelfstudie voor het instellen van Device Provisioning Service in Azure Portal.

> [!div class="nextstepaction"]
> [Zelfstudies over Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)
