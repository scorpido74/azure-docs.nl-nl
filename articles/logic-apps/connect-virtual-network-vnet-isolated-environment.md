---
title: Verbinding maken met virtuele netwerken van Azure met een ISE
description: Maak een ISE (Integration service Environment) die toegang heeft tot Azure Virtual Networks (VNETs) vanaf Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 63174e1d4950b9f18fd3693511c507ed2dd018b3
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77500374"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Verbinding maken met virtuele Azure-netwerken van Azure Logic Apps met behulp van een ISE (Integration service Environment)

Voor scenario's waarin uw Logic apps en integratie accounts toegang nodig hebben tot een [virtueel Azure-netwerk](../virtual-network/virtual-networks-overview.md), maakt u een [ISE ( *Integration service Environment* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Een ISE is een geïsoleerde omgeving waarin speciale opslag en andere bronnen worden gebruikt die gescheiden worden gehouden van de open bare, ' wereld wijde ' multi tenant-Logic Apps service. Deze schei ding vermindert ook de invloed die andere Azure-tenants mogelijk hebben op de prestaties van uw apps. Een ISE biedt u ook uw eigen vaste IP-adressen. Deze IP-adressen zijn gescheiden van de statische IP-adressen die worden gedeeld door de Logic apps in de open bare multi tenant-service.

Wanneer u een ISE maakt, *injecteert* Azure die ISE in uw virtuele Azure-netwerk, waarna de Logic apps-service in uw virtuele netwerk wordt geïmplementeerd. Wanneer u een logische app of een integratie account maakt, selecteert u uw ISE als locatie. Uw logische app of integratie account kan vervolgens rechtstreeks toegang krijgen tot resources, zoals virtuele machines (Vm's), servers, systemen en services, in uw virtuele netwerk.

![Integratie service omgeving selecteren](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Voor logische apps en integratie accounts die in een ISE samen werken, moeten beide *dezelfde ISE* gebruiken als hun locatie.

Een ISE heeft de limieten voor de duur van de uitvoering, de opslag ruimte, de door Voer, de HTTP-aanvraag en de time-out van het antwoord, de bericht grootte en de aangepaste connector aanvragen verhoogd. Zie [limieten en configuratie voor Azure Logic apps](logic-apps-limits-and-config.md)voor meer informatie. Zie [toegang tot Azure Virtual Network-resources vanuit Azure Logic apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)voor meer informatie over ISEs.

In dit artikel wordt beschreven hoe u deze taken uitvoert:

* Toegang inschakelen voor uw ISE.
* Maak uw ISE.
* Voeg extra capaciteit toe aan uw ISE.

> [!IMPORTANT]
> Logic apps, ingebouwde triggers, ingebouwde acties en connectors die worden uitgevoerd in uw ISE, gebruiken een prijs plan dat verschilt van het prijs plan op basis van verbruik. Zie het [Logic apps-prijs model](../logic-apps/logic-apps-pricing.md#fixed-pricing)voor meer informatie over de prijzen en facturerings werkzaamheden voor ISEs. Zie [Logic apps prijzen](../logic-apps/logic-apps-pricing.md)voor prijs tarieven.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een [virtueel Azure-netwerk](../virtual-network/virtual-networks-overview.md). Als u geen virtueel netwerk hebt, leert u hoe u [een virtueel Azure-netwerk maakt](../virtual-network/quick-create-portal.md).

  * Het virtuele netwerk moet vier *lege* subnetten hebben voor het maken en implementeren van resources in uw ISE. Elk subnet ondersteunt een ander Logic Apps onderdeel dat wordt gebruikt in uw ISE. U kunt deze subnetten vooraf maken, maar u kunt wachten totdat u de ISE maakt waar u subnetten tegelijk kunt maken. Meer informatie over de vereisten voor het [subnet](#create-subnet).

  * De namen van subnetten moeten beginnen met een alfabetisch teken of een liggend streepje en kunnen niet de volgende tekens gebruiken: `<`, `>`, `%`, `&`, `\\`, `?`, `/`. 
  
  * Als u de ISE via een Azure Resource Manager sjabloon wilt implementeren, moet u eerst een leeg subnet delegeren naar micro soft. Logic/integrationServiceEnvironment. U hoeft deze delegatie niet uit te voeren wanneer u via de Azure Portal implementeert.

  * Zorg ervoor dat het virtuele netwerk [toegang biedt tot uw ISE](#enable-access) zodat uw ISE goed kan werken en toegankelijk moet blijven.

  * Als u [ExpressRoute](../expressroute/expressroute-introduction.md)gebruikt, een persoonlijke verbinding met micro soft-Cloud Services die wordt vereenvoudigd door de connectiviteits provider, moet u [een route tabel](../virtual-network/manage-route-table.md) met de volgende route maken en die tabel koppelen aan elk subnet dat wordt gebruikt door uw ISE:

    **Naam**: <*route naam*><br>
    **Adres voorvoegsel**: 0.0.0.0/0<br>
    **Volgende hop**: Internet

* Als u aangepaste DNS-servers wilt gebruiken voor uw virtuele Azure-netwerk, [stelt u die servers in door de volgende stappen uit te voeren](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) voordat u uw ISE implementeert in uw virtuele netwerk. Anders moet u, telkens wanneer u de DNS-server wijzigt, uw ISE opnieuw opstarten.

  > [!IMPORTANT]
  > Als u de instellingen van de DNS-server wijzigt nadat u een ISE hebt gemaakt, moet u ervoor zorgen dat u de ISE opnieuw start. Zie [een virtueel netwerk maken, wijzigen of verwijderen](../virtual-network/manage-virtual-network.md#change-dns-servers)voor meer informatie over het beheren van DNS-server instellingen.

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>Toegang inschakelen voor ISE

Wanneer u een ISE gebruikt met een virtueel Azure-netwerk, heeft een veelvoorkomend installatie probleem een of meer geblokkeerde poorten. De connectors die u gebruikt voor het maken van verbindingen tussen uw ISE-en doel systemen, hebben mogelijk ook hun eigen poort vereisten. Als u bijvoorbeeld communiceert met een FTP-systeem met behulp van de FTP-connector, moet de poort die u op uw FTP-systeem gebruikt, beschikbaar zijn, bijvoorbeeld poort 21 voor het verzenden van opdrachten.

Om ervoor te zorgen dat uw ISE toegankelijk is en dat de Logic apps in die ISE kunnen communiceren via elk subnet in het virtuele netwerk, [opent u de poorten die in deze tabel worden beschreven voor elk subnet](#network-ports-for-ise). Als de vereiste poorten niet beschikbaar zijn, zal uw ISE niet goed werken.

* Als u meerdere ISE-exemplaren hebt die toegang nodig hebben tot andere eind punten met IP-beperkingen, implementeert u een [Azure firewall](../firewall/overview.md) of een [virtueel netwerk apparaat](../virtual-network/virtual-networks-overview.md#filter-network-traffic) in uw virtuele netwerk en stuurt u uitgaand verkeer via die firewall of het virtuele netwerk apparaat. U kunt vervolgens [een enkel, uitgaand, openbaar, statisch en voorspelbaar IP-adres instellen](connect-virtual-network-vnet-set-up-single-ip-address.md) dat alle ISE-instanties in uw virtuele netwerk kunnen gebruiken om te communiceren met doel systemen. Op die manier hoeft u geen aanvullende firewall-openingen op deze doel systemen in te stellen voor elke ISE.

   > [!NOTE]
   > U kunt deze methode gebruiken voor één ISE wanneer u voor uw scenario het aantal IP-adressen wilt beperken dat toegang nodig heeft. Bepaal of de extra kosten voor de firewall of het virtuele netwerk apparaat logisch kunnen zijn voor uw scenario. Meer informatie over [Azure firewall prijzen](https://azure.microsoft.com/pricing/details/azure-firewall/).

* Als u een nieuw virtueel Azure-netwerk en subnetten zonder beperkingen hebt gemaakt, hoeft u geen [netwerk beveiligings groepen (nsg's)](../virtual-network/security-overview.md#network-security-groups) in te stellen in uw virtuele netwerk om verkeer tussen subnetten te beheren.

* In een bestaand virtueel netwerk kunt u *optioneel* nsg's instellen door [netwerk verkeer te filteren op subnetten](../virtual-network/tutorial-filter-network-traffic.md). Als u deze route wilt gebruiken of als u al Nsg's gebruikt, moet u ervoor zorgen dat u [de poorten in deze tabel opent](#network-ports-for-ise) op het virtuele netwerk waar u nsg's hebt of nsg's wilt instellen.

  > [!NOTE]
  > Als u [NSG-beveiligings regels](../virtual-network/security-overview.md#security-rules)gebruikt, moet u *zowel* de TCP-als de UDP-protocollen gebruiken. NSG-beveiligings regels beschrijven de poorten die u moet openen voor de IP-adressen die toegang moeten hebben tot deze poorten. Zorg ervoor dat alle firewalls, routers of andere items die tussen deze eind punten bestaan, deze poorten ook toegankelijk maken voor die IP-adressen.

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Netwerk poorten die worden gebruikt door uw ISE

In deze tabel worden de poorten in uw virtuele Azure-netwerk beschreven die uw ISE gebruikt en waar deze poorten worden gebruikt. De [Resource Manager-service Tags](../virtual-network/security-overview.md#service-tags) vertegenwoordigen een groep IP-adres voorvoegsels die de complexiteit helpen beperken bij het maken van beveiligings regels.

> [!IMPORTANT]
> Bron poorten zijn kortstondig, dus zorg ervoor dat u deze instelt op `*` voor alle regels. Zoals aangegeven, verwijst interne ISE en externe ISE naar het [eind punt dat is geselecteerd bij het maken van ISE](connect-virtual-network-vnet-isolated-environment.md#create-environment). Zie [endpoint Access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)voor meer informatie. 

| Doel | Richting | Doelpoorten | Bron servicetag | Doelservicetag | Opmerkingen |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| Communicatie tussen subnet binnen het virtuele netwerk | Binnenkomende &-uitgaand | * | De adres ruimte voor het virtuele netwerk met de subnetten van uw ISE | De adres ruimte voor het virtuele netwerk met de subnetten van uw ISE | Vereist voor verkeer voor stroom *tussen* de subnetten in het virtuele netwerk. <p><p>**Belang rijk**: Zorg ervoor dat u alle poorten in elk subnet opent voor verkeer tussen de *onderdelen* in elk subnet. |
| Communicatie met uw logische app | Inkomend | 443 | Interne ISE: <br>VirtualNetwork <p><p>Externe ISE: <br>Internet | VirtualNetwork | Het bron-IP-adres voor de computer of service dat aanvraag triggers of webhooks aanroept in uw logische app. <p><p>**Belang rijk**: het sluiten of blok keren van deze poort voor komt http-aanroepen naar Logic apps die aanvraag triggers hebben. |
| Uitvoerings geschiedenis van de logische app | Inkomend | 443 | Interne ISE: <br>VirtualNetwork <p><p>Externe ISE: <br>Internet | VirtualNetwork | Het bron-IP-adres van de computer of service waarvan u de uitvoerings geschiedenis van de logische app wilt weer geven. <p><p>**Belang rijk**: Hoewel het sluiten of blok keren van deze poort verhindert dat u de uitvoerings geschiedenis bekijkt, kunt u de invoer en uitvoer voor elke stap in de uitvoerings geschiedenis niet weer geven. |
| Logic Apps Designer: dynamische eigenschappen | Inkomend | 454 | Zie de kolom **opmerkingen** voor het toestaan van IP-adressen | VirtualNetwork | Aanvragen zijn afkomstig van de Logic Apps [binnenkomende](../logic-apps/logic-apps-limits-and-config.md#inbound) IP-adressen van het toegangs punt voor die regio. |
| Connector implementatie | Inkomend | 454 | AzureConnectors | VirtualNetwork | Vereist voor het implementeren en bijwerken van connectors. Als u deze poort sluit of blokkeert, mislukken ISE-implementaties en wordt het bijwerken of oplossen van connectors voor komen. |
| Netwerk status controle | Inkomend | 454 | Zie de kolom **opmerkingen** voor het toestaan van IP-adressen | VirtualNetwork | Aanvragen zijn afkomstig van het Logic Apps toegangs punt voor zowel [binnenkomende](../logic-apps/logic-apps-limits-and-config.md#inbound) als [uitgaande](../logic-apps/logic-apps-limits-and-config.md#outbound) IP-adressen voor die regio. |
| Afhankelijkheid van App Service beheer | Inkomend | 454, 455 | AppServiceManagement | VirtualNetwork | |
| Communicatie van Azure Traffic Manager | Inkomend | Interne ISE: 454 <p><p>Externe ISE: 443 | AzureTrafficManager | VirtualNetwork | |
| API Management-beheer eindpunt | Inkomend | 3443 | APIManagement | VirtualNetwork | |
| Implementatie van connector beleid | Inkomend | 3443 | APIManagement | VirtualNetwork | Vereist voor het implementeren en bijwerken van connectors. Als u deze poort sluit of blokkeert, mislukken ISE-implementaties en wordt het bijwerken of oplossen van connectors voor komen. |
| Communicatie vanuit uw logische app | Uitgaand | 80, 443 | VirtualNetwork | Varieert op basis van bestemming | De eind punten voor de externe service waarmee de logische app moet communiceren. |
| Azure Active Directory | Uitgaand | 80, 443 | VirtualNetwork | AzureActiveDirectory | |
| Verbindings beheer | Uitgaand | 443 | VirtualNetwork  | AppService | |
| Diagnostische logboeken publiceren & metrische gegevens | Uitgaand | 443 | VirtualNetwork  | AzureMonitor | |
| Azure Storage afhankelijkheid | Uitgaand | 80, 443, 445 | VirtualNetwork | Opslag | |
| Azure SQL-afhankelijkheid | Uitgaand | 1433 | VirtualNetwork | SQL | |
| Azure Resource Health | Uitgaand | 1886 | VirtualNetwork | AzureMonitor | Vereist voor het publiceren van de status naar Resource Health |
| Afhankelijkheid van het logboek-naar-Event hub-beleid en-bewakings agent | Uitgaand | 5672 | VirtualNetwork | EventHub | |
| Toegang tot Azure cache voor redis instanties tussen Rolinstanties | Inkomend <br>Uitgaand | 6379-6383 | VirtualNetwork | VirtualNetwork | Bovendien moet u, om ISE te kunnen gebruiken met Azure cache voor redis, deze [uitgaande en binnenkomende poorten openen die worden beschreven in de Azure-cache voor redis-Veelgestelde vragen](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Uw ISE maken

1. Voer in de [Azure Portal](https://portal.azure.com)in het hoofd venster van Azure Search `integration service environments` in als uw filter en selecteer **integratie service omgevingen**.

   ![Zoek en selecteer integratie service omgevingen](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Selecteer **toevoegen**in het deel venster **integratie service omgevingen** .

   ![Zoek en selecteer integratie service omgevingen](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Geef deze gegevens voor uw omgeving op en selecteer vervolgens **bekijken + maken**, bijvoorbeeld:

   ![Details van de omgeving opgeven](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Abonnement** | Ja | <*Azure-subscription-name*> | Het Azure-abonnement dat u wilt gebruiken voor uw omgeving |
   | **Resourcegroep** | Ja | <*Azure-resource-group-name*> | Een nieuwe of bestaande Azure-resource groep waar u uw omgeving wilt maken |
   | **Naam van de integratie service omgeving** | Ja | <*omgeving naam*> | De naam van uw ISE, die alleen letters, cijfers, afbreek streepjes (`-`), onderstrepings tekens (`_`) en punten (`.`) kan bevatten. |
   | **Locatie** | Ja | <*Azure-Data Center-regio*> | De Azure Data Center-regio waar u uw omgeving kunt implementeren |
   | **SKU** | Ja | **Premium** of **ontwikkelaar (geen sla)** | De ISE-SKU die u wilt maken en gebruiken. Zie [ISE sku's](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)(Engelstalig) voor verschillen tussen deze sku's. <p><p>**Belang rijk**: deze optie is alleen beschikbaar bij het maken van ISE en kan later niet worden gewijzigd. |
   | **Extra capaciteit** | Ultieme <br>Ja <p><p>Developer <br>Niet van toepassing | Ultieme <br>0 tot 10 <p><p>Developer <br>Niet van toepassing | Het aantal extra verwerkings eenheden dat voor deze ISE-resource moet worden gebruikt. Zie [ISE-capaciteit toevoegen](#add-capacity)om capaciteit toe te voegen na het maken. |
   | **Toegangs eindpunt** | Ja | **Intern** of **extern** | Het type toegangs eindpunten dat moet worden gebruikt voor uw ISE. Deze eind punten bepalen of de aanvraag of webhook triggers op Logic apps in uw ISE kan ontvangen van buiten uw virtuele netwerk. <p><p>Uw selectie is ook van invloed op de manier waarop u invoer en uitvoer kunt weer geven en openen in de geschiedenis van de logische app-uitvoeringen. Zie [ISE endpoint Access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)(Engelstalig) voor meer informatie. <p><p>**Belang rijk**: deze optie is alleen beschikbaar bij het maken van ISE en kan later niet worden gewijzigd. |
   | **Virtueel netwerk** | Ja | <*Azure-naam van virtueel netwerk*> | Het virtuele Azure-netwerk waar u uw omgeving wilt injecteren zodat logische apps in die omgeving toegang hebben tot uw virtuele netwerk. Als u nog geen netwerk hebt, [maakt u eerst een virtueel netwerk van Azure](../virtual-network/quick-create-portal.md). <p><p>**Belang rijk**: u kunt deze injectie *alleen* uitvoeren wanneer u uw ISE maakt. |
   | **Subnets** | Ja | <*subnet-bron-lijst*> | Een ISE vereist vier *lege* subnetten voor het maken en implementeren van resources in uw omgeving. [Volg de stappen onder deze tabel](#create-subnet)om elk subnet te maken. |
   |||||

   <a name="create-subnet"></a>

   **Subnet maken**

   Voor het maken en implementeren van resources in uw omgeving, heeft uw ISE vier *lege* subnetten nodig die niet worden overgedragen aan een service. Elk subnet ondersteunt een ander Logic Apps onderdeel dat wordt gebruikt in uw ISE. U *kunt* deze subnet-adressen niet wijzigen nadat u uw omgeving hebt gemaakt. Elk subnet moet aan de volgende vereisten voldoen:

   * Heeft een naam die begint met een letter of een onderstrepings teken (geen getallen), en deze tekens worden niet gebruikt: `<`, `>`, `%`, `&`, `\\`, `?`, `/`.

   * Maakt gebruik van de [CIDR-notatie (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) en een klasse B-adres ruimte.

   * Gebruikt ten minste een `/27` in de adres ruimte, omdat voor elk subnet 32 adressen mini *maal*vereist zijn. Bijvoorbeeld:

     * `10.0.0.0/28` heeft slechts 16 adressen en is te klein omdat 2<sup>(32-28)</sup> 2<sup>4</sup> of 16 is.

     * `10.0.0.0/27` heeft 32 adressen omdat 2<sup>(32-27)</sup> 2<sup>5</sup> of 32 is.

     * `10.0.0.0/24` heeft 256 adressen omdat 2<sup>(32-24)</sup> 2<sup>8</sup> of 256 is. Meer adressen bieden echter geen extra voor delen.

     Zie voor meer informatie over het berekenen van adressen [IPv4 CIDR-blokken](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Als u [ExpressRoute](../expressroute/expressroute-introduction.md)gebruikt, moet u [een route tabel](../virtual-network/manage-route-table.md) met de volgende route maken en deze tabel koppelen aan elk subnet dat wordt gebruikt door uw ISE:

     **Naam**: <*route naam*><br>
     **Adres voorvoegsel**: 0.0.0.0/0<br>
     **Volgende hop**: Internet

   1. Selecteer in de lijst **subnets** de optie **subnet-configuratie beheren**.

      ![Subnet-configuratie beheren](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. Selecteer **subnet**in het deel venster **subnetten** .

      ![Vier lege subnetten toevoegen](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. Geef deze informatie op in het deel venster **subnet toevoegen** .

      * **Naam**: de naam voor uw subnet
      * **Adres bereik (CIDR-blok)** : het bereik van uw subnet in uw virtuele netwerk en in CIDR-indeling

      ![Details van subnet toevoegen](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. Wanneer u gereed bent, selecteert u **OK**.

   1. Herhaal deze stappen voor nog drie subnetten.

      > [!NOTE]
      > Als de subnetten die u probeert te maken ongeldig zijn, wordt in de Azure Portal een bericht weer gegeven, maar wordt uw voortgang niet geblokkeerd.

   Zie [subnet van een virtueel netwerk toevoegen](../virtual-network/virtual-network-manage-subnet.md)voor meer informatie over het maken van subnetten.

1. Nadat Azure uw ISE-gegevens heeft gevalideerd, selecteert u **maken**, bijvoorbeeld:

   ![Na een geslaagde validatie selecteert u ' maken '](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure begint met het implementeren van uw omgeving, wat doorgaans binnen twee uur duurt. De implementatie kan af en toe Maxi maal vier uur duren. Als u de implementatie status wilt controleren, selecteert u in de Azure-werk balk het pictogram meldingen, waarmee het deel venster meldingen wordt geopend.

   ![Implementatie status controleren](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Als de implementatie is voltooid, wordt in azure deze melding weer gegeven:

   ![Implementatie is voltooid](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   Volg anders de Azure Portal instructies voor het oplossen van problemen met de implementatie.

   > [!NOTE]
   > Als de implementatie mislukt of als u uw ISE verwijdert, kan het tot een uur duren voordat de subnetten worden vrijgegeven. Deze vertraging betekent dat u mogelijk moet wachten voordat u deze subnetten opnieuw gebruikt in een andere ISE.
   >
   > Als u het virtuele netwerk verwijdert, duurt het over het algemeen Maxi maal twee uur voordat de subnetten worden vrijgegeven, maar deze bewerking kan langer duren. 
   > Wanneer u virtuele netwerken verwijdert, moet u ervoor zorgen dat er nog geen resources zijn verbonden. 
   > Zie [virtueel netwerk verwijderen](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Als u uw omgeving wilt weer geven, selecteert u **naar resource gaan** als Azure niet automatisch naar uw omgeving gaat nadat de implementatie is voltooid.

1. Zie [uw integratie service omgeving beheren](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)om de netwerk status van uw ISE te controleren.

1. Zie [artefacten toevoegen aan integratie service omgevingen](../logic-apps/add-artifacts-integration-service-environment-ise.md)om te beginnen met het maken van logische apps en andere artefacten in uw ISE.

   > [!IMPORTANT]
   > Beheerde ISE-connectors die beschikbaar zijn nadat u uw ISE hebt gemaakt, worden niet automatisch weer gegeven in de connector kiezer van de ontwerp functie voor logische apps. Voordat u deze ISE-connectors kunt gebruiken, moet u [deze connectors hand matig toevoegen aan uw ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) zodat ze worden weer gegeven in de ontwerp functie voor logische apps.

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>ISE-capaciteit toevoegen

De Premium ISE-basis eenheid heeft een vaste capaciteit, dus als u meer door voer wilt, kunt u tijdens het maken of later meer schaal eenheden toevoegen. De Developer SKU bevat niet de mogelijkheid om schaal eenheden toe te voegen.

1. Zoek uw ISE in de Azure Portal.

1. Als u de metrische gegevens over gebruik en prestaties voor uw ISE wilt bekijken, selecteert u in het menu ISE **overzicht**.

   ![Gebruik voor ISE weer geven](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-usage.png)

1. Onder **instellingen**selecteert u **uitschalen**. Selecteer een van deze opties in het deel venster **configureren** :

   * [**Hand matig schalen**](#manual-scale): schalen op basis van het aantal verwerkings eenheden dat u wilt gebruiken.
   * [**Aangepaste automatisch schalen**](#custom-autoscale): schalen op basis van metrische gegevens voor prestaties door te selecteren uit verschillende criteria en de drempel waarden op te geven voor het voldoen aan de criteria.

   ![Selecteer het gewenste schaal type](./media/connect-virtual-network-vnet-isolated-environment/select-scale-out-options.png)

<a name="manual-scale"></a>

### <a name="manual-scale"></a>Hand matig schalen

1. Wanneer u **hand matig schalen**hebt geselecteerd, selecteert u voor **extra capaciteit**het aantal schaal eenheden dat u wilt gebruiken.

   ![Selecteer het gewenste schaal type](./media/connect-virtual-network-vnet-isolated-environment/select-manual-scale-out-units.png)

1. Selecteer **Opslaan** als u klaar bent.

<a name="custom-autoscale"></a>

### <a name="custom-autoscale"></a>Aangepaste automatisch schalen

1. Nadat u **aangepaste automatisch schalen**hebt geselecteerd, moet u bij **naam van instelling voor automatisch schalen**een naam opgeven voor de instelling en optioneel de Azure-resource groep selecteren waarvan de instelling deel uitmaakt.

   ![Geef een naam op voor de instelling voor automatisch schalen en selecteer een resource groep](./media/connect-virtual-network-vnet-isolated-environment/select-custom-autoscale.png)

1. Voor de **standaard** voorwaarde selecteert u **schaal op basis van een metriek** of **schalen naar een specifiek aantal exemplaren**.

   * Als u op instantie gebaseerd kiest, voert u het nummer in voor de verwerkings eenheden. Dit is een waarde van 0 tot 10.

   * Als u op basis van metrische gegevens kiest, voert u de volgende stappen uit:

     1. Selecteer **een regel toevoegen**in de sectie **regels** .

     1. Stel in het deel venster **schaal regel** uw criteria en actie in die moeten worden uitgevoerd wanneer de regel wordt geactiveerd.

     1. Geef voor **instantie limieten**de volgende waarden op:

        * **Minimum**: het minimum aantal verwerkings eenheden dat moet worden gebruikt
        * **Maximum**: het maximum aantal verwerkings eenheden dat moet worden gebruikt
        * **Standaard**: als er problemen zijn bij het lezen van de metrische gegevens van de resource en de huidige capaciteit lager is dan de standaard capaciteit, wordt automatisch schalen geschaald naar het standaard aantal verwerkings eenheden. Als de huidige capaciteit echter de standaard capaciteit overschrijdt, wordt automatisch schalen niet geschaald in.

1. Selecteer **schaal voorwaarde toevoegen**om een andere voor waarde toe te voegen.

1. Wanneer u klaar bent met uw instellingen voor automatisch schalen, slaat u de wijzigingen op.

## <a name="delete-ise"></a>ISE verwijderen

Voordat u een ISE verwijdert die u niet meer nodig hebt of een Azure-resource groep die een ISE bevat, moet u controleren of u geen beleids regels of vergren delingen hebt voor de Azure-resource groep die deze resources bevat of op uw virtuele Azure-netwerk, omdat deze items het verwijderen kunnen blok keren.

Nadat u uw ISE hebt verwijderd, moet u mogelijk tot negen uur wachten voordat u probeert uw virtuele Azure-netwerk of subnetten te verwijderen.

## <a name="next-steps"></a>Volgende stappen

* [Artefacten toevoegen aan integratie service omgevingen](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Controleer de netwerk status voor de integratie service omgevingen](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Meer informatie over [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Meer informatie over de [integratie van virtuele netwerken voor Azure-Services](../virtual-network/virtual-network-for-azure-services.md)
