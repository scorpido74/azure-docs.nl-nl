---
title: Overzicht van aliasrecords - Azure DNS
description: In dit artikel vindt u meer informatie over ondersteuning voor aliasrecords in Microsoft Azure DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 08/09/2019
ms.author: rohink
ms.openlocfilehash: 271770935cf4cb83d4abc6e82a4f4b13ffe865b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295489"
---
# <a name="azure-dns-alias-records-overview"></a>Overzicht van Azure DNS-aliasrecords

Azure DNS-aliasrecords zijn kwalificaties op een DNS-recordset. Ze kunnen verwijzen naar andere Azure-bronnen vanuit uw DNS-zone. U bijvoorbeeld een aliasrecordset maken die verwijst naar een openbaar IP-adres van Azure in plaats van een A-record. Uw aliasrecordset wijst dynamisch naar een azure-ip-adresservice-exemplaar. Als gevolg hiervan wordt de aliasrecordset naadloos bijgewerkt tijdens DNS-resolutie.

Een aliasrecordset wordt ondersteund voor de volgende recordtypen in een Azure DNS-zone: 

- A
- AAAA
- CNAME

> [!NOTE]
> Als u van plan bent een aliasrecord te gebruiken voor de A- of AAAA-recordtypen om een [Azure Traffic Manager-profiel](../traffic-manager/quickstart-create-traffic-manager-profile.md) aan te wijzen, moet u ervoor zorgen dat het Traffic Manager-profiel alleen [externe eindpunten](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints)heeft. U moet het IPv4- of IPv6-adres opgeven voor externe eindpunten in Traffic Manager. U geen volledig gekwalificeerde domeinnamen (FQDN's) gebruiken in eindpunten. Gebruik idealiter statische IP-adressen.

## <a name="capabilities"></a>Functionaliteit

- **Wijs een openbare IP-bron aan vanuit een DNS A/AAAA-recordset.** U een A/AAAA-recordset maken en er een aliasrecord van maken die is ingesteld om te wijzen op een openbare IP-bron (standaard of basis). De DNS-recordset wordt automatisch gewijzigd als het openbare IP-adres wordt gewijzigd of wordt verwijderd. Bungelende DNS-records die wijzen op onjuiste IP-adressen worden vermeden.

   Er is een huidige limiet van 20 aliasrecordssets per resource.

- **Wijs een Traffic Manager-profiel aan vanuit een DNS A/AAAA/CNAME-recordset.** U een A/AAAA- of CNAME-recordset maken en aliasrecords gebruiken om deze naar een Traffic Manager-profiel te leiden. Het is vooral handig wanneer u verkeer moet routeren op een zone-top, omdat traditionele CNAME-records niet worden ondersteund voor een zonetop. Stel dat uw Traffic Manager-profiel myprofile.trafficmanager.net is en uw zakelijke DNS-zone contoso.com is. U een aliasrecordset van type A/AAAA maken voor contoso.com (de zonetop) en myprofile.trafficmanager.net.
- **Wijs een CDN-eindpunt (Azure Content Delivery Network) aan.** Dit is handig wanneer u statische websites maakt met Azure-opslag en Azure CDN.
- **Wijs een andere DNS-record aan die binnen dezelfde zone is ingesteld.** Aliasrecords kunnen naar andere recordsets van hetzelfde type verwijzen. Een DNS CNAME-recordset kan bijvoorbeeld een alias zijn voor een andere CNAME-recordset. Deze regeling is handig als u wilt dat sommige recordsets aliassen en sommige niet-aliassen zijn.

## <a name="scenarios"></a>Scenario's

Er zijn een paar veelvoorkomende scenario's voor Aliasrecords.

### <a name="prevent-dangling-dns-records"></a>Voorkom bungelende DNS-records

Een veelvoorkomend probleem met traditionele DNS-records is bungelende records. Bijvoorbeeld DNS-records die niet zijn bijgewerkt om wijzigingen in IP-adressen weer te geven. Het probleem doet zich vooral voor bij A/AAAA- of CNAME-recordtypen.

Bij een traditionele DNS-zonerecord moet de DNS-record die eraan is gekoppeld, handmatig worden bijgewerkt als het doel-IP of CNAME niet meer bestaat. In sommige organisaties kan een handmatige update niet op tijd plaatsvinden vanwege procesproblemen of de scheiding van rollen en bijbehorende machtigingsniveaus. Een rol kan bijvoorbeeld de bevoegdheid hebben om een CNAME- of IP-adres te verwijderen dat bij een toepassing hoort. Maar het heeft niet voldoende autoriteit om de DNS-record die wijst naar die doelen bij te werken. Een vertraging bij het bijwerken van de DNS-record kan mogelijk leiden tot een storing voor de gebruikers.

Aliasrecords voorkomen bungelende verwijzingen door de levenscyclus van een DNS-record strak te koppelen aan een Azure-bron. Overweeg bijvoorbeeld een DNS-record die is gekwalificeerd als aliasrecord om te wijzen op een openbaar IP-adres of een Traffic Manager-profiel. Als u deze onderliggende bronnen verwijdert, wordt de DNS-aliasrecord een lege recordset. Het verwijst niet langer naar de verwijderde bron.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>DNS-recordset automatisch bijwerken wanneer ip-adressen van toepassingen veranderen

Dit scenario is vergelijkbaar met het vorige scenario. Misschien wordt een toepassing verplaatst of wordt de onderliggende virtuele machine opnieuw opgestart. Een aliasrecord wordt vervolgens automatisch bijgewerkt wanneer het IP-adres wordt gewijzigd voor de onderliggende openbare IP-bron. Dit voorkomt mogelijke beveiligingsrisico's van het doorverwijzen van de gebruikers naar een andere toepassing die het oude openbare IP-adres is toegewezen.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Host load-balanced applicaties at the zone apex Host load-balanced applications at the zone apex Host load-balanced applications at the zone apex Host load

Het DNS-protocol voorkomt de toewijzing van CNAME-records op de zonetop. Bijvoorbeeld als uw domein contoso.com is; U CNAME-records maken voor somelabel.contoso.com; maar je geen CNAME maken voor contoso.com zelf.
Deze beperking vormt een probleem voor toepassingseigenaren die load-balanced-toepassingen achter [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)hebben. Aangezien het gebruik van een Traffic Manager-profiel het maken van een CNAME-record vereist, is het niet mogelijk om het Traffic Manager-profiel aan te wijzen vanaf de top van de zone.

Dit probleem wordt opgelost met aliasrecords. In tegenstelling tot CNAME-records worden aliasrecords gemaakt op de zonetop en kunnen toepassingseigenaren deze gebruiken om hun zonetoprecord te wijzen op een Traffic Manager-profiel met externe eindpunten. Toepassingseigenaren wijzen naar hetzelfde Traffic Manager-profiel dat wordt gebruikt voor een ander domein binnen hun DNS-zone.

Contoso.com en www\.contoso.com kunnen bijvoorbeeld hetzelfde Traffic Manager-profiel aanwijzen. Zie de sectie Volgende stappen voor meer informatie over het gebruik van aliasrecords met Azure Traffic Manager-profielen.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Puntzonetop naar Azure CDN-eindpunten

Net als bij een Traffic Manager-profiel u ook aliasrecords gebruiken om uw DNS-zone-top naar Azure CDN-eindpunten te wijzen. Dit is handig wanneer u statische websites maakt met Azure-opslag en Azure CDN. U dan toegang krijgen tot de website zonder vooraf www aan uw DNS-naam te gebruiken.

Bijvoorbeeld als uw statische website `www.contoso.com, your users can access your site using contoso.com` is vernoemd zonder de noodzaak om www voor te bereiden op de DNS-naam.

Zoals eerder beschreven, worden CNAME-records niet ondersteund op de zonetop. U dus geen CNAME-record gebruiken om contoso.com naar uw CDN-eindpunt te wijzen. In plaats daarvan u een aliasrecord gebruiken om de zonetop rechtstreeks naar een CDN-eindpunt te wijzen.

> [!NOTE]
> Het aanwijzen van een zonetop naar CDN-eindpunten voor Azure CDN van Akamai wordt momenteel niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over aliasrecords:

- [Zelfstudie: Een aliasrecord configureren om te verwijzen naar een openbaar IP-adres van Azure](tutorial-alias-pip.md)
- [Zelfstudie: een Azure DNS-aliasrecord configureren om het gebruik van hoofddomeinnaam met Traffic Manager te ondersteunen](tutorial-alias-tm.md)
- [Veelgestelde vragen over DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
