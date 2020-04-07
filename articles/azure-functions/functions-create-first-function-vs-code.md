---
title: Uw eerste functie maken in Azure met behulp van Visual Studio Code
description: Maak een eenvoudige HTTP-geactiveerde functie en publiceer deze op Azure met behulp van de Azure Functions-extensie in Visual Studio Code.
ms.topic: quickstart
ms.date: 01/10/2020
ms.custom: mvc, devcenter
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: c1e5c0a714a8b66d83c19acc53f6a680a9196a90
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673428"
---
# <a name="quickstart-create-an-azure-functions-project-using-visual-studio-code"></a>Snelstart: een Azure-functieproject maken met Visual Studio Code

In dit artikel gebruikt u Visual Studio Code om een functie te maken die reageert op HTTP-verzoeken. Nadat u de code lokaal hebt getest, implementeert u deze naar de serverloze omgeving van Azure-functies. Het voltooien van deze quickstart brengt een kleine kostenpost van enkele USD cent of minder in uw Azure-account met zich mee. 

Er is ook een [CLI-gebaseerde versie](functions-create-first-azure-function-azure-cli.md) van dit artikel.

## <a name="configure-your-environment"></a>Uw omgeving configureren

Voordat u aan de slag gaat, moet u ervoor zorgen dat u de volgende vereisten hebt:

+ Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-python"  
+ [Node.js](https://nodejs.org/), vereist door Windows voor npm. Alleen [Actieve LTS- en Maintenance LTS-versies. ](https://nodejs.org/about/releases/) Gebruik `npm --version` de opdracht om uw versie te controleren.
    Niet vereist voor lokale ontwikkeling op macOS en Linux.   
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-typescript"  
+ [Node.js](https://nodejs.org/), Active LTS en Maintenance LTS versies (10.14.1 aanbevolen). Gebruik `npm --version` de opdracht om uw versie te controleren.
::: zone-end 
::: zone pivot="programming-language-python"
+ [Python 3.8](https://www.python.org/downloads/release/python-381/), [Python 3.7](https://www.python.org/downloads/release/python-375/), [Python 3.6](https://www.python.org/downloads/release/python-368/) worden ondersteund door Azure Functions (x64).
::: zone-end   
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ De [.NET Core SDK 2.2+](https://www.microsoft.com/net/download)  
::: zone-end  
::: zone pivot="programming-language-java"  
+ De [Java Developer Kit](https://aka.ms/azure-jdks), versie 8.

+ [Apache Maven](https://maven.apache.org), versie 3.0 of hoger.
::: zone-end  
+ [Visual Studio Code](https://code.visualstudio.com/) op een van de [ondersteunde platforms.](https://code.visualstudio.com/docs/supporting/requirements#_platforms)  
::: zone pivot="programming-language-csharp"  
+ De [C#-extensie](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) voor Visual Studio Code.  
::: zone-end  
::: zone pivot="programming-language-python"
+ De [Python-extensie](https://marketplace.visualstudio.com/items?itemName=ms-python.python) voor Visual Studio Code.  
::: zone-end  
::: zone pivot="programming-language-powershell"
+ De [PowerShell-extensie voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell). 
::: zone-end  
::: zone pivot="programming-language-java"  
+ Het [Java-uitbreidingspakket](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
::: zone-end  

+ De [Azure Functions-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) voor Visual Studio Code. 

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Uw lokale project maken 

In deze sectie gebruikt u Visual Studio Code om een lokaal Azure-functieproject in de door u gekozen taal te maken. Later in dit artikel publiceert u uw functiecode naar Azure. 

1. Kies het Azure-pictogram in de activiteitsbalk en selecteer vervolgens in het gebied **Azure: Functions** het pictogram **Nieuw project maken...** .

    ![Een nieuw project maken kiezen](media/functions-create-first-function-vs-code/create-new-project.png)

1. Kies een maplocatie voor uw projectwerkruimte en kies **Selecteren**.

    > [!NOTE]
    > Deze stappen zijn ontworpen om buiten een werkruimte te worden voltooid. Selecteer in dit geval geen projectmap die deel uitmaakt van een werkruimte.

1. Geef de volgende informatie bij de prompts:

    ::: zone pivot="programming-language-csharp"
    + **Selecteer een taal voor uw functieproject:** Kies `C#`.
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    + **Selecteer een taal voor uw functieproject:** Kies `JavaScript`.
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    + **Selecteer een taal voor uw functieproject:** Kies `TypeScript`.
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    + **Selecteer een taal voor uw functieproject:** Kies `PowerShell`.
    ::: zone-end
    ::: zone pivot="programming-language-python"
    + **Selecteer een taal voor uw functieproject:** Kies `Python`.

    + **Selecteer een Python-alias om een virtuele omgeving te maken:** Kies de locatie van uw Python-tolk. Als de locatie niet wordt weergegeven, typt u het volledige pad naar uw Python-binaire.  
    ::: zone-end

    ::: zone pivot="programming-language-java"  
    + **Selecteer een taal voor uw functieproject:** Kies `Java`.

    + **Geef een groeps-ID op:** Kies `com.function`.

    + **Geef een artefact-id op:** Kies `myFunction`.

    + **Geef een**versie `1.0-SNAPSHOT`op: Kies .

    + **Geef een pakketnaam op:** Kies `com.function`.

    + **Geef een app-naam op:** Kies `myFunction-12345`.
    ::: zone-end  
    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"
    + **Selecteer een sjabloon voor de eerste functie van uw project:** Kies `HTTP trigger`.
    
    + **Geef een functienaam**op: Type `HttpExample`.
    ::: zone-end  
    ::: zone pivot="programming-language-csharp"
    + **Geef een naamruimte op:** Type `My.Functions`. 
    ::: zone-end  
    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"
    + **Autorisatieniveau**: `Anonymous`Kies , waarmee iedereen uw functieeindpunt kan aanroepen. Zie [Autorisatiesleutels](functions-bindings-http-webhook-trigger.md#authorization-keys)voor meer informatie over autorisatieniveau.
    ::: zone-end  
    + **Selecteer hoe u uw project wilt openen:** Kies `Add to workspace`.

1. Met behulp van deze informatie genereert Visual Studio Code een Azure Functions-project met een HTTP-trigger. U de lokale projectbestanden in de Explorer weergeven. Zie [Gegenereerde projectbestanden](functions-develop-vs-code.md#generated-project-files)voor meer informatie over bestanden die zijn gemaakt. 

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python,programming-language-java"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

Nadat u hebt geverifieerd dat de functie correct wordt uitgevoerd op uw lokale computer, is het tijd om Visual Studio Code te gebruiken om het project rechtstreeks naar Azure te publiceren. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>De functie uitvoeren in Azure

1. Terug in het **azure: het** gebied Functies in de zijbalk, vouw t/m de nieuwe functie-app uit onder uw abonnement. **Functies**uitvouwen , met de rechtermuisknop (Windows) of Ctrl + klik op **(macOS)** op HttpExample en kies **vervolgens URL van functie kopiëren**.

    ![De functie-URL voor de nieuwe HTTP-trigger kopiëren](./media/functions-create-first-function-vs-code/function-copy-endpoint-url.png)

1. Plak deze URL voor het HTTP-verzoek in de `name` adresbalk `?name=Functions` van uw browser, voeg de querytekenreeks toe aan het einde van deze URL en voer het verzoek uit. De URL die uw HTTP-geactiveerde functie aanroept, moet de volgende indeling hebben:

        http://<functionappname>.azurewebsites.net/api/httpexample?name=Functions 
        
    In het volgende voorbeeld wordt het antwoord in de browser op de externe GET-aanvraag weergegeven die door de functie wordt geretourneerd: 

    ![Het antwoord van de functie in de browser](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u doorgaat met de volgende stap, [voegt u een Azure Storage-wachtrij toe die aan uw functie is gebonden,](functions-add-output-binding-storage-queue-vs-code.md)moet u al uw resources op hun plaats houden om voort te bouwen op wat u al hebt gedaan.

Anders u de volgende stappen gebruiken om de functie-app en de bijbehorende resources te verwijderen om te voorkomen dat u nog meer kosten hoeft te maken.

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

Zie Kosten van het [verbruiksplan schatten](functions-consumption-costs.md)voor meer informatie over de kosten van functies.

## <a name="next-steps"></a>Volgende stappen

U hebt een functie-app met een eenvoudige HTTP-geactiveerde functie gemaakt in Visual Studio Code. In het volgende artikel vouwt u die functie uit door een uitvoerbinding toe te voegen. Met deze binding wordt de tekenreeks van het HTTP-verzoek naar een bericht in een Azure Queue Storage-wachtrij geschreven. 

> [!div class="nextstepaction"]
> [Een Azure Storage-wachtrijbinding toevoegen aan uw functie](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
