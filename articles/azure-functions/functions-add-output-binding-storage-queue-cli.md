---
title: Azure-functies verbinden met Azure Storage met behulp van opdrachtregelgereedschappen
description: Meer informatie over het verbinden van Azure-functies met een Azure Storage-wachtrij door een uitvoerbinding toe te voegen aan uw opdrachtregelproject.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: f9d9573523083b6355f423b7b3db94b795d8657f
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673339"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Azure-functies verbinden met Azure Storage met behulp van opdrachtregelgereedschappen

In dit artikel integreert u een Azure Storage-wachtrij met het functie- en opslagaccount dat u in [de vorige quickstart](functions-create-first-azure-function-azure-cli.md)hebt gemaakt. U bereikt deze integratie door een *uitvoerbinding te* gebruiken die gegevens van een HTTP-verzoek naar een bericht in de wachtrij schrijft. Het invullen van dit artikel brengt geen extra kosten met zich mee die verder gaan dan de paar USD-cent van de vorige quickstart. Zie Azure Functions triggers [and bindings concepts](functions-triggers-bindings.md)voor meer informatie over bindingen.

## <a name="configure-your-local-environment"></a>Uw lokale omgeving configureren

Voordat u begint, moet u het artikel voltooien, [Quickstart: Een Azure-functieproject maken vanaf de opdrachtregel](functions-create-first-azure-function-azure-cli.md). Als u aan het einde van dat artikel resources al hebt opgeschoond, gaat u de stappen opnieuw door om de functie-app en gerelateerde resources in Azure opnieuw te maken.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end   

Zie [Azure Functions triggers and bindings concepts and](functions-triggers-bindings.md) queue [output configuration](functions-bindings-storage-queue-output.md#configuration)voor meer informatie over de details van bindingen.

## <a name="add-code-to-use-the-output-binding"></a>Code toevoegen om de uitvoerbinding te gebruiken

Met de wachtrijbinding gedefinieerd, u nu `msg` uw functie bijwerken om de uitvoerparameter te ontvangen en berichten naar de wachtrij te schrijven.

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

Houd er rekening mee dat u *geen* code hoeft te schrijven voor verificatie, een wachtrijverwijzing of het schrijven van gegevens. Al deze integratietaken worden handig verwerkt in de runtime van Azure Functions en de binding voor wachtrijuitvoer.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Het bericht weergeven in de azure-opslagwachtrij

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

## <a name="redeploy-the-project-to-azure"></a>Het project opnieuw implementeren in Azure

Nu u lokaal hebt geverifieerd dat de functie een bericht heeft geschreven naar de Azure Storage-wachtrij, u uw project opnieuw implementeren om het eindpunt dat op Azure wordt uitgevoerd bij te werken.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp" 
Gebruik in de map *LocalFunctionsProj* de [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) opdracht om`<APP_NAME>` het project opnieuw te implementeren, vervangen door de naam van uw app.

```
func azure functionapp publish <APP_NAME>
```
::: zone-end  

::: zone pivot="programming-language-java" 

Gebruik in de lokale projectmap de volgende opdracht Maven om uw project opnieuw te publiceren:
```
mvn azure-functions:deploy
```
::: zone-end

## <a name="verify-in-azure"></a>Verifiëren in Azure

1. Net als in de vorige quickstart gebruikt u een browser of CURL om de opnieuw geïmplementeerde functie te testen.

    # <a name="browser"></a>[Browser](#tab/browser)
    
    Kopieer de volledige **URL van aanroepen** die in de uitvoer van de `&name=Functions`opdracht publiceren wordt weergegeven naar een adresbalk van de browser, waarbij de queryparameter wordt toegevoegd. De browser moet dezelfde uitvoer weergeven als wanneer u de functie lokaal hebt uitgevoerd.

    ![De uitvoer van de functie wordt uitgevoerd op Azure in een browser](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # <a name="curl"></a>[Curl](#tab/curl)
    
    Voer [`curl`](https://curl.haxx.se/) uit met de URL `&name=Functions` **aanroepen**, waarbij de parameter wordt toegevoegd . De uitvoer van de opdracht moet de tekst zijn, 'Hallo functies'.
    
    ![De uitvoer van de functie wordt uitgevoerd op Azure met CURL](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-curl.png)

    --- 

1. Controleer de opslagwachtrij opnieuw, zoals beschreven in de vorige sectie, om te controleren of het nieuwe bericht naar de wachtrij is geschreven.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u klaar bent, gebruikt u de volgende opdracht om de brongroep en al de opgenomen resources te verwijderen om te voorkomen dat u nog meer kosten hoeft te maken.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Volgende stappen

U hebt de functie HTTP-geactiveerd bijgewerkt om gegevens naar een opslagwachtrij te schrijven. Nu u meer informatie krijgen over het ontwikkelen van functies vanaf de opdrachtregel met Behulp van Core Tools en Azure CLI:

+ [Werken met Core-hulpprogramma's voor Azure-functies](functions-run-local.md)  

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
+ [Azure Functions triggers en bindingen](functions-triggers-bindings.md)

+ [Pagina Functiesprijzen](https://azure.microsoft.com/pricing/details/functions/)

+ [Kosten van het verbruiksplan schatten](functions-consumption-costs.md) 
