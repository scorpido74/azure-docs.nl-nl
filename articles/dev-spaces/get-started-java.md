---
title: 'Maak een Kubernetes dev-ruimte: Visual Studio code & Java'
services: azure-dev-spaces
author: stepro
ms.author: stephpr
ms.date: 09/26/2018
ms.topic: tutorial
description: In deze zelf studie leert u hoe u Azure dev Spaces en Visual Studio code kunt gebruiken om fouten op te sporen en snel een Java-toepassing te herhalen in azure Kubernetes service
keywords: Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, helm, service-net, service mesh routing, kubectl, K8S
manager: gwallace
ms.openlocfilehash: 47948b0ce4ac0cb9a78552891e42d7634335839e
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121761"
---
# <a name="create-a-kubernetes-dev-space-visual-studio-code-and-java-with-azure-dev-spaces"></a>Maak een Kubernetes dev-ruimte: Visual Studio code en Java met Azure dev Spaces

In deze handleiding leert u het volgende:

- Een Kubernetes-omgeving maakt in Azure die is geoptimaliseerd voor ontwikkeling - een _dev-ruimte_.
- Iteratief code ontwikkelt in containers met behulp van VS Code en de opdrachtregel.
- Uw code op een productieve manier ontwikkelen en testen in een teamomgeving.

> [!Note]
> Zie de sectie [probleem oplossing](troubleshooting.md) **Als u** op elk gewenst moment aan de slag gaat.

## <a name="install-the-azure-cli"></a>Azure CLI installeren
Azure Dev Spaces vereist minimale instellingen voor de lokale computer. De configuratie van uw ontwikkelomgeving wordt grotendeels opgeslagen in de cloud en kan worden gedeeld met andere gebruikers. Begin met het downloaden en uitvoeren van de [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="sign-in-to-azure-cli"></a>Aanmelden bij Azure CLI
Meld u aan bij Azure. Typ de volgende opdracht in een terminalvenster:

```cmd
az login
```

> [!Note]
> Als u geen abonnement op Azure hebt, kunt u een [gratis account](https://azure.microsoft.com/free) maken.

#### <a name="if-you-have-multiple-azure-subscriptions"></a>Als u meerdere Azure-abonnementen hebt...
U kunt uw abonnementen bekijken door het volgende uit te voeren: 

```cmd
az account list --output table
```

Zoek het abonnement met de *waarde True* voor *IsDefault*.
Als dit niet het abonnement is dat u wilt gebruiken, kunt u het standaardabonnement wijzigen:

```cmd
az account set --subscription <subscription ID>
```

## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Een Kubernetes-cluster maken dat is ingeschakeld voor Azure Dev Spaces

Maak vanaf de opdracht prompt de resource groep in een [regio die ondersteuning biedt voor Azure dev Spaces][supported-regions].

```cmd
az group create --name MyResourceGroup --location <region>
```

Maak een Kubernetes-cluster met de volgende opdracht:

```cmd
az aks create -g MyResourceGroup -n MyAKS --location <region> --disable-rbac --generate-ssh-keys
```

Het duurt een paar minuten om het cluster te maken.

### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Uw AKS-cluster configureren voor het gebruik van Azure Dev Spaces

Voer de volgende Azure CLI-opdracht in. Gebruik daarbij de resourcegroep die uw AKS-cluster en de naam van uw AKS-cluster bevat. Met de opdracht wordt uw cluster geconfigureerd met ondersteuning voor Azure Dev Spaces.

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

> [!IMPORTANT]
> Met het Azure Dev Spaces-configuratieproces wordt de eventueel aanwezige `azds`-naamruimte in het cluster verwijderd.

## <a name="get-kubernetes-debugging-for-vs-code"></a>Kubernetes-foutopsporing voor VS Code downloaden
Uitgebreide functies, zoals Kubernetes-foutopsporing, zijn beschikbaar voor .NET Core en Node.js-ontwikkelaars die VS Code gebruiken.

1. Installeer [VS Code](https://code.visualstudio.com/Download) als u dit nog niet hebt.
1. Download en installeer de [extensie VS Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Klik eenmaal op Installeren op de Microsoft Azure Marketplace-pagina van de extensie en nog eens in VS Code. 

Om in Java-toepassingen fouten te kunnen opsporen met Azure Dev Spaces, downloadt en installeert u de extensie [Java Debugger for Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) voor VS Code. Klik eenmaal op Installeren op de Microsoft Azure Marketplace-pagina van de extensie en nog eens in VS Code.

## <a name="create-a-web-app-running-in-a-container"></a>Een web-app maken die wordt uitgevoerd in een container

In deze sectie maakt u een Java-webtoepassing en gaat u deze uitvoeren in een container in Kubernetes.

### <a name="create-a-java-web-app"></a>Een Java-web-app maken
Down load code van GitHub door naar [https://github.com/Azure/dev-spaces](https://github.com/Azure/dev-spaces) te navigeren en **kloon of down load** te selecteren om de GitHub-opslag plaats naar uw lokale omgeving te downloaden. De code voor deze handleiding bevindt zich in `samples/java/getting-started/webfrontend`.

## <a name="preparing-code-for-docker-and-kubernetes-development"></a>Code voorbereiden voor de ontwikkeling van Docker en Kubernetes
U hebt nu een eenvoudige web-app die lokaal kan worden uitgevoerd. U gaat hier nu een container van maken door assets te maken waarmee de container van de app wordt gedefinieerd evenals hoe deze in Kubernetes wordt geïmplementeerd. Deze taak kan eenvoudig worden uitgevoerd met Azure Dev Spaces: 

1. Start VS Code en open de map `webfrontend`. (U kunt eventuele standaardprompts negeren om foutopsporingsassets toe te voegen of om het project te herstellen.)
1. Open de geïntegreerde terminal in VS Code (via het menu **Weergave > Geïntegreerde Terminal**).
1. Voer de volgende opdracht uit (controleer of **webfrontend** de huidige map is):

    ```cmd
    azds prep --public
    ```

Met de opdracht `azds prep` van Azure-CLI worden Docker- en Kubernetes-assets met standaardinstellingen gemaakt:
* In `./Dockerfile` wordt beschreven wat de containerinstallatiekopie van de app is, en hoe de broncode is opgebouwd en wordt uitgevoerd in de container.
* Met een [Helm-grafiek](https://docs.helm.sh) die zich onder `./charts/webfrontend` bevindt, wordt beschreven hoe de container in Kubernetes moet worden geïmplementeerd.

> [!TIP]
> De [Dockerfile-en helm-grafiek](how-dev-spaces-works.md#prepare-your-code) voor uw project wordt gebruikt door Azure dev Spaces om uw code te bouwen en uit te voeren, maar u kunt deze bestanden aanpassen als u wilt wijzigen hoe het project wordt gemaakt en uitgevoerd.

Op dit moment is het nog niet nodig om de volledige inhoud van deze bestanden te begrijpen. Wat u wel moet weten is dat **de dezelfde assets voor 'configuratie als code' in Docker en Kubernetes kunnen worden gebruikt van ontwikkeling tot productie, waardoor er meer consistentie tussen de verschillende omgevingen bestaat.**
 
Er wordt met de opdracht `./azds.yaml` ook een bestand met de naam `prep` gegenereerd. Dit is het configuratiebestand voor Azure Dev Spaces. Dit vormt een aanvulling op de Docker- en Kubernetes-artefacten met extra configuraties die herhaalbare ontwikkelingsmogelijkheden in Azure bieden.

## <a name="build-and-run-code-in-kubernetes"></a>Code schrijven en uitvoeren in Kubernetes
We gaan onze code uitvoeren! In het terminalvenster voert u deze opdracht uit vanuit de **hoofdcodemap**, webfrontend:

```cmd
azds up
```

Houd de uitvoer van de opdracht in de gaten. Er zijn meerdere zaken die u zullen opvallen tijdens de uitvoering:
- De broncode is gesynchroniseerd met de dev-ruimte in Azure.
- Er wordt een containerinstallatiekopie gemaakt in Azure, zoals is aangegeven door de Docker-assets in uw codemap.
- Er worden Kubernetes-objecten gemaakt die de containerinstallatiekopie gebruiken zoals wordt aangegeven in de Helm-grafiek in uw codemap.
- Er wordt informatie over het eindpunt of de eindpunten van de container weergegeven. In ons geval verwachten we een openbare HTTP-URL.
- Ervan uitgaande dat de fases hierboven goed worden afgerond, moet u nu `stdout`-uitvoer (en `stderr`) gaan zien terwijl de container wordt opgestart.

> [!Note]
> Deze stappen nemen de eerste keer dat de opdracht `up` wordt uitgevoerd, meer tijd in beslag, maar latere uitvoeringen zullen sneller verlopen.

### <a name="test-the-web-app"></a>De web-app testen
Scan de console-uitvoer voor informatie over de openbare URL die door de opdracht `up` is gemaakt. Het zal in deze vorm te zien zijn: 

```
(pending registration) Service 'webfrontend' port 'http' will be available at <url>
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
```

Bepaal de open bare URL voor de service in de uitvoer van de `up` opdracht. Het eindigt op `.azds.io`. In het bovenstaande voor beeld is de open bare URL `http://webfrontend.1234567890abcdef1234.eus.azds.io/`.

Open de open bare URL in een browser om uw web-app te bekijken. U ziet ook `stdout` en `stderr` uitvoer wordt gestreamd naar het Terminal venster *azds Trace* tijdens de interactie met uw web-app. U ziet ook de tracerings gegevens voor HTTP-aanvragen wanneer ze het systeem door lopen. Dit maakt het eenvoudiger voor u om complexe multi-service aanroepen tijdens de ontwikkeling bij te houden. De instrumentatie die wordt toegevoegd door dev Spaces biedt deze aanvraag tracering.

> [!Note]
> Naast de open bare URL kunt u de alternatieve `http://localhost:<portnumber>` URL gebruiken die wordt weer gegeven in de console-uitvoer. Als u de localhost-URL gebruikt, lijkt het misschien alsof de container lokaal wordt uitgevoerd, maar wordt deze feitelijk uitgevoerd in AKS. Azure dev Spaces maakt gebruik van de Kubernetes *-Poort-Forward-* functionaliteit om de localhost-poort toe te wijzen aan de container die in AKS wordt uitgevoerd. Dit vereenvoudigt het communiceren met de service vanaf uw lokale computer.

### <a name="update-a-content-file"></a>Een inhoudsbestand bijwerken
Azure Dev Spaces draait niet alleen om het ophalen van code die wordt uitgevoerd in Kubernetes. Het gaat er om dat u de codewijzigingen snel en iteratief toegepast kunt zien in een Kubernetes-omgeving in de cloud.

1. Druk in het terminalvenster op `Ctrl+C` (om `azds up` te stoppen).
1. Open `src/main/java/com/ms/sample/webfrontend/Application.java`en bewerk het begroetings bericht op [regel 19](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/webfrontend/src/main/java/com/ms/sample/webfrontend/Application.java#L19):

    ```java
    return "Hello from webfrontend in Azure!";
    ```

1. Sla het bestand op.
1. Voer `azds up` uit in het terminalvenster.

Met deze opdracht wordt de installatiekopie van de container opnieuw gebouwd en het Helm-diagram opnieuw geïmplementeerd. Vernieuw de browser om te zien of uw codewijzigingen zijn doorgevoerd in de actieve toepassing.

Er bestaat echter een nog *snellere methode* voor het ontwikkelen van code. Deze methode gaat u in de volgende sectie verkennen. 

## <a name="debug-a-container-in-kubernetes"></a>Fouten opsporen in een Kubernetes-container

In deze sectie gaat u VS Code gebruiken om direct fouten op te sporen in de container die in Azure wordt uitgevoerd. U leert ook hoe u een cyclus voor bewerken-uitvoeren-testen sneller kunt uitvoeren.

![](media/common/edit-refresh-see.png)

> [!Note]
> **Als u op enig moment niet verder kunt**, kunt u de [probleemoplossingssectie](troubleshooting.md) raadplegen of een opmerking op deze pagina plaatsen.

### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Foutopsporingsassets initialiseren met de VS Code-extensie
U moet eerst uw codeproject configureren, zodat de VS Code met onze dev-ruimte in Azure gaat communiceren. De VS Code-extensie voor Azure Dev Spaces bevat een helpopdracht om de foutopsporingsconfiguratie in te stellen. 

Open het **Opdrachtenpalet** (via het menu **Beeld | Opdrachtenpalet**), en gebruik automatisch aanvullen om te typen en deze opdracht te selecteren: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. 

Hiermee wordt de foutopsporingsconfiguratie voor Azure Dev Spaces toegevoegd onder de map `.vscode`. Deze opdracht moet niet worden verward met de opdracht `azds prep`, waarmee u het project configureert voor implementatie.

![](media/common/command-palette.png)

### <a name="select-the-azds-debug-configuration"></a>Selecteer de AZDS-foutopsporingsconfiguratie
1. Om de foutopsporingsweergave te openen, klikt u op het pictogram Foutopsporing in de **activiteitenbalk** van VS Code.
1. Selecteer **Launch Java Program (AZDS)** als de actieve foutopsporingsconfiguratie.

![](media/get-started-java/debug-configuration.png)

> [!Note]
> Als u geen Azure Dev Spaces-opdrachten ziet in het Opdrachtenpalet, controleert u of u de VS Code-extensie hebt geïnstalleerd voor Azure Dev Spaces. Zorg ervoor dat de werk ruimte die u in VS code hebt geopend, de map is die `azds.yaml`bevat.

### <a name="debug-the-container-in-kubernetes"></a>Fouten opsporen in de container in Kubernetes
Druk op **F5** om fouten in uw code op te sporen in Kubernetes.

Net als bij de opdracht `up` wordt code gesynchroniseerd met de ontwikkelomgeving en wordt een container gemaakt en geïmplementeerd in Kubernetes. Op dit moment is het foutopsporingsprogramma uiteraard gekoppeld aan de externe container.

> [!Tip]
> De status balk VS-code wordt oranje ingeschakeld, wat aangeeft dat het fout opsporingsprogramma is gekoppeld. Er wordt ook een klikbare URL weer gegeven, die u kunt gebruiken om uw toepassing te openen.

![](media/common/vscode-status-bar-url.png)

Stel een onderbrekingspunt in een codebestand aan serverzijde, bijvoorbeeld binnen de functie `greeting()` in het bronbestand `src/main/java/com/ms/sample/webfrontend/Application.java`. Als de browserpagina wordt vernieuwd, wordt het onderbrekingspunt bereikt.

U hebt volledige toegang tot foutopsporingsgegevens, net alsof de code lokaal wordt uitgevoerd. Denk hierbij aan de aanroep-stack, lokale variabelen en informatie over uitzonderingen, enzovoort.

### <a name="edit-code-and-refresh"></a>Code bewerken en vernieuwen
Maak een codewijziging terwijl het foutopsporingsprogramma actief is. Wijzig de begroeting bijvoorbeeld in `src/main/java/com/ms/sample/webfrontend/Application.java`. 

```java
public String greeting()
{
    return "I'm debugging Java code in Azure!";
}
```

Sla het bestand op en klik in het **deel venster acties voor fout opsporing**op de knop **opnieuw opstarten** .

![](media/common/debug-action-refresh.png)

In plaats van telkens als codewijzigingen zijn aangebracht een nieuwe containerinstallatiekopie opnieuw te bouwen en opnieuw te implementeren, wat vaak behoorlijk wat tijd kost, hercompileert Azure Dev Spaces incrementeel code binnen de bestaande container voor een snellere bewerkings-/foutopsporingslus.

Vernieuw de web-app in de browser. U ziet dat uw aangepaste bericht wordt weergegeven in de gebruikersinterface.

**U hebt nu een methode voor het snel doorvoeren van code en foutopsporing rechtstreeks in Kubernetes.** Hierna ziet u hoe u een tweede container kunt maken en aanroepen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Informatie over ontwikkelen van meerdere services](multi-service-java.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
