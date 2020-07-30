---
title: Uw eerste functie maken in Azure met behulp van Visual Studio Code
description: Maak een eenvoudige HTTP-geactiveerde functie en publiceer deze op Azure met behulp van de Azure Functions-extensie in Visual Studio Code.
ms.topic: quickstart
ms.date: 01/10/2020
ms.custom: mvc, devcenter, seo, tracking-python
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 4f13d3bbd3f5b9d8197c48822ea54f08d3d86bfc
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87056099"
---
# <a name="quickstart-create-a-function-in-azure-using-visual-studio-code"></a>Quickstart: Een functie maken in Azure met behulp van Visual Studio Code

::: zone pivot="programming-language-csharp"  
In dit artikel gebruikt u Visual Studio Code om een op een C#-klassenbibliotheek gebaseerde functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. 
::: zone-end  
::: zone pivot="programming-language-javascript"
In dit artikel gebruikt u Visual Studio Code om een JavaScript-functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. 
::: zone-end
::: zone pivot="programming-language-typescript"
In dit artikel gebruikt u Visual Studio Code om een TypeScript-functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. 
::: zone-end   
::: zone pivot="programming-language-powershell"
In dit artikel gebruikt u Visual Studio Code om een PowerShell-functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. 
::: zone-end  
::: zone pivot="programming-language-python" 
In dit artikel gebruikt u Visual Studio Code om een Python-functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. 
::: zone-end  
::: zone pivot="programming-language-java" 
In dit artikel gebruikt u Visual Studio Code om een Java-functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. 
::: zone-end

Voor het voltooien van deze quickstart worden kosten van een paar dollarcent of minder in rekening gebracht bij uw Azure-account. 

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"
Er is ook een [op een CLI gebaseerde versie](functions-create-first-azure-function-azure-cli.md) van dit artikel.
::: zone-end
::: zone pivot="programming-language-java"  
> [!NOTE]
> Als u liever niet ontwikkelt met Visual Studio Code, raadpleegt u onze vergelijkbare zelfstudies voor Java-ontwikkelaars met [Maven](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java), [Gradle](./functions-create-first-java-gradle.md) en[ IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions).
::: zone-end  

## <a name="configure-your-environment"></a>Uw omgeving configureren

Voordat u aan de slag kunt gaan, moet u beschikken over de volgende vereisten:

+ Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-python"  
+ [Node.js](https://nodejs.org/), vereist door Windows voor NPM. Alleen [Active LTS en Maintenance LTS-versies](https://nodejs.org/about/releases/). Gebruik de opdracht `node --version` om uw versie te controleren.
    Niet vereist voor lokale ontwikkeling in macOS en Linux.   
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-typescript"  
+ [Node.js](https://nodejs.org/), Active LTS en Maintenance LTS-versies (10.14.1 aanbevolen). Gebruik de opdracht `node --version` om uw versie te controleren.
::: zone-end 
::: zone pivot="programming-language-python"
+ [Python 3.8](https://www.python.org/downloads/release/python-381/), [Python 3.7](https://www.python.org/downloads/release/python-375/), [Python 3.6](https://www.python.org/downloads/release/python-368/) worden ondersteund door Azure Functions (x64).
::: zone-end   
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ De [.NET Core-SDK 2.2+](https://www.microsoft.com/net/download)  
::: zone-end  
::: zone pivot="programming-language-java"  
+ [Java Developer Kit](https://aka.ms/azure-jdks), versie 8.

+ [Apache Maven](https://maven.apache.org), versie 3.0 of hoger.
::: zone-end  
+ [Visual Studio Code](https://code.visualstudio.com/) op een van de [ondersteunde platforms](https://code.visualstudio.com/docs/supporting/requirements#_platforms).  
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

In deze sectie gebruikt u Visual Studio Code om een lokaal Azure Functions-project te maken in de taal van uw keuze. Verderop in dit artikel publiceert u de functiecode in Azure. 

1. Kies het Azure-pictogram in de activiteitenbalk en selecteer vervolgens in het gedeelte **Azure: Functions** het pictogram **Nieuw project maken...** .

    ![Een nieuw project maken kiezen](media/functions-create-first-function-vs-code/create-new-project.png)

1. Kies een maplocatie voor de werkruimte van uw project en kies **Selecteren**.

    > [!NOTE]
    > Deze stappen waren bedoeld om buiten een werkruimte te worden voltooid. Selecteer in dit geval geen projectmap die deel uitmaakt van een werkruimte.

1. Geef de volgende informatie op bij de prompts:

    ::: zone pivot="programming-language-csharp"
    + **Selecteer een taal voor uw functieproject**: Kies `C#`.
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    + **Selecteer een taal voor uw functieproject**: Kies `JavaScript`.
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    + **Selecteer een taal voor uw functieproject**: Kies `TypeScript`.
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    + **Selecteer een taal voor uw functieproject**: Kies `PowerShell`.
    ::: zone-end
    ::: zone pivot="programming-language-python"
    + **Selecteer een taal voor uw functieproject**: Kies `Python`.

    + **Selecteer een Python-alias voor het maken van een virtuele omgeving**: Kies de locatie van de Python-interpreter. Als de locatie niet wordt weergegeven, typt u het volledige pad naar uw binaire Python-bestand.  
    ::: zone-end

    ::: zone pivot="programming-language-java"  
    + **Selecteer een taal voor uw functieproject**: Kies `Java`.

    + **Geef een groeps-id op**: Kies `com.function`.

    + **Geef een artefact-id op**: Kies `myFunction`.

    + **Geef een versie op**: Kies `1.0-SNAPSHOT`.

    + **Geef een pakketnaam op**: Kies `com.function`.

    + **Geef een app-naam op**: Kies `myFunction-12345`.
    ::: zone-end  
    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"
    + **Selecteer een sjabloon voor de eerste functie van uw project**: Kies `HTTP trigger`.
    
    + **Geef een functienaam op**: Typ `HttpExample`.
    ::: zone-end  
    ::: zone pivot="programming-language-csharp"
    + **Geef een naamruimte op**: Typ `My.Functions`. 
    ::: zone-end  
    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"
    + **Autorisatieniveau**: Kies `Anonymous`, waarmee iedereen uw functie-eindpunt kan aanroepen. Zie [Autorisatiesleutels](functions-bindings-http-webhook-trigger.md#authorization-keys) voor meer informatie over autorisatieniveau.
    ::: zone-end  
    + **Selecteer hoe u uw project wilt openen**: Kies `Add to workspace`.

1. Met behulp van deze informatie wordt met Visual Studio Code een Azure Functions-project gegenereerd met een HTTP-trigger. U kunt de lokale projectbestanden weergeven in de Explorer. Zie [Gegenereerde projectbestanden](functions-develop-vs-code.md#generated-project-files) voor meer informatie over bestanden die worden gemaakt. 

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python,programming-language-java"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

Nadat u hebt gecontroleerd of de functie correct wordt uitgevoerd op uw lokale computer, is het tijd om het project te publiceren in Azure met behulp van Visual Studio Code. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>De functie in Azure uitvoeren

1. Ga terug naar **Azure: Functions** en vouw in de zijbalk de nieuwe functie-app uit onder uw abonnement. Vouw **Functies** uit, klik met de rechtermuisknop (Windows) of CTRL + klik (macOS) op **HttpExample** en kies vervolgens **Functie-URL kopiëren**.

    ![De functie-URL kopiëren voor de nieuwe HTTP-trigger](./media/functions-create-first-function-vs-code/function-copy-endpoint-url.png)

1. Plak deze URL voor de HTTP-aanvraag in de adresbalk van uw browser, voeg de `name`queryreeks toe als `?name=Functions` aan het einde van deze URL en voer de aanvraag uit. De URL die uw HTTP-geactiveerde functie aanroept, moet de volgende indeling hebben:

    ```http
    http://<functionappname>.azurewebsites.net/api/httpexample?name=Functions
    ```
        
    U ziet in het volgende voorbeeld het antwoord op de externe GET-aanvraag dat door de functie wordt geretourneerd, weergegeven in de browser: 

    ![Het antwoord van de functie in de browser](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u verder gaat met de volgende stap, [Een Azure Storage-wachtrijbinding aan uw functie toevoegen](functions-add-output-binding-storage-queue-vs-code.md), moet u alle resources op dezelfde plaats laten staan, zodat u hiermee verder kunt gaan met wat u al hebt gedaan.

Als dat niet het geval is, kunt u de volgende stappen gebruiken om de functie-app en de bijbehorende resources te verwijderen om te voorkomen dat er verdere kosten in rekening worden gebracht.

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

Zie [Kosten voor verbruiksplannen inschatten](functions-consumption-costs.md) voor meer informatie over de kosten voor Functions.

## <a name="next-steps"></a>Volgende stappen

U hebt een functie-app met een eenvoudige HTTP-geactiveerde functie gemaakt in Visual Studio Code. In het volgende artikel vouwt u die functie uit door een uitvoerbinding toe te voegen. Met deze binding wordt de tekenreeks van de HTTP-aanvraag naar een bericht in een Azure Queue Storage-wachtrij geschreven. 

> [!div class="nextstepaction"]
> [Een Azure Storage-wachtrijbinding aan uw functie toevoegen](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
