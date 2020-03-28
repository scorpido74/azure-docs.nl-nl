---
title: 'Voorbeeld: Gezichten in afbeeldingen identificeren - Gezicht'
titleSuffix: Azure Cognitive Services
description: Deze handleiding laat zien hoe je onbekende gezichten identificeren met behulp van PersonGroup-objecten, die van tevoren zijn gemaakt van bekende mensen.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 0b1cf99fe6e2aa4d7fcb12c3fb96b10b42c7c0b7
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169921"
---
# <a name="example-identify-faces-in-images"></a>Voorbeeld: Gezichten in afbeeldingen identificeren

Deze handleiding laat zien hoe je onbekende gezichten identificeren met behulp van PersonGroup-objecten, die van tevoren zijn gemaakt van bekende mensen. De voorbeelden worden geschreven in C# met behulp van de Azure Cognitive Services Face-clientbibliotheek.

## <a name="preparation"></a>Voorbereiding

Dit voorbeeld toont aan:

- Een persoonsgroep maken. Deze persoonsgroep bevat een lijst met bekende personen.
- Hoe gezichten toe te wijzen aan elke persoon. Deze gezichten worden gebruikt als basislijn om mensen te identificeren. We raden u aan duidelijke frontale weergaven van gezichten te gebruiken. Een voorbeeld is een foto-ID. Een goede set van foto's bevat gezichten van dezelfde persoon in verschillende poses, kleding kleuren, of kapsels.

Bereid voor om de demonstratie van dit monster uit te voeren:

- Enkele foto's van het gezicht van de persoon. [Download voorbeeldfoto's](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) voor Anna, Bill en Clare.
- Een reeks testfoto's. De foto's bevatten mogelijk wel of niet de gezichten van Anna, Bill of Clare. Ze worden gebruikt om identificatie te testen. Selecteer ook enkele voorbeeldafbeeldingen uit de voorgaande koppeling.

## <a name="step-1-authorize-the-api-call"></a>Stap 1: De API-aanroep autoriseren

Voor elke aanroep naar de Face-API is een abonnementssleutel vereist. Deze sleutel kan worden doorgegeven via een parameter querytekenreeks of opgegeven in de hoofdtekst van de aanvraag. Zie de url van het verzoek voor de [gezichtsaanroep - Detecteren](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) als voorbeeld:
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Als alternatief geeft u de abonnementssleutel op in de **ocp-apim-subscription-toets van de HTTP-aanvraagheader: &lt;Abonnementssleutel&gt;**.
Wanneer u een clientbibliotheek gebruikt, wordt de abonnementssleutel doorgegeven via de constructeur van de klasse FaceClient. Bijvoorbeeld:
 
```csharp 
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials("<subscription key>"),
            new System.Net.Http.DelegatingHandler[] { });
```
 
Als u de abonnementssleutel wilt ophalen, gaat u naar de Azure Marketplace vanuit de Azure-portal. Zie [Abonnementen voor](https://azure.microsoft.com/try/cognitive-services/)meer informatie.

## <a name="step-2-create-the-persongroup"></a>Stap 2: De PersonGroup maken

In deze stap bevat een personengroep met de naam "MyFriends" Anna, Bill en Clare. Voor elke persoon zijn verschillende gezichten geregistreerd. De gezichten moeten worden gedetecteerd op de beelden. Nadat al deze stappen zijn uitgevoerd, hebt u een PersonGroup die eruitziet als deze afbeelding:

![MijnVrienden](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>Stap 2.1: Personen definiëren voor de persoonsgroep
Een persoon is een basisidentificatie-eenheid. Een gebruiker kan een of meer bekende gezichten geregistreerd hebben. Een PersonGroup is een verzameling mensen. Elke persoon wordt gedefinieerd binnen een bepaalde groep. Identificatie gebeurt tegen een persoonsgroep. De taak is om een persoonsgroep te maken en vervolgens de mensen in het te maken, zoals Anna, Bill en Clare.

Maak eerst een nieuwe persoonsgroep met behulp van de [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API. De bijbehorende clientbibliotheek-API is de CreatePersonGroupAsync-methode voor de klasse FaceClient. De groeps-ID die is opgegeven om de groep te maken, is uniek voor elk abonnement. U ook Persoonsgroepen ontvangen, bijwerken of verwijderen met behulp van andere PersonGroup API's. 

Nadat een groep is gedefinieerd, u mensen daarin definiëren met behulp van de [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API. De clientbibliotheekmethode is CreatePersonAsync. U een gezicht aan elke persoon toevoegen nadat deze is gemaakt.

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
### <a name="step-22-detect-faces-and-register-them-to-the-correct-person"></a><a name="step2-2"></a>Stap 2.2: Gezichten detecteren en registreren bij de juiste persoon
De detectie wordt uitgevoerd door een POST-webaanvraag naar de API [Face - Detecteren](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) te verzenden met het afbeeldingsbestand in de HTTP-aanvraagbody. Wanneer u de clientbibliotheek gebruikt, wordt gezichtsherkenning uitgevoerd via een van de Detect.. Async-methoden van de klasse FaceClient.

Voor elk gezicht dat wordt gedetecteerd, belt u [PersonGroup Person – Voeg Face toe](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) om het toe te voegen aan de juiste persoon.

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
Als de afbeelding meer dan één gezicht bevat, wordt alleen het grootste gezicht toegevoegd. U andere gezichten aan de persoon toevoegen. Geef een tekenreeks door in de indeling 'targetFace = links, boven, breedte, hoogte' aan [PersonGroup Person - Voeg](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) de doelparameter van Face API toe. U ook de optionele parameter targetFace voor de methode AddPersonFaceAsync gebruiken om andere vlakken toe te voegen. Elk gezicht dat aan de persoon wordt toegevoegd, krijgt een unieke aanhoudende face ID. U deze id gebruiken in [PersonGroup Person – Delete Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) and Face – [Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="step-3-train-the-persongroup"></a>Stap 3: De PersonGroup trainen

De PersonGroup moet worden opgeleid voordat een identificatie kan worden uitgevoerd met behulp van het. De Groep van personen moet worden omgeschoold nadat u een persoon hebt toegevoegd of verwijderd of als u het geregistreerde gezicht van een persoon hebt bewerkt. De training wordt uitgevoerd met behulp van de API [PersonGroup – Trainen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249). Wanneer u de clientbibliotheek gebruikt, is dit een aanroep naar de methode TrainPersonGroupAsync:
 
```csharp 
await faceClient.PersonGroup.TrainAsync(personGroupId);
```
 
Training is een asynchrone proces. Het is mogelijk niet klaar, zelfs niet nadat de TrainPersonGroupAsync-methode is geretourneerd. Mogelijk moet u de trainingsstatus opvragen. Gebruik de [PersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) API of GetPersonGroupTrainingStatusAsync methode van de clientbibliotheek. De volgende code toont een eenvoudige logica van het wachten tot de training van PersonGroup is voltooid:
 
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

Wanneer de Face-service identificaties uitvoert, berekent deze de gelijkenis van een testgezicht tussen alle gezichten binnen een groep. Het retourneert de meest vergelijkbare personen voor het testgezicht. Dit proces gebeurt via de [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API of de IdentifyAsync-methode van de clientbibliotheek.

Het testgezicht moet worden gedetecteerd met behulp van de vorige stappen. Vervolgens wordt de face ID als tweede argument doorgegeven aan de identificatie-API. Meerdere gezichts-id's kunnen tegelijk worden geïdentificeerd. Het resultaat bevat alle geïdentificeerde resultaten. Standaard retourneert het identificatieproces slechts één persoon die het beste overeenkomt met het testgezicht. Geef desgevraagd de optionele parameter maxNumOfCandidatesReturned op om het identificatieproces meer kandidaten terug te laten sturen.

De volgende code toont het identificatieproces aan:

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

Nadat u de stappen hebt voltooid, probeert u verschillende gezichten te identificeren. Kijk of de gezichten van Anna, Bill of Clare correct kunnen worden geïdentificeerd op basis van de beelden die zijn geüpload voor gezichtsherkenning. Zie de volgende voorbeelden:

![Verschillende gezichten identificeren](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>Stap 5: Aanvraag voor grootschalige

Een Persoonsgroep kan maximaal 10.000 personen bevatten op basis van de vorige ontwerpbeperking.
Zie voor meer informatie over scenario's met grootschalige capaciteit voor maximaal een miljoen personen [De functie Grootschalig gebruiken](how-to-use-large-scale.md).

## <a name="summary"></a>Samenvatting

In deze handleiding leerde u het proces van het maken van een persoonsgroep en het identificeren van een persoon. De volgende kenmerken werden uitgelegd en gedemonstreerd:

- Gezichten detecteren met behulp van de [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) API.
- Persoonsgroepen maken met behulp van de [Persoonsgroep - API maken.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
- Personen maken met behulp van de [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API.
- Train een persoonsgroep met behulp van de [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API.
- Identificeer onbekende gezichten tegen de persoonsgroep met behulp van de [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API.

## <a name="related-topics"></a>Verwante onderwerpen

- [Concepten voor gezichtsherkenning](../concepts/face-recognition.md)
- [Gezichten in een afbeelding detecteren](HowtoDetectFacesinImage.md)
- [Gezichten toevoegen](how-to-add-faces.md)
- [De functie Grootschalig gebruiken](how-to-use-large-scale.md)
