---
title: Verbinding maken met virtuele Azure-netwerken met een ISE
description: Een integratieserviceomgeving (ISE) maken die toegang heeft tot Virtuele Azure-netwerken (VNET's) vanuit Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 6683c1b78b0e7ecba162026708c83843e2c08180
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478886"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Verbinding maken met virtuele Azure-netwerken vanuit Azure Logic Apps met behulp van een integratieserviceomgeving (ISE)

Voor scenario's waarin uw logische apps en integratieaccounts toegang tot een [virtueel Azure-netwerk](../virtual-network/virtual-networks-overview.md)nodig hebben, maakt u een [ *integratieserviceomgeving* (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) Een ISE is een geïsoleerde omgeving die gebruik maakt van speciale opslag en andere resources die gescheiden worden gehouden van de 'globale' Logic Apps-service met meerdere tenant. Deze scheiding vermindert ook de impact die andere Azure-tenants kunnen hebben op de prestaties van uw apps. Een ISE voorziet u ook van uw eigen statische IP-adressen. Deze IP-adressen staan los van de statische IP-adressen die worden gedeeld door de logische apps in de openbare, multi-tenant service.

Wanneer u een ISE maakt, *injecteert* Azure die ISE in uw virtuele Azure-netwerk, waarmee de Logic Apps-service vervolgens wordt geïmplementeerd in uw virtuele netwerk. Wanneer u een logische app of integratieaccount maakt, selecteert u uw ISE als locatie. Uw logische app of integratieaccount heeft dan rechtstreeks toegang tot bronnen, zoals virtuele machines (VM's), servers, systemen en services in uw virtuele netwerk.

![Integratieserviceomgeving selecteren](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Als logische apps en integratieaccounts in een ISE samenwerken, moeten beide *dezelfde ISE* gebruiken als hun locatie.

Een ISE heeft verhoogde limieten voor runduur, opslagretentie, doorvoer, HTTP-aanvraag- en antwoordtime-outs, berichtformaten en aangepaste verbindingsaanvragen. Zie [Limieten en configuratie voor Azure Logic Apps voor](../logic-apps/logic-apps-limits-and-config.md)meer informatie. Zie Toegang tot Azure [Virtual Network-bronnen van Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)voor meer informatie over ISE's.

In dit artikel ziet u hoe u deze taken voltooien met behulp van de Azure-portal:

* Toegang inschakelen voor uw ISE.
* Maak uw ISE.
* Voeg extra capaciteit toe aan uw ISE.

U ook een ISE maken met behulp van de [sjabloon Voor snelstartvan Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) of met de LOGIC Apps REST API, inclusief het instellen van door de klant beheerde sleutels:

* [Een integratieserviceomgeving (ISE) maken met de Logic Apps REST API](../logic-apps/create-integration-service-environment-rest-api.md)
* [Door de klant beheerde sleutels instellen om gegevens in rust te versleutelen voor ISE's](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

  > [!IMPORTANT]
  > Logische apps, ingebouwde triggers, ingebouwde acties en connectors die in uw ISE worden uitgevoerd, gebruiken een prijsplan dat verschilt van het prijsplan voor consumptie. Zie het [prijsmodel van Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing)voor meer informatie over hoe prijzen en facturering werken voor ISE's. Zie [Logic Apps-prijzen](../logic-apps/logic-apps-pricing.md)voor prijstarieven.

* Een [virtueel Azure-netwerk](../virtual-network/virtual-networks-overview.md). Als u geen virtueel netwerk hebt, leest u hoe [u een virtueel Azure-netwerk maakt.](../virtual-network/quick-create-portal.md)

  * Uw virtuele netwerk moet vier *lege* subnetten hebben voor het maken en implementeren van resources in uw ISE. Elk subnet ondersteunt een andere Component van Logic Apps die in uw ISE wordt gebruikt. U deze subnetten van tevoren maken, of u wachten tot u uw ISE maakt waar u tegelijkertijd subnetten maken. Meer informatie over [subnetvereisten](#create-subnet).

  * Subnetnamen moeten beginnen met een alfabetisch teken of een underscore en `<` `>`kunnen `%` `&`deze `\\` `?`tekens `/`niet gebruiken: , , , , , , . 
  
  * Als u de ISE wilt implementeren via een Azure Resource Manager-sjabloon, moet u eerst controleren of u één leeg subnet delegert aan Microsoft.Logic/integrationServiceEnvironment. U hoeft deze delegatie niet te doen wanneer u implementeert via de Azure-portal.

  * Zorg ervoor dat uw virtuele netwerk [toegang biedt voor uw ISE,](#enable-access) zodat uw ISE correct kan werken en toegankelijk kan blijven.

  * Als u [ExpressRoute](../expressroute/expressroute-introduction.md)gebruikt , dat een privéverbinding biedt met Microsoft-cloudservices die wordt gefaciliteerd door de connectiviteitsprovider, moet u [een routetabel maken](../virtual-network/manage-route-table.md) met de volgende route en die tabel koppelen aan elk subnet dat door uw ISE wordt gebruikt:

    **Naam**: <*routenaam*><br>
    **Adresvoorvoegsel**: 0.0.0.0/0/0<br>
    **Volgende hop**: Internet

* Als u aangepaste DNS-servers voor uw virtuele Azure-netwerk wilt gebruiken, [stelt u deze servers in door deze stappen](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) te volgen voordat u uw ISE implementeert in uw virtuele netwerk. Zie [Een virtueel netwerk maken, wijzigen of verwijderen](../virtual-network/manage-virtual-network.md#change-dns-servers)voor meer informatie over het beheren van DNS-serverinstellingen.

  > [!NOTE]
  > Als u de dns-server- of DNS-serverinstellingen wijzigt, moet u uw ISE opnieuw opstarten, zodat de ISE deze wijzigingen kan oppikken. Zie [Uw ISE opnieuw starten](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE)voor meer informatie.

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>Toegang inschakelen voor ISE

Wanneer u een ISE met een virtueel Azure-netwerk gebruikt, is een veelvoorkomend installatieprobleem het hebben van een of meer geblokkeerde poorten. De connectors die u gebruikt voor het maken van verbindingen tussen uw ISE- en bestemmingssystemen, hebben mogelijk ook hun eigen poortvereisten. Als u bijvoorbeeld met een FTP-systeem communiceert met de FTP-connector, moet de poort die u op uw FTP-systeem gebruikt, beschikbaar zijn, bijvoorbeeld poort 21 voor het verzenden van opdrachten.

Als u ervoor wilt zorgen dat uw ISE toegankelijk is en dat de logische apps in dat ISE over elk subnet in uw virtuele netwerk kunnen communiceren, [opent u de poorten die in deze tabel zijn beschreven voor elk subnet.](#network-ports-for-ise) Als er geen vereiste poorten beschikbaar zijn, werkt uw ISE niet goed.

* Als u meerdere ISE-exemplaren hebt die toegang nodig hebben tot andere eindpunten met IP-beperkingen, implementeert u een [Azure Firewall](../firewall/overview.md) of een [virtueel netwerktoestel](../virtual-network/virtual-networks-overview.md#filter-network-traffic) in uw virtuele netwerk en routeert uitgaand verkeer via dat firewall of virtuele netwerktoestel. U vervolgens [één eenmalig, uitgaand, openbaar, statisch en voorspelbaar IP-adres instellen](connect-virtual-network-vnet-set-up-single-ip-address.md) dat alle ISE-exemplaren in uw virtuele netwerk kunnen gebruiken om te communiceren met doelsystemen. Op die manier hoeft u geen extra firewallopeningen in te stellen op die bestemmingssystemen voor elke ISE.

   > [!NOTE]
   > U deze benadering gebruiken voor één ISE wanneer uw scenario het aantal IP-adressen moet beperken dat toegang nodig heeft. Overweeg of de extra kosten voor de firewall of het virtuele netwerktoestel zinvol zijn voor uw scenario. Meer informatie over [azure firewall-prijzen](https://azure.microsoft.com/pricing/details/azure-firewall/).

* Als u zonder beperkingen een nieuw Virtueel Azure-netwerk en subnetten hebt gemaakt, hoeft u geen [NETWERKbeveiligingsgroepen (NSG's)](../virtual-network/security-overview.md#network-security-groups) in uw virtuele netwerk in te stellen om het verkeer tussen subnetten te beheren.

* Op een bestaand virtueel netwerk u *optioneel* NSG's instellen door [netwerkverkeer over subnetten te filteren.](../virtual-network/tutorial-filter-network-traffic.md) Als u deze route wilt volgen, of als u al NSG's gebruikt, zorg er dan voor dat u [de poorten in deze tabel](#network-ports-for-ise) opent op het virtuele netwerk waar u NSG's hebt of NSG's wilt instellen.

  > [!NOTE]
  > Als u [NSG-beveiligingsregels](../virtual-network/security-overview.md#security-rules)gebruikt, moet u zowel de TCP- *als* de UDP-protocollen gebruiken. NSG-beveiligingsregels beschrijven de poorten die u moet openen voor de IP-adressen die toegang tot die poorten nodig hebben. Zorg ervoor dat firewalls, routers of andere items die tussen deze eindpunten bestaan, deze poorten ook toegankelijk houden voor die IP-adressen.

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Netwerkpoorten die door uw ISE worden gebruikt

In deze tabel worden de poorten in uw virtuele Azure-netwerk beschreven die uw ISE gebruikt en waar deze poorten worden gebruikt. Om de complexiteit te verminderen wanneer u beveiligingsregels maakt, vertegenwoordigen de [servicetags](../virtual-network/service-tags-overview.md) in de tabel groepen IP-adresvoorvoegsels voor een specifieke Azure-service.

> [!IMPORTANT]
> Bronpoorten zijn vluchtig, dus zorg ervoor dat `*` u ze instelt voor alle regels. In de plaats daarvan verwijzen interne ISE en externe ISE naar het [eindpunt dat is geselecteerd bij ISE-creatie](connect-virtual-network-vnet-isolated-environment.md#create-environment). Zie [Toegang tot eindpunt voor](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)meer informatie . 

| Doel | Richting | Doelpoorten | Bronservicetag | Doelservicetag | Opmerkingen |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| Intersubnetcommunicatie binnen uw virtuele netwerk | Binnenkomende & Uitgaand | * | De adresruimte voor het virtuele netwerk met de subnetten van uw ISE | De adresruimte voor het virtuele netwerk met de subnetten van uw ISE | Vereist voor verkeer te stromen *tussen* de subnetten in uw virtuele netwerk. <p><p>**Belangrijk:** Als het verkeer tussen de *componenten* in elk subnet moet stromen, moet u ervoor zorgen dat u alle poorten binnen elk subnet opent. |
| Communicatie naar uw logica-app | Inkomend | 443 | Intern ISE: <br>VirtualNetwork <p><p>Extern ISE: <br>Internet <br>(zie **Kolom Notities)** | VirtualNetwork | In plaats van de **internetservicetag** te gebruiken, u het bron-IP-adres opgeven voor de computer of service die aanvragen triggers of webhooks aanroept in uw logische app. <p><p>**Belangrijk:** als u deze poort sluit of blokkeert, voorkomt u dat HTTP-aanroepen naar logische apps met aanvraagtriggers worden geactiveerd. |
| Geschiedenis van logische app-run | Inkomend | 443 | Intern ISE: <br>VirtualNetwork <p><p>Extern ISE: <br>Internet <br>(zie **Kolom Notities)** | VirtualNetwork | In plaats van de **internetservicetag** te gebruiken, u het bron-IP-adres voor de computer of service opgeven vanaf de plaats waar u de rungeschiedenis van uw logische app wilt weergeven. <p><p>**Belangrijk:** hoewel het sluiten of blokkeren van deze poort u niet belet de rungeschiedenis te bekijken, u de ingangen en uitvoer voor elke stap in die rungeschiedenis niet bekijken. |
| Logic Apps Designer - dynamische eigenschappen | Inkomend | 454 | LogicAppsManagement | VirtualNetwork | Aanvragen zijn afkomstig van het toegangspunt voor eindpunt [binnenkomende](../logic-apps/logic-apps-limits-and-config.md#inbound) IP-adressen van Logic Apps voor die regio. |
| Connectorimplementatie | Inkomend | 454 | AzureConnectors | VirtualNetwork | Vereist voor het implementeren en bijwerken van connectoren. Door deze poort te sluiten of te blokkeren, worden ISE-implementaties mislukt en worden connectorupdates of -oplossingen voorkomen. |
| Netwerkstatuscontrole | Inkomend | 454 | LogicApps | VirtualNetwork | Aanvragen zijn afkomstig van het toegangspunt Logic Apps voor zowel [binnenkomende](../logic-apps/logic-apps-limits-and-config.md#inbound) als [uitgaande](../logic-apps/logic-apps-limits-and-config.md#outbound) IP-adressen voor die regio. |
| Afhankelijkheid van App-servicebeheer | Inkomend | 454, 455 | AppServiceBeheer | VirtualNetwork | |
| Communicatie vanuit Azure Traffic Manager | Inkomend | Intern ISE: 454 <p><p>Extern ISE: 443 | AzureTrafficManager | VirtualNetwork | |
| API-beheer - eindpunt voor beheer | Inkomend | 3443 | APIBeheer | VirtualNetwork | |
| Implementatie van connectorbeleid | Inkomend | 3443 | APIBeheer | VirtualNetwork | Vereist voor het implementeren en bijwerken van connectoren. Door deze poort te sluiten of te blokkeren, worden ISE-implementaties mislukt en worden connectorupdates of -oplossingen voorkomen. |
| Communicatie vanuit uw logica-app | Uitgaand | 80, 443 | VirtualNetwork | Varieert op basis van bestemming | De eindpunten voor de externe service waarmee uw logische app moet communiceren. |
| Azure Active Directory | Uitgaand | 80, 443 | VirtualNetwork | AzureActiveDirectory | |
| Verbindingsbeheer | Uitgaand | 443 | VirtualNetwork  | AppService | |
| Diagnostische logboeken & statistieken publiceren | Uitgaand | 443 | VirtualNetwork  | AzureMonitor | |
| Afhankelijkheid van Azure Storage | Uitgaand | 80, 443, 445 | VirtualNetwork | Storage | |
| Azure SQL-afhankelijkheid | Uitgaand | 1433 | VirtualNetwork | SQL | |
| Azure Resource Health | Uitgaand | 1886 | VirtualNetwork | AzureMonitor | Vereist voor het publiceren van de status van de status aan Resource Health |
| Afhankelijkheid van log naar eventhubbeleid en monitoringagent | Uitgaand | 5672 | VirtualNetwork | EventHub | |
| Azure-cache voor Redis-exemplaren tussen rolinstanties openen | Inkomend <br>Uitgaand | 6379 - 6383 | VirtualNetwork | VirtualNetwork | Als ISE met Azure Cache voor Redis wil werken, moet u deze uitgaande en binnenkomende poorten openen die [zijn beschreven in de veelgestelde vragen over Azure-cache voor Redis.](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements) |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Een ISE maken

1. Voer in de [Azure-portal](https://portal.azure.com)in het `integration service environments` belangrijkste Azure-zoekvak in als filter **de optie Integratieserviceomgevingen .**

   ![Zoeken en selecteren "Integratieserviceomgevingen"](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Selecteer Toevoegen in het deelvenster **Integratieserviceomgevingen** **.**

   ![Zoeken en selecteren "Integratieserviceomgevingen"](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Geef deze gegevens op voor uw omgeving en selecteer **Vervolgens Controleren + maken,** bijvoorbeeld:

   ![Omgevingsdetails verstrekken](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Abonnement** | Ja | <*Azure-abonnementsnaam*> | Het Azure-abonnement dat u voor uw omgeving moet gebruiken |
   | **Resourcegroep** | Ja | <*Naam azure-resource-groep*> | Een nieuwe of bestaande Azure-brongroep waarin u uw omgeving wilt maken |
   | **Naam van integratieserviceomgeving** | Ja | <*milieunaam*> | Uw ISE-naam, die alleen letters, cijfers,`-`koppeltekens`_`( ),`.`onderstreept ( ) en perioden ( ). |
   | **Locatie** | Ja | <*Azure-datacenter-regio*> | Het Azure-datacentergebied waar u uw omgeving implementeren |
   | **Sku** | Ja | **Premium** of **Ontwikkelaar (geen SLA)** | De ISE SKU om te maken en te gebruiken. Voor verschillen tussen deze SKU's, zie [ISE SKU's](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). <p><p>**Belangrijk:** deze optie is alleen beschikbaar bij ISE-creatie en kan later niet worden gewijzigd. |
   | **Extra capaciteit** | Premium: <br>Ja <p><p>Ontwikkelaar: <br>Niet van toepassing | Premium: <br>0 tot 10 <p><p>Ontwikkelaar: <br>Niet van toepassing | Het aantal extra verwerkingseenheden dat moet worden gebruikt voor deze ISE-bron. Zie [ISE-capaciteit toevoegen](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)als u capaciteit na het maken wilt toevoegen. |
   | **Toegangspunt** | Ja | **Intern** of **extern** | Het type toegangspunten dat u moet gebruiken voor uw ISE. Deze eindpunten bepalen of aanvragen of webhook-triggers op logische apps in uw ISE oproepen van buiten uw virtuele netwerk kunnen ontvangen. <p><p>Uw selectie is ook van invloed op de manier waarop u invoer en uitvoer in uw logische app bekijken en openen. Zie [ISE-eindpunttoegang voor](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)meer informatie . <p><p>**Belangrijk:** deze optie is alleen beschikbaar bij ISE-creatie en kan later niet worden gewijzigd. |
   | **Virtueel netwerk** | Ja | <*Azure-virtual-network-name*> | Het virtuele Azure-netwerk waarin u uw omgeving wilt injecteren, zodat logische apps in die omgeving toegang hebben tot uw virtuele netwerk. Als u geen netwerk hebt, [maakt u eerst een virtueel Azure-netwerk.](../virtual-network/quick-create-portal.md) <p><p>**Belangrijk:** U deze injectie *alleen* uitvoeren wanneer u uw ISE maakt. |
   | **Subnetten** | Ja | <*subnet-resource-lijst*> | Een ISE vereist vier *lege* subnetten voor het maken en implementeren van resources in uw omgeving. Als u elk subnet wilt maken, [voert u de stappen onder deze tabel uit.](#create-subnet) |
   |||||

   <a name="create-subnet"></a>

   **Subnet maken**

   Als u resources in uw omgeving wilt maken en implementeren, heeft uw ISE vier *lege* subnetten nodig die niet aan een service zijn gedelegeerd. Elk subnet ondersteunt een andere Component van Logic Apps die in uw ISE wordt gebruikt. U deze subnetadressen *niet* wijzigen nadat u uw omgeving hebt gemaakt. Elk subnet moet aan deze vereisten voldoen:

   * Heeft een naam die begint met een alfabetisch teken of een underscore (geen getallen), `\\` `?`en `/`gebruikt deze tekens niet: `<`, `>`, `%`, `&`, , , .

   * Gebruikt de [CIDR-indeling (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) en een klasse B-adresruimte.

   * Gebruikt ten `/27` minste een in de adresruimte omdat voor elk subnet minimaal 32 adressen nodig *zijn.* Bijvoorbeeld:

     * `10.0.0.0/28`heeft slechts 16 adressen en is te klein omdat 2<sup>(32-28)</sup> 2<sup>4</sup> of 16 is.

     * `10.0.0.0/27`heeft 32 adressen omdat 2<sup>(32-27)</sup> 2<sup>5</sup> of 32 is.

     * `10.0.0.0/24`heeft 256 adressen omdat 2<sup>(32-24)</sup> 2<sup>8</sup> of 256 is. Meer adressen bieden echter geen extra voordelen.

     Zie [IPv4 CIDR-blokken](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks)voor meer informatie over het berekenen van adressen.

   * Als u [ExpressRoute](../expressroute/expressroute-introduction.md)gebruikt, moet u [een routetabel maken](../virtual-network/manage-route-table.md) met de volgende route en die tabel koppelen aan elk subnet dat door uw ISE wordt gebruikt:

     **Naam**: <*routenaam*><br>
     **Adresvoorvoegsel**: 0.0.0.0/0/0<br>
     **Volgende hop**: Internet

   1. Selecteer **subnetconfiguratie beheren**onder de lijst **Subnetten** .

      ![Subnetconfiguratie beheren](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. Selecteer **Subnet**in het deelvenster **Subnetten** .

      ![Vier lege subnetten toevoegen](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. Geef deze informatie op in het **deelvenster Subnet toevoegen.**

      * **Naam:** De naam voor uw subnet
      * **Adresbereik (CIDR-blok):** het bereik van uw subnet in uw virtuele netwerk en in CIDR-indeling

      ![Subnetgegevens toevoegen](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. Wanneer u gereed bent, selecteert u **OK**.

   1. Herhaal deze stappen voor nog drie subnetten.

      > [!NOTE]
      > Als de subnetten die u probeert te maken niet geldig zijn, wordt in de Azure-portal een bericht weergegeven, maar wordt de voortgang niet geblokkeerd.

   Zie [Een virtueel netwerksubnet toevoegen](../virtual-network/virtual-network-manage-subnet.md)voor meer informatie over het maken van subnetten.

1. Nadat Azure uw ISE-gegevens heeft gevalideerd, selecteert u **Bijvoorbeeld Maken:**

   ![Na succesvolle validatie selecteert u 'Maken'](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure begint met het implementeren van uw omgeving, wat meestal binnen twee uur duurt. Af en toe kan de implementatie tot vier uur duren. Als u de implementatiestatus wilt controleren, selecteert u op uw Azure-werkbalk het pictogram meldingen dat het deelvenster Meldingen opent.

   ![Implementatiestatus controleren](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Als de implementatie is voltooid, wordt deze melding weergegeven in Azure:

   ![Implementatie geslaagd](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   Volg anders de instructies van de Azure-portal voor het oplossen van problemen.

   > [!NOTE]
   > Als de implementatie mislukt of als u uw ISE verwijdert, kan het tot een uur duren voordat azure uw subnetten vrijgeeft. Deze vertraging betekent dat u mogelijk moet wachten voordat u deze subnetten in een andere ISE opnieuw gebruikt.
   >
   > Als u uw virtuele netwerk verwijdert, duurt het over het algemeen maximaal twee uur voordat azure uw subnetten vrijgeeft, maar deze bewerking kan langer duren. 
   > Controleer bij het verwijderen van virtuele netwerken of er nog steeds geen bronnen zijn verbonden. 
   > Zie [Virtueel netwerk verwijderen](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Als u uw omgeving wilt weergeven, selecteert u **Ga naar resource** als Azure niet automatisch naar uw omgeving gaat nadat de implementatie is voltooid.

1. Zie [Uw integratieserviceomgeving beheren](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)als u de netwerkstatus voor uw ISE wilt controleren.

1. Zie [Resources toevoegen aan integratieserviceomgevingen](../logic-apps/add-artifacts-integration-service-environment-ise.md)als u logische apps en andere artefacten in uw ISE wilt maken.

   > [!IMPORTANT]
   > Beheerde ISE-connectors die beschikbaar komen nadat u uw ISE hebt gemaakt, worden niet automatisch weergegeven in de connectorkiezer in de Logic App Designer. Voordat u deze ISE-connectors gebruiken, moet u deze connectors handmatig [aan uw ISE toevoegen,](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) zodat ze worden weergegeven in de Logic App Designer.

## <a name="next-steps"></a>Volgende stappen

* [Resources toevoegen aan integratieserviceomgevingen](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Integratieserviceomgevingen beheren](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Meer informatie over [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Meer informatie over [virtuele netwerkintegratie voor Azure-services](../virtual-network/virtual-network-for-azure-services.md)
