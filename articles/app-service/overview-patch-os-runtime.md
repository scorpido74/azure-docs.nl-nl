---
title: OS en runtime patchen cadans
description: Lees hoe Azure App Service het besturingssysteem en de runtimes, welke runtimes en patchniveau uw apps hebben, en hoe u update-aankondigingen ontvangen.
ms.topic: article
ms.date: 02/02/2018
ms.custom: seodec18
ms.openlocfilehash: 597964914f4022899ab027b735ec6932105497b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273624"
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>Patchen van besturingssysteem en runtime in Azure App Service

In dit artikel ziet u hoe u bepaalde versie-informatie over het besturingssysteem of de software in [App Service kunt](overview.md)krijgen. 

App Service is een Platform-as-a-Service, wat betekent dat de OS en de toepassingsstack voor u worden beheerd door Azure; u alleen uw toepassing en de bijbehorende gegevens beheert. Meer controle over de os- en toepassingsstack is beschikbaar in [Azure Virtual Machines.](https://docs.microsoft.com/azure/virtual-machines/) Met dat in gedachten is het toch nuttig voor u als app-servicegebruiker om meer informatie te weten, zoals:

-   Hoe en wanneer worden OS-updates toegepast?
-   Hoe wordt App Service gepatcht tegen aanzienlijke kwetsbaarheden (zoals zero-day)?
-   Welke os- en runtime-versies worden uw apps uitgevoerd?

Om veiligheidsredenen worden bepaalde specifieke kenmerken van beveiligingsinformatie niet gepubliceerd. Het artikel is echter bedoeld om de zorgen te verlichten door de transparantie over het proces te maximaliseren en hoe u op de hoogte blijven van beveiligingsgerelateerde aankondigingen of runtime-updates.

## <a name="how-and-when-are-os-updates-applied"></a>Hoe en wanneer worden OS-updates toegepast?

Azure beheert het patchen van besturingssysteem op twee niveaus, de fysieke servers en de virtuele machines (VM's) van de gast waarop de App Service-resources worden uitgevoerd. Beide worden maandelijks bijgewerkt, wat overeenkomt met het maandelijkse [Patch Tuesday-schema.](https://technet.microsoft.com/security/bulletins.aspx) Deze updates worden automatisch toegepast, op een manier die de sla van Azure-services met hoge beschikbaarheid garandeert. 

Zie [Demystifying de magie achter App Service OS-updates](https://azure.github.io/AppService/2018/01/18/Demystifying-the-magic-behind-App-Service-OS-updates.html)voor gedetailleerde informatie over hoe updates worden toegepast.

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Hoe gaat Azure om met belangrijke kwetsbaarheden?

Wanneer ernstige kwetsbaarheden onmiddellijke patching vereisen, zoals [zero-day kwetsbaarheden,](https://wikipedia.org/wiki/Zero-day_(computing))worden de updates met hoge prioriteit per geval afgehandeld.

Blijf op de hoogte van kritieke beveiligingsaankondigingen in Azure door azure [security blog](https://azure.microsoft.com/blog/topics/security/)te bezoeken. 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>Wanneer worden ondersteunde taalgebruikstijden bijgewerkt, toegevoegd of afgeschaft?

Nieuwe stabiele versies van ondersteunde taalgebruikstijden (groot, klein of patch) worden periodiek toegevoegd aan App Service-exemplaren. Sommige updates overschrijven de bestaande installatie, terwijl andere naast bestaande versies worden geïnstalleerd. Een overschrijfinstallatie betekent dat uw app automatisch wordt uitgevoerd op de bijgewerkte runtime. Een side-by-side installatie betekent dat u uw app handmatig moet migreren om te profiteren van een nieuwe runtime-versie. Zie voor meer informatie een van de onderafdelingen.

Runtime updates en afschrijvingen worden hier aangekondigd:

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> Informatie is hier van toepassing op taalgebruikstijden die zijn ingebouwd in een App Service-app. Een aangepaste runtime die u uploadt naar bijvoorbeeld App Service, blijft ongewijzigd, tenzij u deze handmatig upgradet.
>
>

### <a name="new-patch-updates"></a>Nieuwe patchupdates

Patchupdates voor .NET, PHP, Java SDK of Tomcat/Jetty-versie worden automatisch toegepast door de bestaande installatie met de nieuwe versie te overschrijven. Node.js patch updates worden naast de bestaande versies geïnstalleerd (vergelijkbaar met grote en kleine versies in de volgende sectie). Nieuwe Python-patchversies kunnen handmatig worden geïnstalleerd via [site-extensies,](https://azure.microsoft.com/blog/azure-web-sites-extensions/)naast de ingebouwde Python-installaties.

### <a name="new-major-and-minor-versions"></a>Nieuwe grote en kleine versies

Wanneer een nieuwe grote of secundaire versie wordt toegevoegd, wordt deze naast de bestaande versies geïnstalleerd. U uw app handmatig upgraden naar de nieuwe versie. Als u de runtime-versie hebt geconfigureerd `web.config` in `package.json`een configuratiebestand (zoals en ), moet u met dezelfde methode upgraden. Als u een app-service-instelling hebt gebruikt om uw runtime-versie te configureren, u deze wijzigen in de [Azure-portal](https://portal.azure.com) of door een opdracht [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) uit te voeren in de [Cloud Shell](../cloud-shell/overview.md), zoals weergegeven in de volgende voorbeelden:

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.4 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>Afgeschafte versies  

Wanneer een oudere versie wordt afgeschaft, wordt de verwijderingsdatum aangekondigd, zodat u uw upgrade van de runtime-versie dienovereenkomstig plannen. 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>Hoe kan ik de status van besturingssysteem en runtime-update op mijn exemplaren opvragen?  

Terwijl kritieke besturingssysteemgegevens zijn vergrendeld op basis van toegang (zie [Functie van het besturingssysteem op Azure App Service),](operating-system-functionality.md)u met de [Kudu-console](https://github.com/projectkudu/kudu/wiki/Kudu-console) uw App Service-instantie opvragen met betrekking tot de versie van het besturingssysteem en de runtime-versies. 

In de volgende tabel ziet u hoe u de versies van Windows en de taalruntime waarop uw apps worden uitgevoerd, wordt weergegeven:

| Informatie | Waar te vinden | 
|-|-|
| Windows-versie | Zie `https://<appname>.scm.azurewebsites.net/Env.cshtml` (onder Systeeminfo) |
| .NET-versie | Voer `https://<appname>.scm.azurewebsites.net/DebugConsole`bij de volgende opdracht de volgende opdracht uit in de opdrachtprompt: <br>`powershell -command "gci 'Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Net Framework Setup\NDP\CDF'"` |
| .NET Core-versie | Voer `https://<appname>.scm.azurewebsites.net/DebugConsole`bij de volgende opdracht de volgende opdracht uit in de opdrachtprompt: <br> `dotnet --version` |
| PHP-versie | Voer `https://<appname>.scm.azurewebsites.net/DebugConsole`bij de volgende opdracht de volgende opdracht uit in de opdrachtprompt: <br> `php --version` |
| Standaardversie Node.js | Voer in de [Cloud Shell](../cloud-shell/overview.md)de volgende opdracht uit: <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Python-versie | Voer `https://<appname>.scm.azurewebsites.net/DebugConsole`bij de volgende opdracht de volgende opdracht uit in de opdrachtprompt: <br> `python --version` |  
| Java-versie | Voer `https://<appname>.scm.azurewebsites.net/DebugConsole`bij de volgende opdracht de volgende opdracht uit in de opdrachtprompt: <br> `java -version` |  

> [!NOTE]  
> Toegang tot `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages`registerlocatie , waar informatie over ["KB" patches](https://docs.microsoft.com/security-updates/SecurityBulletins/securitybulletins) wordt opgeslagen, is vergrendeld.
>
>

## <a name="more-resources"></a>Meer bronnen

[Vertrouwenscentrum: beveiliging](https://www.microsoft.com/en-us/trustcenter/security)  
[64-ASP.NET Core op Azure App Service](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7)
