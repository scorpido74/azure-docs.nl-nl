---
title: Facturering op marktplaatsen | Azure Marketplace
description: Dit artikel behandelt handelsgerelateerde onderwerpen voor commerciële marktplaats.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/12/2019
ms.openlocfilehash: a0912e1dca63fabfe6bc546305824a1c582b9a2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279739"
---
# <a name="commercial-marketplace-billing"></a>Facturering op commerciële marktplaatsen

Dit artikel behandelt handelsgerelateerde onderwerpen voor de commerciële marktplaats:

- [Publicatieopties voor Marketplace's](#marketplace-publishing-options)
- [Algemeen overzicht van de transacties](#transact-general-overview)
- [Factureringsmodellen voor transacties](#transact-billing-models)

## <a name="marketplace-publishing-options"></a>Publicatieopties voor Marketplace's

Commerciële marktplaats biedt verschillende publicatiemogelijkheden voor uitgevers.

### <a name="list-and-trial-publishing-options"></a>Opties voor het publiceren van gegevens en proefversies

Uitgevers kunnen gebruikmaken van de lijst, trial, en breng uw eigen licentie (BYOL) publicatieopties voor promotionele en user acquisition doeleinden. Met deze opties neemt Microsoft niet rechtstreeks deel aan de softwarelicentietransacties van de uitgever en zijn er geen bijbehorende transactiekosten. Uitgevers zijn verantwoordelijk voor het ondersteunen van alle aspecten van de softwarelicentietransactie, inclusief maar niet beperkt tot: bestelling, afhandeling, meting, facturering, facturering, betaling en incasso. Met de opties voor het publiceren van de lijst en proefversies houden uitgevers 100% van de licentiekosten van uitgeverssoftware bij de klant.

### <a name="transact-publishing-option"></a>Publicatie van transacties

Naast de opties voor het publiceren van de lijst en proefversies is de optie voor het publiceren van transacties beschikbaar voor uitgevers. Deze optie maakt gebruik van de wereldwijd beschikbare commercemogelijkheden van Microsoft en stelt Microsoft in staat om cloudmarktplaatstransacties namens de uitgever te hosten.

## <a name="transact-general-overview"></a>Algemeen overzicht van de transacties

Wanneer microsoft de optie voor het publiceren van transacties gebruikt, wordt de verkoop van software van derden en de implementatie van bepaalde aanbiedingstypen naar het Azure-abonnement van de klant mogelijk. De uitgever moet bij het selecteren van een factureringsmodel en aanbiedingstype rekening houden met de facturering van infrastructuurkosten en de eigen softwarelicentiekosten van de uitgever.

De optie Transact-publicatie wordt momenteel ondersteund voor de volgende aanbiedingstypen: virtuele machines, Azure-toepassingen en SaaS-toepassingen.

![Transacties in Azure Marketplace](./media/transact-amp.png)

### <a name="billing-infrastructure-costs"></a>Kosten voor factureringsinfrastructuur

#### <a name="for-virtual-machines-and-azure-applications"></a>Voor virtuele machines en Azure-toepassingen

Voor virtuele machines en Azure-toepassingen worden de gebruikskosten voor Azure-infrastructuur gefactureerd aan het Azure-abonnement van de klant. Gebruikskosten voor infrastructuur worden apart geprijsd en gepresenteerd van de licentiekosten van de softwareprovider op de factuur van de klant.

#### <a name="for-saas-apps"></a>Voor SaaS-apps

Voor SaaS-apps moet de uitgever rekening houden met gebruikskosten voor Azure-infrastructuur en kosten voor softwarelicenties als één kostenpost. Het wordt vertegenwoordigd als een vast bedrag aan de klant. Het gebruik van de Azure-infrastructuur wordt rechtstreeks aan de partner beheerd en gefactureerd. Werkelijke gebruikskosten voor infrastructuur worden niet gezien door de klant. Uitgevers kiezen er doorgaans voor om gebruikskosten voor Azure-infrastructuur te bundelen in hun softwarelicentieprijzen. Kosten voor softwarelicenties worden niet gemeten of op basis van het verbruik.

## <a name="transact-billing-models"></a>Factureringsmodellen voor transacties

Afhankelijk van de gebruikte transactieoptie kunnen de softwarelicentiekosten van de uitgever als volgt worden gepresenteerd:

- *Gratis*: Geen kosten voor softwarelicenties.
- *Breng uw eigen licentie (BYOL)*: Alle toepasselijke kosten voor softwarelicenties worden rechtstreeks tussen de uitgever en de klant beheerd. Microsoft gaat alleen door gebruikskosten van azure-infrastructuur. (Alleen virtuele machines en Azure-toepassingen.)
- *Pay-as-you-go*: Softwarelicentiekosten worden gepresenteerd als een vCPU-prijstarief per uur (vCPU) op basis van de gebruikte Azure-infrastructuur. Dit model is alleen van toepassing op virtuele machines en Azure-toepassingen.
- *Abonnementsprijzen*: Softwarelicentiekosten worden gepresenteerd als een maandelijkse of jaarlijkse, terugkerende kosten die worden gefactureerd als een vast tarief of per stoel. Dit model is alleen van toepassing op SaaS-apps en Azure-toepassingen - Beheerde apps.
- *Gratis proefversie van software*: Geen kosten voor softwarelicenties voor 30 dagen of 90 dagen.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Gratis en bring-your-own-license (BYOL) prijzen

Bij het publiceren van een gratis of bring-your-own-license transactieaanbieding speelt Microsoft geen rol bij het faciliteren van de verkooptransactie voor uw softwarelicentiekosten. Net als de opties voor het publiceren van de lijst en proefversies, houdt de uitgever 100% van de softwarelicentiekosten.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Prijzen voor betalen per gebruik en abonnementen (op de site gebaseerd)

Bij het publiceren van een aanbieding voor betalen per gebruik of abonnementstransacties biedt Microsoft de technologie en services voor het verwerken van softwarelicentieaankopen, retourzendingen en terugboekingen. In dit scenario geeft de uitgever Microsoft toestemming om voor deze doeleinden als agent op te treden. De uitgever stelt Microsoft in staat om de softwarelicentietransactie te vergemakkelijken, met behoud van hun aanduiding als verkoper, aanbieder, distributeur en licentiegever.

Microsoft stelt klanten in staat om uitgeverssoftware te bestellen, in licentie te geven en te gebruiken, onder voorbehoud van de algemene voorwaarden van zowel de commerciële Marktplaats van Microsoft als de licentieovereenkomst voor eindgebruikers van de uitgever. Uitgevers moeten hun licentieovereenkomst voor eindgebruikers opgeven of het [standaardcontract](https://docs.microsoft.com/azure/marketplace/standard-contract) selecteren bij het maken van de aanbieding.

### <a name="free-software-trials"></a>Gratis softwareproefversies

Voor het afhandelen van publicatiescenario's kan de uitgever een softwarelicentie 30 dagen of 90 dagen gratis beschikbaar stellen. Deze kortingsmogelijkheid omvat niet de kosten van azure-infrastructuurgebruik die wordt aangestuurd door het gebruik van de partneroplossing.

### <a name="private-offers"></a>Privéaanbiedingen

Naast het gebruik van aanbiedingstypen en factureringsmodellen om inkomsten te genereren met een aanbieding, kunnen uitgevers een privéaanbieding uitvoeren, compleet met onderhandelde en dealspecifieke prijzen of aangepaste configuraties. Privéaanbiedingen worden ondersteund door alle 3 de opties voor het publiceren van transacties.

Deze optie maakt hogere of lagere prijzen mogelijk dan het openbaar beschikbare aanbod. Privéaanbiedingen kunnen worden gebruikt om korting te geven of een premie toe te voegen voor een aanbieding. Privéaanbiedingen kunnen beschikbaar worden gesteld aan een of meer klanten door hun Azure-abonnement op aanbiedingsniveau wit te vermelden.

### <a name="examples"></a>Voorbeelden

#### <a name="pay-as-you-go"></a>Betalen per gebruik

* Als u de optie Betalen per gebruik inschakelt, hebt u de volgende kostenstructuur.

|Uw licentiekosten  | $1.00 per uur  |
|---------|---------|
|Azure-gebruikskosten (D1/1-Core)    |   $0.14 per uur     |
|*Klant wordt gefactureerd door Microsoft*    |  *$1.14 per uur*       |

* In dit scenario factureert Microsoft $ 1,14 per uur voor het gebruik van uw gepubliceerde VM-afbeelding.

|Microsoft rekeningen  | $1.14 per uur  |
|---------|---------|
|Microsoft betaalt u 80% van uw licentiekosten|   $0,80 per uur     |
|Microsoft houdt 20% van uw licentiekosten  |  $0,20 per uur       |
|Microsoft houdt 100% van de Azure-gebruikskosten | $0.14 per uur |

### <a name="bring-your-own-license-byol"></a>Breng uw eigen licentie (BYOL)

* Als u de byol-optie inschakelt, hebt u de volgende kostenstructuur.

|Uw licentiekosten  | Licentiekosten onderhandeld en gefactureerd door u  |
|---------|---------|
|Azure-gebruikskosten (D1/1-Core)    |   $0.14 per uur     |
|*Klant wordt gefactureerd door Microsoft*    |  *$0.14 per uur*       |

* In dit scenario factureert Microsoft $ 0,14 per uur voor het gebruik van uw gepubliceerde VM-afbeelding.

|Microsoft rekeningen  | $0.14 per uur  |
|---------|---------|
|Microsoft houdt de gebruikskosten van Azure    |   $0.14 per uur     |
|Microsoft houdt 0% van uw licentiekosten   |  $0,00 per uur       |

### <a name="saas-app-subscription"></a>SaaS App-abonnement

Deze optie moet zijn geconfigureerd om te verkopen via Microsoft en kan worden geprijsd tegen een vast tarief of per gebruiker op een maandelijkse of jaarlijkse basis.

• Als u de optie Verkopen via Microsoft inschakelt voor een SaaS-aanbieding, hebt u de volgende kostenstructuur.

|Uw licentiekosten       | $ 100,00 per maand  |
|--------------|---------|
|Azure-gebruikskosten (D1/1-Core)    | Rechtstreeks gefactureerd aan de uitgever, niet aan de klant |
|*Klant wordt gefactureerd door Microsoft*    |  *$ 100,00 per maand (let op: uitgever moet rekening houden met eventuele gemaakte of doorgeefinfrastructuurkosten in de licentiekosten)*  |

* In dit scenario factureert Microsoft $ 100,00 voor uw softwarelicentie en betaalt het $ 80,00 uit aan de uitgever.
* Partners die in aanmerking zijn gekomen voor de gereduceerde Marketplace-servicekosten, krijgen van mei 2019 tot juni 2020 een gereduceerde transactievergoeding te zien op de SaaS-aanbiedingen. In dit scenario factureert Microsoft $ 100,00 voor uw softwarelicentie en betaalt het $ 90,00 uit aan de uitgever.

|Microsoft rekeningen  | $ 100,00 per maand  |
|---------|---------|
|Microsoft betaalt u 80% van uw licentiekosten <br> \*Microsoft betaalt u 90% van uw licentiekosten voor alle gekwalificeerde SaaS-apps   |   $ 80,00 per maand <br> \*$ 90,00 per maand    |
|Microsoft houdt 20% van uw licentiekosten <br> \*Microsoft houdt 10% van uw licentiekosten voor gekwalificeerde SaaS-apps.  |  $ 20,00 per maand <br> \*$ 10,00     |

**Lagere Servicekosten voor Marketplace:** Voor bepaalde SaaS-producten die u op onze commerciële marktplaats publiceert, verlaagt Microsoft de Marketplace-servicekosten van 20% (zoals beschreven in de Microsoft Publisher Agreement) tot 10%.  Om uw product in aanmerking te laten komen, moet ten minste één van uw producten door Microsoft worden aangewezen als IP-co-sell ready of IP co-sell. Om deze verlaagde Marketplace-servicekosten voor de maand te ontvangen, moet ten minste vijf (5) werkdagen voor het einde van de vorige kalendermaand aan de voorwaarden worden voldaan. Gereduceerde Marketplace-servicekosten zijn niet van toepassing op VM's, beheerde apps of andere producten die beschikbaar worden gesteld via onze commerciële marktplaats.  Deze gereduceerde Marketplace-servicekosten zijn beschikbaar voor gekwalificeerde aanbiedingen, met licentiekosten die microsoft tussen 1 mei 2019 en 30 juni 2020 heeft geïnd.  Na die tijd worden de Marketplace-servicekosten teruggegeven naar het normale bedrag.
