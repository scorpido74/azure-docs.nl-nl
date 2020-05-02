---
title: Uw eerste functie maken in Azure met behulp van Visual Studio Code
description: Maak een eenvoudige HTTP-geactiveerde functie en publiceer deze op Azure met behulp van de Azure Functions-extensie in Visual Studio Code.
ms.topic: quickstart
ms.date: 01/10/2020
ms.custom: mvc, devcenter, seo
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 64c2e813743a772692efcb1d966c8ab7b52cc66d
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628177"
---
# <a name="quickstart-create-a-function-in-azure-using-visual-studio-code"></a>Quick Start: een functie maken in azure met Visual Studio code

::: zone pivot="programming-language-csharp"  
In dit artikel gebruikt u Visual Studio code voor het maken van een op een C#-klassen bibliotheek gebaseerde functie die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. 
::: zone-end  
::: zone pivot="programming-language-javascript"
In dit artikel gebruikt u Visual Studio code om een Java script-functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. 
::: zone-end
::: zone pivot="programming-language-typescript"
In dit artikel gebruikt u Visual Studio code voor het maken van een type script-functie die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. 
::: zone-end   
::: zone pivot="programming-language-powershell"
In dit artikel gebruikt u Visual Studio code om een Power shell-functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. 
::: zone-end  
::: zone pivot="programming-language-python" 
In dit artikel gebruikt u Visual Studio code voor het maken van een python-functie die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. 
::: zone-end  
::: zone pivot="programming-language-java" 
In dit artikel gebruikt u Visual Studio code om een Java-functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. 
::: zone-end

Het volt ooien van deze Quick Start brengt een kleine prijs van een paar USD cent of minder in uw Azure-account. 

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"
Er is ook een [CLI-versie](functions-create-first-azure-function-azure-cli.md) van dit artikel.
::: zone-end
::: zone pivot="programming-language-java"  
> [!NOTE]
> Als Visual Studio code niet uw voor keur is, raadpleegt u onze vergelijk bare zelf studies voor Java-Ontwikkel aars die gebruikmaken van [maven](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java), [Gradle](/azure/azure-functions/functions-create-first-java-gradle) en [IntelliJ](/azure/developer/java/toolkit-for-intellij/quickstart-functions).
::: zone-end  

## <a name="configure-your-environment"></a>Uw omgeving configureren

Voordat u aan de slag gaat, moet u ervoor zorgen dat u aan de volgende vereisten voldoet:

+ Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-python"  
+ [Node. js](https://nodejs.org/), vereist voor Windows voor NPM. Alleen [actieve LTS en onderhoud LTS-versies](https://nodejs.org/about/releases/). Gebruik de `node --version` opdracht om uw versie te controleren.
    Niet vereist voor lokale ontwikkeling in macOS en Linux.   
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-typescript"  
+ [Node. js](https://nodejs.org/), Active LTS en onderhoud LTS-versies (10.14.1 aanbevolen). Gebruik de `node --version` opdracht om uw versie te controleren.
::: zone-end 
::: zone pivot="programming-language-python"
+ [Python 3,8](https://www.python.org/downloads/release/python-381/), [python 3,7](https://www.python.org/downloads/release/python-375/), [python 3,6](https://www.python.org/downloads/release/python-368/) wordt ondersteund door Azure functions (x64).
::: zone-end   
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ De [.net core SDK 2.2 +](https://www.microsoft.com/net/download)  
::: zone-end  
::: zone pivot="programming-language-java"  
+ De [Java Developer Kit](https://aka.ms/azure-jdks), versie 8.

+ [Apache Maven](https://maven.apache.org), versie 3,0 of hoger.
::: zone-end  
+ [Visual Studio code](https://code.visualstudio.com/) op een van de [ondersteunde platforms](https://code.visualstudio.com/docs/supporting/requirements#_platforms).  
::: zone pivot="programming-language-csharp"  
+ De [C#-extensie](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) voor Visual Studio code.  
::: zone-end  
::: zone pivot="programming-language-python"
+ De [python-extensie](https://marketplace.visualstudio.com/items?itemName=ms-python.python) voor Visual Studio code.  
::: zone-end  
::: zone pivot="programming-language-powershell"
+ De [Power shell-extensie voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell). 
::: zone-end  
::: zone pivot="programming-language-java"  
+ Het [Java-uitbreidings pakket](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
::: zone-end  

+ De [uitbrei ding](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) van de Azure functions voor Visual Studio code. 

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Uw lokale project maken 

In deze sectie gebruikt u Visual Studio code om een lokaal Azure Functions project te maken in de taal die u hebt gekozen. Verderop in dit artikel publiceert u uw functie code naar Azure. 

1. Kies het pictogram van Azure in de activiteiten balk en selecteer vervolgens in het gebied **Azure: functions** het pictogram **Nieuw project maken...** .

    ![Kies een nieuw project maken](media/functions-create-first-function-vs-code/create-new-project.png)

1. Kies een maplocatie voor de Project werkruimte en kies **selecteren**.

    > [!NOTE]
    > Deze stappen zijn zodanig ontworpen dat ze buiten een werk ruimte worden uitgevoerd. Selecteer in dit geval geen projectmap die deel uitmaakt van een werkruimte.

1. Geef de volgende informatie op bij de prompts:

    ::: zone pivot="programming-language-csharp"
    + **Selecteer een taal voor uw functie project**: kiezen `C#`.
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    + **Selecteer een taal voor uw functie project**: kiezen `JavaScript`.
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    + **Selecteer een taal voor uw functie project**: kiezen `TypeScript`.
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    + **Selecteer een taal voor uw functie project**: kiezen `PowerShell`.
    ::: zone-end
    ::: zone pivot="programming-language-python"
    + **Selecteer een taal voor uw functie project**: kiezen `Python`.

    + **Selecteer een python-alias om een virtuele omgeving te maken**: Kies de locatie van de Python-interpreter. Als de locatie niet wordt weer gegeven, typt u het volledige pad naar uw python binaire bestand.  
    ::: zone-end

    ::: zone pivot="programming-language-java"  
    + **Selecteer een taal voor uw functie project**: kiezen `Java`.

    + **Geef een groeps-id**op: Kies `com.function`.

    + **Geef een artefact-id**op `myFunction`: Kies.

    + **Een versie opgeven**: kiezen `1.0-SNAPSHOT`.

    + **Geef een pakket naam**op: `com.function`Kies.

    + **Geef een naam op voor de app**: kiezen `myFunction-12345`.
    ::: zone-end  
    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"
    + **Selecteer een sjabloon voor de eerste functie van uw project**: `HTTP trigger`kiezen.
    
    + **Geef een functie naam**op: `HttpExample`type.
    ::: zone-end  
    ::: zone pivot="programming-language-csharp"
    + **Geef een naam ruimte**op `My.Functions`: type. 
    ::: zone-end  
    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"
    + **Autorisatie niveau**: Kies `Anonymous`, waarmee iedereen uw functie-eind punt kan aanroepen. Zie [autorisatie sleutels](functions-bindings-http-webhook-trigger.md#authorization-keys)voor meer informatie over autorisatie niveau.
    ::: zone-end  
    + **Selecteer hoe u uw project wilt openen**: Kies `Add to workspace`.

1. Met behulp van deze informatie wordt met Visual Studio code een Azure Functions project gegenereerd met een HTTP-trigger. U kunt de lokale project bestanden weer geven in de Explorer. Zie [gegenereerde project bestanden](functions-develop-vs-code.md#generated-project-files)voor meer informatie over bestanden die worden gemaakt. 

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python,programming-language-java"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

Nadat u hebt gecontroleerd of de functie correct wordt uitgevoerd op uw lokale computer, is het tijd om Visual Studio code te gebruiken om het project rechtstreeks naar Azure te publiceren. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>De functie uitvoeren in azure

1. Ga terug naar het gebied **Azure: functions** in de zijbalk en vouw de nieuwe functie-app uit onder uw abonnement. Vouw **functies**uit, klik met de rechter muisknop (Windows) of CTRL + klik (macOS) op **HttpExample**en kies **functie-URL kopiëren**.

    ![Kopieer de functie-URL voor de nieuwe HTTP-trigger](./media/functions-create-first-function-vs-code/function-copy-endpoint-url.png)

1. Plak deze URL voor de HTTP-aanvraag in de adres balk van uw browser, `name` Voeg de query `?name=Functions` reeks toe aan het einde van deze URL en voer de aanvraag uit. De URL die uw HTTP-geactiveerde functie aanroept, moet de volgende indeling hebben:

        http://<functionappname>.azurewebsites.net/api/httpexample?name=Functions 
        
    In het volgende voor beeld wordt het antwoord in de browser weer gegeven op de aanvraag voor het extern ophalen van de functie: 

    ![Het antwoord van de functie in de browser](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u verdergaat met de volgende stap, [voegt u een Azure Storage wachtrij binding aan uw functie toe](functions-add-output-binding-storage-queue-vs-code.md). u moet al uw resources op de juiste plaats houden om te kunnen bouwen op wat u al hebt gedaan.

Als dat niet het geval is, kunt u de volgende stappen gebruiken om de functie-app en de bijbehorende resources te verwijderen om te voor komen dat er verdere kosten in rekening worden gebracht.

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

Zie costing cost costing ( [verbruiks kosten schatten](functions-consumption-costs.md)) voor meer informatie over functies.

## <a name="next-steps"></a>Volgende stappen

U hebt een functie-app met een eenvoudige HTTP-geactiveerde functie gemaakt in Visual Studio Code. In het volgende artikel vouwt u die functie uit door een uitvoer binding toe te voegen. Deze binding schrijft de teken reeks van de HTTP-aanvraag naar een bericht in een Azure Queue Storage-wachtrij. 

> [!div class="nextstepaction"]
> [Een Azure Storage wachtrij binding aan uw functie toevoegen](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
