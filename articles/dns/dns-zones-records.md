---
title: Overzicht DNS-zones en records - Azure DNS
description: Overzicht van ondersteuning voor het hosten van DNS-zones en records in Microsoft Azure DNS.
author: rohinkoul
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: conceptual
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: rohink
ms.openlocfilehash: 19189af6424960b8e20be686af745b10f2d8578b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265153"
---
# <a name="overview-of-dns-zones-and-records"></a>Overzicht van DNS-zones en -records

Op deze pagina worden de belangrijkste concepten van domeinen, DNS-zones en DNS-records en recordsets en hoe deze worden ondersteund in Azure DNS uitgelegd.

## <a name="domain-names"></a>Domeinnamen

Het Domain Name System is een hiërarchie van domeinen. De hiërarchie start vanaf het hoofddomein. De naam van dit domein is eenvoudigweg '**.**'.  Hieronder komen de topleveldomeinen, zoals com, net, org, uk of nl.  Onder de topleveldomeinen komen de secondleveldomeinen, zoals org.uk of co.jp. De domeinen in de DNS-hiërarchie worden wereldwijd gedistribueerd, gehost door DNS-naamservers over de hele wereld.

Een domeinnaamregistrar is een organisatie waarmee u een domeinnaam `contoso.com`kopen, zoals.  Als u een domeinnaam koopt, hebt u het recht om de DNS-hiërarchie onder `www.contoso.com` die naam te beheren, zodat u de naam bijvoorbeeld naar uw bedrijfswebsite leiden. De registrar kan het domein namens u op zijn eigen naamservers hosten of u toestaan alternatieve naamservers op te geven.

Azure DNS biedt een wereldwijd gedistribueerde serverinfrastructuur met hoge beschikbaarheid, die u gebruiken om uw domein te hosten. Door uw domeinen te hosten in Azure DNS, u uw DNS-records beheren met dezelfde referenties, API's, hulpprogramma's, facturering en ondersteuning als uw andere Azure-services.

Azure DNS biedt momenteel geen ondersteuning voor de aankoop van domeinnamen. Als u een domeinnaam wilt kopen, moet u een domeinnaamregistrar van derden gebruiken. De registrar rekent meestal een kleine jaarlijkse vergoeding. De domeinen kunnen vervolgens worden gehost in Azure DNS voor het beheer van DNS-records. Zie [Een domein aan Azure DNS overdragen](dns-domain-delegation.md) voor meer informatie.

## <a name="dns-zones"></a>DNS-zones

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>DNS-records

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Time-to-live

De tijd om te leven, of TTL, geeft aan hoe lang elke record in de cache wordt opgeslagen door clients voordat deze opnieuw wordt bevraagd. In het bovenstaande voorbeeld is de TTL 3600 seconden of 1 uur.

In Azure DNS is de TTL opgegeven voor de recordset, niet voor elke record, dus dezelfde waarde wordt gebruikt voor alle records binnen die recordset.  U een TTL-waarde opgeven tussen 1 en 2.147.483.647 seconden.

### <a name="wildcard-records"></a>Wildcardrecords

Azure DNS ondersteunt [recordsets met jokertekens](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Wildcardrecords worden geretourneerd als reactie op een query met een overeenkomende naam (tenzij er een nauwere overeenkomst is met een recordset zonder wildcard). Azure DNS ondersteunt wildcardrecordsets voor alle recordtypen, behalve NS en SOA.

Als u een wildcardrecordset wilt maken, gebruikt u de recordnaam '\*'. U ook een naam gebruiken\*met ' ' als het meest\*linkse label, bijvoorbeeld ' 'foo'.

### <a name="caa-records"></a>CAA-records

Met CAA-records kunnen domeineigenaren opgeven welke Certificeringsinstanties (CA's) gemachtigd zijn om certificaten voor hun domein uit te geven. Hierdoor kunnen DE's in bepaalde omstandigheden voorkomen dat certificaten verkeerd worden uitgegeven. CAA-records hebben drie eigenschappen:
* **Vlaggen**: Dit is een geheel getal tussen 0 en 255, gebruikt om de kritische vlag die een speciale betekenis heeft per de [RFC](https://tools.ietf.org/html/rfc6844#section-3) vertegenwoordigen
* **Tag:** een ASCII-tekenreeks die een van de volgende handelingen kan zijn:
    * **probleem**: gebruik dit als u CA's wilt opgeven die certs mogen uitgeven (alle soorten)
    * **issuewild**: gebruik dit als u CA's wilt opgeven die certs mogen uitgeven (alleen wildcardcerts)
    * **iodef**: geef een e-mailadres of hostnaam op waarop DE's kunnen worden gemeld voor ongeautoriseerde aanvragen voor cert-uitgifteaanvragen
* **Waarde**: de waarde voor de gekozen specifieke tag

### <a name="cname-records"></a>CNAME-records

CNAME-recordsets kunnen niet worden gecombineerd met andere recordsets met dezelfde naam. U bijvoorbeeld geen CNAME-record maken met de relatieve naam 'www' en een A-record met de relatieve naam 'www' tegelijkertijd.

Omdat de zonetop (naam = '\@') altijd de NS- en SOA-recordsets bevat die zijn gemaakt toen de zone werd gemaakt, u geen CNAME-record maken dat is ingesteld op de zonetop.

Deze beperkingen vloeien voort uit de DNS-standaarden en zijn geen beperkingen van Azure DNS.

### <a name="ns-records"></a>NS-records

Het NS-record dat is ingesteld\@op de zonetop (naam ' ') wordt automatisch gemaakt met elke DNS-zone en wordt automatisch verwijderd wanneer de zone wordt verwijderd (deze kan niet afzonderlijk worden verwijderd).

Deze recordset bevat de namen van de Azure DNS-naamservers die aan de zone zijn toegewezen. U extra naamservers toevoegen aan deze NS-recordset om co-hostingdomeinen met meer dan één DNS-provider te ondersteunen. U ook de TTL en metagegevens voor deze recordset wijzigen. U de vooraf ingevulde Azure DNS-naamservers echter niet verwijderen of wijzigen. 

Dit geldt alleen voor het NS-record dat op de zonetop is gevestigd. Andere NS-recordsets in uw zone (zoals gebruikt om onderliggende zones te delegeren) kunnen zonder beperking worden gemaakt, gewijzigd en verwijderd.

### <a name="soa-records"></a>SOA-records

Een SOA-recordset wordt automatisch gemaakt aan de top\@van elke zone (naam = ' '), en wordt automatisch verwijderd wanneer de zone wordt verwijderd.  SOA-records kunnen niet afzonderlijk worden gemaakt of verwijderd.

U alle eigenschappen van de SOA-record wijzigen, behalve de eigenschap 'host', die vooraf is geconfigureerd om te verwijzen naar de primaire naamservernaam die wordt opgegeven door Azure DNS.

Het zoneserienummer in de SOA-record wordt niet automatisch bijgewerkt wanneer wijzigingen worden aangebracht in de records in de zone. Het kan handmatig worden bijgewerkt door het bewerken van de SOA record, indien nodig.

### <a name="spf-records"></a>SPF-records

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>SRV-records

[SRV-records](https://en.wikipedia.org/wiki/SRV_record) worden door verschillende services gebruikt om serverlocaties op te geven. Bij het opgeven van een SRV-record in Azure DNS:

* De *service* en *het protocol* moeten worden opgegeven als onderdeel van de recordsetnaam, vooraf bevestigd met underscores.  Bijvoorbeeld, 'sip.\_ \_tcp.name.  Voor een record op de zone apex,\@is er geen noodzaak om '' in de\_recordnaam op te geven, gewoon gebruik maken van de service en het protocol, bijvoorbeeld ' sip. \_tcp'.
* De *prioriteit*, *het gewicht,* *de poort*en het *doel* worden opgegeven als parameters van elke record in de recordset.

### <a name="txt-records"></a>TXT-records

TXT-records worden gebruikt om domeinnamen toe te passen aan willekeurige teksttekenreeksen. Ze worden gebruikt in meerdere toepassingen, met name met betrekking tot e-mailconfiguratie, zoals het [Sender Policy Framework (SPF)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) en [DomainKeys Identified Mail (DKIM).](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail)

De DNS-standaarden maken het mogelijk om één TXT-record meerdere tekenreeksen te bevatten, die elk maximaal 254 tekens lang kunnen zijn. Wanneer meerdere tekenreeksen worden gebruikt, worden ze samengevoegd door clients en behandeld als één tekenreeks.

Wanneer u de Azure DNS REST API aanroept, moet u elke TXT-tekenreeks afzonderlijk opgeven.  Wanneer u de Azure-portal-, PowerShell- of CLI-interfaces gebruikt, moet u één tekenreeks per record opgeven, die indien nodig automatisch wordt onderverdeeld in segmenten met 254 tekens.

De meerdere tekenreeksen in een DNS-record mogen niet worden verward met de meerdere TXT-records in een TXT-recordset.  Een TXT-recordset kan meerdere records bevatten, *die elk* meerdere tekenreeksen kunnen bevatten.  Azure DNS ondersteunt een totale tekenreekslengte van maximaal 1024 tekens in elke TXT-recordset (voor alle records samen).

## <a name="tags-and-metadata"></a>Tags en metagegevens

### <a name="tags"></a>Tags

Tags zijn een lijst met naam-waardeparen en worden door Azure Resource Manager gebruikt om resources te labelen.  Azure Resource Manager gebruikt tags om gefilterde weergaven van uw Azure-factuur in te schakelen en stelt u ook in staat om een beleid in te stellen waarop tags vereist zijn. Zie voor meer informatie over tags [Tags gebruiken om uw Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md).

Azure DNS ondersteunt het gebruik van Azure Resource Manager-tags op DNS-zonebronnen.  Het ondersteunt geen tags op DNS-recordsets, hoewel als een alternatieve 'metadata' wordt ondersteund op DNS-recordsets zoals hieronder uitgelegd.

### <a name="metadata"></a>Metagegevens

Als alternatief voor het opnemen van settags ondersteunt Azure DNS het annoteren van recordsets met behulp van 'metadata'.  Net als bij tags u metagegevens naamwaardeparen koppelen aan elke recordset.  Dit kan handig zijn, bijvoorbeeld om het doel van elke recordset vast te leggen.  In tegenstelling tot tags kunnen metagegevens niet worden gebruikt om een gefilterde weergave van uw Azure-factuur te bieden en kunnen ze niet worden opgegeven in een Azure Resource Manager-beleid.

## <a name="etags"></a>Etags

Stel dat twee personen of twee processen tegelijkertijd een DNS-record proberen te wijzigen. Welke wint? En weet de winnaar dat ze veranderingen hebben overschreven die door iemand anders zijn gemaakt?

Azure DNS gebruikt Etags om gelijktijdige wijzigingen in dezelfde bron veilig af te handelen. Etags staan los van [Azure Resource Manager 'Tags'.](#tags) Aan elke DNS-bron (zone of recordset) is een Etag gekoppeld. Wanneer een resource wordt opgehaald, wordt de Etag ook opgehaald. Wanneer u een bron bijwerkt, u ervoor kiezen om de Etag terug te geven, zodat Azure DNS kan controleren of de Etag op de server overeenkomt. Aangezien elke update voor een resource resulteert in een regenereerde Etag, geeft een Etag-mismatch aan dat er een gelijktijdige wijziging heeft plaatsgevonden. Etags kunnen ook worden gebruikt bij het maken van een nieuwe bron om ervoor te zorgen dat de bron nog niet bestaat.

Azure DNS PowerShell gebruikt Etags standaard om gelijktijdige wijzigingen in zones en recordsets te blokkeren. De optionele *-Overschrijfschakelaar* kan worden gebruikt om Etag-controles te onderdrukken, in welk geval gelijktijdige wijzigingen die zich hebben voorgedaan, worden overschreven.

Op het niveau van de Azure DNS REST API worden Etags opgegeven met BEHULP van HTTP-headers.  Hun gedrag wordt gegeven in de volgende tabel:

| Header | Gedrag |
| --- | --- |
| Geen |PUT slaagt altijd (geen Etag-controles) |
| If-match \<etag> |PUT slaagt alleen als er resource bestaat en Etag overeenkomt |
| Als-match * |PUT slaagt alleen als er resource bestaat |
| Als-niemand-match * |PUT slaagt alleen als resource niet bestaat |


## <a name="limits"></a>Limieten

De volgende standaardlimieten zijn van toepassing bij het gebruik van Azure DNS:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Volgende stappen

* Als u Azure DNS wilt gaan gebruiken, leest u hoe u [een DNS-zone maakt](dns-getstarted-create-dnszone-portal.md) en [DNS-records maakt.](dns-getstarted-create-recordset-portal.md)
* Als u een bestaande DNS-zone wilt migreren, leest u hoe u [een DNS-zonebestand importeert en exporteert.](dns-import-export.md)
