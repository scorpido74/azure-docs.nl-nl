---
title: Verbinding maken met virtuele Azure-netwerken vanuit Azure Logic Apps via een ISE (Integration service Environment)
description: Maak een ISE (Integration service Environment), zodat Logic apps en integratie accounts toegang kunnen krijgen tot Azure Virtual Networks (VNETs), terwijl ze privé blijven en geïsoleerd zijn van open bare of ' wereld wijd ' Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: ce663dec47f99b6ba4751e23e7ac7f13de866a5d
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982983"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Verbinding maken met virtuele Azure-netwerken van Azure Logic Apps met behulp van een ISE (Integration service Environment)

Voor scenario's waarin uw Logic apps en integratie accounts toegang nodig hebben tot een [virtueel Azure-netwerk](../virtual-network/virtual-networks-overview.md), maakt u een [ISE ( *Integration service Environment* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Een ISE is een particuliere en geïsoleerde omgeving waarin speciale opslag en andere bronnen worden gebruikt die gescheiden van de open bare of de wereld wijde Logic Apps-service worden bewaard. Deze schei ding vermindert ook de invloed die andere Azure-tenants mogelijk hebben op de prestaties van uw apps.

Wanneer u een ISE maakt, *injecteert* Azure die ISE in uw virtuele Azure-netwerk, waarna de Logic apps-service in uw virtuele netwerk wordt geïmplementeerd. Wanneer u een logische app of een integratie account maakt, selecteert u uw ISE als locatie. Uw logische app of integratie account kan vervolgens rechtstreeks toegang krijgen tot resources, zoals virtuele machines (Vm's), servers, systemen en services, in uw virtuele netwerk.

![Integratie service omgeving selecteren](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Voor logische apps en integratie accounts die in een ISE samen werken, moeten beide *dezelfde ISE* gebruiken als hun locatie.

Een ISE heeft de limieten voor de duur van de uitvoering, de opslag ruimte, de door Voer, de HTTP-aanvraag en de time-out van het antwoord, de bericht grootte en de aangepaste connector aanvragen verhoogd. Zie [limieten en configuratie voor Azure Logic apps](logic-apps-limits-and-config.md)voor meer informatie. Zie [toegang tot Azure Virtual Network-resources vanuit Azure Logic apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)voor meer informatie over ISEs.

In dit artikel wordt beschreven hoe u deze taken kunt volt ooien:

* Zorg ervoor dat alle benodigde poorten op het virtuele netwerk zijn geopend, zodat verkeer kan worden verzonden door uw ISE in de subnetten in dat virtuele netwerk.

* Maak uw ISE.

* Voeg extra capaciteit toe aan uw ISE.

> [!IMPORTANT]
> Logic apps, ingebouwde triggers, ingebouwde acties en connectors die worden uitgevoerd in uw ISE, gebruiken een prijs plan dat verschilt van het prijs plan op basis van verbruik. Zie het [Logic apps-prijs model](../logic-apps/logic-apps-pricing.md#fixed-pricing)voor meer informatie over de prijzen en facturerings werkzaamheden voor ISEs. Zie [Logic apps prijzen](../logic-apps/logic-apps-pricing.md)voor prijs tarieven.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een [virtueel Azure-netwerk](../virtual-network/virtual-networks-overview.md). Als u geen virtueel netwerk hebt, leert u hoe u [een virtueel Azure-netwerk maakt](../virtual-network/quick-create-portal.md).

  * Het virtuele netwerk moet vier *lege* subnetten hebben voor het maken en implementeren van resources in uw ISE. U kunt deze subnetten vooraf maken, maar u kunt wachten totdat u de ISE maakt waar u subnetten tegelijk kunt maken. Meer informatie over de vereisten voor het [subnet](#create-subnet).
  
    > [!NOTE]
    > Als u [ExpressRoute](../expressroute/expressroute-introduction.md)gebruikt, dat een particuliere verbinding met micro soft-Cloud Services biedt, moet u [een route tabel maken](../virtual-network/manage-route-table.md) die de volgende route bevat en die tabel koppelen aan elk subnet dat wordt gebruikt door uw ISE:
    > 
    > **Naam**: <*route naam*><br>
    > **Adres voorvoegsel**: 0.0.0.0/0<br>
    > **Volgende hop**: Internet

  * Zorg ervoor dat het virtuele netwerk [deze poorten beschikbaar maakt](#ports) , zodat uw ISE goed werkt en toegankelijk blijft.

* Als u aangepaste DNS-servers wilt gebruiken voor uw virtuele Azure-netwerk, [stelt u die servers in door de volgende stappen uit te voeren](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) voordat u uw ISE implementeert in uw virtuele netwerk. Anders moet u, telkens wanneer u de DNS-server wijzigt, uw ISE opnieuw opstarten. Dit is een functie die beschikbaar is in de open bare preview van ISE.

<a name="ports"></a>

## <a name="check-network-ports"></a>Netwerk poorten controleren

Wanneer u een ISE met een bestaand virtueel netwerk gebruikt, heeft een veelvoorkomend installatie probleem een of meer geblokkeerde poorten. De connectors die u gebruikt voor het maken van verbindingen tussen uw ISE en het doel systeem, hebben mogelijk ook hun eigen poort vereisten. Als u bijvoorbeeld met de FTP-connector communiceert met een FTP-systeem, moet u ervoor zorgen dat de poort die u op dat FTP-systeem gebruikt, zoals poort 21 voor het verzenden van opdrachten, beschikbaar is.

Als u een nieuw virtueel netwerk en een subnet zonder beperkingen hebt gemaakt, hoeft u geen [netwerk beveiligings groepen (nsg's)](../virtual-network/security-overview.md) in te stellen in uw virtuele netwerk, zodat u verkeer tussen subnets kunt beheren. Voor een bestaand virtueel netwerk kunt u *optioneel* nsg's instellen door [netwerk verkeer te filteren](../virtual-network/tutorial-filter-network-traffic.md)op subnetten. Als u deze route kiest, moet u ervoor zorgen dat uw ISE specifieke poorten opent, zoals beschreven in de volgende tabel, op het virtuele netwerk met de Nsg's. Voor bestaande Nsg's of firewalls in uw virtuele netwerk, moet u er dus voor zorgen dat deze poorten worden geopend. Op die manier blijft uw ISE toegankelijk en werkt deze goed, zodat u geen toegang meer hebt tot uw ISE. Als de vereiste poorten niet beschikbaar zijn, werkt uw ISE niet meer.

In deze tabel worden de poorten in het virtuele netwerk beschreven die uw ISE gebruikt en waar deze poorten worden gebruikt. De [Resource Manager-service Tags](../virtual-network/security-overview.md#service-tags) vertegenwoordigen een groep IP-adres voorvoegsels die de complexiteit helpen beperken bij het maken van beveiligings regels.

> [!IMPORTANT]
> Voor interne communicatie binnen uw subnetten vereist ISE dat u alle poorten in die subnetten opent.

| Doel | Direction | Poorten | Bronservicetag | Doelservicetag | Opmerkingen |
|---------|-----------|-------|--------------------|-------------------------|-------|
| Communicatie van Azure Logic Apps | Uitgaande | 80 & 443 | VirtualNetwork | Internet | De poort is afhankelijk van de externe service waarmee de Logic Apps-service communiceert |
| Azure Active Directory | Uitgaande | 80 & 443 | VirtualNetwork | AzureActiveDirectory | |
| Azure Storage afhankelijkheid | Uitgaande | 80 & 443 | VirtualNetwork | Storage | |
| Communicatie tussen subnet | Binnenkomende &-uitgaand | 80 & 443 | VirtualNetwork | VirtualNetwork | Voor communicatie tussen subnetten |
| Communicatie met Azure Logic Apps | Inkomend | 443 | Interne toegangs eindpunten: <br>VirtualNetwork <p><p>Externe toegangs eindpunten: <br>Internet <p><p>**Opmerking**: Deze eind punten verwijzen naar de eindpunt instelling die is [geselecteerd bij het maken van ISE](#create-environment). Zie [endpoint Access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)voor meer informatie. | VirtualNetwork | Het IP-adres voor de computer of service dat een aanvraag trigger of webhook aanroept die voor komt in uw logische app. Het sluiten of blok keren van deze poort voor komt HTTP-aanroepen naar Logic apps met aanvraag triggers. |
| Uitvoerings geschiedenis van de logische app | Inkomend | 443 | Interne toegangs eindpunten: <br>VirtualNetwork <p><p>Externe toegangs eindpunten: <br>Internet <p><p>**Opmerking**: Deze eind punten verwijzen naar de eindpunt instelling die is [geselecteerd bij het maken van ISE](#create-environment). Zie [endpoint Access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)voor meer informatie. | VirtualNetwork | Het IP-adres van de computer van waaruit u de uitvoerings geschiedenis van de logische app weergeeft. Hoewel het sluiten of blok keren van deze poort verhindert dat u de uitvoerings geschiedenis bekijkt, kunt u de invoer en uitvoer voor elke stap in de uitvoerings geschiedenis niet weer geven. |
| Verbindings beheer | Uitgaande | 443 | VirtualNetwork  | Internet | |
| Diagnostische logboeken publiceren & metrische gegevens | Uitgaande | 443 | VirtualNetwork  | AzureMonitor | |
| Communicatie van Azure Traffic Manager | Inkomend | 443 | AzureTrafficManager | VirtualNetwork | |
| Logic Apps Designer: dynamische eigenschappen | Inkomend | 454 | Internet  | VirtualNetwork | Aanvragen zijn afkomstig van het Logic Apps [toegangs punt inkomende IP-adressen in die regio](../logic-apps/logic-apps-limits-and-config.md#inbound). |
| Afhankelijkheid van App Service beheer | Inkomend | 454 & 455 | AppServiceManagement | VirtualNetwork | |
| Connector implementatie | Inkomend | 454 & 3443 | Internet  | VirtualNetwork | Nodig voor het implementeren en bijwerken van connectors. Als u deze poort sluit of blokkeert, mislukken ISE-implementaties en wordt het bijwerken of oplossen van connectors voor komen. |
| Azure SQL-afhankelijkheid | Uitgaande | 1433 | VirtualNetwork | SQL |
| Azure Resource Health | Uitgaande | 1886 | VirtualNetwork | AzureMonitor | Voor het publiceren van de status naar Resource Health |
| API Management-beheer eindpunt | Inkomend | 3443 | APIManagement  | VirtualNetwork | |
| Afhankelijkheid van het logboek-naar-Event hub-beleid en-bewakings agent | Uitgaande | 5672 | VirtualNetwork  | EventHub | |
| Toegang tot Azure cache voor redis instanties tussen Rolinstanties | Inkomend <br>Uitgaande | 6379-6383 | VirtualNetwork  | VirtualNetwork | Bovendien moet u, om ISE te kunnen gebruiken met Azure cache voor redis, deze [uitgaande en binnenkomende poorten openen die worden beschreven in de Azure-cache voor redis-Veelgestelde vragen](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| Azure Load Balancer | Inkomend | * | AzureLoadBalancer | VirtualNetwork |  |
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

   | Eigenschap | Vereist | Value | Description |
   |----------|----------|-------|-------------|
   | **Abonnement** | Ja | <*Azure-subscription-name*> | Het Azure-abonnement dat u wilt gebruiken voor uw omgeving |
   | **Resourcegroep** | Ja | <*Azure-resource-group-name*> | De Azure-resource groep waar u uw omgeving wilt maken |
   | **Naam van de integratie service omgeving** | Ja | <*omgeving-naam*> | De naam van uw ISE, die alleen letters, cijfers, afbreek streepjes`-`(), onderstrepings tekens`_`() en punten (`.`) kan bevatten. |
   | **Location** | Ja | <*Azure-datacenter-region*> | De Azure Data Center-regio waar u uw omgeving kunt implementeren |
   | **SKU** | Ja | **Premium** of **ontwikkelaar (geen sla)** | De ISE-SKU die u wilt maken en gebruiken. Zie [ISE sku's](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)(Engelstalig) voor verschillen tussen deze sku's. <p><p>**Belang rijk**: Deze optie is alleen beschikbaar bij het maken van ISE en kan later niet worden gewijzigd. |
   | **Extra capaciteit** | Premium: <br>Ja <p><p>Developer: <br>Niet van toepassing | Premium: <br>0 tot 10 <p><p>Developer: <br>Niet van toepassing | Het aantal extra verwerkings eenheden dat voor deze ISE-resource moet worden gebruikt. Zie [ISE-capaciteit toevoegen](#add-capacity)om capaciteit toe te voegen na het maken. |
   | **Toegangs eindpunt** | Ja | **Intern** of **extern** | Het type toegangs eindpunten dat moet worden gebruikt voor uw ISE, waarmee wordt bepaald of aanvragen of webhooks worden geactiveerd voor Logic apps in uw ISE, kunnen aanroepen ontvangen van buiten uw virtuele netwerk. Het eindpunt type is ook van invloed op de toegang tot invoer en uitvoer in de geschiedenis van de logische app-uitvoeringen. Zie [endpoint Access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)voor meer informatie. <p><p>**Belang rijk**: Deze optie is alleen beschikbaar bij het maken van ISE en kan later niet worden gewijzigd. |
   | **Virtueel netwerk** | Ja | <*Azure-virtueel-netwerk-naam*> | Het virtuele Azure-netwerk waar u uw omgeving wilt injecteren zodat logische apps in die omgeving toegang hebben tot uw virtuele netwerk. Als u nog geen netwerk hebt, [maakt u eerst een virtueel netwerk van Azure](../virtual-network/quick-create-portal.md). <p>**Belang rijk**: U kunt deze injectie *alleen* uitvoeren wanneer u uw ISE maakt. |
   | **Subnets** | Ja | <*subnet-resource-list*> | Een ISE vereist vier *lege* subnetten voor het maken en implementeren van resources in uw omgeving. [Volg de stappen onder deze tabel](#create-subnet)om elk subnet te maken.  |
   |||||

   <a name="create-subnet"></a>

   **Subnet maken**

   Voor het maken en implementeren van resources in uw omgeving, heeft uw ISE vier *lege* subnetten nodig die niet worden overgedragen aan een service. U *kunt* deze subnet-adressen niet wijzigen nadat u uw omgeving hebt gemaakt. Elk subnet moet aan de volgende criteria voldoen:

   * Heeft een naam die begint met een alfabet of een onderstrepings teken en heeft niet de volgende tekens `<`: `>`, `%`, `&`, `\\`, `?`,,`/`

   * Maakt gebruik van de [CIDR-notatie (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) en een klasse B-adres ruimte.

   * Gebruikt ten minste a `/27` in de adres ruimte, omdat elk subnet *ten minste* 32 adressen moet hebben. Bijvoorbeeld:

     * `10.0.0.0/27`heeft 32 adressen omdat 2<sup>(32-27)</sup> 2<sup>5</sup> of 32 is.

     * `10.0.0.0/24`heeft 256 adressen omdat 2<sup>(32-24)</sup> 2<sup>8</sup> of 256 is.

     * `10.0.0.0/28`heeft slechts 16 adressen en is te klein omdat 2<sup>(32-28)</sup> 2<sup>4</sup> of 16 is.

     Zie voor meer informatie over het berekenen van adressen [IPv4 CIDR-blokken](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Als u [ExpressRoute](../expressroute/expressroute-introduction.md)gebruikt, moet u [een route tabel maken](../virtual-network/manage-route-table.md) met de volgende route en deze tabel koppelen aan elk subnet dat wordt gebruikt door uw ISE:

     **Naam**: <*route naam*><br>
     **Adres voorvoegsel**: 0.0.0.0/0<br>
     **Volgende hop**: Internet

   1. Kies in de lijst subnets de optie **subnet-configuratie beheren**.

      ![Subnetconfiguratie beheren](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. Kies **subnet**in het deel venster **subnetten** .

      ![Subnet toevoegen](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. Geef deze informatie op in het deel venster **subnet toevoegen** .

      * **Naam**: De naam voor uw subnet
      * **Adres bereik (CIDR-blok)** : Het bereik van uw subnet in uw virtuele netwerk en in CIDR-indeling

      ![Details van subnet toevoegen](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. Als u klaar bent, kiest u **Done**.

   1. Herhaal deze stappen voor nog drie subnetten.

      > [!NOTE]
      > Als de subnetten die u probeert te maken ongeldig zijn, wordt in de Azure Portal een bericht weer gegeven, maar wordt uw voortgang niet geblokkeerd.

   Zie [subnet van een virtueel netwerk toevoegen](../virtual-network/virtual-network-manage-subnet.md)voor meer informatie over het maken van subnetten.

1. Nadat Azure uw ISE-gegevens heeft gevalideerd, kiest u **maken**, bijvoorbeeld:

   ![Nadat de validatie is voltooid, kiest u maken](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure begint met de implementatie van uw omgeving, maar dit proces *kan* tot twee uur duren voordat de verwerking is voltooid. Als u de implementatie status wilt controleren, klikt u op de Azure-werk balk op het pictogram meldingen, waarmee het deel venster meldingen wordt geopend.

   ![Implementatie status controleren](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Als de implementatie is voltooid, wordt in azure deze melding weer gegeven:

   ![Implementatie is voltooid](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

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

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>ISE-capaciteit toevoegen

De Premium ISE-basis eenheid heeft een vaste capaciteit, dus als u meer door voer wilt, kunt u tijdens het maken of later meer schaal eenheden toevoegen. U kunt automatisch schalen op basis van metrische gegevens over prestaties of op basis van een aantal extra verwerkings eenheden. Als u automatisch schalen kiest op basis van metrische gegevens, kunt u kiezen uit verschillende criteria en de drempel waarden opgeven voor het voldoen aan de criteria. De Developer SKU bevat niet de mogelijkheid om schaal eenheden toe te voegen.

1. Zoek uw ISE in de Azure Portal.

1. Als u de metrische gegevens over gebruik en prestaties voor uw ISE wilt bekijken, selecteert u in het hoofd menu van uw ISE **overzicht**.

   ![Gebruik voor ISE weer geven](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-usage.png)

1. Als u **automatisch**schalen wilt instellen, selecteert u onder **instellingen**de optie uitschalen. Kies op het tabblad **configureren** de optie **automatisch schalen inschakelen**.

   ![Automatisch schalen inschakelen](./media/connect-virtual-network-vnet-isolated-environment/scale-out.png)

1. Geef bij **naam van instelling voor automatisch schalen**een naam op voor de instelling.

1. Kies in het gedeelte **standaard** de optie **schalen op basis van een metriek** of **schalen naar een specifiek aantal exemplaren**.

   * Als u op instantie gebaseerd kiest, voert u het aantal verwerkings eenheden in tussen 0 en 10.

   * Als u op basis van metrische gegevens kiest, voert u de volgende stappen uit:

     1. Klik in de sectie **regels** op **een regel toevoegen**.
     1. Stel in het deel venster **schaal regel** uw criteria en actie in die moeten worden uitgevoerd wanneer de regel wordt geactiveerd.

     1. Wanneer u klaar bent, kiest u **toevoegen**.

1. Wanneer u klaar bent met uw instellingen voor automatisch schalen, slaat u de wijzigingen op.

## <a name="next-steps"></a>Volgende stappen

* [Artefacten toevoegen aan integratie service omgevingen](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Controleer de netwerk status voor de integratie service omgevingen](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Meer informatie over [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Meer informatie over de [integratie van virtuele netwerken voor Azure-Services](../virtual-network/virtual-network-for-azure-services.md)
