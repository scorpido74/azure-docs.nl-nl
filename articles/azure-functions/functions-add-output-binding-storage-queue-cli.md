---
title: Azure-functies verbinden met Azure Storage met behulp van opdrachtregelgereedschappen
description: Meer informatie over het verbinden van Azure-functies met een Azure Storage-wachtrij door een uitvoerbinding toe te voegen aan uw opdrachtregelproject.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 9181caf516d5c2003cfe99b125d2921732cbbb9d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79473384"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Azure-functies verbinden met Azure Storage met behulp van opdrachtregelgereedschappen

In dit artikel integreert u een Azure Storage-wachtrij met het functie- en opslagaccount dat u in [de vorige quickstart](functions-create-first-azure-function-azure-cli.md)hebt gemaakt. U bereikt deze integratie door een *uitvoerbinding te* gebruiken die gegevens van een HTTP-verzoek naar een bericht in de wachtrij schrijft. Het invullen van dit artikel brengt geen extra kosten met zich mee die verder gaan dan de paar USD-cent van de vorige quickstart. Zie Azure Functions triggers [and bindings concepts](functions-triggers-bindings.md)voor meer informatie over bindingen.

## <a name="configure-your-local-environment"></a>Uw lokale omgeving configureren

Voordat u begint, moet u het artikel voltooien, [Quickstart: Een Azure-functieproject maken vanaf de opdrachtregel](functions-create-first-azure-function-azure-cli.md). Als u aan het einde van dat artikel resources al hebt opgeschoond, gaat u de stappen opnieuw door om de functie-app en gerelateerde resources in Azure opnieuw te maken.

## <a name="retrieve-the-azure-storage-connection-string"></a>De tekenreeks azure storage-verbinding ophalen

Wanneer u in azure in de vorige quickstart een functie-app hebt gemaakt, hebt u ook een opslagaccount gemaakt. De verbindingstekenreeks voor dit account wordt veilig opgeslagen in app-instellingen in Azure. Als u de instelling downloadt naar het bestand *local.settings.json,* u die verbinding schrijven naar een opslagwachtrij in hetzelfde account gebruiken wanneer u de functie lokaal uitvoert. 

1. Voer vanuit de hoofdmap van het project `<APP_NAME>` de volgende opdracht uit, vervangen door de naam van uw functie-app van de vorige quickstart. Met deze opdracht worden alle bestaande waarden in het bestand overschreven.

    ```
    func azure functionapp fetch-app-settings <APP_NAME>
    ```
    
1. Open *local.settings.json* en zoek `AzureWebJobsStorage`de waarde met de naam , de tekenreeks Opslagaccountverbinding. U gebruikt `AzureWebJobsStorage` de naam en de verbindingstekenreeks in andere secties van dit artikel.

> [!IMPORTANT]
> Omdat *local.settings.json* geheimen bevat die zijn gedownload van Azure, sluit u dit bestand altijd uit van bronbeheer. Het *.gitignore-bestand* dat is gemaakt met een project voor lokale functies, sluit het bestand standaard uit.

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

## <a name="add-an-output-binding-definition-to-the-function"></a>Een uitvoerbindingsdefinitie toevoegen aan de functie

Hoewel een functie slechts één trigger kan hebben, kan deze meerdere invoer- en uitvoerbindingen hebben, waarmee u verbinding maken met andere Azure-services en -bronnen zonder aangepaste integratiecode te schrijven. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
U declareert deze bindingen in het *bestand function.json* in uw functiemap. Van de vorige quickstart bevat het bestand *function.json* in de `bindings` *map HttpExample* twee bindingen in de verzameling:  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
Elke binding heeft ten minste een type, een richting en een naam. In het bovenstaande voorbeeld is de `httpTrigger` eerste `in`binding van type met de richting . Hiermee `in` geeft u voor de richting de naam op van een invoerparameter die naar de functie wordt verzonden wanneer deze door de trigger wordt aangeroepen. `name`  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
De tweede binding in `res`de collectie wordt genoemd. Deze `http` binding is een`out`uitvoerbinding ( ) die wordt gebruikt om het HTTP-antwoord te schrijven. 

Als u vanuit deze functie naar een `out` Azure `queue` Storage-wachtrij `msg`wilt schrijven, voegt u een tekstbinding toe met de naam , zoals in de onderstaande code wordt weergegeven:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
De tweede binding in de `http` collectie `out`is van type `name` met `$return` de richting , in welk geval de speciale van aangeeft dat deze binding de retourwaarde van de functie gebruikt in plaats van het verstrekken van een invoerparameter.

Als u vanuit deze functie naar een `out` Azure `queue` Storage-wachtrij `msg`wilt schrijven, voegt u een tekstbinding toe met de naam , zoals in de onderstaande code wordt weergegeven:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
De tweede binding in `res`de collectie wordt genoemd. Deze `http` binding is een`out`uitvoerbinding ( ) die wordt gebruikt om het HTTP-antwoord te schrijven. 

Als u vanuit deze functie naar een `out` Azure `queue` Storage-wachtrij `msg`wilt schrijven, voegt u een tekstbinding toe met de naam , zoals in de onderstaande code wordt weergegeven:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
In dit `msg` geval wordt gegeven aan de functie als een output argument. Voor `queue` een type moet u ook de `queueName` naam van de wachtrij opgeven en de *naam* van de `connection`Azure Storage-verbinding (van *local.settings.json)* opgeven in . 
::: zone-end  

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  

Zie [Azure Functions triggers and bindings concepts and](functions-triggers-bindings.md) queue [output configuration](functions-bindings-storage-queue-output.md#configuration)voor meer informatie over de details van bindingen.

## <a name="add-code-to-use-the-output-binding"></a>Code toevoegen om de uitvoerbinding te gebruiken

Met de wachtrijbinding die is opgegeven in *function.json,* u nu uw functie bijwerken om de `msg` uitvoerparameter te ontvangen en berichten naar de wachtrij te schrijven.

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

Houd er rekening mee dat u *geen* code hoeft te schrijven voor verificatie, een wachtrijverwijzing of het schrijven van gegevens. Al deze integratietaken worden handig verwerkt in de runtime van Azure Functions en de binding voor wachtrijuitvoer.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Het bericht weergeven in de azure-opslagwachtrij

U de wachtrij weergeven in de [Azure-portal](../storage/queues/storage-quickstart-queues-portal.md) of in de [Microsoft Azure Storage Explorer.](https://storageexplorer.com/) U ook de wachtrij weergeven in de Azure CLI, zoals beschreven in de volgende stappen:

1. Open het bestand *local.setting.json* van het functieproject en kopieer de waarde van de verbindingstekenreeks. Voer in een terminal- of opdrachtvenster de volgende `AZURE_STORAGE_CONNECTION_STRING`opdracht uit om een omgevingsvariabele met de naam , waarbij u uw specifieke verbindingstekenreeks plakt in plaats van `<MY_CONNECTION_STRING>`. (Deze omgevingsvariabele betekent dat u de verbindingstekenreeks niet hoeft `--connection-string` te leveren aan elke volgende opdracht met behulp van het argument.)

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[Powershell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="azure-cli"></a>[Azure-CLI](#tab/cmd)
    
    ```azurecli
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. (Optioneel) Gebruik [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) de opdracht om de opslagwachtrijen in uw account weer te geven. De uitvoer van deze opdracht `outqueue`moet een wachtrij met de naam bevatten, die is gemaakt toen de functie het eerste bericht naar die wachtrij schreef.
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. Gebruik [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) de opdracht om het bericht uit deze wachtrij te lezen, de eerste naam die u eerder hebt gebruikt bij het testen van de functie. De opdracht leest en verwijdert het eerste bericht uit de wachtrij. 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[Powershell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="azure-cli"></a>[Azure-CLI](#tab/cmd)
    
    ```azurecli
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    Dit script maakt gebruik van certutil om de base64-gecodeerde berichtenverzameling uit een lokaal temp-bestand te decoderen. Als er geen uitvoer is, probeert u het script te verwijderen `> NUL` om de certutil-uitvoer te stoppen, voor het geval er een fout optreedt. 
    
    ---
    
    Omdat de berichttekst is opgeslagen [base64 gecodeerd,](functions-bindings-storage-queue-trigger.md#encoding)moet het bericht worden gedecodeerd voordat het wordt weergegeven. Nadat u `az storage message get`bent uitgevoerd, wordt het bericht uit de wachtrij verwijderd. Als er slechts één `outqueue`bericht in is geweest, wordt een bericht niet opgehaald wanneer u deze opdracht een tweede keer uitvoert en in plaats daarvan een foutmelding krijgt.

## <a name="redeploy-the-project-to-azure"></a>Het project opnieuw implementeren in Azure

Nu u lokaal hebt geverifieerd dat de functie een bericht heeft geschreven naar de Azure Storage-wachtrij, u uw project opnieuw implementeren om het eindpunt dat op Azure wordt uitgevoerd bij te werken.

1. Gebruik in de map *LocalFunctionsProj* de [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) opdracht om`<APP_NAME>` het project opnieuw te implementeren, vervangen door de naam van uw app.

    ```
    func azure functionapp publish <APP_NAME>
    ```
    
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
