---
title: CI/CD gebruiken met Azure Dev Spaces
services: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: gwallace
description: Meer informatie over het instellen van continue integratie/continue implementatie met Azure DevOps met Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, containers
ms.openlocfilehash: f2eb9449518b32ab74f2dbbca6b5489aed325db7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685633"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>CI/CD gebruiken met Azure Dev Spaces

Dit artikel begeleidt u bij het instellen van continue integratie/continue implementatie (CI/CD) naar Azure Kubernetes Service (AKS) met Dev Spaces ingeschakeld. Met CI/CD naar AKS kunnen app-updates automatisch worden geïmplementeerd wanneer vastgelegde code naar uw bronopslagplaats wordt gepusht. Het gebruik van CI/CD in combinatie met een cluster met Dev Spaces is handig omdat het een basislijn van de toepassing up-to-date kan houden voor het team om mee te werken.

![Voorbeeld van CI/CD-diagram](../media/common/ci-cd-simple.png)

Hoewel dit artikel u begeleidt met Azure DevOps, zijn dezelfde concepten van toepassing op CI/CD-systemen zoals Jenkins, TeamCity, enz.

## <a name="prerequisites"></a>Vereisten
* [Aks-cluster (Azure Kubernetes Service) met Azure Dev Spaces ingeschakeld](../get-started-netcore.md)
* [Azure Dev Spaces CLI geïnstalleerd](upgrade-tools.md)
* [Azure DevOps-organisatie met een project](https://docs.microsoft.com/azure/devops/user-guide/sign-up-invite-teammates?view=vsts)
* [Azure Container Registry (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * [Beheerdersaccountgegevens](../../container-registry/container-registry-authentication.md#admin-account) azure containerregister beschikbaar
* [Uw AKS-cluster autoriseren om uit uw Azure-containerregister te halen](../../aks/cluster-container-registry-integration.md)

## <a name="download-sample-code"></a>Voorbeeldcode downloaden
Omwille van de tijd, laten we een vork van onze sample code GitHub repository maken. Ga https://github.com/Azure/dev-spaces naar en selecteer **Fork**. Zodra het vorkproces is voltooid, **kloont u** uw gevorkte versie van de repository lokaal. Standaard wordt de _masterbranch_ uitgecheckt, maar we hebben een aantal tijdbesparende wijzigingen in de _azds_updates_ branch opgenomen, die ook tijdens je voorvork hadden moeten worden overgedragen. De _azds_updates-branch_ bevat updates die we u vragen om handmatig te maken in de dev Spaces-zelfstudiesecties, evenals enkele vooraf gemaakte YAML- en JSON-bestanden voor het stroomlijnen van de implementatie van het CI/CD-systeem. U een `git checkout -b azds_updates origin/azds_updates` opdracht zoals gebruiken om de _azds_updates-branch_ in uw lokale opslagplaats te bekijken.

## <a name="dev-spaces-setup"></a>Dev Spaces-installatie
Maak een nieuwe spatie `azds space select` _genaamd dev_ met behulp van de opdracht. De _dev-ruimte_ wordt gebruikt door uw CI/CD-pijplijn om uw codewijzigingen te pushen. Het zal ook worden gebruikt om _onderliggende ruimtes_ te maken op basis _van dev._

```cmd
azds space select -n dev
```

Wanneer u wordt gevraagd een bovenliggende _ \<\>_ dev-ruimte te selecteren, selecteert u geen .

Nadat uw dev-ruimte is gemaakt, moet u het achtervoegsel van de host bepalen. Gebruik `azds show-context` de opdracht om het hostachtervoegsel van de Azure Dev Spaces Ingress Controller weer te geven.

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

In het bovenstaande voorbeeld wordt het hostachtervoegsel _fedcba098.eus.azds.io_. Deze waarde wordt later gebruikt bij het maken van uw releasedefinitie.

De _dev-ruimte_ bevat altijd de nieuwste status van de repository, een basislijn, zodat ontwikkelaars _onderliggende ruimten_ kunnen maken van _dev_ om hun geïsoleerde wijzigingen te testen in de context van de grotere app. Dit concept wordt nader besproken in de Dev Spaces tutorials.

## <a name="creating-the-build-definition"></a>De builddefinitie maken
Open uw Azure DevOps-teamproject in een webbrowser en navigeer naar de sectie _Pijplijnen._ Klik eerst op uw profielfoto rechtsboven op de Azure DevOps-site, open het deelvenster Voorvertoningsfuncties en schakel de _nieuwe YAML-pijplijncreatie-ervaring_uit:

![Deelvenster Voorbeeldfuncties openen](../media/common/preview-feature-open.png)

De optie om uit te schakelen:

![Nieuwe YAML-optie voor het maken van pijplijnen](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> De Azure DevOps _Nieuwe YAML-pijplijnervaringservaringservaring_ is in strijd met het maken van vooraf gedefinieerde buildpijplijnen op dit moment. U moet het voor nu uitschakelen om onze vooraf gedefinieerde buildpipeline te implementeren.

In de _azds_updates-branch_ hebben we een eenvoudige [Azure Pipeline YAML](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema) opgenomen die de buildstappen definieert die nodig zijn voor *mywebapi* en *webfrontend.*

Afhankelijk van de taal die u hebt gekozen, is de yaml van de pijplijn ingecheckt op een pad dat lijkt op:`samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`

Ga als volgt te werk om een pijplijn uit dit bestand te maken:
1. Navigeer op de hoofdpagina van het Ontwikkelaarsproject naar Pipelines > Builds.
1. Selecteer de optie om een pijplijn voor **nieuwe** build te maken.
1. Selecteer **GitHub** als bron, autoriseer indien nodig met uw GitHub-account en selecteer de _azds_updates_ vertakking van uw gevorkte versie van de _dev-spaces_ sample application repository.
1. Selecteer **Configuratie als code**of **YAML**als sjabloon.
1. U krijgt nu een configuratiepagina voor uw buildpijplijn. Zoals hierboven vermeld navigeer je naar het taalspecifieke pad voor het **YAML-bestandspad** met behulp van de **...-knop.** Bijvoorbeeld `samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml`.
1. Ga naar het tabblad **Variabelen.**
1. Voeg _dockerId_ handmatig toe als een variabele, de gebruikersnaam van uw [Azure Container Registry administrator-account.](../../container-registry/container-registry-authentication.md#admin-account) (Vermeld in het artikel voorwaarden)
1. Voeg _dockerPassword_ handmatig toe als een variabele, het wachtwoord van uw [Azure Container Registry administrator-account.](../../container-registry/container-registry-authentication.md#admin-account) Zorg ervoor dat _dockerPassword_ als geheim (door het selecteren van het vergrendelingspictogram) voor beveiligingsdoeleinden op te geven.
1. Selecteer **& wachtrij opslaan**.

Je hebt nu een CI-oplossing die automatisch *mywebapi* en *webfrontend* bouwt voor elke update die naar de _azds_updates-tak_ van je GitHub-vork wordt geschoven. U controleren of de Docker-afbeeldingen zijn gepusht door naar de Azure-portal te navigeren, uw Azure-containerregister te selecteren en op het tabblad **Opslagplaatsen** te bladeren. Het kan enkele minuten duren voordat de afbeeldingen zijn gemaakt en in uw containerregister worden weergegeven.

![Azure Container Registry-opslagplaatsen](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>De releasedefinitie maken

1. Ga op de hoofdpagina van het Ontwikkelaarsproject naar Pipelines > Releases
1. Als u werkt in een gloednieuw DevOps-project dat nog geen releasedefinitie bevat, moet u eerst een lege releasedefinitie maken voordat u verdergaat. De optie Importeren wordt pas weergegeven in de gebruikersinterface als u een bestaande releasedefinitie hebt.
1. Klik aan de linkerkant op de knop **+ Nieuw** en klik vervolgens op Een **pijplijn importeren**.
1. Klik **op** `samples/release.json` Bladeren en selecteer in uw project.
1. Klik op **OK**. Let op: het deelvenster Pijplijn is geladen met de pagina bewerken van de releasedefinitie. Merk ook op dat er enkele rode waarschuwingspictogrammen zijn die clusterspecifieke details aangeven die nog moeten worden geconfigureerd.
1. Klik links van het deelvenster Pijplijn op de **artefactbel toevoegen.**
1. Selecteer **in** de vervolgkeuzelijst Bron de buildpijplijn die u eerder hebt gemaakt.
1. Kies voor de **standaardversie** **Laatste uit de standaardvertakking van de buildpijplijn met tags**.
1. Tags **Tags** leeg laten.
1. Stel de alias `drop` **Bron** in op . De waarde van de **bronalias** wordt gebruikt door de vooraf gedefinieerde releasetaken, zodat deze moet worden ingesteld.
1. Klik op **Add**.
1. Klik nu op het bliksemschichtpictogram op de nieuw gemaakte `drop` artefactbron, zoals hieronder wordt weergegeven:

    ![Instelling voor continue implementatie van artefacten vrijgeven](../media/common/release-artifact-cd-setup.png)
1. Schakel de **trigger voor continue implementatie**in.
1. Plaats de muisaanwijzer op het tabblad **Taken** naast **Pijplijn** en klik op _dev_ om de _taken voor dev-fase_ te bewerken.
1. Controleer of **Azure Resource Manager** is geselecteerd onder **Verbindingstype.** en u ziet de drie vervolgkeuzebesturingselementen in het rood gemarkeerd: ![Instelling voor de definitie van vrijgeven](../media/common/release-setup-tasks.png)
1. Selecteer het Azure-abonnement dat u gebruikt met Azure Dev Spaces. Het kan ook nodig zijn om op **Autoriseren**te klikken.
1. Selecteer de brongroep en het cluster dat u gebruikt met Azure Dev Spaces.
1. Klik op **agent baan**.
1. Selecteer **Gehoste Ubuntu 1604** onder **Agent pool**.
1. Plaats de **muisaanwijzer** boven de taakkiezer bovenaan en klik op _prod_ om de _prod-fasetaken_ te bewerken.
1. Controleer of **Azure Resource Manager** is geselecteerd onder **Verbindingstype.** en selecteer het Azure-abonnement, de brongroep en het cluster dat u gebruikt met Azure Dev Spaces.
1. Klik op **agent baan**.
1. Selecteer **Gehoste Ubuntu 1604** onder **Agent pool**.
1. Klik op het tabblad **Variabelen** om de variabelen voor uw release bij te werken.
1. Werk de waarde van **DevSpacesHostSuffix bij** van **UPDATE_ME** naar uw hostachtervoegsel. Het hostachtervoegsel wordt weergegeven `azds show-context` wanneer u de opdracht eerder hebt uitgevoerd.
1. Klik rechtsboven op **Opslaan** en **OK**.
1. Klik **op + Loslaten** (naast de knop Opslaan) en maak een **release**.
1. Controleer **onder Artefacten**of de nieuwste build van uw buildpijplijn is geselecteerd.
1. Klik **op Maken**.

Er wordt nu een geautomatiseerd releaseproces gestart, waarbij de *mywebapi-* en *webfrontend-diagrammen* worden geïmplementeerd in uw Kubernetes-cluster in _de dev-ruimte_ op het hoogste niveau. U de voortgang van uw release controleren op de Azure DevOps-webportal:

1. Navigeer naar de sectie **Releases** onder **Pijplijnen**.
1. Klik op de releasepijplijn voor de voorbeeldtoepassing.
1. Klik op de naam van de nieuwste release.
1. Plaats **de muisaanwijzer** op het vak Dev onder **Stadia** en klik op **Logboeken**.

De release wordt uitgevoerd wanneer alle taken zijn voltooid.

> [!TIP]
> Als uw release mislukt met een foutbericht zoals *UPGRADE MISLUKT: time-out wachten op de voorwaarde,* probeer dan de pods in uw cluster te inspecteren [met behulp van het Kubernetes-dashboard.](../../aks/kubernetes-dashboard.md) Als u ziet dat de pods niet beginnen met foutberichten zoals *Niet in staat om afbeelding "azdsexample.azurecr.io/mywebapi:122" te trekken: rpc-fout: code = Onbekende desc = Foutreactie van daemon: Download https:\//azdsexample.azurecr.io/v2/mywebapi/manifests/122: ongeautoriseerd: verificatie vereist,* dit kan zijn omdat uw cluster niet is geautoriseerd om uit uw Azure Container Registry te halen. Zorg ervoor dat u het [AKS-cluster autoriseren dat u wilt ophalen uit uw azure-containerregistervereiste.](../../aks/cluster-container-registry-integration.md)

U hebt nu een volledig geautomatiseerde CI/CD-pijplijn voor uw GitHub-voorvork van de voorbeeld-apps van Dev Spaces. Elke keer dat u commit en push code, de build pijplijn zal bouwen en duwen de *mywebapi* en *webfrontend* beelden om uw aangepaste ACR instantie. Vervolgens implementeert de releasepijplijn de Helm-grafiek voor elke app in _de dev-ruimte_ op uw cluster met Dev Spaces.

## <a name="accessing-your-_dev_-services"></a>Toegang tot uw _dev-services_
Na implementatie is _de dev-versie_ van *webfrontend* toegankelijk `http://dev.webfrontend.fedcba098.eus.azds.io`met een openbare URL zoals: . U deze URL `azds list-uri` vinden door de opdracht uit te voeren: 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>Implementeren in productie

Ga als volgt te werk om een bepaalde release handmatig te promoten bij _prod_ met behulp van het CI/CD-systeem dat in deze zelfstudie is gemaakt:
1. Navigeer naar de sectie **Releases** onder **Pijplijnen**.
1. Klik op de releasepijplijn voor de voorbeeldtoepassing.
1. Klik op de naam van de nieuwste release.
1. Plaats de muisaanwijzer boven het **prod-vak** onder **Fasen** en klik op **Implementeren**.
    ![Niveau verhogen naar productie](../media/common/prod-promote.png)
1. Plaats de **prod** muisaanwijzer weer onder **Stadia** en klik op **Logboeken**.

De release wordt uitgevoerd wanneer alle taken zijn voltooid.

De _prod-fase_ van de CI/CD-pijplijn maakt gebruik van een load balancer in plaats van de Dev Spaces Ingress-controller om toegang te bieden tot _prod-services._ Services die in de _prod-fase_ worden geïmplementeerd, zijn toegankelijk als IP-adressen in plaats van DNS-namen. In een productieomgeving u ervoor kiezen om uw eigen Ingress-controller te maken om uw services te hosten op basis van uw eigen DNS-configuratie.

Als u het IP-adres van de webfrontendservice wilt bepalen, klikt u op de **openbare IP-stap Afdrukken om** de logboekuitvoer uit te breiden. Gebruik het IP-adres dat wordt weergegeven in de logboekuitvoer om toegang te krijgen tot de **webfrontend-toepassing.**

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>Dev Spaces instrumentatie in productie
Hoewel Dev Spaces-instrumentatie is ontworpen om de normale werking van uw toepassing _niet_ in de weg te staan, raden we u aan uw productieworkloads uit te voeren in een Kubernetes-naamruimte die niet is ingeschakeld met Dev Spaces. Als u dit type Kubernetes-naamruimte gebruikt, moet `kubectl` u uw productienaamruimte maken met behulp van de CLI of uw CI/CD-systeem toestaan om deze te maken tijdens de eerste Helm-implementatie. _Als u_ een spatie selecteert of anderszins maakt met de gereedschappen Van Dev Spaces, wordt het instrumentatie van Dev Spaces toegevoegd aan die naamruimte.

Hier is een voorbeeld naamruimtestructuur die functieontwikkeling, de 'dev'-omgeving _en_ productie ondersteunt, allemaal in één Kubernetes-cluster:

![Voorbeeld van naamruimtestructuur](../media/common/cicd-namespaces.png)

> [!Tip]
> Als u al een `prod` spatie hebt gemaakt en deze gewoon wilt uitsluiten van de instrumentatie van Dev Spaces (zonder deze te verwijderen!), u dit doen met de volgende opdracht Dev Spaces CLI:
>
> `azds space remove -n prod --no-delete`
>
> Mogelijk moet u alle pods `prod` in de naamruimte verwijderen nadat ze dit hebben gedaan, zodat ze opnieuw kunnen worden gemaakt zonder dev Spaces-instrumentatie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over teamontwikkeling met Azure Dev Spaces](../team-development-netcore.md)