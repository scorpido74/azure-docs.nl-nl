---
title: Zelfstudie - Containergroep activeren op Azure-functie
description: Een door HTTP geactiveerde, serverloze PowerShell-functie maken om het maken van Azure-containerexemplaren te automatiseren
ms.topic: tutorial
ms.date: 09/20/2019
ms.custom: ''
ms.openlocfilehash: 9dbb22a2449e4c41bff802ab827da4489fc7ffeb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78331022"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>Zelfstudie: Een Azure-functie met HTTP-geactiveerd eis gebruiken om een containergroep te maken

[Azure Functions](../azure-functions/functions-overview.md) is een serverloze compute-service die scripts of code kan uitvoeren als reactie op verschillende gebeurtenissen, zoals een HTTP-aanvraag, een timer of een bericht in een Azure Storage-wachtrij.

In deze zelfstudie maakt u een Azure-functie die een HTTP-aanvraag aanneemt en de implementatie van een [containergroep](container-instances-container-groups.md)activeert. In dit voorbeeld worden de basisprincipes weergegeven van het gebruik van Azure-functies om automatisch resources te maken in Azure Container Instances. Wijzig of breid het voorbeeld uit voor complexere scenario's of andere gebeurtenistriggers. 

Procedures voor:

> [!div class="checklist"]
> * Gebruik Visual Studio Code met de [Azure Functions-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) om een basisfunctie voor POWERShell te maken die door HTTP wordt geactiveerd.
> * Schakel een identiteit in de functie-app in en geef deze machtigingen om Azure-resources te maken.
> * Wijzig en publiceer de PowerShell-functie om de implementatie van een containergroep met één container te automatiseren.
> * Controleer de door HTTP geactiveerde implementatie van de container.

> [!IMPORTANT]
> PowerShell voor Azure-functies bevindt zich momenteel in preview. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="prerequisites"></a>Vereisten

Zie [Uw eerste functie in Azure maken](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-powershell#configure-your-environment) voor vereisten voor het installeren en gebruiken van Visual Studio Code met de Azure-functies op uw besturingssysteem.

In enkele stappen in dit artikel wordt de Azure CLI gebruikt. U de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken om deze stappen uit te voeren. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli-install].

## <a name="create-a-basic-powershell-function"></a>Een basisfunctie van PowerShell maken

Voer stappen uit in [Uw eerste PowerShell-functie maken in Azure](../azure-functions/functions-create-first-function-powershell.md) om een PowerShell-functie te maken met behulp van de HTTP-triggersjabloon. Gebruik de standaardAzure-functienaam **HttpTrigger**. Zoals in de quickstart wordt weergegeven, test u de functie lokaal en publiceert u het project naar een functie-app in Azure. Dit voorbeeld is een basisfunctie die door HTTP wordt geactiveerd en die een tekenreeks retourneert. In latere stappen in dit artikel wijzigt u de functie om een containergroep te maken.

In dit artikel wordt ervan uitgegaan dat u het project publiceert met de naam *myfunctionapp*, in een Azure-brongroep die automatisch wordt benoemd op basis van de naam van de functie-app (ook *myfunctionapp*). Vervang uw unieke functie-app naam en resource groep naam in latere stappen.

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>Een door Azure beheerde identiteit inschakelen in de functie-app

Schakel nu een door het systeem toegewezen [beheerde identiteit](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#add-a-system-assigned-identity) in uw functie-app in. De PowerShell-host die de app uitvoert, kan automatisch verifiëren met deze identiteit, waardoor functies acties kunnen uitvoeren op Azure-services waarvoor de identiteit toegang heeft gekregen. In deze zelfstudie verleent u de beheerde identiteitsmachtigingen om resources te maken in de resourcegroep van de functie-app. 

Gebruik eerst de opdracht show van de [AZ-groep][az-group-show] om de id van de resourcegroep van de functie-app op te halen en op te slaan in een omgevingsvariabele. In dit voorbeeld wordt ervan uitgegaan dat u de opdracht uitvoert in een Bash-shell.

```azurecli
rgID=$(az group show --name myfunctionapp --query id --output tsv)
```

Voer [de identiteit-app AZ-functieapp toe][az-functionapp-identity-app-assign] om een lokale identiteit toe te wijzen aan de functie-app en een bijdragende rol toe te wijzen aan de resourcegroep. Met deze rol kan de identiteit extra resources maken, zoals containergroepen in de resourcegroep.

```azurecli
az functionapp identity assign \
  --name myfunctionapp \
  --resource-group myfunctionapp \
  --role contributor --scope $rgID
```

## <a name="modify-httptrigger-function"></a>HttpTrigger wijzigen, functie

Wijzig de PowerShell-code voor de functie **HttpTrigger** om een containergroep te maken. Zoek `run.ps1` in het bestand voor de functie het volgende codeblok. Met deze code wordt een naamwaarde weergegeven, als deze wordt doorgegeven als een querytekenreeks in de functie-URL:

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
[...]
```

Vervang deze code door het volgende voorbeeldblok. Als hier een naamwaarde wordt doorgegeven in de querytekenreeks, wordt deze gebruikt om een containergroep te benoemen en te maken met de cmdlet [Nieuw-AzContainerGroup.][new-azcontainergroup] Zorg ervoor dat de naam *myfunctionapp* van de resourcegroep wordt vervangen door de naam van de resourcegroep voor uw functie-app:

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

In dit voorbeeld wordt een containergroep gemaakt `alpine` die bestaat uit één containerinstantie waarop de afbeelding wordt uitgevoerd. De container voert `echo` één opdracht uit en eindigt vervolgens. In een voorbeeld in de echte wereld u het maken van een of meer containergroepen activeren voor het uitvoeren van een batchtaak.
 
## <a name="test-function-app-locally"></a>Functie-app lokaal testen

Controleer of de functie lokaal correct wordt uitgevoerd voordat het functie-app-project opnieuw wordt gepubliceerd naar Azure. Zoals in de [PowerShell quickstart](../azure-functions/functions-create-first-function-powershell.md)wordt weergegeven, voegt u `Wait-Debugger` een lokaal breekpunt in het PowerShell-script en een aanroep erboven in. Zie [PowerShell Azure-functies debuggen](../azure-functions/functions-debug-powershell-local.md)lokaal voor foutopsporingsrichtlijnen.


## <a name="republish-azure-function-app"></a>Azure-functie-app opnieuw publiceren

Nadat u hebt geverifieerd dat de functie correct wordt uitgevoerd op uw lokale computer, is het tijd om het project opnieuw te publiceren naar de bestaande functie-app in Azure.

> [!NOTE]
> Vergeet niet om `Wait-Debugger` oproepen te verwijderen voordat u uw functies publiceert naar Azure.

1. Open in Visual Studio Code het opdrachtpalet. Zoeken naar `Azure Functions: Deploy to function app...`en selecteer .
1. Selecteer de huidige werkmap om te zippen en te implementeren.
1. Selecteer het abonnement en vervolgens de naam van de bestaande functie-app *(myfunctionapp).* Controleer of u de vorige implementatie wilt overschrijven.

Nadat de functie-app is gemaakt en het implementatiepakket is toegepast, wordt er een melding weergegeven. Selecteer **Uitvoer weergeven** in deze melding om de resultaten van maken en implementeren weer te geven, inclusief de Azure-resources die u hebt bijgewerkt.

## <a name="run-the-function-in-azure"></a>De functie uitvoeren in Azure

Nadat de implementatie is voltooid, krijgt u de URL van de functie. Gebruik bijvoorbeeld het gebied **Azure: Functions** in Visual Studio-code om de **HTTPTrigger-functie-URL** te kopiëren of de functie-URL in de [Azure-portal](../azure-functions/functions-create-first-azure-function.md#test-the-function)op te halen.

De functie-URL bevat een unieke code en is van het formulier:

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==
```

### <a name="run-function-without-passing-a-name"></a>Functie uitvoeren zonder een naam door te geven

Voer als eerste test `curl` de opdracht uit en geef `name` de functie-URL door zonder een querytekenreeks toe te passen. Zorg ervoor dat u de unieke code van uw functie opneemt.

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M=="
```

De functie retourneert statuscode `Please pass a name on the query string or in the request body`400 en de tekst:

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

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>Functie uitvoeren en de naam van een containergroep doorgeven

Voer de `curl` opdracht nu uit door de naam van een containergroep `&name=mycontainergroup`*(mycontainergroep)* toe te schrijven als querytekenreeks:

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup"
```

De functie retourneert statuscode 200 en activeert de creatie van de containergroep:

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

Controleer of de container is uitgevoerd met de opdracht [AZ-containerlogboeken:][az-container-logs]

```azurecli
az container logs --resource-group myfunctionapp --name mycontainergroup
```

Voorbeelduitvoer:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u geen van de resources die u in deze zelfstudie hebt gemaakt, niet meer nodig hebt, u de opdracht verwijderen van de [AZ-groep][az-group-delete] uitvoeren om de brongroep en alle bronnen die deze bevat te verwijderen. Met deze opdracht verwijdert u het containerregister dat u hebt gemaakt, evenals de actieve container en alle gerelateerde resources.

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Azure-functie gemaakt die een HTTP-aanvraag gebruikt en de implementatie van een containergroep activeert. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Gebruik Visual Studio Code met de Azure Functions-extensie om een basisfunctie voor POWERShell te maken die door HTTP wordt geactiveerd.
> * Schakel een identiteit in de functie-app in en geef deze machtigingen om Azure-resources te maken.
> * Wijzig de PowerShell-functiecode om de implementatie van een containergroep met één container te automatiseren.
> * Controleer de door HTTP geactiveerde implementatie van de container.

Zie de blogpost [Gebeurtenisgestuurde serverloze containers met PowerShell Azure-functies en Azure Container Instances en](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b) het bijbehorende [codevoorbeeld](https://github.com/anthonychu/functions-powershell-run-aci)voor een gedetailleerd voorbeeld om een gecontaineriseerde taak te starten en te bewaken.

Zie de [documentatie azure-functies](/azure/azure-functions/) voor gedetailleerde richtlijnen voor het maken van Azure-functies en het publiceren van een functieproject. 

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
