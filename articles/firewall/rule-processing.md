---
title: Regels voor de logicaverwerking in Azure Firewall
description: Azure Firewall heeft NAT-regels, netwerk regels en toepassings regels. De regels worden verwerkt volgens het regel type.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/26/2020
ms.author: victorh
ms.openlocfilehash: 69c0c13c7027707cdadb2f1f1de9cc1655c9c625
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77621885"
---
# <a name="azure-firewall-rule-processing-logic"></a>Regels voor de logicaverwerking in Azure Firewall
U kunt NAT-regels, netwerk regels en toepassings regels configureren op Azure Firewall. De regels worden verwerkt volgens het regel type. 

> [!NOTE]
> Als u filtering op basis van bedreigings informatie inschakelt, zijn deze regels de hoogste prioriteit en worden ze altijd eerst verwerkt. Threat-Intelligence-filtering kan verkeer weigeren voordat er geconfigureerde regels worden verwerkt. Zie [Azure firewall op Threat Intelligence gebaseerde filtering](threat-intel.md)voor meer informatie.

## <a name="outbound"></a>Uitgaand

### <a name="network-rules-and-applications-rules"></a>Netwerk regels en toepassings regels

Als u netwerk regels en toepassings regels configureert, worden netwerk regels in volg orde van prioriteit toegepast vóór toepassings regels. De regels worden afgesloten. Dus als er een overeenkomst wordt gevonden in een netwerk regel, worden er geen andere regels verwerkt.  Als er geen overeenkomst met de netwerk regel is en als het Protocol HTTP, HTTPS of MSSQL is, wordt het pakket vervolgens geëvalueerd op basis van de toepassings regels in volg orde van prioriteit. Als er nog geen overeenkomst wordt gevonden, wordt het pakket vergeleken met de [verzameling van de infrastructuur regel](infrastructure-fqdns.md). Als er dan nog steeds geen overeenkomende regel is, wordt het pakket standaard afgewezen.

## <a name="inbound"></a>Inkomend

### <a name="nat-rules"></a>NAT-regels

Inkomende Internet connectiviteit kan worden ingeschakeld door DNAT (Destination Network Address Translation) te configureren, zoals beschreven in [zelf studie: inkomend verkeer filteren met Azure firewall DNAT met behulp van de Azure Portal](tutorial-firewall-dnat.md). NAT-regels worden vóór netwerk regels op prioriteit toegepast. Als er een overeenkomst wordt gevonden, wordt er een impliciet overeenkomende netwerk regel toegevoegd om het vertaalde verkeer toe te staan. U kunt dit gedrag overschrijven door expliciet een verzameling netwerkregels toe te voegen met regels voor weigeren die overeenkomen met het omgezette verkeer.

Toepassings regels worden niet toegepast op binnenkomende verbindingen. Als u echter het binnenkomende HTTP/S-verkeer wilt filteren, gebruikt u Web Application firewall (WAF). Zie [Wat is Azure Web Application firewall?](../web-application-firewall/overview.md) voor meer informatie.

## <a name="examples"></a>Voorbeelden

In de volgende voor beelden ziet u de resultaten van sommige van deze regel combinaties.

### <a name="example-1"></a>Voorbeeld 1

De verbinding met google.com is toegestaan vanwege een overeenkomende netwerk regel.

**Netwerk regel**

- Actie: toestaan


|naam  |Protocol  |Bron type  |Bron  |Doel type  |Doel adres  |Doelpoorten|
|---------|---------|---------|---------|----------|----------|--------|
|Toestaan-Web     |TCP|IP-adres|*|IP-adres|*|80,443

**Toepassings regel**

- Actie: weigeren

|naam  |Bron type  |Bron  |Protocol: poort|Doel-FQDN-naam|
|---------|---------|---------|---------|----------|----------|
|Weigeren: Google     |IP-adres|*|http: 80, https: 443|google.com

**Daardoor**

De verbinding met google.com is toegestaan omdat het pakket overeenkomt met de regel voor het *toestaan* van webnetwerk. Regel verwerking stopt op dit moment.

### <a name="example-2"></a>Voorbeeld 2

SSH-verkeer wordt geweigerd omdat de verzameling netwerk regels voor *weigeren* van een hogere prioriteit de waarde blokkeert.

**Verzameling van netwerk regels 1**

- Naam: toestaan-verzameling
- Prioriteit: 200
- Actie: toestaan

|naam  |Protocol  |Bron type  |Bron  |Doel type  |Doel adres  |Doelpoorten|
|---------|---------|---------|---------|----------|----------|--------|
|Toestaan-SSH     |TCP|IP-adres|*|IP-adres|*|22

**Verzameling van netwerk regels 2**

- Naam: weigeren-verzameling
- Prioriteit: 100
- Actie: weigeren

|naam  |Protocol  |Bron type  |Bron  |Doel type  |Doel adres  |Doelpoorten|
|---------|---------|---------|---------|----------|----------|--------|
|Weigeren-SSH     |TCP|IP-adres|*|IP-adres|*|22

**Daardoor**

SSH-verbindingen worden geweigerd omdat een netwerk regel verzameling met een hogere prioriteit deze blokkeert. Regel verwerking stopt op dit moment.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [implementeren en configureren van een Azure firewall](tutorial-firewall-deploy-portal.md).