---
title: 'Voor beeld: gezichten identificeren in afbeeldingen-gezicht'
titleSuffix: Azure Cognitive Services
description: In deze hand leiding wordt gedemonstreerd hoe u onbekende gezichten identificeert met behulp van PersonGroup-objecten, die van bekende mensen vooraf worden gemaakt.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 0b1cf99fe6e2aa4d7fcb12c3fb96b10b42c7c0b7
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169921"
---
# <a name="example-identify-faces-in-images"></a>Voor beeld: gezichten identificeren in afbeeldingen

In deze hand leiding wordt gedemonstreerd hoe u onbekende gezichten identificeert met behulp van PersonGroup-objecten, die van bekende mensen vooraf worden gemaakt. De voor beelden zijn geschreven C# in met behulp van de Azure Cognitive Services Face-client bibliotheek.

## <a name="preparation"></a>Voorbereiding

In dit voor beeld ziet u het volgende:

- Een PersonGroup maken. Deze PersonGroup bevat een lijst met bekende personen.
- Hoe u gezichten aan elke persoon toewijst. Deze gezichten worden gebruikt als basis voor het identificeren van personen. U wordt aangeraden om duidelijke front-weer gaven van gezichten te gebruiken. Een voor beeld is een foto-ID. Een goede set Foto's bevat gezichten van dezelfde persoon in verschillende poses, kleding kleuren of Hairstyles.

Als u de demonstratie van dit voor beeld wilt uitvoeren, bereidt u het volgende voor:

- Enkele foto's van het gezicht van de persoon. [Down load voorbeeld Foto's](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) voor Anna, Bill en Clare.
- Een reeks test foto's. De Foto's kunnen of niet de gezichten van Anna, Bill of Clare bevatten. Ze worden gebruikt om identificatie te testen. Selecteer ook enkele voor beelden van installatie kopieën van de voor gaande koppeling.

## <a name="step-1-authorize-the-api-call"></a>Stap 1: De API-aanroep autoriseren

Voor elke aanroep naar de Face-API is een abonnementssleutel vereist. Deze sleutel kan worden door gegeven via een query reeks parameter of worden opgegeven in de aanvraag header. Als u de abonnements sleutel via een query reeks wilt door geven, raadpleegt u de aanvraag-URL voor de detectie van het [gezicht](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) als voor beeld:
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Als alternatief geeft u de abonnements sleutel op in de header van de HTTP-aanvraag **OCP-APIM-Subscription-Key: &lt;-abonnements sleutel&gt;** .
Wanneer u een client bibliotheek gebruikt, wordt de abonnements sleutel door gegeven via de constructor van de FaceClient-klasse. Bijvoorbeeld:
 
```csharp 
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials("<subscription key>"),
            new System.Net.Http.DelegatingHandler[] { });
```
 
Als u de abonnements sleutel wilt ophalen, gaat u naar de Azure Marketplace vanuit de Azure Portal. Zie [abonnementen](https://azure.microsoft.com/try/cognitive-services/)voor meer informatie.

## <a name="step-2-create-the-persongroup"></a>Stap 2: De PersonGroup maken

In deze stap bevat een PersonGroup met de naam ' MyFriends ' de volgende Anna, factuur en Clare. Voor elke persoon zijn verschillende gezichten geregistreerd. De gezichten moeten worden gedetecteerd vanuit de installatie kopieën. Nadat al deze stappen zijn uitgevoerd, hebt u een PersonGroup die eruitziet als deze afbeelding:

![MyFriends](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>Stap 2,1: gebruikers definiëren voor de PersonGroup
Een persoon is een basisidentificatie-eenheid. Een gebruiker kan een of meer bekende gezichten geregistreerd hebben. Een PersonGroup is een verzameling personen. Elke persoon is gedefinieerd binnen een bepaalde PersonGroup. Identificatie wordt uitgevoerd op basis van een PersonGroup. De taak is het maken van een PersonGroup en het maken van de personen hierin, zoals Anna, Bill en Clare.

Maak eerst een nieuwe PersonGroup met behulp van de [PersonGroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API. De bijbehorende API van de client bibliotheek is de methode CreatePersonGroupAsync voor de klasse FaceClient. De groeps-ID die is opgegeven voor het maken van de groep is uniek voor elk abonnement. U kunt PersonGroups ook ophalen, bijwerken of verwijderen met behulp van andere PersonGroup-Api's. 

Nadat u een groep hebt gedefinieerd, kunt u personen binnen deze groepen definiëren met behulp van de [PersonGroup-Create-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API. De clientbibliotheekmethode is CreatePersonAsync. U kunt een gezicht toevoegen aan elke persoon nadat deze is gemaakt.

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
### <a name="step2-2"></a>Stap 2,2: gezichten detecteren en deze registreren bij de juiste persoon
De detectie wordt uitgevoerd door een POST-webaanvraag naar de API [Face - Detecteren](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) te verzenden met het afbeeldingsbestand in de HTTP-aanvraagbody. Wanneer u de-client bibliotheek gebruikt, wordt de detectie van het gezicht uitgevoerd via een van de detectie.. Async-methoden van de FaceClient-klasse.

Voor elk gezicht dat wordt gedetecteerd, kunt u contact opnemen met [PersonGroup. Voeg het gezicht](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) toe om het aan de juiste persoon toe te voegen.

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
Als de afbeelding meer dan één gezicht bevat, wordt alleen het grootste gezicht toegevoegd. U kunt andere gezichten toevoegen aan de persoon. Geef een teken reeks met de notatie ' targetFace = left, top, width, height ' door aan [PersonGroup persoon-add face-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API van de targetFace-query parameter. U kunt ook de optionele para meter targetFace gebruiken voor de methode AddPersonFaceAsync om andere gezichten toe te voegen. Elk gezicht dat wordt toegevoegd aan de persoon krijgt een unieke ID voor het permanente gezicht. U kunt deze ID gebruiken in [PersonGroup-persoon – face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) en [Face-identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="step-3-train-the-persongroup"></a>Stap 3: De PersonGroup trainen

De PersonGroup moet worden getraind voordat een identificatie kan worden uitgevoerd door deze te gebruiken. De PersonGroup moet opnieuw worden getraind nadat u een persoon hebt toegevoegd of verwijderd of als u het geregistreerde gezicht van een persoon bewerkt. De training wordt uitgevoerd met behulp van de API [PersonGroup – Trainen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249). Wanneer u de-client bibliotheek gebruikt, is dit een aanroep van de methode TrainPersonGroupAsync:
 
```csharp 
await faceClient.PersonGroup.TrainAsync(personGroupId);
```
 
Training is een asynchroon proces. De bewerking kan niet worden voltooid, zelfs niet nadat de TrainPersonGroupAsync-methode is geretourneerd. Mogelijk moet u een query uitvoeren op de trainings status. Gebruik de [PersonGroup-trainings status-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) API of de methode GetPersonGroupTrainingStatusAsync van de client bibliotheek. De volgende code toont een eenvoudige logica voor het volt ooien van de PersonGroup-training:
 
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

Wanneer de face-service id's uitvoert, wordt de gelijkenis van een test vlak tussen alle gezichten in een groep berekend. Het retourneert de meest vergelijk bare personen voor het test gezicht. Dit proces wordt uitgevoerd door de API voor het identificeren van het [gezicht](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) of de methode IdentifyAsync van de client bibliotheek.

Het test gezicht moet worden gedetecteerd met behulp van de vorige stappen. Vervolgens wordt de face-ID door gegeven aan de id-API als een tweede argument. Meerdere gezichts-Id's kunnen tegelijkertijd worden geïdentificeerd. Het resultaat bevat alle geïdentificeerde resultaten. Het identificatie proces retourneert standaard slechts één persoon die voldoet aan het test gezicht. U kunt desgewenst de optionele para meter maxNumOfCandidatesReturned opgeven, zodat het identificatie proces meer kandidaten retourneert.

De volgende code toont het identificatie proces:

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

Nadat u de stappen hebt voltooid, kunt u proberen om verschillende gezichten te identificeren. Bekijk of de gezichten van Anna, Bill of Clare correct kunnen worden geïdentificeerd op basis van de afbeeldingen die zijn geüpload voor gezichts detectie. Zie de volgende voorbeelden:

![Verschillende gezichten identificeren](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>Stap 5: aanvraag voor grote schaal

Een PersonGroup kan tot 10.000 personen bevatten op basis van de vorige ontwerp beperking.
Zie voor meer informatie over scenario's met grootschalige capaciteit voor maximaal een miljoen personen [De functie Grootschalig gebruiken](how-to-use-large-scale.md).

## <a name="summary"></a>Samenvatting

In deze hand leiding hebt u het proces voor het maken van een PersonGroup geleerd en het identificeren van een persoon. De volgende functies zijn uitgelegd en aangetoond:

- Spoor gezichten door gebruik te maken van de detectie-API voor het [gezichts](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) licht.
- PersonGroups maken met behulp van de [PersonGroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API.
- Personen maken met behulp van de [PersonGroup-API maken](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) .
- Train een PersonGroup met behulp van de [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API.
- Identificeer onbekende gezichten voor de PersonGroup met behulp van de [Face-identify-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API.

## <a name="related-topics"></a>Verwante onderwerpen

- [Concepten voor gezichts herkenning](../concepts/face-recognition.md)
- [Gezichten detecteren in een installatie kopie](HowtoDetectFacesinImage.md)
- [Gezichten toevoegen](how-to-add-faces.md)
- [De grootschalige functie gebruiken](how-to-use-large-scale.md)
