---
title: Bedrijfscontinuïteit en herstel na noodgevallen
description: Ontwerp uw strategie om gegevens te beschermen, snel te herstellen van disruptieve gebeurtenissen, resources te herstellen die vereist zijn voor kritieke bedrijfsfuncties en de bedrijfscontinuïteit voor Azure Logic Apps te behouden
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 7bf71ce7c44229ccf19022e9cfb0162f9d77cd97
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437704"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>Bedrijfscontinuïteit en noodherstel voor Azure Logic Apps

Om de impact en effecten die onvoorspelbare gebeurtenissen hebben op uw bedrijf en klanten te verminderen, moet u ervoor zorgen dat u over een [DR-oplossing *(Disaster Recovery)* ](https://en.wikipedia.org/wiki/Disaster_recovery) beschikt, zodat u gegevens beschermen, snel de resources herstellen die kritieke bedrijfsfuncties ondersteunen en activiteiten blijven uitvoeren om [ *de bedrijfscontinuïteit* (BC)](https://en.wikipedia.org/wiki/Business_continuity_planning)te behouden. Onderbrekingen kunnen bijvoorbeeld storingen, verliezen in onderliggende infrastructuur of onderdelen zoals opslag, netwerk of compute resources, onherstelbare toepassingsfouten of zelfs een volledig datacenterverlies omvatten. Door een BCDR-oplossing (Business Continuity and Disaster Recovery) klaar te hebben, kan uw onderneming of organisatie sneller reageren op onderbrekingen, gepland of ongepland, en downtime voor uw klanten verminderen.

In dit artikel vindt u BCDR-richtlijnen en -strategieën die u toepassen wanneer u geautomatiseerde werkstromen maakt met [Azure Logic Apps.](../logic-apps/logic-apps-overview.md) Met logische app-werkstromen u eenvoudig gegevens integreren en orkestreren tussen apps, cloudservices en on-premises systemen door te verminderen hoeveel code u moet schrijven. Wanneer u voor BCDR van plan bent, moet u ervoor zorgen dat u niet alleen rekening houdt met uw logische apps, maar ook met deze Azure-bronnen die u met uw logische apps gebruikt:

* [Verbindingen](../connectors/apis-list.md) die u maakt van logische apps naar andere apps, services en systemen. Zie [Verbindingen met bronnen](#resource-connections) later in dit onderwerp voor meer informatie.

* [On-premises datagateways die Azure-bronnen](../logic-apps/logic-apps-gateway-connection.md) zijn die u maakt en gebruikt in uw logische apps om toegang te krijgen tot gegevens in on-premises systemen. Elke gatewaybron vertegenwoordigt een afzonderlijke [installatie van gegevensgateways](../logic-apps/logic-apps-gateway-install.md) op een lokale computer. Zie [On-premises gegevensgateways](#on-premises-data-gateways) later in dit onderwerp voor meer informatie.

* [Integratieaccounts](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) waarin u de artefacten definieert en opslaat die logische apps gebruiken voor [B2B-scenario's (business-to-business).](../logic-apps/logic-apps-enterprise-integration-overview.md) U bijvoorbeeld [cross-region disaster recovery instellen voor integratieaccounts.](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)

* [Integratieserviceomgevingen (ISE's)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) waarin u logische apps maakt die worden uitgevoerd in een geïsoleerde Runtime-instantie van Logic Apps binnen een virtueel Azure-netwerk. Deze logische apps hebben vervolgens toegang tot bronnen die zijn beveiligd achter een firewall in dat virtuele netwerk.

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>Primaire en secundaire locaties

Elke logische app moet de locatie opgeven die u wilt gebruiken voor implementatie. Deze locatie is een openbare regio in het wereldwijde Azure met meerdere tenants, zoals 'West US' of een integratieserviceomgeving (ISE) die u eerder hebt gemaakt en geïmplementeerd in een virtueel Azure-netwerk. Het uitvoeren van logische apps in een ISE is vergelijkbaar met het uitvoeren van logische apps in een wereldwijde Azure-regio, wat betekent dat uw strategie voor noodherstel op beide scenario's van toepassing kan zijn. Ise's hebben echter andere overwegingen, zoals het configureren van toegang tot bronnen die alleen beschikbaar zijn voor ISE's.

> [!NOTE]
> Als uw logische app ook werkt met B2B-artefacten, zoals handelspartners, overeenkomsten, schema's, kaarten en certificaten, die zijn opgeslagen in een integratieaccount, moeten zowel uw integratieaccount als logische apps dezelfde locatie opgeven.

Deze strategie voor noodherstel richt zich op het instellen van uw primaire logica-app om te [*mislukken*](https://en.wikipedia.org/wiki/Failover) op een stand-by- of back-uplogica-app op een alternatieve locatie waar azure logic apps ook beschikbaar zijn. Op die manier, als de primaire lijdt verliezen, verstoringen, of mislukkingen, de secundaire kan nemen op het werk. Deze strategie vereist dat uw secundaire logica-app en afhankelijke resources al zijn geïmplementeerd en klaar zijn op de alternatieve locatie.

Als u goede DevOps-procedures volgt, gebruikt u azure [resourcebeheersjablonen](../azure-resource-manager/management/overview.md) al om uw logische apps en hun afhankelijke bronnen te definiëren en te implementeren. Resourcebeheersjablonen bieden u de mogelijkheid om één implementatiedefinitie te gebruiken en vervolgens parameterbestanden te gebruiken om de configuratiewaarden op te geven die voor elke implementatiebestemming moeten worden gebruikt. Deze mogelijkheid betekent dat u dezelfde logische app implementeren in verschillende omgevingen, bijvoorbeeld ontwikkeling, test en productie. U dezelfde logische app ook implementeren in verschillende Azure-regio's of ISE's, die strategieën voor noodherstel ondersteunen die [gebruikmaken van gekoppelde regio's.](../best-practices-availability-paired-regions.md)

Voor de failoverstrategie moeten uw logische apps en locaties aan deze vereisten voldoen:

* De secundaire logische app-instantie heeft toegang tot dezelfde apps, services en systemen als de primaire logische app-instantie.

* Beide logische app-exemplaren hebben hetzelfde hosttype. Beide instanties worden dus geïmplementeerd in regio's in het wereldwijde Azure met meerdere tenant' s, of beide exemplaren worden geïmplementeerd in ISE's, waarmee uw logische apps rechtstreeks toegang hebben tot bronnen in een virtueel Azure-netwerk. Zie [Business continuity and disaster recovery (BCDR)](../best-practices-availability-paired-regions.md)voor aanbevolen procedures en meer informatie over gekoppelde regio's voor BCDR.

  Zowel de primaire als de secundaire locaties moeten bijvoorbeeld ISE's zijn wanneer de primaire logica-app wordt uitgevoerd in een ISE en [ISE-versieconnectors, HTTP-acties](../connectors/apis-list.md#ise-connectors)gebruiken om resources in het virtuele Azure-netwerk of beide aan te roepen. In dit scenario moet uw secundaire logica-app ook een vergelijkbare instelling hebben op de secundaire locatie als de primaire logica-app.

  > [!NOTE]
  > Voor meer geavanceerde scenario's u zowel Azure met meerdere tenants als een ISE als locaties combineren. Zorg er echter voor dat u rekening houdt met en begrijpt wat de [verschillen zijn tussen de manier waarop logische apps worden uitgevoerd in een ISE versus Azure met meerdere tenants.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)

* Als u ISE's gebruikt, [moet u ervoor zorgen dat ze zijn uitgeschaald of voldoende capaciteit hebben](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) om de belasting te verwerken.

#### <a name="example-multi-tenant-azure"></a>Voorbeeld: Azure met meerdere tenant's

In dit voorbeeld worden primaire en secundaire logische app-exemplaren weergegeven, die worden geïmplementeerd in afzonderlijke regio's in het globale Azure met meerdere tenant voor dit scenario. Eén [resourcemanagersjabloon](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) definieert zowel logische app-exemplaren als de afhankelijke resources die nodig zijn voor die logische apps. Afzonderlijke parameterbestanden geven de te gebruiken configuratiewaarden op voor elke implementatielocatie:

![Primaire en secundaire logica app-exemplaren op afzonderlijke locaties](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>Voorbeeld: Integratieserviceomgeving

In dit voorbeeld worden de vorige primaire en secundaire logische app-exemplaren weergegeven, maar die zijn geïmplementeerd in afzonderlijke ISE's. Eén resourcebeheersjabloon definieert zowel logische app-exemplaren, de afhankelijke resources die nodig zijn voor die logische apps als de ISE's als de implementatielocaties. Afzonderlijke parameterbestanden definiëren de configuratiewaarden die moeten worden gebruikt voor implementatie op elke locatie:

![Primaire en secundaire logische apps op verschillende locaties](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>Verbindingen met resources

Azure Logic Apps biedt [ingebouwde triggers en acties plus honderden beheerde connectors](../connectors/apis-list.md) die uw logische app kan gebruiken om te werken met andere apps, services, systemen en andere bronnen, zoals Azure Storage-accounts, SQL Server-databases, Office 365 Outlook-e-mailaccounts, enzovoort. Als uw logische app toegang tot deze bronnen nodig heeft, maakt u verbindingen die de toegang tot deze bronnen verifiëren. Elke verbinding is een afzonderlijke Azure-bron die op een specifieke locatie bestaat en niet kan worden gebruikt door resources op andere locaties.

Houd voor uw strategie voor noodherstel rekening met de locaties waar afhankelijke resources bestaan ten opzichte van uw logica-app-instanties:

* Uw primaire instantie en afhankelijke resources bestaan op verschillende locaties. In dit geval kan uw secundaire instantie verbinding maken met dezelfde afhankelijke resources of eindpunten. U moet echter verbindingen maken die specifiek voor uw secundaire instantie zijn. Op die manier worden de verbindingen van uw secundaire verbinding niet beïnvloed als uw primaire locatie niet beschikbaar is.

  Stel dat uw primaire logica-app verbinding maakt met een externe service zoals Salesforce. Meestal zijn de beschikbaarheid en locatie van de externe service onafhankelijk van de beschikbaarheid van uw logische app. In dit geval kan uw secundaire instantie verbinding maken met dezelfde service, maar moet het een eigen verbinding hebben.

* Zowel uw primaire instantie als afhankelijke resources bestaan op dezelfde locatie. In dit geval moeten afhankelijke resources back-ups of gerepliceerde versies op een andere locatie hebben, zodat uw secundaire instantie nog steeds toegang heeft tot deze bronnen.

  Stel dat uw primaire logica-app verbinding maakt met een service die zich op dezelfde locatie of regio bevindt, bijvoorbeeld Azure SQL Database. Als deze hele regio niet meer beschikbaar is, is de Azure SQL Database-service in dat gebied waarschijnlijk ook niet beschikbaar. In dit geval wilt u dat uw secundaire instantie een gerepliceerde of back-updatabase gebruikt, samen met een afzonderlijke verbinding met die database.

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>On-premises gegevensgateways

Als uw logische app wordt uitgevoerd in Azure met meerdere tenant's en toegang nodig heeft tot on-premises bronnen zoals SQL Server-databases, moet u de [on-premises gegevensgateway](../logic-apps/logic-apps-gateway-install.md) op een lokale computer installeren. U vervolgens een gegevensgatewaybron maken in de Azure-portal, zodat uw logische app de gateway kan gebruiken wanneer u een verbinding met de bron maakt.

De gegevensgatewaybron is gekoppeld aan een locatie of Azure-regio, net als uw logische app-bron. Zorg er in uw strategie voor noodherstel op dat de gegevensgateway beschikbaar blijft voor uw logische app. U [hoge beschikbaarheid voor uw gateway inschakelen](../logic-apps/logic-apps-gateway-install.md#high-availability) wanneer u meerdere gateway-installaties hebt.

> [!NOTE]
> Als uw logische app wordt uitgevoerd in een integratieserviceomgeving (ISE) en alleen ISE-versieconnectors gebruikt voor on-premises gegevensbronnen, hebt u de gegevensgateway niet nodig omdat ISE-connectors directe toegang bieden tot de on-premises bron.
>
> Als er geen ISE-versie connector beschikbaar is voor de on-premises resource die u wilt, kan uw logische app de verbinding nog steeds maken met behulp van een niet-ISE-connector, die wordt uitgevoerd in de wereldwijde multi-tenant Azure, niet in uw ISE. Deze verbinding vereist echter de on-premises datagateway.

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>Actief-actieve versus actief-passieve rollen

U uw primaire en secundaire locaties zo instellen dat uw logische app-exemplaren op deze locaties deze rollen kunnen spelen:

| Primaire-secundaire rol | Beschrijving |
|------------------------|-------------|
| *Actief actief* | De primaire en secundaire logica-app-exemplaren op beide locaties verwerken actief aanvragen door een van deze patronen te volgen: <p><p>- *Load balans*: U beide instanties naar een eindpunt en laadbalansverkeer naar elke instantie laten luisteren als dat nodig is. <p>- *Concurrerende consumenten*: U beide instanties laten optreden als concurrerende consumenten, zodat de instanties concurreren om berichten uit een wachtrij. Als een instantie mislukt, neemt de andere instantie de werkbelasting over. |
| *Actief-passief* | De primaire logische app-instantie verwerkt actief de volledige werkbelasting, terwijl de secundaire instantie passief is (uitgeschakeld of inactief). De secundaire wacht op een signaal dat de primaire niet beschikbaar is of niet werkt als gevolg van verstoring of storing en neemt de werklast over als de actieve instantie. |
| Combinatie | Sommige logische apps spelen een actief actieve rol, terwijl andere logische apps een actief-passieve rol spelen. |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>Actief-actieve voorbeelden

In deze voorbeelden wordt de active-active-instelling weergegeven waarin beide logische app-instanties actief aanvragen of berichten verwerken. Een ander systeem of een andere service verdeelt de aanvragen of berichten tussen instanties, bijvoorbeeld een van de volgende opties:

* Een "fysieke" load balancer, zoals een stuk hardware dat verkeer

* Een "soft" load balancer zoals [Azure Load Balancer](../load-balancer/load-balancer-overview.md) of [Azure API Management](../api-management/api-management-key-concepts.md). Met API Management u beleid opgeven dat bepaalt hoe u binnenkomend verkeer in balans laden. U ook een service gebruiken die statustracking ondersteunt, bijvoorbeeld [Azure Service Bus.](../service-bus-messaging/service-bus-messaging-overview.md)

  Hoewel in dit voorbeeld azure load balancer wordt weergegeven, u de optie gebruiken die het beste past bij de behoeften van uw scenario:

  !['Actief-actieve' installatie die gebruik maakt van een load balancer of stateful service](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* Elke logische app-instantie fungeert als consument en laat beide instanties concurreren om berichten uit een wachtrij:

  !["Actief-actieve" setup die gebruik maakt van "concurrerende consumenten"](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>Actief-passieve voorbeelden

In dit voorbeeld wordt de actief-passieve instelling weergegeven waarbij de primaire logische app-instantie actief is op één locatie, terwijl de secundaire instantie op een andere locatie inactief blijft. Als de primaire een storing of storing ondervindt, u een operator een script laten uitvoeren dat het secundaire activeert om de werkbelasting op zich te nemen.

!["Actief-passieve" setup die gebruik maakt van "concurrerende consumenten"](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>Combinatie met actief-actief en actief-passief

In dit voorbeeld wordt een gecombineerde instelling weergegeven waarbij de primaire locatie beide actieve logische app-exemplaren heeft, terwijl de secundaire locatie actief-passieve logica-app-exemplaren heeft. Als de primaire locatie een storing of storing ondervindt, kan de actieve logische app op de secundaire locatie, die al een gedeeltelijke werkbelasting verwerkt, de volledige werkbelasting overnemen.

* Op de primaire locatie luistert een actieve logica-app naar een Azure Service Bus-wachtrij voor berichten, terwijl een andere actieve logische app op e-mails controleert met behulp van een Office 365 Outlook-stemtrigger.

* Op de secundaire locatie werkt een actieve logische app met de logische app op de primaire locatie door te luisteren en te concurreren voor berichten uit dezelfde wachtrij voor servicebus. Ondertussen wacht een passieve inactieve logica-app op stand-by om te controleren op e-mails wanneer de primaire locatie niet beschikbaar is, maar is *uitgeschakeld* om te voorkomen dat e-mails opnieuw worden gelezen.

!["Actief-passieve" en "actief-passieve" combinatie die recidieftriggers gebruikt](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>Logica-app-status en -geschiedenis

Wanneer uw logische app wordt geactiveerd en wordt uitgevoerd, wordt de status van de app opgeslagen op dezelfde locatie waar de app is gestart en niet kan worden overdraagbaar naar een andere locatie. Als er een fout of onderbreking optreedt, worden alle binnenkomende werkstroomexemplaren verlaten. Wanneer u een primaire en secundaire locatie hebt ingesteld, worden nieuwe werkstroominstanties op de secundaire locatie uitgevoerd.

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>Verlaten lopende exemplaren verminderen

Als u het aantal verlaten werkstroominstanties in uitvoering wilt minimaliseren, u kiezen uit verschillende berichtpatronen die u implementeren, bijvoorbeeld:

* [Vast routeringsformulierpatroon](https://docs.microsoft.com/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  Dit bedrijfsberichtpatroon dat een bedrijfsproces in kleinere fasen splitst. Voor elke fase stelt u een logische app in die de werkbelasting voor die fase afhandelt. Om met elkaar te communiceren, gebruiken uw logische apps een asynchrone berichtenprotocol, zoals Azure Service Bus-wachtrijen of onderwerpen. Wanneer u een proces in kleinere fasen verdeelt, vermindert u het aantal bedrijfsprocessen dat vast kan komen te zitten in een mislukte logische app-instantie. Zie [Integratiepatronen voor ondernemingen - Routeringsformulier voor](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html)meer algemene informatie over dit patroon.

  In dit voorbeeld wordt een routeringsformulier weergegeven waarin elke logische app een fase vertegenwoordigt en een wachtrij voor servicebus gebruikt om te communiceren met de volgende logische app in het proces.

  ![Een bedrijfsproces splitsen in fasen die worden weergegeven door logische apps, die met elkaar communiceren met Azure Service Bus-wachtrijen](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  Als zowel primaire als secundaire logica-app-exemplaren hetzelfde routeslippatroon volgen op hun locaties, u het [patroon van concurrerende consumenten](https://docs.microsoft.com/azure/architecture/patterns/competing-consumers) implementeren door actief actieve [rollen](#roles) voor die instanties in te stellen.

* [Patroon procesmanager (broker)](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [Peek-lock zonder time-outpatroon](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>Toegang tot trigger en voert geschiedenis uit

Als u meer informatie wilt krijgen over de eerdere werkstroomuitvoeringen van uw logische app, u de trigger van de app bekijken en de geschiedenis uitvoeren. De geschiedenisuitvoeringsgeschiedenis van een logische app wordt opgeslagen op dezelfde locatie of regio waar die logische app is uitgevoerd, wat betekent dat u deze geschiedenis niet naar een andere locatie migreren. Als uw primaire instantie niet overgaat naar een secundaire instantie, hebt u alleen toegang tot de trigger van elk exemplaar en voert u de geschiedenis uit op de respectievelijke locaties waar deze instanties zijn uitgevoerd. U echter locatie-agnostische informatie over de geschiedenis van uw logische app ophalen door uw logische apps in te stellen om diagnostische gebeurtenissen naar een Azure Log Analytics-werkruimte te verzenden. Vervolgens u de status en geschiedenis van logische apps bekijken die op meerdere locaties worden uitgevoerd.

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>Richtlijnen voor het type trigger

Het triggertype dat u in uw logische apps gebruikt, bepaalt uw opties voor hoe u logische apps op verschillende locaties in uw strategie voor noodherstel instellen. Dit zijn de beschikbare triggertypen die u gebruiken in logische apps:

* [Recidieftrigger](#recurrence-trigger)
* [Polling trigger](#polling-trigger)
* [Trigger van aanvragen](#request-trigger)
* [Webhook-trigger](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Recidieftrigger

De **recidieftrigger** is onafhankelijk van een specifieke service of eindpunt en vuurt uitsluitend op basis van een opgegeven planning en geen andere criteria, bijvoorbeeld:

* Een vaste frequentie en interval, zoals elke 10 minuten
* Een meer geavanceerd schema, zoals de laatste maandag van elke maand om 17:00

Wanneer uw logische app begint met een recidieftrigger, moet u uw primaire en secundaire logische app-exemplaren instellen met de [actief-passieve rollen.](#roles) Als u de *doelstelling voor hersteltijd* (RTO) wilt verminderen, die verwijst naar de doelduur voor het herstellen van een bedrijfsproces na een onderbreking of ramp, u uw logische app-exemplaren instellen met een combinatie van [actief-passieve rollen](#roles) en [passiefactieve rollen.](#roles) In deze instelling splitst u het schema over locaties.

Stel dat u een logische app hebt die elke 10 minuten moet worden uitgevoerd. U uw logische apps en locaties zo instellen dat als de primaire locatie niet beschikbaar is, de secundaire locatie het werk kan overnemen:

!["Actief-passieve" en "passief-actieve" combinatie die recidieftriggers gebruikt](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* Stel op de primaire locatie [actieve passieve rollen](#roles) in voor deze logische apps:

  * Stel voor de *actieve* ingeschakelde logica-app de recidieftrigger in om bovenaan het uur te starten en herhaal elke 20 minuten, bijvoorbeeld om 9:00 uur, 9:20 uur, enzovoort.

  * Stel voor de *passieve* uitgeschakelde logica-app de recidieftrigger in op hetzelfde schema, maar begin bij 10 minuten na het uur en herhaal elke 20 minuten, bijvoorbeeld 9:10 uur, 9:30 uur, enzovoort.

* Stel op de secundaire locatie [passief actief](#roles) in voor deze logische apps:

  * Stel voor de *passieve* uitgeschakelde logica-app de recidieftrigger in op hetzelfde schema als de actieve logica-app op de primaire locatie, die zich boven aan het uur bevindt en herhaal elke 20 minuten, bijvoorbeeld 9:00 uur, 9:10 uur, enzovoort.

  * Stel voor de *actieve* logische-app de recidieftrigger in op hetzelfde schema als de passieve logica-app op de primaire locatie, die om 10 minuten na het uur moet beginnen en elke 20 minuten moet herhalen, bijvoorbeeld om 9:10 uur, 9:20 uur, enzovoort.

Als er nu een storende gebeurtenis plaatsvindt op de primaire locatie, activeert u de passieve logica-app op de alternatieve locatie. Op die manier, als het vinden van de fout tijd kost, beperkt deze instelling het aantal gemiste herhalingen tijdens die vertraging.

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>Polling trigger

Als u regelmatig wilt controleren of nieuwe gegevens voor verwerking beschikbaar zijn vanuit een specifieke service of eindpunt, kan uw logische app een *pollingtrigger* gebruiken die herhaaldelijk de service of het eindpunt aanroept op basis van een vast herhalingsschema. De gegevens die de service of het eindpunt biedt, kunnen een van de volgende typen bevatten:

* Statische gegevens, die gegevens beschrijven die altijd beschikbaar zijn voor het lezen
* Vluchtige gegevens, die gegevens beschrijven die niet meer beschikbaar zijn na het lezen

Om te voorkomen dat herhaaldelijk dezelfde gegevens worden gelezen, moet uw logische app onthouden welke gegevens eerder zijn gelezen door de status aan de clientzijde of aan de server-, service- of systeemzijde te behouden.

* Logische apps die werken met client-side state gebruiken triggers die status kunnen behouden.

  Een trigger die bijvoorbeeld een nieuw bericht uit een postvak in e-mail leest, vereist dat de trigger het meest recent gelezen bericht onthoudt. Op die manier start de trigger de logische app alleen wanneer het volgende ongelezen bericht binnenkomt.

* Logische apps die werken met server-, service- of systeemstatus gebruiken eigenschapswaarden of -instellingen die zich aan de server-, service- of systeemzijde bevinden.

  Een op query's gebaseerde trigger die een rij uit `isRead` een database leest, `FALSE`vereist bijvoorbeeld dat de rij een kolom heeft die is ingesteld op . Elke keer dat de trigger een rij leest, wordt `isRead` die `FALSE` `TRUE`rij bijgewerkt door de kolom te wijzigen van .

  Deze benadering aan de serverzijde werkt op dezelfde manier voor wachtrijen voor ServiceBus of onderwerpen met wachtrijen, waarbij een trigger een bericht kan lezen en vergrendelen terwijl de logische app het bericht verwerkt. Wanneer de logische app klaar is met de verwerking, verwijdert de trigger het bericht uit de wachtrij of het onderwerp.

Controleer bij het instellen van de primaire en secundaire instanties van uw logische app of u dit gedrag volgen op basis van de vraag of uw logische app de status aan de clientzijde of aan de serverzijde bijhoudt:

* Controleer ervoor dat uw logische app niet meer dan één keer hetzelfde bericht leest voor een logische app die werkt met de status client-side. Slechts één locatie kan op een bepaald moment een actieve logische app-instantie hebben. Controleer of de instantie logische app op de alternatieve locatie inactief of uitgeschakeld is totdat de primaire instantie niet naar de alternatieve locatie gaat.

  De Office 365 Outlook-trigger behoudt bijvoorbeeld de status aan clientzijde en houdt de tijdstempel bij voor de laatst gelezen e-mail om te voorkomen dat een duplicaat wordt gelezen.

* Voor een logische app die werkt met server-side status, u uw logische app-exemplaren zo instellen dat [actieve rollen](#roles) worden afgespeeld wanneer ze werken als concurrerende consumenten of [actief-passieve rollen](#roles) waarbij de alternatieve instantie wacht totdat de primaire instantie niet naar de alternatieve locatie gaat.

  Als u bijvoorbeeld vanuit een berichtenwachtrij leest, zoals een Azure Service Bus-wachtrij, wordt de server-side status gebruikt omdat de wachtrijservice vergrendelingen op berichten onderhoudt om te voorkomen dat andere clients dezelfde berichten lezen.

  > [!NOTE]
  > Als uw logica-app berichten in een specifieke volgorde moet lezen, bijvoorbeeld vanuit een wachtrij voor servicebus, u het concurrerende consumentenpatroon gebruiken, maar alleen in combinatie met Service Bus-sessies, die ook wel het [ *sequentiële konvooipatroon* ](https://docs.microsoft.com/azure/architecture/patterns/sequential-convoy)worden genoemd. Anders moet u uw logische app-exemplaren instellen met de actief-passieve rollen.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Trigger van aanvragen

De **trigger voor aanvragen** maakt uw logische app aanroepbaar vanuit andere apps, services en systemen en wordt meestal gebruikt om deze mogelijkheden te bieden:

* Een directe REST API voor uw logische app die anderen kunnen aanroepen

  Gebruik bijvoorbeeld de trigger Van Verzoek om uw logische app te starten, zodat andere logische apps de trigger kunnen activeren met behulp van de **callworkflow - Logic Apps** actie.

* Een [webhook-](#webhook-trigger) of callbackmechanisme voor uw logische app

* Een manier waarop u handmatig gebruikersbewerkingen of routines uitvoeren om uw logische app te bellen, bijvoorbeeld door een PowerShell-script te gebruiken dat een specifieke taak uitvoert

Vanuit het oogpunt van noodherstel is de trigger van Aanvraag een passieve ontvanger omdat de logische app geen werk doet en wacht totdat een andere service of systeem expliciet de trigger aanroept. Als passief eindpunt u uw primaire en secundaire instanties op de volgende manieren instellen:

* [Actief actief](#roles): Beide instanties verwerken actief aanvragen of oproepen. De beller of router balanceert of verdeelt het verkeer over die instanties.

* [Actief-passief](#roles): Alleen de primaire instantie is actief en verwerkt al het werk, terwijl de secundaire instantie wacht tot de primaire problemen worden verstoord of mislukt. De beller of router bepaalt wanneer de secundaire instantie moet worden aangeroepen.

Als aanbevolen architectuur u Azure API Management gebruiken als proxy voor de logische apps die Request-triggers gebruiken. API-beheer biedt [ingebouwde cross-regionale tolerantie en de mogelijkheid om verkeer over meerdere eindpunten te routeren.](https://docs.microsoft.com/azure/api-management/api-management-howto-deploy-multi-region)

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>Webhook-trigger

Een *webhook-trigger* biedt de mogelijkheid voor uw logische app om zich te abonneren op een service door een *callback-URL* door te geven aan die service. Uw logica-app kan vervolgens luisteren en wachten tot er een specifieke gebeurtenis plaatsvindt op dat serviceeindpunt. Wanneer de gebeurtenis plaatsvindt, roept de service de webhooktrigger aan met behulp van de terugbel-URL, die vervolgens de logische app uitvoert. Wanneer ingeschakeld, de webhook trigger abonneert zich op de dienst. Wanneer uitgeschakeld, de trigger afgemeld van de service.

Stel vanuit het perspectief van disaster recovery primaire en secundaire instanties in die webhook-triggers gebruiken om actief-passieve rollen te spelen, omdat slechts één exemplaar gebeurtenissen of berichten van het geabonneerde eindpunt moet ontvangen.

## <a name="assess-primary-instance-health"></a>De status van primaire instantie beoordelen

Om uw disaster recovery-strategie te laten werken, heeft uw oplossing manieren nodig om deze taken uit te voeren:

* [De beschikbaarheid van de primaire instantie controleren](#check-primary-availability)
* [De status van de primaire instantie controleren](#monitor-primary-health)
* [De secundaire instantie activeren](#activate-secondary)

In dit gedeelte wordt een oplossing beschreven die u ronduit of als basis voor uw eigen ontwerp gebruiken. Hier is een visueel overzicht op hoog niveau voor deze oplossing:

![Watchdog Logic-app maken die een logica-app voor statuscontrole op de primaire locatie bewaakt](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>Beschikbaarheid van primaire instantie controleren

Als u wilt bepalen of de primaire instantie beschikbaar is, wordt uitgevoerd en kan werken, u een logische app voor statuscontrole maken die zich op dezelfde locatie als de primaire instantie bevindt. U deze health-check-app vervolgens vanaf een alternatieve locatie bellen. Als de app health-check succesvol reageert, is de onderliggende infrastructuur voor de Azure Logic Apps-service in die regio beschikbaar en werkt deze. Als de app voor statuscontrole niet reageert, u ervan uitgaan dat de locatie niet meer in orde is.

Maak voor deze taak een logische basisapp voor statuscontrole waarmee deze taken worden uitgevoerd:

1. Ontvangt een oproep van de watchdog-app met behulp van de trigger Verzoek.

1. Reageer met een status die aangeeft of de aangevinkte logica-app nog steeds werkt met behulp van de actie Reactie.

   > [!IMPORTANT]
   > De logica-app voor statuscontrole moet een reactieactie gebruiken, zodat de app synchroon reageert, niet asynchroon.

1. Optioneel u de status van andere services die op deze locatie communiceren met de doellogica-app om verder te bepalen of de primaire locatie in orde is, rekening houden met de status van andere services die met de doellogica-app communiceren. Breid de app voor de logica van de status controle uit om ook de status voor deze andere services te beoordelen.

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>Een app voor watchdog-logica maken

Als u de status van de primaire instantie wilt controleren en de logica-app voor statuscontrole wilt aanroepen, maakt u een 'waakhond'-logische-app op een *alternatieve locatie.* U bijvoorbeeld de logica-app van de waakhond zo instellen dat als het aanroepen van de logica voor statuscontrole mislukt, de waakhond een waarschuwing naar uw operationele team kan sturen, zodat ze de fout kunnen onderzoeken en waarom de primaire instantie niet reageert.

> [!IMPORTANT]
> Zorg ervoor dat uw watchdog logic-app zich op een *locatie bevindt die verschilt van de primaire locatie.* Als de Logic Apps-service op de primaire locatie problemen ondervindt, wordt de logica-app van uw waakhond mogelijk niet uitgevoerd.

Maak voor deze taak op de secundaire locatie een logische app voor waakhonden die deze taken uitvoert:

1. Uitvoeren op basis van een vaste of geplande herhaling met behulp van de recidieftrigger.

   U de herhaling instellen op een waarde die lager is dan het tolerantieniveau voor uw hersteltijddoelstelling (RTO).

1. Bel de logica-app voor statuscontrole op de primaire locatie met behulp van de HTTP-actie, bijvoorbeeld:

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>Uw secundaire instantie activeren

Als u de secundaire instantie automatisch wilt activeren, u een logische app maken die de beheer-API aanroept, zoals de [Azure Resource Manager-connector](https://docs.microsoft.com/connectors/arm/) om de juiste logische apps op de secundaire locatie te activeren. U uw watchdog-app uitbreiden om deze activeringslogica-app aan te roepen nadat er een bepaald aantal fouten is opgemaakt.

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>Diagnostische gegevens verzamelen

U logboekregistratie instellen voor het uitvoeren van uw logische app en de resulterende diagnostische gegevens verzenden naar services zoals Azure Storage, Azure Event Hubs en Azure Log Analytics voor verdere verwerking en verwerking.

* Als u deze gegevens wilt gebruiken met Azure Log Analytics, u de gegevens beschikbaar maken voor zowel de primaire als de secundaire locaties door de **diagnostische instellingen** van uw logische app in te stellen en de gegevens naar meerdere Log Analytics-werkruimten te verzenden. Zie [Azure Monitor-logboeken instellen en diagnostische gegevens verzamelen voor Azure Logic Apps voor](../logic-apps/monitor-logic-apps-log-analytics.md)meer informatie.

* Als u de gegevens naar Azure Storage of Azure Event Hubs wilt verzenden, u de gegevens beschikbaar maken voor zowel de primaire als de secundaire locaties door georedundantie in te stellen. Raadpleeg voor meer informatie de volgende artikelen:<p>

  * [Azure Blob Storage disaster recovery en account failover](../storage/common/storage-disaster-recovery-guidance.md)
  * [Herstel van geo-ramp Azure Event Hubs](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van tolerantie voor Azure](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)
* [Controlelijst voor tolerantie voor specifieke Azure-services](https://docs.microsoft.com/azure/architecture/checklist/resiliency-per-service)
* [Gegevensbeheer voor tolerantie in Azure](https://docs.microsoft.com/azure/architecture/framework/resiliency/data-management)
* [Back-up en noodherstel voor Azure-toepassingen](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery)
* [Herstel na een serviceonderbreking in de gehele regio](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
* [Microsoft Service Level Agreements (SLA's) voor Azure-services](https://azure.microsoft.com/support/legal/sla/)
