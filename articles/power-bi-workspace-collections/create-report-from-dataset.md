---
title: Rapport maken op basis van een gegevensset-Power BI werkruimte verzamelingen
description: Power BI werkruimte verzamelings rapporten kunnen nu worden gemaakt op basis van een gegevensset in uw eigen toepassing.
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: bcc6044d0f0f5270f81a619e4d1ad71ea35cc170
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427058"
---
# <a name="create-a-new-report-from-a-dataset-in-power-bi-workspace-collections"></a>Een nieuw rapport maken op basis van een gegevensset in Power BI werkruimte verzamelingen

Power BI werkruimte verzamelings rapporten kunnen nu worden gemaakt op basis van een gegevensset in uw eigen toepassing.

> [!IMPORTANT]
> Power BI Workspace Collections is afgeschaft en is beschikbaar tot juni 2018 of tot de datum die in uw contract wordt aangegeven. Om onderbreking van uw toepassing te voorkomen, wordt u geadviseerd om een migratie naar Power BI Embedded te plannen. Voor meer informatie over het migreren van gegevens naar Power BI Embedded raadpleegt u [How to migrate Power BI Workspace Collections content to Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/) (Inhoud van Power BI-werkruimteverzamelingen migreren naar Power BI Embedded).

De verificatie methode is vergelijkbaar met die van het insluiten van een rapport. Het is gebaseerd op toegangs tokens die specifiek zijn voor een gegevensset. Tokens die worden gebruikt voor PowerBI.com, worden uitgegeven door Azure Active Directory (AAD). Power BI werkruimte verzamelings tokens worden uitgegeven door uw eigen toepassing.

Wanneer u een Inge sloten rapport maakt, zijn de uitgegeven tokens voor een specifieke gegevensset. Tokens moeten worden gekoppeld aan de insluit-URL op hetzelfde element om ervoor te zorgen dat elk een uniek token heeft. Als u een Inge sloten rapport wilt maken, moeten *DataSet. Read en Workspace. Report. Create* scopes worden vermeld in het toegangs token.

## <a name="create-access-token-needed-to-create-new-report"></a>Een toegangs token maken dat nodig is voor het maken van een nieuw rapport

Power BI werkruimte verzamelingen gebruiken een insluit token, dat is met HMAC ondertekende JSON-webtokens. De tokens zijn ondertekend met de toegangs sleutel van uw Power BI werkruimte verzameling. Insluit tokens worden standaard gebruikt om alleen-lezen toegang tot een rapport te bieden om in te sluiten in een toepassing. Insluit tokens worden uitgegeven voor een specifiek rapport en moeten worden gekoppeld aan een Inge sloten URL.

Toegangs tokens moeten worden gemaakt op de server als de toegangs sleutels worden gebruikt voor het ondertekenen/versleutelen van de tokens. Zie [verifiëren en autoriseren met Power bi werkruimte verzamelingen](app-token-flow.md)voor meer informatie over het maken van een toegangs token. U kunt ook de methode [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) bekijken. Hier volgt een voor beeld van hoe dit eruit zou zien met behulp van de .NET SDK voor Power BI.

In dit voor beeld hebben we onze gegevensset-ID waarop we het nieuwe rapport willen maken. U moet ook de scopes toevoegen voor *DataSet. Read en Workspace. Report. Create*.

De *PowerBIToken-klasse* vereist dat u het [Power bi core NuGut-pakket](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)installeert.

**NuGet-pakket installeren**

```powershell
Install-Package Microsoft.PowerBI.Core
```

**C#gecodeerd**

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Dataset.Read Workspace.Report.Create";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="create-a-new-blank-report"></a>Een nieuw leeg rapport maken

Als u een nieuw rapport wilt maken, moet u de configuratie maken. Dit moet het toegangs token, de embedURL en de datasetID bevatten waarop we het rapport willen maken. Hiervoor moet u het nuget [Power bi java script-pakket](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)installeren. De embedUrl wordt alleen https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> U kunt het [Java script-rapport insluiten voor beeld](https://microsoft.github.io/PowerBI-JavaScript/demo/) gebruiken om de functionaliteit te testen. Het biedt ook voor beelden van code voor de verschillende bewerkingen die beschikbaar zijn.

**NuGet-pakket installeren**

```powershell
Install-Package Microsoft.PowerBI.JavaScript
```

**Java script-code**

```html
<div id="reportContainer"></div>

<script>
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);
</script>
```

Als u *powerbi. createReport ()* aanroept, wordt een leeg canvas in de bewerkings modus weer gegeven in het *div* -element.

![Nieuw leeg rapport](media/create-report-from-dataset/create-new-report.png)

## <a name="save-new-reports"></a>Nieuwe rapporten opslaan

Het rapport wordt pas gemaakt wanneer u de bewerking **Opslaan als** aanroept. Dit kunt u doen vanuit het menu bestand of vanuit Java script.

```javascript
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);
    
    var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);
```

> [!IMPORTANT]
> Er wordt pas een nieuw rapport gemaakt nadat het **Opslaan** is aangeroepen. Nadat u hebt bespaard, wordt in het canvas nog steeds de gegevensset in de bewerkings modus weer gegeven en niet in het rapport. U moet het nieuwe rapport opnieuw laden, net zoals u elk ander rapport zou doen.

![Menu bestand-opslaan als](media/create-report-from-dataset/save-new-report.png)

## <a name="load-the-new-report"></a>Het nieuwe rapport laden

Als u wilt communiceren met het nieuwe rapport, moet u het insluiten op dezelfde manier als de toepassing een normaal rapport insluit, wat betekent dat er een nieuw token moet worden uitgegeven voor het nieuwe rapport en vervolgens de methode embed aan te roepen.

```html
<div id="reportContainer"></div>
<script>
var embedConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MJ',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: '5dac7a4a-4452-46b3-99f6-a25915e0fe54',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
</script>
```

## <a name="automate-save-and-load-of-a-new-report-using-the-saved-event"></a>Het opslaan en laden van een nieuw rapport automatiseren met behulp van de gebeurtenis ' opgeslagen '

Om het proces van ' opslaan als ' te automatiseren en vervolgens het nieuwe rapport te laden, kunt u het gebruik van de gebeurtenis ' opgeslagen ' maken. Deze gebeurtenis wordt geactiveerd wanneer de bewerking opslaan is voltooid en retourneert een JSON-object dat de nieuwe reportId, rapport naam, de oude reportId (als er een is), en als de bewerking is opgeslagen als opslaan of bewaren.

```json
{
  "reportObjectId": "5dac7a4a-4452-46b3-99f6-a25915e0fe54",
  "reportName": "newReport",
  "saveAs": true,
  "originalReportObjectId": null
}
```

Om het proces te automatiseren, kunt u Luis teren naar de gebeurtenis ' saved ', de nieuwe reportId, het nieuwe token maken en het nieuwe rapport insluiten.

```html
<div id="reportContainer"></div>
<script>
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);


   var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);

    // report.on will add an event handler which prints to Log window.
    report.on("saved", function(event) {
        
         // get new Token
         var newReportId =  event.detail.reportObjectId;

        // create new Token. This is a function that the application should provide
        var newToken = createAccessToken(newReportId,scopes /*provide the wanted scopes*/);
        
        
    var embedConfiguration = {
        accessToken: newToken ,
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: newReportId,
    };

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
       
   // report.off removes a given event handler if it exists.
   report.off("saved");
    });
</script>
```

## <a name="see-also"></a>Zie ook

[Aan de slag met het voorbeeld](get-started-sample.md)  
[Rapporten opslaan](save-reports.md)  
[Een rapport insluiten](embed-report.md)  
[Authenticating and authorizing in Power BI Workspace Collections](app-token-flow.md) (Verifiëren en autoriseren in Power BI Workspace Collections)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Voorbeeld van ingesloten JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Power BI core NuGut-pakket](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Power BI java script-pakket](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  

Nog vragen? [Probeer de Power BI-community](https://community.powerbi.com/)
