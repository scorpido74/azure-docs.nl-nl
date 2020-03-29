---
title: Azure API-beheer gebruiken met interne virtuele netwerken
titleSuffix: Azure API Management
description: Meer informatie over het instellen en configureren van Azure API Management op een intern virtueel netwerk
services: api-management
documentationcenter: ''
author: vladvino
manager: kjoshi
editor: ''
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/31/2019
ms.author: apimpm
ms.openlocfilehash: 6054c595bca26dc2a0432c53369a60a61e3efde0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841860"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Azure API Management-service gebruiken met een intern virtueel netwerk
Met Azure Virtual Networks kan Azure API Management API's beheren die niet toegankelijk zijn op internet. Een aantal VPN-technologieën zijn beschikbaar om de verbinding te maken. API-beheer kan worden geïmplementeerd in twee hoofdmodi binnen een virtueel netwerk:
* Extern
* Intern

Wanneer API Management wordt geïmplementeerd in de interne virtuele netwerkmodus, zijn alle serviceeindpunten (de proxygateway, de portal voor ontwikkelaars, direct beheer en Git) alleen zichtbaar binnen een virtueel netwerk waarop u de toegang tot u beheert. Geen van de serviceeindpunten is geregistreerd op de openbare DNS-server.

> [!NOTE]
> Omdat er geen DNS-vermeldingen zijn voor de serviceeindpunten, zijn deze eindpunten niet toegankelijk totdat [DNS is geconfigureerd](#apim-dns-configuration) voor het virtuele netwerk.

Met API-beheer in de interne modus u de volgende scenario's realiseren:

* Api's die in uw privédatacenter worden gehost, veilig toegankelijk maken voor derden daarbuiten door gebruik te maken van site-to-site- of Azure ExpressRoute VPN-verbindingen.
* Schakel hybride cloudscenario's in door uw api's en on-premises API's in de cloud bloot te stellen via een gemeenschappelijke gateway.
* Beheer uw API's die op meerdere geografische locaties worden gehost met één gatewayeindpunt.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Vereisten

Als u de in dit artikel beschreven stappen wilt uitvoeren, moet u het volgende hebben:

+ **Een actief Azure-abonnement**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Een Azure API Management-exemplaar**. Zie [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md)voor meer informatie.
+ Wanneer een API Management-service wordt geïmplementeerd in een virtueel netwerk, wordt een [lijst met poorten](./api-management-using-with-vnet.md#required-ports) gebruikt en moet deze worden geopend. 

## <a name="creating-an-api-management-in-an-internal-virtual-network"></a><a name="enable-vpn"> </a>Een API-beheer maken in een intern virtueel netwerk
De API Management-service in een intern virtueel netwerk wordt gehost achter een [interne load balancer (klassiek).](https://docs.microsoft.com/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud) Dit is de enige optie die beschikbaar is en kan niet worden gewijzigd.

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Een virtuele netwerkverbinding inschakelen met de Azure-portal

1. Blader naar uw Azure API Management-exemplaar in de [Azure-portal.](https://portal.azure.com/)
2. Selecteer **Virtueel netwerk**.
3. Configureer de instantie API-beheer die moet worden geïmplementeerd in het virtuele netwerk.

    ![Menu voor het instellen van een Azure API-beheer in een intern virtueel netwerk][api-management-using-internal-vnet-menu]

4. Selecteer **Opslaan**.

Nadat de implementatie is geslaagd, ziet u **een virtueel** virtueel IP-adres en **een openbaar** virtueel IP-adres van uw API-beheerservice op het overzichtsblad. Het **privé** virtuele IP-adres is een load balanced IP-adres `gateway`vanuit `portal`het gedelegeerde subnet API Management waarover , `management` en `scm` eindpunten kunnen worden geopend. Het **openbare** virtuele IP-adres wordt **alleen** gebruikt voor het beheer van vlakverkeer naar `management` eindpunt boven poort 3443 en kan worden vergrendeld op de [ApiManagement-servicetag.][ServiceTags]

![API-beheerdashboard met een intern virtueel netwerk geconfigureerd][api-management-internal-vnet-dashboard]

> [!NOTE]
> De testconsole die beschikbaar is op de Azure Portal werkt niet voor **interne** VNET-geïmplementeerde service, omdat de gateway-url niet is geregistreerd op de openbare DNS. Gebruik in plaats daarvan de testconsole op de **ontwikkelaarsportal.**

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>Een virtuele netwerkverbinding inschakelen met PowerShell-cmdlets

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U ook virtuele netwerkconnectiviteit inschakelen met PowerShell-cmdlets.

* Maak een API Management-service in een virtueel netwerk: gebruik de cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) om een Azure API Management-service te maken binnen een virtueel netwerk en configureer deze om het interne virtuele netwerktype te gebruiken.

* Een bestaande implementatie van een API Management-service in een virtueel netwerk bijwerken: gebruik de cmdlet [Update-AzApiManagementRegio](/powershell/module/az.apimanagement/update-azapimanagementregion) om een bestaande API Management-service binnen een virtueel netwerk te verplaatsen en deze te configureren om het interne virtuele netwerktype te gebruiken.

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>DNS-configuratie
Wanneer API Management zich in de externe virtuele netwerkmodus bevindt, wordt de DNS beheerd door Azure. Voor de interne virtuele netwerkmodus moet u uw eigen routering beheren.

> [!NOTE]
> API Management-service luistert niet naar aanvragen afkomstig van IP-adressen. Het reageert alleen op aanvragen voor de hostnaam die is geconfigureerd op de serviceeindpunten. Deze eindpunten omvatten gateway, de Azure-portal en de Developer-portal, het eindpunt voor direct beheer en Git.

### <a name="access-on-default-host-names"></a>Toegang op standaardhostnamen
Wanneer u een API Management-service maakt, bijvoorbeeld 'contosointernalvnet' genoemd, worden de volgende serviceeindpunten standaard geconfigureerd:

   * Gateway of proxy: contosointernalvnet.azure-api.net

   * De developer portal: contosointernalvnet.portal.azure-api.net

   * De nieuwe Developer portal: contosointernalvnet.developer.azure-api.net

   * Eindpunt direct beheer: contosointernalvnet.management.azure-api.net

   * Git: contosointernalvnet.scm.azure-api.net

Als u toegang wilt krijgen tot deze eindpunten van de API Management-service, u een virtuele machine maken in een subnet dat is verbonden met het virtuele netwerk waarin API-beheer is geïmplementeerd. Ervan uitgaande dat het interne virtuele IP-adres voor uw service 10.1.0.5 is, u het hosts-bestand, %SystemDrive%\drivers\etc\hosts, als volgt toewijzen:

   * 10.1.0.5 contosointernalvnet.azure-api.net

   * 10.1.0.5 contosointernalvnet.portal.azure-api.net

   * 10.1.0.5 contosointernalvnet.developer.azure-api.net

   * 10.1.0.5 contosointernalvnet.management.azure-api.net

   * 10,1,0,5 contosointernalvnet.scm.azure-api.net

U hebt dan toegang tot alle serviceeindpunten vanaf de virtuele machine die u hebt gemaakt.
Als u een aangepaste DNS-server in een virtueel netwerk gebruikt, u ook DNS-records maken en toegang krijgen tot deze eindpunten vanaf elke locatie in uw virtuele netwerk.

### <a name="access-on-custom-domain-names"></a>Toegang op aangepaste domeinnamen

1. Als u geen toegang wilt krijgen tot de API Management-service met de standaardhostnamen, u aangepaste domeinnamen instellen voor al uw serviceeindpunten zoals weergegeven in de volgende afbeelding:

   ![Een aangepast domein instellen voor API-beheer][api-management-custom-domain-name]

2. Vervolgens u records maken op uw DNS-server om toegang te krijgen tot de eindpunten die alleen toegankelijk zijn vanuit uw virtuele netwerk.

## <a name="routing"></a><a name="routing"> </a> Routering

* Een gebalanceerd privé-privé-IP-adres uit het subnetbereik wordt gereserveerd en gebruikt om toegang te krijgen tot de API Management-serviceeindpunten vanuit het virtuele netwerk. *private* Dit *private* privé-IP-adres is te vinden op het overzichtsblad voor de service in de Azure-portal. Dit adres moet worden geregistreerd bij de DNS-servers die door het virtuele netwerk worden gebruikt.
* Een load balanced *public* IP-adres (VIP) wordt ook gereserveerd om toegang te bieden tot het eindpunt van de beheerservice via poort 3443. Dit *openbare* IP-adres is te vinden op het overzichtsblad voor de service in de Azure-portal. Het *openbare* IP-adres wordt alleen gebruikt `management` voor het beheer van het vliegtuigverkeer naar het eindpunt via poort 3443 en kan worden vergrendeld op de [ApiManagement-servicetag.][ServiceTags]
* IP-adressen uit het subnet IP-bereik (DIP) worden toegewezen aan elke VM in de service en worden gebruikt om toegang te krijgen tot bronnen binnen het virtuele netwerk. Een openbaar IP-adres (VIP) wordt gebruikt om toegang te krijgen tot bronnen buiten het virtuele netwerk. Als IP-beperkingslijsten worden gebruikt om bronnen binnen het virtuele netwerk te beveiligen, moet het volledige bereik voor het subnet waar de API-beheerservice wordt geïmplementeerd, worden opgegeven om toegang tot de service te verlenen of te beperken.
* De laadgebalanceerde openbare en private IP-adressen zijn te vinden op het overzichtsblad in de Azure-portal.
* De IP-adressen die zijn toegewezen voor openbare en particuliere toegang kunnen veranderen als de service wordt verwijderd en vervolgens weer in het virtuele netwerk wordt toegevoegd. Als dit gebeurt, kan het nodig zijn om DNS-registraties, routeringsregels en IP-beperkingslijsten binnen het virtuele netwerk bij te werken.

## <a name="related-content"></a><a name="related-content"> </a>Gerelateerde inhoud
Zie voor meer informatie de volgende artikelen:
* [Veelvoorkomende netwerkconfiguratieproblemen bij het instellen van Azure API Management in een virtueel netwerk][Common network configuration problems]
* [Veelgestelde vragen over virtueel netwerk](../virtual-network/virtual-networks-faq.md)
* [Een record maken in DNS](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/security-overview.md#service-tags

