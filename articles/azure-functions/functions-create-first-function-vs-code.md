---
title: Uw eerste functie maken in Azure met behulp van Visual Studio Code
description: Maak een eenvoudige HTTP-geactiveerde functie en publiceer deze op Azure met behulp van de Azure Functions-extensie in Visual Studio Code.
ms.topic: quickstart
ms.date: 01/10/2020
ms.custom: mvc, devcenter
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 0540c7b01d693975f34515c7d13f0477ac74d4a1
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842139"
---
# <a name="quickstart-create-an-azure-functions-project-using-visual-studio-code"></a>Snelstartgids: een Azure Functions-project maken met Visual Studio code

In dit artikel gebruikt u Visual Studio code om een functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. Het volt ooien van deze Quick Start brengt een kleine prijs van een paar USD cent of minder in uw Azure-account. 

Er is ook een [CLI-versie](functions-create-first-azure-function-azure-cli.md) van dit artikel.

## <a name="prerequisites"></a>Vereisten

+ [Visual Studio code](https://code.visualstudio.com/) op een van de [ondersteunde platforms](https://code.visualstudio.com/docs/supporting/requirements#_platforms). 
::: zone pivot="programming-language-csharp"
+ De [ C# extensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) voor Visual Studio code.
::: zone-end
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node. js](https://nodejs.org/), Active LTS en onderhoud LTS-versies (8.11.1 en 10.14.1 aanbevolen).
::: zone-end
::: zone pivot="programming-language-python"
+ [Python 3,7](https://www.python.org/downloads/release/python-375/) of [python 3,6](https://www.python.org/downloads/release/python-368/), die wordt ondersteund door Azure functions. Python 3,8 wordt nog niet ondersteund. 

+ De [python-extensie](https://marketplace.visualstudio.com/items?itemName=ms-python.python) voor Visual Studio code.
::: zone-end
::: zone pivot="programming-language-powershell"
+ [Power shell core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ De [.net core SDK 2.2 +](https://www.microsoft.com/net/download)

+ De [Power shell-extensie voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell). 
::: zone-end

+ De [uitbrei ding](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) van de Azure functions voor Visual Studio code. 

+ Een [Azure-account](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) met een actief abonnement. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.

## <a name="create-an-azure-functions-project"></a>Uw lokale project maken 

In deze sectie gebruikt u Visual Studio code om een lokaal Azure Functions project te maken in de taal die u hebt gekozen. Verderop in dit artikel publiceert u uw functie code naar Azure. 

1. Kies het pictogram van Azure in de activiteiten balk en selecteer vervolgens in het gebied **Azure: functions** het pictogram **Nieuw project maken...** .

    ![Kies een nieuw project maken](media/functions-create-first-function-vs-code/create-new-project.png)

1. Kies een maplocatie voor de Project werkruimte en kies **selecteren**.

    > [!NOTE]
    > Deze stappen zijn zodanig ontworpen dat ze buiten een werk ruimte worden uitgevoerd. Selecteer in dit geval geen projectmap die deel uitmaakt van een werkruimte.

1. Geef de volgende informatie op bij de prompts:

    ::: zone pivot="programming-language-csharp"

    | Vraag | Waarde | 
    | ------ | ----- | 
    | Selecteer een taal voor uw functie project | C# |
    | Een versie selecteren | Azure Functions v2 | 
    | Selecteer een sjabloon voor de eerste functie van uw project | HTTP-trigger | 
    | Een functie naam opgeven | HttpExample | 
    | Geef een naam ruimte op | Mijn. functions | 
    | Autorisatieniveau | Anoniem | 
    | Selecteer hoe u uw project wilt openen | Toevoegen aan werk ruimte |

    ::: zone-end

    ::: zone pivot="programming-language-javascript"

    | Vraag | Waarde | 
    | ------ | ----- | 
    | Selecteer een taal voor uw functie project | JavaScript | 
    | Een versie selecteren | Azure Functions v2 | 
    | Selecteer een sjabloon voor de eerste functie van uw project | HTTP-trigger | 
    | Een functie naam opgeven | HttpExample | 
    | Autorisatieniveau | Anoniem | 
    | Selecteer hoe u uw project wilt openen | Toevoegen aan werk ruimte |

    ::: zone-end

    ::: zone pivot="programming-language-typescript"

    | Vraag | Waarde | 
    | ------ | ----- | 
    | Selecteer een taal voor uw functie project | TypeScript | 
    | Een versie selecteren | Azure Functions v2 | 
    | Selecteer een sjabloon voor de eerste functie van uw project | HTTP-trigger | 
    | Een functie naam opgeven | HttpExample | 
    | Autorisatieniveau | Anoniem | 
    | Selecteer hoe u uw project wilt openen | Toevoegen aan werk ruimte |

    ::: zone-end

    ::: zone pivot="programming-language-powershell"

    | Vraag | Waarde | 
    | ------ | ----- | 
    | Selecteer een taal voor uw functie project | PowerShell | 
    | Een versie selecteren | Azure Functions v2 | 
    | Selecteer een sjabloon voor de eerste functie van uw project | HTTP-trigger | 
    | Een functie naam opgeven | HttpExample | 
    | Autorisatieniveau | Anoniem | 
    | Selecteer hoe u uw project wilt openen | Toevoegen aan werk ruimte |

    ::: zone-end

    ::: zone pivot="programming-language-python"

    | Vraag | Waarde | 
    | ------ | ----- | 
    | Selecteer een taal voor uw functie project | Python | 
    | Een versie selecteren | Azure Functions v2 | 
    | Een python-alias selecteren om een virtuele omgeving te maken | Python-alias | 
    | Selecteer een sjabloon voor de eerste functie van uw project | HTTP-trigger | 
    | Een functie naam opgeven | HttpExample | 
    | Autorisatieniveau | Anoniem | 
    | Selecteer hoe u uw project wilt openen | Toevoegen aan werk ruimte | 

    ::: zone-end

1. Visual Studio code doet het volgende:

    + Hiermee maakt u een Azure Functions project in een nieuwe werk ruimte, waarin de configuratie bestanden [host. json](functions-host-json.md) en [Local. settings. json](functions-run-local.md#local-settings-file) zijn opgenomen. 

    ::: zone pivot="programming-language-csharp"

    + Hiermee maakt u een [HttpExample.cs Class-bibliotheek bestand](functions-dotnet-class-library.md#functions-class-library-project) dat de functie implementeert.

    ::: zone-end
        
    ::: zone pivot="programming-language-javascript"
    
    + Hiermee maakt u een package. JSON-bestand in de hoofdmap.

    + Hiermee maakt u een HttpExample-map met daarin het [JSON-definitie bestand](functions-reference-node.md#folder-structure) van de functie en het [bestand index. js](functions-reference-node.md#exporting-a-function), een node. js-bestand dat de functie code bevat.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-typescript"
    
    + Hiermee maakt u een package. JSON-bestand en een tsconfig. JSON-bestand in de hoofdmap.

    + Hiermee maakt u een HttpExample-map met daarin het [definitie bestand function. json](functions-reference-node.md#folder-structure) en het [bestand index. TS](functions-reference-node.md#typescript), een type script-bestand dat de functie code bevat.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-powershell"
    
    + Hiermee maakt u een HttpExample-map met daarin het [definitie bestand function. json](functions-reference-python.md#programming-model) en het bestand run. ps1, dat de functie code bevat.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-python"
    
    + Hiermee maakt u een. txt-bestand voor vereisten op project niveau met een lijst met pakketten die vereist zijn voor-functies.
    
    + Hiermee maakt u een HttpExample-map met daarin het [definitie bestand function. json](functions-reference-python.md#programming-model) en het bestand \_\_init\_\_. py, dat de functie code bevat.
    
    ::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

Nadat u hebt gecontroleerd of de functie correct wordt uitgevoerd op uw lokale computer, is het tijd om Visual Studio code te gebruiken om het project rechtstreeks naar Azure te publiceren. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>De functie uitvoeren in azure

1. Kopieer de URL van de HTTP-trigger vanuit het deelvenster **Output** (Uitvoer). Voeg opnieuw de `name` query reeks toe als `?name=<yourname>` aan het einde van deze URL en voer de aanvraag uit.

    De URL die uw HTTP-geactiveerde functie aanroept, moet de volgende indeling hebben:

        http://<functionappname>.azurewebsites.net/api/httpexample?name=<yourname> 

1. Plak deze nieuwe URL van de HTTP-aanvraag in de adresbalk van uw browser. In het volgende voor beeld wordt het antwoord in de browser weer gegeven op de aanvraag voor het extern ophalen van de functie: 

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
