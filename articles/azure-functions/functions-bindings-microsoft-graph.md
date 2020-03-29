---
title: Microsoft Graph-bindingen voor Azure-functies
description: Meer informatie over het gebruik van Microsoft Graph-triggers en -bindingen in Azure-functies.
author: craigshoemaker
ms.topic: reference
ms.date: 12/20/2017
ms.author: cshoe
ms.openlocfilehash: 770187693e5bac6e059dfd20455099fcc695b74b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76715027"
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Microsoft Graph-bindingen voor Azure-functies

In dit artikel wordt uitgelegd hoe u Microsoft Graph-triggers en -bindingen in Azure-functies configureert en ermee werken. Hiermee u Azure-functies gebruiken om te werken met gegevens, inzichten en gebeurtenissen uit de [Microsoft Graph.](https://developer.microsoft.com/graph)

De Microsoft Graph-extensie bevat de volgende bindingen:
- Met [een auth-token-invoerbinding](#token-input) u communiceren met elke Microsoft Graph-API.
- Met [een Excel-tabelinvoerbinding](#excel-input) u gegevens uit Excel lezen.
- Met [een Excel-tabeluitvoerbinding](#excel-output) u Excel-gegevens wijzigen.
- Met [een OneDrive-bestandsinvoerbinding](#onedrive-input) u bestanden van OneDrive lezen.
- Met [een OneDrive-bestandsuitvoerbinding](#onedrive-output) u naar bestanden in OneDrive schrijven.
- Met [een uitvoerbinding voor Outlook-berichten](#outlook-output) u e-mail verzenden via Outlook.
- Met een verzameling [Microsoft Graph-webhook-triggers en bindingen](#webhooks) u reageren op gebeurtenissen uit de Microsoft Graph.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> Microsoft Graph-bindingen zijn momenteel in preview voor Azure Functions versie 2.x en hoger. Ze worden niet ondersteund in functies versie 1.x.

## <a name="packages"></a>Pakketten

De auth-tokeninvoerbinding wordt geleverd in het [NuGet-pakket Microsoft.Azure.WebJobs.Extensions.AuthTokens.](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) De andere Microsoft Graph-bindingen worden geleverd in het [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph-pakket.](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) Broncode voor de pakketten bevindt zich in de [GitHub-repository azure-functions-microsoftgraph-extension.](https://github.com/Azure/azure-functions-microsoftgraph-extension/)

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="setting-up-the-extensions"></a>De extensies instellen

Microsoft Graph-bindingen zijn beschikbaar via _bindende extensies._ Bindingsextensies zijn optionele onderdelen van de runtime van Azure Functions. In deze sectie ziet u hoe u de extensies voor Microsoft Graph en Auth-token instelt.

### <a name="enabling-functions-20-preview"></a>Voorbeeld van functies 2.0 inschakelen

Bindende extensies zijn alleen beschikbaar voor Azure Functions 2.0 preview. 

Zie [Runtime-versies](set-runtime-version.md)van Azure Functions targeten voor informatie over het instellen van een functie-app om de voorbeeldversie 2.0 van de runtime van functies te gebruiken.

### <a name="installing-the-extension"></a>Het installeren van de extensie

Als u een extensie vanuit de Azure-portal wilt installeren, navigeert u naar een sjabloon of binding waarnaar wordt verwezen. Maak een nieuwe functie en kies in het selectiescherm van de sjabloon het scenario 'Microsoft Graph'. Selecteer een van de sjablonen uit dit scenario. U ook naar het tabblad 'Integreren' van een bestaande functie navigeren en een van de bindingen in dit artikel selecteren.

In beide gevallen verschijnt er een waarschuwing waarin de te installeren extensie wordt aangegeven. Klik **op Installeren** om de extensie te verkrijgen. Elke extensie hoeft slechts één keer per functie-app te worden geïnstalleerd. 

> [!Note] 
> Het in-portal installatieproces kan tot 10 minuten duren op een verbruiksplan.

Als u Visual Studio gebruikt, u de extensies krijgen door [de NuGet-pakketten te](#packages)installeren die eerder in dit artikel worden vermeld.

### <a name="configuring-authentication--authorization"></a>Verificatie / autorisatie configureren

De bindingen in dit artikel vereisen dat een identiteit wordt gebruikt. Hierdoor kan de Microsoft Graph machtigingen afdwingen en interacties controleren. De identiteit kan een gebruiker zijn die toegang heeft tot uw toepassing of de toepassing zelf. Als u deze identiteit wilt configureren, stelt u [Verificatie van de App-service / Autorisatie](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) in met Azure Active Directory. U moet ook resourcemachtigingen aanvragen die uw functies vereisen.

> [!Note] 
> De Microsoft Graph-extensie ondersteunt alleen Azure AD-verificatie. Gebruikers moeten inloggen met een werk- of schoolaccount.

Als u de Azure-portal gebruikt, ziet u een waarschuwing onder de prompt om de extensie te installeren. De waarschuwing vraagt u om verificatie / autorisatie van appservice te configureren en eventuele machtigingen op te vragen die de sjabloon of binding vereist. Klik **nu op Azure AD configureren** of Geef nu waar nodig machtigingen **toe.**



<a name="token-input"></a>
## <a name="auth-token"></a>Auth-token

De auth-tokeninvoerbinding krijgt een Azure AD-token voor een bepaalde bron en biedt deze als tekenreeks aan uw code. De resource kan een resource zijn waarvoor de toepassing machtigingen heeft. 

Deze sectie bevat de volgende onderafdelingen:

* [Voorbeeld](#auth-token---example)
* [Kenmerken](#auth-token---attributes)
* [Configuratie](#auth-token---configuration)
* [Gebruik](#auth-token---usage)

### <a name="auth-token---example"></a>Auth-token - voorbeeld

Zie het taalspecifieke voorbeeld:

* [C#-script (.csx)](#auth-token---c-script-example)
* [Javascript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Voorbeeld van Auth-token - C#-script

In het volgende voorbeeld worden gebruikersprofielgegevens weergegeven.

Het *bestand function.json* definieert een HTTP-trigger met een tokeninvoerbinding:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

De C#-scriptcode gebruikt het token om een HTTP-aanroep naar de Microsoft Graph te voeren en geeft het resultaat als resultaat:

```csharp
using System.Net; 
using System.Net.Http; 
using System.Net.Http.Headers;
using Microsoft.Extensions.Logging; 

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string graphToken, ILogger log)
{
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", graphToken);
    return await client.GetAsync("https://graph.microsoft.com/v1.0/me/");
}
```

#### <a name="auth-token---javascript-example"></a>Auth-token - JavaScript-voorbeeld

In het volgende voorbeeld worden gebruikersprofielgegevens weergegeven.

Het *bestand function.json* definieert een HTTP-trigger met een tokeninvoerbinding:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

De JavaScript-code gebruikt het token om een HTTP-aanroep naar de Microsoft Graph te voeren en retourneert het resultaat.

```js
const rp = require('request-promise');

module.exports = function (context, req) {
    let token = "Bearer " + context.bindings.graphToken;

    let options = {
        uri: 'https://graph.microsoft.com/v1.0/me/',
        headers: {
            'Authorization': token
        }
    };
    
    rp(options)
        .then(function(profile) {
            context.res = {
                body: profile
            };
            context.done();
        })
        .catch(function(err) {
            context.res = {
                status: 500,
                body: err
            };
            context.done();
        });
};
```

### <a name="auth-token---attributes"></a>Auth-token - kenmerken

Gebruik in [klassebibliotheken van C#](functions-dotnet-class-library.md)het kenmerk [Token.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs)

### <a name="auth-token---configuration"></a>Auth-token - configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `Token` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Naam**| N.v.t. |Vereist - de variabele naam die wordt gebruikt in de functiecode voor het auth-token. Zie [Een auth-tokeninvoerbinding gebruiken van code](#token-input-code).|
|**Type**| N.v.t. |Vereist - moet `token`worden ingesteld op .|
|**direction**| N.v.t. |Vereist - moet `in`worden ingesteld op .|
|**Identiteit**|**Identiteit**|Vereist - De identiteit die wordt gebruikt om de actie uit te voeren. Kan een van de volgende waarden zijn:<ul><li><code>userFromRequest</code>- Alleen geldig met [HTTP-trigger]. Gebruikt de identiteit van de aanroepende gebruiker.</li><li><code>userFromId</code>- Gebruikt de identiteit van een eerder ingelogde gebruiker met de opgegeven ID. Zie <code>userId</code> het pand.</li><li><code>userFromToken</code>- Gebruikt de identiteit die wordt weergegeven door het opgegeven token. Zie <code>userToken</code> het pand.</li><li><code>clientCredentials</code>- Gebruikt de identiteit van de functie-app.</li></ul>|
|**Userid**|**Userid**  |Nodig als en _identity_ alleen als `userFromId`identiteit is ingesteld op . Een hoofd-id van de gebruiker die is gekoppeld aan een eerder ingelogde gebruiker.|
|**userToken**|**UserToken**|Nodig als en _identity_ alleen als `userFromToken`identiteit is ingesteld op . Een token dat geldig is voor de functie-app. |
|**Resource**|**Resource**|Vereist - een URL van de Azure AD-bron waarvoor het token wordt aangevraagd.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Auth-token - gebruik

Voor de binding zelf zijn geen Azure AD-machtigingen vereist, maar afhankelijk van hoe het token wordt gebruikt, moet u mogelijk aanvullende machtigingen aanvragen. Controleer de vereisten van de resource die u wilt openen met het token.

Het token wordt altijd gepresenteerd als een tekenreeks.

> [!Note]
> Bij het lokaal ontwikkelen `userFromId` `userFromToken` met `userFromRequest` een van beide of opties, `X-MS-TOKEN-AAD-ID-TOKEN` kan het vereiste token handmatig worden [verkregen](https://github.com/Azure/azure-functions-microsoftgraph-extension/issues/54#issuecomment-392865857) en opgegeven in de aanvraagheader van een aanroepende clienttoepassing.


<a name="excel-input"></a>
## <a name="excel-input"></a>Excel-invoer

De excel-tabelinvoerbinding leest de inhoud van een Excel-tabel die is opgeslagen in OneDrive.

Deze sectie bevat de volgende onderafdelingen:

* [Voorbeeld](#excel-input---example)
* [Kenmerken](#excel-input---attributes)
* [Configuratie](#excel-input---configuration)
* [Gebruik](#excel-input---usage)

### <a name="excel-input---example"></a>Excel-invoer - voorbeeld

Zie het taalspecifieke voorbeeld:

* [C#-script (.csx)](#excel-input---c-script-example)
* [Javascript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Excel-invoer - C#-scriptvoorbeeld

In het volgende *bestand function.json* wordt een HTTP-trigger gedefinieerd met een Excel-invoerbinding:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

De volgende C#-scriptcode leest de inhoud van de opgegeven tabel en retourneert deze naar de gebruiker:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Microsoft.Extensions.Logging;

public static IActionResult Run(HttpRequest req, string[][] excelTableData, ILogger log)
{
    return new OkObjectResult(excelTableData);
}
```

#### <a name="excel-input---javascript-example"></a>Excel-invoer - JavaScript-voorbeeld

In het volgende *bestand function.json* wordt een HTTP-trigger gedefinieerd met een Excel-invoerbinding:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

De volgende JavaScript-code leest de inhoud van de opgegeven tabel en retourneert deze naar de gebruiker.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Excel-invoer - kenmerken

Gebruik [in klassebibliotheken van C#](functions-dotnet-class-library.md)het kenmerk [Excel.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs)

### <a name="excel-input---configuration"></a>Excel-invoer - configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `Excel` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Naam**| N.v.t. |Vereist - de variabele naam die wordt gebruikt in functiecode voor de Excel-tabel. Zie [Een Excel-tabelinvoerbinding gebruiken van code](#excel-input-code).|
|**Type**| N.v.t. |Vereist - moet `excel`worden ingesteld op .|
|**direction**| N.v.t. |Vereist - moet `in`worden ingesteld op .|
|**Identiteit**|**Identiteit**|Vereist - De identiteit die wordt gebruikt om de actie uit te voeren. Kan een van de volgende waarden zijn:<ul><li><code>userFromRequest</code>- Alleen geldig met [HTTP-trigger]. Gebruikt de identiteit van de aanroepende gebruiker.</li><li><code>userFromId</code>- Gebruikt de identiteit van een eerder ingelogde gebruiker met de opgegeven ID. Zie <code>userId</code> het pand.</li><li><code>userFromToken</code>- Gebruikt de identiteit die wordt weergegeven door het opgegeven token. Zie <code>userToken</code> het pand.</li><li><code>clientCredentials</code>- Gebruikt de identiteit van de functie-app.</li></ul>|
|**Userid**|**Userid**  |Nodig als en _identity_ alleen als `userFromId`identiteit is ingesteld op . Een hoofd-id van de gebruiker die is gekoppeld aan een eerder ingelogde gebruiker.|
|**userToken**|**UserToken**|Nodig als en _identity_ alleen als `userFromToken`identiteit is ingesteld op . Een token dat geldig is voor de functie-app. |
|**Pad**|**Pad**|Vereist - het pad in OneDrive naar de Excel-werkmap.|
|**werkbladNaam**|**Werkbladnaam**|Het werkblad waarin de tabel is gevonden.|
|**tableName**|**Tabelnaam**|De naam van de tafel. Als dit niet is opgegeven, wordt de inhoud van het werkblad gebruikt.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Excel-invoer - gebruik

Voor deze binding zijn de volgende Azure AD-machtigingen vereist:

|Resource|Machtiging|
|--------|--------|
|Microsoft Graph|Gebruikersbestanden lezen|

Met de binding worden de volgende typen blootgesteld aan .NET-functies:
- tekenreeks[][]
- Microsoft.Graph.Werkmaptabel
- Aangepaste objecttypen (met structurele modelbinding)










<a name="excel-output"></a>
## <a name="excel-output"></a>Excel-uitvoer

Met de uitvoerbinding van Excel wijzigt u de inhoud van een Excel-tabel die is opgeslagen in OneDrive.

Deze sectie bevat de volgende onderafdelingen:

* [Voorbeeld](#excel-output---example)
* [Kenmerken](#excel-output---attributes)
* [Configuratie](#excel-output---configuration)
* [Gebruik](#excel-output---usage)

### <a name="excel-output---example"></a>Excel-uitvoer - voorbeeld

Zie het taalspecifieke voorbeeld:

* [C#-script (.csx)](#excel-output---c-script-example)
* [Javascript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Excel-uitvoer - C#-scriptvoorbeeld

In het volgende voorbeeld worden rijen toegevoegd aan een Excel-tabel.

Het *bestand functie.json* definieert een HTTP-trigger met een Excel-uitvoerbinding:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

De C#-scriptcode voegt een nieuwe rij toe aan de tabel (vermoedelijk één kolom) op basis van invoer uit de querytekenreeks:

```csharp
using System.Net;
using System.Text;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, IAsyncCollector<object> newExcelRow, ILogger log)
{
    string input = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await newExcelRow.AddAsync(new {
        Text = input
        // Add other properties for additional columns here
    });
    return;
}
```

#### <a name="excel-output---javascript-example"></a>Excel-uitvoer - JavaScript-voorbeeld

In het volgende voorbeeld worden rijen toegevoegd aan een Excel-tabel.

Het *bestand functie.json* definieert een HTTP-trigger met een Excel-uitvoerbinding:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Met de volgende JavaScript-code wordt een nieuwe rij aan de tabel toegevoegd (vermoedelijk één kolom) op basis van invoer uit de querytekenreeks.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Excel-uitvoer - kenmerken

Gebruik [in klassebibliotheken van C#](functions-dotnet-class-library.md)het kenmerk [Excel.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs)

### <a name="excel-output---configuration"></a>Excel-uitvoer - configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `Excel` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Naam**| N.v.t. |Vereist - de variabele naam die wordt gebruikt in de functiecode voor het auth-token. Zie [Een Excel-tabeluitvoerbinding gebruiken van code](#excel-output-code).|
|**Type**| N.v.t. |Vereist - moet `excel`worden ingesteld op .|
|**direction**| N.v.t. |Vereist - moet `out`worden ingesteld op .|
|**Identiteit**|**Identiteit**|Vereist - De identiteit die wordt gebruikt om de actie uit te voeren. Kan een van de volgende waarden zijn:<ul><li><code>userFromRequest</code>- Alleen geldig met [HTTP-trigger]. Gebruikt de identiteit van de aanroepende gebruiker.</li><li><code>userFromId</code>- Gebruikt de identiteit van een eerder ingelogde gebruiker met de opgegeven ID. Zie <code>userId</code> het pand.</li><li><code>userFromToken</code>- Gebruikt de identiteit die wordt weergegeven door het opgegeven token. Zie <code>userToken</code> het pand.</li><li><code>clientCredentials</code>- Gebruikt de identiteit van de functie-app.</li></ul>|
|**Userid** |**Userid** |Nodig als en _identity_ alleen als `userFromId`identiteit is ingesteld op . Een hoofd-id van de gebruiker die is gekoppeld aan een eerder ingelogde gebruiker.|
|**userToken**|**UserToken**|Nodig als en _identity_ alleen als `userFromToken`identiteit is ingesteld op . Een token dat geldig is voor de functie-app. |
|**Pad**|**Pad**|Vereist - het pad in OneDrive naar de Excel-werkmap.|
|**werkbladNaam**|**Werkbladnaam**|Het werkblad waarin de tabel is gevonden.|
|**tableName**|**Tabelnaam**|De naam van de tafel. Als dit niet is opgegeven, wordt de inhoud van het werkblad gebruikt.|
|**updateType**|**UpdateType**|Vereist - Het type wijziging dat moet worden aangepast aan de tabel. Kan een van de volgende waarden zijn:<ul><li><code>update</code>- Vervangt de inhoud van de tabel in OneDrive.</li><li><code>append</code>- Hiermee voegt u de payload toe aan het einde van de tabel in OneDrive door nieuwe rijen te maken.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Excel-uitvoer - gebruik

Voor deze binding zijn de volgende Azure AD-machtigingen vereist:

|Resource|Machtiging|
|--------|--------|
|Microsoft Graph|Volledige toegang hebben tot gebruikersbestanden|

Met de binding worden de volgende typen blootgesteld aan .NET-functies:
- tekenreeks[][]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.Werkmaptabel
- Aangepaste objecttypen (met structurele modelbinding)





<a name="onedrive-input"></a>
## <a name="file-input"></a>Bestandsinvoer

De invoerbinding van OneDrive-bestand leest de inhoud van een bestand dat is opgeslagen in OneDrive.

Deze sectie bevat de volgende onderafdelingen:

* [Voorbeeld](#file-input---example)
* [Kenmerken](#file-input---attributes)
* [Configuratie](#file-input---configuration)
* [Gebruik](#file-input---usage)

### <a name="file-input---example"></a>Bestandsinvoer - voorbeeld

Zie het taalspecifieke voorbeeld:

* [C#-script (.csx)](#file-input---c-script-example)
* [Javascript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Bestandsinvoer - C#-scriptvoorbeeld

In het volgende voorbeeld wordt een bestand gelezen dat is opgeslagen in OneDrive.

Het *bestand function.json* definieert een HTTP-trigger met een OneDrive-bestandsinvoerbinding:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

De C#-scriptcode leest het bestand dat is opgegeven in de querytekenreeks en registreert de lengte ervan:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, ILogger log)
{
    log.LogInformation(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Bestandsinvoer - JavaScript-voorbeeld

In het volgende voorbeeld wordt een bestand gelezen dat is opgeslagen in OneDrive.

Het *bestand function.json* definieert een HTTP-trigger met een OneDrive-bestandsinvoerbinding:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Met de volgende JavaScript-code wordt het bestand gelezen dat is opgegeven in de querytekenreeks en wordt de lengte geretourneerd.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Bestandsinvoer - kenmerken

Gebruik in [klassebibliotheken van C#](functions-dotnet-class-library.md)het kenmerk [OneDrive.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs)

### <a name="file-input---configuration"></a>Bestandsinvoer - configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `OneDrive` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Naam**| N.v.t. |Vereist - de variabele naam die wordt gebruikt in de functiecode voor het bestand. Zie [Een OneDrive-bestandsinvoerbinding van code gebruiken](#onedrive-input-code).|
|**Type**| N.v.t. |Vereist - moet `onedrive`worden ingesteld op .|
|**direction**| N.v.t. |Vereist - moet `in`worden ingesteld op .|
|**Identiteit**|**Identiteit**|Vereist - De identiteit die wordt gebruikt om de actie uit te voeren. Kan een van de volgende waarden zijn:<ul><li><code>userFromRequest</code>- Alleen geldig met [HTTP-trigger]. Gebruikt de identiteit van de aanroepende gebruiker.</li><li><code>userFromId</code>- Gebruikt de identiteit van een eerder ingelogde gebruiker met de opgegeven ID. Zie <code>userId</code> het pand.</li><li><code>userFromToken</code>- Gebruikt de identiteit die wordt weergegeven door het opgegeven token. Zie <code>userToken</code> het pand.</li><li><code>clientCredentials</code>- Gebruikt de identiteit van de functie-app.</li></ul>|
|**Userid**|**Userid**  |Nodig als en _identity_ alleen als `userFromId`identiteit is ingesteld op . Een hoofd-id van de gebruiker die is gekoppeld aan een eerder ingelogde gebruiker.|
|**userToken**|**UserToken**|Nodig als en _identity_ alleen als `userFromToken`identiteit is ingesteld op . Een token dat geldig is voor de functie-app. |
|**Pad**|**Pad**|Vereist - het pad in OneDrive naar het bestand.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Bestandsinvoer - gebruik

Voor deze binding zijn de volgende Azure AD-machtigingen vereist:

|Resource|Machtiging|
|--------|--------|
|Microsoft Graph|Gebruikersbestanden lezen|

Met de binding worden de volgende typen blootgesteld aan .NET-functies:
- byte[]
- Streamen
- tekenreeks
- Microsoft.Graph.DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>Bestandsuitvoer

De uitvoerbinding van Het OneDrive-bestand wijzigt de inhoud van een bestand dat is opgeslagen in OneDrive.

Deze sectie bevat de volgende onderafdelingen:

* [Voorbeeld](#file-output---example)
* [Kenmerken](#file-output---attributes)
* [Configuratie](#file-output---configuration)
* [Gebruik](#file-output---usage)

### <a name="file-output---example"></a>Bestandsuitvoer - voorbeeld

Zie het taalspecifieke voorbeeld:

* [C#-script (.csx)](#file-output---c-script-example)
* [Javascript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Bestandsuitvoer - C#-scriptvoorbeeld

In het volgende voorbeeld wordt geschreven naar een bestand dat is opgeslagen in OneDrive.

Het *bestand function.json* definieert een HTTP-trigger met een OneDrive-uitvoerbinding:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

De C#-scriptcode krijgt tekst uit de querytekenreeks en schrijft deze naar een tekstbestand (FunctionsTest.txt zoals gedefinieerd in het voorgaande voorbeeld) aan de hoofdlijn van de OneDrive van de beller:

```csharp
using System.Net;
using System.Text;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, ILogger log, Stream myOneDriveFile)
{
    string data = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await myOneDriveFile.WriteAsync(Encoding.UTF8.GetBytes(data), 0, data.Length);
    myOneDriveFile.Close();
    return;
}
```

#### <a name="file-output---javascript-example"></a>Bestandsuitvoer - JavaScript-voorbeeld

In het volgende voorbeeld wordt geschreven naar een bestand dat is opgeslagen in OneDrive.

Het *bestand function.json* definieert een HTTP-trigger met een OneDrive-uitvoerbinding:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

De JavaScript-code krijgt tekst uit de querytekenreeks en schrijft deze naar een tekstbestand (FunctionsTest.txt zoals gedefinieerd in de config hierboven) aan de basis van de OneDrive van de beller.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Bestandsuitvoer - kenmerken

Gebruik in [klassebibliotheken van C#](functions-dotnet-class-library.md)het kenmerk [OneDrive.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs)

### <a name="file-output---configuration"></a>Bestandsuitvoer - configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `OneDrive` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Naam**| N.v.t. |Vereist - de variabele naam die wordt gebruikt in functiecode voor het bestand. Zie [Een OneDrive-bestandsuitvoerbinding gebruiken van code](#onedrive-output-code).|
|**Type**| N.v.t. |Vereist - moet `onedrive`worden ingesteld op .|
|**direction**| N.v.t. |Vereist - moet `out`worden ingesteld op .|
|**Identiteit**|**Identiteit**|Vereist - De identiteit die wordt gebruikt om de actie uit te voeren. Kan een van de volgende waarden zijn:<ul><li><code>userFromRequest</code>- Alleen geldig met [HTTP-trigger]. Gebruikt de identiteit van de aanroepende gebruiker.</li><li><code>userFromId</code>- Gebruikt de identiteit van een eerder ingelogde gebruiker met de opgegeven ID. Zie <code>userId</code> het pand.</li><li><code>userFromToken</code>- Gebruikt de identiteit die wordt weergegeven door het opgegeven token. Zie <code>userToken</code> het pand.</li><li><code>clientCredentials</code>- Gebruikt de identiteit van de functie-app.</li></ul>|
|**Userid** |**Userid** |Nodig als en _identity_ alleen als `userFromId`identiteit is ingesteld op . Een hoofd-id van de gebruiker die is gekoppeld aan een eerder ingelogde gebruiker.|
|**userToken**|**UserToken**|Nodig als en _identity_ alleen als `userFromToken`identiteit is ingesteld op . Een token dat geldig is voor de functie-app. |
|**Pad**|**Pad**|Vereist - het pad in OneDrive naar het bestand.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Bestandsuitvoer - gebruik

Voor deze binding zijn de volgende Azure AD-machtigingen vereist:

|Resource|Machtiging|
|--------|--------|
|Microsoft Graph|Volledige toegang hebben tot gebruikersbestanden|

Met de binding worden de volgende typen blootgesteld aan .NET-functies:
- byte[]
- Streamen
- tekenreeks
- Microsoft.Graph.DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Outlook-uitvoer

De uitvoerbinding van het Outlook-bericht verzendt een e-mailbericht via Outlook.

Deze sectie bevat de volgende onderafdelingen:

* [Voorbeeld](#outlook-output---example)
* [Kenmerken](#outlook-output---attributes)
* [Configuratie](#outlook-output---configuration)
* [Gebruik](#outlook-output---usage)

### <a name="outlook-output---example"></a>Outlook-uitvoer - voorbeeld

Zie het taalspecifieke voorbeeld:

* [C#-script (.csx)](#outlook-output---c-script-example)
* [Javascript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Outlook-uitvoer - C#-scriptvoorbeeld

In het volgende voorbeeld wordt een e-mail via Outlook gestuurd.

Het *bestand functie.json* definieert een HTTP-trigger met een Outlook-berichtuitvoerbinding:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

De C#-scriptcode stuurt een e-mail van de beller naar een ontvanger die is opgegeven in de querytekenreeks:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequest req, out Message message, ILogger log)
{ 
    string emailAddress = req.Query["to"];
    message = new Message(){
        subject = "Greetings",
        body = "Sent from Azure Functions",
        recipient = new Recipient() {
            address = emailAddress
        }
    };
}

public class Message {
    public String subject {get; set;}
    public String body {get; set;}
    public Recipient recipient {get; set;}
}

public class Recipient {
    public String address {get; set;}
    public String name {get; set;}
}
```

#### <a name="outlook-output---javascript-example"></a>Outlook-uitvoer - JavaScript-voorbeeld

In het volgende voorbeeld wordt een e-mail via Outlook gestuurd.

Het *bestand functie.json* definieert een HTTP-trigger met een Outlook-berichtuitvoerbinding:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

De JavaScript-code stuurt een e-mail van de beller naar een geadresseerde die is opgegeven in de querytekenreeks:

```js
module.exports = function (context, req) {
    context.bindings.message = {
        subject: "Greetings",
        body: "Sent from Azure Functions with JavaScript",
        recipient: {
            address: req.query.to 
        } 
    };
    context.done();
};
```

### <a name="outlook-output---attributes"></a>Outlook-uitvoer - kenmerken

Gebruik [in klassebibliotheken van C#](functions-dotnet-class-library.md)het kenmerk [Outlook.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs)

### <a name="outlook-output---configuration"></a>Outlook-uitvoer - configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `Outlook` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Naam**| N.v.t. |Vereist - de variabele naam die wordt gebruikt in de functiecode voor het e-mailbericht. Zie [Een Outlook-berichtuitvoerbinding gebruiken van code](#outlook-output-code).|
|**Type**| N.v.t. |Vereist - moet `outlook`worden ingesteld op .|
|**direction**| N.v.t. |Vereist - moet `out`worden ingesteld op .|
|**Identiteit**|**Identiteit**|Vereist - De identiteit die wordt gebruikt om de actie uit te voeren. Kan een van de volgende waarden zijn:<ul><li><code>userFromRequest</code>- Alleen geldig met [HTTP-trigger]. Gebruikt de identiteit van de aanroepende gebruiker.</li><li><code>userFromId</code>- Gebruikt de identiteit van een eerder ingelogde gebruiker met de opgegeven ID. Zie <code>userId</code> het pand.</li><li><code>userFromToken</code>- Gebruikt de identiteit die wordt weergegeven door het opgegeven token. Zie <code>userToken</code> het pand.</li><li><code>clientCredentials</code>- Gebruikt de identiteit van de functie-app.</li></ul>|
|**Userid**|**Userid**  |Nodig als en _identity_ alleen als `userFromId`identiteit is ingesteld op . Een hoofd-id van de gebruiker die is gekoppeld aan een eerder ingelogde gebruiker.|
|**userToken**|**UserToken**|Nodig als en _identity_ alleen als `userFromToken`identiteit is ingesteld op . Een token dat geldig is voor de functie-app. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Outlook-uitvoer - gebruik

Voor deze binding zijn de volgende Azure AD-machtigingen vereist:

|Resource|Machtiging|
|--------|--------|
|Microsoft Graph|E-mail verzenden als gebruiker|

Met de binding worden de volgende typen blootgesteld aan .NET-functies:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- tekenreeks
- Aangepaste objecttypen (met structurele modelbinding)






## <a name="webhooks"></a>Webhooks

Met webhooks u reageren op gebeurtenissen in de Microsoft Graph. Om webhooks te ondersteunen, zijn functies nodig om _webhook-abonnementen_te maken, te vernieuwen en erop te reageren. Een complete webhook oplossing vereist een combinatie van de volgende bindingen:
- Met een [Microsoft Graph-webhook-trigger](#webhook-trigger) u reageren op een inkomende webhook.
- Met een [Microsoft Graph-invoerinvoerbinding voor webhooks](#webhook-input) u bestaande abonnementen aanbieden en deze optioneel vernieuwen.
- Met een [Microsoft Graph-webhook-abonnementsuitvoerbinding](#webhook-output) u webhook-abonnementen maken of verwijderen.

Voor de bindingen zelf zijn geen Azure AD-machtigingen vereist, maar u moet machtigingen aanvragen die relevant zijn voor het resourcetype waarop u wilt reageren. Zie [abonnementsmachtigingen](https://docs.microsoft.com/graph/api/subscription-post-subscriptions?view=graph-rest-1.0)voor een lijst van welke machtigingen nodig zijn voor elk resourcetype.

Zie [Werken met webhooks in Microsoft Graph]voor meer informatie over webhooks.





## <a name="webhook-trigger"></a>Webhook-trigger

Met de Microsoft Graph-webhook-trigger kan een functie reageren op een binnenkomende webhook uit de Microsoft Graph. Elke instantie van deze trigger kan reageren op één Microsoft Graph-resourcetype.

Deze sectie bevat de volgende onderafdelingen:

* [Voorbeeld](#webhook-trigger---example)
* [Kenmerken](#webhook-trigger---attributes)
* [Configuratie](#webhook-trigger---configuration)
* [Gebruik](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Webhook trigger - voorbeeld

Zie het taalspecifieke voorbeeld:

* [C#-script (.csx)](#webhook-trigger---c-script-example)
* [Javascript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Webhook trigger - C# script voorbeeld

In het volgende voorbeeld worden webhooks voor binnenkomende Outlook-berichten verwerkt. Als u een webhook-trigger wilt gebruiken, maakt u [een abonnement](#webhook-output---example)en u het [abonnement vernieuwen](#webhook-subscription-refresh) om te voorkomen dat het afloopt.

Het *bestand function.json* definieert een webhooktrigger:

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

De C#-scriptcode reageert op binnenkomende e-mailberichten en registreert de hoofdtekst van de berichten die door de ontvanger worden verzonden en die 'Azure-functies' in het onderwerp bevatten:

```csharp
#r "Microsoft.Graph"
using Microsoft.Graph;
using System.Net;
using Microsoft.Extensions.Logging;

public static async Task Run(Message msg, ILogger log)  
{
    log.LogInformation("Microsoft Graph webhook trigger function processed a request.");

    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if (msg.Subject.Contains("Azure Functions") && msg.From.Equals(msg.Sender)) {
        log.LogInformation($"Processed email: {msg.BodyPreview}");
    }
}
```

#### <a name="webhook-trigger---javascript-example"></a>Webhook trigger - JavaScript voorbeeld

In het volgende voorbeeld worden webhooks voor binnenkomende Outlook-berichten verwerkt. Als u een webhook-trigger wilt gebruiken, maakt u [een abonnement](#webhook-output---example)en u het [abonnement vernieuwen](#webhook-subscription-refresh) om te voorkomen dat het afloopt.

Het *bestand function.json* definieert een webhooktrigger:

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

De JavaScript-code reageert op binnenkomende e-mailberichten en registreert de hoofdtekst van de berichten die door de ontvanger worden verzonden en met "Azure-functies" in het onderwerp:

```js
module.exports = function (context) {
    context.log("Microsoft Graph webhook trigger function processed a request.");
    const msg = context.bindings.msg
    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if((msg.subject.indexOf("Azure Functions") > -1) && (msg.from === msg.sender) ) {
      context.log(`Processed email: ${msg.bodyPreview}`);
    }
    context.done();
};
```

### <a name="webhook-trigger---attributes"></a>Webhook trigger - attributen

Gebruik in [klassebibliotheken van C#](functions-dotnet-class-library.md)het kenmerk [GraphWebhookTrigger.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookTriggerAttribute.cs)

### <a name="webhook-trigger---configuration"></a>Webhook trigger - configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `GraphWebhookTrigger` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Naam**| N.v.t. |Vereist - de variabele naam die wordt gebruikt in de functiecode voor het e-mailbericht. Zie [Een Outlook-berichtuitvoerbinding gebruiken van code](#outlook-output-code).|
|**Type**| N.v.t. |Vereist - moet `graphWebhook`worden ingesteld op .|
|**direction**| N.v.t. |Vereist - moet `trigger`worden ingesteld op .|
|**Resourcetype**|**ResourceType**|Vereist - de grafiekbron waarvoor deze functie moet reageren op webhooks. Kan een van de volgende waarden zijn:<ul><li><code>#Microsoft.Graph.Message</code>- wijzigingen in Outlook-berichten.</li><li><code>#Microsoft.Graph.DriveItem</code>- wijzigingen in de hoofditems van OneDrive.</li><li><code>#Microsoft.Graph.Contact</code>- wijzigingen in persoonlijke contactpersonen in Outlook.</li><li><code>#Microsoft.Graph.Event</code>- wijzigingen in Outlook-agenda-items.</li></ul>|

> [!Note]
> Een functie-app kan slechts één functie `resourceType` hebben die is geregistreerd tegen een bepaalde waarde.

### <a name="webhook-trigger---usage"></a>Webhook trigger - gebruik

Met de binding worden de volgende typen blootgesteld aan .NET-functies:
- Microsoft Graph SDK-typen die relevant `Microsoft.Graph.Message` zijn `Microsoft.Graph.DriveItem`voor het resourcetype, zoals of .
- Aangepaste objecttypen (met structurele modelbinding)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Webhook-invoer

Met de Microsoft Graph-invoerbinding u de lijst met abonnementen ophalen die door deze functie-app worden beheerd. De binding leest uit de opslag van de functie-app, zodat deze geen andere abonnementen weergeeft die van buiten de app zijn gemaakt.

Deze sectie bevat de volgende onderafdelingen:

* [Voorbeeld](#webhook-input---example)
* [Kenmerken](#webhook-input---attributes)
* [Configuratie](#webhook-input---configuration)
* [Gebruik](#webhook-input---usage)

### <a name="webhook-input---example"></a>Webhook-invoer - voorbeeld

Zie het taalspecifieke voorbeeld:

* [C#-script (.csx)](#webhook-input---c-script-example)
* [Javascript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Webhook-invoer - C#-scriptvoorbeeld

In het volgende voorbeeld worden alle abonnementen voor de aanroepende gebruiker opdeiert en verwijderd.

Het *bestand function.json* definieert een HTTP-trigger met een binding met abonnementsinvoer en een binding met de uitvoer van abonnementen die de verwijderactie gebruikt:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

De C#-scriptcode krijgt de abonnementen en verwijdert deze:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, string[] existingSubscriptions, IAsyncCollector<string> subscriptionsToDelete, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    foreach (var subscription in existingSubscriptions)
    {
        log.LogInformation($"Deleting subscription {subscription}");
        await subscriptionsToDelete.AddAsync(subscription);
    }
}
```

#### <a name="webhook-input---javascript-example"></a>Webhook-invoer - JavaScript-voorbeeld

In het volgende voorbeeld worden alle abonnementen voor de aanroepende gebruiker opdeiert en verwijderd.

Het *bestand function.json* definieert een HTTP-trigger met een binding met abonnementsinvoer en een binding met de uitvoer van abonnementen die de verwijderactie gebruikt:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

De JavaScript-code krijgt de abonnementen en verwijdert deze:

```js
module.exports = function (context, req) {
    const existing = context.bindings.existingSubscriptions;
    var toDelete = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToDelete = toDelete;
    context.done();
};
```

### <a name="webhook-input---attributes"></a>Webhook-invoer - kenmerken

Gebruik in [klassebibliotheken van C#](functions-dotnet-class-library.md)het kenmerk [GraphWebhookSubscription.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs)

### <a name="webhook-input---configuration"></a>Webhook-invoer - configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `GraphWebhookSubscription` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Naam**| N.v.t. |Vereist - de variabele naam die wordt gebruikt in de functiecode voor het e-mailbericht. Zie [Een Outlook-berichtuitvoerbinding gebruiken van code](#outlook-output-code).|
|**Type**| N.v.t. |Vereist - moet `graphWebhookSubscription`worden ingesteld op .|
|**direction**| N.v.t. |Vereist - moet `in`worden ingesteld op .|
|**Filter**|**Filter**| Als ingesteld `userFromRequest`op , dan is de binding alleen ophalen abonnementen die eigendom zijn van de aanroepende gebruiker (alleen geldig met [HTTP trigger]).| 

### <a name="webhook-input---usage"></a>Webhook-invoer - gebruik

Met de binding worden de volgende typen blootgesteld aan .NET-functies:
- tekenreeks[]
- Aangepaste objecttekstarrays
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.Subscription[]





## <a name="webhook-output"></a>Webhook-uitvoer

Met de uitvoerbinding voor webhook-abonnementen u webhook-abonnementen maken, verwijderen en vernieuwen in de Microsoft Graph.

Deze sectie bevat de volgende onderafdelingen:

* [Voorbeeld](#webhook-output---example)
* [Kenmerken](#webhook-output---attributes)
* [Configuratie](#webhook-output---configuration)
* [Gebruik](#webhook-output---usage)

### <a name="webhook-output---example"></a>Webhook-uitvoer - voorbeeld

Zie het taalspecifieke voorbeeld:

* [C#-script (.csx)](#webhook-output---c-script-example)
* [Javascript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Webhook-uitvoer - C#-scriptvoorbeeld

In het volgende voorbeeld wordt een abonnement gemaakt. U [het abonnement vernieuwen](#webhook-subscription-refresh) om te voorkomen dat het afloopt.

Het *bestand function.json* definieert een HTTP-trigger met een binding met een abonnementsuitvoer met de actie maken:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

De C#-scriptcode registreert een webhook die deze functie-app op de hoogte brengt wanneer de aanroepende gebruiker een Outlook-bericht ontvangt:

```csharp
using System;
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage run(HttpRequestMessage req, out string clientState, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
    clientState = Guid.NewGuid().ToString();
    return new HttpResponseMessage(HttpStatusCode.OK);
}
```

#### <a name="webhook-output---javascript-example"></a>Webhook-uitvoer - JavaScript-voorbeeld

In het volgende voorbeeld wordt een abonnement gemaakt. U [het abonnement vernieuwen](#webhook-subscription-refresh) om te voorkomen dat het afloopt.

Het *bestand function.json* definieert een HTTP-trigger met een binding met een abonnementsuitvoer met de actie maken:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

De JavaScript-code registreert een webhook die deze functie-app op de hoogte brengt wanneer de aanroepende gebruiker een Outlook-bericht ontvangt:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Webhook-uitvoer - kenmerken

Gebruik in [klassebibliotheken van C#](functions-dotnet-class-library.md)het kenmerk [GraphWebhookSubscription.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs)

### <a name="webhook-output---configuration"></a>Webhook-uitvoer - configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `GraphWebhookSubscription` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Naam**| N.v.t. |Vereist - de variabele naam die wordt gebruikt in de functiecode voor het e-mailbericht. Zie [Een Outlook-berichtuitvoerbinding gebruiken van code](#outlook-output-code).|
|**Type**| N.v.t. |Vereist - moet `graphWebhookSubscription`worden ingesteld op .|
|**direction**| N.v.t. |Vereist - moet `out`worden ingesteld op .|
|**Identiteit**|**Identiteit**|Vereist - De identiteit die wordt gebruikt om de actie uit te voeren. Kan een van de volgende waarden zijn:<ul><li><code>userFromRequest</code>- Alleen geldig met [HTTP-trigger]. Gebruikt de identiteit van de aanroepende gebruiker.</li><li><code>userFromId</code>- Gebruikt de identiteit van een eerder ingelogde gebruiker met de opgegeven ID. Zie <code>userId</code> het pand.</li><li><code>userFromToken</code>- Gebruikt de identiteit die wordt weergegeven door het opgegeven token. Zie <code>userToken</code> het pand.</li><li><code>clientCredentials</code>- Gebruikt de identiteit van de functie-app.</li></ul>|
|**Userid**|**Userid**  |Nodig als en _identity_ alleen als `userFromId`identiteit is ingesteld op . Een hoofd-id van de gebruiker die is gekoppeld aan een eerder ingelogde gebruiker.|
|**userToken**|**UserToken**|Nodig als en _identity_ alleen als `userFromToken`identiteit is ingesteld op . Een token dat geldig is voor de functie-app. |
|**Actie**|**Actie**|Vereist - geeft de actie aan die de binding moet uitvoeren. Kan een van de volgende waarden zijn:<ul><li><code>create</code>- Registreert een nieuw abonnement.</li><li><code>delete</code>- Verwijdert een opgegeven abonnement.</li><li><code>refresh</code>- Vernieuwt een opgegeven abonnement om te voorkomen dat het verloopt.</li></ul>|
|**abonnementResource**|**AbonnementBron**|Nodig als en _action_ alleen als `create`de actie is ingesteld op . Hiermee geeft u de Microsoft Graph-bron op die wordt gecontroleerd op wijzigingen. Zie [Werken met webhooks in Microsoft Graph]. |
|**changeType**|**ChangeType (ChangeType)**|Nodig als en _action_ alleen als `create`de actie is ingesteld op . Geeft het type wijziging in de geabonneerde resource aan waarmee een melding wordt gedaan. De ondersteunde waarden `created`zijn: , `updated`, `deleted`. Meerdere waarden kunnen worden gecombineerd met behulp van een door komma's gescheiden lijst.|

### <a name="webhook-output---usage"></a>Webhook-uitvoer - gebruik

Met de binding worden de volgende typen blootgesteld aan .NET-functies:
- tekenreeks
- Microsoft.Graph.Abonnement




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Webhook-abonnement vernieuwt

Er zijn twee benaderingen voor het vernieuwen van abonnementen:

- Gebruik de toepassingsidentiteit om alle abonnementen af te handelen. Hiervoor is toestemming nodig van een Azure Active Directory-beheerder. Dit kan worden gebruikt door alle talen die worden ondersteund door Azure-functies.
- Gebruik de identiteit die aan elk abonnement is gekoppeld door elke gebruikersnaam handmatig te binden. Hiervoor is een aangepaste code nodig om de binding uit te voeren. Dit kan alleen worden gebruikt door .NET-functies.

Deze sectie bevat een voorbeeld voor elk van deze benaderingen:

* [Voorbeeld van app-identiteit](#webhook-subscription-refresh---app-identity-example)
* [Voorbeeld van gebruikersidentiteit](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Webhook-abonnement vernieuwen - voorbeeld van app-identiteit

Zie het taalspecifieke voorbeeld:

* [C#-script (.csx)](#app-identity-refresh---c-script-example)
* JavaScript

### <a name="app-identity-refresh---c-script-example"></a>App-identiteit vernieuwen - C#-scriptvoorbeeld

In het volgende voorbeeld wordt de identiteit van de toepassing gebruikt om een abonnement te vernieuwen.

De *functie.json* definieert een timertrigger met een abonnementsinvoerbinding en een binding van abonnementsuitvoer:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

De C#-scriptcode vernieuwt de abonnementen:

```csharp
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, string[] existingSubscriptions, ICollector<string> subscriptionsToRefresh, ILogger log)
{
    // This template uses application permissions and requires consent from an Azure Active Directory admin.
    // See https://go.microsoft.com/fwlink/?linkid=858780
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
      log.LogInformation($"Refreshing subscription {subscription}");
      subscriptionsToRefresh.Add(subscription);
    }
}
```

### <a name="app-identity-refresh---c-script-example"></a>App-identiteit vernieuwen - C#-scriptvoorbeeld

In het volgende voorbeeld wordt de identiteit van de toepassing gebruikt om een abonnement te vernieuwen.

De *functie.json* definieert een timertrigger met een abonnementsinvoerbinding en een binding van abonnementsuitvoer:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

De JavaScript-code vernieuwt de abonnementen:

```js
// This template uses application permissions and requires consent from an Azure Active Directory admin.
// See https://go.microsoft.com/fwlink/?linkid=858780

module.exports = function (context) {
    const existing = context.bindings.existingSubscriptions;
    var toRefresh = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Refreshing subscription ${existing[i]}`);
        toRefresh.push(existing[i]);
    }
    context.bindings.subscriptionsToRefresh = toRefresh;
    context.done();
};
```

### <a name="webhook-subscription-refresh---user-identity-example"></a>Webhook-abonnement vernieuwen - voorbeeld van gebruikersidentiteit

In het volgende voorbeeld wordt de gebruikersidentiteit gebruikt om een abonnement te vernieuwen.

Het *bestand function.json* definieert een timertrigger en stelt de abonnementsinvoer die bindend is voor de functiecode uit:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

De C#-scriptcode vernieuwt de abonnementen en maakt de uitvoerbinding in code met behulp van de identiteit van elke gebruiker:

```csharp
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, UserSubscription[] existingSubscriptions, IBinder binder, ILogger log)
{
  log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
        // binding in code to allow dynamic identity
        using (var subscriptionsToRefresh = await binder.BindAsync<IAsyncCollector<string>>(
            new GraphWebhookSubscriptionAttribute() {
                Action = "refresh",
                Identity = "userFromId",
                UserId = subscription.UserId
            }
        ))
        {
            log.LogInformation($"Refreshing subscription {subscription}");
            await subscriptionsToRefresh.AddAsync(subscription);
        }

    }
}

public class UserSubscription {
    public string UserId {get; set;}
    public string Id {get; set;}
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over triggers en bindingen voor Azure-functies](functions-triggers-bindings.md)

[HTTP-trigger]: functions-bindings-http-webhook.md
[Werken met webhooks in Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
