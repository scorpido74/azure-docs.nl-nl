---
title: 'X.509-apparaat inschrijven voor Azure Device Provisioning Service met C #'
description: In deze snelstart wordt gebruikgemaakt van groepsregistraties. Schrijf in deze quickstart X.509-apparaten in voor de DPS (Azure IoT Hub Device Provisioning Service) met C#.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 64bc3921a606ab3211173b46b268ded53952c8bb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75434667"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-c"></a>Snelstart: X.509-apparaten registreren bij Device Provisioning Service met behulp van C#

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

In deze snelstart wordt uitgelegd hoe u met C# programmatisch een [registratiegroep](concepts-service.md#enrollment-group) kunt maken die gebruikmaakt van tussenliggende of hoofd-CA X.509-certificaten. De inschrijvingsgroep wordt gemaakt met behulp van de [Microsoft Azure IoT SDK voor .NET](https://github.com/Azure/azure-iot-sdk-csharp) en een voorbeeld C# .NET Core-toepassing. Een registratiegroep beheert de toegang tot de inrichtingsservice voor apparaten die een gemeenschappelijk handtekeningcertificaat in hun certificaatketen delen. Zie [Apparaattoegang tot de inrichtingsservice beheren met X.509-certificaten](./concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates) voor meer informatie. Zie [Overzicht van beveiliging op basis van X.509-CA-certificaten](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview) voor meer informatie over het gebruik vanop X.509-certificaten gebaseerde Public Key Infrastructure (PKI) met Azure IoT Hub en Device Provisioning Service. 

Deze quickstart verwacht dat u al een IoT-hub en apparaatinrichtingsservice-exemplaar hebt gemaakt. Als u deze resources nog niet hebt gemaakt, voltooit u de Service Voor [het inrichten van IoT-hub-apparaten snel instellen met de Azure-portal](./quick-setup-auto-provision.md) voordat u verdergaat met dit artikel.

Hoewel de stappen in dit artikel werken op zowel Windows- als Linux-computers, maakt dit artikel gebruik van een Windows-ontwikkelcomputer.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Visual [Studio 2019](https://www.visualstudio.com/vs/)installeren .
* Installeer [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* Installeer [Git](https://git-scm.com/download/).

## <a name="prepare-test-certificates"></a>Testcertificaten voorbereiden

Voor deze snelstart hebt u een .pem- of een .cer-bestand met het openbare gedeelte van een tussenliggend of hoofd-CA x.509-certificaat nodig. Dit certificaat moet worden geüpload naar uw inrichtingsservice en door de service worden geverifieerd.

De [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) bevat testtooling waarmee u een X.509-certificaatketen maken, een root- of intermediatecertificaat uit die keten uploaden en met de service bewijs kan leveren om het certificaat te verifiëren.

> [!CAUTION]
> Gebruik certificaten die zijn gemaakt met de SDK-tooling alleen voor ontwikkelingstests.
> Gebruik deze certificaten niet in productie.
> Ze bevatten hard-gecodeerde wachtwoorden, zoals *1234*, die verlopen na 30 dagen.
> Zie [Een x.509-CA-certificaat ophalen](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) in de documentatie van Azure IoT Hub voor meer informatie over het verkrijgen van certificaten die geschikt zijn voor productiegebruik.
>

Ga als volgt te werk om deze testtooling te gebruiken om certificaten te genereren:

1. Zoek de tagnaam voor de [nieuwste versie](https://github.com/Azure/azure-iot-sdk-c/releases/latest) van de Azure IoT C SDK.

2. Open een opdrachtprompt of Git Bash-shell en wijzig deze in een werkmap op uw computer. Voer de volgende opdrachten uit om de nieuwste versie van de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-repository te klonen. Gebruik de tag die u in de `-b` vorige stap hebt gevonden als de waarde voor de parameter:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Deze bewerking kan enkele minuten in beslag nemen.

   De testhulpmiddelen bevinden zich in de *azure-iot-sdk-c/tools/CACertificates* van de opslagplaats die u hebt gekloond.

3. Volg de stappen in [Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) (CA-testcertificaten voor voorbeelden en zelfstudies beheren).

Naast de tooling in de C SDK laat het [voorbeeld voor groepscertificaatverificatie](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample) in de *Microsoft Azure IoT SDK voor .NET* zien hoe u proof-of-possession in C# doen met een bestaand X.509 intermediate of root CA-certificaat.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>De verbindingsreeks voor de inrichtingsservice ophalen

Voor het voorbeeld in deze snelstart hebt u de verbindingsreeks voor de inrichtingsservice nodig.

1. Meld u aan bij de Azure-portal, selecteer **Alle bronnen**en vervolgens uw service voor apparaatinrichting.

1. Selecteer **Beleid voor gedeelde toegang**en kies vervolgens het toegangsbeleid dat u wilt gebruiken om de eigenschappen ervan te openen. Kopieer en sla in **Toegangsbeleid**de tekenreeks primaire sleutelverbinding op.

    ![Verbindingsreeks voor de inrichtingsservice ophalen uit de portal](media/quick-enroll-device-x509-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-enrollment-group-sample"></a>Een voorbeeld van een registratiegroep maken 

In deze sectie ziet u hoe u een .NET Core-console-app maakt die een inschrijvingsgroep toevoegt aan uw inrichtingsservice. Met enkele aanpassingen kunt u deze stappen ook volgen om een [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot) console-app te maken om aan de registratiegroep toe te voegen. Zie de [Windows IoT Core-documentatie voor ontwikkelaars](https://docs.microsoft.com/windows/iot-core/) voor meer informatie over ontwikkelen met IoT-Core.

1. Open Visual Studio en selecteer **Een nieuw project maken**. Kies **in Een nieuw project maken**de **console-app (.NET Core)** voor C#-projectsjabloon en selecteer **Volgende**.

1. Geef het project *CreateEnrollmentGroup*een naam en druk op **Maken**.

    ![Visual C# Windows Classic Desktop-project configureren](media//quick-enroll-device-x509-csharp/configure-app-vs2019.png)

1. Wanneer de oplossing wordt geopend in Visual Studio, klikt u in het deelvenster **Solution Explorer** met de rechtermuisknop op het project **Groep maken** en selecteert u **NuGet-pakketten beheren.**

1. Selecteer **in NuGet Package Manager** **Bladeren,** zoeken naar en kies **Microsoft.Azure.Devices.Provisioning.Service**en druk op **Installeren**.

    ![Sluit het venster Nuget Package Manager.](media//quick-enroll-device-x509-csharp/add-nuget.png)

   Met deze stap wordt een verwijzing naar het [Azure IoT Provisioning Service Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) NuGet-pakket en de afhankelijkheden ervan gedownload, geïnstalleerd en toegevoegd.

1. Voeg de `using` volgende instructies `using` toe na `Program.cs`de andere instructies bovenaan :

   ```csharp
   using System.Security.Cryptography.X509Certificates;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Voeg de volgende `Program` velden toe aan de klasse en breng de vermelde wijzigingen aan.  

   ```csharp
   private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
   private static string EnrollmentGroupId = "enrollmentgrouptest";
   private static string X509RootCertPath = @"{Path to a .cer or .pem file for a verified root CA or intermediate CA X.509 certificate}";
   ```

   * Vervang `ProvisioningServiceConnectionString` de tijdelijke aanduidingswaarde door de verbindingstekenreeks van de inrichtingsservice waarvoor u de inschrijving wilt maken.

   * Vervang `X509RootCertPath` de tijdelijke aanduidingswaarde door het pad naar een .pem- of .cer-bestand. Dit bestand vertegenwoordigt het openbare deel van een intermediair of hoofd-CA X.509-certificaat dat eerder is geüpload en geverifieerd met uw inrichtingsservice.

   * U de `EnrollmentGroupId` waarde optioneel wijzigen. De tekenreeks mag alleen kleine letters en afbreekstreepjes bevatten.

   > [!IMPORTANT]
   > Voor productiecode dient u op de hoogte te zijn van de volgende beveiligingsoverwegingen:
   >
   > * Hard-coding van de verbindingsreeks voor de inrichtingsservicebeheerder is in strijd met de aanbevolen beveiligingsprocedures. In plaats daarvan moet de verbindingsreeks op een veilige manier worden ondergebracht, zoals in een beveiligd configuratiebestand of in het register.
   > * Upload alleen het openbare deel van het handtekeningcertificaat. Upload nooit .pfx- (PKCS12) of .pem-bestanden met persoonlijke sleutels naar de inrichtingsservice.

1. Voeg de volgende `Program` methode toe aan de klasse. Met deze code wordt een inschrijvingsgroep item gemaakt en vervolgens wordt de `CreateOrUpdateEnrollmentGroupAsync` methode ingeschakeld `ProvisioningServiceClient` om de inschrijvingsgroep toe te voegen aan de inrichtingsservice.

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

1. Ten slotte moet u `Main` de tekst van de methode vervangen door de volgende regels:

   ```csharp
   RunSample().GetAwaiter().GetResult();
   Console.WriteLine("\nHit <Enter> to exit ...");
   Console.ReadLine();
   ```

1. Bouw de oplossing.

## <a name="run-the-enrollment-group-sample"></a>Het voorbeeld van de registratiegroep uitvoeren
  
Voer het voorbeeld uit in Visual Studio om de registratiegroep te maken. Er verschijnt een opdrachtpromptvenster en wordt bevestigingsberichten weergegeven. Bij het succesvol maken worden in het venster Opdrachtprompt de eigenschappen van de nieuwe inschrijvingsgroep weergegeven.

U controleren of de inschrijvingsgroep is gemaakt. Ga naar het overzicht van de service voor apparaatinrichting en selecteer **Inschrijvingen beheren**en selecteer vervolgens **Inschrijvingsgroepen**. U ziet nu een nieuwe registratievermelding die overeenkomt met de registratie-ID die u in het voorbeeld hebt gebruikt.

![Eigenschappen van de inschrijving in de portal](media/quick-enroll-device-x509-csharp/verify-enrollment-portal-vs2019.png)

Selecteer de vermelding om de duimafdruk van het certificaat en andere eigenschappen voor de vermelding te verifiëren.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent het voorbeeld van de C#-service te verkennen, moet u de bronnen die in deze quickstart zijn gemaakt, niet opschonen. Gebruik anders de volgende stappen om alle bronnen die door deze quickstart zijn gemaakt, te verwijderen.

1. Sluit het c#-voorbeelduitvoervenster op uw computer.

1. Navigeer naar de service Apparaatinrichting in de Azure-portal, selecteer **Inschrijvingen beheren**en selecteer vervolgens **Inschrijvingsgroepen**. Selecteer de *registratie-id* voor de inschrijvingsvermelding die u hebt gemaakt met deze snelstart en druk op **Delete**.

1. Selecteer **certificaten,** kies het certificaat dat u voor deze quickstart hebt geüpload en druk op **Delete** boven aan **certificaatgegevens**.  

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een inschrijvingsgroep gemaakt voor een X.509 intermediate of root CA-certificaat met behulp van de Azure IoT Hub Device Provisioning Service. Voor meer informatie over device provisioning, gaat u verder met de zelfstudie voor het instellen van Device Provisioning Service in Azure Portal.

> [!div class="nextstepaction"]
> [Zelfstudies over Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)
