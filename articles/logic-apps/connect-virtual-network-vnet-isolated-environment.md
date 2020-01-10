---
title: Verbinding maken met virtuele netwerken van Azure met een ISE
description: Maak een ISE (Integration service Environment) die toegang heeft tot Azure Virtual Networks (VNETs) vanaf Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: f024659385a92df97b55e88ae217aa9e1e13d860
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732345"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Verbinding maken met virtuele Azure-netwerken van Azure Logic Apps met behulp van een ISE (Integration service Environment)

Voor scenario's waarin uw Logic apps en integratie accounts toegang nodig hebben tot een [virtueel Azure-netwerk](../virtual-network/virtual-networks-overview.md), maakt u een [ISE ( *Integration service Environment* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Een ISE is een particuliere en geïsoleerde omgeving die gebruikmaakt van speciale opslag en andere bronnen die gescheiden worden gehouden van de open bare, ' wereld wijde ' multi tenant-Logic Apps service. Deze schei ding vermindert ook de invloed die andere Azure-tenants mogelijk hebben op de prestaties van uw apps. Een ISE biedt u ook uw eigen vaste IP-adressen. Deze IP-adressen zijn gescheiden van de statische IP-adressen die worden gedeeld door de Logic apps in de open bare multi tenant-service.

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

  * Het virtuele netwerk moet vier *lege* subnetten hebben voor het maken en implementeren van resources in uw ISE. U kunt deze subnetten vooraf maken, maar u kunt wachten totdat u de ISE maakt waar u subnetten tegelijk kunt maken. Meer informatie over de vereisten voor het [subnet](#create-subnet).

  * De namen van subnetten moeten beginnen met een alfabetisch teken of een liggend streepje en kunnen niet de volgende tekens gebruiken: `<`, `>`, `%`, `&`, `\\`, `?`, `/`. 
  
  * Als u de ISE via een Azure Resource Manager sjabloon wilt implementeren, moet u eerst een leeg subnet delegeren naar micro soft. Logic/integrationServiceEnvironment. U hoeft deze delegatie niet uit te voeren wanneer u via de Azure Portal implementeert.

  * Zorg ervoor dat het virtuele netwerk [toegang biedt tot uw ISE](#enable-access) zodat uw ISE goed kan werken en toegankelijk moet blijven.

  * Als u [ExpressRoute](../expressroute/expressroute-introduction.md)gebruikt, dat een particuliere verbinding met micro soft-Cloud Services biedt, moet u [een route tabel maken](../virtual-network/manage-route-table.md) die de volgende route heeft en die tabel koppelen aan elk subnet dat wordt gebruikt door uw ISE:

    **Naam**: <*route naam*><br>
    **Adres voorvoegsel**: 0.0.0.0/0<br>
    **Volgende hop**: Internet

* Als u aangepaste DNS-servers wilt gebruiken voor uw virtuele Azure-netwerk, [stelt u die servers in door de volgende stappen uit te voeren](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) voordat u uw ISE implementeert in uw virtuele netwerk. Anders moet u, telkens wanneer u de DNS-server wijzigt, uw ISE opnieuw opstarten.

  > [!IMPORTANT]
  > Als u de instellingen van de DNS-server wijzigt nadat u een ISE hebt gemaakt, moet u ervoor zorgen dat u de ISE opnieuw start. Zie [een virtueel netwerk maken, wijzigen of verwijderen](../virtual-network/manage-virtual-network.md#change-dns-servers)voor meer informatie over het beheren van DNS-server instellingen.

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>Toegang inschakelen voor ISE

Wanneer u een ISE gebruikt met een virtueel Azure-netwerk, heeft een veelvoorkomend installatie probleem een of meer geblokkeerde poorten. De connectors die u gebruikt voor het maken van verbindingen tussen uw ISE-en doel systemen, hebben mogelijk ook hun eigen poort vereisten. Als u bijvoorbeeld communiceert met een FTP-systeem met behulp van de FTP-connector, moet de poort die u op uw FTP-systeem gebruikt, beschikbaar zijn, bijvoorbeeld poort 21 voor het verzenden van opdrachten.

Om ervoor te zorgen dat uw ISE toegankelijk is en dat de Logic apps in die ISE kunnen communiceren via de subnetten in uw virtuele netwerk, [opent u de poorten in deze tabel](#network-ports-for-ise). Als de vereiste poorten niet beschikbaar zijn, zal uw ISE niet goed werken.

* Als u meerdere ISE-exemplaren hebt die toegang nodig hebben tot andere eind punten met IP-beperkingen, implementeert u een [Azure firewall](../firewall/overview.md) of een [virtueel netwerk apparaat](../virtual-network/virtual-networks-overview.md#filter-network-traffic) in uw virtuele netwerk en stuurt u uitgaand verkeer via die firewall of het virtuele netwerk apparaat. U kunt vervolgens [een enkel, uitgaand, openbaar, statisch en voorspelbaar IP-adres instellen](connect-virtual-network-vnet-set-up-single-ip-address.md) dat alle ISE-instanties in uw virtuele netwerk kunnen gebruiken om te communiceren met doel systemen. Op die manier hoeft u geen aanvullende firewall-openingen op deze doel systemen in te stellen voor elke ISE.

   > [!NOTE]
   > U kunt deze methode gebruiken voor één ISE wanneer u voor uw scenario het aantal IP-adressen wilt beperken dat toegang nodig heeft. Bepaal of de extra kosten voor de firewall of het virtuele netwerk apparaat logisch kunnen zijn voor uw scenario. Meer informatie over [Azure firewall prijzen](https://azure.microsoft.com/pricing/details/azure-firewall/).

* Als u een nieuw virtueel Azure-netwerk en subnetten zonder beperkingen hebt gemaakt, hoeft u geen [netwerk beveiligings groepen (nsg's)](../virtual-network/security-overview.md#network-security-groups) in te stellen in uw virtuele netwerk om verkeer tussen subnetten te beheren.

* In een bestaand virtueel netwerk kunt u *optioneel* nsg's instellen door [netwerk verkeer te filteren op subnetten](../virtual-network/tutorial-filter-network-traffic.md). Als u deze route kiest, moet u in het virtuele netwerk waar u de Nsg's wilt instellen, ervoor zorgen dat u [de poorten in deze tabel opent](#network-ports-for-ise). Als u [NSG-beveiligings regels](../virtual-network/security-overview.md#security-rules)gebruikt, hebt u TCP-en UDP-protocollen nodig.

* Als u al een bestaand Nsg's hebt, moet u ervoor zorgen dat u [de poorten in deze tabel opent](#network-ports-for-ise). Als u [NSG-beveiligings regels](../virtual-network/security-overview.md#security-rules)gebruikt, hebt u TCP-en UDP-protocollen nodig.

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Netwerk poorten die worden gebruikt door uw ISE

In deze tabel worden de poorten in uw virtuele Azure-netwerk beschreven die uw ISE gebruikt en waar deze poorten worden gebruikt. De [Resource Manager-service Tags](../virtual-network/security-overview.md#service-tags) vertegenwoordigen een groep IP-adres voorvoegsels die de complexiteit helpen beperken bij het maken van beveiligings regels.

> [!IMPORTANT]
> Bron poorten zijn kortstondig, dus zorg ervoor dat u deze instelt op `*` voor alle regels.

| Doel | Richting | Doelpoorten | Servicetag van bron | Doelservicetag | Opmerkingen |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| Intrasubnet-communicatie | Binnenkomende &-uitgaand | * | - | - | **Belang rijk**: Zorg ervoor dat u alle poorten in deze subnetten opent voor communicatie tussen onderdelen binnen subnetten. |
| Communicatie tussen subnet | Binnenkomende &-uitgaand | 80, 443 | VirtualNetwork | VirtualNetwork | Voor communicatie tussen subnetten |
| Communicatie van Azure Logic Apps | Uitgaand | 80, 443 | VirtualNetwork | Internet | De poort is afhankelijk van de externe service waarmee de Logic Apps-service communiceert |
| Azure Active Directory | Uitgaand | 80, 443 | VirtualNetwork | AzureActiveDirectory | |
| Azure Storage afhankelijkheid | Uitgaand | 80, 443 | VirtualNetwork | Storage | |
| Communicatie met Azure Logic Apps | Inkomend | 443 | Interne toegangs eindpunten: <br>VirtualNetwork <p><p>Externe toegangs eindpunten: <br>Internet <p><p>**Opmerking**: deze eind punten verwijzen naar de eindpunt instelling die is [geselecteerd bij het maken van ISE](connect-virtual-network-vnet-isolated-environment.md#create-environment). Zie [endpoint Access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)voor meer informatie. | VirtualNetwork | Het IP-adres voor de computer of service dat een aanvraag trigger of webhook aanroept die voor komt in uw logische app. Het sluiten of blok keren van deze poort voor komt HTTP-aanroepen naar Logic apps met aanvraag triggers. |
| Uitvoerings geschiedenis van de logische app | Inkomend | 443 | Interne toegangs eindpunten: <br>VirtualNetwork <p><p>Externe toegangs eindpunten: <br>Internet <p><p>**Opmerking**: deze eind punten verwijzen naar de eindpunt instelling die is [geselecteerd bij het maken van ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#create-environment). Zie [endpoint Access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)voor meer informatie. | VirtualNetwork | Het IP-adres van de computer van waaruit u de uitvoerings geschiedenis van de logische app weergeeft. Hoewel het sluiten of blok keren van deze poort verhindert dat u de uitvoerings geschiedenis bekijkt, kunt u de invoer en uitvoer voor elke stap in de uitvoerings geschiedenis niet weer geven. |
| Verbindings beheer | Uitgaand | 443 | VirtualNetwork  | AppService | |
| Diagnostische logboeken publiceren & metrische gegevens | Uitgaand | 443 | VirtualNetwork  | AzureMonitor | |
| Communicatie van Azure Traffic Manager | Inkomend | 443 | AzureTrafficManager | VirtualNetwork | |
| Logic Apps Designer: dynamische eigenschappen | Inkomend | 454 | Zie de kolom opmerkingen voor het toestaan van IP-adressen | VirtualNetwork | Aanvragen zijn afkomstig van de Logic Apps [binnenkomende](../logic-apps/logic-apps-limits-and-config.md#inbound) IP-adressen van het toegangs punt voor die regio. |
| Netwerk status controle | Inkomend | 454 | Zie de kolom opmerkingen voor het toestaan van IP-adressen | VirtualNetwork | Aanvragen zijn afkomstig van het Logic Apps toegangs punt voor zowel [binnenkomende](../logic-apps/logic-apps-limits-and-config.md#inbound) als [uitgaande](../logic-apps/logic-apps-limits-and-config.md#outbound) IP-adressen voor die regio. |
| Afhankelijkheid van App Service beheer | Inkomend | 454, 455 | AppServiceManagement | VirtualNetwork | |
| Connector implementatie | Inkomend | 454 | AzureConnectors | VirtualNetwork | Nodig voor het implementeren en bijwerken van connectors. Als u deze poort sluit of blokkeert, mislukken ISE-implementaties en wordt het bijwerken of oplossen van connectors voor komen. |
| Implementatie van connector beleid | Inkomend | 3443 | Internet | VirtualNetwork | Nodig voor het implementeren en bijwerken van connectors. Als u deze poort sluit of blokkeert, mislukken ISE-implementaties en wordt het bijwerken of oplossen van connectors voor komen. |
| Azure SQL-afhankelijkheid | Uitgaand | 1433 | VirtualNetwork | SQL | |
| Azure Resource Health | Uitgaand | 1886 | VirtualNetwork | AzureMonitor | Voor het publiceren van de status naar Resource Health |
| API Management-beheer eindpunt | Inkomend | 3443 | APIManagement | VirtualNetwork | |
| Afhankelijkheid van het logboek-naar-Event hub-beleid en-bewakings agent | Uitgaand | 5672 | VirtualNetwork | EventHub | |
| Toegang tot Azure cache voor redis instanties tussen Rolinstanties | Inkomend <br>Uitgaand | 6379-6383 | VirtualNetwork | VirtualNetwork | Bovendien moet u, om ISE te kunnen gebruiken met Azure cache voor redis, deze [uitgaande en binnenkomende poorten openen die worden beschreven in de Azure-cache voor redis-Veelgestelde vragen](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| Azure Load Balancer | Inkomend | * | AzureLoadBalancer | VirtualNetwork | |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Uw ISE maken

Voer de volgende stappen uit om uw ISE (Integration service Environment) te maken:

1. Selecteer in de [Azure Portal](https://portal.azure.com)in het hoofd menu van Azure de optie **een resource maken**.
In het zoekvak voert u "Integration service Environment" in als uw filter.

   ![Nieuwe resource maken](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Kies **maken**in het deel venster voor het maken van Integratieserviceomgeving.

   ![Kies ' maken '](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Geef deze gegevens voor uw omgeving op en kies vervolgens **controleren + maken**, bijvoorbeeld:

   ![Details van de omgeving opgeven](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Eigenschap | Verplicht | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Abonnement** | Ja | <*Azure-subscription-name*> | Het Azure-abonnement dat u wilt gebruiken voor uw omgeving |
   | **Resourcegroep** | Ja | <*Azure-resource-group-name*> | De Azure-resource groep waar u uw omgeving wilt maken |
   | **Naam van de integratie service omgeving** | Ja | <*omgeving naam*> | De naam van uw ISE, die alleen letters, cijfers, afbreek streepjes (`-`), onderstrepings tekens (`_`) en punten (`.`) kan bevatten. |
   | **Locatie** | Ja | <*Azure-datacenter-region*> | De Azure Data Center-regio waar u uw omgeving kunt implementeren |
   | **SKU** | Ja | **Premium** of **ontwikkelaar (geen sla)** | De ISE-SKU die u wilt maken en gebruiken. Zie [ISE sku's](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)(Engelstalig) voor verschillen tussen deze sku's. <p><p>**Belang rijk**: deze optie is alleen beschikbaar bij het maken van ISE en kan later niet worden gewijzigd. |
   | **Extra capaciteit** | Premium: <br>Ja <p><p>Developer: <br>Niet van toepassing | Premium: <br>0 tot 10 <p><p>Developer: <br>Niet van toepassing | Het aantal extra verwerkings eenheden dat voor deze ISE-resource moet worden gebruikt. Zie [ISE-capaciteit toevoegen](#add-capacity)om capaciteit toe te voegen na het maken. |
   | **Toegangs eindpunt** | Ja | **Intern** of **extern** | Het type toegangs eindpunten dat moet worden gebruikt voor uw ISE. Deze eind punten bepalen of de aanvraag of webhook triggers op Logic apps in uw ISE kan ontvangen van buiten uw virtuele netwerk. <p><p>Uw selectie is ook van invloed op de manier waarop u invoer en uitvoer kunt weer geven en openen in de geschiedenis van de logische app-uitvoeringen. Zie [ISE endpoint Access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)(Engelstalig) voor meer informatie. <p><p>**Belang rijk**: deze optie is alleen beschikbaar bij het maken van ISE en kan later niet worden gewijzigd. |
   | **Virtueel netwerk** | Ja | <*Azure-naam van virtueel netwerk*> | Het virtuele Azure-netwerk waar u uw omgeving wilt injecteren zodat logische apps in die omgeving toegang hebben tot uw virtuele netwerk. Als u nog geen netwerk hebt, [maakt u eerst een virtueel netwerk van Azure](../virtual-network/quick-create-portal.md). <p>**Belang rijk**: u kunt deze injectie *alleen* uitvoeren wanneer u uw ISE maakt. |
   | **Subnets** | Ja | <*subnet-resource-list*> | Een ISE vereist vier *lege* subnetten voor het maken en implementeren van resources in uw omgeving. [Volg de stappen onder deze tabel](#create-subnet)om elk subnet te maken. |
   |||||

   <a name="create-subnet"></a>

   **Subnet maken**

   Voor het maken en implementeren van resources in uw omgeving, heeft uw ISE vier *lege* subnetten nodig die niet worden overgedragen aan een service. U *kunt* deze subnet-adressen niet wijzigen nadat u uw omgeving hebt gemaakt.

   > [!IMPORTANT]
   > 
   > Namen van subnetten moeten beginnen met een letter of een onderstrepings teken (geen getallen). deze tekens worden niet gebruikt: `<`, `>`, `%`, `&`, `\\`, `?`, `/`.

   Daarnaast moet elk subnet aan de volgende vereisten voldoen:

   * Maakt gebruik van de [CIDR-notatie (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) en een klasse B-adres ruimte.

   * Maakt gebruik van Mini maal een `/27` in de adres ruimte, omdat elk subnet *ten minste* 32 adressen moet *hebben.* Bijvoorbeeld:

     * `10.0.0.0/27` heeft 32 adressen omdat 2<sup>(32-27)</sup> 2<sup>5</sup> of 32 is.

     * `10.0.0.0/24` heeft 256 adressen omdat 2<sup>(32-24)</sup> 2<sup>8</sup> of 256 is.

     * `10.0.0.0/28` heeft slechts 16 adressen en is te klein omdat 2<sup>(32-28)</sup> 2<sup>4</sup> of 16 is.

     Zie voor meer informatie over het berekenen van adressen [IPv4 CIDR-blokken](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Als u [ExpressRoute](../expressroute/expressroute-introduction.md)gebruikt, moet u [een route tabel](../virtual-network/manage-route-table.md) met de volgende route maken en deze tabel koppelen aan elk subnet dat wordt gebruikt door uw ISE:

     **Naam**: <*route naam*><br>
     **Adres voorvoegsel**: 0.0.0.0/0<br>
     **Volgende hop**: Internet

   1. Kies in de lijst **subnets** de optie **subnet-configuratie beheren**.

      ![Subnet-configuratie beheren](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. Kies **subnet**in het deel venster **subnetten** .

      ![Subnet toevoegen](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. Geef deze informatie op in het deel venster **subnet toevoegen** .

      * **Naam**: de naam voor uw subnet
      * **Adres bereik (CIDR-blok)** : het bereik van uw subnet in uw virtuele netwerk en in CIDR-indeling

      ![Details van subnet toevoegen](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. Als u klaar bent, kiest u **Done**.

   1. Herhaal deze stappen voor nog drie subnetten.

      > [!NOTE]
      > Als de subnetten die u probeert te maken ongeldig zijn, wordt in de Azure Portal een bericht weer gegeven, maar wordt uw voortgang niet geblokkeerd.

   Zie [subnet van een virtueel netwerk toevoegen](../virtual-network/virtual-network-manage-subnet.md)voor meer informatie over het maken van subnetten.

1. Nadat Azure uw ISE-gegevens heeft gevalideerd, kiest u **maken**, bijvoorbeeld:

   ![Nadat de validatie is voltooid, kiest u maken](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure begint met het implementeren van uw omgeving, wat doorgaans binnen twee uur duurt. De implementatie kan af en toe Maxi maal vier uur duren. Als u de implementatie status wilt controleren, klikt u op de Azure-werk balk op het pictogram meldingen, waarmee het deel venster meldingen wordt geopend.

   ![Implementatie status controleren](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Als de implementatie is voltooid, wordt in azure deze melding weer gegeven:

   ![Implementatie geslaagd](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   Volg anders de Azure Portal instructies voor het oplossen van problemen met de implementatie.

   > [!NOTE]
   > Als de implementatie mislukt of als u uw ISE verwijdert, kan het tot een uur duren voordat de subnetten worden vrijgegeven. Deze vertraging betekent dat u mogelijk moet wachten voordat u deze subnetten opnieuw gebruikt in een andere ISE.
   >
   > Als u het virtuele netwerk verwijdert, duurt het over het algemeen Maxi maal twee uur voordat de subnetten worden vrijgegeven, maar deze bewerking kan langer duren. 
   > Wanneer u virtuele netwerken verwijdert, moet u ervoor zorgen dat er nog geen resources zijn verbonden. 
   > Zie [virtueel netwerk verwijderen](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Als u uw omgeving wilt weer geven, kiest **u naar resource gaan** als Azure niet automatisch naar uw omgeving gaat nadat de implementatie is voltooid.

1. Zie [uw integratie service omgeving beheren](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)om de netwerk status van uw ISE te controleren.

1. Zie [artefacten toevoegen aan integratie service omgevingen](../logic-apps/add-artifacts-integration-service-environment-ise.md)om te beginnen met het maken van logische apps en andere artefacten in uw ISE.

   > [!IMPORTANT]
   > Beheerde ISE-connectors die beschikbaar zijn nadat u uw ISE hebt gemaakt, worden niet automatisch weer gegeven in de connector kiezer van de ontwerp functie voor logische apps. Voordat u deze ISE-connectors kunt gebruiken, moet u [deze connectors hand matig toevoegen aan uw ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) zodat ze worden weer gegeven in de ontwerp functie voor logische apps.

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>ISE-capaciteit toevoegen

De Premium ISE-basis eenheid heeft een vaste capaciteit, dus als u meer door voer wilt, kunt u tijdens het maken of later meer schaal eenheden toevoegen. U kunt automatisch schalen op basis van metrische gegevens over prestaties of op basis van een aantal extra verwerkings eenheden. Als u automatisch schalen kiest op basis van metrische gegevens, kunt u kiezen uit verschillende criteria en de drempel waarden opgeven voor het voldoen aan de criteria. De Developer SKU bevat niet de mogelijkheid om schaal eenheden toe te voegen.

1. Zoek uw ISE in de Azure Portal.

1. Als u de metrische gegevens over gebruik en prestaties voor uw ISE wilt bekijken, selecteert u in het hoofd menu van uw ISE **overzicht**.

   ![Gebruik voor ISE weer geven](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-usage.png)

1. Als u automatisch schalen wilt instellen, selecteert u onder **instellingen**de optie **uitschalen**. Kies op het tabblad **configureren** de optie **automatisch schalen inschakelen**.

   ![Automatisch schalen inschakelen](./media/connect-virtual-network-vnet-isolated-environment/scale-out.png)

1. Geef bij **naam van instelling voor automatisch schalen**een naam op voor de instelling.

1. Kies in het gedeelte **standaard** de optie **schalen op basis van een metriek** of **schalen naar een specifiek aantal exemplaren**.

   * Als u op instantie gebaseerd kiest, voert u het aantal verwerkings eenheden in tussen 0 en 10.

   * Als u op basis van metrische gegevens kiest, voert u de volgende stappen uit:

     1. Klik in de sectie **regels** op **een regel toevoegen**.

     1. Stel in het deel venster **schaal regel** uw criteria en actie in die moeten worden uitgevoerd wanneer de regel wordt geactiveerd.

     1. Wanneer u klaar bent, kiest u **toevoegen**.

1. Wanneer u klaar bent met uw instellingen voor automatisch schalen, slaat u de wijzigingen op.

## <a name="delete-ise"></a>ISE verwijderen

Voordat u een ISE verwijdert die u niet meer nodig hebt of een Azure-resource groep die een ISE bevat, moet u controleren of u geen beleids regels of vergren delingen hebt voor de Azure-resource groep die deze resources bevat of op uw virtuele Azure-netwerk, omdat deze items het verwijderen kunnen blok keren.

Nadat u uw ISE hebt verwijderd, moet u mogelijk tot negen uur wachten voordat u probeert uw virtuele Azure-netwerk of subnetten te verwijderen.

## <a name="next-steps"></a>Volgende stappen

* [Artefacten toevoegen aan integratie service omgevingen](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Controleer de netwerk status voor de integratie service omgevingen](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Meer informatie over [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Meer informatie over de [integratie van virtuele netwerken voor Azure-Services](../virtual-network/virtual-network-for-azure-services.md)
