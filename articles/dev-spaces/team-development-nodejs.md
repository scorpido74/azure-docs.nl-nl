---
title: Teamontwikkeling met behulp van Node.js en Visual Studio Code
services: azure-dev-spaces
ms.date: 07/09/2018
ms.topic: tutorial
description: In deze zelfstudie ziet u hoe u Azure Dev Spaces en Visual Studio Code gebruikt voor teamontwikkeling in een Node.js-toepassing in Azure Kubernetes Service
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, servicemesh, servicemeshroutering, kubectl, k8s '
ms.openlocfilehash: aa168921f212f96f6e40ed062d2665e49202e86c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87013589"
---
# <a name="team-development-using-nodejs-and-visual-studio-code-with-azure-dev-spaces"></a>Teamontwikkeling met behulp van Node.js en Visual Studio Code met Azure Dev Spaces

In deze zelfstudie leert u hoe een team van ontwikkelaars tegelijkertijd in hetzelfde Kubernetes-cluster kan samenwerken met behulp van Dev Spaces.

## <a name="learn-about-team-development"></a>Meer informatie over teamontwikkeling
Tot nu toe hebt u de code van de toepassing uitgevoerd alsof u de enige ontwikkelaar bent die werkt aan de app. In deze sectie leert u hoe teamontwikkeling wordt gestroomlijnd met Azure Dev Spaces:
* Laat een team ontwikkelaars in dezelfde omgeving werken door ze waar nodig in een gedeelde ontwikkelruimte of in individuele ontwikkelruimten te laten werken.
* Biedt ondersteuning om alle ontwikkelaars hun code geïsoleerd te laten herhalen, zonder dat ze het gevaar lopen dat ze andermans code breken.
* Test code end-to-end, voordat u de code doorvoert, zonder nagebootste code te maken of afhankelijkheden te simuleren.

### <a name="challenges-with-developing-microservices"></a>Uitdagingen bij het ontwikkelen van microservices
Momenteel is uw voorbeeldcode niet erg complex. Maar in de pratijk krijgt u bij het ontwikkelen al snel te maken met uitdagingen wanneer u meer services toevoegt en het ontwikkelteam groeit. Alles lokaal uitvoeren voor ontwikkeling is dan misschien niet zo realistisch.

* Uw ontwikkelcomputer heeft mogelijk niet voldoende resources om elke service die u nodig hebt onmiddellijk uit te voeren.
* Sommige services moeten wellicht openbaar bereikbaar zijn. Een service moet bijvoorbeeld een eindpunt hebben dat op een webhook reageert.
* Als u een subset met services wilt uitvoeren, moet u de volledige afhankelijkheidshiërarchie tussen al uw services kennen. Het kan lastig zijn om dat vast te stellen, met name als het aantal services toeneemt.
* Sommige ontwikkelaars maken gebruik van het simuleren of nabootsen van veel van hun serviceafhankelijkheden. Deze aanpak kan helpen, maar dergelijke nabootsingen zijn al gauw van invloed op de ontwikkelingskosten. Bovendien kan deze aanpak ertoe leiden dat uw ontwikkelomgeving er compleet anders uitziet dan de productieomgeving, waardoor er subtiele foutjes kunnen binnensluipen.
* Een direct gevolg is dat het testen van integratie op welke manier dan ook moeilijk wordt. De integratie kan alleen realistisch worden getest na een doorvoering, wat betekent dat problemen pas later in de ontwikkelcyclus zichtbaar worden.

    ![Een afbeelding met de complexiteit van integratietests door de relaties tussen een app-service en de bijbehorende afhankelijkheden te illustreren.](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>In een gedeelde ontwikkelruimte werken
Met Azure Dev Spaces kunt u een *gedeelde* ontwikkelruimte instellen in Azure. Elke ontwikkelaar kan zich richten op het eigen deel van de toepassing, en kan *vooraf doorgevoerde code* iteratief doorvoeren in een ontwikkelruimte die alle andere services en cloudresources al bevat waarvan de scenario’s afhankelijk zijn. Afhankelijkheden zijn altijd up-to-date en ontwikkelaars werken op een manier die de productie weerspiegelt.

### <a name="work-in-your-own-space"></a>Werken in uw eigen ruimte
Terwijl u code ontwikkelt voor uw service en voordat u klaar bent om deze uit te geven, is de codestatus meestal niet in orde. U bent de code nog steeds iteratief aan het vormen en testen, en aan het experimenten met oplossingen. Azure Dev Spaces biedt het concept van een **ruimte**, waardoor u geïsoleerd kunt werken, zonder dat u de code van teamleden per ongeluk breekt.

## <a name="use-dev-spaces-for-team-development"></a>Dev Spaces gebruiken voor teamontwikkeling
We demonstreren deze ideeën aan de hand van een concreet voorbeeld met behulp van onze *webfrontend* -> *mywebapi*-voorbeeldtoepassing. We gebruiken een scenario waarin een ontwikkelaar, Scott, een wijziging moet aanbrengen in de *mywebapi*-service, en in die service *alleen*. De *webfrontend* hoeft niet te worden gewijzigd als onderdeel van de door Scott uitgevoerde update.

_Zonder_ Dev Spaces zou Scott maar over een paar manieren beschikken om de update te ontwikkelen en te testen, en geen van alle is daarvoor ideaal:
* ALLE onderdelen lokaal uitvoeren. Hiervoor moet een krachtigere ontwikkelmachine met Docker worden geïnstalleerd, en wellicht ook MiniKube.
* ALLE onderdelen uitvoeren in een geïsoleerde naamruimte in de Kubernetes-cluster. Aangezien de *webfrontend* niet wordt gewijzigd, is dit een verspilling van clusterresources.
* ALLEEN *mywebapi* uitvoeren en handmatige REST-aanroepen doen om te testen. Hiermee wordt niet de volledige end-to-end-stroom getest.
* Op ontwikkeling gerichte code toevoegen aan de *webfrontend* waarmee de ontwikkelaar aanvragen kan verzenden naar een ander exemplaar van *mywebapi*. Dat maakt de *webfrontend*-service ingewikkelder.

### <a name="set-up-your-baseline"></a>De basislijn instellen
We moeten eerst een basislijn voor onze services implementeren. Deze implementatie vertegenwoordigt de laatst bekende goed werkende situatie. Daardoor kunt u eenvoudig het gedrag van uw lokale code vergelijken met de ingecheckte versie. Vervolgens maakt u een onderliggende ruimte op basis van deze basislijn zodat we onze wijzigingen van *mywebapi* kunnen testen binnen de context van de grotere toepassing.

1. De [Dev Spaces-voorbeeldtoepassing](https://github.com/Azure/dev-spaces) klonen: `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. De externe branch *azds_updates* uitchecken: `git checkout -b azds_updates origin/azds_updates`
1. Selecteer de _dev_-ruimte: `azds space select --name dev`. Wanneer u wordt gevraagd een bovenliggende dev-ruimte te selecteren, selecteert u _\<none\>_ .
1. Navigeer naar de _mywebapi_-map en voer deze opdracht uit: `azds up -d`
1. Navigeer naar de _webfrontend_-map en voer deze opdracht uit: `azds up -d`
1. Voer `azds list-uris` uit om het openbare eindpunt voor de _webfrontend_ weer te geven

> [!TIP]
> De bovenstaande stappen stellen handmatig een basislijn in, maar de teams kunnen het beste CI/CD gebruiken om automatisch uw basislijn up-to-date te houden met toegewezen code.
>
> Bekijk onze [handleiding voor het instellen van CI/CD met Azure DevOps](how-to/setup-cicd.md) om een werkstroom te maken die vergelijkbaar is met het volgende diagram.
>
> ![Voorbeeld van CI/CD-diagram](media/common/ci-cd-complex.png)

Op dit punt aangekomen zou uw basislijn moeten worden uitgevoerd. Voer de opdracht `azds list-up --all` uit en de uitvoer is vergelijkbaar met de volgende uitvoer:

```
$ azds list-up --all

Name                          DevSpace  Type     Updated  Status
----------------------------  --------  -------  -------  -------
mywebapi                      dev       Service  3m ago   Running
mywebapi-56c8f45d9-zs4mw      dev       Pod      3m ago   Running
webfrontend                   dev       Service  1m ago   Running
webfrontend-6b6ddbb98f-fgvnc  dev       Pod      1m ago   Running
```

In de kolom DevSpace wordt aangegeven dat beide services in een ruimte met de naam _dev_ worden uitgevoerd. Iedereen die de openbare URL opent en naar de web-app navigeert, roept het ingecheckte codepad aan dat in beide services wordt uitgevoerd. Stel nu dat u door wilt gaan met de ontwikkeling van _mywebapi_. Hoe kunt u wijzigingen aanbrengen in de code en die testen zonder andere ontwikkelaars te onderbreken die dezelfde ontwikkelruimte gebruiken? Hiervoor moet u uw eigen ruimte instellen.

### <a name="create-a-dev-space"></a>Een ontwikkelruimte maken
Om uw eigen versie van _mywebapi_ uit te voeren in een andere ruimte dan _dev_, kunt u uw eigen ruimte maken met de volgende opdracht:

```cmd
azds space select --name scott
```

Selecteer _dev_ als de **bovenliggende ontwikkelruimte** als u daarom wordt gevraagd. Dit betekent dat onze nieuwe ruimte, _dev/scott_, wordt afgeleid van de _dev_-ruimte. Binnenkort zien we hoe dit ons helpt met testen.

Ervan uitgaande dat deze inleiding nog steeds hypothetisch is, hebben we voor de nieuwe ruimte de naam _scott_ gebruikt, zodat collega's kunnen nagaan wie daarin aan het werk is. U kunt er elke naam aan geven, met de nodige flexibiliteit wat betreft de betekenis ervan, zoals _sprint4_ of _demo_. Hoe het ook zij, _dev_ fungeert als basislijn voor alle ontwikkelaars die aan een onderdeel van deze toepassing werken:

![Een diagram dat een eenvoudige ontwikkelruimte weergeeft.](media/common/ci-cd-space-setup.png)

Voer de opdracht `azds space list` uit om een lijst te krijgen van alle ruimten in de ontwikkelomgeving. In de kolom _Geselecteerd_ wordt aangegeven welke ruimte u momenteel hebt geselecteerd (true/false). In uw geval werd de ruimte met de naam _cott_ automatisch geselecteerd toen die werd gemaakt. U kunt op elk moment een andere ruimte selecteren met de opdracht `azds space select`.

Laten we eens kijken hoe dat in zijn werk gaat.

### <a name="make-a-code-change"></a>Een code wijzigen
Ga naar het VS Code-venster voor `mywebapi` en wijzig de code van de standaard-GET `/`-handler in `server.js`, bijvoorbeeld:

```javascript
app.get('/', function (req, res) {
    res.send('mywebapi now says something new');
});
```

### <a name="run-the-service"></a>De service uitvoeren

Druk op F5 (of typ `azds up` in het Terminal-venster) om de service uit te voeren. De service wordt automatisch uitgevoerd in uw zojuist geselecteerde _dev/scott_-ruimte. Controleer of uw service in een eigen ruimte wordt uitgevoerd door de opdracht `azds list-up` uit te voeren:

```cmd
$ azds list-up

Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

U ziet dat er nu een exemplaar van *mywebapi* wordt uitgevoerd in de ruimte _dev/scott_. De versie in _dev_ wordt nog steeds uitgevoerd, maar wordt niet vermeld.

Vermeld de URL’s voor de huidige ruimte door `azds list-uris` uit te voeren.

```cmd
$ azds list-uris

Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```

U ziet dat de URL van het openbare toegangspunt voor *webfrontend* wordt voorafgegaan door *scott.s*. Deze URL is uniek voor de _dev/scott_-ruimte. Dit URL-voorvoegsel vertelt de controller van inkomend verkeer verzoeken te routeren naar de _dev/scott_-versie van een service. Wanneer een verzoek met deze URL wordt afgehandeld door Dev Spaces, probeert de controller van inkomend verkeer het verzoek eerst te routeren naar de *webfrontend*-service in de _dev/scott_-ruimte. Als dat niet lukt, wordt het verzoek gerouteerd naar de *webfrontend*-service in de _dev_-ruimte als terugvaloptie. Merk ook op dat er een localhost-URL is voor toegang tot de service via localhost met behulp van de Kubernetes-functie *port-forward*. Zie [Hoe Azure Dev Spaces werkt en is geconfigureerd](how-dev-spaces-works.md) voor meer informatie over URL’s en routering in Azure Dev Spaces.

![Ruimteroutering](media/common/Space-Routing.png)

Deze ingebouwde functie van Azure Dev Spaces stelt u in staat om code te testen in een gedeelde ruimte, zonder dat elke ontwikkelaar telkens de volledige groep services opnieuw moet maken in hun eigen ruimte. Voor deze routering moeten doorgifteheaders worden doorgestuurd in uw app-code, zoals uitgelegd in de vorige stap van deze handleiding.

### <a name="test-code-in-a-space"></a>Code testen in een ruimte
Als u de nieuwe versie van *mywebapi* wilt testen samen met *webfrontend*, opent u de URL van het openbare toegangspunt voor *webfrontend* in de browser en gaat u naar de pagina Over. Hier wordt nu uw nieuwe bericht weergegeven.

Verwijder nu het gedeelte 'scott.s.' uit de URL en vernieuw de browser. U zou nu het oude gedrag weer moeten zien (waarbij de *mywebapi*-versie wordt uitgevoerd in _dev_).

Zodra u een _dev_-ruimte hebt die altijd de meest recente wijzigingen bevat en ervan uitgaande dat uw toepassing is ontworpen om te profiteren van de DevSpace-routering op basis van ruimte, zoals beschreven in dit gedeelte van de zelfstudie, wordt het in het ideale geval gemakkelijk om te zien hoe Dev Spaces aanzienlijk kan helpen bij het testen van nieuwe functies in de context van de grotere toepassing. In plaats van dat u _alle_ services naar uw privéruimte moet implementeren, kunt u een privéruimte maken die is afgeleid van _dev_, en alleen de services aanroepen waaraan u daadwerkelijk werkt. De routeringsinfrastructuur van Dev Spaces zorgt voor de rest door alle services die deze kan vinden buiten uw privéruimte, terwijl de standaardwaarden terug worden gezet naar de meest recent uitgevoerde versie in de _dev_-ruimte. En nog beter: _meerdere_ ontwikkelaars kunnen actief verschillende services op hetzelfde moment in hun eigen ruimte ontwikkelen zonder elkaar te onderbreken.

### <a name="well-done"></a>Dat is dus gelukt.
U hebt de introductiehandleiding voltooid! U hebt geleerd hoe u:

> [!div class="checklist"]
> * Azure Dev Spaces instellen met een beheerd Kubernetes-cluster in Azure.
> * Iteratief code ontwikkelen in containers.
> * Twee afzonderlijke services ontwikkelen en de DNS-servicedetectie van Kubernetes gebruiken om een andere service aan te roepen.
> * Uw code op een productieve manier ontwikkelen en testen in een teamomgeving.
> * Zorg voor een basislijn van de functionaliteit van Dev-spaties van om eenvoudig geïsoleerde wijzigingen te testen in de context van een grotere microservicetoepassing

Nu u Azure Dev Spaces hebt verkend, kunt u [uw dev-ruimte delen met teamleden](how-to/share-dev-spaces.md) en gaan samenwerken.

## <a name="clean-up"></a>Opruimen
Als u een exemplaar van Azure Dev Spaces in een cluster volledig wilt verwijderen, waaronder alle ontwikkelruimtes en de actieve services die erin worden uitgevoerd, gebruikt u de opdracht `az aks remove-dev-spaces`. Houd er rekening mee dat deze actie niet kan worden teruggedraaid. U kunt later opnieuw ondersteuning voor Azure Dev Spaces toevoegen aan het cluster, maar u moet dan helemaal opnieuw beginnen. Uw oude services en ruimtes worden niet hersteld.

In het volgende voorbeeld worden de Azure Dev Spaces-controllers in uw actieve abonnement vermeld en wordt vervolgens de Azure Dev Spaces-controller die is gekoppeld aan het AKS-cluster 'myaks' in de resourcegroep 'myaks-rg' verwijderd.

```cmd
azds controller list
```

```azurecli
az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
