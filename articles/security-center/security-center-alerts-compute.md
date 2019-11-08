---
title: Detectie van bedreigingen voor systeem eigen Cloud Computing in Azure Security Center | Microsoft Docs
description: In dit artikel vindt u de eigen Cloud Compute-waarschuwingen die beschikbaar zijn in Azure Security Center.
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
ms.date: 11/05/2019
ms.author: memildin
ms.openlocfilehash: 6b6acb0ae1452795fe02906779b920e4b41f9a55
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748388"
---
# <a name="threat-detection-for-cloud-native-computing-in-azure-security-center"></a>Detectie van bedreigingen voor systeem eigen Cloud Computing in Azure Security Center

Als systeem eigen oplossing heeft Azure Security Center unieke zicht baarheid in interne logboeken voor het identificeren van een aanvals methodologie op meerdere doelen. Dit artikel bevat de waarschuwingen die beschikbaar zijn voor de volgende Azure-Services:

* [Azure App Service](#app-services)
* [Azure-containers](#azure-containers) 

> [!NOTE]
> Deze services zijn momenteel niet beschikbaar in azure Government en soevereine Cloud regio's.

## Azure App Service<a name="app-services"></a>

Security Center gebruikt de schaal van de cloud om aanvallen te identificeren die gericht zijn op toepassingen die meer dan App Service. Aanvallers kunnen webtoepassingen testen om zwakke plekken te vinden en misbruik te maken. Aanvragen voor toepassingen die in Azure worden uitgevoerd, worden voordat ze naar specifieke omgevingen worden doorgestuurd via verschillende gateways, waar ze worden geïnspecteerd en geregistreerd. Deze gegevens worden vervolgens gebruikt om aanvallen en aanvallers te identificeren en om nieuwe patronen te leren die later zullen worden gebruikt.

Door de zicht baarheid van Azure als een Cloud provider te gebruiken, Security Center analyseert App Service interne Logboeken om een aanvals methodologie op meerdere doelen te identificeren. Zo omvat de methodologie uitgebreide scans en gedistribueerde aanvallen. Dit type aanval is doorgaans afkomstig uit een kleine subset van IP-adressen en toont patronen van het verkennen naar vergelijk bare eind punten op meerdere hosts. De aanvallen zoeken naar een kwets bare pagina of invoeg toepassing en kunnen niet worden geïdentificeerd op basis van het standpunt van één host.

Security Center heeft ook toegang tot de onderliggende sandboxes en virtuele machines. Samen met het geheugen forensische kan de infra structuur het verhaal vertellen, van een nieuwe aanval die in het wild wordt circuleren tot inbreuk op de klant machines. Daarom, zelfs als Security Center wordt geïmplementeerd nadat een web-app is misbruikt, kan het mogelijk worden doorlopende aanvallen te detecteren.

> [!div class="mx-tableFixed"]

|Waarschuwing|Beschrijving|
|---|---|
|**Aanroep van verdacht WordPress-thema gedetecteerd**|Het App Service activiteiten logboek duidt op een mogelijke code-injectie activiteit op uw App Service-Resource.<br/> Deze verdachte activiteit lijkt op een activiteit die een WordPress-thema bewerkt ter ondersteuning van de uitvoering van code aan de server zijde, gevolgd door een directe webaanvraag om het gemanipuleerde thema bestand aan te roepen. Dit type activiteit kan deel uitmaken van een aanvals campagne via WordPress.|
|**Verbinding met webpagina van afwijkend IP-adres gedetecteerd**|Het App Service activiteiten logboek duidt op een verbinding met een gevoelige webpagina van een bron adres dat nooit eerder verbinding heeft gemaakt met deze pagina. Deze verbinding kan erop wijzen dat iemand probeert een beveiligings aanval uit te voeren op de beheer pagina's van uw web-app. Het kan ook het gevolg zijn van een rechtmatige gebruiker met behulp van een nieuw IP-adres.|
|**Er is een IP-adres gevonden dat is verbonden met uw Azure App Service FTP-interface in bedreigings informatie**|App Service FTP-logboeken analyse heeft een verbinding gedetecteerd van een bron adres dat is gevonden in de feed voor bedreigings informatie. Tijdens deze verbinding heeft een gebruiker toegang tot de pagina's die worden weer gegeven.|
|**Webvingerafdruking gedetecteerd**|Het App Service activiteiten logboek duidt op een mogelijke Web-vingerafdruk activiteit op uw App Service-bron. <br/>Deze verdachte activiteit is gekoppeld aan een hulp programma met de naam blind Elephant. Het hulp programma vervingerafdrukt webservers en probeert de geïnstalleerde toepassingen en hun versies te detecteren. Aanvallers gebruiken dit hulp programma vaak om de webtoepassingen te zoeken om beveiligings problemen op te sporen.|
|**Verdachte toegang tot mogelijk kwets bare webpagina gedetecteerd**|In het App Service activiteiten logboek wordt aangegeven dat een webpagina die vertrouwelijk lijkt te zijn geopend. <br/>Deze verdachte activiteit is afkomstig van een bron adres waarvan het toegangs patroon op een webscanner lijkt. Dit soort activiteit is vaak gekoppeld aan een poging van een aanvaller om uw netwerk te scannen om toegang te krijgen tot gevoelige of kwets bare webpagina's.|
|**PHP-bestand in uploadmap**|Het App Service activiteiten logboek geeft aan dat er een verdachte PHP-pagina is geopend in de uploadmap. <br/>Dit type map bevat doorgaans geen PHP-bestanden. Het bestaan van dit type bestand kan duiden op een voor deel dat er sprake is van een probleem met het uploaden van wille keurige bestanden.|
|**Een poging om Linux-opdrachten uit te voeren op een Windows-App Service**|Tijdens de analyse van App Service processen is een poging tot het uitvoeren van een Linux-opdracht op een Windows-App Service gedetecteerd. Deze actie is uitgevoerd door de webtoepassing. Dit gedrag wordt vaak weer gegeven tijdens campagnes die misbruik maken van een beveiligingslek in een gemeen schappelijke webtoepassing.|
|**Verdachte PHP-uitvoering gedetecteerd**|Computer logboeken geven aan dat een verdacht PHP-proces wordt uitgevoerd. De actie bevat een poging om de opdrachten van het besturings systeem of PHP-code uit te voeren vanaf de opdracht regel met behulp van het PHP-proces. Dit gedrag kan legitiem zijn, in webtoepassingen dit gedrag kan wijzen op schadelijke activiteiten, zoals pogingen om websites te infecteren met webshells.|
|**Uitvoering van tijdelijke map verwerken**|App Service analyse van processen heeft een uitvoering van een proces in de tijdelijke map van de app gedetecteerd. Hoewel dit gedrag legitiem kan zijn, kunnen in webtoepassingen dit gedrag duiden op schadelijke activiteiten.|
|**Poging tot het uitvoeren van een opdracht met hoge bevoegdheden gedetecteerd**|Tijdens de analyse van App Service processen is een poging tot het uitvoeren van een opdracht met hoge bevoegdheden gedetecteerd. De opdracht is uitgevoerd in de context van de webtoepassing. Hoewel dit gedrag legitiem kan zijn, kunnen in webtoepassingen dit gedrag duiden op schadelijke activiteiten.|

## Azure-containers<a name="azure-containers"></a>

Security Center voorziet in realtime detectie van bedreigingen voor uw container omgevingen en genereert waarschuwingen voor verdachte activiteiten. U kunt deze informatie gebruiken om snel beveiligingsproblemen op te lossen en om de beveiliging van uw containers te verbeteren.

We detecteren bedreigingen op verschillende niveaus: 

* **Host level** -Security Center Agent (Zie de [prijs](security-center-pricing.md) informatie voor de Standard-laag) wordt Linux gecontroleerd op verdachte activiteiten. De agent activeert waarschuwingen voor verdachte activiteiten die afkomstig zijn van het knoop punt of een container die erop wordt uitgevoerd. Voor beelden van dergelijke activiteiten zijn Webshell detectie en verbinding met bekende verdachte IP-adressen.

    Voor een diep gaande inzicht in de beveiliging van uw container omgeving bewaakt de agent de container-specifieke analyses. Er worden waarschuwingen geactiveerd voor gebeurtenissen, zoals het maken van geprivilegieerde containers, verdachte toegang tot API-servers en SSH-servers (Secure Shell) die binnen een docker-container worden uitgevoerd.

    >[!NOTE]
    > Als u ervoor kiest om de agents op uw hosts niet te installeren, ontvangt u alleen een subset van de voor delen en waarschuwingen voor bedreigingen detectie. U ontvangt nog steeds waarschuwingen met betrekking tot netwerk analyse en communicatie met schadelijke servers.

* Voor **AKS-cluster niveau**is er sprake van detectie van bedreigingen op basis van de analyse van Kubernetes-controle Logboeken. Als u deze bewaking zonder **agents** wilt inschakelen, voegt u de optie Kubernetes toe aan uw abonnement op de pagina met **prijs & instellingen** (Zie [prijzen](security-center-pricing.md)). Als u waarschuwingen op dit niveau wilt genereren, controleert Security Center uw door AKS beheerde services met de logboeken die zijn opgehaald door AKS. Voor beelden van gebeurtenissen op dit niveau zijn onder andere weer gegeven Kubernetes-Dash boards, het maken van rollen met hoge bevoegdheden en het maken van gevoelige koppels.

    >[!NOTE]
    > Security Center genereert detectie waarschuwingen voor Azure Kubernetes-service acties en implementaties die worden uitgevoerd nadat de optie Kubernetes is ingeschakeld op de abonnements instellingen. 

Het wereld wijde team van beveiligings onderzoekers bewaakt ook voortdurend de bedreigings landschap. Ze voegen container-specifieke waarschuwingen en beveiligings problemen toe wanneer ze worden gedetecteerd.


### <a name="aks-cluster-level-alerts"></a>Waarschuwingen op AKS-cluster niveau

> [!div class="mx-tableFixed"]

|Waarschuwing|Beschrijving|
|---|---|
|**PREVIEW-functie binding met de rol cluster-beheerder gedetecteerd**|Kubernetes-controle logboek analyse heeft een nieuwe binding met de rol cluster beheerder gedetecteerd, wat resulteert in Administrator bevoegdheden. Het is niet noodzakelijkerwijs mogelijk om beheerders bevoegdheden te bieden in het cluster.|
|**VOOR beeld-weer gegeven Kubernetes-dash board gedetecteerd**|Kubernetes-controle logboek analyse heeft de bloot stelling van het Kubernetes-dash board door een Load Balancer-service gedetecteerd. Met beschik bare Dash boards kunt u niet-geverifieerde toegang tot het cluster beheer en een beveiligings risico vormen.|
|**VOOR beeld-nieuwe rol met hoge bevoegdheden gedetecteerd**|Kubernetes-controle logboek analyse heeft een nieuwe rol met hoge bevoegdheden gedetecteerd. Een binding met een rol met hoge bevoegdheden geeft de gebruiker/groep verhoogde bevoegdheden in het cluster. Het is niet nood zakelijk dat verhoogde bevoegdheden worden geboden, maar kan leiden tot problemen met de escalatie van bevoegdheden in het cluster.|
|**PREVIEW-nieuwe container in de uitvoeren-naam ruimte gedetecteerd**|Kubernetes-controle logboek analyse heeft een nieuwe container gedetecteerd in de uitvoeren-systeem naam ruimte die niet voor komt in de containers die normaal gesp roken in deze naam ruimte worden uitgevoerd. De uitvoeren-naam ruimten mogen geen gebruikers resources bevatten. Aanvallers kunnen deze naam ruimte gebruiken om schadelijke onderdelen te verbergen.|
|**PREVIEW-analyse container voor digitale valuta gedetecteerd**|Kubernetes-controle logboek analyse heeft een container gedetecteerd met een installatie kopie die is gekoppeld aan een hulp programma voor het analyseren van digitale valuta.|
|**VOOR beeld: geprivilegieerde container gedetecteerd**|Er is een nieuwe geprivilegieerde container gedetecteerd door de analyse van Kubernetes-controle Logboeken. Een geprivilegieerde container heeft toegang tot de resources van het knoop punt en verbreekt de isolatie tussen containers. Als er is geknoeid, kan een aanvaller de geprivilegieerde container gebruiken om toegang te krijgen tot het knoop punt.|
|**PREVIEW-container met een gevoelige volume koppeling gedetecteerd**|Kubernetes-controle logboek analyse heeft een nieuwe container met een gevoelige volume koppeling gedetecteerd. Het gedetecteerde volume is een hostPath-type dat een gevoelige bestand of map van het knoop punt koppelt aan de container. Als de container wordt aangetast, kan de aanvaller deze koppeling gebruiken om toegang te krijgen tot het knoop punt.|



### <a name="host-level-alerts"></a>Waarschuwingen op hostniveau

> [!div class="mx-tableFixed"]

|Waarschuwing|Beschrijving|
|---|---|
|**Geprivilegieerde container gedetecteerd**|Computer logboeken geven aan dat een geprivilegieerde docker-container wordt uitgevoerd. Een gemachtigde container heeft volledige toegang tot de resources van de host. Als er is geknoeid, kan een aanvaller de geprivilegieerde container gebruiken om toegang te krijgen tot de hostmachine.|
|**Geprivilegieerde opdracht uitgevoerd in container**|Computer logboeken geven aan dat een geprivilegieerde opdracht is uitgevoerd in een docker-container. Een geprivilegieerde opdracht heeft uitgebreide bevoegdheden op de hostmachine.|
|**Weer gegeven docker-daemon gedetecteerd**|Computer logboeken geven aan dat uw docker daemon (dockerd) een TCP-socket beschrijft. Standaard maakt docker-configuratie geen gebruik van versleuteling of verificatie wanneer een TCP-socket is ingeschakeld. Iedereen met toegang tot de relevante poort kan vervolgens volledige toegang krijgen tot de docker-daemon.|
|**SSH-server wordt uitgevoerd in een container**|Computer logboeken geven aan dat een SSH-server wordt uitgevoerd in een docker-container. Hoewel dit gedrag opzettelijk kan zijn, geeft dit vaak aan dat een container onjuist is geconfigureerd of wordt geschonden.|
|**Er is een container met een Miner-installatie kopie gedetecteerd**|Met computer Logboeken wordt de uitvoering aangegeven van een docker-container met een installatie kopie die is gekoppeld aan digitale valuta analyse. Dit gedrag kan erop duiden dat uw resources worden misbruikt.|
|**Verdachte aanvraag voor Kubernetes-API**|Computer logboeken geven aan dat er een verdachte aanvraag is gedaan voor de Kubernetes-API. De aanvraag is verzonden vanaf een Kubernetes-knoop punt, mogelijk vanuit een van de containers die in het knoop punt worden uitgevoerd. Hoewel dit gedrag opzettelijk kan zijn, kan dit erop wijzen dat op het knoop punt een beschadigde container wordt uitgevoerd.|
|**Verdachte aanvraag naar het Kubernetes-dash board**|Computer logboeken geven aan dat er een verdachte aanvraag is gedaan aan het Kubernetes-dash board. De aanvraag is verzonden vanaf een Kubernetes-knoop punt, mogelijk vanuit een van de containers die in het knoop punt worden uitgevoerd. Hoewel dit gedrag opzettelijk kan zijn, kan dit erop wijzen dat op het knoop punt een beschadigde container wordt uitgevoerd.|