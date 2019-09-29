---
title: Functies verbinden met Azure Storage met Visual Studio code
description: Meer informatie over het toevoegen van een uitvoer binding om uw functies te koppelen aan een Azure Storage wachtrij met Visual Studio code.
author: ggailey777
ms.author: glenga
ms.date: 06/25/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
manager: jeconnoc
ms.openlocfilehash: 63065c918a6f78510b4908c5e2ae80df67665b40
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672599"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio-code"></a>Functies verbinden met Azure Storage met Visual Studio code

Met Azure Functions kunt u functies verbinden met Azure-Services en andere resources zonder uw eigen integratie code te hoeven schrijven. Deze *bindingen*, die zowel invoer als uitvoer vertegenwoordigen, worden gedeclareerd in de functie definitie. Gegevens van bindingen worden aan de functie door gegeven als para meters. Een trigger is een speciaal type invoer binding. Hoewel een functie slechts één trigger heeft, kan deze meerdere invoer-en uitvoer bindingen hebben. Zie [Azure functions triggers en bindingen](functions-triggers-bindings.md)voor meer informatie.

In dit artikel wordt beschreven hoe u Visual Studio code gebruikt om de functie die u hebt gemaakt in het [vorige Quick](functions-create-first-function-vs-code.md) start-artikel te koppelen aan Azure Storage. De uitvoer binding die u aan deze functie toevoegt, schrijft gegevens van de HTTP-aanvraag naar een bericht in een Azure Queue-opslag wachtrij. 

Voor de meeste bindingen is een opgeslagen connection string vereist die functies gebruiken om toegang te krijgen tot de gebonden service. Om het eenvoudiger te maken, gebruikt u het opslag account dat u hebt gemaakt met uw functie-app. De verbinding met dit account is al opgeslagen in een app-instelling met de naam `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Vereisten

Voordat u aan dit artikel begint, moet aan de volgende vereisten worden voldaan:

* Installeer de [Azure Storage-extensie voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).
* Installeer [Azure Storage Explorer](https://storageexplorer.com/). Storage Explorer is een hulp programma dat u gebruikt om wachtrij berichten te onderzoeken die zijn gegenereerd door de uitvoer binding. Storage Explorer wordt ondersteund op macOS-, Windows-en Linux-besturings systemen.
* Installeer [.net core SLI-hulpprogram ma's](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x) (C# alleen projecten).
* Voltooi de stappen in [deel 1 van de Snelstartgids voor Visual Studio code](functions-create-first-function-vs-code.md). 

In dit artikel wordt ervan uitgegaan dat u al bent aangemeld bij uw Azure-abonnement vanuit Visual Studio code. U kunt zich aanmelden door `Azure: Sign In` vanuit het opdracht palet uit te voeren. 

## <a name="download-the-function-app-settings"></a>De instellingen van de functie-app downloaden

In het [vorige Quick](functions-create-first-function-vs-code.md)start-artikel hebt u een functie-app gemaakt in azure, samen met het vereiste opslag account. De connection string voor dit account wordt veilig opgeslagen in de app-instellingen in Azure. In dit artikel schrijft u berichten naar een opslag wachtrij in hetzelfde account. Als u verbinding wilt maken met uw opslag account wanneer u de functie lokaal uitvoert, moet u de app-instellingen downloaden naar het bestand local. settings. json. 

1. Druk op de F1-toets om het opdracht palet te openen. Zoek vervolgens de opdracht `Azure Functions: Download Remote Settings....` en voer deze uit. 

1. Kies de functie-app die u in het vorige artikel hebt gemaakt. Selecteer **Ja op alles** om de bestaande lokale instellingen te overschrijven. 

    > [!IMPORTANT]  
    > Omdat deze geheimen bevat, wordt het bestand local. settings. json nooit gepubliceerd en wordt het bron beheer uitgesloten.

1. Kopieer de waarde `AzureWebJobsStorage`. Dit is de sleutel voor het opslag account connection string waarde. U gebruikt deze verbinding om te controleren of de uitvoer binding werkt zoals verwacht.

## <a name="register-binding-extensions"></a>Binding-extensies registreren

Omdat u een uitvoer binding voor de wachtrij opslag gebruikt, moet u de extensie opslag bindingen hebben geïnstalleerd voordat u het project uitvoert. 

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

Met uitzonde ring van HTTP-en timer triggers worden bindingen geïmplementeerd als uitbreidings pakketten. Voer de volgende [DotNet-opdracht add package](/dotnet/core/tools/dotnet-add-package) uit in het Terminal venster om het opslag extensie pakket toe te voegen aan uw project.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```
---
Nu kunt u de opslag-uitvoer binding toevoegen aan uw project.

## <a name="add-an-output-binding"></a>Een uitvoerbinding toevoegen

In functies moet voor elk type binding een `direction`, `type` en een unieke `name` worden gedefinieerd in het bestand function. json. De manier waarop u deze kenmerken definieert, is afhankelijk van de taal van uw functie-app.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

Bindings kenmerken worden rechtstreeks in het bestand function. json gedefinieerd. Afhankelijk van het bindings type zijn er mogelijk aanvullende eigenschappen vereist. De [uitvoer configuratie](functions-bindings-storage-queue.md#output---configuration) van de wachtrij beschrijft de velden die vereist zijn voor een Azure Storage binding van de wachtrij. Met deze extensie kunt u eenvoudig bindingen toevoegen aan het bestand function. json. 

Als u een binding wilt maken, klikt u met de rechter muisknop (CTRL + klik op macOS) het `function.json`-bestand in de map http trigger en kiest u **binding toevoegen...** . Volg de aanwijzingen om de volgende bindings eigenschappen te definiëren voor de nieuwe binding:

| Vraag | Value | Description |
| -------- | ----- | ----------- |
| **Bindings richting selecteren** | `out` | De binding is een uitvoer binding. |
| **Binding met richting selecteren...** | `Azure Queue Storage` | De binding is een Azure Storage wachtrij binding. |
| **De naam die wordt gebruikt om deze binding in uw code aan te duiden** | `msg` | Naam die de bindings parameter identificeert waarnaar in uw code wordt verwezen. |
| **De wachtrij waarnaar het bericht wordt verzonden** | `outqueue` | De naam van de wachtrij waarnaar de binding wordt geschreven. Wanneer de *wachtrij* naam niet bestaat, wordt deze door de binding gemaakt bij het eerste gebruik. |
| **Selecteer de instelling in ' lokaal. instelling. json '** | `AzureWebJobsStorage` | De naam van een toepassings instelling die de connection string voor het opslag account bevat. De instelling @no__t 0 bevat de connection string voor het opslag account dat u hebt gemaakt met de functie-app. |

Een binding wordt toegevoegd aan de matrix @no__t 0 in het bestand function. json, dat er nu uitziet als in het volgende voor beeld:

```json
{
   ...

  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

---

## <a name="add-code-that-uses-the-output-binding"></a>Code toevoegen die gebruikmaakt van de uitvoerbinding

Nadat de binding is gedefinieerd, kunt u de `name` van de binding gebruiken om deze te openen als een kenmerk in de functie handtekening. Als u een uitvoer binding gebruikt, hoeft u niet de Azure Storage SDK-code te gebruiken voor authenticatie, het ophalen van een wachtrij verwijzing of het schrijven van gegevens. De functies runtime en wachtrij-uitvoer binding voeren deze taken voor u uit.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

Voeg code toe die gebruikmaakt van het `msg`-object voor uitvoer bindingen op `context.bindings` om een wachtrij bericht te maken. Voeg deze code toe vóór de instructie `context.res`.

```javascript
// Add a message to the Storage queue.
context.bindings.msg = "Name passed to the function: " + 
(req.query.name || req.body.name);
```

De functie moet op dit moment er als volgt uitzien:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    if (req.query.name || (req.body && req.body.name)) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " + 
        (req.query.name || req.body.name);
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};
```

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

---

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Er wordt een nieuwe wachtrij met de naam **outqueue** in uw opslag account gemaakt door de functions-runtime wanneer de uitvoer binding voor het eerst wordt gebruikt. U gebruikt Storage Explorer om te controleren of de wachtrij samen met het nieuwe bericht is gemaakt.

### <a name="connect-storage-explorer-to-your-account"></a>Storage Explorer verbinden met uw account

Sla deze sectie over als u Azure Storage Explorer al hebt geïnstalleerd en deze hebt verbonden met uw Azure-account.

1. Voer het [Azure-opslagverkenner] -hulp programma uit, selecteer aan de linkerkant het pictogram verbinding maken en selecteer **een account toevoegen**.

    ![Een Azure-account toevoegen aan Microsoft Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. Kies in het dialoog venster verbinden **een Azure-account toevoegen**, kies uw **Azure-omgeving**en selecteer **aanmelden...** . 

    ![Aanmelden bij uw Azure-account](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Nadat u zich hebt aangemeld bij uw account, ziet u alle Azure-abonnementen die zijn gekoppeld aan uw account.

### <a name="examine-the-output-queue"></a>De uitvoerwachtrij controleren

1. Druk in Visual Studio code op de F1-toets om het opdracht palet te openen, zoek de opdracht en voer deze uit `Azure Storage: Open in Storage Explorer` en kies de naam van het opslag account. Uw opslag account wordt geopend in Azure Storage Explorer.  

1. Vouw het knooppunt **Wachtrijen** uit en selecteer vervolgens de wachtrij met de naam **outqueue**. 

   De wachtrij bevat het bericht dat met de Queue Storage-uitvoerbinding is gemaakt toen u de met HTTP geactiveerde functie hebt uitgevoerd. Als u de functie hebt aangeroepen met de standaardwaarde voor `name`, namelijk *Azure*, is het wachtrijbericht *Naam is doorgegeven aan de functie: Azure*.

    ![Wachtrij bericht weer gegeven in Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Voer de functie opnieuw uit, verzend een andere aanvraag en er wordt een nieuw bericht weer gegeven in de wachtrij.  

Nu is het tijd om de bijgewerkte functie-app opnieuw te publiceren naar Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>De bijgewerkte app opnieuw implementeren en verifiëren

1. Druk in Visual Studio code op F1 om het opdracht palet te openen. In het opdracht palet zoekt en selecteert u `Azure Functions: Deploy to function app...`.

1. Kies de functie-app die u in het eerste artikel hebt gemaakt. Omdat u uw project opnieuw implementeert naar dezelfde app, selecteert u **implementeren** om de waarschuwing over het overschrijven van bestanden te negeren.

1. Nadat de implementatie is voltooid, kunt u de opnieuw geïmplementeerde functie weer testen door krul of een browser te gebruiken. Voeg de query reeks `&name=<yourname>` toe aan de URL, zoals in het volgende voor beeld:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. [Bekijk nogmaals het bericht in de opslag wachtrij](#examine-the-output-queue) om te controleren of de uitvoer binding opnieuw een nieuw bericht in de wachtrij genereert.

## <a name="clean-up-resources"></a>Resources opschonen

*Resources* verwijst in Azure naar functie-apps, functies, opslagaccounts enzovoort. Deze zijn gegroepeerd in *resourcegroepen*. U kunt alle resources in een groep verwijderen door de groep zelf te verwijderen.

U hebt resources gemaakt om deze snelstartgidsen te voltooien. Deze resources kunnen bij u in rekening worden gebracht, afhankelijk van de [accountstatus](https://azure.microsoft.com/account/) en [serviceprijzen](https://azure.microsoft.com/pricing/). Als u de resources niet meer nodig hebt, kunt u ze als volgt verwijderen:

1. Druk in Visual Studio code op F1 om het opdracht palet te openen. In het opdracht palet zoekt en selecteert u `Azure Functions: Open in portal`.

1. Kies uw functie-app en druk op ENTER. De pagina functie-app wordt geopend in de [Azure Portal](https://portal.azure.com).

1. Selecteer op het tabblad **overzicht** de benoemde koppeling onder **resource groep**.

    ![Selecteer op de pagina Functie-app de resourcegroep die u wilt verwijderen.](./media/functions-add-output-binding-storage-queue-vs-code/functions-app-delete-resource-group.png)

1. Bekijk op de pagina **Resourcegroep** de lijst met opgenomen resources en controleer of dit de resources zijn die u wilt verwijderen.
 
1. Selecteer **Resourcegroep verwijderen** en volg de instructies.

   Verwijderen kan enkele minuten duren. Wanneer dit is voltooid, verschijnt een aantal seconden een melding in beeld. U kunt ook het belpictogram boven aan de pagina selecteren om de melding te bekijken.

## <a name="next-steps"></a>Volgende stappen

U hebt uw HTTP-geactiveerde functie bijgewerkt om gegevens naar een opslag wachtrij te schrijven. Zie voor meer informatie over het ontwikkelen van functies [Azure functions ontwikkelen met Visual Studio code](functions-develop-vs-code.md).

Schakel vervolgens Application Insights bewaking in voor uw functie-app:

> [!div class="nextstepaction"]
> [Application Insights-integratie inschakelen](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure-opslagverkenner]: https://storageexplorer.com/
