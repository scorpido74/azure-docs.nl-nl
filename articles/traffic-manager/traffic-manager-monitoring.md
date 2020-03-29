---
title: Azure Traffic Manager-eindpuntbewaking | Microsoft Documenten
description: In dit artikel u begrijpen hoe Traffic Manager endpoint-monitoring en automatische endpoint-failover gebruikt om Azure-klanten te helpen bij het implementeren van toepassingen met hoge beschikbaarheid
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2018
ms.author: rohink
ms.openlocfilehash: fcc9c5333b37c041342c2d20a53cf5d3908d1a26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938561"
---
# <a name="traffic-manager-endpoint-monitoring"></a>Eindpuntbewaking in Traffic Manager

Azure Traffic Manager bevat ingebouwde endpoint-monitoring en automatische failover van eindpunten. Met deze functie u toepassingen met hoge beschikbaarheid leveren die bestand zijn tegen endpoint-fouten, waaronder azure-regiofouten.

## <a name="configure-endpoint-monitoring"></a>Eindpuntbewaking configureren

Als u eindpuntbewaking wilt configureren, moet u de volgende instellingen opgeven in uw Traffic Manager-profiel:

* **Protocol**. Kies HTTP, HTTPS of TCP als het protocol dat Traffic Manager gebruikt bij het onderzoeken van uw eindpunt om de status ervan te controleren. HTTPS-monitoring controleert niet of uw SSL-certificaat geldig is, het controleert alleen of het certificaat aanwezig is.
* **Haven**. Kies de poort die voor de aanvraag wordt gebruikt.
* **Pad.** Deze configuratie-instelling is alleen geldig voor de HTTP- en HTTPS-protocollen, waarvoor de padinstelling is opgegeven. Het verstrekken van deze instelling voor het TCP-bewakingsprotocol resulteert in een fout. Geef voor HTTP- en HTTPS-protocol het relatieve pad en de naam van de webpagina of het bestand dat de controle opent. Een slash doorsturen (/) is een geldige vermelding voor het relatieve pad. Deze waarde houdt in dat het bestand zich in de hoofdmap (standaard).
* **Aangepaste koptekstinstellingen** Met deze configuratie-instelling u specifieke HTTP-koppen toevoegen aan de statuscontroles die Traffic Manager naar eindpunten onder een profiel verzendt. De aangepaste headers kunnen worden opgegeven op profielniveau dat van toepassing is op alle eindpunten in dat profiel en/of op een eindpuntniveau dat alleen van toepassing is op dat eindpunt. U aangepaste kopteksten gebruiken voor het uitvoeren van statuscontroles op eindpunten in een omgeving met meerdere tenants die correct naar hun bestemming worden doorgestuurd door een hostheader op te geven. U deze instelling ook gebruiken door unieke kopteksten toe te voegen die kunnen worden gebruikt om http-aanvragen van traffic manager te identificeren en deze anders te verwerken. U maximaal acht koptekst:waardeparen opgeven die door een komma zijn gescheiden. Bijvoorbeeld 'header1:value1,header2:value2'. 
* **Verwachte statuscodebereiken** Met deze instelling u meerdere succescodebereiken opgeven in de indeling 200-299, 301-301. Als deze statuscodes worden ontvangen als antwoord van een eindpunt wanneer een statuscontrole wordt gestart, markeert Traffic Manager deze eindpunten als gezond. U maximaal 8 statuscodebereiken opgeven. Deze instelling is alleen van toepassing op het HTTP- en HTTPS-protocol en op alle eindpunten. Deze instelling is op profielniveau Traffic Manager en standaard wordt de waarde 200 gedefinieerd als de successtatuscode.
* **Indringende interval**. Deze waarde geeft aan hoe vaak een eindpunt wordt gecontroleerd op de status van een indringende agent voor verkeersbeheer. U hier twee waarden opgeven: 30 seconden (normaal indringende) en 10 seconden (snel indringende). Als er geen waarden zijn opgegeven, wordt het profiel ingesteld op een standaardwaarde van 30 seconden. Ga naar de pagina [Traffic Manager Pricing](https://azure.microsoft.com/pricing/details/traffic-manager) voor meer informatie over snel indringende prijzen.
* **Getolereerd aantal mislukkingen**. Deze waarde geeft aan hoeveel fouten een indringende agent voor verkeersbeheer wordt getolereerd voordat het eindpunt als niet-inord is. De waarde kan variëren tussen 0 en 9. Een waarde van 0 betekent dat een enkele bewakingsfout kan ertoe leiden dat dat eindpunt als ongezond wordt gemarkeerd. Als er geen waarde is opgegeven, wordt de standaardwaarde van 3 gebruikt.
* **Sonde time-out**. Deze eigenschap geeft aan hoe lang de indringende agent Traffic Manager moet wachten voordat u bedenkt dat een fout wordt gecontroleerd wanneer een sonde voor de statuscontrole naar het eindpunt wordt verzonden. Als het indringende interval is ingesteld op 30 seconden, u de time-outwaarde instellen tussen 5 en 10 seconden. Als er geen waarde is opgegeven, wordt een standaardwaarde van 10 seconden gebruikt. Als het indringende interval is ingesteld op 10 seconden, u de time-outwaarde instellen tussen 5 en 9 seconden. Als er geen time-outwaarde is opgegeven, wordt een standaardwaarde van 9 seconden gebruikt.

    ![Eindpuntbewaking in Traffic Manager](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

    **Figuur: Eindpuntbewaking van Traffic Manager**

## <a name="how-endpoint-monitoring-works"></a>Hoe endpoint monitoring werkt

Als het bewakingsprotocol is ingesteld als HTTP of HTTPS, doet de indringende agent Traffic Manager een GET-verzoek naar het eindpunt met behulp van het opgegeven protocol, de poort en het relatieve pad. Als het een 200-OK-antwoord terugkrijgt, of een van de antwoorden die zijn geconfigureerd in de **reeksen van de verwachte statuscode, \*** wordt dat eindpunt als gezond beschouwd. Als het antwoord een andere waarde is of als er binnen de opgegeven time-outperiode geen antwoord wordt ontvangen, probeert de medewerker verkeerbeheer opnieuw te onderzoeken volgens de instelling Getolereerd aantal fouten (er worden geen nieuwe pogingen uitgevoerd als deze instelling 0 is). Als het aantal opeenvolgende fouten hoger is dan de instelling Getolereerd aantal fouten, wordt dat eindpunt gemarkeerd als ongezond. 

Als het bewakingsprotocol TCP is, start de indringende agent Traffic Manager een TCP-verbindingsaanvraag met behulp van de opgegeven poort. Als het eindpunt op de aanvraag reageert met een antwoord om de verbinding tot stand te brengen, wordt die statuscontrole gemarkeerd als een succes en wordt de tcp-verbinding opnieuw ingesteld door de indringende agent Traffic Manager. Als het antwoord een andere waarde is of als er binnen de opgegeven time-outperiode geen antwoord wordt ontvangen, probeert de medewerker verkeerbeheer opnieuw te onderzoeken op basis van de instelling Getolereerd aantal fouten (er worden geen nieuwe pogingen uitgevoerd als deze instelling 0 is). Als het aantal opeenvolgende fouten hoger is dan de instelling Getolereerd aantal fouten, wordt dat eindpunt als niet-inord gemarkeerd.

In alle gevallen, Traffic Manager sondes vanaf meerdere locaties en de opeenvolgende fout bepaling gebeurt binnen elke regio. Dit betekent ook dat eindpunten statussondes ontvangen van Traffic Manager met een hogere frequentie dan de instelling die wordt gebruikt voor Probing Interval.

>[!NOTE]
>Voor HTTP- of HTTPS-monitoringprotocol is een gangbare praktijk aan de eindpuntzijde het implementeren van een aangepaste pagina binnen uw toepassing , bijvoorbeeld /health.aspx. Met behulp van dit pad voor controle u toepassingsspecifieke controles uitvoeren, zoals het controleren van prestatiemeteritems of het verifiëren van de beschikbaarheid van database. Op basis van deze aangepaste controles retourneert de pagina een geschikte HTTP-statuscode.

Alle eindpunten in de controle-instellingen voor het beheer van een Traffic Manager-profieldelen. Als u verschillende bewakingsinstellingen voor verschillende eindpunten moet gebruiken, u [geneste Traffic Manager-profielen](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings)maken.

## <a name="endpoint-and-profile-status"></a>Eindpunt en profielstatus

U Traffic Manager-profielen en eindpunten in- en uitschakelen. Een wijziging in de eindpuntstatus kan echter ook optreden als gevolg van geautomatiseerde instellingen en processen voor Traffic Manager.

### <a name="endpoint-status"></a>Eindpuntstatus

U een specifiek eindpunt in- of uitschakelen. De onderliggende service, die mogelijk nog steeds gezond is, wordt niet beïnvloed. Als u de status eindpunt wijzigt, bepaalt u de beschikbaarheid van het eindpunt in het profiel Verkeersbeheerbeheer. Wanneer een eindpuntstatus is uitgeschakeld, controleert Traffic Manager de status ervan niet en wordt het eindpunt niet opgenomen in een DNS-reactie.

### <a name="profile-status"></a>Profile status

Met de instelling voor profielstatus u een specifiek profiel in- of uitschakelen. Hoewel de status van eindpunt van invloed is op één eindpunt, is de profielstatus van invloed op het hele profiel, inclusief alle eindpunten. Wanneer u een profiel uitschakelt, worden de eindpunten niet gecontroleerd op status en worden er geen eindpunten opgenomen in een DNS-antwoord. Een [NXDOMAIN-antwoordcode](https://tools.ietf.org/html/rfc2308) wordt geretourneerd voor de DNS-query.

### <a name="endpoint-monitor-status"></a>Status van endpointmonitor

Status van de eindpuntmonitor is een door Traffic Manager gegenereerde waarde die de status van het eindpunt weergeeft. U deze instelling niet handmatig wijzigen. De status van de eindpuntmonitor is een combinatie van de resultaten van endpointmonitoring en de geconfigureerde eindpuntstatus. De mogelijke waarden van de status van eindpuntmonitor worden weergegeven in de volgende tabel:

| Profile status | Eindpuntstatus | Status van endpointmonitor | Opmerkingen |
| --- | --- | --- | --- |
| Uitgeschakeld |Ingeschakeld |Niet-actief |Het profiel is uitgeschakeld. Hoewel de eindpuntstatus is ingeschakeld, heeft de profielstatus (Uitgeschakeld) voorrang. Eindpunten in uitgeschakelde profielen worden niet gecontroleerd. Een NXDOMAIN-antwoordcode wordt geretourneerd voor de DNS-query. |
| &lt;alle&gt; |Uitgeschakeld |Uitgeschakeld |Het eindpunt is uitgeschakeld. Uitgeschakelde eindpunten worden niet gecontroleerd. Het eindpunt is niet opgenomen in DNS-antwoorden, daarom ontvangt het geen verkeer. |
| Ingeschakeld |Ingeschakeld |Online |Het eindpunt wordt bewaakt en is gezond. Het is opgenomen in DNS-antwoorden en kan verkeer ontvangen. |
| Ingeschakeld |Ingeschakeld |Verminderd beschikbaar |De statuscontroles van eindpunten mislukken. Het eindpunt is niet opgenomen in DNS-antwoorden en ontvangt geen verkeer. <br>Een uitzondering hierop is als alle eindpunten zijn afgebroken, in welk geval ze allemaal worden beschouwd als worden geretourneerd in het queryantwoord).</br>|
| Ingeschakeld |Ingeschakeld |Controleeindpunt |Het eindpunt wordt gemonitord, maar de resultaten van de eerste sonde zijn nog niet ontvangen. CheckingEndpoint is een tijdelijke status die meestal onmiddellijk optreedt na het toevoegen of inschakelen van een eindpunt in het profiel. Een eindpunt in deze status is opgenomen in DNS-antwoorden en kan verkeer ontvangen. |
| Ingeschakeld |Ingeschakeld |Gestopt |De web-app waarnaar het eindpunt verwijst, wordt niet uitgevoerd. Controleer de instellingen van de web-app. Dit kan ook gebeuren als het eindpunt van het type geneste eindpunt is en het onderliggende profiel is uitgeschakeld of inactief is. <br>Een eindpunt met een status Gestopt wordt niet gecontroleerd. Het is niet opgenomen in DNS-antwoorden en ontvangt geen verkeer. Een uitzondering hierop is als alle eindpunten zijn afgebroken, in welk geval ze allemaal worden beschouwd als worden geretourneerd in het queryantwoord.</br>|

Zie [geneste Traffic Manager-profielen](traffic-manager-nested-profiles.md)voor meer informatie over de manier waarop de status van eindpuntmonitor wordt berekend voor geneste eindpunten.

>[!NOTE]
> Een gestopte status van eindpuntmonitor kan plaatsvinden op App Service als uw webtoepassing niet wordt uitgevoerd in de standaardlaag of hoger. Zie [Traffic Manager-integratie met App Service](/azure/app-service/web-sites-traffic-manager)voor meer informatie.

### <a name="profile-monitor-status"></a>Status van profielmonitor

De status van de profielmonitor is een combinatie van de geconfigureerde profielstatus en de statuswaarden van de eindpuntmonitor voor alle eindpunten. De mogelijke waarden worden beschreven in de volgende tabel:

| Profielstatus (zoals geconfigureerd) | Status van endpointmonitor | Status van profielmonitor | Opmerkingen |
| --- | --- | --- | --- |
| Uitgeschakeld |&lt;een&gt; profiel zonder gedefinieerde eindpunten. |Uitgeschakeld |Het profiel is uitgeschakeld. |
| Ingeschakeld |De status van ten minste één eindpunt is Gedegradeerd. |Verminderd beschikbaar |Controleer de afzonderlijke eindpuntstatuswaarden om te bepalen welke eindpunten meer aandacht nodig hebben. |
| Ingeschakeld |De status van ten minste één eindpunt is Online. Geen eindpunten hebben een gedegradeerde status. |Online |De service accepteert verkeer. Er is geen verdere actie vereist. |
| Ingeschakeld |De status van ten minste één eindpunt is CheckingEndpoint. Er zijn geen eindpunten in de status Online of Gedegradeerd. |Eindpunten controleren |Deze overgangsstatus treedt op wanneer een profiel is gemaakt of ingeschakeld. De status van het eindpunt wordt voor het eerst gecontroleerd. |
| Ingeschakeld |De statussen van alle eindpunten in het profiel zijn uitgeschakeld of gestopt of het profiel heeft geen gedefinieerde eindpunten. |Niet-actief |Er zijn geen eindpunten actief, maar het profiel is nog steeds ingeschakeld. |

## <a name="endpoint-failover-and-recovery"></a>Failover en herstel van eindpunten

Traffic Manager controleert periodiek de status van elk eindpunt, inclusief ongezonde eindpunten. Traffic Manager detecteert wanneer een eindpunt gezond wordt en brengt het terug in rotatie.

Een eindpunt is niet in orde wanneer een van de volgende gebeurtenissen optreedt:

- Als het bewakingsprotocol HTTP of HTTPS is:
    - Er wordt een niet-200-antwoord ontvangen of een antwoord dat niet het statusbereik bevat dat is opgegeven in de instelling **Verwachte statuscodebereiken** (inclusief een andere 2xx-code of een omleiding van 301/302).
- Als het bewakingsprotocol TCP is: 
    - Een ander antwoord dan ACK of SYN-ACK wordt ontvangen naar aanleiding van het SYN-verzoek van Traffic Manager om een verbindingsinstelling te proberen.
- Timeout. 
- Elk ander verbindingsprobleem waardoor het eindpunt niet bereikbaar is.

Zie [Status oplossen oplossen van problemen oplossen in Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md)voor meer informatie over het oplossen van mislukte controles. 

De tijdlijn in de volgende figuur is een gedetailleerde beschrijving van het controleproces van Traffic Manager eindpunt dat de volgende instellingen heeft: monitoring protocol is HTTP, indringende interval is 30 seconden, aantal getolereerde fouten is 3, time-out waarde is 10 seconden en DNS TTL is 30 seconden.

![Eindpuntfailover- en failbackreeks van Traffic Manager](./media/traffic-manager-monitoring/timeline.png)

**Afbeelding: Eindpuntfailover- en herstelreeks voor verkeersbeheerbeheer**

1. **GET**. Voor elk eindpunt voert het monitoringsysteem Voor verkeersbeheer een GET-aanvraag uit op het pad dat is opgegeven in de bewakingsinstellingen.
2. **200 OK of aangepaste codebereik opgegeven Traffic Manager-profielcontrole-instellingen** . Het bewakingssysteem verwacht dat een HTTP 200 OK of het opgegeven of aangepaste codebereik traffic manager-profielcontrole-instellingenbericht binnen 10 seconden wordt geretourneerd. Wanneer het dit antwoord ontvangt, erkent het dat de service beschikbaar is.
3. **30 seconden tussen de controles.** De statuscontrole van het eindpunt wordt elke 30 seconden herhaald.
4. **Service niet beschikbaar**. De service is niet beschikbaar. Traffic Manager zal niet weten tot de volgende health check.
5. **Pogingen om toegang te krijgen tot het bewakingspad**. Het bewakingssysteem voert een GET-aanvraag uit, maar ontvangt geen antwoord binnen de time-outperiode van 10 seconden (als alternatief kan een niet-200-antwoord worden ontvangen). Het probeert dan nog drie keer, met intervallen van 30 seconden. Als een van de pogingen succesvol is, wordt het aantal pogingen gereset.
6. **Status ingesteld op Gedegradeerd**. Na een vierde opeenvolgende fout markeert het bewakingssysteem de niet-beschikbare eindpuntstatus als Gedegradeerd.
7. **Het verkeer wordt omgeleid naar andere eindpunten.** De DNS-naamservers van Traffic Manager worden bijgewerkt en Traffic Manager retourneert het eindpunt niet meer als reactie op DNS-query's. Nieuwe verbindingen worden gericht op andere, beschikbare eindpunten. Eerdere DNS-antwoorden die dit eindpunt bevatten, kunnen echter nog steeds worden opgeslagen door recursieve DNS-servers en DNS-clients. Clients blijven het eindpunt gebruiken totdat de DNS-cache verloopt. Naarmate de DNS-cache verloopt, maken clients nieuwe DNS-query's en worden ze naar verschillende eindpunten geleid. De cacheduur wordt geregeld door de TTL-instelling in het Traffic Manager-profiel, bijvoorbeeld 30 seconden.
8. **Gezondheidscontroles worden voortgezet**. Traffic Manager blijft de status van het eindpunt controleren terwijl het een gedegradeerde status heeft. Traffic Manager detecteert wanneer het eindpunt terugkeert naar de status.
9. **Service komt weer online.** De service wordt beschikbaar. Het eindpunt behoudt de status Gedegradeerd in Traffic Manager totdat het bewakingssysteem de volgende statuscontrole uitvoert.
10. **Verkeer naar de service wordt hervat.** Traffic Manager stuurt een GET-aanvraag en ontvangt een 200 OK-statusantwoord. De service is weer gezond. De naamservers van Traffic Manager worden bijgewerkt en beginnen de DNS-naam van de service uit te delen in DNS-reacties. Verkeer keert terug naar het eindpunt als DNS-antwoorden in de cache die andere eindpunten retourneren en als bestaande verbindingen met andere eindpunten worden beëindigd.

    > [!NOTE]
    > Omdat Traffic Manager op DNS-niveau werkt, kan het geen invloed hebben op bestaande verbindingen met een eindpunt. Wanneer het verkeer tussen eindpunten wordt doorsturen (door gewijzigde profielinstellingen of tijdens failover of failback), stuurt Traffic Manager nieuwe verbindingen naar beschikbare eindpunten. Andere eindpunten kunnen echter verkeer via bestaande verbindingen blijven ontvangen totdat deze sessies zijn beëindigd. Om verkeer uit bestaande verbindingen te laten weglopen, moeten toepassingen de sessieduur beperken die bij elk eindpunt wordt gebruikt.

## <a name="traffic-routing-methods"></a>Methoden voor het routeren van verkeer

Wanneer een eindpunt een gedegradeerde status heeft, wordt het niet meer geretourneerd als reactie op DNS-query's. In plaats daarvan wordt een alternatief eindpunt gekozen en geretourneerd. De verkeersrouteringsmethode die in het profiel is geconfigureerd, bepaalt hoe het alternatieve eindpunt wordt gekozen.

* **Prioriteit**. Eindpunten vormen een geprioritteerde lijst. Het eerste beschikbare eindpunt in de lijst wordt altijd geretourneerd. Als een eindpuntstatus wordt afgebroken, wordt het volgende beschikbare eindpunt geretourneerd.
* **Gewogen**. Elk beschikbaar eindpunt wordt willekeurig gekozen op basis van hun toegewezen gewichten en de gewichten van de andere beschikbare eindpunten.
* **Prestaties**. Het eindpunt dat het dichtst bij de eindgebruiker staat, wordt geretourneerd. Als dat eindpunt niet beschikbaar is, verplaatst Traffic Manager verkeer naar de eindpunten in de dichtstbijzijnde Azure-regio. U alternatieve failoverplannen configureren voor het routeren van prestatieverkeer met behulp van [geneste Traffic Manager-profielen.](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region)
* **Geografisch**. Het eindpunt dat is toegewezen om de geografische locatie weer te geven op basis van de IP-adressen van de queryaanvraag, wordt geretourneerd. Als dat eindpunt niet beschikbaar is, wordt een ander eindpunt niet geselecteerd om naar te mislukken, omdat een geografische locatie slechts kan worden toegewezen aan één eindpunt in een profiel (meer details staan in de [veelgestelde vragen).](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method) Als aanbevolen aanbevolen aanbevolen functie, bij het gebruik van geografische routering, raden we klanten aan om geneste Traffic Manager-profielen te gebruiken met meer dan één eindpunt als eindpunten van het profiel.
* **Multivalueeren** Meerdere eindpunten die zijn toegewezen aan IPv4/IPv6-adressen worden geretourneerd. Wanneer een query voor dit profiel wordt ontvangen, worden gezonde eindpunten geretourneerd op basis van het **maximale aantal recordin reactiewaarde** dat u hebt opgegeven. Het standaardaantal antwoorden is twee eindpunten.
* **Subnet** Het eindpunt dat is toegewezen aan een set IP-adresbereiken, wordt geretourneerd. Wanneer een aanvraag van dat IP-adres wordt ontvangen, is het geretourneerde eindpunt het eindpunt dat voor dat IP-adres is toegewezen. 

Zie [Verkeersbeheermethoden voor](traffic-manager-routing-methods.md)meer informatie .

> [!NOTE]
> Een uitzondering op normaal gedrag van verkeersroutering treedt op wanneer alle in aanmerking komende eindpunten een gedegradeerde status hebben. Traffic Manager doet een "best effort" poging en *reageert alsof alle gedegradeerde status eindpunten daadwerkelijk in een online staat*. Dit gedrag verdient de voorkeur boven het alternatief, wat zou zijn om geen eindpunt terug te keren in de DNS-respons. Uitgeschakelde of gestopte eindpunten worden niet gecontroleerd, daarom komen ze niet in aanmerking voor verkeer.
>
> Deze voorwaarde wordt vaak veroorzaakt door een onjuiste configuratie van de service, zoals:
>
> * Een toegangscontrolelijst [ACL] die de statuscontroles van de verkeersbeheerder blokkeert.
> * Een onjuiste configuratie van de bewakingspoort of het protocol in het profiel van de verkeersbeheerder.
>
> Het gevolg van dit gedrag is dat als de statuscontroles van Traffic Manager niet correct zijn geconfigureerd, deze mogelijk wordt weergegeven vanaf de verkeersroutering alsof Traffic Manager naar behoren *werkt.* In dit geval kan echter geen endpoint failover plaatsvinden die van invloed is op de algehele beschikbaarheid van toepassingen. Het is belangrijk om te controleren of het profiel een online status weergeeft, niet een gedegradeerde status. Een status Online geeft aan dat de statuscontroles van verkeersbeheerbeheer werken zoals verwacht.

Zie Status oplossen oplossen van [problemen oplossen in Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md)voor meer informatie over het oplossen van mislukte statuscontroles.

## <a name="faqs"></a>Veelgestelde vragen

* [Is Traffic Manager bestand tegen azure-regiofouten?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-traffic-manager-resilient-to-azure-region-failures)

* [Welke invloed heeft de locatie van de resourcegroep op Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-the-choice-of-resource-group-location-affect-traffic-manager)

* [Hoe bepaal ik de huidige status van elk eindpunt?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-determine-the-current-health-of-each-endpoint)

* [Kan ik HTTPS-eindpunten controleren?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-monitor-https-endpoints)

* [Gebruik ik een IP-adres of een DNS-naam bij het toevoegen van een eindpunt?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint)

* [Welke typen IP-adressen kan ik gebruiken bij het toevoegen van een eindpunt?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint)

* [Kan ik verschillende endpoint-adresseringstypen in één profiel gebruiken?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-different-endpoint-addressing-types-within-a-single-profile)

* [Wat gebeurt er als het recordtype van een binnenkomende query verschilt van het recordtype dat is gekoppeld aan het adresseringstype van de eindpunten?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints)

* [Kan ik een profiel met IPv4/ IPv6 geadresseerde eindpunten gebruiken in een genest profiel?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile)

* [Ik stopte een eindpunt van een webtoepassing in mijn Traffic Manager-profiel, maar ik ontvang geen verkeer, zelfs niet nadat ik het opnieuw heb opgestart. Hoe kan ik dit oplossen?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this)

* [Kan ik Traffic Manager gebruiken, zelfs als mijn toepassing geen ondersteuning heeft voor HTTP of HTTPS?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https)

* [Welke specifieke antwoorden zijn vereist vanaf het eindpunt bij het gebruik van TCP-monitoring?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring)

* [Hoe snel haalt Traffic Manager mijn gebruikers weg van een ongezond eindpunt?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint)

* [Hoe kan ik verschillende bewakingsinstellingen opgeven voor verschillende eindpunten in een profiel?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile)

* [Hoe kan ik HTTP-headers toewijzen aan de statuscontroles van Traffic Manager aan mijn eindpunten?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints)

* [Welke hostheader gebruiken eindpuntstatuscontroles?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-host-header-do-endpoint-health-checks-use)

* [Wat zijn de IP-adressen waar de gezondheidscontroles vandaan komen?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-ip-addresses-from-which-the-health-checks-originate)

* [Hoeveel gezondheidscontroles voor mijn eindpunt kan ik van Traffic Manager verwachten?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager)

* [Hoe kan ik een melding krijgen als een van mijn eindpunten naar beneden gaat?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-get-notified-if-one-of-my-endpoints-goes-down)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [hoe Traffic Manager werkt](traffic-manager-how-it-works.md)

Meer informatie over de [verkeersrouteringsmethoden](traffic-manager-routing-methods.md) die worden ondersteund door Traffic Manager

Meer informatie over het [maken van een Traffic Manager-profiel](traffic-manager-manage-profiles.md)

[De gedegradeerde status op](traffic-manager-troubleshooting-degraded.md) een eindpunt van Traffic Manager oplossen
