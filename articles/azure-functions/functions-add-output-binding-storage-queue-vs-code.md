---
title: Azure Functions verbinding maken met Azure Storage met behulp van Visual Studio code
description: Informatie over het verbinden van Azure Functions met een Azure Storage wachtrij door een uitvoer binding toe te voegen aan uw Visual Studio-code project.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 22f7df52e90a35a3ed9a26a7672f8354efc173e3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79241330"
---
# <a name="connect-azure-functions-to-azure-storage-using-visual-studio-code"></a>Azure Functions verbinding maken met Azure Storage met behulp van Visual Studio code

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

In dit artikel wordt beschreven hoe u Visual Studio code gebruikt om de functie die u hebt gemaakt in het [vorige Quick](functions-create-first-function-vs-code.md) start-artikel te koppelen aan Azure Storage. De uitvoer binding die u aan deze functie toevoegt, schrijft gegevens van de HTTP-aanvraag naar een bericht in een Azure Queue-opslag wachtrij. 

Voor de meeste bindingen is een opgeslagen connection string vereist die functies gebruiken om toegang te krijgen tot de gebonden service. Om het eenvoudiger te maken, gebruikt u het opslag account dat u hebt gemaakt met uw functie-app. De verbinding met dit account is al opgeslagen in een app-instelling met de naam `AzureWebJobsStorage`.  

## <a name="configure-your-local-environment"></a>Uw lokale omgeving configureren

Voordat u aan dit artikel begint, moet aan de volgende vereisten worden voldaan:

* Installeer de [Azure Storage-extensie voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).

* Installeer [Azure Storage Explorer](https://storageexplorer.com/). Storage Explorer is een hulp programma dat u gebruikt om wachtrij berichten te onderzoeken die zijn gegenereerd door de uitvoer binding. Storage Explorer wordt ondersteund op macOS-, Windows-en Linux-besturings systemen.

::: zone pivot="programming-language-csharp"
* Installeer [.net core SLI-hulpprogram ma's](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x).
::: zone-end

* Voltooi de stappen in [deel 1 van de Snelstartgids voor Visual Studio code](functions-create-first-function-vs-code.md). 

In dit artikel wordt ervan uitgegaan dat u al bent aangemeld bij uw Azure-abonnement vanuit Visual Studio code. U kunt zich aanmelden door `Azure: Sign In` vanuit het opdracht palet uit te voeren. 

## <a name="download-the-function-app-settings"></a>De instellingen van de functie-app downloaden

In het [vorige Quick](functions-create-first-function-vs-code.md)start-artikel hebt u een functie-app gemaakt in azure, samen met het vereiste opslag account. De connection string voor dit account wordt veilig opgeslagen in de app-instellingen in Azure. In dit artikel schrijft u berichten naar een opslag wachtrij in hetzelfde account. Als u verbinding wilt maken met uw opslag account wanneer u de functie lokaal uitvoert, moet u de app-instellingen downloaden naar het bestand local. settings. json. 

1. Druk op de F1-toets om het opdracht palet te openen, zoek de opdracht `Azure Functions: Download Remote Settings....`en voer deze uit. 

1. Kies de functie-app die u in het vorige artikel hebt gemaakt. Selecteer **Ja op alles** om de bestaande lokale instellingen te overschrijven. 

    > [!IMPORTANT]  
    > Omdat deze geheimen bevat, wordt het bestand local. settings. json nooit gepubliceerd en wordt het bron beheer uitgesloten.

1. Kopieer de waarde `AzureWebJobsStorage`. Dit is de sleutel voor het opslag account connection string waarde. U gebruikt deze verbinding om te controleren of de uitvoer binding werkt zoals verwacht.

## <a name="register-binding-extensions"></a>Binding-extensies registreren

Omdat u een uitvoer binding voor de wachtrij opslag gebruikt, moet u de extensie opslag bindingen hebben geïnstalleerd voordat u het project uitvoert. 

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

Met uitzonde ring van HTTP-en timer triggers worden bindingen geïmplementeerd als uitbreidings pakketten. Voer de volgende [DotNet-opdracht add package](/dotnet/core/tools/dotnet-add-package) uit in het Terminal venster om het opslag extensie pakket toe te voegen aan uw project.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

::: zone-end

Nu kunt u de opslag-uitvoer binding toevoegen aan uw project.

## <a name="add-an-output-binding"></a>Een uitvoerbinding toevoegen

In functies moet voor elk type binding een `direction`, `type`en een unieke `name` worden gedefinieerd in het bestand function. json. De manier waarop u deze kenmerken definieert, is afhankelijk van de taal van uw functie-app.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Code toevoegen die gebruikmaakt van de uitvoerbinding

Nadat de binding is gedefinieerd, kunt u de `name` van de binding gebruiken om deze te openen als een kenmerk in de functie handtekening. Als u een uitvoer binding gebruikt, hoeft u niet de Azure Storage SDK-code te gebruiken voor authenticatie, het ophalen van een wachtrij verwijzing of het schrijven van gegevens. De functies runtime en wachtrij-uitvoer binding voeren deze taken voor u uit.

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

Er wordt een nieuwe wachtrij met de naam **outqueue** in uw opslag account gemaakt door de functions-runtime wanneer de uitvoer binding voor het eerst wordt gebruikt. U gebruikt Storage Explorer om te controleren of de wachtrij samen met het nieuwe bericht is gemaakt.

### <a name="connect-storage-explorer-to-your-account"></a>Storage Explorer verbinden met uw account

Sla deze sectie over als u Azure Storage Explorer al hebt geïnstalleerd en deze hebt verbonden met uw Azure-account.

1. Voer het hulp programma [Azure Storage Explorer] uit, selecteer aan de linkerkant het pictogram verbinding maken en selecteer **een account toevoegen**.

    ![Een Azure-account toevoegen aan Microsoft Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. Kies in het dialoog venster verbinden **een Azure-account toevoegen**, kies uw **Azure-omgeving**en selecteer **aanmelden...** . 

    ![Aanmelden bij uw Azure-account](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Nadat u zich hebt aangemeld bij uw account, ziet u alle Azure-abonnementen die zijn gekoppeld aan uw account.

### <a name="examine-the-output-queue"></a>De uitvoerwachtrij controleren

1. Druk in Visual Studio code op de F1-toets om het opdracht palet te openen, zoek de opdracht en voer deze uit `Azure Storage: Open in Storage Explorer` en kies de naam van het opslag account. Uw opslag account wordt geopend in Azure Storage Explorer.  

1. Vouw het knooppunt **Wachtrijen** uit en selecteer vervolgens de wachtrij met de naam **outqueue**. 

   De wachtrij bevat het bericht dat met de Queue Storage-uitvoerbinding is gemaakt toen u de met HTTP geactiveerde functie hebt uitgevoerd. Als u de functie hebt aangeroepen met de standaardwaarde `name` van *Azure*, is het wachtrijbericht *Naam is doorgegeven aan de functie: Azure*.

    ![Wachtrij bericht weer gegeven in Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Voer de functie opnieuw uit, verzend een andere aanvraag en er wordt een nieuw bericht weer gegeven in de wachtrij.  

Nu is het tijd om de bijgewerkte functie-app opnieuw te publiceren naar Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>De bijgewerkte app opnieuw implementeren en verifiëren

1. Druk in Visual Studio code op F1 om het opdracht palet te openen. In het opdracht palet zoekt en selecteert u `Azure Functions: Deploy to function app...`.

1. Kies de functie-app die u in het eerste artikel hebt gemaakt. Omdat u uw project opnieuw implementeert naar dezelfde app, selecteert u **implementeren** om de waarschuwing over het overschrijven van bestanden te negeren.

1. Nadat de implementatie is voltooid, kunt u de opnieuw geïmplementeerde functie weer testen door krul of een browser te gebruiken. Voeg, net als voorheen, de query reeks `&name=<yourname>` toe aan de URL, zoals in het volgende voor beeld:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. [Bekijk nogmaals het bericht in de opslag wachtrij](#examine-the-output-queue) om te controleren of de uitvoer binding opnieuw een nieuw bericht in de wachtrij genereert.

## <a name="clean-up-resources"></a>Resources opschonen

*Resources* verwijst in Azure naar functie-apps, functies, opslagaccounts enzovoort. Deze zijn gegroepeerd in *resourcegroepen*. U kunt alle resources in een groep verwijderen door de groep zelf te verwijderen.

U hebt resources gemaakt om deze snelstartgidsen te voltooien. Deze resources kunnen bij u in rekening worden gebracht, afhankelijk van de [accountstatus](https://azure.microsoft.com/account/) en [serviceprijzen](https://azure.microsoft.com/pricing/). Als u de resources niet meer nodig hebt, kunt u ze als volgt verwijderen:

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt uw HTTP-geactiveerde functie bijgewerkt om gegevens naar een opslag wachtrij te schrijven. Nu kunt u meer te weten komen over het ontwikkelen van functies met Visual Studio code:

+ [Azure Functions ontwikkelen met Visual Studio code](functions-develop-vs-code.md)
::: zone pivot="programming-language-csharp"  
+ [Voor beelden van complete functie projecten C#in ](/samples/browse/?products=azure-functions&languages=csharp).
+ [Naslag C# informatie voor Azure functions ontwikkel aars](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Voor beelden van complete functie projecten in Java script](/samples/browse/?products=azure-functions&languages=javascript).
+ [Ontwikkelaars handleiding voor Azure Functions java script](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Voor beelden van complete functie projecten in type script](/samples/browse/?products=azure-functions&languages=typescript).
+ [Ontwikkelaars handleiding voor Azure Functions type script](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Voor beelden van complete functie projecten in python](/samples/browse/?products=azure-functions&languages=python).
+ [Azure Functions python-ontwikkelaars handleiding](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Voor beelden van complete functie projecten in Power shell](/samples/browse/?products=azure-functions&languages=azurepowershell).
+ [Azure Functions Power shell-ontwikkelaars handleiding](functions-reference-powershell.md) 
::: zone-end
+ [Azure functions triggers en bindingen](functions-triggers-bindings.md).
+ [Pagina met prijzen voor functies](https://azure.microsoft.com/pricing/details/functions/)
+ Het artikel [kosten voor verbruiks plan schatten](functions-consumption-costs.md) .
