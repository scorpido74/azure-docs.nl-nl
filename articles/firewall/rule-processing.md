---
title: Regels voor de logicaverwerking in Azure Firewall
description: Azure Firewall heeft NAT-regels, netwerkregels en toepassingenregels. De regels worden verwerkt op basis van het regeltype.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 04/10/2020
ms.author: victorh
ms.openlocfilehash: 93677b3e473ab825665fed5590ac345a8cfcc300
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113423"
---
# <a name="azure-firewall-rule-processing-logic"></a>Regels voor de logicaverwerking in Azure Firewall
U NAT-regels, netwerkregels en toepassingenregels configureren op Azure Firewall. Regelverzamelingen worden verwerkt volgens het regeltype in prioriteitsvolgorde, lagere getallen tot hogere getallen van 100 tot 65.000. Een regelverzamelingsnaam kan alleen letters, cijfers, onderscores, perioden of koppeltekens hebben. Het moet beginnen met een letter of nummer, en eindigen met een letter, nummer of underscore. De maximale naamlengte is 80 tekens.

Het is het beste om in eerste instantie de prioriteitsnummers van uw regelverzameling in 100 stappen te plaatsen (100, 200, 300, enzovoort), zodat u ruimte hebt om indien nodig meer regelverzamelingen toe te voegen.

> [!NOTE]
> Als u filtering op basis van bedreigingsinformatie inschakelt, hebben deze regels de hoogste prioriteit en worden ze altijd eerst verwerkt. Filtering op bedreigingsinformatie kan verkeer weigeren voordat geconfigureerde regels worden verwerkt. Zie [Azure Firewall threat intelligence-gebaseerde filtering](threat-intel.md)voor meer informatie.

## <a name="outbound-connectivity"></a>Uitgaande connectiviteit

### <a name="network-rules-and-applications-rules"></a>Regels voor netwerkregels en toepassingen

Als u netwerkregels en toepassingsregels configureert, worden netwerkregels toegepast in prioriteitsvolgorde vóór toepassingsregels. De regels lopen af. Dus als een overeenkomst wordt gevonden in een netwerkregel, worden er geen andere regels verwerkt.  Als er geen netwerkregelovereenkomst is en als het protocol HTTP, HTTPS of MSSQL is, wordt het pakket vervolgens geëvalueerd door de toepassingsregels in prioriteitsvolgorde. Als er nog steeds geen overeenkomst wordt gevonden, wordt het pakket geëvalueerd aan de hand van de [verzameling infrastructuurregels.](infrastructure-fqdns.md) Als er dan nog steeds geen overeenkomende regel is, wordt het pakket standaard afgewezen.

## <a name="inbound-connectivity"></a>Binnenkomende connectiviteit

### <a name="nat-rules"></a>NAT-regels

Inkomende internetverbinding kan worden ingeschakeld door de vertaling van het doelnetwerkadres (DNAT) te configureren zoals beschreven in [Zelfstudie: Filter binnenkomend verkeer met Azure Firewall DNAT met behulp van de Azure-portal.](tutorial-firewall-dnat.md) NAT-regels worden toegepast in prioriteit vóór netwerkregels. Als er een overeenkomst wordt gevonden, wordt een impliciete bijbehorende netwerkregel toegevoegd om het vertaalde verkeer toe te staan. U kunt dit gedrag overschrijven door expliciet een verzameling netwerkregels toe te voegen met regels voor weigeren die overeenkomen met het omgezette verkeer.

Toepassingsregels worden niet toegepast voor binnenkomende verbindingen. Dus als u binnenkomend HTTP/S-verkeer wilt filteren, moet u Web Application Firewall (WAF) gebruiken. Zie Wat is Azure Web Application Firewall voor meer [informatie?](../web-application-firewall/overview.md)

## <a name="examples"></a>Voorbeelden

In de volgende voorbeelden worden de resultaten van sommige van deze regelcombinaties weergegeven.

### <a name="example-1"></a>Voorbeeld 1

Verbinding met google.com is toegestaan vanwege een overeenkomende netwerkregel.

**Netwerkregel**

- Actie: Toestaan


|name  |Protocol  |Brontype  |Bron  |Doeltype  |Bestemmingsadres  |Doelpoorten|
|---------|---------|---------|---------|----------|----------|--------|
|Toestaan-web     |TCP|IP-adres|*|IP-adres|*|80.443

**Toepassingsregel**

- Actie: Weigeren

|name  |Brontype  |Bron  |Protocol:Poort|FQDN's targeten|
|---------|---------|---------|---------|----------|----------|
|Deny-google     |IP-adres|*|http:80,https:443|google.com

**Resultaat**

De verbinding met google.com is toegestaan omdat het pakket overeenkomt met de regel *Allow-web* network. Regelverwerking stopt op dit punt.

### <a name="example-2"></a>Voorbeeld 2

SSH-verkeer wordt geweigerd *Deny* omdat een hogere prioriteit Deny-netwerkregelverzameling dit blokkeert.

**Verzameling van netwerkregels 1**

- Naam: Verzameling toestaan
- Prioriteit: 200
- Actie: Toestaan

|name  |Protocol  |Brontype  |Bron  |Doeltype  |Bestemmingsadres  |Doelpoorten|
|---------|---------|---------|---------|----------|----------|--------|
|Allow-SSH     |TCP|IP-adres|*|IP-adres|*|22

**Verzameling van netwerkregels 2**

- Naam: Deny-collectie
- Prioriteit: 100
- Actie: Weigeren

|name  |Protocol  |Brontype  |Bron  |Doeltype  |Bestemmingsadres  |Doelpoorten|
|---------|---------|---------|---------|----------|----------|--------|
|Deny-SSH Deny-SSH     |TCP|IP-adres|*|IP-adres|*|22

**Resultaat**

SSH-verbindingen worden geweigerd omdat een netwerkregelverzameling met een hogere prioriteit deze blokkeert. Regelverwerking stopt op dit punt.

## <a name="rule-changes"></a>Regelwijzigingen

Als u een regel wijzigt om eerder toegestaan verkeer te weigeren, worden alle relevante bestaande sessies verwijderd.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [implementeren en configureren van een Azure Firewall](tutorial-firewall-deploy-portal.md).