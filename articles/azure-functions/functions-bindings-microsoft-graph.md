---
title: Microsoft Graph bindingen voor Azure Functions
description: Meer informatie over het gebruik van Microsoft Graph triggers en bindingen in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 12/20/2017
ms.author: cshoe
ms.openlocfilehash: 9dd4067d066362f5842b504971afbc59fd0717a3
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212213"
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Microsoft Graph bindingen voor Azure Functions

In dit artikel wordt uitgelegd hoe u Microsoft Graph triggers en bindingen kunt configureren en gebruiken in Azure Functions. Met deze kunt u Azure Functions gebruiken om te werken met gegevens, inzichten en gebeurtenissen van de [Microsoft Graph](https://developer.microsoft.com/graph).

De uitbrei ding Microsoft Graph biedt de volgende bindingen:
- Met een [invoer binding van een verificatie token](#token-input) kunt u communiceren met elke Microsoft Graph-API.
- Met een binding van de [Excel-tabel invoer](#excel-input) kunt u gegevens uit Excel lezen.
- Met een koppeling naar een [Excel-tabel uitvoer](#excel-output) kunt u Excel-gegevens wijzigen.
- Met een [OneDrive File-invoer binding](#onedrive-input) kunt u bestanden uit OneDrive lezen.
- Met een [OneDrive file-uitvoer binding](#onedrive-output) kunt u schrijven naar bestanden in OneDrive.
- Met een [Outlook-bericht uitvoer binding](#outlook-output) kunt u e-mail berichten verzenden via Outlook.
- Met een verzameling [Microsoft Graph-webhook-triggers en-bindingen](#webhooks) kunt u reageren op gebeurtenissen van de Microsoft Graph.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> Microsoft Graph bindingen zijn momenteel beschikbaar als Preview voor Azure Functions versie 2. x of hoger. Ze worden niet ondersteund in functies versie 1. x.

## <a name="packages"></a>Pakketten

De invoer binding van het auth-token is opgegeven in het pakket [micro soft. Azure. webjobs. Extensions. AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) NuGet. De andere Microsoft Graph bindingen zijn opgenomen in het pakket [micro soft. Azure. webjobs. Extensions. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) . De bron code voor de pakketten bevindt zich in de [Azure-functions-microsoftgraph-extension github-](https://github.com/Azure/azure-functions-microsoftgraph-extension/) opslag plaats.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="setting-up-the-extensions"></a>De uitbrei dingen instellen

Microsoft Graph bindingen zijn beschikbaar via de _bindings extensies_. Bindings uitbreidingen zijn optionele onderdelen voor de runtime van Azure Functions. In deze sectie wordt beschreven hoe u de Microsoft Graph-en auth-token uitbreidingen instelt.

### <a name="enabling-functions-20"></a>Functies 2,0 inschakelen

Bindings uitbreidingen zijn alleen beschikbaar voor Azure Functions 2,0. 

Zie [How to target Azure functions runtime](set-runtime-version.md)Versioning (Engelstalig) voor meer informatie over het instellen van een functie-app voor het gebruik van de 2,0-versie van de functions-runtime.

### <a name="installing-the-extension"></a>De extensie installeren

Als u een uitbrei ding van de Azure Portal wilt installeren, gaat u naar een sjabloon of binding die ernaar verwijst. Maak een nieuwe functie en kies in het scherm sjabloon selectie het scenario Microsoft Graph. Selecteer een van de sjablonen uit dit scenario. U kunt ook naar het tabblad integreren van een bestaande functie navigeren en een van de bindingen selecteren die in dit artikel worden besproken.

In beide gevallen wordt er een waarschuwing weer gegeven waarin wordt aangegeven welke extensie moet worden geïnstalleerd. Klik op **installeren** om de extensie te verkrijgen. Elke uitbrei ding hoeft slechts eenmaal per functie-app te worden geïnstalleerd. 

> [!Note] 
> Het installatie proces in de portal kan Maxi maal tien minuten duren op een verbruiks abonnement.

Als u Visual Studio gebruikt, kunt u de uitbrei dingen ophalen door [de NuGet-pakketten te installeren die eerder in dit artikel worden vermeld](#packages).

### <a name="configuring-authentication--authorization"></a>Verificatie/autorisatie configureren

Voor de bindingen die in dit artikel worden beschreven, moet een identiteit worden gebruikt. Hiermee kan de Microsoft Graph machtigingen en controle interacties afdwingen. De identiteit kan een gebruiker zijn die toegang heeft tot uw toepassing of de toepassing zelf. Als u deze identiteit wilt configureren, stelt u [app service verificatie/autorisatie](../app-service/overview-authentication-authorization.md) in met Azure Active Directory. U moet ook alle resource machtigingen aanvragen die uw functies vereisen.

> [!Note] 
> De uitbrei ding Microsoft Graph biedt alleen ondersteuning voor Azure AD-verificatie. Gebruikers moeten zich aanmelden met een werk-of school account.

Als u de Azure Portal gebruikt, wordt er een waarschuwing weer gegeven onder de prompt om de extensie te installeren. In de waarschuwing wordt u gevraagd App Service verificatie/autorisatie te configureren en alle machtigingen voor de sjabloon of binding aan te vragen. Klik op **Azure AD nu configureren** of **Voeg nu de machtigingen toe** , indien van toepassing.



<a name="token-input"></a>
## <a name="auth-token"></a>Verificatie token

De invoer binding van het auth-token krijgt een Azure AD-token voor een bepaalde resource en levert deze aan uw code als een teken reeks. De resource kan elk zijn waarvoor de toepassing machtigingen heeft. 

Deze sectie bevat de volgende subsecties:

* [Voorbeeld](#auth-token---example)
* [Kenmerken](#auth-token---attributes)
* [Configuratie](#auth-token---configuration)
* [Gebruik](#auth-token---usage)

### <a name="auth-token---example"></a>Verificatie token-voor beeld

Zie het taalspecifieke voor beeld:

* [C#-script (.csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Verificatie token-voor beeld van C#-script

In het volgende voor beeld worden gebruikers profiel gegevens opgehaald.

De *function.jsin* het bestand definieert een http-trigger met een token-invoer binding:

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

De C#-script code gebruikt het token om een HTTP-aanroep naar de Microsoft Graph uit te voeren en het resultaat te retour neren:

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

#### <a name="auth-token---javascript-example"></a>Verificatie token-java script-voor beeld

In het volgende voor beeld worden gebruikers profiel gegevens opgehaald.

De *function.jsin* het bestand definieert een http-trigger met een token-invoer binding:

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

De Java script-code gebruikt het token om een HTTP-aanroep naar de Microsoft Graph uit te voeren en het resultaat te retour neren.

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

### <a name="auth-token---attributes"></a>Verificatie token-kenmerken

Gebruik in [C# class libraries](functions-dotnet-class-library.md)het kenmerk [token](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs) .

### <a name="auth-token---configuration"></a>Verificatie token-configuratie

De volgende tabel bevat uitleg over de binding configuratie-eigenschappen die u hebt ingesteld in de *function.jsvoor* het bestand en het `Token` kenmerk.

|function.jsbij eigenschap | Kenmerk eigenschap |Beschrijving|
|---------|---------|----------------------|
|**name**| N.v.t. |Vereist: de naam van de variabele die wordt gebruikt in de functie code voor het verificatie token. Zie [een verificatie token-invoer binding van code gebruiken](#token-input-code).|
|**type**| N.v.t. |Vereist: moet worden ingesteld op `token` .|
|**direction**| N.v.t. |Vereist: moet worden ingesteld op `in` .|
|**identity**|**Identiteit**|Vereist: de identiteit die wordt gebruikt om de actie uit te voeren. Dit kan een van de volgende waarden zijn:<ul><li><code>userFromRequest</code> -Alleen geldig met [http-trigger]. Maakt gebruik van de identiteit van de aanroepende gebruiker.</li><li><code>userFromId</code> -Gebruikt de identiteit van een eerder aangemelde gebruiker met de opgegeven ID. Zie de <code>userId</code> eigenschap.</li><li><code>userFromToken</code> -Gebruikt de identiteit die wordt vertegenwoordigd door het opgegeven token. Zie de <code>userToken</code> eigenschap.</li><li><code>clientCredentials</code> -Maakt gebruik van de identiteit van de functie-app.</li></ul>|
|**userId**|**Naam**  |Alleen vereist als de _identiteit_ is ingesteld op `userFromId` . Een User Principal-ID die is gekoppeld aan een eerder aangemelde gebruiker.|
|**userToken**|**UserToken**|Alleen vereist als de _identiteit_ is ingesteld op `userFromToken` . Een token dat geldig is voor de functie-app. |
|**Resource**|**resource**|Vereist: een Azure AD-resource-URL waarvoor het token wordt aangevraagd.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Verificatie token-gebruik

Voor de binding zelf zijn geen Azure AD-machtigingen vereist, maar afhankelijk van hoe het token wordt gebruikt, moet u mogelijk aanvullende machtigingen aanvragen. Controleer de vereisten van de resource die u wilt gebruiken met het token.

Het token wordt altijd weer gegeven als een teken reeks.

> [!Note]
> Bij het lokaal ontwikkelen met een `userFromId` `userFromToken` of meer `userFromRequest` opties kan een vereist token [hand matig worden verkregen](https://github.com/Azure/azure-functions-microsoftgraph-extension/issues/54#issuecomment-392865857) en worden opgegeven in de `X-MS-TOKEN-AAD-ID-TOKEN` aanvraag header van een aanroepende client toepassing.


<a name="excel-input"></a>
## <a name="excel-input"></a>Excel-invoer

De binding van de Excel-tabel invoer leest de inhoud van een Excel-tabel die is opgeslagen in OneDrive.

Deze sectie bevat de volgende subsecties:

* [Voorbeeld](#excel-input---example)
* [Kenmerken](#excel-input---attributes)
* [Configuratie](#excel-input---configuration)
* [Gebruik](#excel-input---usage)

### <a name="excel-input---example"></a>Excel-invoer-voor beeld

Zie het taalspecifieke voor beeld:

* [C#-script (.csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Excel-invoer-C#-script voorbeeld

De volgende *function.jsin* het bestand definieert een http-trigger met een binding met Excel-invoer:

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

De volgende C#-script code leest de inhoud van de opgegeven tabel en retourneert deze naar de gebruiker:

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

#### <a name="excel-input---javascript-example"></a>Excel-invoer-java script-voor beeld

De volgende *function.jsin* het bestand definieert een http-trigger met een binding met Excel-invoer:

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

De volgende Java script-code leest de inhoud van de opgegeven tabel en retourneert deze naar de gebruiker.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Excel-invoer kenmerken

Gebruik in [C# class libraries](functions-dotnet-class-library.md)het kenmerk [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) .

### <a name="excel-input---configuration"></a>Excel-invoer-configuratie

De volgende tabel bevat uitleg over de binding configuratie-eigenschappen die u hebt ingesteld in de *function.jsvoor* het bestand en het `Excel` kenmerk.

|function.jsbij eigenschap | Kenmerk eigenschap |Beschrijving|
|---------|---------|----------------------|
|**name**| N.v.t. |Vereist: de naam van de variabele die wordt gebruikt in de functie code voor de Excel-tabel. Zie [een Excel-tabel-invoer binding gebruiken vanuit code](#excel-input-code).|
|**type**| N.v.t. |Vereist: moet worden ingesteld op `excel` .|
|**direction**| N.v.t. |Vereist: moet worden ingesteld op `in` .|
|**identity**|**Identiteit**|Vereist: de identiteit die wordt gebruikt om de actie uit te voeren. Dit kan een van de volgende waarden zijn:<ul><li><code>userFromRequest</code> -Alleen geldig met [http-trigger]. Maakt gebruik van de identiteit van de aanroepende gebruiker.</li><li><code>userFromId</code> -Gebruikt de identiteit van een eerder aangemelde gebruiker met de opgegeven ID. Zie de <code>userId</code> eigenschap.</li><li><code>userFromToken</code> -Gebruikt de identiteit die wordt vertegenwoordigd door het opgegeven token. Zie de <code>userToken</code> eigenschap.</li><li><code>clientCredentials</code> -Maakt gebruik van de identiteit van de functie-app.</li></ul>|
|**userId**|**Naam**  |Alleen vereist als de _identiteit_ is ingesteld op `userFromId` . Een User Principal-ID die is gekoppeld aan een eerder aangemelde gebruiker.|
|**userToken**|**UserToken**|Alleen vereist als de _identiteit_ is ingesteld op `userFromToken` . Een token dat geldig is voor de functie-app. |
|**programmapad**|**Pad**|Vereist: het pad in OneDrive naar de Excel-werkmap.|
|**Werkbladnaam**|**Werkbladnaam**|Het werk blad waarin de tabel is gevonden.|
|**tableName**|**TableName**|De naam van de tabel. Als u niets opgeeft, wordt de inhoud van het werk blad gebruikt.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Excel-invoer-gebruik

Voor deze binding zijn de volgende Azure AD-machtigingen vereist:

|Resource|Machtiging|
|--------|--------|
|Microsoft Graph|Gebruikers bestanden lezen|

Met de binding worden de volgende typen in .NET-functies weer gegeven:
- teken reeks [] []
- Micro soft. Graph. WorkbookTable
- Aangepaste object typen (met behulp van Structured model binding)










<a name="excel-output"></a>
## <a name="excel-output"></a>Excel-uitvoer

De Excel-uitvoer binding wijzigt de inhoud van een Excel-tabel die is opgeslagen in OneDrive.

Deze sectie bevat de volgende subsecties:

* [Voorbeeld](#excel-output---example)
* [Kenmerken](#excel-output---attributes)
* [Configuratie](#excel-output---configuration)
* [Gebruik](#excel-output---usage)

### <a name="excel-output---example"></a>Excel-uitvoer-voor beeld

Zie het taalspecifieke voor beeld:

* [C#-script (.csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Excel-uitvoer-voor beeld van C#-script

In het volgende voor beeld worden rijen toegevoegd aan een Excel-tabel.

De *function.jsin* het bestand definieert een http-trigger met een Excel-uitvoer binding:

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

De C#-script code voegt een nieuwe rij aan de tabel toe (wordt aangenomen dat deze één kolom bevat) op basis van de invoer van de query reeks:

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

#### <a name="excel-output---javascript-example"></a>Excel-uitvoer-voor beeld van Java script

In het volgende voor beeld worden rijen toegevoegd aan een Excel-tabel.

De *function.jsin* het bestand definieert een http-trigger met een Excel-uitvoer binding:

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

Met de volgende Java script-code wordt een nieuwe rij aan de tabel toegevoegd (aangenomen dat deze één kolom bevat) op basis van de invoer van de query teken reeks.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Excel-uitvoer kenmerken

Gebruik in [C# class libraries](functions-dotnet-class-library.md)het kenmerk [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) .

### <a name="excel-output---configuration"></a>Excel-uitvoer-configuratie

De volgende tabel bevat uitleg over de binding configuratie-eigenschappen die u hebt ingesteld in de *function.jsvoor* het bestand en het `Excel` kenmerk.

|function.jsbij eigenschap | Kenmerk eigenschap |Beschrijving|
|---------|---------|----------------------|
|**name**| N.v.t. |Vereist: de naam van de variabele die wordt gebruikt in de functie code voor het verificatie token. Zie [een Excel-tabel uitvoer binding van code gebruiken](#excel-output-code).|
|**type**| N.v.t. |Vereist: moet worden ingesteld op `excel` .|
|**direction**| N.v.t. |Vereist: moet worden ingesteld op `out` .|
|**identity**|**Identiteit**|Vereist: de identiteit die wordt gebruikt om de actie uit te voeren. Dit kan een van de volgende waarden zijn:<ul><li><code>userFromRequest</code> -Alleen geldig met [http-trigger]. Maakt gebruik van de identiteit van de aanroepende gebruiker.</li><li><code>userFromId</code> -Gebruikt de identiteit van een eerder aangemelde gebruiker met de opgegeven ID. Zie de <code>userId</code> eigenschap.</li><li><code>userFromToken</code> -Gebruikt de identiteit die wordt vertegenwoordigd door het opgegeven token. Zie de <code>userToken</code> eigenschap.</li><li><code>clientCredentials</code> -Maakt gebruik van de identiteit van de functie-app.</li></ul>|
|**Naam** |**userId** |Alleen vereist als de _identiteit_ is ingesteld op `userFromId` . Een User Principal-ID die is gekoppeld aan een eerder aangemelde gebruiker.|
|**userToken**|**UserToken**|Alleen vereist als de _identiteit_ is ingesteld op `userFromToken` . Een token dat geldig is voor de functie-app. |
|**programmapad**|**Pad**|Vereist: het pad in OneDrive naar de Excel-werkmap.|
|**Werkbladnaam**|**Werkbladnaam**|Het werk blad waarin de tabel is gevonden.|
|**tableName**|**TableName**|De naam van de tabel. Als u niets opgeeft, wordt de inhoud van het werk blad gebruikt.|
|**updateType**|**UpdateType**|Vereist: het type wijziging dat moet worden aangebracht in de tabel. Dit kan een van de volgende waarden zijn:<ul><li><code>update</code> -Vervangt de inhoud van de tabel in OneDrive.</li><li><code>append</code> -Voegt de payload toe aan het einde van de tabel in OneDrive door nieuwe rijen te maken.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Excel-uitvoer-gebruik

Voor deze binding zijn de volgende Azure AD-machtigingen vereist:

|Resource|Machtiging|
|--------|--------|
|Microsoft Graph|Volledige toegang tot gebruikers bestanden|

Met de binding worden de volgende typen in .NET-functies weer gegeven:
- teken reeks [] []
- Newtonsoft.Jsop. LINQ. JObject
- Micro soft. Graph. WorkbookTable
- Aangepaste object typen (met behulp van Structured model binding)





<a name="onedrive-input"></a>
## <a name="file-input"></a>Bestands invoer

Met de binding van OneDrive file input wordt de inhoud gelezen van een bestand dat is opgeslagen in OneDrive.

Deze sectie bevat de volgende subsecties:

* [Voorbeeld](#file-input---example)
* [Kenmerken](#file-input---attributes)
* [Configuratie](#file-input---configuration)
* [Gebruik](#file-input---usage)

### <a name="file-input---example"></a>Bestands invoer-voor beeld

Zie het taalspecifieke voor beeld:

* [C#-script (.csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Bestands invoer-C#-script voorbeeld

In het volgende voor beeld wordt een bestand gelezen dat is opgeslagen in OneDrive.

De *function.jsin* het bestand definieert een http-trigger met een OneDrive File-invoer binding:

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

De C#-script code leest het bestand dat is opgegeven in de query teken reeks en registreert de lengte ervan:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, ILogger log)
{
    log.LogInformation(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Bestands invoer-java script-voor beeld

In het volgende voor beeld wordt een bestand gelezen dat is opgeslagen in OneDrive.

De *function.jsin* het bestand definieert een http-trigger met een OneDrive File-invoer binding:

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

De volgende Java script-code leest het bestand dat is opgegeven in de query teken reeks en retourneert de lengte ervan.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Bestands invoer-kenmerken

Gebruik in [C# class libraries](functions-dotnet-class-library.md)het kenmerk [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) .

### <a name="file-input---configuration"></a>Bestands invoer-configuratie

De volgende tabel bevat uitleg over de binding configuratie-eigenschappen die u hebt ingesteld in de *function.jsvoor* het bestand en het `OneDrive` kenmerk.

|function.jsbij eigenschap | Kenmerk eigenschap |Beschrijving|
|---------|---------|----------------------|
|**name**| N.v.t. |Vereist: de naam van de variabele die wordt gebruikt in de functie code voor het bestand. Zie [een OneDrive File-invoer binding gebruiken vanuit code](#onedrive-input-code).|
|**type**| N.v.t. |Vereist: moet worden ingesteld op `onedrive` .|
|**direction**| N.v.t. |Vereist: moet worden ingesteld op `in` .|
|**identity**|**Identiteit**|Vereist: de identiteit die wordt gebruikt om de actie uit te voeren. Dit kan een van de volgende waarden zijn:<ul><li><code>userFromRequest</code> -Alleen geldig met [http-trigger]. Maakt gebruik van de identiteit van de aanroepende gebruiker.</li><li><code>userFromId</code> -Gebruikt de identiteit van een eerder aangemelde gebruiker met de opgegeven ID. Zie de <code>userId</code> eigenschap.</li><li><code>userFromToken</code> -Gebruikt de identiteit die wordt vertegenwoordigd door het opgegeven token. Zie de <code>userToken</code> eigenschap.</li><li><code>clientCredentials</code> -Maakt gebruik van de identiteit van de functie-app.</li></ul>|
|**userId**|**Naam**  |Alleen vereist als de _identiteit_ is ingesteld op `userFromId` . Een User Principal-ID die is gekoppeld aan een eerder aangemelde gebruiker.|
|**userToken**|**UserToken**|Alleen vereist als de _identiteit_ is ingesteld op `userFromToken` . Een token dat geldig is voor de functie-app. |
|**programmapad**|**Pad**|Vereist: het pad in OneDrive naar het bestand.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Bestands invoer-gebruik

Voor deze binding zijn de volgende Azure AD-machtigingen vereist:

|Resource|Machtiging|
|--------|--------|
|Microsoft Graph|Gebruikers bestanden lezen|

Met de binding worden de volgende typen in .NET-functies weer gegeven:
- byte []
- Stroom
- tekenreeks
- Micro soft. Graph. DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>Bestands uitvoer

Met de binding van het OneDrive-bestands uitvoer wijzigt u de inhoud van een bestand dat is opgeslagen in OneDrive.

Deze sectie bevat de volgende subsecties:

* [Voorbeeld](#file-output---example)
* [Kenmerken](#file-output---attributes)
* [Configuratie](#file-output---configuration)
* [Gebruik](#file-output---usage)

### <a name="file-output---example"></a>Bestands uitvoer-voor beeld

Zie het taalspecifieke voor beeld:

* [C#-script (.csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Bestands uitvoer-voor beeld van C#-script

In het volgende voor beeld wordt geschreven naar een bestand dat is opgeslagen in OneDrive.

De *function.jsin* het bestand definieert een http-trigger met een OneDrive-uitvoer binding:

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

De C#-script code haalt tekst op uit de query teken reeks en schrijft deze naar een tekst bestand (FunctionsTest.txt zoals gedefinieerd in het voor gaande voor beeld) in de hoofdmap van de OneDrive van de beller:

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

#### <a name="file-output---javascript-example"></a>Bestands uitvoer-voor beeld van Java script

In het volgende voor beeld wordt geschreven naar een bestand dat is opgeslagen in OneDrive.

De *function.jsin* het bestand definieert een http-trigger met een OneDrive-uitvoer binding:

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

De Java script-code haalt tekst op uit de query teken reeks en schrijft deze naar een tekst bestand (FunctionsTest.txt zoals gedefinieerd in de bovenstaande configuratie) in de hoofdmap van de OneDrive van de beller.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Bestands uitvoer-kenmerken

Gebruik in [C# class libraries](functions-dotnet-class-library.md)het kenmerk [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) .

### <a name="file-output---configuration"></a>Bestands uitvoer-configuratie

De volgende tabel bevat uitleg over de binding configuratie-eigenschappen die u hebt ingesteld in de *function.jsvoor* het bestand en het `OneDrive` kenmerk.

|function.jsbij eigenschap | Kenmerk eigenschap |Beschrijving|
|---------|---------|----------------------|
|**name**| N.v.t. |Vereist: de naam van de variabele die wordt gebruikt in de functie code voor het bestand. Zie [een OneDrive file-uitvoer binding gebruiken vanuit code](#onedrive-output-code).|
|**type**| N.v.t. |Vereist: moet worden ingesteld op `onedrive` .|
|**direction**| N.v.t. |Vereist: moet worden ingesteld op `out` .|
|**identity**|**Identiteit**|Vereist: de identiteit die wordt gebruikt om de actie uit te voeren. Dit kan een van de volgende waarden zijn:<ul><li><code>userFromRequest</code> -Alleen geldig met [http-trigger]. Maakt gebruik van de identiteit van de aanroepende gebruiker.</li><li><code>userFromId</code> -Gebruikt de identiteit van een eerder aangemelde gebruiker met de opgegeven ID. Zie de <code>userId</code> eigenschap.</li><li><code>userFromToken</code> -Gebruikt de identiteit die wordt vertegenwoordigd door het opgegeven token. Zie de <code>userToken</code> eigenschap.</li><li><code>clientCredentials</code> -Maakt gebruik van de identiteit van de functie-app.</li></ul>|
|**Naam** |**userId** |Alleen vereist als de _identiteit_ is ingesteld op `userFromId` . Een User Principal-ID die is gekoppeld aan een eerder aangemelde gebruiker.|
|**userToken**|**UserToken**|Alleen vereist als de _identiteit_ is ingesteld op `userFromToken` . Een token dat geldig is voor de functie-app. |
|**programmapad**|**Pad**|Vereist: het pad in OneDrive naar het bestand.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Bestands uitvoer-gebruik

Voor deze binding zijn de volgende Azure AD-machtigingen vereist:

|Resource|Machtiging|
|--------|--------|
|Microsoft Graph|Volledige toegang tot gebruikers bestanden|

Met de binding worden de volgende typen in .NET-functies weer gegeven:
- byte []
- Stroom
- tekenreeks
- Micro soft. Graph. DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Outlook-uitvoer

De Outlook-bericht uitvoer binding verzendt een e-mail bericht via Outlook.

Deze sectie bevat de volgende subsecties:

* [Voorbeeld](#outlook-output---example)
* [Kenmerken](#outlook-output---attributes)
* [Configuratie](#outlook-output---configuration)
* [Gebruik](#outlook-output---usage)

### <a name="outlook-output---example"></a>Outlook-uitvoer-voor beeld

Zie het taalspecifieke voor beeld:

* [C#-script (.csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Outlook-uitvoer-voor beeld van C#-script

In het volgende voor beeld wordt een e-mail via Outlook verzonden.

De *function.jsin* het bestand definieert een http-trigger met een Outlook-bericht uitvoer binding:

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

De C#-script code verzendt een e-mail van de aanroeper naar een ontvanger die is opgegeven in de query reeks:

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

#### <a name="outlook-output---javascript-example"></a>Outlook-uitvoer-voor beeld van Java script

In het volgende voor beeld wordt een e-mail via Outlook verzonden.

De *function.jsin* het bestand definieert een http-trigger met een Outlook-bericht uitvoer binding:

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

De Java script-code verzendt een e-mail van de aanroeper naar een ontvanger die is opgegeven in de query reeks:

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

### <a name="outlook-output---attributes"></a>Uitvoer kenmerken van Outlook

Gebruik in [C# class libraries](functions-dotnet-class-library.md)het kenmerk [Outlook](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs) .

### <a name="outlook-output---configuration"></a>Outlook-uitvoer-configuratie

De volgende tabel bevat uitleg over de binding configuratie-eigenschappen die u hebt ingesteld in de *function.jsvoor* het bestand en het `Outlook` kenmerk.

|function.jsbij eigenschap | Kenmerk eigenschap |Beschrijving|
|---------|---------|----------------------|
|**name**| N.v.t. |Vereist: de naam van de variabele die wordt gebruikt in de functie code voor het e-mail bericht. Zie [een Outlook-bericht uitvoer binding van code gebruiken](#outlook-output-code).|
|**type**| N.v.t. |Vereist: moet worden ingesteld op `outlook` .|
|**direction**| N.v.t. |Vereist: moet worden ingesteld op `out` .|
|**identity**|**Identiteit**|Vereist: de identiteit die wordt gebruikt om de actie uit te voeren. Dit kan een van de volgende waarden zijn:<ul><li><code>userFromRequest</code> -Alleen geldig met [http-trigger]. Maakt gebruik van de identiteit van de aanroepende gebruiker.</li><li><code>userFromId</code> -Gebruikt de identiteit van een eerder aangemelde gebruiker met de opgegeven ID. Zie de <code>userId</code> eigenschap.</li><li><code>userFromToken</code> -Gebruikt de identiteit die wordt vertegenwoordigd door het opgegeven token. Zie de <code>userToken</code> eigenschap.</li><li><code>clientCredentials</code> -Maakt gebruik van de identiteit van de functie-app.</li></ul>|
|**userId**|**Naam**  |Alleen vereist als de _identiteit_ is ingesteld op `userFromId` . Een User Principal-ID die is gekoppeld aan een eerder aangemelde gebruiker.|
|**userToken**|**UserToken**|Alleen vereist als de _identiteit_ is ingesteld op `userFromToken` . Een token dat geldig is voor de functie-app. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Outlook-uitvoer gebruik

Voor deze binding zijn de volgende Azure AD-machtigingen vereist:

|Resource|Machtiging|
|--------|--------|
|Microsoft Graph|E-mail verzenden als gebruiker|

Met de binding worden de volgende typen in .NET-functies weer gegeven:
- Micro soft. Graph. Message
- Newtonsoft.Jsop. LINQ. JObject
- tekenreeks
- Aangepaste object typen (met behulp van Structured model binding)






## <a name="webhooks"></a>Webhooks

Met webhooks kunt u reageren op gebeurtenissen in de Microsoft Graph. Voor het ondersteunen van webhooks zijn functies nodig voor het maken, vernieuwen en reageren op _webhook-abonnementen_. Voor een volledige webhook-oplossing is een combi natie van de volgende bindingen vereist:
- Met een [Microsoft Graph-webhook-trigger](#webhook-trigger) kunt u reageren op een binnenkomende webhook.
- Met een [invoer binding van het Microsoft Graph webhook-abonnement](#webhook-input) kunt u bestaande abonnementen weer geven en eventueel vernieuwen.
- Met een Microsoft Graph-koppeling van een [webhook-abonnement](#webhook-output) kunt u webhook-abonnementen maken of verwijderen.

Voor de bindingen zelf zijn geen Azure AD-machtigingen vereist, maar u moet machtigingen aanvragen die relevant zijn voor het bron type waarop u wilt reageren. Zie [abonnements machtigingen](/graph/api/subscription-post-subscriptions?view=graph-rest-1.0)voor een lijst met de machtigingen die nodig zijn voor elk bron type.

Zie [werken met webhooks in Microsoft Graph]voor meer informatie over webhooks.





## <a name="webhook-trigger"></a>Webhook-trigger

Met de trigger voor het Microsoft Graph-webhook kan een functie reageren op een binnenkomende webhook vanuit de Microsoft Graph. Elke instantie van deze trigger kan reageren op één Microsoft Graph-resourcetype.

Deze sectie bevat de volgende subsecties:

* [Voorbeeld](#webhook-trigger---example)
* [Kenmerken](#webhook-trigger---attributes)
* [Configuratie](#webhook-trigger---configuration)
* [Gebruik](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Webhook-trigger-voor beeld

Zie het taalspecifieke voor beeld:

* [C#-script (.csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Webhook-trigger-C#-script voorbeeld

In het volgende voor beeld worden webhooks voor binnenkomende Outlook-berichten verwerkt. Als u een webhook-trigger wilt gebruiken, [maakt u een abonnement](#webhook-output---example)en kunt u [het abonnement vernieuwen](#webhook-subscription-refresh) om te voor komen dat het wordt verloopt.

De *function.jsin* het bestand definieert een webhook-trigger:

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

De C#-script code reageert op binnenkomende e-mail berichten en registreert de hoofd tekst van degenen die zijn verzonden door de ontvanger en die Azure Functions bevat in het onderwerp:

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

#### <a name="webhook-trigger---javascript-example"></a>Webhook-trigger-java script-voor beeld

In het volgende voor beeld worden webhooks voor binnenkomende Outlook-berichten verwerkt. Als u een webhook-trigger wilt gebruiken, [maakt u een abonnement](#webhook-output---example)en kunt u [het abonnement vernieuwen](#webhook-subscription-refresh) om te voor komen dat het wordt verloopt.

De *function.jsin* het bestand definieert een webhook-trigger:

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

De Java script-code reageert op binnenkomende e-mail berichten en registreert de hoofd tekst van degenen die zijn verzonden door de ontvanger en die Azure Functions bevat in het onderwerp:

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

### <a name="webhook-trigger---attributes"></a>Webhook-trigger-kenmerken

Gebruik in [C# class libraries](functions-dotnet-class-library.md)het kenmerk [GraphWebhookTrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookTriggerAttribute.cs) .

### <a name="webhook-trigger---configuration"></a>Webhook-trigger-configuratie

De volgende tabel bevat uitleg over de binding configuratie-eigenschappen die u hebt ingesteld in de *function.jsvoor* het bestand en het `GraphWebhookTrigger` kenmerk.

|function.jsbij eigenschap | Kenmerk eigenschap |Beschrijving|
|---------|---------|----------------------|
|**name**| N.v.t. |Vereist: de naam van de variabele die wordt gebruikt in de functie code voor het e-mail bericht. Zie [een Outlook-bericht uitvoer binding van code gebruiken](#outlook-output-code).|
|**type**| N.v.t. |Vereist: moet worden ingesteld op `graphWebhook` .|
|**direction**| N.v.t. |Vereist: moet worden ingesteld op `trigger` .|
|**Resource**|**Resource**|Vereist: de grafiek resource waarvoor deze functie moet reageren op webhooks. Dit kan een van de volgende waarden zijn:<ul><li><code>#Microsoft.Graph.Message</code> -wijzigingen aangebracht in Outlook-berichten.</li><li><code>#Microsoft.Graph.DriveItem</code> -Er zijn wijzigingen aangebracht in de OneDrive-hoofd items.</li><li><code>#Microsoft.Graph.Contact</code> -wijzigingen aangebracht in persoonlijke contact personen in Outlook.</li><li><code>#Microsoft.Graph.Event</code> -wijzigingen aangebracht in Outlook agenda-items.</li></ul>|

> [!Note]
> Een functie-app kan slechts één functie hebben die is geregistreerd voor een bepaalde `resourceType` waarde.

### <a name="webhook-trigger---usage"></a>Webhook-trigger-gebruik

Met de binding worden de volgende typen in .NET-functies weer gegeven:
- Microsoft Graph SDK-typen die relevant zijn voor het bron type, zoals `Microsoft.Graph.Message` of `Microsoft.Graph.DriveItem` .
- Aangepaste object typen (met behulp van Structured model binding)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Invoer van webhook

Met de Microsoft Graph webhook-invoer binding kunt u de lijst met abonnementen ophalen die worden beheerd door deze functie-app. De binding leest van functie-app-opslag, zodat er geen andere abonnementen worden weer gegeven die zijn gemaakt van buiten de app.

Deze sectie bevat de volgende subsecties:

* [Voorbeeld](#webhook-input---example)
* [Kenmerken](#webhook-input---attributes)
* [Configuratie](#webhook-input---configuration)
* [Gebruik](#webhook-input---usage)

### <a name="webhook-input---example"></a>Invoer van webhook-voor beeld

Zie het taalspecifieke voor beeld:

* [C#-script (.csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Voor beeld van webhook-invoer-C#-script

In het volgende voor beeld worden alle abonnementen opgehaald voor de aanroepende gebruiker en worden deze verwijderd.

De *function.jsin* het bestand definieert een http-trigger met een invoer binding van een abonnement en een uitvoer binding van een abonnement die gebruikmaakt van de actie verwijderen:

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

Met de C#-script code worden de abonnementen opgehaald en worden deze verwijderd:

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

#### <a name="webhook-input---javascript-example"></a>Voor beeld van webhook-invoer-java script

In het volgende voor beeld worden alle abonnementen opgehaald voor de aanroepende gebruiker en worden deze verwijderd.

De *function.jsin* het bestand definieert een http-trigger met een invoer binding van een abonnement en een uitvoer binding van een abonnement die gebruikmaakt van de actie verwijderen:

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

De Java script-code haalt de abonnementen op en verwijdert deze:

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

### <a name="webhook-input---attributes"></a>Invoer kenmerken van webhook

Gebruik in [C# class libraries](functions-dotnet-class-library.md)het kenmerk [GraphWebhookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs) .

### <a name="webhook-input---configuration"></a>Invoer van webhook-configuratie

De volgende tabel bevat uitleg over de binding configuratie-eigenschappen die u hebt ingesteld in de *function.jsvoor* het bestand en het `GraphWebhookSubscription` kenmerk.

|function.jsbij eigenschap | Kenmerk eigenschap |Beschrijving|
|---------|---------|----------------------|
|**name**| N.v.t. |Vereist: de naam van de variabele die wordt gebruikt in de functie code voor het e-mail bericht. Zie [een Outlook-bericht uitvoer binding van code gebruiken](#outlook-output-code).|
|**type**| N.v.t. |Vereist: moet worden ingesteld op `graphWebhookSubscription` .|
|**direction**| N.v.t. |Vereist: moet worden ingesteld op `in` .|
|**Filterwebonderdelen**|**Filter**| Als `userFromRequest` deze is ingesteld op, wordt met de binding alleen abonnementen opgehaald die eigendom zijn van de aanroepende gebruiker (alleen geldig met [http-trigger]).| 

### <a name="webhook-input---usage"></a>Invoer van webhook-gebruik

Met de binding worden de volgende typen in .NET-functies weer gegeven:
- teken reeks []
- Aangepaste object type matrices
- Newtonsoft.Jsop. LINQ. JObject []
- Micro soft. Graph. abonnement []





## <a name="webhook-output"></a>Webhook-uitvoer

Met de webhook-abonnements uitvoer binding kunt u webhook-abonnementen maken, verwijderen en vernieuwen in de Microsoft Graph.

Deze sectie bevat de volgende subsecties:

* [Voorbeeld](#webhook-output---example)
* [Kenmerken](#webhook-output---attributes)
* [Configuratie](#webhook-output---configuration)
* [Gebruik](#webhook-output---usage)

### <a name="webhook-output---example"></a>Webhook-uitvoer-voor beeld

Zie het taalspecifieke voor beeld:

* [C#-script (.csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Voor beeld van webhook-uitvoer-C#-script

In het volgende voor beeld wordt een abonnement gemaakt. U kunt [het abonnement vernieuwen](#webhook-subscription-refresh) om te voor komen dat het wordt verloopt.

De *function.jsin* het bestand definieert een http-trigger met een abonnements-uitvoer binding met behulp van de actie maken:

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

De C#-script code registreert een webhook die deze functie-app op de hoogte stelt wanneer de aanroepende gebruiker een Outlook-bericht ontvangt:

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

#### <a name="webhook-output---javascript-example"></a>Uitvoer van webhook-java script-voor beeld

In het volgende voor beeld wordt een abonnement gemaakt. U kunt [het abonnement vernieuwen](#webhook-subscription-refresh) om te voor komen dat het wordt verloopt.

De *function.jsin* het bestand definieert een http-trigger met een abonnements-uitvoer binding met behulp van de actie maken:

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

De Java script-code registreert een webhook die deze functie-app op de hoogte stelt wanneer de aanroepende gebruiker een Outlook-bericht ontvangt:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Uitvoer kenmerken van de webhook

Gebruik in [C# class libraries](functions-dotnet-class-library.md)het kenmerk [GraphWebhookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs) .

### <a name="webhook-output---configuration"></a>Uitvoer van webhook-configuratie

De volgende tabel bevat uitleg over de binding configuratie-eigenschappen die u hebt ingesteld in de *function.jsvoor* het bestand en het `GraphWebhookSubscription` kenmerk.

|function.jsbij eigenschap | Kenmerk eigenschap |Beschrijving|
|---------|---------|----------------------|
|**name**| N.v.t. |Vereist: de naam van de variabele die wordt gebruikt in de functie code voor het e-mail bericht. Zie [een Outlook-bericht uitvoer binding van code gebruiken](#outlook-output-code).|
|**type**| N.v.t. |Vereist: moet worden ingesteld op `graphWebhookSubscription` .|
|**direction**| N.v.t. |Vereist: moet worden ingesteld op `out` .|
|**identity**|**Identiteit**|Vereist: de identiteit die wordt gebruikt om de actie uit te voeren. Dit kan een van de volgende waarden zijn:<ul><li><code>userFromRequest</code> -Alleen geldig met [http-trigger]. Maakt gebruik van de identiteit van de aanroepende gebruiker.</li><li><code>userFromId</code> -Gebruikt de identiteit van een eerder aangemelde gebruiker met de opgegeven ID. Zie de <code>userId</code> eigenschap.</li><li><code>userFromToken</code> -Gebruikt de identiteit die wordt vertegenwoordigd door het opgegeven token. Zie de <code>userToken</code> eigenschap.</li><li><code>clientCredentials</code> -Maakt gebruik van de identiteit van de functie-app.</li></ul>|
|**userId**|**Naam**  |Alleen vereist als de _identiteit_ is ingesteld op `userFromId` . Een User Principal-ID die is gekoppeld aan een eerder aangemelde gebruiker.|
|**userToken**|**UserToken**|Alleen vereist als de _identiteit_ is ingesteld op `userFromToken` . Een token dat geldig is voor de functie-app. |
|**action**|**Actie**|Vereist: Hiermee geeft u de actie op die de binding moet uitvoeren. Dit kan een van de volgende waarden zijn:<ul><li><code>create</code> -Registreert een nieuw abonnement.</li><li><code>delete</code> -Hiermee verwijdert u een opgegeven abonnement.</li><li><code>refresh</code> -Hiermee vernieuwt u een opgegeven abonnement om te voor komen dat het verloopt.</li></ul>|
|**subscriptionResource**|**SubscriptionResource**|Alleen vereist als de _actie_ is ingesteld op `create` . Hiermee geeft u de Microsoft Graph resource op die wordt gecontroleerd op wijzigingen. Zie [werken met webhooks in Microsoft Graph]. |
|**Change type**|**Change type**|Alleen vereist als de _actie_ is ingesteld op `create` . Hiermee wordt het type wijziging in de geplaatste resource aangegeven waarmee een melding wordt gegenereerd. De ondersteunde waarden zijn: `created` , `updated` , `deleted` . Meerdere waarden kunnen worden gecombineerd met behulp van een door komma's gescheiden lijst.|

### <a name="webhook-output---usage"></a>Gebruik van webhook-uitvoer

Met de binding worden de volgende typen in .NET-functies weer gegeven:
- tekenreeks
- Micro soft. Graph. abonnement




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Webhook-abonnement vernieuwen

Er zijn twee manieren om abonnementen te vernieuwen:

- Gebruik de toepassings-id om alle abonnementen te verwerken. Hiervoor is toestemming van een Azure Active Directory beheerder vereist. Dit kan worden gebruikt door alle talen die door Azure Functions worden ondersteund.
- Gebruik de identiteit die is gekoppeld aan elk abonnement door elke gebruikers-ID hand matig te binden. Hiervoor is een aangepaste code vereist om de binding uit te voeren. Dit kan alleen worden gebruikt door .NET-functies.

Deze sectie bevat een voor beeld voor elk van de volgende benaderingen:

* [Voor beeld van app-identiteit](#webhook-subscription-refresh---app-identity-example)
* [Voor beeld van gebruikers identiteit](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Webhook-abonnement vernieuwen-voor beeld van app-identiteit

Zie het taalspecifieke voor beeld:

* [C#-script (.csx)](#app-identity-refresh---c-script-example)
* Javascript

### <a name="app-identity-refresh---c-script-example"></a>App-identiteit vernieuwen-voor beeld van C#-script

In het volgende voor beeld wordt de toepassings-id gebruikt om een abonnement te vernieuwen.

De *function.jsop* definieert een timer trigger met een invoer binding voor het abonnement en een binding voor een abonnements uitvoer:

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

Met de C#-script code worden de abonnementen vernieuwd:

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

### <a name="app-identity-refresh---c-script-example"></a>App-identiteit vernieuwen-voor beeld van C#-script

In het volgende voor beeld wordt de toepassings-id gebruikt om een abonnement te vernieuwen.

De *function.jsop* definieert een timer trigger met een invoer binding voor het abonnement en een binding voor een abonnements uitvoer:

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

Met de Java script-code worden de abonnementen vernieuwd:

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

### <a name="webhook-subscription-refresh---user-identity-example"></a>Webhook-abonnement vernieuwen-voor beeld van gebruikers-id

In het volgende voor beeld wordt de gebruikers-id gebruikt om een abonnement te vernieuwen.

De *function.jsin* het bestand definieert een timer trigger en stelt de invoer van het abonnement uit aan de functie code:

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

Met de C#-script code worden de abonnementen vernieuwd en wordt de uitvoer binding in code gemaakt met behulp van de identiteit van elke gebruiker:

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
> [Meer informatie over Azure functions-triggers en-bindingen](functions-triggers-bindings.md)

[HTTP-trigger]: functions-bindings-http-webhook.md
[Werken met webhooks in Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
