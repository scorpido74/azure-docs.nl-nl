---
title: Aanbevelingen voor Azure Security Center voor netwerken
description: In dit artikel vindt u de aanbevelingen voor de beveiliging van Azure Security Center waarmee u uw netwerk bronnen kunt beveiligen.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2019
ms.author: memildin
ms.openlocfilehash: 1b8d84286d14949c007d1ece3d089a7606464aaf
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74781967"
---
# <a name="network-recommendations---reference-guide"></a>Aanbevelingen voor netwerken-Naslag Gids

In dit artikel vindt u de volledige lijst met aanbevelingen die u in Azure Security Center kunt zien met betrekking tot de topologie van uw netwerk en uw Internet gerichte eind punten.

Zie [hier](security-center-network-recommendations.md)voor een uitleg over het vinden van deze oplossingen en hoe u deze kunt oplossen.

## <a name="network-recommendations"></a>Aanbevelingen voor netwerken

|Naam aanbeveling|Beschrijving|Ernst|Beveiligingsscore|Resourcetype|
|----|----|----|----|----|----|
|Netwerk beveiligings groepen op het subnetniveau moeten zijn ingeschakeld|Netwerk beveiligings groepen inschakelen voor het beheren van netwerk toegang van resources die zijn geïmplementeerd in uw subnetten.|Hoog/gemiddeld|30|Subnet|
|Virtuele machines moeten worden gekoppeld aan een netwerk beveiligings groep|Schakel netwerk beveiligings groepen in om de netwerk toegang van uw virtuele machines te beheren.|Hoog/gemiddeld|30|Virtuele machine|
|Toegang moet worden beperkt voor strikte netwerk beveiligings groepen met Internet gerichte Vm's|Beperk de netwerk beveiligings groepen van uw Internet gerichte Vm's door de toegang tot uw bestaande regels voor toestaan te beperken.|Hoog|20|Virtuele machine|
|De regels voor webtoepassingen op IaaS Nsg's moeten worden gehard|De netwerk beveiligings groep (NSG) van uw virtuele machines waarop webtoepassingen worden uitgevoerd, beveiligen met NSG-regels die zich te maken hebben met betrekking tot de poorten van de web-app.|Hoog|20|Virtuele machine|
|Toegang tot App Services moet worden beperkt|Beperk de toegang tot uw App Services door de netwerk configuratie te wijzigen, om inkomend verkeer te weigeren van bereiken die te breed zijn.|Hoog|10|App service|
|Beheer poorten moeten worden gesloten op uw virtuele machines|Beperk de netwerk beveiligings groep van uw virtuele machines om de toegang tot beheer poorten te beperken.|Hoog|10|Virtuele machine|
DDoS Protection standaard moet zijn ingeschakeld|Beveilig virtuele netwerken met toepassingen met open bare Ip's door de DDoS Protection Service-standaard in te scha kelen. DDoS Protection maakt het beperken van netwerk-en protocol aanvallen mogelijk.|Hoog|10|Virtueel netwerk|
|Door sturen via IP op uw virtuele machine moet worden uitgeschakeld|Schakel door sturen via IP uit. Als door sturen via IP is ingeschakeld op de NIC van een virtuele machine, kan de computer verkeer ontvangen dat is geadresseerd aan andere bestemmingen. Door sturen via IP is zelden vereist (bijvoorbeeld wanneer u de virtuele machine als een virtueel netwerk apparaat gebruikt), en dit moet daarom worden gecontroleerd door het netwerk beveiligings team.|Middelgroot|10|Virtuele machine|
|Webtoepassing mag alleen toegankelijk zijn via HTTPS|Schakel de toegang ' alleen HTTPS ' in voor webtoepassingen. Het gebruik van HTTPS garandeert Server/service-verificatie en beveiligt gegevens tijdens de overdracht van aanvallen met een netwerklaag.|Middelgroot|20|Webtoepassing|
|Just-in-time-netwerk toegangs beheer moet worden toegepast op virtuele machines|Pas een just-in-time-toegangs beheer (VM) toe om de toegang tot geselecteerde poorten permanent te vergren delen en stel gemachtigde gebruikers in staat om ze te openen via JIT, gedurende een beperkte periode.|Hoog|20|Virtuele machine|
|Functie-apps moeten alleen toegankelijk zijn via HTTPS|Schakel de toegang ' alleen HTTPS ' in voor functie-apps. Het gebruik van HTTPS garandeert Server/service-verificatie en beveiligt gegevens tijdens de overdracht van aanvallen met een netwerklaag.|Middelgroot|20|Function App|
|Beveiligde overdracht naar opslag accounts moet zijn ingeschakeld|Schakel beveiligde overdracht naar opslag accounts in. Beveiligde overdracht is een optie die ervoor zorgt dat uw opslag account alleen aanvragen van beveiligde verbindingen (HTTPS) accepteert. Het gebruik van HTTPS zorgt voor verificatie tussen de server en de service en beveiligt de door Voer van gegevens via netwerklaag aanvallen, zoals man-in-the-Middle, inkomend en inkomend en sessie overname.|Hoog|20|Opslagaccount|


## <a name="next-steps"></a>Volgende stappen
Zie het volgende voor meer informatie over de aanbevelingen die van toepassing zijn op andere Azure-resource typen:

* [Identiteit en toegang bewaken](security-center-identity-access.md)
* [Uw computers en toepassingen beveiligen](security-center-virtual-machine-protection.md)
* [Uw Azure SQL-service beveiligen](security-center-sql-service-recommendations.md)

