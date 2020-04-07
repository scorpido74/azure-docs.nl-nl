---
title: Load-balancing services gebruiken in Azure | Microsoft Documenten
description: 'In deze zelfstudie ziet u hoe u een scenario maakt met behulp van het Azure load-balancing portfolio: Traffic Manager, Application Gateway en Load Balancer.'
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: rohink
ms.openlocfilehash: cccd4a6b0b52608a6a17b73688e18f27088df5b0
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757204"
---
# <a name="using-load-balancing-services-in-azure"></a>Taakverdelingsservices gebruiken in Azure

## <a name="introduction"></a>Inleiding

Microsoft Azure biedt meerdere services voor het beheren van de manier waarop netwerkverkeer wordt gedistribueerd en in balans wordt gebracht. U deze diensten individueel gebruiken of hun methoden combineren, afhankelijk van uw behoeften, om de optimale oplossing te bouwen.

In deze zelfstudie definiëren we eerst een use case voor klanten en zien we hoe deze robuuster en performanter kan worden gemaakt met behulp van de volgende Azure load-balancing portfolio: Traffic Manager, Application Gateway en Load Balancer. Vervolgens geven we stapsgewijze instructies voor het maken van een implementatie die geografisch redundant is, verkeer naar VM's distribueert en u verschillende soorten aanvragen beheren.

Op conceptueel niveau speelt elk van deze services een duidelijke rol in de hiërarchie van de taakverdeling.

* **Traffic Manager** biedt globale DNS-taakverdeling. Het kijkt naar inkomende DNS-verzoeken en reageert met een gezond eindpunt, in overeenstemming met het routeringsbeleid dat de klant heeft geselecteerd. Opties voor routeringsmethoden zijn:
  * Prestatieroutering om de aanvrager naar het dichtstbijzijnde eindpunt te sturen in termen van latentie.
  * Prioriteitsroutering om al het verkeer naar een eindpunt te leiden, met andere eindpunten als back-up.
  * Gewogen round-robin routing, die verkeer verdeelt op basis van de weging die is toegewezen aan elk eindpunt.
  * Routering op basis van geografie om het verkeer te distribueren naar de eindpunten van uw toepassing op basis van de geografische locatie van de gebruiker.
  * Subnetgebaseerde routering om het verkeer te distribueren naar de eindpunten van uw toepassing op basis van het subnet (IP-adresbereik) van de gebruiker.
  * Multi Value routing waarmee u IP-adressen van meer dan één toepassingseindpunt verzenden in één DNS-antwoord.

  De client maakt rechtstreeks verbinding met het eindpunt dat door Traffic Manager wordt geretourneerd. Azure Traffic Manager detecteert wanneer een eindpunt niet in orde is en leidt de clients vervolgens door naar een andere gezonde instantie. Raadpleeg [azure traffic manager-documentatie](traffic-manager-overview.md) voor meer informatie over de service.
* **Application Gateway** biedt application delivery controller (ADC) als een service, met verschillende Layer 7 load-balancing mogelijkheden voor uw toepassing. Het stelt klanten in staat om de productiviteit van webfarmen te optimaliseren door CPU-intensieve TLS-beëindiging te ontladen naar de toepassingsgateway. Andere Routeringsmogelijkheden van Layer 7 zijn round-robin-distributie van binnenkomend verkeer, sessieaffiniteit op basis van cookies, URL-padgebaseerde routering en de mogelijkheid om meerdere websites achter één toepassingsgateway te hosten. Application Gateway kan worden geconfigureerd als een naar internet gerichte gateway, een interne gateway of een combinatie van beide. Application Gateway is volledig azure beheerd, schaalbaar en zeer beschikbaar. Het biedt een uitgebreide verzameling diagnostische gegevens en functies voor logboekregistratie voor betere beheersbaarheid.
* **Load Balancer** is een integraal onderdeel van de Azure SDN-stack en biedt krachtige, low-latency Layer 4 load-balancing services voor alle UDP- en TCP-protocollen. Het beheert inkomende en uitgaande verbindingen. U kunt openbare en interne load-balancedeindpunten configureren en regels definiëren om binnenkomende verbindingen toe te wijzen aan back-end-poolbestemmingen met behulp van TCP- en HTTP-opties voor statustesten om de beschikbaarheid van de service te beheren.

## <a name="scenario"></a>Scenario

In dit voorbeeldscenario gebruiken we een eenvoudige website die twee soorten inhoud bedient: afbeeldingen en dynamisch weergegeven webpagina's. De website moet geografisch overbodig zijn en moet zijn gebruikers van de dichtstbijzijnde (laagste latentie) locatie naar hen bedienen. De toepassingsontwikkelaar heeft besloten dat alle URL's die overeenkomen met het patroon /afbeeldingen/* worden geserveerd vanuit een speciale groep VM's die verschillen van de rest van de webfarm.

Bovendien moet de standaard VM-groep met de dynamische inhoud praten met een back-enddatabase die wordt gehost op een cluster met hoge beschikbaarheid. De volledige implementatie wordt ingesteld via Azure Resource Manager.

Met behulp van Traffic Manager, Application Gateway en Load Balancer kan deze website deze ontwerpdoelen bereiken:

* **Multi-geo redundantie**: Als één regio uitvalt, leidt Traffic Manager het verkeer naadloos naar de dichtstbijzijnde regio zonder tussenkomst van de eigenaar van de toepassing.
* **Verminderde latentie**: Omdat Traffic Manager de klant automatisch naar de dichtstbijzijnde regio leidt, ervaart de klant een lagere latentie bij het aanvragen van de inhoud van de webpagina.
* **Onafhankelijke schaalbaarheid**: Omdat de werkbelasting van de webtoepassing wordt gescheiden door het type inhoud, kan de eigenaar van de toepassing de aanvraagworkloads onafhankelijk van elkaar schalen. Application Gateway zorgt ervoor dat het verkeer wordt doorgestuurd naar de juiste pools op basis van de opgegeven regels en de status van de toepassing.
* **Interne taakverdeling:** Omdat Load Balancer zich voor het cluster met hoge beschikbaarheid bevindt, wordt alleen het actieve en gezonde eindpunt voor een database blootgesteld aan de toepassing. Bovendien kan een databasebeheerder de werkbelasting optimaliseren door actieve en passieve replica's over het cluster te distribueren, onafhankelijk van de front-endtoepassing. Load Balancer levert verbindingen met het cluster met hoge beschikbaarheid en zorgt ervoor dat alleen gezonde databases verbindingsaanvragen ontvangen.

In het volgende diagram ziet u de architectuur van dit scenario:

![Diagram van load-balancing architectuur](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> Dit voorbeeld is slechts een van de vele mogelijke configuraties van de load-balancing services die Azure biedt. Traffic Manager, Application Gateway en Load Balancer kunnen worden gemengd en afgestemd op uw behoeften op het gebied van taakverdeling. Als TLS-offload of Layer 7-verwerking bijvoorbeeld niet nodig is, kan Load Balancer worden gebruikt in plaats van Application Gateway.

## <a name="setting-up-the-load-balancing-stack"></a>De load-balancing stack instellen

### <a name="step-1-create-a-traffic-manager-profile"></a>Stap 1: Een verkeersbeheerprofiel maken

1. Klik in de Azure-portal op **Een resource** > **Networking** > **Traffic Manager-profiel** > **maken**.
2. Voer de volgende basisgegevens in:

   * **Naam:** Geef uw Traffic Manager-profiel een DNS-voorvoegselnaam.
   * **Routeringsmethode:** selecteer het beleid voor de routeringsmethode voor verkeer. Zie [Over verkeersbeheermethoden](traffic-manager-routing-methods.md)voor meer informatie over de methoden .
   * **Abonnement**: Selecteer het abonnement dat het profiel bevat.
   * **Resourcegroep:** selecteer de resourcegroep die het profiel bevat. Het kan een nieuwe of bestaande brongroep zijn.
   * **Locatie resourcegroep**: Traffic Manager-service is globaal en niet gebonden aan een locatie. U moet echter wel een gebied opgeven voor de groep waar de metagegevens die zijn gekoppeld aan het Traffic Manager-profiel zich bevinden. Deze locatie heeft geen invloed op de beschikbaarheid van runtime van het profiel.

3. Klik **op Maken** om het profiel Verkeersbeheer te genereren.

   ![Blade 'Traffic Manager' maken](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>Stap 2: De toepassingsgateways maken

1. Klik in de Azure-portal in het linkerdeelvenster op **Een gateway voor een bronnetwerktoepassing** > **Networking** > **maken**.
2. Voer de volgende basisgegevens over de toepassingsgateway in:

   * **Naam:** De naam van de toepassingsgateway.
   * **SKU-grootte**: de grootte van de toepassingsgateway, beschikbaar als klein, gemiddeld of groot.
   * **Aantal instanties:** het aantal exemplaren, een waarde van 2 tot en met 10.
   * **Resourcegroep:** de resourcegroep die de toepassingsgateway bevat. Het kan een bestaande resourcegroep of een nieuwe groep zijn.
   * **Locatie:** het gebied voor de toepassingsgateway, dezelfde locatie als de resourcegroep. De locatie is belangrijk, omdat het virtuele netwerk en het openbare IP-adres zich op dezelfde locatie moeten bevinden als de gateway.
3. Klik op **OK**.
4. Definieer de virtuele netwerk-, subnet-, front-end IP- en listenerconfiguraties voor de toepassingsgateway. In dit scenario is het front-end IP-adres **Openbaar,** waardoor het later als eindpunt kan worden toegevoegd aan het Traffic Manager-profiel.
5. Configureer de listener met een van de volgende opties:
    * Als u HTTP gebruikt, is er niets om te configureren. Klik op **OK**.
    * Als u HTTPS gebruikt, is verdere configuratie vereist. Raadpleeg [Een toepassingsgateway maken,](../application-gateway/application-gateway-create-gateway-portal.md)te beginnen bij stap 9. Klik op **OK**wanneer u de configuratie hebt voltooid.

#### <a name="configure-url-routing-for-application-gateways"></a>URL-routering configureren voor toepassingsgateways

Wanneer u een back-endpool kiest, neemt een toepassingsgateway die is geconfigureerd met een op paden gebaseerde regel een padpatroon van de aanvraag-URL naast de round-robin-verdeling. In dit scenario voegen we een op paden gebaseerde regel\*toe om elke URL met "/images/" naar de afbeeldingsservergroep te sturen. Raadpleeg [Een op pad gebaseerde regel voor een toepassingsgateway maken voor](../application-gateway/application-gateway-create-url-route-portal.md)meer informatie over het configureren van URL-padgebaseerde routering voor een toepassingsgateway.

![Webtierdiagram voor toepassingsgateway](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. Ga vanuit uw resourcegroep naar de instantie van de toepassingsgateway die u in de vorige sectie hebt gemaakt.
2. Selecteer **onder Instellingen**de optie **Backend-groepen**en selecteer Vervolgens **Toevoegen** om de VM's toe te voegen die u wilt koppelen aan de back-endgroepen van de webrijlaag.
3. Voer de naam in van de back-endpool en alle IP-adressen van de machines die zich in de groep bevinden. In dit scenario verbinden we twee back-endservergroepen van virtuele machines.

   ![Toepassingsgateway "Backend pool toevoegen"](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. Selecteer **onder Instellingen** van de toepassingsgateway **Regels**en klik vervolgens op de knop Pad **op basis** van de knop om een regel toe te voegen.

   ![Knop Toepassingsgatewayregels 'Pad gebaseerd'](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. Configureer de regel door de volgende informatie op te geven.

   Basisinstellingen:

   + **Naam**: De vriendelijke naam van de regel die toegankelijk is in het portaal.
   + **Listener:** De listener die wordt gebruikt voor de regel.
   + **Standaardbackendpool**: de back-endpool die moet worden gebruikt met de standaardregel.
   + **StandaardHTTP-instellingen:** de HTTP-instellingen die moeten worden gebruikt met de standaardregel.

   Op paden gebaseerde regels:

   + **Naam:** De vriendelijke naam van de op paden gebaseerde regel.
   + **Paden:** de padregel die wordt gebruikt voor het doorsturen van verkeer.
   + **Backend Pool**: De back-end pool die met deze regel moet worden gebruikt.
   + **HTTP-instelling:** de HTTP-instellingen die met deze regel moeten worden gebruikt.

   > [!IMPORTANT]
   > Paden: Geldige paden moeten beginnen met "/". De wildcard\*" " is alleen toegestaan aan het einde. Geldige voorbeelden zijn /xyz, /xyz\*of /xyz/\*.

   ![Blade van het blad 'Op pad gebaseerde regel toevoegen'](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>Stap 3: Toepassingsgateways toevoegen aan de eindpunten van Traffic Manager

In dit scenario is Traffic Manager verbonden met toepassingsgateways (zoals geconfigureerd in de voorgaande stappen) die zich in verschillende regio's bevinden. Nu de toepassingsgateways zijn geconfigureerd, is de volgende stap om ze te verbinden met uw Traffic Manager-profiel.

1. Open uw Traffic Manager-profiel. Zoek hiervoor in uw resourcegroep of zoek naar de naam van het profiel Traffic Manager van **Alle bronnen**.
2. Selecteer **Eindpunten**in het linkerdeelvenster en klik op **Toevoegen** om een eindpunt toe te voegen.

   ![Knop Eindpunten voor verkeersbeheer](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. Maak een eindpunt door de volgende gegevens in te voeren:

   * **Type:** Selecteer het type eindpunt dat moet worden geladen. Selecteer in dit scenario **Azure-eindpunt** omdat we het verbinden met de toepassingsgateway-exemplaren die eerder zijn geconfigureerd.
   * **Naam:** Voer de naam van het eindpunt in.
   * **Doelbrontype**: Selecteer **Openbaar IP-adres** en selecteer vervolgens onder **Doelbron**het openbare IP-adres van de eerder geconfigureerde toepassingsgateway.

   ![Traffic Manager "Eindpunt toevoegen"](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. Nu u uw setup testen door deze te openen met de DNS van uw Traffic Manager-profiel (in dit voorbeeld: TrafficManagerScenario.trafficmanager.net). U aanvragen opnieuw verzenden, VM's en webservers die in verschillende regio's zijn gemaakt, weer naar boven halen of neerhalen en de profielinstellingen van Traffic Manager wijzigen om uw installatie te testen.

### <a name="step-4-create-a-load-balancer"></a>Stap 4: Een load balancer maken

In dit scenario distribueert Load Balancer verbindingen van de weblaag naar de databases binnen een cluster met hoge beschikbaarheid.

Als uw databasecluster met hoge beschikbaarheid SQL Server AlwaysOn gebruikt, raadpleegt u [Een of meer Always On Availability Group Listeners configureren](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) voor stapsgewijze instructies.

Zie [Een interne load balancer maken in de Azure-portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)voor meer informatie over het configureren van een interne load balancer.

1. Klik in de Azure-portal in het linkerdeelvenster op **Een** > **resource Netwerklastsbalans****Networking** > maken .
2. Kies een naam voor uw load balancer.
3. Stel het **type** in **op Intern**en kies het juiste virtuele netwerk en subnet waar de load balancer zich in moet bevinden.
4. Selecteer **onder IP-adrestoewijzing** **Dynamisch** of **Statisch**.
5. Kies **onder Resourcegroep**de resourcegroep voor de load balancer.
6. Kies **onder Locatie**de juiste regio voor de load balancer.
7. Klik **op Maken** om de load balancer te genereren.

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>Een back-enddatabaselaag verbinden met de load balancer

1. Zoek in de vorige stappen de load balancer die in de vorige stappen is gemaakt.
2. Klik **onder Instellingen**op **Backend-groepen**en klik vervolgens op **Toevoegen** om een back-endpool toe te voegen.

   ![Load Balancer "Backend pool toevoegen"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. Voer de naam van de back-endpool in.
4. Voeg afzonderlijke machines of een beschikbaarheidsset toe aan de back-endpool.

#### <a name="configure-a-probe"></a>Een sonde configureren

1. Selecteer in uw load balancer onder **Instellingen**de optie **Sondes**en klik vervolgens op **Toevoegen** om een sonde toe te voegen.

   ![Load Balancer "Probe toevoegen"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. Voer de naam voor de sonde in.
3. Selecteer het **protocol** voor de sonde. Voor een database wilt u mogelijk een TCP-sonde in plaats van een HTTP-sonde. Zie [Sondes van load balancer](../load-balancer/load-balancer-custom-probe-overview.md)begrijpen voor meer informatie over sondes van load-balancer .
4. Voer de **poort** van uw database in die moet worden gebruikt voor toegang tot de sonde.
5. Geef **onder Interval**op hoe vaak de toepassing moet worden gesonde.
6. Geef **onder Onineerdrempel**het aantal continue sondefouten op dat moet optreden om de back-end-vm als niet-inord te stellen.
7. Klik op **OK** om de sonde te maken.

#### <a name="configure-the-load-balancing-rules"></a>De regels voor het balanceren van de lastenafweging configureren

1. Selecteer **onder Instellingen** van uw load balancer de optie **Taakverdelingsregels**en klik vervolgens op **Toevoegen** om een regel te maken.
2. Voer de **naam** in voor de regel voor het balanceren van de lastenverdeling.
3. Kies het **IP-adres frontend** van de load balancer, **Protocol**en **Port**.
4. Geef **onder Backend-poort**de poort op die in de back-endpool moet worden gebruikt.
5. Selecteer de **groep Backend** en de **sonde** die in de vorige stappen zijn gemaakt om de regel op toe te passen.
6. Kies **onder Sessiepersistentie**hoe u de sessies wilt laten voortduren.
7. Geef **onder Idle time-outs**het aantal minuten op voor een niet-actieve time-out.
8. Selecteer **onder Zwevend IP**de optie **Uitgeschakeld** of **Ingeschakeld**.
9. Klik op **OK** om de regel te maken.

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>Stap 5: VM's met webniveau verbinden met de load balancer

Nu configureren we het IP-adres en de front-endpoort van de load-balancer in de toepassingen die worden uitgevoerd op uw vm's op het webniveau voor alle databaseverbindingen. Deze configuratie is specifiek voor de toepassingen die op deze VM's worden uitgevoerd. Als u het ip-adres en de poort van de bestemming wilt configureren, raadpleegt u de toepassingsdocumentatie. Ga naar de front-end-ip-pool op de **instellingen voor load balancer**om het IP-adres van de front-end te vinden.

![Navigatiedeelvenster 'Frontend IP-pool' van Load Balancer](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Traffic Manager](traffic-manager-overview.md)
* [Overzicht van Application Gateway](../application-gateway/application-gateway-introduction.md)
* [Overzicht van Azure Load Balancer](../load-balancer/load-balancer-overview.md)
