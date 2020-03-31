---
title: Implementatietechnologieën in Azure-functies
description: Meer informatie over de verschillende manieren waarop u code implementeren in Azure-functies.
author: georgewallace
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gwallace
ms.openlocfilehash: 43352117d149abbe41ba7bf49a1ffb68e46d2707
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277126"
---
# <a name="deployment-technologies-in-azure-functions"></a>Implementatietechnologieën in Azure-functies

U een aantal verschillende technologieën gebruiken om uw Azure Functions-projectcode te implementeren in Azure. Dit artikel bevat een uitputtende lijst van deze technologieën, beschrijft welke technologieën beschikbaar zijn voor welke smaken van functies, legt uit wat er gebeurt wanneer u elke methode gebruikt en geeft aanbevelingen voor de beste methode om te gebruiken in verschillende scenario's . De verschillende tools die de implementatie van Azure-functies ondersteunen, zijn afgestemd op de juiste technologie op basis van hun context. In het algemeen is zip-implementatie de aanbevolen implementatietechnologie voor Azure-functies.

## <a name="deployment-technology-availability"></a>Beschikbaarheid van implementatietechnologie

Azure Functions ondersteunt lokale ontwikkeling en hosting op meerdere platforms op Windows en Linux. Momenteel zijn er drie hostingplannen beschikbaar:

+ [Verbruik](functions-scale.md#consumption-plan)
+ [Premium](functions-scale.md#premium-plan)
+ [Dedicated (App Service)](functions-scale.md#app-service-plan)

Elk plan heeft ander gedrag. Niet alle implementatietechnologieën zijn beschikbaar voor elke smaak van Azure-functies. In de volgende grafiek ziet u welke implementatietechnologieën worden ondersteund voor elke combinatie van besturingssysteem en hostingplan:

| Implementatietechnologie | Windows-verbruik | Windows Premium | Windows Dedicated  | Linux-verbruik | Linux Premium | Linux Dedicated |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| URL van extern pakket<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| Zip-implementatie |✔|✔|✔|✔|✔|✔|
| Dockercontainer | | | | |✔|✔|
| Web implementeren |✔|✔|✔| | | |
| Broncodebeheer |✔|✔|✔| |✔|✔|
| Lokale Git<sup>1</sup> |✔|✔|✔| |✔|✔|
| Cloudsynchronisatie<sup>1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| Portalbewerking |✔|✔|✔| |✔<sup>2.</sup>|✔<sup>2.</sup>|

<sup>1</sup> Implementatietechnologie waarvoor [handmatige triggersynchronisatie vereist is.](#trigger-syncing)  
<sup>2</sup> Portal editing is alleen ingeschakeld voor HTTP en Timer triggers voor functies op Linux met behulp van Premium en Dedicated plannen.

## <a name="key-concepts"></a>Belangrijkste concepten

Sommige belangrijke concepten zijn essentieel om te begrijpen hoe implementaties werken in Azure-functies.

### <a name="trigger-syncing"></a>Synchronisatie activeren

Wanneer u een van uw triggers wijzigt, moet de infrastructuur voor functies op de hoogte zijn van de wijzigingen. Synchronisatie gebeurt automatisch voor veel implementatietechnologieën. In sommige gevallen moet u uw triggers echter handmatig synchroniseren. Wanneer u uw updates implementeert door te verwijzen naar een URL van een extern pakket, lokale Git, cloudsynchronisatie of FTP, moet u de triggers handmatig synchroniseren. U triggers op drie manieren synchroniseren:

* Uw functie-app opnieuw starten in de Azure-portal
* Stuur een HTTP `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` POST-verzoek naar het gebruik van de [hoofdsleutel](functions-bindings-http-webhook-trigger.md#authorization-keys).
* Stuur een HTTP `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`POST-verzoek naar . Vervang de tijdelijke aanduidingen door uw abonnements-ID, de naam van de resourcegroep en de naam van uw functie-app.

### <a name="remote-build"></a>Op afstand bouwen

Azure Functions kan automatisch builds uitvoeren op de code die het ontvangt na zip-implementaties. Deze builds gedragen zich iets anders, afhankelijk van of uw app draait op Windows of Linux. Externe builds worden niet uitgevoerd wanneer een app eerder is ingesteld om uit te voeren in de modus [Uitvoeren vanaf pakket.](run-functions-from-deployment-package.md) Als u wilt weten hoe u externe builden gebruiken, navigeert u naar [zip-implementatie.](#zip-deploy)

> [!NOTE]
> Als u problemen ondervindt met externe build, kan dit zijn omdat uw app is gemaakt voordat de functie beschikbaar is gesteld (1 augustus 2019). Probeer een nieuwe functie-app `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` te maken of werk om uw functie-app bij te werken. Deze opdracht kan twee pogingen vergen om te slagen.

#### <a name="remote-build-on-windows"></a>Externe build op Windows

Alle functie-apps die op Windows worden uitgevoerd, hebben een kleine beheer-app, de SCM -site (of [Kudu).](https://github.com/projectkudu/kudu) Deze site verwerkt een groot deel van de implementatie- en buildlogica voor Azure-functies.

Wanneer een app wordt geïmplementeerd in Windows, worden `dotnet restore` taalspecifieke opdrachten `npm install` uitgevoerd, zoals (C#) of (JavaScript).

#### <a name="remote-build-on-linux"></a>Remote build op Linux

Om remote build op Linux in te schakelen, moeten de volgende [toepassingsinstellingen](functions-how-to-use-azure-function-app-settings.md#settings) zijn ingesteld:

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Standaard voeren zowel [Azure Functions Core Tools](functions-run-local.md) als de Azure Functions Extension for Visual Studio [Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) remote builds uit bij het implementeren op Linux. Hierdoor maken beide hulpprogramma's deze instellingen automatisch voor u in Azure. 

Wanneer apps op afstand op Linux worden gebouwd, worden ze [uitgevoerd vanuit het implementatiepakket.](run-functions-from-deployment-package.md) 

##### <a name="consumption-plan"></a>Verbruiksabonnement

Linux-functie-apps die worden uitgevoerd in het verbruiksplan hebben geen SCM/Kudu-site, waardoor de implementatieopties worden beperkt. Functie-apps op Linux die in het verbruiksplan worden uitgevoerd, ondersteunen echter externe builds.

##### <a name="dedicated-and-premium-plans"></a>Speciale en Premium-abonnementen

Functie-apps die op Linux worden uitgevoerd in het [Dedicated (App Service) abonnement](functions-scale.md#app-service-plan) en het [Premium-abonnement](functions-scale.md#premium-plan) hebben ook een beperkte SCM/Kudu-site.

## <a name="deployment-technology-details"></a>Details van implementatietechnologie

De volgende implementatiemethoden zijn beschikbaar in Azure-functies.

### <a name="external-package-url"></a>URL van extern pakket

U een URL van een extern pakket gebruiken om te verwijzen naar een extern pakketbestand (.zip) dat uw functie-app bevat. Het bestand wordt gedownload van de opgegeven URL en de app wordt uitgevoerd in de modus [Uitvoeren vanaf pakket.](run-functions-from-deployment-package.md)

>__Hoe het te gebruiken:__ Toevoegen `WEBSITE_RUN_FROM_PACKAGE` aan de toepassingsinstellingen. De waarde van deze instelling moet een URL zijn (de locatie van het specifieke pakketbestand dat u wilt uitvoeren). U instellingen toevoegen [in de portal](functions-how-to-use-azure-function-app-settings.md#settings) of met de Azure [CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). 
>
>Als u Azure Blob-opslag gebruikt, gebruikt u een privécontainer met een [sas (shared access signature)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) om functies toegang te geven tot het pakket. Elke keer dat de toepassing opnieuw wordt opgestart, wordt een kopie van de inhoud opgehaald. Uw referentie moet geldig zijn voor de levensduur van de toepassing.

>__Wanneer te gebruiken:__ URL van extern pakket is de enige ondersteunde implementatiemethode voor Azure-functies die op Linux worden uitgevoerd in het beheerplan, als de gebruiker niet wil dat er een [externe build](#remote-build) plaatsvindt. Wanneer u het pakketbestand bijwerkt waarnaar een functie-app verwijst, moet u [triggers handmatig synchroniseren](#trigger-syncing) om Azure te vertellen dat uw toepassing is gewijzigd.

### <a name="zip-deploy"></a>Zip-implementatie

Gebruik zip-implementatie om een .zip-bestand met uw functie-app naar Azure te duwen. Optioneel u instellen dat uw app [vanuit het pakket wordt uitgevoerd](run-functions-from-deployment-package.md)of opgeven dat er een externe [build](#remote-build) plaatsvindt.

>__Hoe het te gebruiken:__ Implementeren met behulp van uw favoriete clienttool: [Visual Studio Code](functions-develop-vs-code.md#publish-to-azure), Visual [Studio](functions-develop-vs.md#publish-to-azure)of vanaf de opdrachtregel met behulp van de Azure Functions [Core Tools](functions-run-local.md#project-file-deployment). Standaard gebruiken deze hulpprogramma's zip-implementatie en [worden ze uitgevoerd vanuit het pakket.](run-functions-from-deployment-package.md) Core Tools en de Visual Studio Code extensie maken beide [remote build](#remote-build) mogelijk bij het implementeren op Linux. Als u een .zip-bestand handmatig wilt implementeren in uw functie-app, volgt u de instructies in [Implementeren vanuit een .zip-bestand of URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

>Wanneer u implementeert met zip-implementatie, u instellen dat uw app [vanaf het pakket wordt uitgevoerd.](run-functions-from-deployment-package.md) Als u wilt uitvoeren `WEBSITE_RUN_FROM_PACKAGE` vanaf het `1`pakket, stelt u de waarde van de toepassingsinstelling in op . We raden zip-implementatie aan. Het levert snellere laadtijden op voor uw toepassingen en het is de standaardinstelling voor VS Code, Visual Studio en azure CLI. 

>__Wanneer te gebruiken:__ Zip-implementatie is de aanbevolen implementatietechnologie voor Azure-functies.

### <a name="docker-container"></a>Dockercontainer

U een Linux-containerafbeelding implementeren die uw functie-app bevat.

>__Hoe het te gebruiken:__ Maak een Linux-functie-app in het Premium- of Dedicated-abonnement en geef op van welke containerafbeelding moet worden uitgevoerd. U kunt dit op twee manieren doen:
>
>* Maak een Linux-functie-app op een Azure App Service-abonnement in de Azure-portal. Selecteer **Docker Image**voor **Publiceren**en configureer de container. Voer de locatie in waar de afbeelding wordt gehost.
>* Maak een Linux-functie-app op een App Service-abonnement met behulp van de Azure CLI. Zie [Een functie op Linux maken met behulp van een aangepaste afbeelding](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function)voor meer informatie.
>
>Als u wilt implementeren naar een bestaande app met behulp [`func deploy`](functions-run-local.md#publish) van een aangepaste container, gebruikt u in Azure Functions Core [Tools](functions-run-local.md)de opdracht.

>__Wanneer te gebruiken:__ Gebruik de docker-containeroptie wanneer u meer controle nodig hebt over de Linux-omgeving waar uw functie-app wordt uitgevoerd. Dit implementatiemechanisme is alleen beschikbaar voor functies die op Linux worden uitgevoerd.

### <a name="web-deploy-msdeploy"></a>Webdeploy (MSDeploy)

Web Deploy-pakketten en implementeert uw Windows-toepassingen op elke IIS-server, inclusief uw functie-apps die op Windows in Azure worden uitgevoerd.

>__Hoe het te gebruiken:__ Gebruik [Hulpprogramma's voor Visual Studio voor Azure-functies](functions-create-your-first-function-visual-studio.md). Schakel het selectievakje **Uitvoeren op pakketbestand (aanbevolen)** uit.
>
>U [Web Deploy 3.6](https://www.iis.net/downloads/microsoft/web-deploy) ook downloaden en rechtstreeks bellen. `MSDeploy.exe`

>__Wanneer te gebruiken:__ Web Deploy wordt ondersteund en heeft geen problemen, maar het voorkeursmechanisme is [zip-implementatie met Run From Package ingeschakeld.](#zip-deploy) Zie de [ontwikkelgids voor Visual Studio](functions-develop-vs.md#publish-to-azure)voor meer informatie.

### <a name="source-control"></a>Broncodebeheer

Gebruik bronbesturingselement om uw functie-app te verbinden met een Git-opslagplaats. Een update van code in die repository activeert implementatie. Zie voor meer informatie de [Kudu Wiki.](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments)

>__Hoe het te gebruiken:__ Gebruik Implementatiecentrum in het gebied Functies van de portal om publicatie in te stellen vanuit bronbeheer. Zie [Continue implementatie voor Azure-functies voor](functions-continuous-deployment.md)meer informatie.

>__Wanneer te gebruiken:__ Het gebruik van bronbeheer is de beste praktijk voor teams die samenwerken aan hun functie-apps. Bronbeheer is een goede implementatieoptie die geavanceerdere implementatiepijplijnen mogelijk maakt.

### <a name="local-git"></a>Lokale Git

U de lokale Git gebruiken om code van uw lokale machine naar Azure-functies te pushen met Git.

>__Hoe het te gebruiken:__ Volg de instructies in [Lokale Git-implementatie naar Azure App Service.](../app-service/deploy-local-git.md)

>__Wanneer te gebruiken:__ In het algemeen raden we u aan een andere implementatiemethode te gebruiken. Wanneer u publiceert vanuit de lokale Git, moet u [triggers handmatig synchroniseren.](#trigger-syncing)

### <a name="cloud-sync"></a>Cloudsynchronisatie

Gebruik cloudsynchronisatie om je inhoud te synchroniseren van Dropbox en OneDrive naar Azure-functies.

>__Hoe het te gebruiken:__ Volg de instructies in [Inhoud synchroniseren vanuit een cloudmap.](../app-service/deploy-content-sync.md)

>__Wanneer te gebruiken:__ In het algemeen raden we andere implementatiemethoden aan. Wanneer u publiceert met behulp van cloudsynchronisatie, moet u [triggers handmatig synchroniseren.](#trigger-syncing)

### <a name="ftp"></a>FTP

U FTP gebruiken om bestanden rechtstreeks over te zetten naar Azure-functies.

>__Hoe het te gebruiken:__ Volg de instructies in [Inhoud implementeren met FTP/s.](../app-service/deploy-ftp.md)

>__Wanneer te gebruiken:__ In het algemeen raden we andere implementatiemethoden aan. Wanneer u publiceert met FTP, moet u [triggers handmatig synchroniseren.](#trigger-syncing)

### <a name="portal-editing"></a>Portalbewerking

In de portal-gebaseerde editor u de bestanden die zich in uw functie-app bevinden direct bewerken (in wezen elke keer dat u uw wijzigingen opslaat).

>__Hoe het te gebruiken:__ Als u uw functies in de Azure-portal wilt kunnen bewerken, moet u uw functies in de portal hebben [gemaakt.](functions-create-first-azure-function.md) Om één enkele bron van waarheid te behouden, maakt het gebruik van een andere implementatiemethode uw functie alleen-lezen en voorkomt u voortdurende portalbewerking. Als u wilt terugkeren naar een status waarin u uw bestanden in de Azure-portal `Read/Write` bewerken, u `WEBSITE_RUN_FROM_PACKAGE`de bewerkingsmodus handmatig terugschakelen naar en alle implementatiegerelateerde toepassingsinstellingen verwijderen (zoals ). 

>__Wanneer te gebruiken:__ De portal is een goede manier om aan de slag te gaan met Azure-functies. Voor intensievere ontwikkelingswerkzaamheden raden we u aan een van de volgende clienttools te gebruiken:
>
>* [Visual Studio-code](functions-create-first-function-vs-code.md)
>* [Azure Functions Core-hulpprogramma's (opdrachtregel)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

In de volgende tabel worden de besturingssystemen en talen weergegeven die portalbewerking ondersteunen:

| | Windows-verbruik | Windows Premium | Windows Dedicated | Linux-verbruik | Linux Premium | Linux Dedicated |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| C# Script |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (Preview) | | | | | | |
| PowerShell (Preview) |✔|✔|✔| | | |
| TypeScript (Node.js) | | | | | | |

<sup>*</sup>Portalediting is alleen ingeschakeld voor HTTP- en Timer-triggers voor functies op Linux met behulp van Premium- en Dedicated-abonnementen.

## <a name="deployment-slots"></a>Implementatiesites

Wanneer u uw functie-app implementeert naar Azure, u deze implementeren naar een afzonderlijke implementatiesleuf in plaats van rechtstreeks naar de productie. Zie de documentatie [azure functions Deployment Slots](../app-service/deploy-staging-slots.md) voor meer informatie voor meer informatie over implementatiesleuven.

## <a name="next-steps"></a>Volgende stappen

Lees deze artikelen voor meer informatie over het implementeren van uw functie-apps: 

+ [Doorlopende implementatie voor Azure Functions](functions-continuous-deployment.md)
+ [Continue levering met Azure DevOps](functions-how-to-azure-devops.md)
+ [Zip-implementaties voor Azure-functies](deployment-zip-push.md)
+ [Uw Azure-functies uitvoeren vanuit een pakketbestand](run-functions-from-deployment-package.md)
+ [Resourceimplementatie voor uw functie-app automatiseren in Azure-functies](functions-infrastructure-as-code.md)
