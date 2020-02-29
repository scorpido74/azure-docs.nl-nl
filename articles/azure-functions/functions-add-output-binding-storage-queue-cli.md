---
title: Azure Functions verbinding maken met Azure Storage met behulp van opdracht regel Programma's
description: Informatie over het verbinden van Azure Functions met een Azure Storage wachtrij door een uitvoer binding toe te voegen aan het opdracht regel project.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: e3c37b368b723cc95302949baa8e85e2a8b621be
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201925"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Azure Functions verbinding maken met Azure Storage met behulp van opdracht regel Programma's

In dit artikel integreert u een Azure Storage wachtrij met het functie-en opslag account dat u in [de vorige Snelstartgids](functions-create-first-azure-function-azure-cli.md)hebt gemaakt. U verkrijgt deze integratie met behulp van een *uitvoer binding* waarmee gegevens worden geschreven van een HTTP-aanvraag naar een bericht in de wachtrij. In dit artikel worden geen extra kosten in rekening gebracht tot meer dan de enige USD cent van de vorige Snelstartgids. Zie [Azure functions triggers en bindingen](functions-triggers-bindings.md)voor meer informatie over bindingen.

## <a name="configure-your-local-environment"></a>Uw lokale omgeving configureren

Voordat u begint, moet u het artikel volt ooien, [Snelstartgids: een Azure functions-project maken vanaf de opdracht regel](functions-create-first-azure-function-azure-cli.md). Als u de resources aan het einde van dat artikel al hebt opgeruimd, kunt u de stappen opnieuw door lopen om de functie-app en gerelateerde resources in azure opnieuw te maken.

## <a name="retrieve-the-azure-storage-connection-string"></a>De Azure Storage ophalen connection string

Als u in de vorige Snelstartgids een functie-app hebt gemaakt in azure, hebt u ook een opslag account gemaakt. De connection string voor dit account wordt veilig opgeslagen in de app-instellingen in Azure. Door de instelling in het bestand *Local. settings. json* te downloaden, kunt u deze verbinding schrijven naar een opslag wachtrij in hetzelfde account wanneer u de functie lokaal uitvoert. 

1. Voer in de hoofdmap van het project de volgende opdracht uit en vervang `<APP_NAME>` door de naam van uw functie-app uit de vorige Snelstartgids. Met deze opdracht worden alle bestaande waarden in het bestand overschreven.

    ```
    func azure functionapp fetch-app-settings <APP_NAME>
    ```
    
1. Open *Local. settings. json* en zoek de waarde met de naam `AzureWebJobsStorage`, het opslag account Connection String. U gebruikt de naam `AzureWebJobsStorage` en de connection string in andere secties van dit artikel.

> [!IMPORTANT]
> Omdat *Local. settings. json* bevat geheimen die zijn gedownload van Azure, moet u dit bestand altijd uitsluiten van broncode beheer. Het *. gitignore* -bestand dat is gemaakt met een lokale functie project, sluit het bestand standaard uit.

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

## <a name="add-an-output-binding-definition-to-the-function"></a>Een definitie van een uitvoer binding toevoegen aan de functie

Hoewel een functie slechts één trigger kan hebben, kan deze meerdere invoer-en uitvoer bindingen hebben, waarmee u verbinding kunt maken met andere Azure-Services en-resources zonder aangepaste integratie code te schrijven. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
U declareert deze bindingen in het bestand *Function. json* in de map function. In de vorige Snelstartgids bevat uw *Function. json* -bestand in de map *HttpExample* twee bindingen in de verzameling `bindings`:  
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
Elke binding heeft ten minste een type, een richting en een naam. In het bovenstaande voor beeld is de eerste binding van het type `httpTrigger` met de richting `in`. Voor de `in` richting geeft `name` de naam op van een invoer parameter die wordt verzonden naar de functie wanneer deze wordt aangeroepen door de trigger.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
De tweede binding in de verzameling heeft de naam `res`. Deze `http` binding is een uitvoer binding (`out`) die wordt gebruikt voor het schrijven van het HTTP-antwoord. 

Als u vanuit deze functie naar een Azure Storage wachtrij wilt schrijven, voegt u een `out` binding van het type `queue` met de naam `msg`toe, zoals wordt weer gegeven in de onderstaande code:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
De tweede binding in de verzameling is van het type `http` met de richtings `out`, in welk geval de speciale `name` van `$return` aangeeft dat deze binding de retour waarde van de functie gebruikt in plaats van een invoer parameter op te geven.

Als u vanuit deze functie naar een Azure Storage wachtrij wilt schrijven, voegt u een `out` binding van het type `queue` met de naam `msg`toe, zoals wordt weer gegeven in de onderstaande code:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
De tweede binding in de verzameling heeft de naam `res`. Deze `http` binding is een uitvoer binding (`out`) die wordt gebruikt voor het schrijven van het HTTP-antwoord. 

Als u vanuit deze functie naar een Azure Storage wachtrij wilt schrijven, voegt u een `out` binding van het type `queue` met de naam `msg`toe, zoals wordt weer gegeven in de onderstaande code:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
In dit geval wordt `msg` als uitvoer argument aan de functie gegeven. Voor een `queue` type moet u ook de naam van de wachtrij in `queueName` opgeven en de *naam* opgeven van de Azure Storage verbinding (van *Local. settings. json*) in `connection`. 
::: zone-end  

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  

Zie voor meer informatie over de details van bindingen [Azure functions triggers en bindingen](functions-triggers-bindings.md) en de configuratie van de [wachtrij-uitvoer](functions-bindings-storage-queue-output.md#configuration).

## <a name="add-code-to-use-the-output-binding"></a>Code toevoegen voor het gebruik van de uitvoer binding

Als de wachtrij binding is opgegeven in *Function. json*, kunt u de functie nu bijwerken om de `msg` uitvoer parameter te ontvangen en berichten te schrijven naar de wachtrij.

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

Houd er rekening mee dat u *geen* code hoeft te schrijven voor verificatie, het ophalen van een wachtrij verwijzing of het schrijven van gegevens. Al deze integratie taken kunnen worden verwerkt in de Azure Functions runtime-en wachtrij-uitvoer binding.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Het bericht in de wachtrij van Azure Storage weer geven

U kunt de wachtrij weer geven in de [Azure Portal](../storage/queues/storage-quickstart-queues-portal.md) of in de [Microsoft Azure Storage Explorer](https://storageexplorer.com/). U kunt de wachtrij ook weer geven in de Azure CLI, zoals wordt beschreven in de volgende stappen:

1. Open het bestand *Local. setting. json* van het functie project en kopieer de Connection String waarde. Voer in een Terminal-of opdracht venster de volgende opdracht uit om een omgevings variabele met de naam `AZURE_STORAGE_CONNECTION_STRING`te maken en uw specifieke connection string in plaats van `<MY_CONNECTION_STRING>`te plakken. (Deze omgevings variabele houdt in dat u de connection string niet hoeft op te geven bij elke volgende opdracht met behulp van het argument `--connection-string`.)

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. Beschrijving Gebruik de [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) opdracht om de opslag wachtrijen in uw account weer te geven. De uitvoer van deze opdracht moet een wachtrij bevatten met de naam `outqueue`, die is gemaakt toen de functie het eerste bericht naar die wachtrij heeft geschreven.
    
    ```azure-cli
    az storage queue list --output tsv
    ```

1. Gebruik de opdracht [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) om het bericht uit deze wachtrij te lezen. dit moet de eerste naam zijn die u hebt gebruikt bij het testen van de functie. Met de opdracht leest en verwijdert u het eerste bericht uit de wachtrij. 

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    Dit script maakt gebruik van Certutil voor het decoderen van de met base64 gecodeerde berichten verzameling vanuit een lokaal tijdelijk bestand. Als er geen uitvoer is, probeert u `> NUL` te verwijderen uit het script om het onderdrukken van Certutil-uitvoer te stoppen, als er een fout is opgetreden. 
    
    ---
    
    Omdat de bericht tekst base64- [gecodeerd](functions-bindings-storage-queue-trigger.md#encoding)is opgeslagen, moet het bericht worden gedecodeerd voordat het wordt weer gegeven. Nadat u `az storage message get`hebt uitgevoerd, wordt het bericht uit de wachtrij verwijderd. Als er slechts één bericht in `outqueue`, wordt er geen bericht weer gegeven wanneer u deze opdracht een tweede keer uitvoert en wordt in plaats daarvan een fout opgetreden.

## <a name="redeploy-the-project-to-azure"></a>Het project opnieuw implementeren in azure

Nu u lokaal hebt gecontroleerd dat de functie een bericht naar de Azure Storage wachtrij heeft geschreven, kunt u uw project opnieuw implementeren om het eind punt bij te werken dat op Azure wordt uitgevoerd.

1. Gebruik in de map *LocalFunctionsProj* de opdracht [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) om het project opnieuw te implementeren, waarbij u`<APP_NAME>` vervangt door de naam van uw app.

    ```
    func azure functionapp publish <APP_NAME>
    ```
    
1. Net als in de vorige Snelstartgids gebruikt u een browser of krul om de opnieuw geïmplementeerde functie te testen.

    # <a name="browser"></a>[Browser](#tab/browser)
    
    Kopieer de volledige **invoke-URL** die wordt weer gegeven in de uitvoer van de opdracht publiceren naar een adres balk van de browser en voeg de query parameter `&name=Functions`toe. De browser moet vergelijk bare uitvoer weer geven als u de functie lokaal hebt uitgevoerd.

    ![De uitvoer van de functie wordt uitgevoerd op Azure in een browser](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # <a name="curl"></a>[Ezelsoor](#tab/curl)
    
    Voer [`curl`](https://curl.haxx.se/) uit met de **aanroepen-URL**en voeg de para meter `&name=Functions`toe. De uitvoer van de opdracht moet de tekst ' Hello functions ' zijn.
    
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
+ [Azure Functions triggers en bindingen](functions-triggers-bindings.md)

+ [Pagina met prijzen voor functies](https://azure.microsoft.com/pricing/details/functions/)

+ [Kosten voor verbruiks plan schatten](functions-consumption-costs.md) 
