---
title: Veelgestelde vragen over persoonlijke koppelingen van Azure
description: Meer informatie over persoonlijke Azure-koppelingen.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 9239917599b40ac159e1cdbd4d3db6f6e70e12d2
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019035"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Veelgestelde vragen over persoonlijke koppelingen van Azure

## <a name="private-link"></a>Persoonlijke koppeling

### <a name="what-is-azure-private-link-service-and-private-endpoint"></a>Wat is Azure Private Link service en persoonlijk eind punt?

- **Persoonlijk Azure-eind punt**: Persoonlijk Azure-eind punt is een netwerk interface waarmee u privé en veilig een service kunt verbinden met een persoonlijke Azure-koppeling. U kunt privé-eind punten gebruiken om verbinding te maken met een Azure PaaS-service die een persoonlijke koppeling of een persoonlijke koppelings service ondersteunt.
- **Persoonlijke Azure-koppelings service**: De service Azure private link is een service die is gemaakt door een service provider. Op dit moment kan een persoonlijke koppelings service worden gekoppeld aan de front-end-IP-configuratie van een Standard Load Balancer. 

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>Kan ik mijn Service koppelen aan meerdere persoonlijke eind punten?
Ja. Een service voor persoonlijke koppelingen kan verbindingen van meerdere persoonlijke eind punten ontvangen. Een persoonlijk eind punt kan echter alleen verbinding maken met één privé koppelings service.  
 
### <a name="is-data-transferred-over-private-link-always-private"></a>Worden gegevens die via een persoonlijke koppeling worden overgebracht altijd privé?
Ja. Alle gegevens via de persoonlijke Azure-koppeling blijven op de micro soft-backbone. Er wordt geen verbinding met Internet verzonden.  
 
### <a name="what-is-the-difference-between-a-vnet-service-endpoint-and-a-private-endpoint"></a>Wat is het verschil tussen een VNet-service-eind punt en een persoonlijk eind punt?
- Met de VNet-service-eind punten worden de privé-adres ruimte van uw virtuele netwerk en de identiteit van uw VNet uitgebreid naar de Azure-Services, via een directe verbinding. Met Service-eind punten kunt u uw kritieke Azure-service resources alleen op uw virtuele netwerken beveiligen, terwijl het verkeer wordt geoptimaliseerd en in het micro soft backbone-netwerk blijft, het sdestined het open bare IP-adres van de service is.
- Privé-eind punt is een netwerk bron die fungeert als een toegangs punt in uw virtuele netwerk en gebruikmaakt van een privé-IP-adres om de services te bereiken die worden aangedreven door een persoonlijke koppeling. Het verkeer is geoptimaliseerd voor route ring en blijft in het micro soft backbone-netwerk.   

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Wat is de relatie tussen de service voor persoonlijke koppelingen en het persoonlijke eind punt?
Het is een een-op-veel-relatie. Een service voor persoonlijke koppelingen kan verbindingen van meerdere persoonlijke eind punten ontvangen. Aan de andere kant kan één persoonlijk eind punt alleen verbinding maken met één privé koppelings service.    
 
### <a name="will-vnet-service-endpoints-be-deprecated-once-private-endpoints-are-available"></a>Worden de VNet-service-eind punten afgeschaft wanneer er privé-eind punten beschikbaar zijn? 
Nee. VNet-service-eind punten en privé-eind punten zijn onafhankelijke technologieën/bronnen. Ze kunnen elkaar aanvullen en beide bestaan samen. Sommige functionaliteits-en use-cases overlappen mogelijk, u kunt het model kiezen dat aan uw behoeften voldoet.  
 
### <a name="i-am-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Ik ben een service provider met behulp van een persoonlijke Azure-koppeling. Moet ik ervoor zorgen dat alle klanten een unieke IP-ruimte hebben en elkaar niet overlappen met mijn IP-ruimte? 
Nee. Persoonlijke Azure-koppeling biedt deze functionaliteit voor u. Daarom is het niet nodig om niet-overlappende adres ruimte te hebben met de adres ruimte van uw klant. 
 
## <a name="private-link-service"></a>Persoonlijke koppelings service
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Wat zijn de vereisten voor het maken van een privé koppelings service? 
Uw service back-ends moeten zich in een virtueel netwerk en achter een Standard Load Balancer bevindt.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Hoe kan ik een persoonlijke koppelings service schalen? 
U kunt uw persoonlijke koppelings service op een aantal verschillende manieren schalen: 
- Back-end-Vm's toevoegen aan de groep achter uw Standard Load Balancer 
- Voeg een IP-adres toe aan de persoonlijke koppelings service. We hebben Maxi maal 8 Ip's per privé koppelings service toegestaan.  
- Voeg een nieuwe persoonlijke koppelings service toe aan Standard Load Balancer. We hebben Maxi maal acht Services voor persoonlijke koppelingen per load balancer toegestaan.   


### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Hoe kan ik de bloot stelling van mijn persoonlijke koppelings service controleren?
U kunt de bloot stelling regelen met behulp van de zichtbaarheids configuratie voor de service private link. De zicht baarheid ondersteunt drie instellingen:

- **Geen** : alleen abonnementen met RBAC-toegang kunnen de service vinden. 
- **Beperkte** abonnementen die white list zijn en met RBAC-toegang, kunnen de service vinden. 
- **Alle** -iedereen kan de service vinden. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Kan ik een persoonlijke koppelings service maken met basis Load Balancer? 
Nee. Een persoonlijke koppelings service via een basis Load Balancer wordt niet ondersteund.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Is een toegewezen subnet vereist voor de service voor privé-koppeling? 
Nee. Voor de service voor persoonlijke koppelingen is geen toegewezen subnet vereist. U kunt elk subnet in uw VNet kiezen waar uw service wordt geïmplementeerd.   

## <a name="private-endpoint"></a>Privé-eindpunt 
 
### <a name="cani-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>CanI meerdere persoonlijke eind punten in hetzelfde VNet maken? Kunnen ze verbinding maken met verschillende services? 
Ja. U kunt meerdere persoonlijke eind punten hebben in hetzelfde VNet of subnet. Ze kunnen verbinding maken met verschillende services.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Is er een toegewezen subnet vereist voor privé-eind punten? 
Nee. U hebt geen toegewezen subnet voor privé-eind punten nodig. U kunt een privé-eindpunt-IP-adres kiezen uit elk subnet van het VNet waar uw service is geïmplementeerd.  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>Kan een persoonlijk eind punt verbinding maken met de service voor persoonlijke koppelingen tussen Azure Active Directory tenants? 
Ja. Privé-eind punten kunnen verbinding maken met persoonlijke koppelings Services of Azure PaaS over AD-tenants.  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>Kan een persoonlijk eind punt verbinding maken met Azure PaaS-resources in azure-regio's?
Ja. Privé-eind punten kunnen verbinding maken met Azure PaaS-resources in azure-regio's.

##  <a name="next-steps"></a>Volgende stappen

- Meer informatie over [persoonlijke Azure-koppelingen](private-link-overview.md)
