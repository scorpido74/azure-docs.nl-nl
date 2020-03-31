---
title: Lokale cache
description: Lees hoe de lokale cache werkt in Azure App Service en hoe u de status van de lokale cache van uw app in- en wijzigen en opvragen.
tags: optional
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.topic: article
ms.date: 03/04/2016
ms.custom: seodec18
ms.openlocfilehash: 1945730acaddb0c1c7ee1b28eeb926635efad643
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78227890"
---
# <a name="azure-app-service-local-cache-overview"></a>Overzicht van de lokale cache van Azure App Service

> [!NOTE]
> Lokale cache wordt niet ondersteund in functie-apps of gecontaineriseerde App Service-apps, zoals in [Windows Containers](app-service-web-get-started-windows-container.md) of op [App Service op Linux.](containers/app-service-linux-intro.md)


Azure App Service-inhoud wordt opgeslagen in Azure Storage en wordt op een duurzame manier opgedoken als inhoudsshare. Dit ontwerp is bedoeld om te werken met een verscheidenheid aan apps en heeft de volgende kenmerken:  

* De inhoud wordt gedeeld via meerdere vm-exemplaren (virtual machine) van de app.
* De inhoud is duurzaam en kan worden gewijzigd door apps uit te voeren.
* Logbestanden en diagnostische gegevensbestanden zijn beschikbaar onder dezelfde map met gedeelde inhoud.
* Als u nieuwe inhoud publiceert, wordt de inhoudsmap rechtstreeks bijgewerkt. U dezelfde inhoud onmiddellijk bekijken via de SCM-website en de lopende app (meestal starten sommige technologieën, zoals ASP.NET een app-herstart op sommige bestandswijzigingen om de nieuwste inhoud te krijgen).

Hoewel veel apps een of al deze functies gebruiken, hebben sommige apps alleen een inhoudsarchief met hoge prestaties nodig, waarvan ze kunnen worden uitgevoerd met hoge beschikbaarheid. Deze apps kunnen profiteren van een VM-exemplaar van een specifieke lokale cache.

De functie Lokale cache van Azure App Service biedt een webrolweergave van uw inhoud. Deze inhoud is een schrijf-maar-verwijderen cache van uw opslaginhoud die asynchroon on-site opstarten is gemaakt. Wanneer de cache klaar is, wordt de site ingeschakeld om te draaien tegen de inhoud in de cache. Apps die op lokale cache worden uitgevoerd, hebben de volgende voordelen:

* Ze zijn immuun voor latencies die optreden wanneer ze toegang krijgen tot inhoud op Azure Storage.
* Ze zijn immuun voor de geplande upgrades of ongeplande downtimes en andere onderbrekingen met Azure Storage die optreden op servers die de inhoud delen.
* Ze hebben minder app-opnieuw opstarten als gevolg van wijzigingen in het delen van opslag.

## <a name="how-the-local-cache-changes-the-behavior-of-app-service"></a>Hoe de lokale cache het gedrag van app-service verandert
* _D:\home_ wijst naar de lokale cache, die wordt gemaakt op de VM-instantie wanneer de app wordt opgestart. _D:\local_ blijft wijzen op de tijdelijke VM-specifieke opslag.
* De lokale cache bevat een eenmalige kopie van de mappen _/site-_ _en/site-extensies_ van de gedeelde inhoudsopslag, op _respectievelijk D:\home\site_ en _D:\home\siteextensions._ De bestanden worden gekopieerd naar de lokale cache wanneer de app wordt opgestart. De grootte van de twee mappen voor elke app is standaard beperkt tot 300 MB, maar u deze verhogen tot 2 GB. Als de gekopieerde bestanden groter zijn dan de grootte van de lokale cache, negeert App Service in stilte de lokale cache en leest u uit de externe bestandsshare.
* De lokale cache is read-write. Elke wijziging wordt echter verwijderd wanneer de app virtuele machines verplaatst of opnieuw wordt gestart. Gebruik de lokale cache niet voor apps die bedrijfskritieke gegevens opslaan in de inhoudswinkel.
* _D:\home\LogFiles_ en _D:\home\Data_ bevatten logbestanden en app-gegevens. De twee submappen worden lokaal opgeslagen in de VM-instantie en worden periodiek gekopieerd naar het gedeelde inhoudsarchief. Apps kunnen logboekbestanden en gegevens blijven registreren door ze naar deze mappen te schrijven. De kopie naar het gedeelde inhoudsarchief is echter het beste, dus het is mogelijk dat logbestanden en gegevens verloren gaan als gevolg van een plotselinge crash van een VM-exemplaar.
* [Log streaming](troubleshoot-diagnostic-logs.md#stream-logs) wordt beïnvloed door de best-effort kopie. U kon tot een vertraging van één minuut in de gestreamde logboeken waarnemen.
* In het gedeelde inhoudsarchief is er een wijziging in de map map map _LogFiles_ en _Gegevens_ voor apps die de lokale cache gebruiken. Er zijn nu submappen in hen die het naamgevingspatroon van "unieke identifier" + tijdstempel volgen. Elk van de submappen komt overeen met een VM-exemplaar waarbij de app wordt uitgevoerd of is uitgevoerd.
* Andere mappen in _D:\home_ blijven in de lokale cache en worden niet gekopieerd naar het gedeelde inhoudsarchief.
* App-implementatie via een ondersteunde methode publiceert rechtstreeks naar de duurzame gedeelde contentstore. Als u de mappen _D:\home\site_ en _D:\home\siteextensions_ in de lokale cache wilt vernieuwen, moet de app opnieuw worden gestart. Zie de informatie later in dit artikel om de levenscyclus naadloos te maken.
* De standaardinhoudsweergave van de SCM-site blijft die van de gedeelde inhoudsopslag.

## <a name="enable-local-cache-in-app-service"></a>Lokale cache inschakelen in app-service
U configureert lokale cache met behulp van een combinatie van gereserveerde app-instellingen. U deze app-instellingen configureren met behulp van de volgende methoden:

* [Azure-portal](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Lokale cache configureren met behulp van de Azure-portal
<a name="Configure-Local-Cache-Portal"></a>

U schakelt lokale cache per web-app in met deze app-instelling:`WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Instellingen voor Azure-portal-apps: lokale cache](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Lokale cache configureren met Azure Resource Manager
<a name="Configure-Local-Cache-ARM"></a>

```json

...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],

    "properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "300"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>De grootteinstelling wijzigen in lokale cache
Standaard is de lokale cachegrootte **300 MB.** Dit omvat de mappen /site- en/site-extensies die zijn gekopieerd uit de inhoudswinkel, evenals alle lokaal gemaakte logboeken en gegevensmappen. Als u deze limiet wilt `WEBSITE_LOCAL_CACHE_SIZEINMB`verhogen, gebruikt u de app-instelling . U de grootte verhogen tot **2 GB** (2000 MB) per app.

## <a name="best-practices-for-using-app-service-local-cache"></a>Aanbevolen procedures voor het gebruik van lokale cache van App Service
We raden u aan lokale cache te gebruiken in combinatie met de functie [Staging Environments.](../app-service/deploy-staging-slots.md)

* Voeg de *plak-app-instelling* `WEBSITE_LOCAL_CACHE_OPTION` met de waarde `Always` toe aan uw **productiesleuf.** Als u deze `WEBSITE_LOCAL_CACHE_SIZEINMB`gebruikt, voegt u deze ook toe als een kleverige instelling aan uw productiesleuf.
* Maak een **faseringssleuf** en publiceer naar de faseringssleuf. U stelt de tijdelijke sleuf doorgaans niet in om lokale cache te gebruiken om een naadloze levenscyclus van build-deploy-test in te schakelen voor fasering als u de voordelen van lokale cache voor de productiesleuf krijgt.
* Test uw site tegen uw staging-sleuf.  
* Wanneer u klaar bent, geeft u een [swapbewerking uit](../app-service/deploy-staging-slots.md#Swap) tussen uw tijdelijke en productieruimten.  
* Sticky instellingen omvatten naam en plakkerig aan een sleuf. Dus wanneer de staging-sleuf wordt omgeruild in productie, neemt deze de instellingen van de lokale cache-app over. De nieuw geruilde productiesleuf loopt na een paar minuten tegen de lokale cache en wordt opgewarmd als onderdeel van de opwarmruimte na swap. Dus wanneer de sleufswap is voltooid, wordt uw productiesleuf uitgevoerd tegen de lokale cache.

## <a name="frequently-asked-questions-faq"></a>Veelgestelde vragen

### <a name="how-can-i-tell-if-local-cache-applies-to-my-app"></a>Hoe weet ik of lokale cache van toepassing is op mijn app?
Als uw app een krachtige, betrouwbare inhoudsopslag nodig heeft, de inhoudsopslag niet gebruikt om kritieke gegevens te schrijven tijdens runtime en minder dan 2 GB in totale grootte is, dan is het antwoord "ja"! Als u de totale grootte van uw mappen /site en /site-extensies wilt krijgen, u de site-extensie 'Schijfgebruik Azure Web Apps' gebruiken.

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Hoe weet ik of mijn site is overgestapt op het gebruik van lokale cache?
Als u de functie Lokale cache met tijdelijke omgevingen gebruikt, wordt de swapbewerking pas voltooid nadat de lokale cache is opgewarmd. Als u wilt controleren of uw site wordt uitgevoerd in `WEBSITE_LOCALCACHE_READY`vergelijking met lokale cache, u de variabele procesomgeving van de werknemer controleren. Gebruik de variabele pagina van de [werkprocesomgeving](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) om toegang te krijgen tot de variabele van de werkprocesomgeving op meerdere instanties.  

### <a name="i-just-published-new-changes-but-my-app-does-not-seem-to-have-them-why"></a>Ik heb net nieuwe wijzigingen gepubliceerd, maar mijn app lijkt ze niet te hebben. Hoe komt dat?
Als uw app lokale cache gebruikt, moet u uw site opnieuw starten om de laatste wijzigingen op te vragen. Wilt u geen wijzigingen in een productiesite publiceren? Bekijk de slotopties in de vorige sectie best practices.

### <a name="where-are-my-logs"></a>Waar zijn mijn logboeken?
Met Lokale cache zien uw logboeken en gegevensmappen er een beetje anders uit. De structuur van uw submappen blijft echter hetzelfde, behalve dat de submappen zich onder een submap bevinden met de indeling "unieke VM-id" + tijdstempel.

### <a name="i-have-local-cache-enabled-but-my--app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Ik heb Lokale cache ingeschakeld, maar mijn app wordt nog steeds opnieuw opgestart. De reden hiervoor? Ik dacht dat lokale cache geholpen met frequente app opnieuw opstart.
Lokale cache helpt wel voorkomen dat apps opnieuw worden opgestart met opslag. Uw app kan echter nog steeds opnieuw worden opgestart tijdens geplande infrastructuurupgrades van de VM. De algemene app opnieuw wordt opgestart die u met Lokale cache hebt ingeschakeld, moet minder zijn.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>Sluit Local Cache uit dat mappen worden gekopieerd naar de snellere lokale schijf?
Als onderdeel van de stap die de opslaginhoud kopieert, is elke map met de naam repository uitgesloten. Dit helpt bij scenario's waarin uw site-inhoud een bronbeheeropslagplaats kan bevatten die mogelijk niet nodig is in de dagelijkse werking van de app. 
