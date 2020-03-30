---
title: Veelgestelde vragen - Azure DNS
description: Lees in dit artikel over veelgestelde vragen over Azure DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 6/15/2019
ms.author: rohink
ms.openlocfilehash: 76b19cfb3c00a26d81eab81f67d8e156a520f377
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77121716"
---
# <a name="azure-dns-faq"></a>Veelgestelde vragen over Azure DNS

## <a name="about-azure-dns"></a>Over Azure DNS

### <a name="what-is-azure-dns"></a>Wat is Azure DNS?

Het DNS (Domain Name System) vertaalt of lost een website of servicenaam naar het IP-adres. Azure DNS is een hostingservice voor DNS-domeinen. Het biedt naamsresolutie met behulp van Microsoft Azure-infrastructuur. Door uw domeinen in Azure te hosten, kunt u uw DNS-records met dezelfde referenties, API's, hulpprogramma's en facturering beheren als voor uw andere Azure-services.

DNS-domeinen in Azure DNS worden gehost op het wereldwijde Azure-netwerk van DNS-naamservers. Dit systeem maakt gebruik van Anycast-netwerken, zodat elke DNS-query wordt beantwoord door de dichtstbijzijnde beschikbare DNS-server. Azure DNS biedt snelle prestaties en hoge beschikbaarheid voor uw domein.

Azure DNS is gebaseerd op Azure Resource Manager. Azure DNS profiteert van Resource Manager-functies zoals op rollen gebaseerd toegangscontrolebeheer, controlelogboeken en bronvergrendeling. U domeinen en records beheren via de Azure-portal, Azure PowerShell-cmdlets en het azure CLI met meerdere platforms. Toepassingen die automatisch DNS-beheer vereisen, kunnen met de service worden geïntegreerd via de REST API en SDK's.

### <a name="how-much-does-azure-dns-cost"></a>Hoeveel kost Azure DNS?

Het Azure DNS-factureringsmodel is gebaseerd op het aantal DNS-zones dat wordt gehost in Azure DNS. Het is ook gebaseerd op het aantal DNS-query's dat ze ontvangen. Kortingen worden verstrekt op basis van het gebruik.

Zie de [prijspagina van Azure DNS voor](https://azure.microsoft.com/pricing/details/dns/)meer informatie .

### <a name="what-is-the-sla-for-azure-dns"></a>Wat is de SLA voor Azure DNS?

Azure garandeert dat geldige DNS-verzoeken 100% van de tijd een antwoord ontvangen van ten minste één Azure DNS-naamserver.

Zie de pagina [Azure DNS SLA](https://azure.microsoft.com/support/legal/sla/dns)voor meer informatie .

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Wat is een DNS-zone? Is het hetzelfde als een DNS-domein? 

Een domein is een unieke naam in het domeinnaamsysteem. Bijvoorbeeld: contoso.com.

Een DNS-zone wordt gebruikt om de DNS-records voor een bepaald domein te hosten. Het domein contoso.com kan bijvoorbeeld meerdere DNS-records bevatten. De records kunnen mail.contoso.com bevatten voor\.een e-mailserver en www contoso.com voor een website. Deze records worden gehost in de DNS-zone contoso.com.

Een domeinnaam is *slechts een naam.* Een DNS-zone is een gegevensbron die de DNS-records voor een domeinnaam bevat. Met Azure DNS kunt u een DNS-zone hosten en de DNS-records voor een domein in Azure beheren. Het biedt ook DNS-naamservers om DNS-query's van het internet te beantwoorden.

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>Moet ik een DNS-domeinnaam kopen om Azure DNS te gebruiken? 

Niet per se.

U hoeft geen domein te kopen om een DNS-zone in Azure DNS te hosten. U op elk gewenst moment een DNS-zone maken zonder de domeinnaam te bezitten. DNS-query's voor deze zone worden alleen opgelost als ze worden doorverwezen naar de Azure DNS-naamservers die aan de zone zijn toegewezen.

Als u uw DNS-zone wilt koppelen aan de algemene DNS-hiërarchie, moet u de domeinnaam kopen. Dns-query's van overal ter wereld vinden vervolgens uw DNS-zone en beantwoorden met uw DNS-records.

## <a name="azure-dns-features"></a>Azure DNS-functies

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Zijn er beperkingen bij het gebruik van aliasrecords voor een domeinnaamapex met Traffic Manager?

Ja. U moet statische openbare IP-adressen gebruiken met Azure Traffic Manager. Configureer het **doel voor extern eindpunt** met een statisch IP-adres. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Ondersteunt Azure DNS dns-gebaseerde verkeersroutering of endpoint failover?

DNS-gebaseerde verkeersroutering en endpoint failover worden geleverd door Traffic Manager. Traffic Manager is een aparte Azure-service die kan worden gebruikt met Azure DNS. Zie het overzicht [Verkeersbeheer](../traffic-manager/traffic-manager-overview.md)voor meer informatie.

Azure DNS ondersteunt alleen het hosten van statische DNS-domeinen, waarbij elke DNS-query voor een bepaalde DNS-record altijd dezelfde DNS-respons ontvangt.

### <a name="does-azure-dns-support-domain-name-registration"></a>Ondersteunt Azure DNS domeinnaamregistratie?

Nee. Azure DNS biedt momenteel geen ondersteuning voor de optie om domeinnamen te kopen. Als u domeinen wilt kopen, moet u een domeinnaamregistrar van derden gebruiken. De registrar rekent meestal een kleine jaarlijkse vergoeding. De domeinen kunnen vervolgens worden gehost in Azure DNS voor het beheer van DNS-records. Zie [Een domein delegeren aan Azure DNS](dns-domain-delegation.md)voor meer informatie.

De functie voor het kopen van domeinnamen wordt bijgehouden in de Azure-achterstand. Gebruik de feedbacksite om uw ondersteuning voor deze functie te [registreren.](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar)

### <a name="does-azure-dns-support-dnssec"></a>Ondersteunt Azure DNS DNSSEC?

Nee. Azure DNS biedt momenteel geen ondersteuning voor dnssec (Domain Name System Security Extensions).

De DNSSEC-functie wordt bijgehouden in de Azure DNS-achterstand. Gebruik de feedbacksite om uw ondersteuning voor deze functie te [registreren.](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support)

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Wordt Azure DNS support zone overgedragen (AXFR/IXFR)?

Nee. Azure DNS ondersteunt momenteel geen zoneoverdrachten. DNS-zones kunnen worden [geïmporteerd in Azure DNS met behulp van de Azure CLI](dns-import-export.md). DNS-records worden beheerd via de [Azure DNS-beheerportal,](dns-operations-recordsets-portal.md) [REST API,](https://docs.microsoft.com/powershell/module/az.dns) [SDK,](dns-sdk.md) [PowerShell-cmdlets](dns-operations-recordsets.md)of het [CLI-hulpprogramma](dns-operations-recordsets-cli.md).

De functie zoneoverdracht wordt bijgehouden in de Azure DNS-achterstand. Gebruik de feedbacksite om uw ondersteuning voor deze functie te [registreren.](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c)

### <a name="does-azure-dns-support-url-redirects"></a>Maakt Azure DNS URL-omleidingen?

Nee. URL-omleidingsservices zijn geen DNS-service. Ze werken op HET HTTP-niveau in plaats van op DNS-niveau. Sommige DNS-providers bundelen een URL redirect-service als onderdeel van hun totale aanbod. Deze service wordt momenteel niet ondersteund door Azure DNS.

De functie URL-omleiding wordt bijgehouden in de Azure DNS-achterstand. Gebruik de feedbacksite om uw ondersteuning voor deze functie te [registreren.](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape)

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>Ondersteunt Azure DNS de uitgebreide ASCII-codering (8-bits) die is ingesteld voor TXT-recordsets?

Ja. Azure DNS ondersteunt de uitgebreide ASCII-coderingsset voor TXT-recordsets. U moet echter de nieuwste versie van de Azure REST API's, SDK's, PowerShell en de CLI gebruiken. Versies ouder dan 1 oktober 2017 of SDK 2.1 ondersteunen de uitgebreide ASCII-set niet. 

U bijvoorbeeld een tekenreeks als de waarde voor een TXT-record met het uitgebreide ASCII-teken \128 toevoegen. Een voorbeeld is 'abcd\128efgh'. Azure DNS gebruikt de bytewaarde van dit teken, dat 128 is, in interne weergave. Op het moment van DNS-resolutie wordt deze bytewaarde geretourneerd in het antwoord. Houd er ook rekening mee dat "abc" en "\097\098\099" uitwisselbaar zijn wat de resolutie betreft. 

We volgen [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) zone file master format escape rules voor TXT records. Bijvoorbeeld, `\` nu eigenlijk ontsnapt aan alles per de RFC. Als u `A\B` de TXT-recordwaarde opgeeft, wordt deze `AB`weergegeven en opgelost als slechts . Als je echt wilt dat `A\B` de TXT-record op `\` resolutie is, moet je opnieuw ontsnappen. Geef als voorbeeld `A\\B`op .

Deze ondersteuning is momenteel niet beschikbaar voor TXT-records die zijn gemaakt vanuit de Azure-portal.

## <a name="alias-records"></a>Aliasrecords

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>Wat zijn enkele scenario's waarin aliasrecords nuttig zijn?

Zie de sectie scenario's in het [overzicht van Azure DNS-aliasrecords](dns-alias.md).

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>Welke recordtypen worden ondersteund voor aliasrecordsets?

Aliasrecordsets worden ondersteund voor de volgende recordtypen in een Azure DNS-zone:
 
- A 
- AAAA
- CNAME 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>Welke resources worden ondersteund als doelen voor aliasrecordsets?

- **Wijs een openbare IP-bron aan vanuit een DNS A/AAAA-recordset.** U een A/AAAA-recordset maken en er een aliasrecord van maken die is ingesteld om te wijzen op een openbare IP-bron.
- **Wijs een Traffic Manager-profiel aan vanuit een DNS A/AAAA/CNAME-recordset.** U de CNAME van een Traffic Manager-profiel aanwijzen vanuit een DNS CNAME-recordset. Een voorbeeld is contoso.trafficmanager.net. U nu ook wijzen op een Traffic Manager-profiel met externe eindpunten van een A- of AAAA-record in uw DNS-zone.
- **Wijs een CDN-eindpunt (Azure Content Delivery Network) aan.** Dit is handig wanneer u statische websites maakt met Azure-opslag en Azure CDN.
- **Wijs een andere DNS-record aan die binnen dezelfde zone is ingesteld.** Aliasrecords kunnen verwijzen naar andere recordsets van hetzelfde type. U kunt bijvoorbeeld een DNS CNAME-recordset hebben als alias voor een andere CNAME-recordset van hetzelfde type. Deze regeling is handig als u wilt dat sommige recordsets aliassen en sommige niet-aliassen zijn.

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>Kan ik aliasrecords maken en bijwerken vanuit de Azure-portal?

Ja. U aliasrecords maken of beheren in de Azure-portal, samen met de Azure REST API's, PowerShell, de CLI en De DK's.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>Helpen aliasrecords ervoor te zorgen dat mijn DNS-recordset wordt verwijderd wanneer het onderliggende openbare IP-adres wordt verwijderd?

Ja. Deze functie is een van de kernmogelijkheden van aliasrecords. Het helpt u mogelijke uitval voor gebruikers van uw toepassing te voorkomen.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>Helpen aliasrecords ervoor te zorgen dat mijn DNS-recordset wordt bijgewerkt naar het juiste IP-adres wanneer het onderliggende openbare IP-adres verandert?

Ja. Deze functie is een van de kernmogelijkheden van aliasrecords. Het helpt u potentiële uitval of beveiligingsrisico's voor uw toepassing te voorkomen.

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>Zijn er beperkingen bij het gebruik van aliasrecordsets voor A- of AAAA-records die naar Traffic Manager worden aangeduid?

Ja. Als u een Traffic Manager-profiel als alias wilt aanwijzen uit een A- of AAAA-recordset, mag het profiel Traffic Manager alleen externe eindpunten gebruiken. Wanneer u de externe eindpunten maakt in Traffic Manager, geeft u de werkelijke IP-adressen van de eindpunten op.

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>Zijn er extra kosten verbonden aan het gebruik van aliasrecords?

Aliasrecords zijn een kwalificatie op een geldige DNS-recordset. Er is geen extra facturering voor aliasrecords.

## <a name="use-azure-dns"></a>Azure DNS gebruiken

### <a name="can-i-co-host-a-domain-by-using-azure-dns-and-another-dns-provider"></a>Kan ik een domein co-hosten met Azure DNS en een andere DNS-provider?

Ja. Azure DNS ondersteunt co-hostingdomeinen met andere DNS-services.

Als u co-hosting wilt instellen, wijzigt u de NS-records voor het domein om naar de naamservers van beide providers te wijzen. De registraties van de naamserver (NS) bepalen welke providers DNS-query's voor het domein ontvangen. U deze NS-records wijzigen in Azure DNS, in de andere provider en in de bovenliggende zone. De bovenliggende zone wordt meestal geconfigureerd via de domeinnaamregistrar. Zie [DNS-domeindelegatie](dns-domain-delegation.md)voor meer informatie over DNS-delegatie.

Zorg er ook voor dat de DNS-records voor het domein synchroon zijn tussen beide DNS-providers. Azure DNS biedt momenteel geen ondersteuning voor DNS-zoneoverdrachten. DNS-records moeten worden gesynchroniseerd met behulp van de [Azure DNS-beheerportal,](dns-operations-recordsets-portal.md) [REST API,](https://docs.microsoft.com/rest/api/dns/) [SDK,](dns-sdk.md) [PowerShell-cmdlets](dns-operations-recordsets.md)of het [CLI-gereedschap](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Moet ik mijn domein delegeren aan alle vier Azure DNS-naamservers?

Ja. Azure DNS wijst vier naamservers toe aan elke DNS-zone. Deze regeling is voor foutisolatie en verhoogde veerkracht. Als u in aanmerking wilt komen voor de Azure DNS SLA, delegert u uw domein aan alle vier de naamservers.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Wat zijn de gebruikslimieten voor Azure DNS?

De volgende standaardlimieten zijn van toepassing wanneer u Azure DNS gebruikt.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Kan ik een Azure DNS-zone verplaatsen tussen resourcegroepen of tussen abonnementen?

Ja. DNS-zones kunnen worden verplaatst tussen resourcegroepen of tussen abonnementen.

Er is geen effect op DNS-query's wanneer u een DNS-zone verplaatst. De naamservers die aan de zone zijn toegewezen, blijven hetzelfde. DNS-query's worden overal als normaal verwerkt.

Zie [Resources verplaatsen naar een nieuwe brongroep of -abonnement voor](../azure-resource-manager/management/move-resource-group-and-subscription.md)meer informatie en instructies over het verplaatsen van DNS-zones.

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Hoe lang duurt het voordat DNS-wijzigingen van kracht worden?

Nieuwe DNS-zones en DNS-records worden doorgaans snel weergegeven in de Azure DNS-naamservers. De timing is een paar seconden.

Wijzigingen in bestaande DNS-records kunnen iets langer duren. Ze worden meestal binnen 60 seconden weergegeven in de Azure DNS-naamservers. DNS-caching door DNS-clients en DNS-recursieve resolvers buiten Azure DNS kunnen ook de timing beïnvloeden. Als u deze cacheduur wilt beheren, gebruikt u de eigenschap Time-To-Live (TTL) van elke recordset.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Hoe kan ik mijn DNS-zones beschermen tegen onbedoelde verwijdering?

Azure DNS wordt beheerd met Azure Resource Manager. Azure DNS profiteert van de toegangscontrolefuncties die Azure Resource Manager biedt. Op rollen gebaseerde toegangscontrolebesturingselementen die gebruikers hebben gelezen of schrijven toegang tot DNS-zones en recordsets. Bronvergrendelingen voorkomen onbedoelde wijziging of verwijdering van DNS-zones en recordsets.

Zie [DNS-zones en -records beveiligen](dns-protect-zones-recordsets.md)voor meer informatie .

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Hoe stel ik SPF-records in Azure DNS in?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-name-servers-resolve-over-ipv6"></a>Verdwijnen Azure DNS-naamservers via IPv6? 

Ja. Azure DNS-naamservers zijn dual stack. Dual stack betekent dat ze IPv4- en IPv6-adressen hebben. Als u het IPv6-adres wilt vinden voor de Azure DNS-naamservers die zijn toegewezen aan uw DNS-zone, gebruikt u een hulpprogramma zoals nslookup. Een voorbeeld is `nslookup -q=aaaa <Azure DNS Nameserver>`.

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>Hoe stel ik een IDN in Azure DNS in?

Geïninternationaliseerde domeinnamen (IDN's) coderen elke DNS-naam met behulp van [punycode](https://en.wikipedia.org/wiki/Punycode). DNS-query's worden gemaakt met behulp van deze punycode-gecodeerde namen.

Als u IDN's in Azure DNS wilt configureren, converteert u de zonenaam of recordsetnaam naar punycode. Azure DNS biedt momenteel geen ondersteuning voor ingebouwde conversie van of naar punycode.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over Azure DNS](dns-overview.md).

- [Meer informatie over het gebruik van Azure DNS voor privédomeinen](private-dns-overview.md).

- [Meer informatie over DNS-zones en -records](dns-zones-records.md).

- [Aan de slag met Azure DNS](dns-getstarted-portal.md).
