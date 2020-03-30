---
title: Herstel na noodgevallen met Azure DNS en Traffic Manager | Microsoft Documenten
description: Overzicht van de oplossingen voor noodherstel met Azure DNS en Traffic Manager.
services: dns
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/08/2018
ms.author: kumud
ms.openlocfilehash: 6eab1803bf5adab42be87b5f8567682c6d75947e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74483534"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Herstel na noodgevallen met Azure DNS en Traffic Manager

Herstel na noodgevallen is gericht op het herstellen van een ernstig verlies van de functionaliteit van de toepassing. Om een oplossing voor noodherstel te kiezen, moeten eigenaren van bedrijven en technologie eerst het niveau van functionaliteit bepalen dat tijdens een ramp vereist is, zoals - niet beschikbaar, gedeeltelijk beschikbaar via verminderde functionaliteit of vertraagde beschikbaarheid, of volledig beschikbaar.
De meeste zakelijke klanten kiezen voor een multi-regio architectuur voor tolerantie tegen een toepassing of infrastructuur niveau failover. Klanten kunnen verschillende benaderingen kiezen in de zoektocht naar failover en hoge beschikbaarheid via redundante architectuur. Hier zijn enkele van de populaire benaderingen:

- **Actief-passief met koude stand-by**: In deze failoveroplossing zijn de VM's en andere apparaten die in het stand-bygebied draaien niet actief totdat er behoefte is aan failover. De productieomgeving wordt echter gerepliceerd in de vorm van back-ups, VM-afbeeldingen of Resource Manager-sjablonen naar een andere regio. Dit failovermechanisme is kosteneffectief, maar neemt langer de tijd om een volledige failover te ondernemen.
 
    ![Actief/Passief met koude stand-by](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    *Figuur - Actief/Passief met koude stand-by noodherstelconfiguratie*

- **Actief/Passief met proeflicht**: In deze failoveroplossing wordt de stand-by omgeving ingesteld met een minimale configuratie. De installatie heeft alleen de benodigde services die worden uitgevoerd om slechts een minimale en kritieke set toepassingen te ondersteunen. In zijn oorspronkelijke vorm kan dit scenario alleen minimale functionaliteit uitvoeren, maar kan aanvullende services worden opgeschaald en spawnen om het grootste deel van de productiebelasting te nemen als er een failover optreedt.
    
    ![Actief/Passief met waaklicht](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *Figuur: Actief/Passief met pilot light disaster recovery configuratie*

- **Actief/passief met warme stand-by**: In deze failoveroplossing is het stand-bygebied voorverwarmd en is het klaar om de basisbelasting te nemen, wordt automatisch schalen ingeschakeld en zijn alle exemplaren ingeschakeld. Deze oplossing wordt niet geschaald om de volledige productiebelasting te nemen, maar is functioneel en alle services zijn operationeel. Deze oplossing is een verbeterde versie van de pilot licht aanpak.
    
    ![Actief/Passief met warme stand-by](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    *Figuur: Actief/Passief met warme stand-by disaster recovery configuratie*
    
Zie [Disaster Recovery voor Azure-toepassingen voor](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications)meer informatie over failover en hoge beschikbaarheid.


## <a name="planning-your-disaster-recovery-architecture"></a>Uw disaster recovery-architectuur plannen

Er zijn twee technische aspecten voor het opzetten van uw disaster recovery architectuur:
-  Een implementatiemechanisme gebruiken om instanties, gegevens en configuraties te repliceren tussen primaire en stand-byomgevingen. Dit type disaster recovery kan native worden uitgevoerd via Azure Site-Recovery via Microsoft Azure-partnerappliances/services zoals Veritas of NetApp. 
- Het ontwikkelen van een oplossing om netwerk- en webverkeer om te leiden van de primaire site naar de stand-by site. Dit type disaster recovery kan worden bereikt via Azure DNS, Azure Traffic Manager (DNS) of externe globale load balancers.

Dit artikel is beperkt tot benaderingen via netwerk- en webverkeeromleiding. Zie [Azure Site Recovery Documentation](https://docs.microsoft.com/azure/site-recovery/)voor instructies voor het instellen van Azure Site Recovery .
DNS is een van de meest efficiënte mechanismen om netwerkverkeer om te leiden omdat DNS vaak wereldwijd en extern is in het datacenter en is geïsoleerd van eventuele fouten op het niveau van de regionale of beschikbaarheidszone (AZ). Men kan een dns-gebaseerd failovermechanisme gebruiken en in Azure kunnen twee DNS-services op een bepaalde manier hetzelfde bereiken - Azure DNS (gezaghebbende DNS) en Azure Traffic Manager (DNS-gebaseerde smart traffic routing). 

Het is belangrijk om weinig concepten in DNS te begrijpen die op grote schaal worden gebruikt om de oplossingen in dit artikel te bespreken:
- **DNS A Record** – A Records zijn aanwijzers die een domein naar een IPv4-adres wijzen. 
- **CNAME of Canonical name** - Dit recordtype wordt gebruikt om naar een andere DNS-record te wijzen. CNAME reageert niet met een IP-adres, maar met de aanwijzer naar de record die het IP-adres bevat. 
- **Gewogen routering** - men kan ervoor kiezen om een gewicht te koppelen aan service eindpunten en vervolgens het verkeer te verdelen op basis van de toegewezen gewichten. Deze routeringsmethode is een van de vier verkeersrouteringsmechanismen die beschikbaar zijn binnen Traffic Manager. Zie [Gewogen routeringsmethode](../traffic-manager/traffic-manager-routing-methods.md#weighted)voor meer informatie .
- **Prioriteitsroutering** : prioriteitsroutering is gebaseerd op statuscontroles van eindpunten. Azure Traffic Manager stuurt standaard al het verkeer naar het eindpunt met de hoogste prioriteit en bij een storing of ramp leidt Traffic Manager het verkeer naar het secundaire eindpunt. Zie [Prioriteitsrouteringsmethode](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method)voor meer informatie .

## <a name="manual-failover-using-azure-dns"></a>Handmatige failover met Azure DNS
De Azure DNS-handmatige failoveroplossing voor noodherstel maakt gebruik van het standaard DNS-mechanisme om niet naar de back-upsite te gaan. De handmatige optie via Azure DNS werkt het beste wanneer deze wordt gebruikt in combinatie met de koude stand-by of de pilotlichtbenadering. 

![Handmatige failover met Azure DNS](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

*Figuur - Handmatige failover met Azure DNS*

De veronderstellingen voor de oplossing zijn:
- Zowel primaire als secundaire eindpunten hebben statische IP's die niet vaak veranderen. Zeg voor de primaire site het IP is 100.168.124.44 en het IP voor de secundaire site is 100.168.124.43.
- Er bestaat een Azure DNS-zone voor zowel de primaire als de secundaire site. Zeg voor de primaire site het eindpunt is prod.contoso.com en voor de back-up site is dr.contoso.com. Een DNS-record voor de\.belangrijkste toepassing bekend als www contoso.com bestaat ook.   
- De TTL bevindt zich op of onder de RTO SLA set in de organisatie. Bijvoorbeeld, als een onderneming stelt de RTO van de toepassing ramp reactie op 60 minuten, dan is de TTL moet minder dan 60 minuten, bij voorkeur hoe lager hoe beter. 
  U Azure DNS als volgt instellen voor handmatige failover:
- Een DNS-zone maken
- DNS-zonerecords maken
- CNAME-record bijwerken

### <a name="step-1-create-a-dns"></a>Stap 1: Een DNS maken
Maak een DNS-zone (bijvoorbeeld www\.contoso.com) zoals hieronder weergegeven:

![Een DNS-zone maken in Azure](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

*Figuur - Een DNS-zone maken in Azure*

### <a name="step-2-create-dns-zone-records"></a>Stap 2: DNS-zonerecords maken

Maak binnen deze zone drie records\.(bijvoorbeeld - www contoso.com, prod.contoso.com en dr.consoto.com) zoals hieronder te zien.

![DNS-zonerecords maken](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

*Figuur - DNS-zonerecords maken in Azure*

In dit scenario,\.site, www contoso.com heeft een TTL van 30 minuten, die ver onder de genoemde RTO, en wijst naar de productiesite prod.contoso.com. Deze configuratie is tijdens normale bedrijfsactiviteiten. De TTL van prod.contoso.com en dr.contoso.com is ingesteld op 300 seconden of 5 minuten. U een Azure-bewakingsservice gebruiken, zoals Azure Monitor of Azure App Insights, of, oplossingen voor partnerbewaking zoals Dynatrace, U zelfs oplossingen van eigen bodem gebruiken die fouten op toepassing- of virtuele infrastructuurniveau kunnen controleren of detecteren.

### <a name="step-3-update-the-cname-record"></a>Stap 3: De CNAME-record bijwerken

Zodra de fout is gedetecteerd, wijzigt u de recordwaarde om te wijzen op dr.contoso.com zoals hieronder wordt weergegeven:
       
![CNAME-record bijwerken](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

*Afbeelding - De CNAME-record bijwerken in Azure*

Binnen 30 minuten, waarin de meeste resolvers het opgeslagen zonebestand in de cache vernieuwen, wordt elke query naar www\.contoso.com doorgestuurd naar dr.contoso.com.
U ook de volgende opdracht Azure CLI uitvoeren om de CNAME-waarde te wijzigen:
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
Deze stap kan handmatig of via automatisering worden uitgevoerd. Het kan handmatig worden gedaan via de console of door de Azure CLI. De Azure SDK en API kunnen worden gebruikt om de CNAME-update te automatiseren, zodat er geen handmatige interventie nodig is. Automatisering kan worden gebouwd via Azure-functies of binnen een bewakingstoepassing van derden of zelfs van on-premises.

### <a name="how-manual-failover-works-using-azure-dns"></a>Hoe handmatige failover werkt met Azure DNS
Aangezien de DNS-server zich buiten het failover- of rampgebied bevindt, is deze geïsoleerd tegen elke downtime. Dit stelt de gebruiker in staat om een eenvoudig failoverscenario te ontwerpen dat kosteneffectief is en de hele tijd zal werken in de veronderstelling dat de operator netwerkconnectiviteit heeft tijdens een ramp en de flip kan maken. Als de oplossing is gescript, moet men ervoor zorgen dat de server of service waarop het script wordt uitgevoerd, moet worden geïsoleerd tegen het probleem dat van invloed is op de productieomgeving. Houd ook rekening met de lage TTL die tegen de zone is ingesteld, zodat geen enkele resolver over de hele wereld het eindpunt lang in de cache houdt en klanten toegang hebben tot de site binnen de RTO. Voor een koude stand-by en pilot licht, omdat sommige prewarming en andere administratieve activiteit kan nodig zijn - men moet ook genoeg tijd geven voordat u de flip.

## <a name="automatic-failover-using-azure-traffic-manager"></a>Automatische failover met Azure Traffic Manager
Wanneer u complexe architecturen en meerdere sets resources hebt die dezelfde functie kunnen uitvoeren, u Azure Traffic Manager (op basis van DNS) configureren om de status van uw resources te controleren en het verkeer van de niet-gezonde bron naar de gezonde bron te leiden Resource. In het volgende voorbeeld hebben zowel de primaire regio als de secundaire regio een volledige implementatie. Deze implementatie omvat de cloudservices en een gesynchroniseerde database. 

![Automatische failover met Azure Traffic Manager](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

*Figuur - Automatische failover met Azure Traffic Manager*

Alleen de primaire regio verwerkt echter actief netwerkaanvragen van de gebruikers. De secundaire regio wordt alleen actief wanneer de primaire regio een onderbreking van de service ondervindt. In dat geval worden alle nieuwe netwerkaanvragen omgeleid naar de secundaire regio. Aangezien de back-up van de database bijna onmiddellijk is, hebben zowel de load balancers IP's die kunnen worden gecontroleerd op de gezondheid, en de exemplaren zijn altijd up and running, deze topologie biedt een optie voor het gaan voor een lage RTO en failover zonder handmatige tussenkomst. De secundaire failoverregio moet onmiddellijk na het mislukken van de primaire regio klaar zijn om te gaan leven.
Dit scenario is ideaal voor het gebruik van Azure Traffic Manager dat sondes heeft ingebouwd voor verschillende soorten statuscontroles, waaronder http / https en TCP. Azure Traffic manager heeft ook een regelengine die kan worden geconfigureerd om te mislukken wanneer een fout optreedt zoals hieronder beschreven. Laten we de volgende oplossing bekijken met Traffic Manager:
- De klant heeft het regio-#1 eindpunt dat bekend staat als prod.contoso.com met een statisch IP-adres als 100.168.124.44 en een regio-#2 eindpunt dat bekend staat als dr.contoso.com met een statisch IP-adres als 100.168.124.43. 
-   Elk van deze omgevingen wordt vooraan gefronted via een openbare gevel eigendom als een load balancer. De load balancer kan worden geconfigureerd met een DNS-gebaseerd eindpunt of een volledig gekwalificeerde domeinnaam (FQDN) zoals hierboven weergegeven.
-   Alle exemplaren in regio 2 zijn in bijna realtime replicatie met regio 1. Bovendien zijn de machineafbeeldingen up-to-date en worden alle software-/configuratiegegevens gepatcht en zijn ze in lijn met regio 1.  
-   Automatisch schalen is vooraf geconfigureerd. 

De stappen die zijn genomen om de failover met Azure Traffic Manager te configureren, zijn als volgt:
1. Een nieuw Azure Traffic Manager-profiel maken
2. Eindpunten maken in het profiel Traffic Manager
3. Statuscontrole en failoverconfiguratie instellen

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>Stap 1: Een nieuw Azure Traffic Manager-profiel maken
Maak een nieuw Azure Traffic Manager-profiel met de naam contoso123 en selecteer de routeringsmethode als prioriteit. Als u een reeds bestaande resourcegroep hebt waaraan u wilt koppelen, u een bestaande resourcegroep selecteren, anders u een nieuwe resourcegroep maken.

![Profiel verkeersbeheer maken](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)

*Figuur - Een profiel van verkeersbeheer maken*

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>Stap 2: Eindpunten maken in het profiel Traffic Manager

In deze stap maakt u eindpunten die wijzen op de productie- en noodherstellocaties. Kies hier het **type** als extern eindpunt, maar als de bron wordt gehost in Azure, u ook **Azure-eindpunt** kiezen. Als u **Azure-eindpunt**kiest, selecteert u een **Doelbron** die een **App-service** of een **openbaar IP-adres** is dat door Azure wordt toegewezen. De prioriteit is ingesteld als **1** omdat deze de primaire service is voor regio 1.
Maak ook het eindpunt voor noodherstel binnen Traffic Manager.

![Eindpunten voor noodherstel maken](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

*Figuur - Eindpunten voor noodherstel maken*

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>Stap 3: Configuratie van statuscontrole en failoverconfiguratie

In deze stap stelt u de DNS TTL in op 10 seconden, die wordt gehonoreerd door de meeste op internet gerichte recursieve resolvers. Deze configuratie betekent dat geen enkele DNS-resolver de informatie langer dan 10 seconden in de cache bewaart. Voor de instellingen voor eindpuntmonitor is het pad actueel ingesteld op / of root, maar u de eindpuntinstellingen aanpassen om een pad te evalueren, bijvoorbeeld prod.contoso.com/index. Het onderstaande voorbeeld toont de **https** als het indringende protocol. U echter ook kiezen voor **http** of **tcp.** De keuze van het protocol is afhankelijk van de eindtoepassing. Het indringende interval is ingesteld op 10 seconden, waardoor snel indringende mogelijk is en de nieuwe poging is ingesteld op 3. Als gevolg hiervan mislukt Traffic Manager naar het tweede eindpunt als drie opeenvolgende intervallen een fout registreren. De volgende formule definieert de totale tijd voor een geautomatiseerde failover: Tijd voor failover = TTL + Opnieuw proberen * Indringende interval En in dit geval is de waarde 10 + 3 * 10 = 40 seconden (Max).
Als de opnieuw proberen is ingesteld op 1 en TTL is ingesteld op 10 seconden, dan is de tijd voor failover 10 + 1 * 10 = 20 seconden. Stel de Opnieuw proberen op een waarde groter dan **1** om de kans op failovers als gevolg van false positives of een klein netwerk blips te elimineren. 


![Statuscontrole instellen](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

*Figuur - Statuscontrole en failoverconfiguratie instellen*

### <a name="how-automatic-failover-works-using-traffic-manager"></a>Hoe automatische failover werkt met Traffic Manager

Tijdens een ramp wordt het primaire eindpunt gesondeerd en worden de statuswijzigingen in **afgebroken** en de rampherstelsite **online gehouden.** Standaard verzendt Traffic Manager al het verkeer naar het primaire eindpunt (met de hoogste prioriteit). Als het primaire eindpunt gedegradeerd wordt weergegeven, leidt Traffic Manager het verkeer naar het tweede eindpunt zolang het gezond blijft. Men heeft de mogelijkheid om meer eindpunten binnen Traffic Manager te configureren die kunnen dienen als extra failover-eindpunten, of als load balancers die de belasting tussen eindpunten delen.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md).
- Meer informatie over [Azure DNS](../dns/dns-overview.md).









