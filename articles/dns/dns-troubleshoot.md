---
title: Troubleshooting guide - Azure DNS
description: In this learning path, get started troubleshooting common issues with Azure DNS
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 09/20/2019
ms.author: allensu
ms.openlocfilehash: b5fedba7b739c07a37f3aabf75ddd8ca465ba73b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74210944"
---
# <a name="azure-dns-troubleshooting-guide"></a>Azure DNS troubleshooting guide

This article provides troubleshooting information for common Azure DNS questions.

If these steps don't resolve your issue, you can also search for or post your issue on our [community support forum on MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WAVirtualMachinesVirtualNetwork). Or, you can open an Azure support request.


## <a name="i-cant-create-a-dns-zone"></a>I can't create a DNS zone

Probeer een of meer van de volgende stappen om veelvoorkomende problemen op te lossen:

1.  Review the Azure DNS audit logs to determine the failure reason.
2.  Elke DNS-zonenaam moet uniek zijn binnen de resourcegroep. That is, two DNS zones with the same name can't share a resource group. Gebruik een andere zonenaam of een andere resourcegroep.
3.  U ziet mogelijk de foutmelding 'Het maximumaantal zones in abonnement {abonnements-id} is bereikt of overschreden'. Gebruik een ander Azure-abonnement, verwijder enkele zones of neem contact op met de ondersteuning van Azure om uw abonnementslimiet te verhogen.
4.  U ziet mogelijk de foutmelding 'De zone {zonenaam} is niet beschikbaar'. Dit betekent dat Azure DNS geen naamservers kan toewijzen voor deze DNS-zone. Gebruik dan een andere zonenaam. Or, if you are the domain name owner you can contact Azure support to allocate name servers for you.


### <a name="recommended-articles"></a>Recommended articles

* [DNS-zones en -records](dns-zones-records.md)
* [Een DNS-zone maken](dns-getstarted-create-dnszone-portal.md)

## <a name="i-cant-create-a-dns-record"></a>Ik kan geen DNS-record maken

Probeer een of meer van de volgende stappen om veelvoorkomende problemen op te lossen:

1.  Review the Azure DNS audit logs to determine the failure reason.
2.  Bestaat de recordset al?  Azure DNS beheert records als *recordsets*. Hierin zijn records met dezelfde naam en van hetzelfde type opgenomen. Als er al een record met dezelfde naam en van hetzelfde type bestaat, kunt u nog een dergelijke record toevoegen door de bestaande recordset te bewerken.
3.  Probeert u een record te maken in de apex (het hoofdniveau) van de DNS-zone? Dan is het de DNS-conventie om het @-teken te gebruiken als recordnaam. Also note that the DNS standards don't permit CNAME records at the zone apex.
4.  Is er sprake van een CNAME-conflict?  The DNS standards don't allow a CNAME record with the same name as a record of any other type. Als u een bestaande CNAME hebt, kunt u geen record maken met dezelfde naam maar van een ander type.  Op dezelfde manier kunt u ook geen CNAME maken als de naam overeenkomt met een bestaande record van een ander type. Verhelp het conflict door de andere record te verwijderen of een andere recordnaam te kiezen.
5.  Hebt u de limiet voor het aantal toegestane recordsets in een DNS-zone bereikt? Het huidige aantal recordsets en het maximumaantal recordsets worden weergegeven in Azure Portal onder de eigenschappen van de zone. If you've reached this limit, then either delete some record sets or contact Azure Support to raise your record set limit for this zone, then try again. 


### <a name="recommended-articles"></a>Recommended articles

* [DNS-zones en -records](dns-zones-records.md)
* [Een DNS-zone maken](dns-getstarted-create-dnszone-portal.md)



## <a name="i-cant-resolve-my-dns-record"></a>Ik kan mijn DNS-record niet omzetten

De DNS-naamomzetting vereist meerdere stappen en kan om verschillende redenen mislukken. Aan de hand van de volgende stappen kunt u onderzoeken waarom de DNS-omzetting mislukt voor een DNS-record in een zone in Azure DNS.

1.  Controleer of de DNS-records correct zijn geconfigureerd in Azure DNS. Controleer of de zonenaam, de naam en het type van de DNS-records in Azure Portal correct zijn.
2.  Controleer of de DNS-records correct zijn omgezet in de Azure DNS-naamservers.
    - Als u DNS-query's maakt op uw lokale pc, tonen de resultaten in de cache mogelijk niet de huidige status van de naamservers.  Daarnaast maken bedrijfsnetwerken vaak gebruik van DNS-proxyservers, waardoor DNS-query's niet worden doorgestuurd naar specifieke naamservers.  U kunt deze problemen voorkomen door een webservice voor naamomzetting te gebruiken, zoals [digwebinterface](https://digwebinterface.com).
    - Zorg er wel voor dat u de juiste naamservers voor uw DNS-zone opgeeft, zoals weergegeven in Azure Portal.
    - Controleer of de DNS-naam (dit is de volledig gekwalificeerde naam, inclusief de zonenaam) en het recordtype kloppen.
3.  Controleer of de DNS-domeinnaam juist is [gedelegeerd naar de Azure DNS-naamservers](dns-domain-delegation.md). Er zijn [veel websites van derden die de DNS-delegering kunnen valideren](https://www.bing.com/search?q=dns+check+tool). Dit is een delegeringstest voor de *zone*. Geef dus alleen de naam van de DNS-zone op en niet de volledig gekwalificeerde recordnaam.
4.  Nadat u het bovenstaande hebt voltooid, zou uw DNS-record correct moeten worden omgezet. U kunt dit controleren door opnieuw [digwebinterface](https://digwebinterface.com) te gebruiken, maar nu met de standaardinstellingen van de naamserver.


### <a name="recommended-articles"></a>Recommended articles

* [Een domein delegeren naar Azure DNS](dns-domain-delegation.md)



## <a name="how-do-i-specify-the-service-and-protocol-for-an-srv-record"></a>Hoe geef ik de 'service' en het 'protocol' voor een SRV-record op?

Azure DNS beheert DNS-records als recordsets. Hierin zijn records met dezelfde naam en van hetzelfde type opgenomen. Voor een SRV-recordset moeten de 'service' en het 'protocol' worden opgegeven als onderdeel van de recordsetnaam. De andere SRV-parameters ('prioriteit', 'gewicht', 'poort' en 'doel') worden afzonderlijk opgegeven voor elke record in de recordset.

Voorbeeld van SRV-recordnamen (servicenaam 'sip', protocol 'tcp'):

- \_sip.\_tcp (er wordt een record gemaakt in de apex van de zone)
- \_sip.\_tcp.sipservice (er wordt een recordset gemaakt met de naam 'sipservice')

### <a name="recommended-articles"></a>Recommended articles

* [DNS-zones en -records](dns-zones-records.md)
* [DNS-recordsets en -records maken met Azure Portal](dns-getstarted-create-recordset-portal.md)
* [SRV-recordtype (Wikipedia)](https://en.wikipedia.org/wiki/SRV_record)


## <a name="next-steps"></a>Volgende stappen

* Learn about [Azure DNS zones and records](dns-zones-records.md)
* To start using Azure DNS, learn how to [create a DNS zone](dns-getstarted-create-dnszone-portal.md) and [create DNS records](dns-getstarted-create-recordset-portal.md).
* To migrate an existing DNS zone, learn how to [import and export a DNS zone file](dns-import-export.md).

