---
title: Meerdere afhankelijke services uitvoeren met node. js en VS code
services: azure-dev-spaces
ms.date: 11/21/2018
ms.topic: tutorial
description: Snelle Kubernetes-ontwikkeling met containers en microservices in Azure
keywords: Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, helm, service-net, service mesh routing, kubectl, K8S
ms.openlocfilehash: e9aca4415b9500a6f9667f7df54916d783367f22
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279598"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Ontwikkeling van meerdere services met Azure Dev Spaces

In deze zelfstudie leert u hoe u toepassingen met meerdere services ontwikkelt met Azure Dev Spaces, samen met enkele aanvullende voordelen van Azure Dev Spaces.

## <a name="call-a-service-running-in-a-separate-container"></a>Een service aanroepen die wordt uitgevoerd in een afzonderlijke container

In deze sectie maakt u een tweede service, `mywebapi`, en laat u deze aanroepen in `webfrontend`. Elke service wordt uitgevoerd in een afzonderlijke container. Vervolgens spoort u fouten op in beide containers.

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>Voorbeeldcode openen voor *mywebapi*
U moet de voorbeeld code voor `mywebapi` voor deze hand leiding in een map met de naam `samples` al hebben (als dat niet het geval is, gaat u naar https://github.com/Azure/dev-spaces en selecteert u **klonen of downloaden** om de GitHub-opslag plaats te downloaden.) De code voor deze sectie bevindt zich in `samples/nodejs/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>*mywebapi* uitvoeren
1. Open de map `mywebapi` in een *afzonderlijk VS Code-venster*.
1. Open het **Opdrachtenpalet** (via het menu **Beeld | Opdrachtenpalet**), en gebruik automatisch aanvullen om te typen en deze opdracht te selecteren: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. Deze opdracht moet niet worden verward met de opdracht `azds prep`, waarmee u het project configureert voor implementatie.
1. Druk op F5 en wacht tot de service is gebouwd en geïmplementeerd. U weet dat deze klaar is wanneer het bericht *Luis teren naar poort 80* wordt weer gegeven in de console voor fout opsporing.
1. Noteer de URL van het eindpunt. Deze ziet er ongeveer als volgt uit: `http://localhost:<portnumber>`. **Tip: de status balk VS-code wordt oranje en een klikbare URL weer gegeven.** Het lijkt misschien alsof de container lokaal wordt uitgevoerd, maar dat is niet zo. De container wordt uitgevoerd in de ontwikkelomgeving in Azure. Het localhost-adres is te zien omdat er nog geen openbare eindpunten zijn gedefinieerd in `mywebapi` en toegang daarom alleen mogelijk is binnen het Kubernetes-exemplaar. Voor uw gemak en om interactie met de privésessie mogelijk te maken vanaf de lokale computer wordt in Azure Dev Spaces een tijdelijke SSH-tunnel gemaakt naar de container die wordt uitgevoerd in Azure.
1. Wanneer `mywebapi` klaar is, opent u de browser naar het localhost-adres. U ziet nu een reactie van de `mywebapi`-service. (Hello from mywebapi).


### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Verzend een aanvraag van *webfrontend* naar *mywebapi*
Nu gaan we code schrijven in `webfrontend` waarmee een aanvraag wordt verzonden naar `mywebapi`.
1. Schakel over naar het VS Code-venster voor `webfrontend`.
1. Voeg deze coderegels toe boven aan `server.js`:
    ```javascript
    var request = require('request');
    ```

3. *Vervang* de code door de `/api`-GET-handler. Tijdens het verwerken van een aanvraag, wordt vervolgens een aanroep verzonden naar `mywebapi` en worden de resultaten van beide services geretourneerd.

    ```javascript
    app.get('/api', function (req, res) {
       request({
          uri: 'http://mywebapi',
          headers: {
             /* propagate the dev space routing header */
             'azds-route-as': req.headers['azds-route-as']
          }
       }, function (error, response, body) {
           res.send('Hello from webfrontend and ' + body);
       });
    });
    ```
   1. *Verwijder* de `server.close()`-regel aan het einde van `server.js`

In het vorige codevoorbeeld wordt de `azds-route-as`-header van de binnenkomende aanvraag doorgestuurd naar de uitgaande aanvraag. Later ziet u hoe dit teamleden helpt om samen te ontwikkelen.

### <a name="debug-across-multiple-services"></a>Foutopsporing in meerdere services
1. Op dit punt moet `mywebapi` nog steeds worden uitgevoerd met het bijgevoegde foutopsporingsprogramma. Als dit niet het geval is, drukt u op F5 in het `mywebapi`-project.
1. Stel een onderbrekings punt in op de standaard GET `/`-handler [op regel 8 van `server.js`](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/mywebapi/server.js#L8).
1. Stel in het `webfrontend`-project een onderbrekingspunt in vlak voordat een GET-aanvraag wordt verzonden naar `http://mywebapi`.
1. Druk op F5 in het `webfrontend`-project.
1. Open de web-app en analyseer de code in beide services. Als het goed is, wordt nu een bericht weergegeven dat is samengesteld uit de twee services: Hello from webfrontend en Hello from mywebapi.

### <a name="well-done"></a>Dat is dus gelukt.
U hebt nu een toepassing met meerdere containers waarin elke container afzonderlijk kan worden ontwikkeld en geïmplementeerd.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over teamontwikkeling in Dev Spaces](team-development-nodejs.md)
