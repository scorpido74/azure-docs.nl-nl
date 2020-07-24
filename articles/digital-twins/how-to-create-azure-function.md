---
title: Een Azure-functie instellen om gegevens te verwerken
titleSuffix: Azure Digital Twins
description: Zie een Azure-functie maken die kan worden geopend en geactiveerd door Digital apparaatdubbels.
author: cschormann
ms.author: cschorm
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 3e284bc76da9ca40341d72f772aa7ee947a11638
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87124303"
---
# <a name="connect-azure-functions-apps-for-processing-data"></a>Azure Functions-apps verbinden voor het verwerken van gegevens

Tijdens de preview-fase wordt het bijwerken van digitale apparaatdubbels op basis van gegevens verwerkt met behulp van [**gebeurtenis routes**](concepts-route-events.md) via Compute-resources, zoals [Azure functions](../azure-functions/functions-overview.md). Een Azure-functie kan worden gebruikt om een digitale dubbele taak bij te werken als reactie op:
* telemetrie-gegevens van apparaten die afkomstig zijn van IoT Hub
* eigenschaps wijziging of andere gegevens die afkomstig zijn van een andere digitale twee in het dubbele diagram

Dit artikel helpt u bij het maken van een Azure-functie voor gebruik met Azure Digital Apparaatdubbels. 

Hier volgt een overzicht van de stappen die het bevat:

1. Een Azure Functions-app maken in Visual Studio
2. Een Azure-functie schrijven met een [Event grid](../event-grid/overview.md) trigger
3. Voeg verificatie code toe aan de functie (om toegang te krijgen tot Azure Digital Apparaatdubbels)
4. De functie-app publiceren in azure
5. [Beveiligings](concepts-security.md) toegang instellen. Als de Azure-functie een toegangs token tijdens runtime moet ontvangen om toegang tot de service te krijgen, moet u Managed Service Identity configureren voor de functie-app.

In de rest van dit artikel worden de stappen van de Azure function-installatie beschreven. een voor een.

## <a name="create-an-azure-functions-app-in-visual-studio"></a>Een Azure Functions-app maken in Visual Studio

Selecteer in Visual Studio 2019 *bestand > nieuw project*. Zoek de *Azure functions* -sjabloon, Selecteer deze en druk op volgende.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-new-project.png" alt-text="Visual Studio: dialoog venster Nieuw project":::

Geef een naam op voor de functie-app en druk op maken.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-config.png" alt-text="Visual Studio: dialoog venster Project configureren":::

Selecteer de *trigger Event grid* en druk op ' maken '.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-trigger.png" alt-text="Visual Studio: dialoog venster activering van Azure function-project":::

## <a name="write-an-azure-function-with-an-event-grid-trigger"></a>Een Azure-functie schrijven met een Event Grid trigger

Met de volgende code maakt u een korte Azure-functie die u kunt gebruiken om gebeurtenissen te registreren: 

```csharp
// Default URL for triggering Event Grid function in the local environment
// http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
using System;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;

namespace FunctionSample
{
    public static class FooFunction    {
        [FunctionName("Foo")]
        public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Dit is de basis functie van Azure.

### <a name="run-and-debug-the-azure-function-app"></a>De Azure-functie-app uitvoeren en fouten opsporen

U kunt nu de functie compileren en uitvoeren. Hoewel Azure functions uiteindelijk bedoeld zijn om te worden uitgevoerd in de Cloud, kunt u Azure-functies ook lokaal uitvoeren en fouten opsporen.

Zie [*Debug Event grid trigger lokaal*](../azure-functions/functions-debug-event-grid-trigger-local.md)voor meer informatie hierover.

### <a name="add-the-azure-digital-twins-sdk-to-your-azure-function-app"></a>De Azure Digital Apparaatdubbels SDK toevoegen aan uw Azure-functie-app

De functie-app communiceert met Azure Digital Apparaatdubbels met behulp [van de Azure IOT Digital-client bibliotheek voor .net (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). Als u de SDK wilt gebruiken, moet u de volgende pakketten in uw project toevoegen:
* `Azure.DigitalTwins.Core`(versie `1.0.0-preview.2` )
* `Azure.Identity`

Als u de Azure SDK-pijp lijn zo wilt configureren dat deze correct wordt ingesteld voor Azure Functions, hebt u het volgende nodig:
* `Azure.Net.Http`
* `Azure.Core`

Afhankelijk van uw hulp middelen kunt u dit doen met behulp van Visual Studio package manager of het `dotnet` opdracht regel programma. 

Voeg de volgende using-instructies toe aan uw Azure-functie.

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
using System.Net.Http;
using Azure.Core.Pipeline;
```

## <a name="add-authentication-code-to-the-azure-function"></a>Verificatie code toevoegen aan de Azure-functie

Voeg vervolgens een verificatie code toe waarmee de functie toegang kan krijgen tot Azure Digital Apparaatdubbels.

Voeg variabelen toe aan uw functie klasse voor deze waarden: 
* De Azure Digital Apparaatdubbels App-ID
* De URL van uw Azure Digital Apparaatdubbels-exemplaar. Het is een goed idee om de service-URL te lezen uit een omgevings variabele in plaats van deze op te harden in de functie.
* Een statische variabele die een httpclient maakt-exemplaar kan bevatten. Httpclient maakt is relatief kostbaar en we willen voor komen dat u dit hoeft te doen voor elke functie aanroep.

Voeg ook een lokale variabele in uw functie toe om uw Azure Digital Apparaatdubbels-client exemplaar te bewaren voor het functie project. Maak *geen* statische variabele in uw klasse.

Wijzig ten slotte de functie handtekening om asynchroon te zijn.

Na deze wijzigingen moet de functie code er ongeveer als volgt uitzien:

```csharp
namespace FunctionSample
{
    public static class FooFunction
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("Foo")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            DigitalTwinsClient client = null;
            try
            {
                ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
                DigitalTwinsClientOptions opts = 
                    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
                                                
                log.LogInformation($"ADT service client connection created.");
            }
            catch (Exception e)
            {
                log.LogError($"ADT service client connection failed. " + e.ToString());
                return;
            }
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Als u wilt dat uw functions-app toegang kan krijgen tot Azure Digital Apparaatdubbels, moet deze een door een systeem beheerde identiteit hebben en gemachtigd zijn om toegang te krijgen tot uw Azure Digital Apparaatdubbels-exemplaar.

Gebruik de volgende opdracht om de door het systeem beheerde identiteit te maken. Noteer het veld *principalId* in de uitvoer.

```azurecli
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Gebruik de waarde van *principalId* in de volgende opdracht om de functie-app-id toe te wijzen aan de *eigenaar*srol voor uw Azure Digital Twins-instantie:

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```

Voor meer informatie over beheerde identiteiten raadpleegt u [*beheerde identiteiten gebruiken voor app service en Azure functions*](../app-service/overview-managed-identity.md).

Ten slotte kunt u de URL van uw Azure Digital Apparaatdubbels-exemplaar toegankelijk maken voor uw functie door een omgevings variabele in te stellen. Zie [*omgevings variabelen*](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables)voor meer informatie.

> [!TIP]
> De URL van het Azure Digital Apparaatdubbels-exemplaar wordt gemaakt door *https://* toe te voegen aan het begin van de *hostnaam*van uw Azure Digital apparaatdubbels-exemplaar. Als u de hostnaam, samen met alle eigenschappen van uw exemplaar, wilt zien, kunt u uitvoeren `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-URL>"
```

## <a name="publish-the-azure-function-app"></a>De Azure-functie-app publiceren

Als u de functie-app naar Azure wilt publiceren, selecteert u het functie project (niet de oplossing) in Solution Explorer en kiest u *publiceren ()*.

Het volgende tabblad wordt weer gegeven:

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-1.png" alt-text="Visual Studio: functie dialoog venster publiceren, pagina 1":::

Selecteer of maak een App Service plan voor gebruik met Azure Functions. Als u het niet zeker weet, kunt u beginnen met het standaard verbruiks plan.

> [!IMPORTANT] 
> Als u een Azure-functie publiceert, worden er extra kosten in rekening gebracht voor uw abonnement, onafhankelijk van Azure Digital Apparaatdubbels.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-2.png" alt-text="Visual Studio: functie dialoog venster publiceren, pagina 2":::

Voer op de volgende pagina de gewenste naam in voor de nieuwe functie-app, een resource groep en andere gegevens.

## <a name="set-up-security-access-for-the-azure-function-app"></a>Beveiligings toegang instellen voor de Azure function-app

De Azure function-skelet van eerdere voor beelden vereist dat er een Bearer-token wordt door gegeven, zodat het kan worden geverifieerd met Azure Digital Apparaatdubbels. Als u er zeker van wilt zijn dat dit Bearer-token is door gegeven, moet u [Managed Service Identity (MSI)](../active-directory/managed-identities-azure-resources/overview.md) instellen voor de functie-app. U hoeft dit slechts één keer te doen voor elke functie-app.

Als u dit wilt instellen, gaat u naar de [Azure Portal](https://portal.azure.com/) en navigeert u naar uw functie-app.

Op het tabblad *platform functies* selecteert u *identiteit*:

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-1.png" alt-text="Azure Portal: identiteit selecteren voor een Azure-functie":::

Stel op de pagina identiteit de *status* in *op aan*. 

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-2.png" alt-text="Azure Portal: identiteits status inschakelen":::

Noteer ook de **object-id** die op deze pagina wordt weer gegeven, zoals deze wordt gebruikt in de volgende sectie.

### <a name="assign-access-roles"></a>Toegangs rollen toewijzen

Omdat Azure Digital Apparaatdubbels toegangs beheer op basis van rollen gebruikt voor het beheren van toegang (Zie [*concepten: beveiliging voor Azure Digital apparaatdubbels-oplossingen*](concepts-security.md) voor meer informatie), moet u ook een rol toevoegen voor elke functie-app die u toegang wilt geven tot Azure Digital apparaatdubbels.

Als u een rol wilt toewijzen, moet u de **resource-id** van het Azure Digital apparaatdubbels-exemplaar dat u hebt gemaakt, hebben. Als u deze niet eerder hebt vastgelegd tijdens het maken van uw exemplaar, kunt u deze ophalen met behulp van de volgende opdracht:

```bash
az dt show --name <your-instance-name> -g <your-resource-group-name>
```
De resource-ID maakt deel uit van de uitvoer, als een lange teken reeks met de naam ' id ' die begint met de letters '/subscriptions/... '.

Gebruik de resource-ID samen met de object-ID van de Azure-functie die u eerder hebt gebruikt in de onderstaande opdracht:

```azurecli
az role assignment create --role "Azure Digital Twins Owner (Preview)" --assignee <object-ID> --scope <resource-ID>
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u de stappen voor het instellen van een Azure-functie gevolgd voor gebruik met Azure Digital Apparaatdubbels. Vervolgens kunt u uw Azure-functie abonneren op Event Grid om te Luis teren naar een eind punt. Dit eind punt kan het volgende zijn:
* Een Event Grid-eind punt dat is gekoppeld aan Azure Digital Apparaatdubbels om berichten te verwerken die afkomstig zijn van Azure Digital Apparaatdubbels zelf (zoals wijzigings berichten van eigenschappen, telemetrie-berichten die worden gegenereerd door [digitale apparaatdubbels](concepts-twins-graph.md) in het dubbele diagram of levenscyclus berichten)
* De IoT-systeem onderwerpen die door IoT Hub worden gebruikt voor het verzenden van telemetrie en andere gebeurtenissen voor apparaten
* Een Event Grid-eind punt dat berichten van andere services ontvangt

Bekijk vervolgens hoe u uw eenvoudige Azure function bouwt om IoT Hub gegevens op te nemen in azure Digital Apparaatdubbels:
* [*Instructies: telemetrie opnemen van IoT Hub*](how-to-ingest-iot-hub-data.md)