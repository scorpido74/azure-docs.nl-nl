---
title: Een Azure IoT-hub maken met de REST API van resourceprovider | Microsoft Documenten
description: Meer informatie over het programmatisch gebruik maken en beheren van de resourceprovider C# REST API.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: c4cb230c9f0b56e3ff9d81e0d85134a7f192e6e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75429164"
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Een IoT-hub maken met de REST API (.NET) van de resourceprovider

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

U de [REST API van IoT Hub-bronprovider](https://docs.microsoft.com/rest/api/iothub/iothubresource) gebruiken om Azure IoT-hubs programmatisch te maken en te beheren. In deze zelfstudie ziet u hoe u de REST API van IoT Hub-bronprovider gebruiken om een IoT-hub te maken vanuit een C#-programma.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Visual Studio.

* Een actief Azure-account. Als je nog geen account hebt, kun je binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aanmaken.

* [Azure PowerShell 1.0](https://docs.microsoft.com/powershell/azure/install-Az-ps) of hoger.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Uw Visual Studio-project voorbereiden

1. Maak in Visual Studio een Visual C# Windows Classic Desktop-project met de projectsjabloon **Console App (.NET Framework).** Geef het project **CreateIoTHubREST**een naam.

2. Klik in Solution Explorer met de rechtermuisknop op uw project en klik vervolgens op **NuGet-pakketten beheren.**

3. Schakel in NuGet Package Manager **prerelease opnemen**en ga in de **zoekpagina Bladeren** naar **Microsoft.Azure.Management.ResourceManager**. Selecteer het pakket, klik op **Installeren**, klik in **Wijzigingen controleren** op **OK**en klik vervolgens op **Ik accepteer** om de licenties te accepteren.

4. Zoek in NuGet Package Manager naar **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Klik **op Installeren**, klik in Wijzigingen **controleren** op **OK**en klik vervolgens op **Ik accepteer** om de licentie te accepteren.

5. Vervang in Program.cs de bestaande **gebruiksinstructies** door de volgende code:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```

6. Voeg in Program.cs de volgende statische variabelen toe die de tijdelijke aanduidingswaarden vervangen. U hebt eerder in deze zelfstudie een notitie gemaakt van **ApplicationId,** **SubscriptionId,** **TenantId**en **Password.** De naam van de **brongroep** is de naam van de resourcegroep die u gebruikt wanneer u de IoT-hub maakt. U een reeds bestaande of een nieuwe resourcegroep gebruiken. **IoT Hub-naam** is de naam van de IoT-hub die u maakt, zoals **MyIoTHub.** De naam van uw IoT-hub moet wereldwijd uniek zijn. **Implementatienaam** is een naam voor de implementatie, zoals **Deployment_01**.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";

    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```
   
    [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>De REST-API van resourceprovider gebruiken om een IoT-hub te maken

Gebruik de [REST API voor IoT Hub-bronprovider](https://docs.microsoft.com/rest/api/iothub/iothubresource) om een IoT-hub in uw brongroep te maken. U de REST API van resourceprovider ook gebruiken om wijzigingen aan te brengen in een bestaande IoT-hub.

1. Voeg de volgende methode toe aan Program.cs:

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. Voeg de volgende code toe aan de **createIoTHub-methode.** Met deze code wordt een **HttpClient-object** gemaakt met het verificatietoken in de kopteksten:

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Voeg de volgende code toe aan de **createIoTHub-methode.** Deze code beschrijft de IoT-hub om een JSON-weergave te maken en genereert deze. Zie [Azure Status](https://azure.microsoft.com/status/)voor de huidige lijst met locaties die IoT Hub ondersteunen:

    ```csharp
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };

    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. Voeg de volgende code toe aan de **createIoTHub-methode.** Deze code dient het REST-verzoek in bij Azure. De code controleert vervolgens het antwoord en haalt de URL op die u gebruiken om de status van de implementatietaak te controleren:

    ```csharp
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;

    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }

    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. Voeg de volgende code toe aan het einde van de **CreateIoTHub-methode.** Deze code gebruikt het **asyncStatusUri-adres** dat in de vorige stap is opgehaald om te wachten tot de implementatie is voltooid:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Voeg de volgende code toe aan het einde van de **CreateIoTHub-methode.** Met deze code worden de sleutels opgehaald van de IoT-hub die u hebt gemaakt en wordt deze afgedrukt op de console:

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>De toepassing voltooien en uitvoeren

U de toepassing nu voltooien door de **CreateIoTHub-methode** aan te roepen voordat u deze bouwt en uitvoert.

1. Voeg de volgende code toe aan het einde van de **hoofdmethode:**

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. Klik **op Bouwen** en vervolgens op Oplossing **bouwen**. Eventuele fouten corrigeren.

3. Klik **op Foutopsporing** en **begin vervolgens met Foutopsporing** om de toepassing uit te voeren. Het kan enkele minuten duren voordat de implementatie is uitgevoerd.

4. Ga naar de [Azure-portal](https://portal.azure.com/) en bekijk uw lijst met bronnen om te controleren of uw toepassing de nieuwe IoT-hub heeft toegevoegd. U ook de **get-AzResource** PowerShell-cmdlet gebruiken.

> [!NOTE]
> In deze voorbeeldtoepassing wordt een S1 Standard IoT Hub toegevoegd waarvoor u wordt gefactureerd. Wanneer u klaar bent, u de IoT-hub verwijderen via de [Azure-portal](https://portal.azure.com/) of met de cmdlet **Remove-AzResource** PowerShell wanneer u klaar bent.

## <a name="next-steps"></a>Volgende stappen

Nu u een IoT-hub hebt geïmplementeerd met behulp van de REST API voor resourceprovider, u verder verkennen:

* Lees meer over de mogelijkheden van de REST API van [IoT Hub-bronprovider](https://docs.microsoft.com/rest/api/iothub/iothubresource).

* Lees [het overzicht van Azure Resource Manager](../azure-resource-manager/management/overview.md) voor meer informatie over de mogelijkheden van Azure Resource Manager.

Zie de volgende artikelen voor meer informatie over het ontwikkelen voor IoT Hub:

* [Inleiding tot C SDK](iot-hub-device-sdk-c-intro.md)

* [SDK’s voor Azure IoT](iot-hub-devguide-sdks.md)

Zie:

* [AI implementeren op Edge-apparaten met Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)