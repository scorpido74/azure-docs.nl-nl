---
title: Migreer uw gezichtsgegevens over abonnementen - Face
titleSuffix: Azure Cognitive Services
description: In deze handleiding ziet u hoe u uw opgeslagen gezichtsgegevens van het ene Face-abonnement naar het andere migreren.
services: cognitive-services
author: lewlu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: lewlu
ms.openlocfilehash: e5ca51da7322e4eab4ea364ec5da086a1068fa9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169814"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Uw gezichtsgegevens migreren naar een ander Face-abonnement

In deze handleiding ziet u hoe u gezichtsgegevens, zoals een opgeslagen PersonGroup-object met gezichten, verplaatst naar een ander Azure Cognitive Services Face-abonnement. Als u de gegevens wilt verplaatsen, gebruikt u de functie Momentopname. Op deze manier hoeft u niet herhaaldelijk een PersonGroup- of FaceList-object te bouwen en te trainen wanneer u uw activiteiten verplaatst of uitbreidt. U hebt bijvoorbeeld een PersonGroup-object gemaakt met een gratis proefabonnement en wilt het nu migreren naar uw betaalde abonnement. Of het kan zijn dat u gezichtsgegevens moet synchroniseren tussen abonnementen in verschillende regio's voor een grote bedrijfsbewerking.

Dezelfde migratiestrategie is ook van toepassing op largepersongroup- en LargeFaceList-objecten. Als u niet bekend bent met de concepten in deze handleiding, raadpleegt u hun definities in de gids [voor concepten voor gezichtsherkenning.](../concepts/face-recognition.md) Deze handleiding maakt gebruik van de Face .NET-clientbibliotheek met C#.

## <a name="prerequisites"></a>Vereisten

Je hebt de volgende objecten nodig:

- Twee Face-abonnementssleutels, één met de bestaande gegevens en één om naar te migreren. Als u zich wilt abonneren op de Face-service en uw sleutel wilt krijgen, volgt u de instructies in [Een Account voor Cognitieve services maken.](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)
- De Face-abonnements-ID-tekenreeks die overeenkomt met het doelabonnement. Als u deze wilt vinden, selecteert u **Overzicht** in de Azure-portal. 
- Elke editie van [Visual Studio 2015 of 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Het Visual Studio-project maken

Deze handleiding maakt gebruik van een eenvoudige console-app om de migratie van gezichtsgegevens uit te voeren. Zie het voorbeeld van de [Face-momentopname](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) op GitHub voor een volledige implementatie.

1. Maak in Visual Studio een nieuw Console-app .NET Framework-project. Noem het **FaceApiSnapshotSample**.
1. Download de vereiste NuGet-pakketten. Klik met de rechtermuisknop op uw project in de Solution Explorer en selecteer **NuGet-pakketten beheren.** Selecteer het tabblad **Bladeren** en selecteer **Prerelease opnemen**. Zoek en installeer het volgende pakket:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>Gezichtsclients maken

Maak in de **hoofdmethode** in *Program.cs*twee [FaceClient-exemplaren](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) voor uw bron- en doelabonnementen. In dit voorbeeld wordt een Face-abonnement in de regio Oost-Azië als bron en een West-Amerikaans abonnement als doel gebruikt. In dit voorbeeld wordt uitgelegd hoe u gegevens van de ene Azure-regio naar de andere migreert. 

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

```csharp
var FaceClientEastAsia = new FaceClient(new ApiKeyServiceClientCredentials("<East Asia Subscription Key>"))
    {
        Endpoint = "https://southeastasia.api.cognitive.microsoft.com/>"
    };

var FaceClientWestUS = new FaceClient(new ApiKeyServiceClientCredentials("<West US Subscription Key>"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com/"
    };
```

Vul de abonnementssleutelwaarden en eindpunt-URL's in voor uw bron- en doelabonnementen.


## <a name="prepare-a-persongroup-for-migration"></a>Een persoonsgroep voorbereiden op migratie

U hebt de id van de persoonsgroep in uw bronabonnement nodig om deze te migreren naar het doelabonnement. Gebruik de methode [PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet) om een lijst met uw PersonGroup-objecten op te halen. Download vervolgens de eigenschap [PersonGroup.PersonGroupId.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) Dit proces ziet er anders uit op basis van welke PersonGroup-objecten u hebt. In deze handleiding wordt de bron-persoonsgroep-id opgeslagen in `personGroupId`.

> [!NOTE]
> De [voorbeeldcode](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) maakt en traint een nieuwe persoonsgroep om te migreren. In de meeste gevallen moet u al een persoonsgroep hebben om te gebruiken.

## <a name="take-a-snapshot-of-a-persongroup"></a>Een momentopname van een persoonsgroep maken

Een momentopname is tijdelijke externe opslag voor bepaalde Face-gegevenstypen. Het functioneert als een soort klembord om gegevens van het ene abonnement naar het andere te kopiëren. Eerst maakt u een momentopname van de gegevens in het bronabonnement. Vervolgens past u het toe op een nieuw gegevensobject in het doelabonnement.

Gebruik het FaceClient-exemplaar van het bronabonnement om een momentopname van de persoonsgroep te maken. Gebruik [TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) met de persoonsgroep-id en de id van het doelabonnement. Als u meerdere doelabonnementen hebt, voegt u deze toe als arrayvermeldingen in de derde parameter.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> Het proces van het maken en toepassen van snapshots verstoort geen regelmatige oproepen naar de bron of doelgroep PersonGroups of FaceLists. Maak geen gelijktijdige aanroepen die het bronobject wijzigen, zoals [FaceList-beheeroproepen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) of de [call van PersonGroup Train,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) bijvoorbeeld. De momentopnamebewerking kan voor of na deze bewerkingen worden uitgevoerd of er kunnen fouten optreden.

## <a name="retrieve-the-snapshot-id"></a>De momentopname-id ophalen

De methode die wordt gebruikt om snapshots te maken is asynchroon, dus je moet wachten op de voltooiing ervan. Momentopnamebewerkingen kunnen niet worden geannuleerd. In deze code `WaitForOperation` houdt de methode de asynchrone aanroep in de gaten. Het controleert de status om de 100 ms. Nadat de bewerking is voltooid, haalt u `OperationLocation` een bewerkings-id op door het veld te ontwenen. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Een `OperationLocation` typische waarde ziet er als volgt uit:

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

De `WaitForOperation` helper methode is hier:

```csharp
/// <summary>
/// Waits for the take/apply operation to complete and returns the final operation status.
/// </summary>
/// <returns>The final operation status.</returns>
private static async Task<OperationStatus> WaitForOperation(IFaceClient client, Guid operationId)
{
    OperationStatus operationStatus = null;
    do
    {
        if (operationStatus != null)
        {
            Thread.Sleep(TimeSpan.FromMilliseconds(100));
        }

        // Get the status of the operation.
        operationStatus = await client.Snapshot.GetOperationStatusAsync(operationId);

        Console.WriteLine($"Operation Status: {operationStatus.Status}");
    }
    while (operationStatus.Status != OperationStatusType.Succeeded
            && operationStatus.Status != OperationStatusType.Failed);

    return operationStatus;
}
```

Nadat de bewerkingsstatus wordt weergegeven, `Succeeded`krijgt u `ResourceLocation` de momentopname-id door het veld van de geretourneerde instantie OperationStatus te ontwijs.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Een `resourceLocation` typische waarde ziet er als volgt uit:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Een momentopname toepassen op een doelabonnement

Maak vervolgens de nieuwe persoonsgroep in het doelabonnement met behulp van een willekeurig gegenereerde ID. Gebruik vervolgens het FaceClient-exemplaar van het doelabonnement om de momentopname toe te passen op deze Persoonsgroep. Geef de momentopname-id en de nieuwe persoonsgroep-id door.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Een object Snapshot is slechts 48 uur geldig. Maak alleen een momentopname als u van plan bent deze kort daarna te gebruiken voor gegevensmigratie.

Een aanvraag voor een momentopname toepassen retourneert een andere bewerkings-id. Als u deze id wilt `OperationLocation` krijgen, ontleedt u het veld van de instantie Returned applySnapshotResult. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

De momentopname aanvraagprocedure is ook asynchroon, dus opnieuw gebruiken `WaitForOperation` om te wachten tot het is voltooid.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>De gegevensmigratie testen

Nadat u de momentopname hebt toegepast, wordt de nieuwe persoonsgroep in het doelabonnement gevuld met de oorspronkelijke gezichtsgegevens. Standaard worden ook trainingsresultaten gekopieerd. De nieuwe PersonGroup is klaar voor gezichtsidentificatiegesprekken zonder omscholing.

Als u de gegevensmigratie wilt testen, voert u de volgende bewerkingen uit en vergelijkt u de resultaten die ze afdrukken met de console:

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the person group before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

Gebruik de volgende helpermethoden:

```csharp
private static async Task DisplayPersonGroup(IFaceClient client, string personGroupId)
{
    var personGroup = await client.PersonGroup.GetAsync(personGroupId);
    Console.WriteLine("Person Group:");
    Console.WriteLine(JsonConvert.SerializeObject(personGroup));

    // List persons.
    var persons = await client.PersonGroupPerson.ListAsync(personGroupId);

    foreach (var person in persons)
    {
        Console.WriteLine(JsonConvert.SerializeObject(person));
    }

    Console.WriteLine();
}
```

```csharp
private static async Task IdentifyInPersonGroup(IFaceClient client, string personGroupId)
{
    using (var fileStream = new FileStream("data\\PersonGroup\\Daughter\\Daughter1.jpg", FileMode.Open, FileAccess.Read))
    {
        var detectedFaces = await client.Face.DetectWithStreamAsync(fileStream);

        var result = await client.Face.IdentifyAsync(detectedFaces.Select(face => face.FaceId.Value).ToList(), personGroupId);
        Console.WriteLine("Test identify against PersonGroup");
        Console.WriteLine(JsonConvert.SerializeObject(result));
        Console.WriteLine();
    }
}
```

Nu u de nieuwe Persoonsgroep gebruiken in het doelabonnement. 

Als u de doelgroepgroep in de toekomst opnieuw wilt bijwerken, maakt u een nieuwe persoonsgroep om de momentopname te ontvangen. Volg hiervoor de stappen in deze handleiding. Met één object PersonGroup kan een momentopname slechts één keer worden toegepast.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u gezichtsgegevens hebt gemigreerd, verwijdert u het momentopnameobject handmatig.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>Volgende stappen

Bekijk vervolgens de relevante API-referentiedocumentatie, verken een voorbeeld-app die de snapshot-functie gebruikt of volg een handleiding om de andere API-bewerkingen te gebruiken die hier worden genoemd:

- [Referentiedocumentatie voor momentopnamen (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Voorbeeld van momentopname van het gezicht](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Gezichten toevoegen](how-to-add-faces.md)
- [Gezichten in een afbeelding detecteren](HowtoDetectFacesinImage.md)
- [Gezichten in een afbeelding identificeren](HowtoIdentifyFacesinImage.md)
