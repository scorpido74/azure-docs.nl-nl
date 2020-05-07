---
title: Facturering voor commerciële Marketplace in azure Marketplace
description: Meer informatie over de publicatie opties voor Marketplace en de facturerings modellen voor trans acties voor de commerciële Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/12/2019
ms.openlocfilehash: deb4f3f1bb17bff0b09b2e4f79ceb967d4d7ff59
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744960"
---
# <a name="commercial-marketplace-billing-with-transact-billing-models"></a>Commercial Marketplace billing with Transact billing models

Dit artikel heeft betrekking op commerce-gerelateerde onderwerpen voor de commerciële Marketplace:

- [Publicatie opties voor Marketplace](#marketplace-publishing-options)
- [Overzicht van Transact-algemeen](#transact-general-overview)
- [Facturerings modellen voor trans acties](#transact-billing-models)

## <a name="marketplace-publishing-options"></a>Publicatie opties voor Marketplace

Commerciële Marketplace biedt verschillende publicatie opties voor uitgevers.

### <a name="list-and-trial-publishing-options"></a>Publicatie opties voor lijst en proef versie

Uitgevers kunnen gebruikmaken van de lijst, proef versie en uw eigen licentie (BYOL) publicatie opties voor promotie-en gebruikers verwervings doeleinden. Met deze opties neemt micro soft niet rechtstreeks deel aan de software licentie transacties van de uitgever en zijn er geen transactie kosten gekoppeld. Uitgevers zijn verantwoordelijk voor de ondersteuning van alle aspecten van de software licentie transactie, inclusief, maar niet beperkt tot: order, verwerking, meting, facturering, facturering, betaling en verzameling. Met de lijst-en proef publicatie opties blijven er 100% van de licentie kosten voor Publisher-software van de klant ontvangen.

### <a name="transact-publishing-option"></a>Optie voor het publiceren van Transact

Naast de lijst met opties voor de proef versie, is de optie voor het publiceren van publicaties beschikbaar voor uitgevers. Deze optie maakt gebruik van de wereld wijd beschik bare commerce mogelijkheden van micro soft en stelt micro soft in staat Cloud Marketplace-trans acties te hosten namens de uitgever.

## <a name="transact-general-overview"></a>Overzicht van Transact-algemeen

Wanneer u de optie voor het publiceren van Transact gebruikt, schakelt micro soft de verkoop van software van derden in en de implementatie van sommige aanbiedings typen aan het Azure-abonnement van de klant. De uitgever moet rekening houden met de facturering van de kosten voor de infra structuur en de eigen software licentie kosten van de uitgever bij het selecteren van een facturerings model en aanbiedings type.

De optie voor het publiceren van Transact wordt momenteel ondersteund voor de volgende aanbiedings typen: virtuele machines, Azure-toepassingen en SaaS-toepassingen.

![Trans acties in azure Marketplace](./media/transact-amp.png)

### <a name="billing-infrastructure-costs"></a>Kosten van facturerings infrastructuur

#### <a name="for-virtual-machines-and-azure-applications"></a>Voor virtuele machines en Azure-toepassingen

Voor virtuele machines en Azure-toepassingen worden de gebruiks kosten voor Azure-infra structuur in rekening gebracht voor het Azure-abonnement van de klant. De kosten voor het gebruik van de infra structuur zijn geprijsd en worden afzonderlijk van de licentie kosten van de software provider weer gegeven op de factuur van de klant.

#### <a name="for-saas-apps"></a>Voor SaaS-apps

Voor SaaS-apps moet de uitgever rekening met de gebruiks kosten voor Azure-infra structuur en software licentie kosten als één kosten item. Het wordt weer gegeven als een vast bedrag voor de klant. Het gebruik van de Azure-infra structuur wordt rechtstreeks beheerd en gefactureerd met de partner. De werkelijke gebruiks kosten voor de infra structuur zijn niet zichtbaar voor de klant. Uitgevers willen doorgaans de gebruiks kosten voor Azure-infra structuur bundelen in hun prijzen voor software licenties. Software licentie kosten worden niet gemeten of verbruikt.

## <a name="transact-billing-models"></a>Facturerings modellen voor trans acties

Afhankelijk van de gebruikte transactie optie kunnen de software licentie kosten van de uitgever als volgt worden weer gegeven:

- *Gratis*: er worden geen kosten in rekening gebracht voor software licenties.
- *Bring your own License (BYOL)*: eventuele toepasselijke kosten voor software licenties worden rechtstreeks beheerd tussen de uitgever en de klant. Micro soft passeert alleen de gebruiks kosten voor Azure-infra structuur. (Alleen Virtual Machines en Azure-toepassingen.)
- *Betalen naar*gebruik: de kosten voor software licenties worden gepresenteerd als een prijs per uur, per kern (vCPU) op basis van de gebruikte Azure-infra structuur. Dit model is alleen van toepassing op virtuele machines en Azure-toepassingen.
- *Abonnements prijzen*: de kosten voor software licenties worden weer gegeven als maandelijks of jaarlijks, terugkerend bedrag in rekening gebracht als een vast tarief of per seat. Dit model is alleen van toepassing op SaaS-apps en door Azure toepassingen beheerde apps.
- *Gratis proef versie*: er worden gedurende 30 dagen of 90 dagen geen kosten in rekening gebracht voor software licenties.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Gratis en BYOL-prijzen (uw eigen licentie)

Bij het publiceren van een gratis of uw eigen licentie transactie aanbieding speelt micro soft geen rol om de verkoop transactie voor uw software licentie kosten te vergemakkelijken. Net als de opties voor het publiceren van een lijst en proef versie, houdt de uitgever 100% van de software licentie kosten in rekening.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Prijzen voor betalen per gebruik en abonnementen (op site gebaseerd)

Wanneer u een betalen per gebruik-of abonnements transactie aanbieding publiceert, biedt micro soft de technologie en services voor het verwerken van software licentie-aankopen, retour neren en terugbrengen van back-ups. In dit scenario wordt micro soft door de uitgever geautoriseerd om als agent te fungeren voor deze doel einden. De uitgever stelt micro soft in staat om de Software Licensing-trans actie te vergemakkelijken, waarbij de toewijzing wordt behouden als verkoper, provider, distributeur en licentie gever.

Micro soft stelt klanten in staat om uitgever software te best Ellen, te bestemmen en te gebruiken, onder voor waarden van zowel de commerciële Marketplace van micro soft als de gebruiksrecht overeenkomst van de uitgever. Uitgevers moeten hun gebruiksrecht overeenkomst voor eind gebruikers opgeven of het [standaard contract](https://docs.microsoft.com/azure/marketplace/standard-contract) selecteren bij het maken van de aanbieding.

### <a name="free-software-trials"></a>Gratis software-experimenten

Voor scenario's voor het publiceren van publicaties kan de uitgever een software licentie gratis beschikbaar maken gedurende 30 dagen of 90 dagen. Deze functie korting omvat niet de kosten van het gebruik van Azure-infra structuur dat wordt aangestuurd door het gebruik van de partner oplossing.

### <a name="private-offers"></a>Persoonlijke aanbiedingen

Uitgevers kunnen niet alleen gebruikmaken van aanbiedings typen en facturerings modellen om een aanbieding te geld verdienenen, maar ook een persoonlijke aanbieding, compleet met onderhandelde prijzen of aangepaste configuraties. Persoonlijke aanbiedingen worden ondersteund door alle drie de opties voor het publiceren van trans acties.

Met deze optie kunnen hogere of lagere prijzen worden gemaakt dan voor de openbaar beschik bare aanbieding. Persoonlijke aanbiedingen kunnen worden gebruikt om korting te bieden of een Premium toe te voegen voor een aanbieding. Persoonlijke aanbiedingen kunnen aan een of meer klanten beschikbaar worden gesteld door een overzicht van hun Azure-abonnement op het niveau van de aanbieding.

### <a name="examples"></a>Voorbeelden

#### <a name="pay-as-you-go"></a>Betalen per gebruik

* Als u de optie betalen naar gebruik inschakelt, hebt u de volgende kosten structuur.

|De licentie kosten  | $1,00 per uur  |
|---------|---------|
|Kosten voor Azure-gebruik (D1/1-core)    |   $0,14 per uur     |
|*Klant wordt gefactureerd door micro soft*    |  *$1,14 per uur*       |

* In dit scenario $1,14 factureert micro soft per uur voor het gebruik van uw gepubliceerde VM-installatie kopie.

|Micro soft-facturen  | $1,14 per uur  |
|---------|---------|
|Micro soft betaalt u 80% van uw licentie kosten|   $0,80 per uur     |
|Micro soft bewaart 20% van de licentie kosten  |  $0,20 per uur       |
|Micro soft bewaart 100% van de kosten voor Azure-gebruik | $0,14 per uur |

### <a name="bring-your-own-license-byol"></a>Neem uw eigen licentie mee (BYOL)

* Als u de optie BYOL inschakelt, hebt u de volgende kosten structuur.

|De licentie kosten  | Licentie kosten die door u worden onderhandeld en gefactureerd  |
|---------|---------|
|Kosten voor Azure-gebruik (D1/1-core)    |   $0,14 per uur     |
|*Klant wordt gefactureerd door micro soft*    |  *$0,14 per uur*       |

* In dit scenario $0,14 factureert micro soft per uur voor het gebruik van uw gepubliceerde VM-installatie kopie.

|Micro soft-facturen  | $0,14 per uur  |
|---------|---------|
|Micro soft behoudt de kosten voor Azure-gebruik    |   $0,14 per uur     |
|Micro soft bewaart 0% van de licentie kosten   |  $0,00 per uur       |

### <a name="saas-app-subscription"></a>SaaS-app-abonnement

Deze optie moet worden geconfigureerd om via micro soft te worden verkocht en kan maandelijks of per jaar worden geprijsd op basis van een vast bedrag.

• Als u de optie verkopen via micro soft inschakelt voor een SaaS-aanbieding, hebt u de volgende kosten structuur.

|De licentie kosten       | $100,00 per maand  |
|--------------|---------|
|Kosten voor Azure-gebruik (D1/1-core)    | Rechtstreeks aan de uitgever gefactureerd, niet de klant |
|*Klant wordt gefactureerd door micro soft*    |  *$100,00 per maand (Opmerking: Publisher moet rekening houden met de kosten voor het ontstaan of pass-through infra structuur van de licentie kosten)*  |

* In dit scenario betaalt micro soft de $100,00 voor uw software licentie en wordt $80,00 op de Publisher uitgewisseld.
* Partners die in aanmerking komen voor de lagere kosten voor Marketplace-service, zien een gereduceerde transactie kosten voor de SaaS-aanbiedingen van mei 2019 tot juni 2020. In dit scenario betaalt micro soft de $100,00 voor uw software licentie en wordt $90,00 op de Publisher uitgewisseld.

|Micro soft-facturen  | $100,00 per maand  |
|---------|---------|
|Micro soft betaalt u 80% van uw licentie kosten <br> \*Micro soft betaalt u 90% van de licentie kosten voor alle gekwalificeerde SaaS-apps   |   $80,00 per maand <br> \*$90,00 per maand    |
|Micro soft bewaart 20% van de licentie kosten <br> \*Micro soft houdt 10% van de licentie kosten voor alle gekwalificeerde SaaS-apps.  |  $20,00 per maand <br> \*$10,00     |

**Lagere kosten voor Marketplace-service:** Voor bepaalde SaaS-producten die u op onze commerciële Marketplace publiceert, verlaagt micro soft de service kosten voor Marketplace van 20% (zoals beschreven in de overeenkomst voor micro soft Publisher) tot 10%.  Om uw product in aanmerking te komen, moet u ten minste één van uw producten door micro soft worden aangewezen als een voor bereiding op het IP-adres of door de IP-mede-verkoop prioriteit. Om dit gereduceerde service-tarief voor de Marketplace voor de maand te ontvangen, moet aan de voor waarden worden voldaan ten minste vijf (5) werk dagen vóór het einde van de vorige kalender maand. Lagere kosten voor Marketplace-service zijn niet van toepassing op Vm's, beheerde apps of andere producten die beschikbaar worden gesteld via onze commerciële Marketplace.  Dit lagere tarief voor Marketplace-Services is beschikbaar voor gekwalificeerde aanbiedingen, met licentie kosten die door micro soft zijn verzameld tussen 1 mei 2019 en 30 juni 2020.  Na deze periode wordt de service kosten voor de Marketplace teruggebracht naar de normale hoeveelheid.
