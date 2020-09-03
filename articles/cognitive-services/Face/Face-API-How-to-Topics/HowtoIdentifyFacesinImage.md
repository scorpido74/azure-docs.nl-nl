---
title: 'Voorbeeld: Gezichten in afbeeldingen identificeren - Face'
titleSuffix: Azure Cognitive Services
description: In deze handleiding ziet u hoe u onbekende gezichten identificeert met PersonGroups-objecten, die vooraf worden gemaakt op basis van bekende personen.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 07/02/2020
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: bae84d93d15abe8804a430dacd2f2cddf8a9aed9
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919162"
---
# <a name="example-identify-faces-in-images"></a>Voorbeeld: Gezichten in afbeeldingen identificeren

In deze handleiding ziet u hoe u onbekende gezichten identificeert met PersonGroups-objecten, die vooraf worden gemaakt op basis van bekende personen. De voorbeelden worden geschreven in C# met behulp van de Face-clientbibliotheek van Azure Cognitive Services.

In dit voorbeeld krijgt u instructies voor het volgende:

- Het maken van een PersonGroup. Deze PersonGroup bevat een lijst met bekende personen.
- Het toewijzen van gezichten aan elke persoon. Deze gezichten worden als een basislijn gebruikt om personen te identificeren. U wordt aangeraden om duidelijke frontale weergaven van gezichten te gebruiken. Denk bijvoorbeeld aan een foto-id. Een goede verzameling foto's bevat gezichten van dezelfde persoon in verschillende poses, in verschillende kleuren kleding of met verschillende haarstijlen.

## <a name="prerequisites"></a>Vereisten
* De huidige versie van [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* Zodra u een Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Een Face-resource maken"  target="_blank">maakt u een Face-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Naar de resource gaan** en kopieert u uw sleutel.
* Een aantal foto's met de gezichten van geïdentificeerde personen. [Download voorbeeldfoto's](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) voor Anna, Bill en Clare.
* Een reeks testfoto's. De foto's hoeven niet per se de gezichten van de geïdentificeerde personen te bevatten. Gebruik een aantal afbeeldingen via de koppeling naar voorbeeldfoto's.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

Maak een nieuwe .NET Core-toepassing in uw favoriete editor of IDE. 

Gebruik in een consolevenster (zoals cmd, PowerShell of Bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam `face-identify`. Met deze opdracht maakt u een eenvoudig Hallo wereld-C#-project met één bronbestand: *Program.cs*. 

```dotnetcli
dotnet new console -n face-quickstart
```

Wijzig uw map in de zojuist gemaakte app-map. U kunt de toepassing maken met:

```dotnetcli
dotnet build
```

De build-uitvoer mag geen waarschuwingen of fouten bevatten. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="step-1-authorize-the-api-call"></a>Stap 1: De API-aanroep autoriseren

Voor elke aanroep naar de Face-API is een abonnementssleutel vereist. Deze sleutel kan worden doorgegeven via een tekenreeksparameter of worden opgegeven in de aanvraagheader. Als u gebruikmaakt van een clientbibliotheek wordt de abonnementssleutel doorgegeven via de constructor van de klasse **FaceClient**. Voeg de volgende code toe aan de methode **Main** in het bestand *Program.cs*.
 
```csharp 
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<subscription key>"),
    new System.Net.Http.DelegatingHandler[] { });
```

## <a name="step-2-create-the-persongroup"></a>Stap 2: De PersonGroup maken

In deze stap bevat een **PersonGroup** met de naam MyFriends Anna, Bill en Clare. Voor elke persoon zijn verschillende gezichten geregistreerd. De gezichten moeten worden gedetecteerd in de afbeeldingen. Nadat al deze stappen zijn uitgevoerd, hebt u een **PersonGroup** die eruitziet als deze afbeelding:

![MyFriends](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>Stap 2.1: Personen voor de PersonGroup definiëren
Een persoon is een basisidentificatie-eenheid. Een gebruiker kan een of meer bekende gezichten geregistreerd hebben. Een **PersonGroup** is een verzameling personen. Elke persoon wordt gedefinieerd binnen een specifieke **PersonGroup**. Identificatie wordt uitgevoerd op basis van een **PersonGroup**. De taak bestaat uit het maken van een **PersonGroup** en het maken van de personen in die PersonGroup, zoals Anna, Bill en Clare.

Maak eerst een nieuwe **PersonGroup** met behulp van de API [PersonGroup - Maken](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244). De bijbehorende clientbibliotheek-API is de methode **CreatePersonGroupAsync** voor de klasse **FaceClient**. De groeps-id die is opgegeven om de groep te maken, is uniek voor elk abonnement. U kunt ook **PersonGroups** ophalen, bijwerken of verwijderen met behulp van andere **PersonGroup**-API's. 

Nadat een groep is gedefinieerd, kunt u personen in de groep definiëren met behulp van de API [PersonGroup Person - Maken](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c). De clientbibliotheekmethode is **CreatePersonAsync**. Nadat de personen zijn gemaakt, kunt u aan elke persoon een gezicht toevoegen.

```csharp 
// Create an empty PersonGroup
string personGroupId = "myfriends";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Friends");
 
// Define Anna
CreatePersonResult friend1 = await faceClient.PersonGroupPerson.CreateAsync(
    // Id of the PersonGroup that the person belonged to
    personGroupId,    
    // Name of the person
    "Anna"            
);
 
// Define Bill and Clare in the same way
```
### <a name="step-22-detect-faces-and-register-them-to-the-correct-person"></a><a name="step2-2"></a> Stap 2.2: Gezichten detecteren en deze registreren bij de juiste persoon
De detectie wordt uitgevoerd door een POST-webaanvraag naar de API [Face - Detecteren](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) te verzenden met het afbeeldingsbestand in de HTTP-aanvraagbody. Wanneer u de clientbibliotheek gebruikt, wordt gezichtsdetectie uitgevoerd met een van de Detect..Async-methoden van de FaceClient-klasse.

Voor elk gedetecteerd gezicht roept u [PersonGroup Person – Gezicht toevoegen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) aan om het gezicht toe te voegen aan de juiste persoon.

In de volgende code ziet u het proces voor het detecteren van een gezicht in een afbeelding en het toevoegen van het gezicht aan een persoon:

```csharp 
// Directory contains image files of Anna
const string friend1ImageDir = @"D:\Pictures\MyFriends\Anna\";
 
foreach (string imagePath in Directory.GetFiles(friend1ImageDir, "*.jpg"))
{
    using (Stream s = File.OpenRead(imagePath))
    {
        // Detect faces in the image and add to Anna
        await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(
            personGroupId, friend1.PersonId, s);
    }
}
// Do the same for Bill and Clare
``` 
Als de afbeelding meer dan één gezicht bevat, wordt alleen het grootste gezicht toegevoegd. U kunt andere gezichten aan de persoon toevoegen. Geef een tekenreeks met de indeling 'targetFace = left, top, width, height' door aan de targetFace-queryparameter van de API [PersonGroup Person - Gezicht toevoegen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b). U kunt ook de optionele parameter targetFace voor de AddPersonFaceAsync-methode gebruiken om andere gezichten toe te voegen. Aan elk gezicht dat aan de persoon wordt toegevoegd, wordt een unieke, permanente gezichts-id gegeven. U kunt deze id gebruiken in [PersonGroup Person – Gezicht verwijderen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) en [Face – Identificeren](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="step-3-train-the-persongroup"></a>Stap 3: De PersonGroup trainen

De **PersonGroup** moet worden getraind voordat u er gezichten van personen mee kunt identificeren. De **PersonGroup** moet opnieuw worden getraind nadat u een persoon toevoegt of verwijdert, of wanneer u het geregistreerde gezicht van een persoon bewerkt. De training wordt uitgevoerd met behulp van de API [PersonGroup – Trainen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249). Wanneer u de clientbibliotheek gebruikt, hoeft u alleen de methode **TrainPersonGroupAsync** aan te roepen:
 
```csharp 
await faceClient.PersonGroup.TrainAsync(personGroupId);
```
 
Trainen is een asynchroon proces. Dit proces kan nog actief zijn nadat de methode **TrainPersonGroupAsync** is geretourneerd. Mogelijk moet u een query uitvoeren op de trainingsstatus. Gebruik de API [PersonGroup - Trainingsstatus ophalen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) of de methode **GetPersonGroupTrainingStatusAsync** van de clientbibliotheek. In de volgende code ziet u eenvoudige logica van een **PersonGroup**-training in afwachting van voltooiing:
 
```csharp 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceClient.PersonGroup.GetTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != TrainingStatusType.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step-4-identify-a-face-against-a-defined-persongroup"></a>Stap 4: Een gezicht identificeren op basis van een gedefinieerde PersonGroup

Wanneer met de Face-service identificaties worden uitgevoerd, wordt berekend in hoeverre een testgezicht vergelijkbaar is met alle gezichten in een groep. De personen die het meest vergelijkbaar zijn met het testgezicht, worden geretourneerd. Dit proces wordt uitgevoerd met behulp van de API [Face - Identificeren](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) of de methode IdentifyAsync van de clientbibliotheek.

Het testgezicht moet worden gedetecteerd met behulp van de vorige stappen. Vervolgens wordt de gezichts-id als een tweede argument doorgegeven aan de identificatie-API. Er kunnen meerdere gezichts-id's tegelijk worden geïdentificeerd. Het resultaat bevat alle geïdentificeerde resultaten. Standaard wordt alleen de persoon weergegeven die het meeste overeenkomt met het testgezicht. Desgewenst kunt u de optionele parameter _maxNumOfCandidatesReturned_ gebruiken om meer kandidaten te retourneren.

In de volgende code ziet u hoe het identificatieproces in zijn werk gaat:

```csharp 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceClient.Face.DetectWithStreamAsync(s);
    var faceIds = faces.Select(face => face.FaceId.Value).ToArray();
 
    var results = await faceClient.Face.IdentifyAsync(faceIds, personGroupId);
    foreach (var identifyResult in results)
    {
        Console.WriteLine("Result of face: {0}", identifyResult.FaceId);
        if (identifyResult.Candidates.Length == 0)
        {
            Console.WriteLine("No one identified");
        }
        else
        {
            // Get top 1 among all candidates returned
            var candidateId = identifyResult.Candidates[0].PersonId;
            var person = await faceClient.PersonGroupPerson.GetAsync(personGroupId, candidateId);
            Console.WriteLine("Identified as {0}", person.Name);
        }
    }
}
``` 

Nadat u de stappen hebt voltooid, probeert u verschillende gezichten te identificeren. Kijk of de gezichten van Anna, Bill of Clare goed kunnen worden geïdentificeerd volgens de afbeeldingen die u voor gezichtsdetectie hebt geüpload. Zie de volgende voorbeelden:

![Verschillende gezichten identificeren](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>Stap 5: Aanvraag voor grote schaal

Een **PersonGroup** kan maximaal 10.000 personen bevatten op basis van beperkingen in het vorige ontwerp. Zie voor meer informatie over scenario's met grootschalige capaciteit voor maximaal een miljoen personen [De functie Grootschalig gebruiken](how-to-use-large-scale.md).

## <a name="summary"></a>Samenvatting

In deze handleiding hebt u het proces voor het maken van een **PersonGroup** en het identificeren van gezichten geleerd. De volgende functies zijn uitgelegd en gedemonstreerd:

- Gezichten detecteren met behulp van de API [Face - Detecteren](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d).
- PersonGroups maken met behulp van de API [PersonGroup - Maken](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244).
- Personen maken met behulp van de API [PersonGroup Person - Maken](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c).
- Een PersonGroup trainen met behulp van de API [PersonGroup – Trainen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).
- Onbekende gezichten identificeren op basis van de PersonGroup met behulp van de API [Face - Identificeren](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="related-topics"></a>Verwante onderwerpen

- [Gezichtsherkenningsconcepten](../concepts/face-recognition.md)
- [Gezichten in een afbeelding detecteren](HowtoDetectFacesinImage.md)
- [Gezichten toevoegen](how-to-add-faces.md)
- [De functie Grootschalig gebruiken](how-to-use-large-scale.md)
