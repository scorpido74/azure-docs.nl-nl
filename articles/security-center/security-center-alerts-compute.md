---
title: Detectie van bedreigingen voor de eigen Cloud Compute in Azure Security Center | Microsoft Docs
description: Dit onderwerp bevat de eigen Cloud Compute-waarschuwingen die beschikbaar zijn in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: memildin
ms.openlocfilehash: c3fcbadf93ff72f7d2a1dca3b25ace81c9d4f1ae
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202622"
---
# <a name="threat-detection-for-cloud-native-compute-in-azure-security-center"></a>Detectie van bedreigingen voor de systeem eigen Cloud Compute in Azure Security Center

Azure Security Center maakt gebruik van de unieke zicht baarheid van een Cloud provider voor het analyseren van interne logboeken en het identificeren van een aanvals methodologie op meerdere doelen. Dit onderwerp bevat de waarschuwingen die beschikbaar zijn voor de volgende Azure-Services:

* [Azure App Service](#app-services)
* [Azure Container Service](#azure-containers) 

## Azure App Service<a name="app-services"></a>

Security Center gebruikt de schaal van de cloud om aanvallen te identificeren die gericht zijn op toepassingen die meer dan App Service. Webtoepassingen zijn gebruikelijk in moderne netwerken en aanvallers testen deze en zwakke plekken. Aanvragen voor toepassingen die in Azure worden uitgevoerd, worden voordat ze naar specifieke omgevingen worden doorgestuurd via verschillende gateways, waar ze worden geïnspecteerd en geregistreerd. Deze gegevens worden vervolgens gebruikt om aanvallen en aanvallers te identificeren en om nieuwe patronen te leren die later zullen worden gebruikt.

Door de zicht baarheid van Azure als een Cloud provider te gebruiken, Security Center analyseert App Service interne Logboeken om een aanvals methodologie op meerdere doelen te identificeren. Zo omvat de methodologie uitgebreide scans en gedistribueerde aanvallen. Dit type aanval is doorgaans afkomstig uit een kleine subset van IP-adressen en vertoont patronen van het verkennen naar vergelijk bare eind punten op meerdere hosts. De aanvallen zoeken naar een kwets bare pagina of invoeg toepassing en kunnen niet worden geïdentificeerd op basis van het standpunt van één host.

Security Center heeft ook toegang tot de onderliggende sandboxes en virtuele machines. Samen met het geheugen forensische kan de infra structuur het verhaal vertellen, van een nieuwe aanval die in het wild wordt circuleren tot inbreuk op de klant machines. Daarom kunnen Security Center voor het detecteren van aanvallen op webtoepassingen lang duren nadat ze zijn misbruikt.

> [!div class="mx-tableFixed"]

|Waarschuwing|Description|
|---|---|
|**Aanroep van verdacht WordPress-thema gedetecteerd**|Het App Service activiteiten logboek duidt op een mogelijke code-injectie activiteit op uw App Service-Resource.<br/> Deze verdachte activiteit lijkt op een activiteit die een WordPress-thema bewerkt ter ondersteuning van de uitvoering van code aan de server zijde, gevolgd door een directe webaanvraag om het gemanipuleerde thema bestand aan te roepen. Dit type activiteit kan deel uitmaken van een aanvals campagne via WordPress.|
|**Verbinding met webpagina van afwijkend IP-adres gedetecteerd**|Het App Service activiteiten logboek duidt op een verbinding met een gevoelige webpagina van een bron adres dat nooit eerder verbinding heeft gemaakt met deze pagina. Dit kan erop wijzen dat iemand een beveiligings aanval probeert uit te voeren in de beheer pagina's van uw web-app. Het kan ook het gevolg zijn van een rechtmatige gebruiker met behulp van een nieuw IP-adres.|
|**Er is een IP-adres gevonden dat is verbonden met uw Azure App Service FTP-interface in bedreigings informatie**|App Service FTP-logboeken analyse heeft een verbinding gedetecteerd van een bron adres dat is gevonden in de feed voor bedreigings informatie. Tijdens deze verbinding heeft een gebruiker toegang tot de pagina's die worden weer gegeven.|
|**Webvingerafdruking gedetecteerd**|Het App Service activiteiten logboek duidt op een mogelijke Web-vingerafdruk activiteit op uw App Service-bron. <br/>Deze verdachte activiteit is gekoppeld aan een hulp programma met de naam blind Elephant. Het hulp programma vervingerafdrukt webservers en probeert de geïnstalleerde toepassingen en hun versies te detecteren. Aanvallers gebruiken dit hulp programma vaak om de webtoepassingen te zoeken om beveiligings problemen op te sporen.|
|**Verdachte toegang tot mogelijk kwets bare webpagina gedetecteerd**|In het App Service activiteiten logboek wordt aangegeven dat een webpagina die vertrouwelijk lijkt te zijn geopend. <br/>Deze verdachte activiteit is afkomstig van een bron adres waarvan het toegangs patroon op een webscanner lijkt. Dit soort activiteit is vaak gekoppeld aan een poging van een aanvaller om uw netwerk te scannen om te proberen toegang te krijgen tot gevoelige of kwets bare webpagina's.|
|**PHP-bestand in uploadmap**|Het App Service activiteiten logboek geeft aan dat er een verdachte PHP-pagina is geopend in de uploadmap. <br/>Dit type map bevat doorgaans geen PHP-bestanden. Het bestaan van dit type bestand kan duiden op een voor deel dat er sprake is van een probleem met het uploaden van wille keurige bestanden.|
|**Een poging om Linux-opdrachten uit te voeren op een Windows-App Service**|Tijdens de analyse van App Service processen is een poging tot het uitvoeren van een Linux-opdracht op een Windows-App Service gedetecteerd. Deze actie is uitgevoerd door de webtoepassing. Dit gedrag wordt vaak weer gegeven tijdens campagnes die misbruik maken van een beveiligingslek in een gemeen schappelijke webtoepassing.|
|**Verdachte PHP-uitvoering gedetecteerd**|Computer logboeken geven aan dat een verdacht PHP-proces wordt uitgevoerd. De actie bevat een poging om de opdrachten van het besturings systeem of PHP-code uit te voeren vanaf de opdracht regel met behulp van het PHP-proces. Dit gedrag kan legitiem zijn, in webtoepassingen dit gedrag kan wijzen op schadelijke activiteiten, zoals pogingen om websites te infecteren met webshells.|
|**Uitvoering van tijdelijke map verwerken**|App Service analyse van processen heeft een uitvoering van een proces in de tijdelijke map van de app gedetecteerd. Hoewel dit gedrag legitiem kan zijn, kunnen in webtoepassingen dit gedrag duiden op schadelijke activiteiten.|
|**Poging tot het uitvoeren van een opdracht met hoge bevoegdheden gedetecteerd**|Tijdens de analyse van App Service processen is een poging tot het uitvoeren van een opdracht met hoge bevoegdheden gedetecteerd. De opdracht is uitgevoerd in de context van de webtoepassing. Hoewel dit gedrag legitiem kan zijn, kunnen in webtoepassingen dit gedrag duiden op schadelijke activiteiten.|

> [!NOTE]
> Security Center detectie van bedreigingen voor App Service is momenteel niet beschikbaar in azure Government en soevereine Cloud regio's.

## Azure Container Service<a name="azure-containers"></a>

Security Center biedt in realtime detectie van bedreigingen voor uw containers op Linux-machines op basis van het gecontroleerde Framework. De waarschuwingen identificeren verschillende verdachte docker-activiteiten, zoals het maken van een geprivilegieerde container op een host, een indicatie van een SSH-server (Secure Shell) die binnen een docker-container of het gebruik van crypto grafie-Miners. 

U kunt deze informatie gebruiken om snel beveiligingsproblemen op te lossen en om de beveiliging van uw containers te verbeteren. Naast Linux-detecties biedt Security Center ook analyses die specifiek zijn voor implementaties van containers.
