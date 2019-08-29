---
title: Uitgebracht voor besturingssysteem-en runtime patches-Azure App Service | Microsoft Docs
description: Hierin wordt beschreven hoe Azure App Service het besturings systeem en de runtime bijwerkt en hoe u update aankondigingen kunt ophalen.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 3469c4f11a075ceb958e35e4cfc87a78e60b3882
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074136"
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>Patches voor besturings systeem en uitvoering in Azure App Service

In dit artikel leest u hoe u bepaalde versie gegevens kunt verkrijgen over het besturings systeem of de software in [app service](overview.md). 

App Service is een platform-as-a-service. Dit betekent dat het besturings systeem en de toepassings stack voor u door Azure worden beheerd. u kunt uw toepassing en de bijbehorende gegevens alleen beheren. Meer controle over het besturings systeem en toepassings stack is beschikbaar in [Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/). In het geval is het toch handig voor u als App Service gebruiker om meer informatie te weten, zoals:

-   Hoe en wanneer worden besturingssysteem updates toegepast?
-   Hoe worden App Service patches uitgevoerd op basis van belang rijke beveiligings problemen (zoals Zero-Day)?
-   Welke versies van het besturings systeem en runtime worden uw apps uitgevoerd?

Uit veiligheids overwegingen worden bepaalde specifieke beveiligings gegevens niet gepubliceerd. Het artikel is echter van belang om problemen op te lossen door de transparantie van het proces te maximaliseren en te bepalen hoe u op het gebied van beveiligings meldingen of runtime-updates up-to-date kunt blijven.

## <a name="how-and-when-are-os-updates-applied"></a>Hoe en wanneer worden besturingssysteem updates toegepast?

Azure beheert patching van het besturings systeem op twee niveaus, de fysieke servers en de virtuele gast machines (Vm's) die de App Service resources uitvoeren. Beide worden maandelijks bijgewerkt, wat wordt uitgelijnd op de planning van de maandelijkse [patch-dinsdag](https://technet.microsoft.com/security/bulletins.aspx) . Deze updates worden automatisch toegepast, op een manier waarmee de SLA met hoge Beschik baarheid van Azure-Services wordt gegarandeerd. 

Voor gedetailleerde informatie over hoe updates worden toegepast, Zie [ontrafelen The Magic achter app service updates van het besturings systeem](https://azure.github.io/AppService/2018/01/18/Demystifying-the-magic-behind-App-Service-OS-updates.html).

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Hoe behandelt Azure aanzienlijke beveiligings problemen?

Wanneer ernstige beveiligings problemen onmiddellijke patches vereisen, zoals [problemen met een nul dag](https://wikipedia.org/wiki/Zero-day_(computing)), worden de updates met hoge prioriteit per geval afgehandeld.

Blijf op de hoogte met essentiële beveiligings aankondigingen in azure door naar [Azure Security Blog](https://azure.microsoft.com/blog/topics/security/)te gaan. 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>Wanneer worden ondersteunde taal Runtimes bijgewerkt, toegevoegd of afgeschaft?

Nieuwe stabiele versies van ondersteunde taal Runtimes (primair, secundair of patch) worden regel matig toegevoegd aan App Service exemplaren. Sommige updates overschrijven de bestaande installatie, terwijl anderen naast bestaande versies worden geïnstalleerd. Een overschrijvings installatie betekent dat uw app automatisch wordt uitgevoerd op de bijgewerkte runtime. Een gelijktijdige installatie betekent dat u uw app hand matig moet migreren om te profiteren van een nieuwe runtime versie. Zie een van de subsecties voor meer informatie.

Runtime-updates en afschaffing worden hier aangekondigd:

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> Hier vindt u informatie over taal runtime die in een App Service-app is ingebouwd. Een aangepaste runtime die u uploadt naar App Service, blijft bijvoorbeeld ongewijzigd, tenzij u deze hand matig bijwerkt.
>
>

### <a name="new-patch-updates"></a>Nieuwe patch updates

Patch-updates voor .NET, PHP, Java SDK of Tomcat/jetty-versie worden automatisch toegepast door de bestaande installatie te overschrijven met de nieuwe versie. Updates voor node. js-patches worden naast de bestaande versies geïnstalleerd (vergelijkbaar met de primaire en secundaire versies in de volgende sectie). Nieuwe python-patch versies kunnen hand matig worden geïnstalleerd via [site-extensies](https://www.siteextensions.net/packages?q=Tags%3A%22python%22)), naast de ingebouwde python-installaties.

### <a name="new-major-and-minor-versions"></a>Nieuwe primaire en secundaire versies

Wanneer een nieuwe primaire of secundaire versie wordt toegevoegd, wordt deze naast de bestaande versies geïnstalleerd. U kunt uw app hand matig bijwerken naar de nieuwe versie. Als u de runtime versie in een configuratie bestand (zoals `web.config` en `package.json`) hebt geconfigureerd, moet u een upgrade uitvoeren met dezelfde methode. Als u een App Service instelling hebt gebruikt om uw runtime versie te configureren, kunt u deze wijzigen in de [Azure Portal](https://portal.azure.com) of door een [Azure cli](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) -opdracht uit te voeren in de [Cloud shell](../cloud-shell/overview.md), zoals wordt weer gegeven in de volgende voor beelden:

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.4 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>Afgeschafte versies  

Wanneer een oudere versie wordt afgeschaft, wordt de verwijderings datum aangekondigd zodat u de upgrade van runtime versie dienovereenkomstig kunt plannen. 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>Hoe kan ik een query uitvoeren op de status van het besturings systeem en de runtime-update voor mijn instanties?  

Hoewel essentiële besturingssysteem gegevens worden vergrendeld vanuit Access (Zie [functionaliteit van het besturings systeem op Azure app service](operating-system-functionality.md)), kunt u met de [kudu-console](https://github.com/projectkudu/kudu/wiki/Kudu-console) uw app service exemplaar opvragen met betrekking tot de versie van het besturings systeem en de runtime versie. 

In de volgende tabel ziet u hoe de versies van Windows en de taal runtime worden uitgevoerd op uw apps:

| Information | Waar u het kunt vinden | 
|-|-|
| Windows-versie | Zie `https://<appname>.scm.azurewebsites.net/Env.cshtml` (onder systeem info) |
| .NET-versie | `https://<appname>.scm.azurewebsites.net/DebugConsole`Voer de volgende opdracht uit in de opdracht prompt: <br>`powershell -command "gci 'Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Net Framework Setup\NDP\CDF'"` |
| .NET core-versie | `https://<appname>.scm.azurewebsites.net/DebugConsole`Voer de volgende opdracht uit in de opdracht prompt: <br> `dotnet --version` |
| PHP-versie | `https://<appname>.scm.azurewebsites.net/DebugConsole`Voer de volgende opdracht uit in de opdracht prompt: <br> `php --version` |
| Standaard versie van node. js | Voer in het [Cloud shell](../cloud-shell/overview.md)de volgende opdracht uit: <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Python-versie | `https://<appname>.scm.azurewebsites.net/DebugConsole`Voer de volgende opdracht uit in de opdracht prompt: <br> `python --version` |  

> [!NOTE]  
> Toegang tot de register `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages`locatie, waar informatie over [KB](https://docs.microsoft.com/security-updates/SecurityBulletins/securitybulletins) -patches wordt opgeslagen, is vergrendeld.
>
>

## <a name="more-resources"></a>Meer bronnen

[Vertrouwens centrum: Beveiliging](https://www.microsoft.com/en-us/trustcenter/security)  
[64-bits ASP.NET Core op Azure App Service](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7)
