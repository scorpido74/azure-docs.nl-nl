---
title: 'Running multiple dependent services: Java & Visual Studio Code'
services: azure-dev-spaces
ms.date: 11/21/2018
ms.topic: tutorial
description: Snelle Kubernetes-ontwikkeling met containers en microservices in Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
ms.openlocfilehash: 3fe19997ab54f02b6a5f029abbdb69d5ea6532f7
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325715"
---
# <a name="running-multiple-dependent-services-java-and-visual-studio-code-with-azure-dev-spaces"></a>Running multiple dependent services: Java and Visual Studio Code with Azure Dev Spaces

In deze zelfstudie leert u hoe u toepassingen met meerdere services ontwikkelt met Azure Dev Spaces, samen met enkele van de aanvullende voordelen van Azure Dev Spaces.

## <a name="call-a-service-running-in-a-separate-container"></a>Een service aanroepen die wordt uitgevoerd in een afzonderlijke container

In deze sectie maakt u een tweede service, `mywebapi`, en laat u deze aanroepen door `webfrontend`. Elke service wordt uitgevoerd in een afzonderlijke container. Vervolgens spoort u fouten op in beide containers.

![Meerdere containers](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Voorbeeldcode voor *mywebapi* downloaden
Omwille van de tijd downloaden we voorbeeldcode uit een GitHub-opslagplaats. Ga naar https://github.com/Azure/dev-spaces en selecteer **Clone or Download** om de GitHub-opslagplaats te downloaden. De code voor deze sectie bevindt zich in `samples/java/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>*mywebapi* uitvoeren
1. Open de map `mywebapi` in een *afzonderlijk VS Code-venster*.
1. Open het **Opdrachtenpalet** (via het menu **Beeld | Opdrachtenpalet**), en gebruik automatisch aanvullen om te typen en deze opdracht te selecteren: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.
1. Druk op F5 en wacht tot de service is gebouwd en geïmplementeerd. You'll know it's ready when a message similar to the below appears in the debug console:

    ```cmd
    2019-03-11 17:02:35.935  INFO 216 --- [           main] com.ms.sample.mywebapi.Application       : Started Application in 8.164 seconds (JVM running for 9.272)
    ```

1. De eindpunt-URL ziet er ongeveer uit als `http://localhost:<portnumber>`. **Tip: The VS Code status bar will turn orange and display a clickable URL.** Het lijkt misschien alsof de container lokaal wordt uitgevoerd, maar dat is niet zo. De container wordt uitgevoerd in onze ontwikkelomgeving in Azure. Het localhost-adres is te zien omdat er nog geen openbare eindpunten zijn gedefinieerd in `mywebapi` en toegang daarom alleen mogelijk is binnen het Kubernetes-exemplaar. Voor uw gemak en om interactie met de privésessie mogelijk te maken vanaf de lokale computer wordt in Azure Dev Spaces een tijdelijke SSH-tunnel gemaakt naar de container die wordt uitgevoerd in Azure.
1. Wanneer `mywebapi` klaar is, opent u de browser naar het localhost-adres.
1. Als alle stappen zijn voltooid, moet er een reactie van de `mywebapi`-service te zien zijn.

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Verzend een aanvraag van *webfrontend* naar *mywebapi*
Nu gaan we code schrijven in `webfrontend` waarmee een aanvraag wordt verzonden naar `mywebapi`.
1. Schakel over naar het VS Code-venster voor `webfrontend`.
1. *Voeg* de volgende `import`-instructies toe onder de `package`-instructie:

   ```java
   import java.io.*;
   import java.net.*;
   ```
1. *Vervang* de code voor de begroetingsmethode:

    ```java
    @RequestMapping(value = "/greeting", produces = "text/plain")
    public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
        URLConnection conn = new URL("http://mywebapi/").openConnection();
        conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
        {
            return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
        }
    }
    ```

In het vorige codevoorbeeld wordt de `azds-route-as`-header van de binnenkomende aanvraag doorgestuurd naar de uitgaande aanvraag. Later ziet u hoe dit teamleden helpt om samen te ontwikkelen.

### <a name="debug-across-multiple-services"></a>Foutopsporing in meerdere services
1. Op dit punt moet `mywebapi` nog steeds worden uitgevoerd met het bijgevoegde foutopsporingsprogramma. Als dit niet het geval is, drukt u op F5 in het `mywebapi`-project.
1. Set a breakpoint in the `index()` method of the `mywebapi` project, on [line 19 of `Application.java`](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java#L19)
1. Stel in het `webfrontend`-project een onderbrekingspunt in vlak voordat deze een GET-aanvraag verzendt naar `mywebapi`, en wel op de regel die begint met `try`.
1. Druk op F5 in het `webfrontend`-project (of start het foutopsporingsprogramma opnieuw als het op dat moment wordt uitgevoerd).
1. Roep de web-app aan en analyseer de code in beide services.
1. In de web-app wordt nu op de pagina About een bericht weergegeven dat is samengesteld uit de twee services: Hello from webfrontend and Hello from mywebapi.

### <a name="well-done"></a>Dat is dus gelukt.
U hebt nu een toepassing met meerdere containers waarin elke container afzonderlijk kan worden ontwikkeld en geïmplementeerd.


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over teamontwikkeling in Dev Spaces](team-development-java.md)
