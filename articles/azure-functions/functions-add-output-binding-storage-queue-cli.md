---
title: Azure Functions verbinding maken met Azure Storage met behulp van opdracht regel Programma's
description: Informatie over het verbinden van Azure Functions met een Azure Storage wachtrij door een uitvoer binding toe te voegen aan het opdracht regel project.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: f9d9573523083b6355f423b7b3db94b795d8657f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673339"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Azure Functions verbinding maken met Azure Storage met behulp van opdracht regel Programma's

In dit artikel integreert u een Azure Storage wachtrij met het functie-en opslag account dat u in [de vorige Snelstartgids](functions-create-first-azure-function-azure-cli.md)hebt gemaakt. U verkrijgt deze integratie met behulp van een *uitvoer binding* waarmee gegevens worden geschreven van een HTTP-aanvraag naar een bericht in de wachtrij. In dit artikel worden geen extra kosten in rekening gebracht tot meer dan de enige USD cent van de vorige Snelstartgids. Zie [Azure functions triggers en bindingen](functions-triggers-bindings.md)voor meer informatie over bindingen.

## <a name="configure-your-local-environment"></a>Uw lokale omgeving configureren

Voordat u begint, moet u het artikel volt ooien, [Snelstartgids: een Azure functions-project maken vanaf de opdracht regel](functions-create-first-azure-function-azure-cli.md). Als u de resources aan het einde van dat artikel al hebt opgeruimd, kunt u de stappen opnieuw door lopen om de functie-app en gerelateerde resources in azure opnieuw te maken.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end   

Zie voor meer informatie over de details van bindingen [Azure functions triggers en bindingen](functions-triggers-bindings.md) en de configuratie van de [wachtrij-uitvoer](functions-bindings-storage-queue-output.md#configuration).

## <a name="add-code-to-use-the-output-binding"></a>Code toevoegen voor het gebruik van de uitvoer binding

Als de wachtrij binding is gedefinieerd, kunt u de functie nu bijwerken om de `msg` uitvoer parameter te ontvangen en berichten te schrijven naar de wachtrij.

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

Houd er rekening mee dat u *geen* code hoeft te schrijven voor verificatie, het ophalen van een wachtrij verwijzing of het schrijven van gegevens. Al deze integratie taken kunnen worden verwerkt in de Azure Functions runtime-en wachtrij-uitvoer binding.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Het bericht in de wachtrij van Azure Storage weer geven

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

## <a name="redeploy-the-project-to-azure"></a>Het project opnieuw implementeren in azure

Nu u lokaal hebt gecontroleerd dat de functie een bericht naar de Azure Storage wachtrij heeft geschreven, kunt u uw project opnieuw implementeren om het eind punt bij te werken dat op Azure wordt uitgevoerd.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp" 
Gebruik in de map *LocalFunctionsProj* de [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) opdracht om het project opnieuw te implementeren en vervang`<APP_NAME>` door de naam van uw app.

```
func azure functionapp publish <APP_NAME>
```
::: zone-end  

::: zone pivot="programming-language-java" 

Gebruik in de lokale projectmap de volgende maven-opdracht om het project opnieuw te publiceren:
```
mvn azure-functions:deploy
```
::: zone-end

## <a name="verify-in-azure"></a>Verifiëren in azure

1. Net als in de vorige Snelstartgids gebruikt u een browser of krul om de opnieuw geïmplementeerde functie te testen.

    # <a name="browser"></a>[Browser](#tab/browser)
    
    Kopieer de volledige **invoke-URL** die wordt weer gegeven in de uitvoer van de opdracht publiceren naar een adres balk van de browser `&name=Functions`en voeg de query parameter toe. De browser moet vergelijk bare uitvoer weer geven als u de functie lokaal hebt uitgevoerd.

    ![De uitvoer van de functie wordt uitgevoerd op Azure in een browser](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # <a name="curl"></a>[Ezelsoor](#tab/curl)
    
    Voer [`curl`](https://curl.haxx.se/) uit met de **aanroepen-URL**en voeg `&name=Functions`de para meter toe. De uitvoer van de opdracht moet de tekst ' Hello functions ' zijn.
    
    ![De uitvoer van de functie wordt uitgevoerd op Azure met behulp van krul](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-curl.png)

    --- 

1. Controleer de opslag wachtrij opnieuw, zoals beschreven in de vorige sectie, om te controleren of deze het nieuwe bericht bevat dat naar de wachtrij is geschreven.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent, gebruikt u de volgende opdracht om de resource groep en alle bijbehorende resources te verwijderen om te voor komen dat er meer kosten in rekening worden gebracht.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Volgende stappen

U hebt uw HTTP-geactiveerde functie bijgewerkt om gegevens naar een opslag wachtrij te schrijven. Nu vindt u meer informatie over het ontwikkelen van functies vanaf de opdracht regel met behulp van kern Hulpprogramma's en Azure CLI:

+ [Werken met Azure Functions Core Tools](functions-run-local.md)  

::: zone pivot="programming-language-csharp"  
+ [Voor beelden van complete functie projecten in C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Naslag informatie voor Azure Functions C#-ontwikkel aars](functions-dotnet-class-library.md)  
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
+ [Azure Functions triggers en bindingen](functions-triggers-bindings.md)

+ [Pagina met prijzen voor functies](https://azure.microsoft.com/pricing/details/functions/)

+ [Kosten voor verbruiks plan schatten](functions-consumption-costs.md) 
