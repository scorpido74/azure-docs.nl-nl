---
title: Azure API Management gebruiken met virtuele netwerken
description: Meer informatie over het instellen van een verbinding met een virtueel netwerk in Azure API Management en toegang tot webservices via het netwerk.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/09/2020
ms.author: apimpm
ms.openlocfilehash: dcc2c38238f707a5d43cde03502c589add9461b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335929"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Azure API Management gebruiken met virtuele netwerken
Met Azure Virtual Networks (VNETs) kunt u uw Azure-resources in een routeerbaar netwerk (buiten internet) plaatsen waarvan u de toegang beheert. Deze netwerken kunnen vervolgens worden verbonden met uw on-premises netwerken met behulp van verschillende VPN-technologieën. Begin met de informatie hier om meer te weten te komen over Azure Virtual Networks: [Azure Virtual Network Overview](../virtual-network/virtual-networks-overview.md).

Azure API Management kan worden geïmplementeerd binnen het virtuele netwerk (VNET), zodat het toegang heeft tot backendservices binnen het netwerk. De ontwikkelaarsportal en API-gateway kunnen worden geconfigureerd om toegankelijk te zijn vanaf het internet of alleen binnen het virtuele netwerk.

> [!NOTE]
> De URL van het API-importdocument moet worden gehost op een openbaar toegankelijk internetadres.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Vereisten

Als u de in dit artikel beschreven stappen wilt uitvoeren, moet u het volgende hebben:

+ Een actief Azure-abonnement.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Een APIM-exemplaar. Zie [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md)voor meer informatie.

## <a name="enable-vnet-connection"></a><a name="enable-vpn"> </a>VNET-verbinding inschakelen

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>VNET-connectiviteit inschakelen met de Azure-portal

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw API-beheerexemplaar te vinden. Zoeken naar **API-beheerservices**en selecteer deze .

2. Kies uw API-beheerexemplaar.

3. Selecteer **Virtueel netwerk**.
4. Configureer de instantie API-beheer die moet worden geïmplementeerd in een virtueel netwerk.

    ![Virtueel netwerkmenu van API-beheer][api-management-using-vnet-menu]
5. Selecteer het gewenste toegangstype:

    * **Uit:** Dit is de standaardinstelling. API-beheer wordt niet geïmplementeerd in een virtueel netwerk.

    * **Extern**: De API Management gateway en developer portal zijn toegankelijk vanaf het openbare internet via een externe load balancer. De gateway heeft toegang tot bronnen binnen het virtuele netwerk.

        ![Openbare peering][api-management-vnet-public]

    * **Intern**: De API Management gateway en developer portal zijn alleen toegankelijk vanuit het virtuele netwerk via een interne load balancer. De gateway heeft toegang tot bronnen binnen het virtuele netwerk.

        ![Persoonlijke peering][api-management-vnet-private]

6. Als u **Extern** of **intern hebt**geselecteerd, ziet u een lijst met alle regio's waar uw API-beheerservice is ingericht. Kies een **locatie**en kies vervolgens het **virtuele netwerk** en **subnet**. De lijst met virtuele netwerken wordt gevuld met zowel klassieke als Resource Manager virtuele netwerken die beschikbaar zijn in uw Azure-abonnementen die zijn ingesteld in de regio die u configureert.

    > [!IMPORTANT]
    > Wanneer u een Azure API Management-exemplaar implementeert in een VNET voor Resourcebeheer, moet de service zich in een speciaal subnet bevinden dat geen andere resources bevat, behalve azure API Management-exemplaren. Als een poging wordt gedaan om een Azure API Management-instantie te implementeren in een VNET-subnet voor Resource Beheer dat andere bronnen bevat, mislukt de implementatie.

    Selecteer vervolgens **Toepassen**. De **virtuele netwerkpagina** van uw API-beheerexemplaar wordt bijgewerkt met uw nieuwe virtuele netwerk- en subnetkeuzes.

    ![Selecteer VPN][api-management-setup-vpn-select]

7. Selecteer op de bovenste navigatiebalk **Opslaan**en selecteer **Vervolgens Netwerkconfiguratie toepassen**.

> [!NOTE]
> Het VIP-adres van de instantie API-beheer verandert telkens wanneer VNET is ingeschakeld of uitgeschakeld.
> Het VIP-adres wordt ook gewijzigd wanneer API-beheer wordt verplaatst van **Extern** naar **intern**of vice versa.
>

> [!IMPORTANT]
> Als u API-beheer uit een VNET verwijdert of het beheer ervan wijzigt, kan het eerder gebruikte VNET maximaal zes uur vergrendeld blijven. Gedurende deze periode is het niet mogelijk om de VNET te verwijderen of er een nieuwe bron op te implementeren. Dit gedrag geldt voor clients die api-versie 2018-01-01 en eerder gebruiken. Clients die gebruik maken van api-versie 2019-01-01 en hoger, de VNET wordt vrijgemaakt zodra de bijbehorende API Management-service is verwijderd.

## <a name="enable-vnet-connection-using-powershell-cmdlets"></a><a name="enable-vnet-powershell"> </a>VNET-verbinding inschakelen met PowerShell-cmdlets
U vnet-connectiviteit ook inschakelen met de PowerShell-cmdlets.

* **Maak een API Management-service in een VNET:** gebruik de cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) om een Azure API Management-service te maken in een VNET.

* **Een bestaande API Management-service implementeren in een VNET:** gebruik de cmdlet [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) om een bestaande Azure API Management-service binnen een virtueel netwerk te verplaatsen.

## <a name="connect-to-a-web-service-hosted-within-a-virtual-network"></a><a name="connect-vnet"> </a>Verbinding maken met een webservice die wordt gehost binnen een virtueel netwerk
Nadat uw API Management-service is verbonden met de VNET, is toegang tot backendservices binnen het servicenummer niet anders dan toegang tot openbare services. Typ gewoon het lokale IP-adres of de hostnaam (als een DNS-server is geconfigureerd voor de VNET) van uw webservice in het veld URL van de **webservice** wanneer u een nieuwe API maakt of een bestaande server bewerkt.

![API toevoegen vanaf VPN][api-management-setup-vpn-add-api]

## <a name="common-network-configuration-issues"></a><a name="network-configuration-issues"> </a>Veelvoorkomende problemen met netwerkconfiguratie
Hieronder volgt een lijst met veelvoorkomende foutieve configuratieproblemen die kunnen optreden tijdens het implementeren van api-beheerservice in een virtueel netwerk.

* **Aangepaste DNS-serverinstelling**: De API-beheerservice is afhankelijk van verschillende Azure-services. Wanneer API Management wordt gehost in een VNET met een aangepaste DNS-server, moet het de hostnamen van die Azure-services oplossen. Volg [deze](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) richtlijnen voor aangepaste DNS-installatie. Zie de onderstaande tabel poorten en andere netwerkvereisten voor referentie.

> [!IMPORTANT]
> Als u van plan bent een aangepaste DNS-server(s) te gebruiken voor het VNET, moet u deze instellen **voordat** u er een API Management-service in implementeert. Anders moet u de API Management-service bijwerken telkens wanneer u de DNS-server(s) wijzigt door de [bewerking Netwerkconfiguratie toepassen](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/ApiManagementService/ApplyNetworkConfigurationUpdates) uit te voeren

* **Poorten die nodig zijn voor API-beheer:** Binnenkomend en uitgaand verkeer naar het subnet waarin API-beheer is geïmplementeerd, kunnen worden beheerd met behulp van [Network Security Group][Network Security Group]. Als een van deze poorten niet beschikbaar is, werkt API-beheer mogelijk niet goed en kan het ontoegankelijk worden. Het blokkeren van een of meer van deze poorten is een ander veelvoorkomend foutconfiguratieprobleem bij het gebruik van API-beheer met een VNET.

<a name="required-ports"> </a> Wanneer een API Management-serviceinstantie wordt gehost in een VNET, worden de poorten in de volgende tabel gebruikt.

| Bron / bestemmingspoort(en) | Richting          | Transportprotocol |   [Servicetags](../virtual-network/security-overview.md#service-tags) <br> Bron / Bestemming   | Doel (*)                                                 | Type virtueel netwerk |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / [80], 443                  | Inkomend            | TCP                | INTERNET / VIRTUAL_NETWORK            | Clientcommunicatie naar API-beheer                      | Extern             |
| * / 3443                     | Inkomend            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Eindpunt beheer voor Azure-portal en Powershell         | Extern & Intern  |
| * / 443                  | Uitgaand           | TCP                | VIRTUAL_NETWORK / Opslag             | **Afhankelijkheid van Azure Storage**                             | Extern & Intern  |
| * / 443                  | Uitgaand           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | [Azure Active Directory](api-management-howto-aad.md) (indien van toepassing)                   | Extern & Intern  |
| * / 1433                     | Uitgaand           | TCP                | VIRTUAL_NETWORK / SQL                 | **Toegang tot Azure SQL-eindpunten**                           | Extern & Intern  |
| * / 5671, 5672, 443          | Uitgaand           | TCP                | VIRTUAL_NETWORK / EventHub            | Afhankelijkheid voor beleid en monitoringsagent [log-naar-gebeurtenishub](api-management-howto-log-event-hubs.md) | Extern & Intern  |
| * / 445                      | Uitgaand           | TCP                | VIRTUAL_NETWORK / Opslag             | Afhankelijkheid van Azure File Share voor [GIT](api-management-configuration-repository-git.md)                      | Extern & Intern  |
| * / 1886                     | Uitgaand           | TCP                | VIRTUAL_NETWORK / AzureCloud            | Nodig om de status van gezondheid te publiceren naar resourcestatus          | Extern & Intern  |
| * / 443                     | Uitgaand           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | [Diagnostische logboeken en statistieken](api-management-howto-use-azure-monitor.md) publiceren                       | Extern & Intern  |
| * / 25                       | Uitgaand           | TCP                | VIRTUAL_NETWORK / INTERNET            | Verbinding maken met SMTP Relay voor het verzenden van e-mails                    | Extern & Intern  |
| * / 587                      | Uitgaand           | TCP                | VIRTUAL_NETWORK / INTERNET            | Verbinding maken met SMTP Relay voor het verzenden van e-mails                    | Extern & Intern  |
| * / 25028                    | Uitgaand           | TCP                | VIRTUAL_NETWORK / INTERNET            | Verbinding maken met SMTP Relay voor het verzenden van e-mails                    | Extern & Intern  |
| * / 6381 - 6383              | Binnenkomende & Uitgaand | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Toegang tot Redis Service voor [tarieflimietbeleid](api-management-access-restriction-policies.md#LimitCallRateByKey) tussen machines         | Extern & Intern  |
| * / *                        | Inkomend            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Azure Infrastructure Load Balancer                          | Extern & Intern  |

>[!IMPORTANT]
> De poorten waarvoor het *doel* **vet is,** zijn vereist om de API-beheerservice succesvol te kunnen implementeren. Het blokkeren van de andere poorten zal echter leiden tot degradatie in de mogelijkheid om de lopende service te gebruiken en te controleren.

+ **TLS-functionaliteit**: Om TLS/SSL-certificaatketenbuilding en -validatie in te schakelen, heeft de API Management-service uitgaande netwerkconnectiviteit nodig om te ocsp.msocsp.com, mscrl.microsoft.com en crl.microsoft.com. Deze afhankelijkheid is niet vereist, als een certificaat dat u uploadt naar API Management de volledige keten naar de CA-root bevat.

+ **DNS-toegang:** Uitgaande toegang op poort 53 is vereist voor communicatie met DNS-servers. Als er een aangepaste DNS-server bestaat aan de andere kant van een VPN-gateway, moet de DNS-server bereikbaar zijn via het subnet hosting API Management.

+ **Metrische gegevens en statusbewaking:** uitgaande netwerkconnectiviteit met Azure Monitoring-eindpunten, die worden opgelost onder de volgende domeinen:

    | Azure-omgeving | Eindpunten                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Openbare Azure-peering      | <ul><li>gcs.prod.monitoring.core.windows.net(**nieuw**)</li><li>prod.warmpath.msftcloudes.com(**te deprecated**)</li><li>shoebox2.metrics.microsoftmetrics.com(**nieuw**)</li><li>shoebox2.metrics.nsatc.net(**te deprecated**)</li><li>prod3.metrics.microsoftmetrics.com(**nieuw**)</li><li>prod3.metrics.nsatc.net(**te deprecated**)</li><li>prod3-black.prod3.metrics.microsoftmetrics.com(**nieuw**)</li><li>prod3-black.prod3.metrics.nsatc.net(**te deprecated**)</li><li>prod3-red.prod3.metrics.microsoftmetrics.com(**nieuw**)</li><li>prod3-red.prod3.metrics.nsatc.net(**te deprecated**)</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`azure region`.warm.ingestion.msftcloudes.com `East US 2` waar is eastus2.warm.ingestion.msftcloudes.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.microsoftmetrics.com(**nieuw**)</li><li>shoebox2.metrics.nsatc.net(**te deprecated**)</li><li>prod3.metrics.microsoftmetrics.com(**nieuw**)</li><li>prod3.metrics.nsatc.net(**te deprecated**)</li><li>prod5.prod.microsoftmetrics.com</li></ul>                                                                                                                                                                                                                                                |
    | Azure China 21Vianet     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.microsoftmetrics.com(**nieuw**)</li><li>shoebox2.metrics.nsatc.net(**te deprecated**)</li><li>prod3.metrics.microsoftmetrics.com(**nieuw**)</li><li>prod3.metrics.nsatc.net(**te deprecated**)</li><li>prod5.prod.microsoftmetrics.com</li></ul>                                                                                                                                                                                                                                                |

>[!IMPORTANT]
> De wijziging van clusters hierboven met dns-zone **.nsatc.net** naar **.microsoftmetrics.com** is meestal een DNS-wijziging. IP-adres van het cluster wordt niet gewijzigd.

+ **SMTP Relay**: Uitgaande netwerkconnectiviteit voor het SMTP-relais, `smtpi-co1.msn.com` `smtpi-ch1.msn.com`dat `smtpi-db3.msn.com` `smtpi-sin.msn.com` oplost onder de host , , en`ies.global.microsoft.com`

+ **Developer portal CAPTCHA:** Uitgaande netwerkconnectiviteit voor captcha van de ontwikkelaarsportal, `client.hip.live.com` `partner.hip.live.com`die oplost onder de hosts en .

+ **Azure-portaldiagnostics:** Om de stroom van diagnostische logboeken vanuit Azure-portal in te schakelen `dc.services.visualstudio.com` wanneer u de API-beheerextensie gebruikt vanuit een virtueel netwerk, is uitgaande toegang tot poort 443 vereist. Dit helpt bij het oplossen van problemen waarmee u te maken krijgen bij het gebruik van extensie.

+ **Tunneling Traffic to On-prem Firewall Using Express Route or Network Virtual Appliance**: Een veelgebruikte klantconfiguratie is het definiëren van hun eigen standaardroute (0.0.0.0/0) die al het verkeer van het gedelegeerde subnet api-beheer dwingt om door een on-premises firewall of naar een virtueel netwerktoestel te stromen. Deze verkeersstroom breekt steevast de verbinding met Azure API Management omdat het uitgaande verkeer on-premises wordt geblokkeerd of NAT'd naar een onherkenbare set adressen die niet langer werken met verschillende Azure-eindpunten. De oplossing vereist dat u een paar dingen te doen:

  * Serviceeindpunten inschakelen op het subnet waarin de API-beheerservice is geïmplementeerd. [Serviceeindpunten][ServiceEndpoints] moeten zijn ingeschakeld voor Azure Sql, Azure Storage, Azure EventHub en Azure ServiceBus. Door eindpunten rechtstreeks in te schakelen van het gedelegeerdsubnet van API Management naar deze services, kunnen ze het Microsoft Azure-backbonenetwerk gebruiken dat optimale routering biedt voor serviceverkeer. Als u Service-eindpunten gebruikt met een geforceerd api-beheer met een ondertunneling, wordt het bovenstaande Azure-servicesverkeer niet geforceerd getunneld. Het andere afhankelijkheidsverkeer van apibeheerservice wordt gedwongen getunneld en kan niet verloren gaan of de API-beheerservice zou niet goed functioneren.
    
  * Al het controlevlakverkeer van internet naar het beheereindpunt van uw API-beheerservice wordt doorgestuurd via een specifieke set inkomende IP's die worden gehost door API-beheer. Wanneer het verkeer wordt gedwongen tunneled de reacties zal niet symmetrisch terug te sturen naar deze Inbound bron IP's. Om de beperking te overwinnen, moeten we de volgende door de gebruiker gedefinieerde routes[(UDR's)][UDRs]toevoegen om verkeer terug te sturen naar Azure door de bestemming van deze hostroutes in te stellen op 'Internet'. De set inkomende IP's voor controle Vliegtuigverkeer is gedocumenteerd [Control Plane IP-adressen](#control-plane-ips)

  * Voor andere api-beheerserviceafhankelijkheden die met kracht worden getunneld, moet er een manier zijn om de hostnaam op te lossen en het eindpunt te bereiken. Deze omvatten
      - Statistieken en gezondheidsmonitoring
      - Azure-portaldiagnostiek
      - SMTP-relais
      - Ontwikkelaarsportal CAPTCHA

## <a name="troubleshooting"></a><a name="troubleshooting"> </a>Probleemoplossing
* **Initiële installatie:** Wanneer de eerste implementatie van API Management-service in een subnet niet slaagt, wordt geadviseerd om eerst een virtuele machine in hetzelfde subnet te implementeren. Volgende externe desktop in de virtuele machine en valideren dat er connectiviteit is met een van elke bron hieronder in uw Azure-abonnement
    * Blob azure-opslag
    * Azure SQL Database
    * Azure-opslagtabel

  > [!IMPORTANT]
  > Nadat u de connectiviteit hebt gevalideerd, moet u alle bronnen verwijderen die in het subnet zijn geïmplementeerd, voordat u API-beheer in het subnet implementeert.

* **Incrementele updates:** Wanneer u wijzigingen aanbrengt in uw netwerk, raadpleegt u [de NetworkStatus API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/networkstatus)om te controleren of de API-beheerservice geen toegang heeft verloren tot een van de kritieke bronnen, waarvan deze afhankelijk is. De verbindingsstatus moet elke 15 minuten worden bijgewerkt.

* **Resourcenavigatiekoppelingen:** Wanneer api-beheer het subnet subnet in resourcebeheerstijl implementeert, reserveert apibeheer het subnet door een koppeling voor resourcenavigatie te maken. Als het subnet al een bron van een andere provider bevat, **mislukt**de implementatie . Wanneer u een API Management-service naar een ander subnet verplaatst of verwijdert, verwijderen we die koppeling met resourcenavigatie.

## <a name="subnet-size-requirement"></a><a name="subnet-size"> </a> Vereiste subnetgrootte
Azure reserveert een aantal IP-adressen binnen elk subnet en deze adressen kunnen niet worden gebruikt. De eerste en laatste IP-adressen van de subnetten zijn gereserveerd voor protocolconformiteit, samen met nog drie adressen die worden gebruikt voor Azure-services. Zie [Zijn er beperkingen voor het gebruik van IP-adressen binnen deze subnetten?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Naast de IP-adressen die worden gebruikt door de Azure VNET-infrastructuur, gebruikt elke Api-beheerinstantie in het subnet twee IP-adressen per eenheid Premium SKU of één IP-adres voor de Ontwikkelaar SKU. Elke instantie reserveert een extra IP-adres voor de externe load balancer. Bij het implementeren in het interne virtuele netwerk is een extra IP-adres nodig voor de interne load balancer.

Gezien de berekening hierboven de minimale grootte van het subnet, waarin API Management kan worden ingezet is / 29 dat drie bruikbare IP-adressen geeft.

Elke extra schaaleenheid van API-beheer vereist nog twee IP-adressen.

## <a name="routing"></a><a name="routing"> </a> Routering
+ Een laadgebalanceerd openbaar IP-adres (VIP) wordt gereserveerd om toegang te bieden tot alle serviceeindpunten.
+ Een IP-adres uit een subnet IP-bereik (DIP) zal worden gebruikt om toegang te krijgen tot bronnen binnen het vnet en een openbaar IP-adres (VIP) zal worden gebruikt om toegang te krijgen tot bronnen buiten het vnet.
+ Het gebalanceerde openbare IP-adres laden is te vinden op het blade Overzicht/Essentials in de Azure-portal.

## <a name="limitations"></a><a name="limitations"> </a>Beperkingen
* Een subnet met API-beheerinstanties mag geen andere Azure-brontypen bevatten.
* Het subnet en de API Management-service moeten in hetzelfde abonnement zitten.
* Een subnet met API Management-exemplaren kan niet over abonnementen worden verplaatst.
* Voor API-beheerimplementaties met meerdere regio's die zijn geconfigureerd in de interne virtuele netwerkmodus, zijn gebruikers verantwoordelijk voor het beheer van de taakverdeling in meerdere regio's, omdat ze eigenaar zijn van de routering.
* Connectiviteit van een resource in een wereldwijd peered VNET in een andere regio naar API Management-service in interne modus werkt niet vanwege platformbeperking. Zie [Resources in één virtueel netwerk kan niet communiceren met Azure internal load balancer in een virtueel netwerk met peered.](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)

## <a name="control-plane-ip-addresses"></a><a name="control-plane-ips"> </a> IP-adressen van het controlevlak

De IP-adressen worden gedeeld door **azure-omgeving.** Bij het toestaan van binnenkomende aanvragen moet IP-adres dat is gemarkeerd met **Globaal** op de witte lijst worden gebracht, samen met het specifieke IP-adres **regio.**

| **Azure-omgeving**|   **Regio**|  **IP-adres**|
|-----------------|-------------------------|---------------|
| Openbare Azure-peering| South Central US (Global)| 104.214.19.224|
| Openbare Azure-peering| North Central US (Global)| 52.162.110.80|
| Openbare Azure-peering| VS - west-centraal| 52.253.135.58|
| Openbare Azure-peering| Korea - centraal| 40.82.157.167|
| Openbare Azure-peering| Verenigd Koninkrijk West| 51.137.136.0|
| Openbare Azure-peering| Japan - west| 40.81.185.8|
| Openbare Azure-peering| VS - noord-centraal| 40.81.47.216|
| Openbare Azure-peering| Verenigd Koninkrijk Zuid| 51.145.56.125|
| Openbare Azure-peering| India - west| 40.81.89.24|
| Openbare Azure-peering| VS - oost| 52.224.186.99|
| Openbare Azure-peering| Europa -west| 51.145.179.78|
| Openbare Azure-peering| Japan - oost| 52.140.238.179|
| Openbare Azure-peering| Frankrijk - centraal| 40.66.60.111|
| Openbare Azure-peering| Canada - oost| 52.139.80.117|
| Openbare Azure-peering| VAE Noord| 20.46.144.85|
| Openbare Azure-peering| Brazilië - zuid| 191.233.24.179|
| Openbare Azure-peering| Azië - zuidoost| 40.90.185.46|
| Openbare Azure-peering| Zuid-Afrika Noord| 102.133.130.197|
| Openbare Azure-peering| Canada - midden| 52.139.20.34|
| Openbare Azure-peering| Korea - zuid| 40.80.232.185|
| Openbare Azure-peering| India - centraal| 13.71.49.1|
| Openbare Azure-peering| VS - west| 13.64.39.16|
| Openbare Azure-peering| Australië - zuidoost| 20.40.160.107|
| Openbare Azure-peering| Australië - centraal| 20.37.52.67|
| Openbare Azure-peering| India - zuid| 20.44.33.246|
| Openbare Azure-peering| VS - centraal| 13.86.102.66|
| Openbare Azure-peering| Australië - oost| 20.40.125.155|
| Openbare Azure-peering| VS - west 2| 51.143.127.203|
| Openbare Azure-peering| Oost-VS 2 EUAP| 52.253.229.253|
| Openbare Azure-peering| Centrale EUAP van de VS| 52.253.159.160|
| Openbare Azure-peering| VS - zuid-centraal| 20.188.77.119|
| Openbare Azure-peering| VS - oost 2| 20.44.72.3|
| Openbare Azure-peering| Europa - noord| 52.142.95.35|
| Openbare Azure-peering| Azië - oost| 52.139.152.27|
| Openbare Azure-peering| Frankrijk - zuid| 20.39.80.2|
| Openbare Azure-peering| Zwitserland West| 51.107.96.8|
| Openbare Azure-peering| Australië - centraal 2| 20.39.99.81|
| Openbare Azure-peering| VAE Centraal| 20.37.81.41|
| Openbare Azure-peering| Zwitserland Noord| 51.107.0.91|
| Openbare Azure-peering| Zuid-Afrika West| 102.133.0.79|
| Openbare Azure-peering| Duitsland West Centraal| 51.116.96.0|
| Openbare Azure-peering| Duitsland Noord| 51.116.0.0|
| Openbare Azure-peering| Noorwegen-Oost| 51.120.2.185|
| Openbare Azure-peering| Noorwegen West| 51.120.130.134|
| Azure China 21Vianet| China North (Wereldwijd)| 139.217.51.16|
| Azure China 21Vianet| China East (Wereldwijd)| 139.217.171.176|
| Azure China 21Vianet| China - noord| 40.125.137.220|
| Azure China 21Vianet| China East| 40.126.120.30|
| Azure China 21Vianet| China Noord 2| 40.73.41.178|
| Azure China 21Vianet| China Oost 2| 40.73.104.4|
| Azure Government| USGov Virginia (Wereldwijd)| 52.127.42.160|
| Azure Government| USGov Texas (Global)| 52.127.34.192|
| Azure Government| USGov - Virginia| 52.227.222.92|
| Azure Government| USGov Iowa| 13.73.72.21|
| Azure Government| USGov Arizona| 52.244.32.39|
| Azure Government| USGov - Texas| 52.243.154.118|
| Azure Government| USDoD Centraal| 52.182.32.132|
| Azure Government| USDoD Oost| 52.181.32.192|

## <a name="related-content"></a><a name="related-content"> </a>Gerelateerde inhoud
* [Een virtueel netwerk verbinden met backend via Vpn-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [Een virtueel netwerk verbinden vanuit verschillende implementatiemodellen](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Api-controle gebruiken om oproepen in Azure API-beheer te traceren](api-management-howto-api-inspector.md)
* [Virtueel netwerk veelgestelde vragen](../virtual-network/virtual-networks-faq.md)
* [Servicetags](../virtual-network/security-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/security-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/security-overview.md#service-tags
