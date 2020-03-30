---
title: Een Azure IoT-hub maken met een sjabloon (.NET) | Microsoft Documenten
description: Een Azure Resource Manager-sjabloon gebruiken om een IoT-hub met een C#-programma te maken.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: 02e814a9da320d688fe57edf3a3fe0640b8f5a47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75976741"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>Een IoT-hub maken met azure resource manager-sjabloon (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

U Azure Resource Manager gebruiken om Azure IoT-hubs programmatisch te maken en te beheren. In deze zelfstudie ziet u hoe u een Azure Resource Manager-sjabloon gebruiken om een IoT-hub te maken vanuit een C#-programma.

> [!NOTE]
> Azure heeft twee verschillende implementatiemodellen voor het maken en werken met resources: [Azure Resource Manager en classic](../azure-resource-manager/management/deployment-models.md).  In dit artikel wordt het implementatiemodel azure resource manager gebruikt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Visual Studio.
* Een actief Azure-account. <br/>Als je nog geen account hebt, kun je binnen een paar minuten een [gratis account][lnk-free-trial] aanmaken.
* Een [Azure Storage-account][lnk-storage-account] waar u uw Azure Resource Manager-sjabloonbestanden opslaan.
* [Azure PowerShell 1.0][lnk-powershell-install] of hoger.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Uw Visual Studio-project voorbereiden

1. Maak in Visual Studio een Visual C# Windows Classic Desktop-project met de projectsjabloon **Console App (.NET Framework).** Geef het project **CreateIoTHub**een naam .

2. Klik in Solution Explorer met de rechtermuisknop op uw project en klik vervolgens op **NuGet-pakketten beheren.**

3. Schakel in NuGet Package Manager **prerelease opnemen**en ga in de **zoekpagina Bladeren** naar **Microsoft.Azure.Management.ResourceManager**. Selecteer het pakket, klik op **Installeren**, klik in **Wijzigingen controleren** op **OK**en klik vervolgens op **Ik accepteer** om de licenties te accepteren.

4. Zoek in NuGet Package Manager naar **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Klik **op Installeren**, klik in Wijzigingen **controleren** op **OK**en klik vervolgens op **Ik accepteer** om de licentie te accepteren.

5. Vervang in Program.cs de bestaande **gebruiksinstructies** door de volgende code:

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. Voeg in Program.cs de volgende statische variabelen toe die de tijdelijke aanduidingswaarden vervangen. U hebt eerder in deze zelfstudie een notitie gemaakt van **ApplicationId,** **SubscriptionId,** **TenantId**en **Password.** **Uw Azure Storage-accountnaam** is de naam van het Azure Storage-account waar u uw Azure Resource Manager-sjabloonbestanden opslaat. De naam van de **brongroep** is de naam van de resourcegroep die u gebruikt wanneer u de IoT-hub maakt. De naam kan een reeds bestaande of nieuwe resourcegroep zijn. **Implementatienaam** is een naam voor de implementatie, zoals **Deployment_01**.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-a-template-to-create-an-iot-hub"></a>Een sjabloon verzenden om een IoT-hub te maken

Gebruik een JSON-sjabloon en parameterbestand om een IoT-hub in uw brongroep te maken. U ook een Azure Resource Manager-sjabloon gebruiken om wijzigingen aan te brengen in een bestaande IoT-hub.

1. Klik in Solution Explorer met de rechtermuisknop op uw project, klik op **Toevoegen**en klik vervolgens op **Nieuw item**. Voeg een JSON-bestand met de naam **template.json** toe aan uw project.

2. Als u een standaard IoT-hub wilt toevoegen aan de **regio Oost-VS,** vervangt u de inhoud van **template.json** door de volgende resourcedefinitie. Zie [Azure Status][lnk-status]voor de huidige lijst met regio's die IoT Hub ondersteunen:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

3. Klik in Solution Explorer met de rechtermuisknop op uw project, klik op **Toevoegen**en klik vervolgens op **Nieuw item**. Voeg een JSON-bestand genaamd **parameters.json** toe aan uw project.

4. Vervang de inhoud van **parameters.json** door de volgende parametergegevens die een naam voor de nieuwe IoT-hub instelt, zoals **{uw initialen}mynewiothub**. De naam van de IoT-hub moet wereldwijd uniek zijn, dus het moet uw naam of initialen bevatten:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```
   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

5. Maak in **Server Explorer**verbinding met uw Azure-abonnement en maak in uw Azure Storage-account een container met de naam **sjablonen.** Stel in het deelvenster **Eigenschappen** de machtigingen voor **openbaar leestoegang** voor de **container sjablonen** in op **Blob**.

6. Klik in **Server Explorer**met de rechtermuisknop op de container **met sjablonen** en klik vervolgens op **Blobcontainer weergeven**. Klik op de knop **Blob uploaden,** selecteer de twee bestanden, **parameters.json** en **templates.json**en klik vervolgens op **Openen** om de JSON-bestanden naar de **sjablonencontainer** te uploaden. De URL's van de blobs die de JSON-gegevens bevatten, zijn:

    ```csharp
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. Voeg de volgende methode toe aan Program.cs:

    ```csharp
    static void CreateIoTHub(ResourceManagementClient client)
    {

    }
    ```

8. Voeg de volgende code toe aan de **methode CreateIoTHub** om de sjabloon- en parameterbestanden in te dienen bij Azure Resource Manager:

    ```csharp
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

9. Voeg de volgende code toe aan de **createIoTHub-methode** die de status en de sleutels voor de nieuwe IoT-hub weergeeft:

    ```csharp
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>De toepassing voltooien en uitvoeren

U de toepassing nu voltooien door de **CreateIoTHub-methode** aan te roepen voordat u deze bouwt en uitvoert.

1. Voeg de volgende code toe aan het einde van de **hoofdmethode:**

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. Klik **op Bouwen** en vervolgens op Oplossing **bouwen**. Eventuele fouten corrigeren.

3. Klik **op Foutopsporing** en **begin vervolgens met Foutopsporing** om de toepassing uit te voeren. Het kan enkele minuten duren voordat de implementatie is uitgevoerd.

4. Als u wilt controleren of uw toepassing de nieuwe IoT-hub heeft toegevoegd, gaat u naar de [Azure-portal][lnk-azure-portal] en bekijkt u uw lijst met bronnen. U ook de **get-AzResource** PowerShell-cmdlet gebruiken.

> [!NOTE]
> In deze voorbeeldtoepassing wordt een S1 Standard IoT Hub toegevoegd waarvoor u wordt gefactureerd. U de IoT-hub verwijderen via de [Azure-portal][lnk-azure-portal] of met de cmdlet **Remove-AzResource** PowerShell wanneer u klaar bent.

## <a name="next-steps"></a>Volgende stappen
Nu u een IoT-hub hebt geïmplementeerd met behulp van een Azure Resource Manager-sjabloon met een C#-programma, u verder verkennen:

* Lees meer over de mogelijkheden van de REST API van [IoT Hub-bronprovider][lnk-rest-api].
* Lees [het overzicht van Azure Resource Manager][lnk-azure-rm-overview] voor meer informatie over de mogelijkheden van Azure Resource Manager.
* Zie [Microsoft.Devices-brontypen](/azure/templates/microsoft.devices/iothub-allversions)voor de syntaxis en eigenschappen van JSON in sjablonen.

Zie de volgende artikelen voor meer informatie over het ontwikkelen voor IoT Hub:

* [Inleiding tot C SDK][lnk-c-sdk]
* [SDK’s voor Azure IoT][lnk-sdks]

Zie:

* [AI implementeren op Edge-apparaten met Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-storage-account]:../storage/common/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
