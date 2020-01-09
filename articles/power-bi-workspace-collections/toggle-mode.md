---
title: Scha kelen tussen de weer gave-en bewerkings modus voor rapporten
description: Meer informatie over hoe u kunt scha kelen tussen de weer gave-en bewerkings modus voor uw rapporten in Power BI werkruimte verzamelingen.
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: b2696560b5d5013fe337b51ec61cbfac9e512610
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75357661"
---
# <a name="toggle-between-view-and-edit-mode-for-reports-in-power-bi-workspace-collections"></a>Scha kelen tussen de weer gave-en bewerkings modus voor rapporten in Power BI werkruimte verzamelingen

Meer informatie over hoe u kunt scha kelen tussen de weer gave-en bewerkings modus voor uw rapporten in Power BI werkruimte verzamelingen.

> [!IMPORTANT]
> Power BI Workspace Collections is afgeschaft en is beschikbaar tot juni 2018 of tot de datum die in uw contract wordt aangegeven. Om onderbreking van uw toepassing te voorkomen, wordt u geadviseerd om een migratie naar Power BI Embedded te plannen. Voor meer informatie over het migreren van gegevens naar Power BI Embedded raadpleegt u [How to migrate Power BI Workspace Collections content to Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/) (Inhoud van Power BI-werkruimteverzamelingen migreren naar Power BI Embedded).

## <a name="creating-an-access-token"></a>Een toegangs token maken

U moet een toegangs token maken dat u de mogelijkheid biedt om een rapport weer te geven en te bewerken. Als u een rapport wilt bewerken en opslaan, moet u de machtiging **Report. readwrite** -token hebben. Zie [verifiëren en autoriseren in Power bi werkruimte verzamelingen](app-token-flow.md)voor meer informatie.

> [!NOTE]
> Zo kunt u wijzigingen in een bestaand rapport bewerken en opslaan. Als u ook de functie van **Opslaan als**wilt ondersteunen, moet u extra machtigingen opgeven. Zie [scopes](app-token-flow.md#scopes)voor meer informatie.

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Report.ReadWrite";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="embed-configuration"></a>Configuratie insluiten

U moet machtigingen en een View mode opgeven om de knop Opslaan te zien in de bewerkings modus. Zie [Configuratie Details insluiten](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details)voor meer informatie.

Bijvoorbeeld in Java script:

```html
   <div id="reportContainer"></div>

    <script>
    // Get models. Models, it contains enums that can be used.
    var models = window['powerbi-client'].models;

    // Embed configuration used to describe the what and how to embed.
    // This object is used when calling powerbi.embed.
    // This also includes settings and options such as filters.
    // You can find more information at https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details.
    var config= {
        type: 'report',
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        id:  '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
        permissions: models.Permissions.ReadWrite /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.View,
        settings: {
            filterPaneEnabled: true,
            navContentPaneEnabled: true
        }
    };

    // Get a reference to the embedded report HTML element
    var reportContainer = $('#reportContainer')[0];

    // Embed the report and display it within the div container.
    var report = powerbi.embed(reportContainer, config);
    </script>
```

Dit geeft aan dat het rapport wordt Inge sloten in de weergave modus op basis van **View mode** wordt ingesteld op **modellen. View mode. View**.

## <a name="view-mode"></a>Weergave modus

U kunt de volgende Java script gebruiken om over te scha kelen naar de weergave modus, als u zich in de bewerkings modus bevindt.

```javascript
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to view mode.
report.switchMode("view");

```

## <a name="edit-mode"></a>De modus Bewerken

U kunt de volgende Java script gebruiken om over te scha kelen naar de bewerkings modus, als u zich in de weergave modus bevindt.

```javascript
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to edit mode.
report.switchMode("edit");

```

## <a name="see-also"></a>Zie ook

[Aan de slag met het voorbeeld](get-started-sample.md)  
[Een rapport insluiten](embed-report.md)  
[Authenticating and authorizing in Power BI Workspace Collections](app-token-flow.md) (Verifiëren en autoriseren in Power BI Workspace Collections)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[Voorbeeld van ingesloten JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[PowerBI-CSharp Git opslag plaats](https://github.com/Microsoft/PowerBI-CSharp)  
[Git opslag plaats voor het PowerBI-knoop punt](https://github.com/Microsoft/PowerBI-Node)  

Nog vragen? [Probeer de Power BI-community](https://community.powerbi.com/)
