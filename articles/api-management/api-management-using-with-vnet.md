---
title: Azure API Management gebruiken met virtuele netwerken
description: Meer informatie over het instellen van een verbinding met een virtueel netwerk in azure API Management en het verkrijgen van toegang tot webservices.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/09/2019
ms.author: apimpm
ms.openlocfilehash: cc4426ee1bb13eaf66e664c261c51f8893fdf10b
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129774"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Azure API Management gebruiken met virtuele netwerken
Met Azure Virtual Networks (VNETs) kunt u uw Azure-resources in een niet-Internet routeerbaar netwerk plaatsen waartoe u de toegang beheert. Deze netwerken kunnen vervolgens worden verbonden met uw on-premises netwerken met behulp van verschillende VPN-technologieën. Voor meer informatie over Azure Virtual Networks begint u met de informatie hier: [Overzicht van Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

Azure API Management kan worden geïmplementeerd in het virtuele netwerk (VNET), zodat het toegang heeft tot back-end-services in het netwerk. De ontwikkelaars Portal en API-gateway kunnen zodanig worden geconfigureerd dat ze toegankelijk zijn vanaf internet of alleen binnen het virtuele netwerk.

> [!NOTE]
> De URL van het importeren van het API-document moet worden gehost op een openbaar toegankelijk Internet adres.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Vereisten

Voor het uitvoeren van de stappen die in dit artikel worden beschreven, hebt u het volgende nodig:

+ Een actief Azure-abonnement.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Een APIM-exemplaar. Zie [een Azure API Management-exemplaar maken](get-started-create-service-instance.md)voor meer informatie.

## <a name="enable-vpn"> </a>VNET-verbinding inschakelen

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>VNET-connectiviteit inschakelen met behulp van de Azure Portal

1. Navigeer naar uw APIM-instantie in de [Azure Portal](https://portal.azure.com/).
2. Selecteer **Virtual Network**.
3. Configureer het API Management-exemplaar dat in een virtueel netwerk moet worden geïmplementeerd.

    ![Het menu virtueel netwerk van API Management][api-management-using-vnet-menu]
4. Selecteer het gewenste toegangs type:

   * **Uit**: dit is de standaard instelling. API Management wordt niet geïmplementeerd in een virtueel netwerk.

   * **Extern**: de API Management Gateway en de ontwikkelaars Portal zijn toegankelijk via het open bare Internet via een extern Load Balancer. De gateway kan toegang krijgen tot bronnen in het virtuele netwerk.

     ![Openbare peering][api-management-vnet-public]

   * **Intern**: de API Management Gateway en de ontwikkelaars Portal zijn alleen toegankelijk vanuit het virtuele netwerk via een interne Load Balancer. De gateway kan toegang krijgen tot bronnen in het virtuele netwerk.

     ![Persoonlijke peering][api-management-vnet-private]

     Er wordt nu een lijst weer geven met alle regio's waar uw API Management-service is ingericht. Selecteer een VNET en subnet voor elke regio. De lijst is gevuld met zowel de klassieke als de virtuele netwerken van de resource manager die beschikbaar zijn in uw Azure-abonnementen die zijn ingesteld in de regio die u configureert.

     > [!IMPORTANT]
     > Bij het implementeren van een Azure API Management-exemplaar naar een resource manager VNET, moet de service zich in een toegewijd subnet bevinden dat geen andere resources bevat, met uitzonde ring van Azure API Management-exemplaren. Als er wordt geprobeerd een Azure API Management-exemplaar te implementeren in een resource manager VNET-subnet dat andere resources bevat, mislukt de implementatie.
     >

     ![VPN selecteren][api-management-setup-vpn-select]

5. Klik op **Opslaan** in de bovenste navigatie balk.
6. Klik op **netwerk configuratie Toep assen** in de bovenste navigatie balk.

> [!NOTE]
> Het VIP-adres van het API Management exemplaar wordt gewijzigd telkens VNET wordt ingeschakeld of uitgeschakeld.
> Het VIP-adres wordt ook gewijzigd wanneer API Management wordt verplaatst van **extern** naar **intern** of vice versa
>

> [!IMPORTANT]
> Als u API Management uit een VNET verwijdert of als u de implementatie wijzigt in, kan het eerder gebruikte VNET vergrendeld blijven tot zes uur. Tijdens deze periode is het niet mogelijk om het VNET te verwijderen of om een nieuwe resource te implementeren. Dit gedrag geldt voor clients die gebruikmaken van API-versie 2018-01-01 en eerder. Clients die gebruikmaken van API-versie 2019-01-01 en hoger, het VNET wordt vrijgegeven zodra de bijbehorende API Management service wordt verwijderd.

## <a name="enable-vnet-powershell"> </a>VNET-verbinding inschakelen met Power shell-cmdlets
U kunt de VNET-connectiviteit ook inschakelen met de Power shell-cmdlets

* **Een API Management-service maken in een VNET**: Gebruik de cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) om een Azure API Management-service in een VNET te maken.

* **Een bestaande API Management-service in een VNET implementeren**: Gebruik de cmdlet [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) om een bestaande Azure API Management-service binnen een Virtual Network te verplaatsen.

## <a name="connect-vnet"> </a>Verbinding maken met een webservice die wordt gehost in een virtueel netwerk
Nadat uw API Management-service is verbonden met het VNET, is toegang tot de back-end-services binnen het netwerk niet anders dan bij het openen van open bare Services. Typ de naam van het lokale IP-adres of de hostnaam (als een DNS-server is geconfigureerd voor het VNET) van uw webservice in het veld **webservice-URL** wanneer u een nieuwe API maakt of een bestaande bewerkt.

![API van VPN toevoegen][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>Veelvoorkomende problemen met de netwerk configuratie
Hieronder vindt u een lijst met veelvoorkomende fouten die zich kunnen voordoen tijdens de implementatie van API Management-service in een Virtual Network.

* **Aangepaste installatie van DNS-server**: De API Management-service is afhankelijk van verschillende Azure-Services. Als API Management wordt gehost in een VNET met een aangepaste DNS-server, moet deze de hostnamen van deze Azure-Services omzetten. Volg [deze](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) richt lijnen voor aangepaste DNS-instellingen. Zie de onderstaande tabel poorten en andere netwerk vereisten voor naslag informatie.

> [!IMPORTANT]
> Als u van plan bent om een aangepaste DNS-server (s) te gebruiken voor het VNET, moet u deze instellen **voordat** u een API Management-service implementeert. Anders moet u de API Management-service telkens bijwerken wanneer u de DNS-server (s) wijzigt door de [bewerking netwerk configuratie Toep assen](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/ApiManagementService/ApplyNetworkConfigurationUpdates) uit te voeren

* **Poorten die vereist zijn voor de API Management**: Binnenkomend en uitgaand verkeer in het subnet waarin API Management wordt geïmplementeerd, kunnen worden beheerd met behulp van de [netwerk beveiligings groep][Network Security Group]. Als een van deze poorten niet beschikbaar is, werkt API Management mogelijk niet goed en wordt deze mogelijk niet meer toegankelijk. Als een of meer van deze poorten zijn geblokkeerd, is een ander veelvoorkomend probleem met de configuratie bij het gebruik van API Management met een VNET.

<a name="required-ports"></a> Wanneer een API Management service-exemplaar wordt gehost in een VNET, worden de poorten in de volgende tabel gebruikt.

| Bron/doel poort (en) | Direction          | Transport Protocol |   [Service Tags](../virtual-network/security-overview.md#service-tags) <br> Bron/doel   | Doel (*)                                                 | Virtual Network type |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / 80, 443                  | Inkomend            | TCP                | INTERNET-VIRTUAL_NETWORK            | Client communicatie met API Management                      | Extern             |
| * / 3443                     | Inkomend            | TCP                | ApiManagement/VIRTUAL_NETWORK       | Beheer eindpunt voor Azure Portal en Power shell         | Externe & intern  |
| * / 80, 443                  | Uitgaande           | TCP                | VIRTUAL_NETWORK/opslag             | **Afhankelijkheid van Azure Storage**                             | Externe & intern  |
| * / 80, 443                  | Uitgaande           | TCP                | VIRTUAL_NETWORK/AzureActiveDirectory | Azure Active Directory (indien van toepassing)                   | Externe & intern  |
| * / 1433                     | Uitgaande           | TCP                | VIRTUAL_NETWORK/SQL                 | **Toegang tot Azure SQL-eind punten**                           | Externe & intern  |
| * / 5672                     | Uitgaande           | TCP                | VIRTUAL_NETWORK/EventHub            | Afhankelijkheid voor logboek registratie van Event hub-beleid en bewakings agent | Externe & intern  |
| * / 445                      | Uitgaande           | TCP                | VIRTUAL_NETWORK/opslag             | Afhankelijkheid van de Azure-bestands share voor GIT                      | Externe & intern  |
| * / 1886                     | Uitgaande           | TCP                | VIRTUAL_NETWORK/INTERNET            | Vereist voor het publiceren van de status naar Resource Health          | Externe & intern  |
| */443                     | Uitgaande           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | Diagnostische logboeken en metrische gegevens publiceren                        | Externe & intern  |
| * / 25                       | Uitgaande           | TCP                | VIRTUAL_NETWORK/INTERNET            | Verbinding maken met SMTP relay voor het verzenden van e-mail berichten                    | Externe & intern  |
| */587                      | Uitgaande           | TCP                | VIRTUAL_NETWORK/INTERNET            | Verbinding maken met SMTP relay voor het verzenden van e-mail berichten                    | Externe & intern  |
| * / 25028                    | Uitgaande           | TCP                | VIRTUAL_NETWORK/INTERNET            | Verbinding maken met SMTP relay voor het verzenden van e-mail berichten                    | Externe & intern  |
| * / 6381 - 6383              | Binnenkomende &-uitgaand | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Toegang tot Azure cache voor redis-instanties tussen RoleInstances          | Externe & intern  |
| * / *                        | Inkomend            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Load Balancer van Azure-infra structuur                          | Externe & intern  |

>[!IMPORTANT]
> De poorten waarvoor het *doel* **vet** is, zijn vereist voor het implementeren van API Management service. Als u de andere poorten blokkeert, zal dit echter de mogelijkheid hebben om de actieve service te gebruiken en te bewaken.

+ **SSL-functionaliteit**: Om het maken en valideren van een SSL-certificaat keten in te scha kelen, heeft de API Management-service een uitgaande netwerk verbinding nodig met ocsp.msocsp.com, mscrl.microsoft.com en crl.microsoft.com. Deze afhankelijkheid is niet vereist als een certificaat dat u uploadt naar API Management de volledige keten naar de basis van de CA bevatten.

+ **DNS-toegang**: Uitgaande toegang op poort 53 is vereist voor communicatie met DNS-servers. Als er een aangepaste DNS-server bestaat aan het andere uiteinde van een VPN-gateway, moet de DNS-server bereikbaar zijn vanaf het subnet dat als host fungeert voor de API Management.

+ **Metrische gegevens en status controle**: Uitgaande netwerk verbinding met Azure monitoring-eind punten die worden omgezet in de volgende domeinen:

    | Azure-omgeving | Eindpunten                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Open bare Azure      | <ul><li>prod.warmpath.msftcloudes.com</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li><li>prod3-black.prod3.metrics.nsatc.net</li><li>prod3-red.prod3.metrics.nsatc.net</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`azure region`. warm.ingestion.msftcloudes.com waarbij `East US 2` eastus2.warm.ingestion.msftcloudes.com is</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |
    | Azure China       | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |

+ **SMTP-relay**: Uitgaande netwerk verbinding voor de SMTP-relay, die wordt omgezet onder de `smtpi-co1.msn.com`host `smtpi-ch1.msn.com` `smtpi-db3.msn.com`, `smtpi-sin.msn.com` , en`ies.global.microsoft.com`

+ **Ontwikkelaars Portal CAPTCHA**: Uitgaande netwerk verbinding voor de CAPTCHA van de ontwikkelaars Portal, die wordt omgezet onder de `client.hip.live.com` hosts `partner.hip.live.com`en.

+ **Azure Portal diagnostische gegevens**: Als u de stroom van Diagnostische logboeken van Azure Portal wilt inschakelen wanneer u de API Management extensie van binnen een Virtual Network `dc.services.visualstudio.com` gebruikt, is uitgaande toegang tot op poort 443 vereist. Dit helpt bij het oplossen van problemen die u kunt tegen komen wanneer u uitbrei ding gebruikt.

+ **Verkeer naar een on-premises firewall forceren met behulp van Express route of een virtueel netwerk apparaat**: Een veelvoorkomende klant configuratie is het definiëren van een eigen standaard route (0.0.0.0/0) die ervoor zorgt dat alle verkeer van het API Management overgedragen subnet wordt getransporteerd door een on-premises firewall of een virtueel netwerk apparaat. Deze verkeers stroom invariably verbreekt de connectiviteit met Azure API Management, omdat het uitgaande verkeer on-premises of NAT zou worden geblokkeerd door een niet-herken bare set adressen die niet meer werken met verschillende Azure-eind punten. Voor de oplossing moet u een aantal dingen doen:

  * Schakel service-eind punten in op het subnet waarin de API Management-service is geïmplementeerd. [Service-eind punten][ServiceEndpoints] moeten worden ingeschakeld voor Azure Sql, Azure Storage, Azure EventHub en Azure ServiceBus. Als u eind punten rechtstreeks vanuit API Management overgedragen subnet naar deze services inschakelt, kunnen ze het Microsoft Azure backbone-netwerk gebruiken dat een optimale route ring voor service verkeer biedt. Als u service-eind punten gebruikt met een API-beheer met geforceerde tunneling, wordt het bovenstaande verkeer van Azure-Services niet geforceerd getunneld. Het andere API Management verkeer met Service afhankelijkheid wordt geforceerd getunneld en kan niet verloren gaan of de API Management-service werkt niet goed.
    
  * Alle verkeer van het besturings element van Internet naar het beheer eindpunt van uw API Management-service wordt doorgestuurd via een specifieke set inkomende IP-adressen die worden gehost door API Management. Wanneer het verkeer geforceerde tunneling is, worden de antwoorden niet symmetrisch toegewezen aan deze IP-adressen voor binnenkomende bronnen. Om de beperking te overwinnen, moeten we de volgende door de gebruiker gedefinieerde routes ([udr's][UDRs]) toevoegen aan het stuur verkeer terug naar Azure door het doel van deze hostroutes in te stellen op internet. De set met inkomende Ip's voor besturings vlak verkeer is als volgt:
    
     | Azure-omgeving | IP-adressen van beheer                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Open bare Azure      | 13.84.189.17/32, 13.85.22.63/32, 23.96.224.175/32, 23.101.166.38/32, 52.162.110.80/32, 104.214.19.224/32, 52.159.16.255/32, 40.82.157.167/32, 51.137.136.0/32, 40.81.185.8/32, 40.81.47.216/32, 51.145.56.125/32, 40.81.89.24/32 52.224.186.99/32, 51.145.179.78/32, 52.140.238.179/32, 40.66.60.111/32, 52.139.80.117/32, 20.46.144.85/32, 191.233.24.179/32, 40.90.185.46/32, 102.133.130.197/32, 52.139.20.34/32, 40.80.232.185/32, 13.71.49.1/32, 13.64.39.16/32 20.40.160.107/32, 20.37.52.67/32, 20.44.33.246/32, 13.86.102.66/32, 20.40.125.155/32, 51.143.127.203/32, 52.253.225.124/32, 52.253.159.160/32, 20.188.77.119/32, 20.44.72.3/32, 52.142.95.35/32, 52.139.152.27/32, 20.39.80.2/32, 51.107.96.8 32, 20.39.99.81/32, 20.37.81.41/32, 51.107.0.91/32, 102.133.0.79/32, 51.116.96.0/32, 51.116.0.0/32 |
    | Azure Government  | 52.127.42.160/32, 52.127.34.192/32 |
    | Azure China       | 139.217.51.16/32, 139.217.171.176/32 |

  * Voor andere API Management-service afhankelijkheden die geforceerde tunnels zijn, moet er een manier zijn om de hostnaam op te lossen en te bereiken aan het eind punt. Dit zijn onder andere
      - Metrische gegevens en status controle
      - Diagnostische gegevens Azure Portal
      - SMTP-relay
      - Ontwikkelaars Portal CAPTCHA

## <a name="troubleshooting"> </a>Problemen oplossen
* **Eerste installatie**: Wanneer de eerste implementatie van API Management service in een subnet mislukt, wordt u geadviseerd om eerst een virtuele machine in hetzelfde subnet te implementeren. Volgende extern bureau blad in de virtuele machine en controleren of er verbinding is met een van de onderstaande resources in uw Azure-abonnement
    * Azure Storage BLOB
    * Azure SQL Database
    * Azure Storage tabel

  > [!IMPORTANT]
  > Nadat u de connectiviteit hebt gevalideerd, moet u alle resources die in het subnet zijn geïmplementeerd, verwijderen voordat u API Management in het subnet implementeert.

* **Incrementele updates**: Als u wijzigingen aanbrengt in uw netwerk, raadpleegt u de [NetworkStatus-API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/networkstatus)om te controleren of de API Management-service geen toegang meer heeft tot een van de kritieke resources waarvan deze afhankelijk is. De connectiviteits status moet elke 15 minuten worden bijgewerkt.

* **Koppelingen voor bronnen navigatie**: Wanneer u implementeert in het Resource Manager-type vnet-subnet, wordt API Management het subnet gereserveerd door een resource navigatie koppeling te maken. Als het subnet al een resource van een andere provider bevat, **mislukt**de implementatie. En wanneer u een API Management-service naar een ander subnet verplaatst of verwijdert, worden de navigatie koppeling naar die resource verwijderd.

## <a name="subnet-size"></a> Vereiste voor de grootte van het subnet
Azure reserveert een aantal IP-adressen binnen elk subnet en deze adressen kunnen niet worden gebruikt. Het eerste en laatste IP-adres van de subnetten zijn gereserveerd voor protocol conformiteit, samen met drie meer adressen die worden gebruikt voor Azure-Services. Zie [zijn er beperkingen voor het gebruik van IP-adressen in deze subnetten?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) voor meer informatie.

Naast de IP-adressen die worden gebruikt door de Azure VNET-infra structuur, maakt elk API Management-exemplaar in het subnet gebruik van twee IP-adressen per eenheid van Premium SKU of één IP-adres voor de Developer-SKU. Elk exemplaar reserveert een extra IP-adres voor de externe load balancer. Bij implementatie in een intern vnet is een extra IP-adres voor de interne load balancer vereist.

Gezien de berekening boven de minimale grootte van het subnet, waarin API Management kan worden geïmplementeerd is/29, waardoor drie IP-adressen worden geleverd.

## <a name="routing"> </a> Routing
+ Een openbaar IP-adres (VIP) met gelijke taak verdeling wordt gereserveerd om toegang te bieden tot alle service-eind punten.
+ Een IP-adres uit een subnet IP-bereik (DIP) wordt gebruikt voor toegang tot bronnen binnen het vnet en een openbaar IP-adres (VIP) wordt gebruikt voor toegang tot bronnen buiten het vnet.
+ Het open bare IP-adres met taak verdeling vindt u op de Blade Overzicht/essentiële elementen in het Azure Portal.

## <a name="limitations"> </a>Beperkingen
* Een subnet met API Management exemplaren kan geen andere Azure-resource typen bevatten.
* Het subnet en de API Management-service moeten zich in hetzelfde abonnement bevinden.
* Een subnet met API Management exemplaren kan niet worden verplaatst voor abonnementen.
* Voor API Management implementaties in meerdere regio's die in de interne virtuele netwerk modus zijn geconfigureerd, zijn gebruikers verantwoordelijk voor het beheer van de taak verdeling over meerdere regio's, aangezien ze eigenaar zijn van de route ring.
* De connectiviteit van een bron in een globaal gepeerd VNET in een andere regio naar API Management service in de interne modus werkt niet als gevolg van de platform beperking. Zie [resources in één virtueel netwerk kunnen niet communiceren met interne Load Balancer van Azure in een peered virtueel netwerk](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) voor meer informatie


## <a name="related-content"> </a>Gerelateerde inhoud
* [Een Virtual Network met back-end verbinden via een VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [Een Virtual Network van verschillende implementatie modellen verbinden](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [De API-Inspector gebruiken voor het traceren van aanroepen in azure API Management](api-management-howto-api-inspector.md)
* [Veelgestelde vragen over Virtual Network](../virtual-network/virtual-networks-faq.md)
* [Service Tags](../virtual-network/security-overview.md#service-tags)

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
