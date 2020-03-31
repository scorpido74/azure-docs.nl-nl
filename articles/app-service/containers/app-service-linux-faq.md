---
title: Veelgestelde vragen over ingebouwde containers uitvoeren
description: Vind antwoorden op de veelgestelde vragen over de ingebouwde Linux-containers in Azure App Service.
keywords: azure app service, web app, faq, linux, oss, web app voor containers, multi-container, multicontainer
author: msangapu-msft
ms.topic: article
ms.date: 10/30/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: f0a8b1758571a9473402d11a4d5141a11f76504d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245817"
---
# <a name="azure-app-service-on-linux-faq"></a>Veelgestelde vragen over Azure App Service on Linux

Met de release van App Service op Linux werken we aan het toevoegen van functies en het aanbrengen van verbeteringen aan ons platform. Dit artikel geeft antwoorden op vragen die onze klanten ons onlangs hebben gesteld.

Als je een vraag hebt, reageer dan op dit artikel.

## <a name="built-in-images"></a>Ingebouwde afbeeldingen

**Ik wil de ingebouwde Docker containers die het platform biedt vork. Waar kan ik die bestanden vinden?**

U alle Docker-bestanden vinden op [GitHub.](https://github.com/azure-app-service) U vindt alle Docker containers op [Docker Hub.](https://hub.docker.com/u/appsvc/)

<a id="#startup-file"></a>

**Wat zijn de verwachte waarden voor de sectie Opstartbestand wanneer ik de runtimestack configureer?**

| Stack           | Verwachte waarde                                                                         |
|-----------------|----------------------------------------------------------------------------------------|
| Java SE         | de opdracht om uw JAR-app `java -jar /home/site/wwwroot/app.jar --server.port=80`te starten (bijvoorbeeld) |
| Tomcat          | de locatie van een script om de benodigde configuraties uit te voeren `/home/site/deployments/tools/startup_script.sh`(bijvoorbeeld)          |
| Node.js         | het PM2-configuratiebestand of uw scriptbestand                                |
| .NET Core       | de gecompileerde DLL-naam als`dotnet <myapp>.dll`                                 |
| Ruby            | het Ruby-script waarmee u uw app wilt initialiseren                     |

Deze opdrachten of scripts worden uitgevoerd nadat de ingebouwde Docker-container is gestart, maar voordat de toepassingscode wordt gestart.

## <a name="management"></a>Beheer

**Wat gebeurt er als ik op de knop Opnieuw starten in de Azure-portal druk?**

Deze actie is hetzelfde als een Docker herstart.

**Kan ik Secure Shell (SSH) gebruiken om verbinding te maken met de virtuele machine (VM) van de app container?**

Ja, dat kan via de Site Source Control Management (SCM).

> [!NOTE]
> U kunt ook rechtstreeks vanaf uw lokale ontwikkelcomputer verbinding maken met de app-container via SSH, SFTP of Visual Studio Code (voor live foutopsporing gebruikt u Node.js-apps). Zie [Foutopsporing op afstand en SSH in App Service in Linux](https://azure.github.io/AppService/2018/05/07/New-SSH-Experience-and-Remote-Debugging-for-Linux-Web-Apps.html) voor meer informatie.
>

**Hoe kan ik een Linux App Service-abonnement maken via een SDK- of Azure Resource Manager-sjabloon?**

Stel het **gereserveerde** veld van de app-service in *op true.*

## <a name="continuous-integration-and-deployment"></a>Continue integratie en implementatie

**Mijn web-app maakt nog steeds gebruik van een oude Docker-containerafbeelding nadat ik de afbeelding op Docker Hub heb bijgewerkt. Ondersteunt u continue integratie en implementatie van aangepaste containers?**

Ja, om continue integratie/implementatie voor Azure Container Registry of DockerHub in te stellen, door Continue implementatie te volgen [met Web App voor containers.](./app-service-linux-ci-cd.md) Voor privéregisters u de container vernieuwen door de web-app te stoppen en vervolgens te starten. Of u een dummy-toepassingsinstelling wijzigen of toevoegen om een vernieuwing van uw container te forceren.

**Ondersteunt u staging-omgevingen?**

Ja.

**Kan ik *WebDeploy/MSDeploy* gebruiken om mijn web-app te implementeren?**

Ja, u moet een app-instelling instellen die wordt aangeroepen `WEBSITE_WEBDEPLOY_USE_SCM` als *false.*

**Git implementatie van mijn applicatie mislukt bij het gebruik van Linux web app. Hoe kan ik het probleem oplossen?**

Als git-implementatie mislukt in uw Linux-web-app, kiest u een van de volgende opties om uw toepassingscode te implementeren:

- Gebruik de functie Continue levering (Preview): u de broncode van uw app opslaan in een Repo van Azure DevOps Git of GitHub om Azure Continuous Delivery te gebruiken. Zie [Continue levering configureren voor Linux-web-app voor](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/)meer informatie.

- Gebruik de [API voor ZIP-deploy:](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)Om deze API te gebruiken, gaat [SSH naar uw web-app](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support) en gaat u naar de map waar u uw code wilt implementeren. Voer de volgende code uit:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Als u een foutmelding `curl` krijgt dat de opdracht niet is `apt-get install curl` gevonden, moet `curl` u de krul installeren voordat u de vorige opdracht uitvoert.

## <a name="language-support"></a>Taalondersteuning

**Ik wil websockets gebruiken in mijn Node.js-applicatie, speciale instellingen of configuraties om in te stellen?**

Ja, `perMessageDeflate` schakel de Node.js-code aan de serverzijde uit. Als u bijvoorbeeld socket.io gebruikt, gebruikt u de volgende code:

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Ondersteunt u ongecompileerde .NET Core-apps?**

Ja.

**Ondersteunt u Composer als afhankelijkheidsmanager voor PHP-apps?**

Ja, tijdens een Git-implementatie moet Kudu detecteren dat u een PHP-toepassing implementeert (dankzij de aanwezigheid van een composer.lock-bestand), en Kudu activeert vervolgens een installatie van een componist.

## <a name="custom-containers"></a>Aangepaste containers

**Ik gebruik mijn eigen aangepaste container. Ik wil dat het platform een `/home/` SMB-aandeel op de directory monteert.**

Als `WEBSITES_ENABLE_APP_SERVICE_STORAGE` de instelling **niet** is opgegeven `/home/` of ingesteld op *true,* wordt de map **gedeeld** over schaalinstanties en blijven de geschreven bestanden **over** het begin bestaan. Als u `WEBSITES_ENABLE_APP_SERVICE_STORAGE` expliciet instelt op *false,* wordt de houder uitgeschakeld.

**Mijn aangepaste container duurt lang om te starten en het platform start de container opnieuw op voordat deze klaar is met opstarten.**

U de tijd configureren waarop het platform wacht voordat het de container opnieuw start. Stel hiervoor de `WEBSITES_CONTAINER_START_TIME_LIMIT` app-instelling in op de gewenste waarde. De standaardwaarde is 230 seconden en de maximale waarde is 1800 seconden.

**Wat is de indeling voor de URL van de privéregisterserver?**

Geef de volledige url `http://` `https://`van het register op, inclusief of .

**Wat is het formaat voor de afbeeldingsnaam in de optie privéregister?**

Voeg de volledige afbeeldingsnaam toe, inclusief de URL van het privéregister (bijvoorbeeld myacr.azurecr.io/dotnet:latest). Afbeeldingsnamen die een aangepaste poort [gebruiken, kunnen niet via de portal worden ingevoerd.](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650) Als `docker-custom-image-name`u wilt instellen, gebruikt u het [ `az` gereedschap opdrachtregel](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set).

**Kan ik meer dan één poort op mijn aangepaste containerafbeelding weergeven?**

We ondersteunen het blootstellen van meer dan één poort niet.

**Kan ik mijn eigen opslag meenemen?**

Ja, [breng uw eigen opslag](https://docs.microsoft.com/azure/app-service/containers/how-to-serve-content-from-azure-storage) is in preview.

**Waarom kan ik niet bladeren door het bestandssysteem van mijn aangepaste container of processen uitvoeren vanaf de SCM-site?**

De SCM-site wordt uitgevoerd in een aparte container. U het bestandssysteem of de lopende processen van de app-container niet controleren.

**Mijn aangepaste container luistert naar een andere poort dan poort 80. Hoe kan ik mijn app configureren om aanvragen naar die poort te routeren?**

We hebben automatische poortdetectie. U ook een app-instelling opgeven met de naam *WEBSITES_PORT* en deze de waarde van het verwachte poortnummer geven. Voorheen gebruikte het *PORT* platform de instelling PORT-app. We zijn van plan om deze app-instelling te deprecate en *WEBSITES_PORT* uitsluitend te gebruiken.

**Moet ik HTTPS implementeren in mijn aangepaste container?**

Nee, het platform verwerkt HTTPS-beëindiging aan de gedeelde front-ends.

## <a name="multi-container-with-docker-compose"></a>Multicontainer met Docker Compose

**Hoe configureer ik Azure Container Registry (ACR) voor gebruik met meerdere containers?**

Om ACR met meerdere containers te kunnen gebruiken, moeten **alle containerafbeeldingen** op dezelfde ACR-registerserver worden gehost. Zodra ze zich op dezelfde registerserver bevinden, moet u toepassingsinstellingen maken en vervolgens het configuratiebestand Docker Compose bijwerken om de naam van de ACR-afbeelding op te nemen.

Maak de volgende toepassingsinstellingen:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (volledige URL, `https://<server-name>.azurecr.io`bijvoorbeeld)
- DOCKER_REGISTRY_SERVER_PASSWORD (beheerderstoegang inschakelen in ACR-instellingen)

Verwijs in het configuratiebestand naar uw ACR-afbeelding als het volgende voorbeeld:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Hoe weet ik welke container internettoegankelijk is?**

- Er kan slechts één container open zijn voor toegang
- Alleen poort 80 en 8080 is toegankelijk (blootgestelde poorten)

Hier zijn de regels voor het bepalen welke container toegankelijk is - in de volgorde van voorrang:

- Toepassingsinstelling `WEBSITES_WEB_CONTAINER_NAME` ingesteld op de containernaam
- De eerste container die poort 80 of 8080 definieert
- Als geen van de bovenstaande waar is, is de eerste container die in het bestand is gedefinieerd, toegankelijk (blootgesteld)


## <a name="web-sockets"></a>Websockets

Websockets worden ondersteund op Linux-apps.

> [!IMPORTANT]
> Websockets worden momenteel niet ondersteund voor Linux-apps in gratis app-serviceplannen. We werken aan het verwijderen van deze beperking en zijn van plan om maximaal 5 websocketverbindingen op gratis app-serviceplannen te ondersteunen.

## <a name="pricing-and-sla"></a>Prijzen en SLA

**Wat is de prijsstelling, nu de service algemeen beschikbaar is?**

Prijzen variëren per SKU en regio, maar u meer details zien op onze prijspagina: [App Service Pricing](https://azure.microsoft.com/pricing/details/app-service/linux/).

## <a name="other-questions"></a>Andere vragen

**Wat betekent 'Aangevraagde functie is niet beschikbaar in resourcegroep'?**

Mogelijk ziet u dit bericht bij het maken van een web-app met Azure Resource Manager (ARM). Op basis van een huidige beperking u voor dezelfde resourcegroep geen Windows- en Linux-apps in dezelfde regio mengen.

**Wat zijn de ondersteunde tekens in de namen van toepassingsinstellingen?**

U alleen letters (A-Z, a-z), getallen (0-9) en het onderteken (_) gebruiken voor toepassingsinstellingen.

**Waar kan ik nieuwe functies aanvragen?**

U uw idee indienen op het [web apps feedback forum](https://aka.ms/webapps-uservoice). Voeg "[Linux]" toe aan de titel van uw idee.

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure App Service op Linux?](app-service-linux-intro.md)
- [Faseringsomgevingen in Azure App Service instellen](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Continue implementatie met Web App voor containers](./app-service-linux-ci-cd.md)
