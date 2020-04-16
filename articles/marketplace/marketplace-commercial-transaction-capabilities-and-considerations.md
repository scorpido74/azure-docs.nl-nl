---
title: De mogelijkheden voor commerciële marktplaatstransacties van Microsoft
description: In dit artikel worden overwegingen voor prijzen, facturering, facturering en uitbetaling voor de commerciële marktplaatsoptie van Microsoft beschreven.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 768fa9ca2080fc9a58fb321e62d8d61a608f9564
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415261"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Commerciële marktplaatstransacties mogelijkheden

In dit artikel worden de volgende handelsgerelateerde onderwerpen voor de commerciële marktplaats van Microsoft behandeld:

* Transactieverantwoordelijkheid voor verschillende publicatieopties
* Algemeen overzicht van de transacties
* Factureringsmodellen voor transacties
* Transactvereisten

## <a name="transactions-by-publishing-option"></a>Opties per publicatieoptie

De uitgever of Microsoft is verantwoordelijk voor het beheer van softwarelicentietransacties voor aanbiedingen op de commerciële marktplaats. De publicatieoptie die u voor uw aanbieding kiest, bepaalt wie de transactie beheert. Zie [Uw publicatieoptie bepalen](./determine-your-listing-type.md#choose-a-publishing-option) voor beschikbaarheid en uitleg van elke publicatieoptie.

### <a name="list-trial-and-byol-publishing-options"></a>Publicatieopties voor lijst- en proefversies

Uitgevers met bestaande commercemogelijkheden kunnen opties voor het publiceren van de lijst, proefversie en bring-your-own-license (BYOL) kiezen voor promotionele en gebruikersacquisitiedoeleinden. Met deze opties neemt Microsoft niet rechtstreeks deel aan de softwarelicentietransacties van de uitgever en zijn er geen bijbehorende transactiekosten. Uitgevers zijn verantwoordelijk voor het ondersteunen van alle aspecten van de softwarelicentietransactie, inclusief maar niet beperkt tot: bestelling, afhandeling, meting, facturering, facturering, betaling en incasso. Met de opties voor het publiceren van de lijst en proefversies houden uitgevers 100% van de licentiekosten van uitgeverssoftware bij de klant.

### <a name="transact-publishing-option"></a>Publicatie van transacties

De optie voor het publiceren van transacties maakt gebruik van de commercemogelijkheden van Microsoft en biedt een end-to-end-ervaring, van ontdekking en evaluatie tot aankoop en implementatie. Aanbiedingen voor transacties worden gefactureerd op basis van een bestaand Microsoft-abonnement of een creditcard, waardoor Microsoft namens de uitgever cloudmarktplaatstransacties kan hosten.

U kiest de optie voor transacties wanneer u een nieuwe aanbieding maakt in partnercentrum. Selecteer op de pagina **Installatie instellen** onder **Installatiegegevens**de optie 'Ja, ik wil verkopen via Microsoft en microsoft-hosttransacties namens mij laten hosten'. Deze optie wordt alleen weergegeven als er transacties beschikbaar zijn voor uw aanbiedingstype.

## <a name="transact-general-overview"></a>Algemeen overzicht van de transacties

Wanneer microsoft de optie voor het publiceren van transacties gebruikt, wordt de verkoop van software van derden en de implementatie van bepaalde aanbiedingstypen naar het Azure-abonnement van de klant mogelijk. De uitgever moet bij het selecteren van een factureringsmodel en aanbiedingstype rekening houden met de facturering van infrastructuurkosten en de eigen softwarelicentiekosten van de uitgever.

De optie Transact-publicatie wordt momenteel ondersteund voor de volgende aanbiedingstypen: Virtuele machines, Azure-toepassingen en SaaS-apps.

### <a name="billing-infrastructure-costs"></a>Kosten voor factureringsinfrastructuur

**Voor virtuele machines en Azure-toepassingen**

Voor virtuele machines en Azure-toepassingen worden de gebruikskosten voor Azure-infrastructuur gefactureerd op het Azure-abonnement van de klant. Gebruikskosten voor infrastructuur worden apart geprijsd en gepresenteerd van de licentiekosten van de softwareprovider op de factuur van de klant.

**Voor SaaS-apps**

Voor SaaS-apps moet de uitgever rekening houden met gebruikskosten voor Azure-infrastructuur en kosten voor softwarelicenties als één kostenpost.  Het wordt vertegenwoordigd als een vast bedrag aan de klant. Het gebruik van de Azure-infrastructuur wordt rechtstreeks aan de partner beheerd en gefactureerd. Werkelijke gebruikskosten voor infrastructuur worden niet gezien door de klant. Uitgevers kiezen er doorgaans voor om gebruikskosten voor Azure-infrastructuur te bundelen in hun softwarelicentieprijzen. Kosten voor softwarelicenties worden niet gemeten of op basis van het verbruik.

## <a name="transact-billing-models"></a>Factureringsmodellen voor transacties

Afhankelijk van de gebruikte transactieoptie kunnen de softwarelicentiekosten van de uitgever als volgt worden gepresenteerd:  

* Gratis: Geen kosten voor softwarelicenties.

* Breng uw eigen licentie (BYOL): Alle toepasselijke kosten voor softwarelicenties worden rechtstreeks tussen de uitgever en de klant beheerd. Microsoft gaat alleen door gebruikskosten van azure-infrastructuur. Dit geldt alleen voor virtuele machines en Azure-toepassingen.

* Pay-as-you-go: Softwarelicentiekosten worden gepresenteerd als een vCPU-prijstarief per uur (vCPU) op basis van de gebruikte Azure-infrastructuur. Dit geldt alleen voor virtuele machines en Azure-toepassingen.

* Abonnementsprijzen: Softwarelicentiekosten worden gepresenteerd als een maandelijkse of jaarlijkse, terugkerende kosten die worden gefactureerd als een vast tarief of per stoel. Dit geldt alleen voor SaaS-apps en Azure-toepassingen - Alleen beheerde apps.

* Gratis proefversie van software: geen kosten voor softwarelicenties voor 30 dagen of 90 dagen.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Gratis en bring-your-own-license (BYOL) prijzen

Bij het publiceren van een gratis of bring-your-own-license transactieaanbieding speelt Microsoft geen rol bij het faciliteren van de verkooptransactie voor uw softwarelicentiekosten. Net als de opties voor het publiceren van de lijst en proefversies, houdt de uitgever 100% van de softwarelicentiekosten.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Prijzen voor betalen per gebruik en abonnementen (op de site gebaseerd)

Bij het publiceren van een aanbieding voor betalen per gebruik of abonnementstransacties biedt Microsoft de technologie en services voor het verwerken van aankopen, retouren en terugboekingen van softwarelicenties. In dit scenario geeft de uitgever Microsoft toestemming om voor deze doeleinden als agent op te treden. De uitgever stelt Microsoft in staat om de softwarelicentietransactie te vergemakkelijken, met behoud van hun aanduiding als verkoper, aanbieder, distributeur en licentiegever.

Microsoft stelt klanten in staat om uitgeverssoftware te bestellen, in licentie te geven en te gebruiken, onder voorbehoud van de algemene voorwaarden van zowel de commerciële Marktplaats van Microsoft als de licentieovereenkomst voor eindgebruikers van de uitgever. Uitgevers moeten hun licentieovereenkomst voor eindgebruikers opgeven of het [standaardcontract](./standard-contract.md) selecteren bij het maken van het aanbod.

### <a name="free-software-trials"></a>Gratis softwareproefversies

Voor het afhandelen van publicatiescenario's kan de uitgever een softwarelicentie 30 dagen of 90 dagen gratis beschikbaar stellen. Deze kortingsmogelijkheid omvat niet de kosten van azure-infrastructuurgebruik die wordt aangestuurd door het gebruik van de partneroplossing.

### <a name="private-offers"></a>Privéaanbiedingen

Naast het gebruik van aanbiedingstypen en factureringsmodellen om inkomsten te genereren met een aanbieding, kunnen uitgevers een privé-aanbieding uitvoeren, compleet met onderhandelde, dealspecifieke prijzen of aangepaste configuraties. Privéaanbiedingen worden ondersteund door alle 3 de opties voor het publiceren van transacties.

Deze optie maakt hogere of lagere prijzen mogelijk dan het openbaar beschikbare aanbod. Privéaanbiedingen kunnen worden gebruikt om korting te geven of een premie toe te voegen voor een aanbieding. Privéaanbiedingen kunnen beschikbaar worden gesteld aan een of meer klanten door hun Azure-abonnement op aanbiedingsniveau wit te vermelden.


### <a name="examples"></a>Voorbeelden

**Betalen per gebruik** 

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

**Breng uw eigen licentie (BYOL)**

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

**SaaS App-abonnement**

Deze optie moet zijn geconfigureerd om te verkopen via Microsoft en kan worden geprijsd tegen een vast tarief of per gebruiker op een maandelijkse of jaarlijkse basis.
*    Als u de optie Verkopen via Microsoft inschakelt voor een SaaS-aanbieding, hebt u de volgende kostenstructuur.

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

* **Lagere Servicekosten voor Marketplace:** Voor bepaalde SaaS-producten die u op onze commerciële marktplaats publiceert, verlaagt Microsoft de Marketplace-servicekosten van 20% (zoals beschreven in de Microsoft Publisher Agreement) tot 10%.  Om uw product in aanmerking te laten komen, moet ten minste één van uw producten door Microsoft worden aangewezen als IP-co-sell ready of IP co-sell. Om deze verlaagde Marketplace-servicekosten voor de maand te ontvangen, moet ten minste vijf (5) werkdagen voor het einde van de vorige kalendermaand aan de voorwaarden worden voldaan. Gereduceerde Marketplace-servicekosten zijn niet van toepassing op VM's, beheerde apps of andere producten die beschikbaar worden gesteld via onze commerciële marktplaats.  Deze gereduceerde Marketplace-servicekosten zijn beschikbaar voor gekwalificeerde aanbiedingen, met licentiekosten die microsoft tussen 1 mei 2019 en 30 juni 2020 heeft geïnd.  Na die tijd worden de Marketplace-servicekosten teruggegeven naar het normale bedrag.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Facturering, betaling, facturering en incasso's van klanten

**Facturatie en betaling**

Uitgever kan de gewenste factureringsmethode van de klant gebruiken om abonnements- of PAYGO-softwarelicentiekosten te leveren.

**Ondernemingsovereenkomst** 

Als de voorkeursfactureringsmethode van de klant de Microsoft Enterprise Agreement is, worden uw softwarelicentiekosten in rekening gebracht met deze factureringsmethode als gespecificeerde kosten, los van eventuele Azure-specifieke gebruikskosten.

**Creditcards en maandelijkse factuur** 

Klanten kunnen ook betalen met een creditcard en een maandelijkse factuur. In dit geval worden uw softwarelicentiekosten in rekening gebracht, net als het Enterprise Agreement-scenario, als gespecificeerde kosten, los van eventuele Azure-specifieke gebruikskosten.

Als de klant bijvoorbeeld met een creditcard koopt:

|Beschrijving    |    Date  |
|----------|----------|
|Bestelperiode   | 15 augustus 2018 - 30 augustus 2018 |
|Term eindigend (maand)   | 30 augustus 2018 |
|Factureringsdatum | 1 september 2018 |
|Betalingsdatum van klant | 1 september 2018 |
|Borgperiode (alleen creditcards, 30 dagen) | 1 september 2018 - 30 september 2018 |
|Beginperiode | 1 september 2018 |
|Einde van de inzamelperiode (maximaal 30 dagen) | 30 september 2018 |
|Uitbetalingsberekeningsdatum (maandelijks op de 15e) | 1 oktober 2018 |
|Uitbetalingsdatum | 15 okt 2018 |

Als de klant een Enterprise Agreement koopt:

| Beschrijving |    Date  |
|----------|----------|
|Bestelperiode | 15 augustus 2018 - 30 augustus 2018 |
|Term Ending (kwartaal) | 30 september 2018 |
|Factureringsdatum | 15 okt 2018 |
|Borgperiode (alleen creditcards, 30 dagen) | N.v.t. |
|Beginperiode | 15 okt 2018 |
|Einde van de inzamelperiode (maximaal 90 dagen) | 15 jan 2019 |
|Betalingsdatum van klant | 30 december 2018 |
|Uitbetalingsberekeningsdatum (maandelijks op de 15e) | 15 jan 2019 |
|Uitbetalingsdatum | 15 februari 2019 |

**Gratis kredieten en monetaire toezeggingen** 

Sommige klanten kiezen ervoor om Azure vooraf te betalen met een monetaire toezegging in de Enterprise Agreement of hebben gratis credits gekregen voor gebruik met Azure. Hoewel deze credits kunnen worden gebruikt om te betalen voor Azure-gebruik, kunnen ze niet worden gebruikt om licentiekosten voor uitgeverssoftware te betalen.

**Facturering en incasso's** 

Publisher software licentie facturering wordt gepresenteerd met behulp van de klant geselecteerde methode van facturatie en volgt de facturering tijdlijn. Klanten zonder enterprise-overeenkomst worden maandelijks gefactureerd voor marketplace-softwarelicenties. Klanten met een Enterprise Agreement worden maandelijks gefactureerd via een factuur die elk kwartaal wordt gepresenteerd.

Wanneer abonnements- of betalenper-gebruik-prijsmodellen zijn geselecteerd, treedt Microsoft op als de agent van de uitgever en is het verantwoordelijk voor alle aspecten van facturering, betaling en verzameling.

### <a name="publisher-payout-and-reporting"></a>Uitbetaling en rapportage van uitgevers

* Voor softwarelicentiekosten die door Microsoft als agent worden geïnd, zijn transactiekosten van 20% verschuldigd, tenzij anders aangegeven en worden ze in mindering gebracht op het moment van uitbetaling door de uitgever.

* Klanten kopen doorgaans met de Enterprise Agreement of een pay-as-you-go-overeenkomst met creditcard. Het type overeenkomst bepaalt de facturerings-, facturerings-, incasso- en uitbetalingstiming.

>[!NOTE]
>Alle rapportages en inzichten voor de transact publishing-optie zijn beschikbaar via de sectie Insights van de sectie Cloud Partner Portal of Analytics van partnercentrum.

#### <a name="billing-questions-and-support"></a>Vragen en ondersteuning voor facturering

Zie de [Uitgeversovereenkomst](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) (beschikbaar in de Cloud Partner Portal) voor meer informatie en juridisch beleid.

Neem contact op met de ondersteuning van [commerciële marktplaatsuitgevers](https://aka.ms/marketplacepublishersupport)om hulp te krijgen bij factureringsvragen.

## <a name="transact-requirements"></a>Transactvereisten

De transacties voor verschillende aanbiedingstypen komen in deze sectie aan bod.

### <a name="requirements-for-all-offer-types"></a>Vereisten voor alle aanbiedingstypen

* Een Microsoft-account en financiële informatie zijn vereist voor de optie voor het publiceren van transacties, ongeacht het prijsmodel van de aanbieding.
* Verplichte financiële informatie omvat uitbetalingsrekening en belastingprofiel.

Zie Uw account op de [commerciële marktplaats beheren in partnercentrum voor](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account)meer informatie over het instellen van deze accounts.

### <a name="requirements-for-specific-offer-types"></a>Vereisten voor specifieke aanbiedingstypen

De optie voor het publiceren van transacties is alleen beschikbaar voor gebruik met de volgende soorten marktplaatsaanbiedingen:

**Virtuele machine** 

Kies uit gratis, bring-your-own-license of pay-as-you-go-pricing modellen en presenteer als SKU's gedefinieerd op het aanbiedingsniveau. Op de Azure-factuur van de klant presenteert Microsoft de licentiekosten voor uitgeverssoftware afzonderlijk van de onderliggende Azure-infrastructuurkosten. Azure-infrastructuurkosten worden bepaald door het gebruik van de uitgeverssoftware.

**Azure-toepassingen: oplossingssjabloon of beheerde app** 

Moet de voorziening een of meer virtuele machines en trekt door de som van de virtuele machine prijsstelling. Voor beheerde apps op één abonnement kan een vast maandelijks abonnement worden geselecteerd als het prijsmodel in plaats van de prijzen voor virtuele machines. In sommige gevallen worden de gebruikskosten van azure-infrastructuur afzonderlijk doorberekend aan de klant, maar op hetzelfde factuuroverzicht. Als u echter een Managed App-aanbieding voor ISV-infrastructuurkosten configureert, worden de Azure-resources gefactureerd aan de uitgever en ontvangt de klant een vast bedrag dat de kosten van infrastructuur, softwarelicenties en beheerservices omvat.

## <a name="next-steps"></a>Volgende stappen

* Bekijk de geschiktheidsvereisten in de publicatieopties per sectie aanbiedingstype om de selectie en configuratie van uw aanbieding af te ronden.
* Bekijk de publicatiepatronen per etalage voor voorbeelden over hoe uw oplossing wordt toegewezen aan een aanbiedingstype en -configuratie.
