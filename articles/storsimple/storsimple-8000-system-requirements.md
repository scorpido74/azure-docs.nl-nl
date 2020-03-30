---
title: StorSimple 8000-serie systeemvereisten | Microsoft Documenten
description: Beschrijft software, netwerken en vereisten met hoge beschikbaarheid en aanbevolen procedures voor een Microsoft Azure StorSimple-oplossing.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 3032585c6f0a5cc6143eee06b12b6def50cd7cd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297710"
---
# <a name="storsimple-8000-series-software-high-availability-and-networking-requirements"></a>StorSimple 8000-serie software, hoge beschikbaarheid en netwerkvereisten

## <a name="overview"></a>Overzicht

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

Welkom bij Microsoft Azure StorSimple. In dit artikel worden belangrijke systeemvereisten en aanbevolen procedures beschreven voor uw StorSimple-apparaat en voor de opslagclients die toegang hebben tot het apparaat. We raden u aan de informatie zorgvuldig te bekijken voordat u uw StorSimple-systeem implementeert en deze vervolgens zo nodig terugte verwijzen naar de informatie tijdens de implementatie en de daaropvolgende bewerking.

De systeemvereisten omvatten:

* **Softwarevereisten voor opslagclients** - beschrijft de ondersteunde besturingssystemen en eventuele aanvullende vereisten voor deze besturingssystemen.
* **Netwerkvereisten voor het StorSimple-apparaat** bieden informatie over de poorten die in uw firewall moeten worden geopend om iSCSI-, cloud- of beheerverkeer mogelijk te maken.
* **Hoge beschikbaarheidsvereisten voor StorSimple** - beschrijft hoge beschikbaarheidsvereisten en aanbevolen procedures voor uw StorSimple-apparaat en hostcomputer.

## <a name="software-requirements-for-storage-clients"></a>Softwarevereisten voor opslagclients

De volgende softwarevereisten zijn voor de opslagclients die toegang hebben tot uw StorSimple-apparaat.

| Ondersteunde besturingssystemen | Versie vereist | Aanvullende vereisten/notities |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2, 2016 |StorSimple iSCSI-volumes worden ondersteund voor gebruik op alleen de volgende Windows-schijftypen:<ul><li>Eenvoudig volume op basisschijf</li><li>Eenvoudig en gespiegeld volume op dynamische schijf</li></ul>Alleen de software iSCSI-initiators die native aanwezig zijn in het besturingssysteem worden ondersteund. Hardware iSCSI-initiators worden niet ondersteund.<br></br>Windows Server 2012 en 2016 thin provisioning en ODX-functies worden ondersteund als u een StorSimple iSCSI-volume gebruikt.<br><br>StorSimple kan dun ingerichte en volledig ingerichte volumes maken. Het kan geen gedeeltelijk ingerichte volumes maken.<br><br>Het opnieuw formatteren van een dun ingerichte volume kan lang duren. We raden u aan het volume te verwijderen en vervolgens een nieuw volume te maken in plaats van opnieuw te formatteren. Echter, als u nog steeds de voorkeur aan een volume te forforeren:<ul><li>Voer de volgende opdracht uit vóór de opforaom om vertragingen bij het inwinnen van de ruimte te voorkomen: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Nadat de opmaak is voltooid, gebruikt u de volgende opdracht om het opnieuw inschakelen van ruimteterugwinning:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Pas de hotfix van Windows Server 2012 zoals beschreven in [KB 2878635](https://support.microsoft.com/kb/2870270) toe op uw Windows Server-computer.</li></ul></li></ul></ul> Als u StorSimple Snapshot Manager of StorSimple Adapter voor SharePoint configureert, gaat u naar [Softwarevereisten voor optionele onderdelen.](#software-requirements-for-optional-components) |
| VMware ESX |5.5 en 6.0 |Ondersteund met VMware vSphere als iSCSI-client. VAAI-block-functie wordt ondersteund met VMware vSphere op StorSimple-apparaten. |
| Linux RHEL/CentOS |5, 6 en 7 |Ondersteuning voor Linux iSCSI-clients met open-iSCSI initiator versies 5, 6 en 7. |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> IBM AIX wordt momenteel niet ondersteund met StorSimple.


## <a name="software-requirements-for-optional-components"></a>Softwarevereisten voor optionele onderdelen

De volgende softwarevereisten zijn voor de optionele StorSimple-componenten (StorSimple Snapshot Manager en StorSimple Adapter voor SharePoint).

| Onderdeel | Hostplatform | Aanvullende vereisten/notities |
| --- | --- | --- |
| StorSimple Snapshot Manager |Windows Server 2008 R2 SP1, 2012, 2012 R2 |Het gebruik van StorSimple Snapshot Manager op Windows Server is vereist voor back-ups/herstel van gespiegelde dynamische schijven en voor toepassingsconsistente back-ups.<br> StorSimple Snapshot Manager wordt alleen ondersteund op Windows Server 2008 R2 SP1 (64-bits), Windows Server 2012 R2 en Windows Server 2012.<ul><li>Als u Windows Server 2012 gebruikt, moet u .NET 3.5–4.5 installeren voordat u StorSimple Snapshot Manager installeert.</li><li>Als u Windows Server 2008 R2 SP1 gebruikt, moet u Windows Management Framework 3.0 installeren voordat u StorSimple Snapshot Manager installeert.</li></ul> |
| StorSimple Adapter voor SharePoint |Windows Server 2008 R2 SP1, 2012, 2012 R2 |<ul><li>De StorSimple-adapter voor SharePoint wordt alleen ondersteund in SharePoint 2010 en SharePoint 2013.</li><li>RBS vereist SQL Server Enterprise Edition, versie 2008 R2 of 2012.</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>Netwerkvereisten voor uw StorSimple-apparaat

Uw StorSimple-apparaat is een vergrendeld apparaat. Er moeten echter poorten worden geopend in uw firewall om iSCSI-, cloud- en beheerverkeer mogelijk te maken. In de volgende tabel worden de poorten weergegeven die in uw firewall moeten worden geopend. In deze tabel verwijst *in* of *inbound* naar de richting van waaruit binnenkomende clientaanvragen toegang krijgen tot uw apparaat. *Out-* of *outbound* verwijst naar de richting waarin uw StorSimple-apparaat gegevens extern verzendt, buiten de implementatie: bijvoorbeeld uitgaand naar het internet.

| Poort<sup>nr.</sup> | In of uit | Poortbereik | Vereist | Opmerkingen |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP)<sup>3</sup> |Uit |WAN |Nee |<ul><li>Uitgaande poort wordt gebruikt voor internettoegang om updates op te halen.</li><li>De uitgaande webproxy is configureerbaar voor de gebruiker.</li><li>Om systeemupdates mogelijk te maken, moet deze poort ook open staan voor de vaste IP's van de controller.</li></ul> |
| TCP 443 (HTTPS)<sup>3</sup> |Uit |WAN |Ja |<ul><li>Uitgaande poort wordt gebruikt voor toegang tot gegevens in de cloud.</li><li>De uitgaande webproxy is configureerbaar voor de gebruiker.</li><li>Om systeemupdates mogelijk te maken, moet deze poort ook open staan voor de vaste IP's van de controller.</li><li>Deze poort wordt ook gebruikt op beide controllers voor het ophalen van afval.</li></ul> |
| UDP 53 (DNS) |Uit |WAN |In sommige gevallen; zie notities. |Deze poort is alleen vereist als u een dns-server op internet gebruikt. |
| UDP 123 (NTP) |Uit |WAN |In sommige gevallen; zie notities. |Deze poort is alleen vereist als u een NTP-server op internet gebruikt. |
| TCP 9354 |Uit |WAN |Ja |De uitgaande poort wordt gebruikt door het StorSimple-apparaat om te communiceren met de StorSimple Device Manager-service. |
| 3260 (iSCSI) |In |LAN |Nee |Deze poort wordt gebruikt om toegang te krijgen tot gegevens via iSCSI. |
| 5985 |In |LAN |Nee |De binnenkomende poort wordt door StorSimple Snapshot Manager gebruikt om te communiceren met het StorSimple-apparaat.<br>Deze poort wordt ook gebruikt wanneer u op afstand verbinding maakt met Windows PowerShell voor StorSimple via HTTP. |
| 5986 |In |LAN |Nee |Deze poort wordt gebruikt wanneer u op afstand verbinding maakt met Windows PowerShell voor StorSimple via HTTPS. |

<sup>1</sup> Er hoeven geen inkomende poorten te worden geopend op het openbare internet.

<sup>2</sup> Als meerdere poorten een gatewayconfiguratie hebben, wordt de uitgaande routeringsvolgorde bepaald op basis van de poortrouteringsvolgorde die wordt beschreven in [Poortroutering](#routing-metric), hieronder.

<sup>3</sup> De vaste IP's van de controller op uw StorSimple-apparaat moeten routeerbaar zijn en rechtstreeks of via de geconfigureerde webproxy verbinding kunnen maken met internet. De vaste IP-adressen worden gebruikt voor het onderhoud van de updates aan het apparaat en voor het ophalen van afval. Als de apparaatcontrollers geen verbinding kunnen maken met internet via de vaste IP's, u uw StorSimple-apparaat niet bijwerken en werkt de garbage collection niet goed.

> [!IMPORTANT]
> Zorg ervoor dat de firewall geen TLS-verkeer tussen het StorSimple-apparaat en Azure wijzigt of decodeert.


### <a name="url-patterns-for-firewall-rules"></a>URL-patronen voor firewallregels

Netwerkbeheerders kunnen vaak geavanceerde firewallregels configureren op basis van de URL-patronen om het binnenkomende en het uitgaande verkeer te filteren. Uw StorSimple-apparaat en de StorSimple Device Manager-service zijn afhankelijk van andere Microsoft-toepassingen, zoals Azure Service Bus, Azure Active Directory Access Control, opslagaccounts en Microsoft Update-servers. De URL-patronen die aan deze toepassingen zijn gekoppeld, kunnen worden gebruikt om firewallregels te configureren. Het is belangrijk om te begrijpen dat de URL-patronen die aan deze toepassingen zijn gekoppeld, kunnen veranderen. Dit vereist op zijn beurt dat de netwerkbeheerder firewallregels voor uw StorSimple controleert en bijwerkt wanneer dat nodig is.

We raden u aan uw firewallregels voor uitgaand verkeer in te stellen, op basis van vaste IP-adressen van StorSimple, in de meeste gevallen. U de onderstaande informatie echter gebruiken om geavanceerde firewallregels in te stellen die nodig zijn om beveiligde omgevingen te maken.

> [!NOTE]
> De IP's van het apparaat (bron) moeten altijd worden ingesteld op alle ingeschakelde netwerkinterfaces. De ip-bereiken van de bestemming moeten worden ingesteld op [IP-bereiken voor Azure-datacenters.](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653)


#### <a name="url-patterns-for-azure-portal"></a>URL-patronen voor Azure-portal

| URL-patroon | Component/functionaliteit | Apparaat-IP's |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |StorSimple-apparaatbeheerfunctie<br>Toegangscontroleservice<br>Azure Service Bus<br>Verificatieservice |Netwerkinterfaces met cloud-enabled |
| `https://*.backup.windowsazure.com` |Apparaatregistratie |Alleen GEGEVENS 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Intrekking van certificaat |Netwerkinterfaces met cloud-enabled |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure-opslagaccounts en -bewaking |Netwerkinterfaces met cloud-enabled |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update-servers<br> |Alleen vaste IP's voor controller |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Alleen vaste IP's voor controller |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Ondersteuningspakket |Netwerkinterfaces met cloud-enabled |

#### <a name="url-patterns-for-azure-government-portal"></a>URL-patronen voor Azure Government-portal

| URL-patroon | Component/functionaliteit | Apparaat-IP's |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`<br>`https://login.microsoftonline.us` |StorSimple-apparaatbeheerfunctie<br>Toegangscontroleservice<br>Azure Service Bus<br>Verificatieservice |Netwerkinterfaces met cloud-enabled |
| `https://*.backup.windowsazure.us` |Apparaatregistratie |Alleen GEGEVENS 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Intrekking van certificaat |Netwerkinterfaces met cloud-enabled |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure-opslagaccounts en -bewaking |Netwerkinterfaces met cloud-enabled |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update-servers<br> |Alleen vaste IP's voor controller |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Alleen vaste IP's voor controller |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Ondersteuningspakket |Netwerkinterfaces met cloud-enabled |

### <a name="routing-metric"></a>Routeringsstatistiek

Een routeringsstatistiek is gekoppeld aan de interfaces en de gateway die de gegevens naar de opgegeven netwerken leiden. Routeringsstatistiek wordt gebruikt door het routeringsprotocol om het beste pad naar een bepaalde bestemming te berekenen, als er meerdere paden naar dezelfde bestemming bestaan. Hoe lager de routeringsstatistiek, hoe hoger de voorkeur.

In de context van StorSimple, als meerdere netwerkinterfaces en gateways zijn geconfigureerd om het kanaalverkeer, de routing metrics zal in het spel komen om de relatieve volgorde waarin de interfaces zal wennen te bepalen. De routeringsstatistieken kunnen niet door de gebruiker worden gewijzigd. U de `Get-HcsRoutingTable` cmdlet echter gebruiken om de routeringstabel (en statistieken) uit te printen op uw StorSimple-apparaat. Meer informatie over get-hcsroutingtable-cmdlet in [het oplossen van problemen met StorSimple-implementatie](storsimple-troubleshoot-deployment.md).

Het routeringsmetrische algoritme dat wordt gebruikt voor update 2 en latere versies kan als volgt worden uitgelegd.

* Een reeks vooraf bepaalde waarden is toegewezen aan netwerkinterfaces.
* Bekijk een voorbeeldtabel hieronder met waarden die zijn toegewezen aan de verschillende netwerkinterfaces wanneer deze cloud- of clouduitgeschakeld zijn, maar met een geconfigureerde gateway. Let op: de waarden die hier zijn toegewezen, zijn alleen voorbeeldwaarden.

    | Netwerkinterface | Cloud-enabled | Cloud-uitgeschakeld met gateway |
    |-----|---------------|---------------------------|
    | Gegevens 0  | 1            | -                        |
    | Gegevens 1  | 2            | 20                       |
    | Gegevens 2  | 3            | 30                       |
    | Gegevens 3  | 4            | 40                       |
    | Gegevens 4  | 5            | 50                       |
    | Gegevens 5  | 6            | 60                       |


* De volgorde waarin het cloudverkeer wordt omgeleid via de netwerkinterfaces is:
  
    *Gegevens 0 > Gegevens 1 > datum 2 > gegevens 3 > gegevens 4 > Gegevens 5*
  
    Dit kan worden verklaard door het volgende voorbeeld.
  
    Overweeg een StorSimple-apparaat met twee netwerkinterfaces met cloud- ingeschakeld, Gegevens 0 en Gegevens 5. Gegevens 1 tot en met gegevens 4 zijn uitgeschakeld in de cloud, maar hebben een geconfigureerde gateway. De volgorde waarin het verkeer zal worden gerouteerd voor dit apparaat zal zijn:
  
    *Gegevens 0 (1) > gegevens 5 (6) > gegevens 1 (20) > gegevens 2 (30) > gegevens 3 (40) > gegevens 4 (50)*
  
    *De getallen tussen haakjes geven de respectievelijke routeringsstatistieken aan.*
  
    Als gegevens 0 mislukt, wordt het cloudverkeer omgeleid via Gegevens 5. Aangezien een gateway is geconfigureerd op alle andere netwerken, als zowel Gegevens 0 als Gegevens 5 zouden mislukken, gaat het cloudverkeer via Gegevens 1.
* Als een netwerkinterface met cloud-enabled uitvalt, worden er 3 pogingen met een vertraging van 30 seconden uitgevoerd om verbinding te maken met de interface. Als alle nieuwe pogingen mislukken, wordt het verkeer doorgestuurd naar de volgende beschikbare cloudinterface, zoals bepaald door de routeringstabel. Als alle netwerkinterfaces met cloud-ingeschakeld mislukken, mislukt het apparaat naar de andere controller (in dit geval geen reboot).
* Als er een VIP-storing is voor een netwerkinterface met iSCSI-interface, zijn er 3 nieuwe pogingen met een vertraging van 2 seconden. Dit gedrag is hetzelfde gebleven van de vorige releases. Als alle iSCSI-netwerkinterfaces mislukken, treedt er een failover van de controller op (vergezeld van een reboot).
* Er wordt ook een waarschuwing gegenereerd op uw StorSimple-apparaat wanneer er een VIP-storing optreedt. Ga voor meer informatie naar [snelverwijzing.](storsimple-8000-manage-alerts.md)
* In termen van nieuwe pogingen heeft iSCSI voorrang op cloud.
  
    Denk aan het volgende voorbeeld: Een StorSimple-apparaat heeft twee netwerkinterfaces ingeschakeld, Gegevens 0 en Gegevens 1. Gegevens 0 is cloud-enabled, terwijl Data 1 zowel cloud als iSCSI-enabled is. Er zijn geen andere netwerkinterfaces op dit apparaat ingeschakeld voor cloud of iSCSI.
  
    Als gegevens 1 uitvalt, aangezien dit de laatste iSCSI-netwerkinterface is, zal dit resulteren in een mislukte controller naar Gegevens 1 op de andere controller.

### <a name="networking-best-practices"></a>Aanbevolen procedures voor netwerken

Naast de bovenstaande netwerkvereisten u zich voor de optimale prestaties van uw StorSimple-oplossing houden aan de volgende aanbevolen procedures:

* Zorg ervoor dat uw StorSimple-apparaat te allen tijde een speciale bandbreedte (of meer) van 40 Mbps beschikbaar heeft. Deze bandbreedte mag niet worden gedeeld (of toewijzing moet worden gegarandeerd door het gebruik van QoS-beleid) met andere toepassingen.
* Zorg ervoor dat de netwerkverbinding met internet te allen tijde beschikbaar is. Sporadische of onbetrouwbare internetverbinding met de apparaten, waaronder geen enkele internetverbinding, zal resulteren in een niet-ondersteunde configuratie.
* Isoleer het iSCSI- en cloudverkeer door speciale netwerkinterfaces op uw apparaat te hebben voor iSCSI- en cloudtoegang. Zie voor meer informatie hoe u netwerkinterfaces op uw StorSimple-apparaat [wijzigen.](storsimple-8000-modify-device-config.md#modify-network-interfaces)
* Gebruik geen LACP-configuratie (Link Aggregation Control Protocol) voor uw netwerkinterfaces. Dit is een niet-ondersteunde configuratie.

## <a name="high-availability-requirements-for-storsimple"></a>Hoge beschikbaarheidsvereisten voor StorSimple

Het hardwareplatform dat bij de StorSimple-oplossing is meegeleverd, heeft beschikbaarheids- en betrouwbaarheidsfuncties die een basis vormen voor een zeer beschikbare, fouttolerante opslaginfrastructuur in uw datacenter. Er zijn echter vereisten en best practices waaraan u moet voldoen om de beschikbaarheid van uw StorSimple-oplossing te garanderen. Voordat u StorSimple implementeert, bekijkt u zorgvuldig de volgende vereisten en aanbevolen procedures voor het StorSimple-apparaat en verbonden hostcomputers.

Voor meer informatie over het bewaken en onderhouden van de hardwarecomponenten van uw StorSimple-apparaat, gaat u [naar De StorSimple Device Manager-service gebruiken om hardwarecomponenten en status en](storsimple-8000-monitor-hardware-status.md) vervanging van [StorSimple-hardwarecomponenten](storsimple-8000-hardware-component-replacement.md)te controleren.

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Hoge beschikbaarheidsvereisten en procedures voor uw StorSimple-apparaat

Bekijk de volgende informatie zorgvuldig om de hoge beschikbaarheid van uw StorSimple-apparaat te garanderen.

#### <a name="pcms"></a>PCM's

StorSimple-apparaten omvatten redundante, hot-swappable stroom- en koelmodules (PCM's). Elke PCM heeft voldoende capaciteit om service te bieden voor het gehele chassis. Om een hoge beschikbaarheid te garanderen, moeten beide PCM's worden geïnstalleerd.

* Sluit uw PCM's aan op verschillende stroombronnen om beschikbaarheid te bieden als een stroombron uitvalt.
* Als een PCM mislukt, vraag dan onmiddellijk een vervanging aan.
* Verwijder een mislukte PCM alleen wanneer u de vervanging hebt en klaar bent om het te installeren.
* Verwijder beide PCM's niet gelijktijdig. De PCM-module bevat de back-up batterijmodule. Het verwijderen van beide PCM's zal resulteren in een uitschakeling zonder batterijbeveiliging en de status van het apparaat wordt niet opgeslagen. Ga voor meer informatie over de batterij naar [De reservebatterijmodule onderhouden.](storsimple-8000-battery-replacement.md#maintain-the-backup-battery-module)

#### <a name="controller-modules"></a>Controllermodules

StorSimple-apparaten bevatten redundante, hot-swappable controllermodules. De controllermodules werken actief/passief. Op een gegeven moment is één controllermodule actief en biedt service, terwijl de andere controllermodule passief is. De passieve controllermodule wordt ingeschakeld en wordt operationeel als de actieve controllermodule uitvalt of wordt verwijderd. Elke controllermodule heeft voldoende capaciteit om service te bieden voor het gehele chassis. Beide controllermodules moeten worden geïnstalleerd om een hoge beschikbaarheid te garanderen.

* Zorg ervoor dat beide controllermodules te allen tijde zijn geïnstalleerd.
* Als een controllermodule uitvalt, vraagt u onmiddellijk een vervanging aan.
* Verwijder een defecte controllermodule alleen wanneer u de vervanging hebt en klaar bent om deze te installeren. Het verwijderen van een module voor langere tijd heeft invloed op de luchtstroom en dus de koeling van het systeem.
* Zorg ervoor dat de netwerkverbindingen met beide controllermodules identiek zijn en dat de verbonden netwerkinterfaces een identieke netwerkconfiguratie hebben.
* Als een controllermodule uitvalt of moet worden vervangen, controleert u of de andere controllermodule in actieve staat is voordat u de defecte controllermodule vervangt. Ga naar De actieve controller [op uw apparaat](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)identificeren om te controleren of een controller actief is.
* Verwijder beide controllermodules niet tegelijkertijd. Als er een controllerfailover aan de gang is, schakelt u de stand-by controllermodule niet uit of verwijdert u deze niet van het chassis.
* Wacht na een controle-failover ten minste vijf minuten voordat u een controllermodule verwijdert.

#### <a name="network-interfaces"></a>Netwerkinterfaces

StorSimple-apparaatcontrollermodules hebben elk vier 1 Gigabit- en twee 10 Gigabit Ethernet-netwerkinterfaces.

* Zorg ervoor dat de netwerkverbindingen met beide controllermodules identiek zijn en dat de netwerkinterfaces die de controllermodule-interfaces hebben, een identieke netwerkconfiguratie hebben.
* Implementeer waar mogelijk netwerkverbindingen via verschillende switches om de beschikbaarheid van de service te garanderen in het geval van een storing in het netwerkapparaat.
* Wanneer u de stekker uit de enige of de laatst overgebleven iSCSI-interface hebt aangesloten (met IP's toegewezen), schakelt u eerst de interface uit en trekt u de stekker uit de kabels. Als de interface eerst wordt losgekoppeld, dan zal dit ertoe leiden dat de actieve controller niet naar de passieve controller. Als de passieve controller ook de bijbehorende interfaces heeft losgekoppeld, dan zullen beide controllers meerdere keren opnieuw opstarten voordat ze zich op één controller vestigen.
* Maak vanaf elke controllermodule ten minste twee DATA-interfaces met het netwerk verbonden.
* Als u de twee 10 GbE-interfaces hebt ingeschakeld, implementeert u deze op verschillende switches.
* Gebruik mpio op servers waar mogelijk om ervoor te zorgen dat de servers een koppeling, netwerk of interfacestoring kunnen verdragen.

Ga voor meer informatie over het netwerken van uw apparaat voor hoge beschikbaarheid en prestaties naar [Uw StorSimple 8100-apparaat installeren](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) of [uw StorSimple 8600-apparaat installeren.](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device)

#### <a name="ssds-and-hdds"></a>SSD's en HDD's

StorSimple-apparaten omvatten solid state-schijven (SSD's) en harde schijven (HDD's) die zijn beveiligd met gespiegelde ruimten. Het gebruik van gespiegelde ruimten zorgt ervoor dat het apparaat het falen van een of meer SSD's of HDD's kan verdragen.

* Zorg ervoor dat alle SSD- en HDD-modules zijn geïnstalleerd.
* Als een SSD of HDD uitvalt, vraag dan onmiddellijk een vervanging aan.
* Als een SSD of HDD uitvalt of moet worden vervangen, moet u ervoor zorgen dat u alleen de SSD of HDD verwijdert die moet worden vervangen.
* Verwijder op geen enkel moment meer dan één SSD of HDD uit het systeem.
  Een storing van 2 of meer schijven van een bepaald type (HDD, SSD) of opeenvolgende storing binnen een kort tijdsbestek kan leiden tot een systeemstoring en mogelijk gegevensverlies. Neem als dit gebeurt [contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md) voor hulp.
* Controleer tijdens de vervanging de **gedeelde componenten** in het **hardware-gezondheidsblad** voor de schijven in de SSD's en HDD's. Een groene controlestatus geeft aan dat de schijven gezond of OK zijn, terwijl een rood uitroepteken een mislukte SSD of HDD aangeeft.
* We raden u aan cloudmomentopnamen te configureren voor alle volumes die u moet beschermen in geval van een systeemfout.

#### <a name="ebod-enclosure"></a>EBOD-behuizing

StorSimple-apparaatmodel 8600 bevat naast de primaire behuizing een EBOD-behuizing (Extended Bunch of Disks). Een EBOD bevat EBOD-controllers en harde schijven (HDD's) die zijn beveiligd met gespiegelde ruimten. Het gebruik van gespiegelde ruimten zorgt ervoor dat het apparaat het falen van een of meer HDD's kan verdragen. De EBOD-behuizing is via redundante SAS-kabels op de primaire behuizing aangesloten.

* Zorg ervoor dat beide EBOD-modules voor de behuizing, zowel SAS-kabels als alle harde schijven te allen tijde zijn geïnstalleerd.
* Als een EBOD-module voor de bediening uitvalt, vraagt u onmiddellijk een vervanging aan.
* Als een EBOD-module voor de verwerkingsruimte uitvalt, controleert u of de andere controllermodule actief is voordat u de defecte module vervangt. Ga naar De actieve controller [op uw apparaat](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)identificeren om te controleren of een controller actief is.
* Controleer tijdens een ebod-controllermodule continu de status van het onderdeel in de StorSimple Device Manager-service door toegang te krijgen tot **de** > **monitorhardwarestatus.**
* Als een SAS-kabel uitvalt of moet worden vervangen (Microsoft Support moet worden ingeschakeld om een dergelijke bepaling te maken), zorg er dan voor dat u alleen de SAS-kabel verwijdert die moet worden vervangen.
* Verwijder beide SAS-kabels niet tegelijkertijd op elk moment uit het systeem.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Aanbevelingen voor hoge beschikbaarheid voor uw hostcomputers

Bekijk deze aanbevolen procedures zorgvuldig om ervoor te zorgen dat hosts die zijn verbonden met uw StorSimple-apparaat, goed beschikbaar zijn.

* Configureer StorSimple met [clusterconfiguraties met twee nodebestanden][1]. Door het verwijderen van enkele punten van mislukking en het bouwen in redundantie aan de host kant, de hele oplossing wordt zeer beschikbaar.
* Gebruik continu beschikbare (CA)-shares die beschikbaar zijn met Windows Server 2012 (SMB 3.0) voor hoge beschikbaarheid tijdens failover van de opslagcontrollers. Raadpleeg deze [videodemo](https://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares)voor meer informatie voor het configureren van bestandsserverclusters en continu beschikbare shares met Windows Server 2012.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over StorSimple-systeemlimieten.](storsimple-8000-limits.md)
* [Meer informatie over het implementeren van uw StorSimple-oplossing.](storsimple-8000-deployment-walkthrough-u2.md)

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx
