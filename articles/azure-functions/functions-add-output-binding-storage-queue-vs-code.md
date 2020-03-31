---
title: Azure-functies verbinden met Azure Storage met Behulp van Visual Studio-code
description: Meer informatie over het verbinden van Azure-functies met een Azure Storage-wachtrij door een uitvoerbinding toe te voegen aan uw Visual Studio Code-project.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 22f7df52e90a35a3ed9a26a7672f8354efc173e3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241330"
---
# <a name="connect-azure-functions-to-azure-storage-using-visual-studio-code"></a>Azure-functies verbinden met Azure Storage met Behulp van Visual Studio-code

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

In dit artikel ziet u hoe u Visual Studio Code gebruiken om de functie die u in het [vorige quickstart-artikel](functions-create-first-function-vs-code.md) hebt gemaakt, te verbinden met Azure Storage. De uitvoerbinding die u aan deze functie toevoegt, schrijft gegevens van het HTTP-verzoek naar een bericht in een Azure Queue-opslagwachtrij. 

Voor de meeste bindingen is een opgeslagen verbindingstekenreeks vereist die functies gebruiken om toegang te krijgen tot de gebonden service. Om het u gemakkelijker te maken, gebruikt u het opslagaccount dat u met uw functie-app hebt gemaakt. De verbinding met dit account is al `AzureWebJobsStorage`opgeslagen in een app-instelling met de naam .  

## <a name="configure-your-local-environment"></a>Uw lokale omgeving configureren

Voordat u aan dit artikel begint, moet u aan de volgende vereisten voldoen:

* Installeer de [Azure Storage-extensie voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).

* Installeer [Azure Storage Explorer](https://storageexplorer.com/). Storage Explorer is een hulpmiddel dat u gebruikt om wachtrijberichten te onderzoeken die zijn gegenereerd door uw uitvoerbinding. Storage Explorer wordt ondersteund op besturingssystemen op basis van macOS, Windows en Linux.

::: zone pivot="programming-language-csharp"
* Installeer [.NET Core CLI-gereedschappen](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x).
::: zone-end

* Voer de stappen in [deel 1 van de Visual Studio Code snel aan.](functions-create-first-function-vs-code.md) 

In dit artikel wordt ervan uitgegaan dat u al bent aangemeld bij uw Azure-abonnement vanuit Visual Studio Code. U zich `Azure: Sign In` aanmelden door vanuit het opdrachtpalet te rennen. 

## <a name="download-the-function-app-settings"></a>De instellingen van de functie-app downloaden

In het [vorige quickstart-artikel](functions-create-first-function-vs-code.md)hebt u een functie-app in Azure gemaakt, samen met het vereiste opslagaccount. De verbindingstekenreeks voor dit account wordt veilig opgeslagen in app-instellingen in Azure. In dit artikel schrijft u berichten naar een opslagwachtrij in hetzelfde account. Als u verbinding wilt maken met uw opslagaccount wanneer u de functie lokaal uitvoert, moet u app-instellingen downloaden naar het bestand local.settings.json. 

1. Druk op de F1-toets om het opdrachtpalet `Azure Functions: Download Remote Settings....`te openen en zoek en voer de opdracht uit. 

1. Kies de functie-app die u in het vorige artikel hebt gemaakt. Selecteer **Ja voor iedereen** om de bestaande lokale instellingen te overschrijven. 

    > [!IMPORTANT]  
    > Omdat het geheimen bevat, wordt het bestand local.settings.json nooit gepubliceerd en wordt het uitgesloten van bronbeheer.

1. Kopieer de `AzureWebJobsStorage`waarde , de sleutel voor de waarde van de tekenreeks opslagaccountverbinding. U gebruikt deze verbinding om te controleren of de uitvoerbinding werkt zoals verwacht.

## <a name="register-binding-extensions"></a>Binding-extensies registreren

Omdat u een binding voor de uitvoer van wachtrijopslag gebruikt, moet u de extensie Opslagbindingen hebben geïnstalleerd voordat u het project uitvoert. 

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

Met uitzondering van HTTP- en timertriggers worden bindingen geïmplementeerd als uitbreidingspakketten. Voer de volgende [opdracht voor dotnetadd-pakketten uit](/dotnet/core/tools/dotnet-add-package) in het terminalvenster om het uitbreidingspakket Opslag aan uw project toe te voegen.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

::: zone-end

U nu de binding van de opslaguitvoer toevoegen aan uw project.

## <a name="add-an-output-binding"></a>Een uitvoerbinding toevoegen

In Functies vereist elk type `direction` `type`binding een `name` , , en een unieke om te worden gedefinieerd in het function.json-bestand. De manier waarop u deze kenmerken definieert, is afhankelijk van de taal van uw functie-app.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Code toevoegen die gebruikmaakt van de uitvoerbinding

Nadat de binding is gedefinieerd, `name` kunt u de binding gebruiken om toegang te krijgen tot de binding als kenmerk in de functiehandtekening. Door een uitvoerbinding te gebruiken, hoeft u de Azure Storage SDK-code niet te gebruiken voor verificatie, het verkrijgen van een wachtrijverwijzing of het schrijven van gegevens. De runtime en queue output binding van functies doen deze taken voor u.

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end  

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

In uw opslagaccount wordt een nieuwe wachtrij met de naam **outqueue** gemaakt door de runtime Van Functies wanneer de uitvoerbinding voor het eerst wordt gebruikt. U gebruikt Storage Explorer om te controleren of de wachtrij samen met het nieuwe bericht is gemaakt.

### <a name="connect-storage-explorer-to-your-account"></a>Storage Explorer verbinden met uw account

Sla deze sectie over als u Azure Storage Explorer al hebt geïnstalleerd en deze hebt verbonden met uw Azure-account.

1. Voer het gereedschap [Azure Storage Explorer] uit, selecteer het pictogram Verbinding maken aan de linkerkant en selecteer **Een account toevoegen**.

    ![Een Azure-account toevoegen aan Microsoft Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. Kies in het dialoogvenster **Verbinding** de optie **Een Azure-account toevoegen,** kies uw **Azure-omgeving**en selecteer **Aanmelden...**. 

    ![Aanmelden bij uw Azure-account](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Nadat u zich met succes hebt aangemeld bij uw account, ziet u alle Azure-abonnementen die aan uw account zijn gekoppeld.

### <a name="examine-the-output-queue"></a>De uitvoerwachtrij controleren

1. Druk in Visual Studio Code op de F1-toets om het `Azure Storage: Open in Storage Explorer` opdrachtpalet te openen, zoek en voer de opdracht uit en kies de naam van uw opslagaccount. Uw opslagaccount wordt geopend in Azure Storage Explorer.  

1. Vouw het knooppunt **Wachtrijen** uit en selecteer vervolgens de wachtrij met de naam **outqueue**. 

   De wachtrij bevat het bericht dat met de Queue Storage-uitvoerbinding is gemaakt toen u de met HTTP geactiveerde functie hebt uitgevoerd. Als u de functie hebt aangeroepen met de standaardwaarde `name` van *Azure*, is het wachtrijbericht *Naam is doorgegeven aan de functie: Azure*.

    ![Wachtrijbericht weergegeven in Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Voer de functie opnieuw uit, stuur een ander verzoek en u ziet een nieuw bericht in de wachtrij verschijnen.  

Nu is het tijd om de bijgewerkte functie-app opnieuw te publiceren naar Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>De bijgewerkte app opnieuw implementeren en verifiëren

1. Druk in Visual Studio Code op F1 om het opdrachtpalet te openen. Zoek en selecteer `Azure Functions: Deploy to function app...`in het opdrachtpalet .

1. Kies de functie-app die u in het eerste artikel hebt gemaakt. Omdat u uw project opnieuw implementeert naar dezelfde app, selecteert u **Implementeren** om de waarschuwing voor het overschrijven van bestanden te verwijderen.

1. Nadat de implementatie is voltooid, u opnieuw cURL of een browser gebruiken om de opnieuw geïmplementeerde functie te testen. Net als voorheen wordt `&name=<yourname>` de querytekenreeks toegevoegd aan de URL, zoals in het volgende voorbeeld:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. Bekijk opnieuw [het bericht in de opslagwachtrij](#examine-the-output-queue) om te controleren of de uitvoerbinding opnieuw een nieuw bericht in de wachtrij genereert.

## <a name="clean-up-resources"></a>Resources opschonen

*Resources* verwijst in Azure naar functie-apps, functies, opslagaccounts enzovoort. Deze zijn gegroepeerd in *resourcegroepen*. U kunt alle resources in een groep verwijderen door de groep zelf te verwijderen.

U hebt resources gemaakt om deze snelstartgidsen te voltooien. Deze resources kunnen bij u in rekening worden gebracht, afhankelijk van de [accountstatus](https://azure.microsoft.com/account/) en [serviceprijzen](https://azure.microsoft.com/pricing/). Als u de resources niet meer nodig hebt, kunt u ze als volgt verwijderen:

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt de functie HTTP-geactiveerd bijgewerkt om gegevens naar een opslagwachtrij te schrijven. Nu u meer te weten komen over het ontwikkelen van functies met behulp van Visual Studio Code:

+ [Azure-functies ontwikkelen met Behulp van Visual Studio-code](functions-develop-vs-code.md)
::: zone pivot="programming-language-csharp"  
+ [Voorbeelden van complete functieprojecten in C#](/samples/browse/?products=azure-functions&languages=csharp).
+ [Naslaginformatie over Azure Functions C#-ontwikkelaars](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Voorbeelden van volledige functieprojecten in JavaScript](/samples/browse/?products=azure-functions&languages=javascript).
+ [JavaScript-ontwikkelaarshandleiding voor Azure Functions](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Voorbeelden van volledige functieprojecten in TypeScript](/samples/browse/?products=azure-functions&languages=typescript).
+ [TypeScript-ontwikkelaarshandleiding voor Azure Functions](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Voorbeelden van volledige functieprojecten in Python.](/samples/browse/?products=azure-functions&languages=python)
+ [Handleiding voor Azure Functions Python-ontwikkelaars](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Voorbeelden van complete functieprojecten in PowerShell](/samples/browse/?products=azure-functions&languages=azurepowershell).
+ [PowerShell-ontwikkelaarshandleiding voor Azure Functions](functions-reference-powershell.md) 
::: zone-end
+ [Azure Functions triggers en bindingen](functions-triggers-bindings.md).
+ [Pagina Functiesprijzen](https://azure.microsoft.com/pricing/details/functions/)
+ [Schatting Van het verbruik plan kosten](functions-consumption-costs.md) artikel.
