---
title: Team ontwikkeling met behulp van .NET core en Visual Studio
services: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: DrEsteban
ms.author: stevenry
ms.date: 12/09/2018
ms.topic: tutorial
description: Deze zelf studie laat zien hoe u Azure dev Spaces en Visual Studio kunt gebruiken om team ontwikkeling te doen op een .NET core-toepassing in azure Kubernetes service
keywords: 'Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, helm, service-net, service mesh routing, kubectl, K8S '
ms.openlocfilehash: b4520ce35807fb022fa39ae9b00347a27e192380
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78245050"
---
# <a name="team-development-using-net-core-and-visual-studio-with-azure-dev-spaces"></a>Team ontwikkeling met behulp van .NET core en Visual Studio met Azure dev Spaces

In deze zelfstudie leert u hoe een team van ontwikkelaars tegelijkertijd in hetzelfde Kubernetes-cluster kan samenwerken met behulp van Dev Spaces.

## <a name="learn-about-team-development"></a>Meer informatie over teamontwikkeling

Tot nu toe hebt u de code van de toepassing uitgevoerd alsof u de enige ontwikkelaar bent die werkt aan de app. In deze sectie leert u hoe teamontwikkeling wordt gestroomlijnd met Azure Dev Spaces:
* Laat een team ontwikkelaars in dezelfde omgeving werken door ze waar nodig in een gedeelde ontwikkelruimte of in individuele ontwikkelruimten te laten werken.
* Biedt ondersteuning om alle ontwikkelaars hun code geïsoleerd te laten herhalen, zonder dat ze het gevaar lopen dat ze andermans code breken.
* Test code end-to-end, voordat u de code doorvoert, zonder nagebootste code te maken of afhankelijkheden te simuleren.

### <a name="challenges-with-developing-microservices"></a>Uitdagingen bij het ontwikkelen van microservices
Momenteel is uw voorbeeldtoepassing niet complex. Maar in de pratijk krijgt u bij het ontwikkelen al snel te maken met uitdagingen wanneer u meer services toevoegt en het ontwikkelteam groeit.

* Uw ontwikkelcomputer heeft mogelijk niet voldoende resources om elke service die u nodig hebt onmiddellijk uit te voeren.
* Sommige services moeten wellicht openbaar bereikbaar zijn. Een service moet bijvoorbeeld een eindpunt hebben dat op een webhook reageert.
* Als u een subset met services wilt uitvoeren, moet u de volledige afhankelijkheidshiërarchie tussen al uw services kennen. Het kan lastig zijn om deze hiërarchie vast te stellen, met name als het aantal services toeneemt.
* Sommige ontwikkelaars maken gebruik van het simuleren of nabootsen van veel van hun serviceafhankelijkheden. Deze aanpak kan helpen, maar dergelijke nabootsingen zijn al gauw van invloed op de ontwikkelingskosten. Bovendien kan deze aanpak ertoe leiden dat uw ontwikkelomgeving er compleet anders uitziet dan de productieomgeving, waardoor er subtiele foutjes kunnen optreden.
* Een direct gevolg is dat het testen van integratie op welke manier dan ook moeilijk wordt. De integratie kan alleen realistisch worden getest na een doorvoering, wat betekent dat problemen pas later in de ontwikkelcyclus zichtbaar worden.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>In een gedeelde ontwikkelruimte werken
Met Azure Dev Spaces kunt u een *gedeelde* ontwikkelruimte instellen in Azure. Elke ontwikkelaar kan zich richten op het eigen deel van de toepassing, en kan *vooraf doorgevoerde code* iteratief doorvoeren in een ontwikkelruimte die alle andere services en cloudresources al bevat waarvan de scenario’s afhankelijk zijn. Afhankelijkheden zijn altijd up-to-date en ontwikkelaars werken op een manier die de productie weerspiegelt.

### <a name="work-in-your-own-space"></a>Werken in uw eigen ruimte
Terwijl u code ontwikkelt voor uw service en voordat u klaar bent om deze uit te geven, is de codestatus meestal niet in orde. U bent de code nog steeds iteratief aan het vormen en testen, en aan het experimenten met oplossingen. Azure Dev Spaces biedt het concept van een **ruimte**, waardoor u geïsoleerd kunt werken, zonder dat u de code van teamleden per ongeluk breekt.

## <a name="use-dev-spaces-for-team-development"></a>Dev Spaces gebruiken voor teamontwikkeling
We demonstreren deze ideeën aan de hand van een concreet voorbeeld met behulp van onze *webfrontend* -> *mywebapi*-voorbeeldtoepassing. We gebruiken een scenario waarin een ontwikkelaar, Scott, een wijziging moet aanbrengen in de *mywebapi*-service, en in die service *alleen*. De *webfrontend* hoeft niet te worden gewijzigd als onderdeel van de door Scott uitgevoerde update.

_Zonder_ Dev Spaces zou Scott maar over een paar manieren beschikken om de update te ontwikkelen en te testen, en geen van alle is daarvoor ideaal:
* ALLE onderdelen lokaal uitvoeren, waarvoor een krachtigere ontwikkelmachine waarop Docker is geïnstalleerd en mogelijk MiniKube is vereist.
* ALLE onderdelen uitvoeren in een geïsoleerde naamruimte in het Kubernetes-cluster. Aangezien *webfrontend* niet wordt gewijzigd, is gebruik van een geïsoleerde naamruimte een verspilling van clusterresources.
* ALLEEN *mywebapi* uitvoeren en handmatige REST-aanroepen doen om te testen. Hiermee wordt niet de volledige end-to-end-stroom getest.
* Op ontwikkeling gerichte code toevoegen aan de *webfrontend* waarmee de ontwikkelaar aanvragen kan verzenden naar een ander exemplaar van *mywebapi*. Het toevoegen van deze code maakt de *webfrontend*-service ingewikkelder.

### <a name="set-up-your-baseline"></a>De basislijn instellen
We moeten eerst een basislijn voor onze services implementeren. Deze implementatie vertegenwoordigt de laatst bekende goed werkende situatie. Daardoor kunt u eenvoudig het gedrag van uw lokale code vergelijken met de ingecheckte versie. Vervolgens maakt u een onderliggende ruimte op basis van deze basislijn zodat we onze wijzigingen van *mywebapi* kunnen testen binnen de context van de grotere toepassing.

1. De [Dev Spaces-voorbeeldtoepassing](https://github.com/Azure/dev-spaces) klonen: `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. De externe branch *azds_updates* uitchecken: `git checkout -b azds_updates origin/azds_updates`
1. Sluit alle F5-foutopsporingssessies voor beide services, maar houd de projecten open in de bijbehorende Visual Studio-vensters.
1. Schakel over naar het Visual Studio-venster met het _mywebapi_-project.
1. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Properties**.
1. Selecteer aan de linkerkant het tabblad **Debug** om de Azure Dev Spaces-instellingen weer te geven.
1. Selecteer **Wijziging** om de ruimte te maken die wordt gebruikt wanneer u F5 of Ctrl + F5 gebruikt in de service.
1. Selecteer **\<Nieuwe ruimte maken...\>** in de vervolgkeuze lijst Ruimte.
1. Zorg ervoor dat de bovenliggende ruimte is ingesteld op **\<Geen\>** en voer als naam van ruimte **dev** in. Klik op OK.
1. Druk op Ctrl + F5 om _mywebapi_ uit te voeren zonder het gekoppelde foutopsporingsprogramma.
1. Schakel over naar het Visual Studio-venster met het _webfrontend_-project en druk op Ctrl+F5 om dit ook uit te voeren.

> [!Note]
> Soms is het noodzakelijk om de browser te vernieuwen nadat de webpagina de eerste keer wordt uitgevoerd na Ctrl+F5.

> [!TIP]
> De bovenstaande stappen stellen handmatig een basislijn in, maar de teams kunnen het beste CI/CD gebruiken om automatisch uw basislijn up-to-date te houden met doorgevoerde code.
>
> Bekijk onze [handleiding voor het instellen van CI/CD met Azure DevOps](how-to/setup-cicd.md) om een werkstroom te maken die vergelijkbaar is met het volgende diagram.
>
> ![Voorbeeld van CI/CD-diagram](media/common/ci-cd-complex.png)

Iedereen die de openbare URL opent en naar de web-app navigeert, roept het codepad aan dat u hebt geschreven en dat actief is in beide services met behulp van de standaard _dev_-ruimte. Stel dat u wilt verdergaan met het ontwikkelen van *mywebapi*. Hoe kunt u dit doen zonder andere ontwikkelaars te onderbreken die de ontwikkelomgeving ook gebruiken? Hiervoor moet u uw eigen ruimte instellen.

### <a name="create-a-new-dev-space"></a>Een nieuwe ontwikkelomgeving maken
U kunt vanuit Visual Studio extra ruimten maken die worden gebruikt wanneer u F5 of Ctrl+F5 gebruikt voor de service. U kunt een ruimte elke gewenste naam geven en u kunt vrij bepalen wat deze betekent (bijvoorbeeld _sprint4_ of _demo_).

Doe het volgende om een nieuwe ruimte te maken:
1. Schakel over naar het Visual Studio-venster met het *mywebapi*-project.
2. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Properties**.
3. Selecteer aan de linkerkant het tabblad **Debug** om de Azure Dev Spaces-instellingen weer te geven.
4. Van hieruit kunt u het cluster en/of de ruimte maken die wordt gebruikt wanneer u F5 of Ctrl+F5 gebruikt. *Zorg ervoor dat de Azure Dev-ruimte is geselecteerd die u eerder hebt gemaakt*.
5. Selecteer **\<Nieuwe ruimte maken...\>** in de vervolgkeuze lijst Ruimte.

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. Stel in het dialoogvenster **Ruimte toevoegen** de bovenliggende ruimte in op **dev** en voer een naam in voor de nieuwe ruimte. U kunt uw naam gebruiken voor de nieuwe ruimte (bijvoorbeeld Scott), zodat het duidelijk is dat dit de ruimte is waarin u werkt. Klik op **OK**.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. Nu zijn uw AKS-cluster en Nieuwe ruimte geselecteerd op de pagina met projecteigenschappen.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Voorbeeldcode voor *mywebapi* bijwerken

1. Maak in het *mywebapi*-project een codewijziging in de methode `string Get(int id)` in het bestand `Controllers/ValuesController.cs`. Dit doet u als volgt:
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Stel een onderbrekingspunt in dit bijgewerkte codeblok in (misschien hebt u al eerder een onderbrekingspunt ingesteld).
3. Met F5 wordt de _mywebapi_-service in het cluster gestart met de geselecteerde ruimte. De geselecteerde ruimte is in dit geval _scott_.

Hier ziet u een diagram waarin u de werking van de verschillende ruimtes kunt zien. Het paarse pad laat een aanvraag zien via de _dev_-ruimte. Dit is het standaardpad dat wordt gebruikt als er geen ruimte is toegevoegd aan de URL. Het roze pad laat een aanvraag zien via de _dev/scott_-ruimte.

![](media/common/Space-Routing.png)

Deze ingebouwde mogelijkheid van Azure Dev Spaces stelt u in staat om code end-to-end te testen in een gedeelde omgeving zonder dat ontwikkelaars de volledige groep services in hun ruimte opnieuw hoeven te maken. Voor deze routering moeten doorgifteheaders worden doorgestuurd in uw app-code, zoals uitgelegd in de vorige stap van deze handleiding.

### <a name="test-code-running-in-the-_devscott_-space"></a>Code testen die wordt uitgevoerd in de _dev/scott_-ruimte
Als u de nieuwe versie van *mywebapi* wilt testen samen met *webfrontend*, opent u de URL van het openbare toegangspunt voor *webfrontend* in de browser (bijvoorbeeld http://dev.webfrontend.123456abcdef.eus.azds.io)) en gaat u naar de pagina About. Als het goed is, ziet u nu het oorspronkelijke bericht: Hello from webfrontend and Hello from mywebapi.

Voeg nu het gedeelte scott.s. toe onderdeel van de URL, zodat er iets zoals http\://scott.s.dev.webfrontend.123456abcdef.eus.azds.io wordt gelezen en de browser vernieuwen. Het onderbrekingspunt dat u hebt ingesteld in het *mywebapi*-project wordt nu gevonden. Klik op F5 om door te gaan. In de browser ziet u nu het nieuwe bericht: Hello from webfrontend and mywebapi now says something new. Dit komt omdat het pad naar de bijgewerkte code in *mywebapi* wordt uitgevoerd in de _dev/scott_-ruimte.

Zodra u een _dev_-ruimte hebt die altijd de meest recente wijzigingen bevat en ervan uitgaande dat uw toepassing is ontworpen om te profiteren van de DevSpace-routering op basis van ruimte, zoals beschreven in dit gedeelte van de zelfstudie, wordt het in het ideale geval gemakkelijk om te zien hoe Dev Spaces aanzienlijk kan helpen bij het testen van nieuwe functies in de context van de grotere toepassing. In plaats van dat u _alle_ services naar uw privéruimte moet implementeren, kunt u een privéruimte maken die is afgeleid van _dev_, en alleen de services aanroepen waaraan u daadwerkelijk werkt. De routeringsinfrastructuur van Dev Spaces zorgt voor de rest door alle services die deze kan vinden buiten uw privéruimte, terwijl de standaardwaarden terug worden gezet naar de meest recent uitgevoerde versie in de _dev_-ruimte. En nog beter: _meerdere_ ontwikkelaars kunnen actief verschillende services op hetzelfde moment in hun eigen ruimte ontwikkelen zonder elkaar te onderbreken.

### <a name="well-done"></a>Dat is dus gelukt.
U hebt de introductiehandleiding voltooid! U hebt geleerd hoe u:

> [!div class="checklist"]
> * Azure Dev Spaces instellen met een beheerd Kubernetes-cluster in Azure.
> * Iteratief code ontwikkelen in containers.
> * Twee afzonderlijke services ontwikkelen en de DNS-servicedetectie van Kubernetes gebruiken om een andere service aan te roepen.
> * Uw code op een productieve manier ontwikkelen en testen in een teamomgeving.
> * Zorg voor een basislijn van de functionaliteit van Dev-spaties van om eenvoudig geïsoleerde wijzigingen te testen in de context van een grotere microservicetoepassing

Nu u Azure Dev Spaces hebt verkend, kunt u [uw dev-ruimte delen met teamleden](how-to/share-dev-spaces.md) en hen laten zien hoe eenvoudig het is om samen te werken.

## <a name="clean-up"></a>Opruimen
Als u een exemplaar van Azure Dev Spaces in een cluster volledig wilt verwijderen, waaronder alle ontwikkelruimtes en de actieve services die erin worden uitgevoerd, gebruikt u de opdracht `az aks remove-dev-spaces`. Houd er rekening mee dat deze actie niet kan worden teruggedraaid. U kunt later opnieuw ondersteuning voor Azure Dev Spaces toevoegen aan het cluster, maar u moet dan helemaal opnieuw beginnen. Uw oude services en ruimtes worden niet hersteld.

In het volgende voorbeeld worden de Azure Dev Spaces-controllers in uw actieve abonnement vermeld en wordt vervolgens de Azure Dev Spaces-controller die is gekoppeld aan het AKS-cluster 'myaks' in de resourcegroep 'myaks-rg' verwijderd.

```cmd
azds controller list
```

```azurecli
az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
