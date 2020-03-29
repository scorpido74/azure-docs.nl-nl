---
title: Veelgestelde vragen over Azure Private Link (FAQ)
description: Meer informatie over Azure Private Link.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 7870b68ca931123d50e88e846aa066ce53972dbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349940"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Veelgestelde vragen over Azure Private Link (FAQ)

## <a name="private-link"></a>Private Link

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>Wat is Azure Private Endpoint en Azure Private Link Service?

- **[Azure Private Endpoint](private-endpoint-overview.md)**: Azure Private Endpoint is een netwerkinterface die u privé en veilig verbindt met een service die wordt aangedreven door Azure Private Link. U privéeindpunten gebruiken om verbinding te maken met een Azure PaaS-service die Private Link ondersteunt of met uw eigen Private Link-service.
- **[Azure Private Link Service](private-link-service-overview.md)**: Azure Private Link-service is een service die is gemaakt door een serviceprovider. Momenteel kan een Private Link-service worden gekoppeld aan de ip-configuratie aan de voorkant van een Standaard Load Balancer. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>Hoe wordt verkeer verzonden bij het gebruik van Private Link?
Verkeer wordt privé verzonden met behulp van Microsoft backbone. Het doorkruist het internet niet.  
 
### <a name="what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints"></a>Wat is het verschil tussen een Service Endpoints en een Private Endpoints?
- Bij het gebruik van private eindpunten wordt netwerktoegang verleend aan specifieke bronnen achter een bepaalde service die gedetailleerde segmentatie biedt, ook verkeer kan de servicebron van on-premises bereiken zonder openbare eindpunten te gebruiken.
- Een serviceeindpunt blijft een openbaar routeerbaar IP-adres.  Een privéeindpunt is een privé-IP in de adresruimte van het virtuele netwerk waar het privéeindpunt is geconfigureerd.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Wat is de relatie tussen Private Link-service en Private Endpoint?
Private Endpoint biedt toegang tot meerdere private link-bronnen, waaronder Azure PaaS-services en uw eigen Private Link-service. Het is een één-op-veel relatie. Eén Private Link-service kan verbindingen ontvangen van meerdere privéeindpunten. Aan de andere kant kan één privéeindpunt alleen verbinding maken met één Private Link-service.    

## <a name="private-endpoint"></a>Privé-eindpunt 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>Kan ik meerdere privéeindpunten maken in dezelfde VNet? Kunnen ze verbinding maken met verschillende services? 
Ja. U meerdere privéeindpunten in hetzelfde VNet of subnet hebben. Ze kunnen verbinding maken met verschillende diensten.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Heb ik een speciaal subnet nodig voor privé eindpunten? 
Nee. U hebt geen speciaal subnet nodig voor privéeindpunten. U een privé-eindpunt-IP kiezen uit elk subnet van het VNet waar uw service wordt geïmplementeerd.  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>Kan Private Endpoint verbinding maken met de Private Link-service voor Azure Active Directory Tenants? 
Ja. Privéeindpunten kunnen verbinding maken met Private Link-services of Azure PaaS voor AD-tenants.  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>Kan privéeindpunt verbinding maken met Azure PaaS-resources in Azure-regio's?
Ja. Privéeindpunten kunnen verbinding maken met Azure PaaS-resources in Azure-regio's.

## <a name="private-link-service"></a>Private Link-service
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Wat zijn de vereisten voor het maken van een Private Link-service? 
Uw service backends moeten zich in een virtueel netwerk en achter een Standard Load Balancer bevinden.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Hoe kan ik mijn Private Link-service schalen? 
U uw Private Link-service op een aantal verschillende manieren schalen: 
- Backend VM's toevoegen aan de pool achter uw Standaard Load Balancer 
- Voeg een IP toe aan de Private Link-service. We staan maximaal 8 IP's per Private Link-service toe.  
- Voeg nieuwe Private Link-service toe aan Standard Load Balancer. We staan maximaal acht Private Link-services per load balancer toe.   

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>Kan ik mijn service koppelen aan meerdere privéeindpunten?
Ja. Eén Private Link-service kan verbindingen ontvangen van meerdere privéeindpunten. Eén privéeindpunt kan echter alleen verbinding maken met één Private Link-service.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Hoe moet ik de belichting van mijn Private Link-service beheren?
U de belichting regelen met de zichtbaarheidsconfiguratie op de Private Link-service. Zichtbaarheid ondersteunt drie instellingen:

- **Geen** - Alleen abonnementen met RBAC-toegang kunnen de service vinden. 
- **Beperkend** - Alleen abonnementen die op de witte lijst staan en met RBAC-toegang kunnen de service vinden. 
- **Alles** - Iedereen kan de service vinden. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Kan ik een Private Link-service maken met Basic Load Balancer? 
Nee. Private Link-service via een Basic Load Balancer wordt niet ondersteund.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Is een speciaal subnet vereist voor Private Link-service? 
Nee. Private Link-service vereist geen speciaal subnet. U elk subnet in uw VNet kiezen waar uw service wordt geïmplementeerd.   

### <a name="i-am-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Ik ben een serviceprovider die Azure Private Link gebruikt. Moet ik ervoor zorgen dat al mijn klanten unieke IP-ruimte hebben en niet overlappen met mijn IP-ruimte? 
Nee. Azure Private Link biedt deze functionaliteit voor u. U hoeft dus geen niet-overlappende adresruimte te hebben met de adresruimte van uw klant. 

##  <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure Private Link](private-link-overview.md)
