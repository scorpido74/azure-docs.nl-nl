---
title: Zelf studie-container groep activeren met Azure function
description: Een door HTTP geactiveerde Power shell-functie zonder server maken voor het automatiseren van het maken van Azure container instances
ms.topic: tutorial
ms.date: 09/20/2019
ms.custom: ''
ms.openlocfilehash: 49eb0721972a92f33bda2532367bc78280b6e655
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533378"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>Zelf studie: een Azure-functie activeren die door HTTP wordt geactiveerd om een container groep te maken

[Azure functions](../azure-functions/functions-overview.md) is een serverloze compute-service die scripts of code kan uitvoeren als reactie op diverse gebeurtenissen, zoals een HTTP-aanvraag, een timer of een bericht in een Azure Storage wachtrij.

In deze zelf studie maakt u een Azure-functie die een HTTP-aanvraag afneemt en de implementatie van een [container groep](container-instances-container-groups.md)activeert. In dit voor beeld ziet u de basis principes van het gebruik van Azure Functions om automatisch resources te maken in Azure Container Instances. Wijzig of breid het voor beeld uit voor complexere scenario's of andere gebeurtenis triggers. 

Procedures voor:

> [!div class="checklist"]
> * Gebruik Visual Studio code met de [Azure functions extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) om een eenvoudige, door http geactiveerde Power shell-functie te maken.
> * Schakel een identiteit in de functie-app in en geef deze machtigingen om Azure-resources te maken.
> * Wijzig en publiceer de Power shell-functie om de implementatie van een container groep met één container te automatiseren.
> * Controleer de door HTTP geactiveerde implementatie van de container.

> [!IMPORTANT]
> Power shell voor Azure Functions is momenteel beschikbaar als preview-versie. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functie worden mogelijk nog gewijzigd voordat de functie algemeen beschikbaar wordt.

## <a name="prerequisites"></a>Vereisten

Zie [uw eerste Power shell-functie in azure maken](../azure-functions/functions-create-first-function-powershell.md#prerequisites) voor vereisten voor het installeren en gebruiken van Visual Studio code met de Azure functions van uw besturings systeem.

Voor sommige stappen in dit artikel wordt gebruikgemaakt van de Azure CLI. U kunt de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken om deze stappen uit te voeren. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-a-basic-powershell-function"></a>Een eenvoudige Power shell-functie maken

Volg de stappen in [uw eerste Power shell-functie maken in azure](../azure-functions/functions-create-first-function-powershell.md) om een Power shell-functie te maken met de http-trigger sjabloon. Gebruik de standaard naam van de Azure-functie **http trigger**. Zoals in de Snelstartgids wordt weer gegeven, test u de functie lokaal en publiceert u het project naar een functie-app in Azure. Dit voor beeld is een eenvoudige, door HTTP geactiveerde functie waarmee een teken reeks wordt geretourneerd. In latere stappen in dit artikel wijzigt u de functie voor het maken van een container groep.

In dit artikel wordt ervan uitgegaan dat u het project publiceert met behulp van de naam *myfunctionapp*, in een Azure-resource groep, die automatisch wordt benoemd op basis van de naam van de functie-app (ook *myfunctionapp*). Vervang in latere stappen uw unieke naam van de functie-app en de naam van de resource groep.

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>Een door Azure beheerde identiteit inschakelen in de functie-app

Schakel nu een door het systeem toegewezen [beheerde identiteit](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#adding-a-system-assigned-identity) in de functie-app in. De Power shell-host waarop de app wordt uitgevoerd, kan automatisch worden geverifieerd met deze identiteit, waardoor functies kunnen worden uitgevoerd op Azure-Services waaraan de identiteit toegang heeft verleend. In deze zelf studie verleent u de beheerde identiteits machtigingen om resources te maken in de resource groep van de functie-app. 

Gebruik eerst de opdracht [AZ Group show][az-group-show] om de id van de resource groep van de functie-app op te halen en op te slaan in een omgevings variabele. In dit voor beeld wordt ervan uitgegaan dat u de opdracht uitvoert in een bash-shell.

```azurecli
rgID=$(az group show --name myfunctionapp --query id --output tsv)
```

Voer [AZ functionapp id app Assign][az-functionapp-identity-app-assign] toe om een lokale identiteit toe te wijzen aan de functie-app en een Inzender rol toe te wijzen aan de resource groep. Met deze rol kan de identiteit extra resources maken, zoals container groepen in de resource groep.

```azurecli
az functionapp identity assign \
  --name myfunctionapp \
  --resource-group myfunctionapp \
  --role contributor --scope $rgID
```

## <a name="modify-httptrigger-function"></a>De functie http trigger wijzigen

Wijzig de Power shell-code voor de functie **http trigger** om een container groep te maken. Zoek het volgende code blok in bestands `run.ps1` voor de functie. Met deze code wordt een naam waarde weer gegeven, als er een is door gegeven als een query reeks in de functie-URL:

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
[...]
```

Vervang deze code door het volgende voorbeeld blok. Als er een naam waarde wordt door gegeven in de query reeks, wordt deze naam gebruikt om een container groep te benoemen en te maken met behulp van de cmdlet [New-AzContainerGroup][new-azcontainergroup] . Zorg ervoor dat u de naam van de resource groep *myfunctionapp* vervangt door de naam van de resource groep voor uw functie-app:

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    New-AzContainerGroup -ResourceGroupName myfunctionapp -Name $name `
        -Image alpine -OsType Linux `
        -Command "echo 'Hello from an Azure container instance triggered by an Azure function'" `
        -RestartPolicy Never
    $body = "Started container group $name"
}
[...]
```

In dit voor beeld wordt een container groep gemaakt die bestaat uit één container instantie waarop de `alpine`-installatie kopie wordt uitgevoerd. De container voert één `echo` opdracht uit en eindigt vervolgens. In een echt wereld voorbeeld kunt u een of meer container groepen maken voor het uitvoeren van een batch-taak.
 
## <a name="test-function-app-locally"></a>De functie-app lokaal testen

Zorg ervoor dat de functie lokaal wordt uitgevoerd voordat u het functie-app-project opnieuw publiceert naar Azure. Zoals weer gegeven in de [Power shell-Snelstartgids](../azure-functions/functions-create-first-function-powershell.md), voegt u een lokaal onderbrekings punt in het Power shell-script en een `Wait-Debugger`-aanroep hierboven in. Zie [debug Power shell Azure functions Local](../azure-functions/functions-debug-powershell-local.md)voor hulp bij fout opsporing.


## <a name="republish-azure-function-app"></a>Azure-functie-app opnieuw publiceren

Nadat u hebt gecontroleerd of de functie correct wordt uitgevoerd op uw lokale computer, is het tijd om het project opnieuw te publiceren naar de bestaande functie-app in Azure.

> [!NOTE]
> Vergeet niet alle aanroepen naar `Wait-Debugger` te verwijderen voordat u uw functies naar Azure publiceert.

1. Open in Visual Studio code het opdracht palet. Zoek en selecteer `Azure Functions: Deploy to function app...`.
1. Selecteer de huidige werkmap die moet worden gezip en geïmplementeerd.
1. Selecteer het abonnement en vervolgens de naam van de bestaande functie-app (*myfunctionapp*). Bevestig dat u de vorige implementatie wilt overschrijven.

Nadat de functie-app is gemaakt en het implementatiepakket is toegepast, wordt er een melding weergegeven. Selecteer **uitvoer weer geven** in deze melding om de resultaten voor het maken en implementeren weer te geven, inclusief de Azure-resources die u hebt bijgewerkt.

## <a name="run-the-function-in-azure"></a>De functie uitvoeren in azure

Wanneer de implementatie is voltooid, kunt u de functie-URL ophalen. Gebruik bijvoorbeeld het gedeelte **functies van Azure:** in Visual Studio code om de URL van de **http trigger** -functie te kopiëren of de functie-URL in de [Azure Portal](../azure-functions/functions-create-first-azure-function.md#test-the-function)op te halen.

De functie-URL bevat een unieke code en heeft de volgende vorm:

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==
```

### <a name="run-function-without-passing-a-name"></a>De functie run zonder een naam door geven

Als eerste test voert u de `curl` opdracht uit en geeft u de functie-URL door zonder een `name` query reeks toe te voegen. Zorg ervoor dat u de unieke code van uw functie opneemt.

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M=="
```

De functie retourneert de status code 400 en de tekst `Please pass a name on the query string or in the request body`:

```
[...]
> GET /api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M== HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 400 
< content-length: 62
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:08:15 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Please pass a name on the query string or in the request body.
```

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>De functie run en de naam van een container groep door geven

Voer nu de `curl` opdracht uit door de naam van een container groep (*mycontainergroup*) toe te voegen als een query reeks `&name=mycontainergroup`:

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup"
```

De functie retourneert status code 200 en activeert het maken van de container groep:

```
[...]
> GET /api/HttpTrigger?ode=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 200 
< content-length: 28
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:15:30 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Started container group mycontainergroup
```

Controleer of de container is uitgevoerd met de opdracht [AZ container logs][az-container-logs] :

```azurecli
az container logs --resource-group myfunctionapp --name mycontainergroup
```

Voorbeelduitvoer:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u in deze zelf studie hebt gemaakt, niet meer nodig hebt, kunt u de opdracht [AZ Group delete][az-group-delete] uitvoeren om de resource groep en alle resources die deze bevat, te verwijderen. Met deze opdracht verwijdert u het containerregister dat u hebt gemaakt, evenals de actieve container en alle gerelateerde resources.

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een Azure-functie gemaakt die een HTTP-aanvraag afneemt en de implementatie van een container groep activeert. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Gebruik Visual Studio code met de Azure Functions extensie om een eenvoudige, door HTTP geactiveerde Power shell-functie te maken.
> * Schakel een identiteit in de functie-app in en geef deze machtigingen om Azure-resources te maken.
> * Wijzig de Power shell-functie code voor het automatiseren van de implementatie van een container groep met één container.
> * Controleer de door HTTP geactiveerde implementatie van de container.

Voor een gedetailleerd voor beeld voor het starten en bewaken van een taak met containers raadpleegt u het blog bericht [met de gebeurtenis gestuurde serverloze containers met Power shell Azure functions en Azure container instances](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b) en het bijbehorende [code voorbeeld](https://github.com/anthonychu/functions-powershell-run-aci).

Raadpleeg de [Azure functions-documentatie](/azure/azure-functions/) voor gedetailleerde richt lijnen voor het maken van Azure functions en het publiceren van een functions-project. 

<!-- IMAGES -->


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-functionapp-identity-app-assign]: /cli/azure/functionapp/identity#az-functionapp-identity-assign
[new-azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[az-container-logs]: /cli/azure/container#az-container-logs
