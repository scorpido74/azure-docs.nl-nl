---
title: 'TPM-apparaat inschrijven voor Azure Device Provisioning Service met C #'
description: Snelstart - TPM-apparaat inschrijven voor Azure IoT Hub Device Provisioning Service (DPS) met Behulp van C#-service SDK. In deze snelstart wordt gebruikgemaakt van afzonderlijke inschrijvingen.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: ee1b803459e0c81b86021b617a29e0b29ee19909
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74976839"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-c-service-sdk"></a>Snelstart: TPM-apparaat inschrijven voor IoT Hub Device Provisioning Service met C#-service SDK

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

In dit artikel ziet u hoe u een afzonderlijke inschrijving voor een TPM-apparaat in de Azure IoT Hub Device Provisioning Service programmatisch maakt met behulp van de [C# Service SDK](https://github.com/Azure/azure-iot-sdk-csharp) en een voorbeeld C# .NET Core-toepassing. U optioneel een gesimuleerd TPM-apparaat inschrijven voor de inrichtingsservice met behulp van deze afzonderlijke inschrijvingsvermelding. Hoewel deze stappen werken op zowel Windows- als Linux-computers, maakt dit artikel gebruik van een Windows-ontwikkelcomputer.

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

1. Controleer of Visual [Studio 2019](https://www.visualstudio.com/vs/) op uw computer is geïnstalleerd.

1. Controleer of de [.NET Core SDK](https://www.microsoft.com/net/download/windows) op uw computer is geïnstalleerd.

1. Voer de stappen uit in De Service voor [het inrichten van IoT Hub-apparaten instellen met de Azure-portal](./quick-setup-auto-provision.md) voordat u verdergaat.

1. (Optioneel) Als u een gesimuleerd apparaat wilt inschrijven aan het einde van deze quickstart, volgt u de procedure in [Een gesimuleerd TPM-apparaat maken en inrichten met C# device SDK](quick-create-simulated-device-tpm-csharp.md) tot de stap waar u een goedkeuringssleutel voor het apparaat krijgt. Sla de goedkeuringssleutel, registratie-id en, optioneel, de apparaat-ID op, omdat u deze later in deze snelstart moet gebruiken.

   > [!NOTE]
   > Volg de stappen niet om een individuele inschrijving te maken met behulp van de Azure-portal.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>De verbindingsreeks voor de inrichtingsservice ophalen

Voor het voorbeeld in deze snelstart hebt u de verbindingsreeks voor de inrichtingsservice nodig.

1. Meld u aan bij de Azure-portal, selecteer **Alle bronnen**en vervolgens uw service voor apparaatinrichting.

1. Kies **Beleid voor gedeelde toegang**en selecteer vervolgens het toegangsbeleid dat u wilt gebruiken om de eigenschappen ervan te openen. Kopieer en sla in **Toegangsbeleid**de tekenreeks primaire sleutelverbinding op.

    ![Verbindingsreeks voor de inrichtingsservice ophalen uit de portal](media/quick-enroll-device-tpm-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-individual-enrollment-sample"></a>Het voorbeeld van de afzonderlijke inschrijving maken

In dit gedeelte ziet u hoe u een .NET Core-console-app maakt die een afzonderlijke inschrijving voor een TPM-apparaat toevoegt aan uw inrichtingsservice. Met enkele aanpassingen kunt u deze stappen ook volgen om een [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot) console-app te maken om aan de afzonderlijke registratie toe te voegen. Zie [Windows IoT Core-ontwikkelaarsdocumentatie](https://docs.microsoft.com/windows/iot-core/)voor meer informatie over ontwikkelen met IoT Core.

1. Open Visual Studio en selecteer **Een nieuw project maken**. Kies **in Een nieuw project maken**de projectsjabloon Console App **(.NET Core)** voor C# en selecteer **Volgende**.

1. Geef het project *CreateTpmEnrollment*een naam en druk op **Maken**.

    ![Visual C# Windows Classic Desktop-project configureren](media/quick-enroll-device-tpm-csharp/configure-tpm-app-vs2019.png)

1. Wanneer de oplossing wordt geopend in Visual Studio, klikt u in het deelvenster **Solution Explorer** met de rechtermuisknop op het project **CreateTpmEnrollment.** Selecteer **NuGet-pakketten beheren**.

1. Selecteer **in NuGet Package Manager** **Bladeren,** zoeken naar en kies **Microsoft.Azure.Devices.Provisioning.Service**en druk op **Installeren**.

   ![Sluit het venster Nuget Package Manager.](media//quick-enroll-device-tpm-csharp/add-nuget.png)

   Met deze stap wordt een verwijzing naar het [Azure IoT Provisioning Service Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) NuGet-pakket en de afhankelijkheden ervan gedownload, geïnstalleerd en toegevoegd.

1. Voeg de `using` volgende instructies `using` toe na `Program.cs`de andere instructies bovenaan :
  
   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Voeg de volgende `Program` velden toe aan de klasse en maak de onderstaande wijzigingen.

   ```csharp
   private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
   private const string RegistrationId = "sample-registrationid-csharp";
   private const string TpmEndorsementKey =
       "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
       "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
       "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
       "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
       "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
       
   // Optional parameters
   private const string OptionalDeviceId = "myCSharpDevice";
   private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
   ```

   * Vervang `ProvisioningServiceConnectionString` de tijdelijke aanduidingswaarde door de verbindingstekenreeks van de inrichtingsservice waarvoor u de inschrijving wilt maken.

   * U kunt desgewenst de registratie-ID, goedkeuringssleutel, apparaat-ID en inrichtingsstatus wijzigen.

   * Als u deze quickstart gebruikt samen met het [apparaat Maken en inrichten van een gesimuleerd TPM-apparaat met C#-apparaat SDK](quick-create-simulated-device-tpm-csharp.md) snel start om een gesimuleerd apparaat in te richten, vervangt u de goedkeuringssleutel en registratie-id door de waarden die u in die quickstart hebt opgemerkt. U de apparaat-id vervangen door de waarde die in die quickstart wordt voorgesteld, uw eigen waarde gebruiken of de standaardwaarde in dit voorbeeld gebruiken.

1. Voeg de volgende `Program` methode toe aan de klasse.  Met deze code wordt afzonderlijke `CreateOrUpdateIndividualEnrollmentAsync` inschrijvingsinvoer `ProvisioningServiceClient` gemaakt en wordt de methode aanroept om de afzonderlijke inschrijving toe te voegen aan de inrichtingsservice.

   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");

       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new individualEnrollment config
           Console.WriteLine("\nCreating a new individualEnrollment...");
           Attestation attestation = new TpmAttestation(TpmEndorsementKey);
           IndividualEnrollment individualEnrollment =
                   new IndividualEnrollment(
                           RegistrationId,
                           attestation);

           // The following parameters are optional. Remove them if you don't need them.
           individualEnrollment.DeviceId = OptionalDeviceId;
           individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;
           #endregion

           #region Create the individualEnrollment
           Console.WriteLine("\nAdding new individualEnrollment...");
           IndividualEnrollment individualEnrollmentResult =
               await provisioningServiceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);
           Console.WriteLine("\nIndividualEnrollment created with success.");
           Console.WriteLine(individualEnrollmentResult);
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

## <a name="run-the-individual-enrollment-sample"></a>Het voorbeeld van de afzonderlijke inschrijving uitvoeren
  
Voer het voorbeeld uit in Visual Studio om de afzonderlijke registratie voor uw TPM-apparaat te maken.

Er verschijnt een opdrachtpromptvenster en wordt bevestigingsberichten weergegeven. Bij het succesvol maken worden in het venster Opdrachtprompt de eigenschappen van de nieuwe afzonderlijke inschrijving weergegeven.

U controleren of de afzonderlijke inschrijving is gemaakt. Ga naar het overzicht van de service voor apparaatinrichting en selecteer **Inschrijvingen beheren**en selecteer **Vervolgens Afzonderlijke inschrijvingen**. U ziet nu een nieuwe registratievermelding die overeenkomt met de registratie-ID die u in het voorbeeld hebt gebruikt.

![Eigenschappen van de inschrijving in de portal](media/quick-enroll-device-tpm-csharp/verify-enrollment-portal-vs2019.png)

Selecteer het item om de goedkeuringssleutel en andere eigenschappen voor de vermelding te verifiëren.

Als u de stappen in het [apparaat maken en inrichten met C# device SDK](quick-create-simulated-device-tpm-csharp.md) snel start, u doorgaan met de resterende stappen in die quickstart om uw gesimuleerde apparaat in te schrijven. Volg niet de stappen voor het maken van een afzonderlijke inschrijving via Azure Portal.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent het voorbeeld van de C#-service te verkennen, moet u de bronnen die in deze quickstart zijn gemaakt, niet opschonen. Gebruik anders de volgende stappen om alle bronnen die door deze quickstart zijn gemaakt, te verwijderen.

1. Sluit het c#-voorbeelduitvoervenster op uw computer.

1. Navigeer naar de service Apparaatinrichting in de Azure-portal, selecteer Inschrijvingen beheren en schakel het tabblad **Individuele inschrijvingen** in. Schakel het selectievakje in naast de *registratie-id* voor het **inschrijvingsitem**dat u met deze quickstart hebt gemaakt en druk op de knop **Verwijderen** boven aan het deelvenster.

1. Als u de stappen hebt gevolgd in [Een gesimuleerd TPM-apparaat maken en inrichten met C# device SDK](quick-create-simulated-device-tpm-csharp.md) om een gesimuleerd TPM-apparaat te maken, gaat u als volgt te werk:

    1. Sluit het venster van de TPM-simulator en het voorbeelduitvoervenster voor het gesimuleerde apparaat.

    1. Navigeer in Azure Portal naar de IoT Hub waar het apparaat is ingericht. Schakel in het menu onder **Explorers**De optie **IoT-apparaten**in, schakel het selectievakje in naast de *apparaat-id* van het apparaat dat u in deze quickstart hebt geregistreerd en druk vervolgens op de knop **Verwijderen** boven aan het deelvenster.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u programmatisch een afzonderlijke inschrijvingsvermelding voor een TPM-apparaat gemaakt. Optioneel hebt u een TPM-gesimuleerd apparaat op uw computer gemaakt en deze in gerichte tijd ingerichte op uw IoT-hub met behulp van de Azure IoT Hub Device Provisioning Service. Voor meer informatie over device provisioning, gaat u verder met de zelfstudie voor het instellen van Device Provisioning Service in Azure Portal.

> [!div class="nextstepaction"]
> [Zelfstudies over Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)
