---
title: Extra beveiliging tegen bedreigingen in Azure Security Center
description: Meer informatie over de bedreigings beveiliging die beschikbaar is via Azure Security Center buiten Azure Defender
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: memildin
ms.openlocfilehash: 0f4a849af2be9f02187dc3cda526c9c4727cab1b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935768"
---
# <a name="additional-threat-protections-in-azure-security-center"></a>Aanvullende beveiligings Risico's in Azure Security Center
En de ingebouwde [Azure Defender-beveiligingen](azure-defender.md)bieden Azure Security Center ook de volgende mogelijkheden voor beveiliging tegen bedreigingen.

> [!TIP]
> Als u de mogelijkheden voor bedreigings beveiliging van Security Center wilt inschakelen, moet u Azure Defender inschakelen op het abonnement dat de toepasselijke werk belastingen bevat.
>
> U kunt bedreigingen beveiliging inschakelen voor **Azure database for MariaDB/MySQL/PostgreSQL** op het niveau van de resource.


## <a name="threat-protection-for-azure-network-layer"></a>Bedreigings beveiliging voor Azure Network Layer <a name="network-layer"></a>
Security Center Network-laag analyse zijn gebaseerd op voorbeeld [gegevens van IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), die pakket headers zijn die worden verzameld door Azure core-routers. Op basis van deze gegevensfeed gebruikt Security Center machine learning modellen om kwaad aardige verkeers activiteiten te identificeren en te markeren. Security Center gebruikt de micro soft Threat Intelligence-data base ook om IP-adressen te verrijken.

Sommige netwerk configuraties kunnen Security Center beperken van het genereren van waarschuwingen over verdachte netwerk activiteit. Zorg ervoor dat de volgende handelingen worden Security Center om netwerk waarschuwingen te genereren:
- De virtuele machine heeft een openbaar IP-adres (of bevindt zich op een load balancer met een openbaar IP-adres).
- Het netwerk verkeer van de virtuele machine wordt niet geblokkeerd door een externe ID-oplossing.
- De virtuele machine is toegewezen aan hetzelfde IP-adres voor het hele uur waarin de verdachte communicatie heeft plaatsgevonden. Dit geldt ook voor virtuele machines die zijn gemaakt als onderdeel van een beheerde service (bijvoorbeeld AKS, Databricks).

Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-azurenetlayer)voor een overzicht van de waarschuwingen van de Azure-netwerklaag.


## <a name="threat-protection-for-azure-resource-manager-preview"></a>Bedreigings beveiliging voor Azure Resource Manager (preview-versie)<a name ="management-layer"></a>
De beveiligingslaag van Security Center is gebaseerd op Azure Resource Manager momenteel als preview-versie beschikbaar is.

Security Center biedt een extra beveiligingslaag door gebruik te maken van Azure Resource Manager-gebeurtenissen. dit wordt beschouwd als het controle vlak voor Azure. Door de Azure Resource Manager-records te analyseren, Security Center een ongebruikelijke of mogelijk schadelijke bewerking in de Azure-abonnements omgeving detecteert.

Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-azureresourceman)voor een lijst met waarschuwingen voor Azure Resource Manager (preview).


>[!NOTE]
> Verschillende van de voor gaande analyses worden mogelijk gemaakt door Microsoft Cloud App Security. Als u van deze analyses wilt profiteren, moet u een Cloud App Security-licentie activeren. Als u een licentie voor Cloud App Security hebt, zijn deze waarschuwingen standaard ingeschakeld. De waarschuwingen uitschakelen:
>
> 1. Selecteer **Prijzen en instellingen** in het hoofdmenu van Security Center.
> 1. Selecteer het abonnement dat u wilt wijzigen.
> 1. Selecteer **bedreigingen detectie**.
> 1. Schakel **Microsoft Cloud app Security toegang tot mijn gegevens toestaan**uit en selecteer **Opslaan**.


>[!NOTE]
>Security Center klant gegevens met betrekking tot beveiliging worden opgeslagen in dezelfde geografische regio als de resource. Als micro soft Security Center nog niet heeft geïmplementeerd in de geografische regio van de resource, worden de gegevens opgeslagen in de Verenigde Staten. Als Cloud App Security is ingeschakeld, wordt deze informatie opgeslagen in overeenstemming met de geolocatie regels van Cloud App Security. Zie [gegevens opslag voor niet-regionale Services](https://azuredatacentermap.azurewebsites.net/)voor meer informatie.

1. Stel de werk ruimte in waarop u de agent wilt installeren. Zorg ervoor dat de werk ruimte zich in hetzelfde abonnement bevindt dat u in Security Center gebruikt en dat u lees-en schrijf machtigingen hebt voor de werk ruimte.

1. Schakel **Azure Defender**in en selecteer **Opslaan**.


## <a name="threat-protection-for-azure-cosmos-db-preview"></a>Bedreigings beveiliging voor Azure Cosmos DB (preview-versie)<a name="cosmos-db"></a>

De Azure Cosmos DB waarschuwingen worden gegenereerd door ongebruikelijke en mogelijk schadelijke pogingen om Azure Cosmos DB accounts te openen of misbruik te maken.

Zie voor meer informatie:

* [Advanced Threat Protection voor Azure Cosmos DB (preview-versie)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [De lijst met beveiligings waarschuwingen voor bedreigingen voor Azure Cosmos DB (preview-versie)](alerts-reference.md#alerts-azurecosmos)



## <a name="threat-protection-for-other-microsoft-services"></a>Bedreigings beveiliging voor andere micro soft-Services <a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Bedreigings beveiliging voor Azure WAF <a name="azure-waf"></a>

Azure Application Gateway biedt de functie Web Application Firewall (WAF) voor de gecentraliseerde beveiliging van uw webtoepassingen tegen bekende aanvallen en beveiligingsproblemen.

Webtoepassingen zijn in toenemende mate het doel van aanvallen die gebruikmaken van algemeen bekende beveiligingsproblemen. De Application Gateway WAF is gebaseerd op de core Rule set 3,0 of 2.2.9 van het open Web Application Security-project. De WAF wordt automatisch bijgewerkt om te beschermen tegen nieuwe beveiligings problemen. 

Als u een licentie voor Azure WAF hebt, worden uw WAF-waarschuwingen gestreamd naar Security Center zonder dat er aanvullende configuratie nodig is. Zie voor meer informatie over de waarschuwingen die door WAF worden gegenereerd, [CRS-regel groepen en-regels voor Web Application firewall](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="threat-protection-for-azure-ddos-protection"></a>Bedreigings beveiliging voor Azure DDoS Protection <a name="azure-ddos"></a>

DDoS-aanvallen (Distributed Denial of service) zijn bekend om eenvoudig te worden uitgevoerd. Ze zijn een geweldig beveiligings probleem, met name als u uw toepassingen naar de Cloud verplaatst. 

Een DDoS-aanval probeert de bronnen van een toepassing te ontnemen, waardoor de toepassing niet beschikbaar is voor legitieme gebruikers. DDoS-aanvallen kunnen worden gericht op elk eind punt dat kan worden bereikt via internet.

Schaf een licentie voor Azure DDoS Protection aan en zorg ervoor dat u de aanbevolen procedures voor het ontwerpen van toepassingen gebruikt om DDoS-aanvallen te voor komen. DDoS Protection biedt verschillende service lagen. Zie [Azure DDoS Protection Overview](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)voor meer informatie.

Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-azureddos)voor een overzicht van de waarschuwingen voor Azure DDoS Protection.


## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over de beveiligings waarschuwingen van deze functies voor bedreigingen beveiliging:

* [Referentie tabel voor alle Azure Security Center waarschuwingen](alerts-reference.md)
* [Beveiligingswaarschuwingen in Azure Security Center](security-center-alerts-overview.md)
* [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Beveiligings waarschuwingen en aanbevelingen exporteren (preview-versie)](continuous-export.md)