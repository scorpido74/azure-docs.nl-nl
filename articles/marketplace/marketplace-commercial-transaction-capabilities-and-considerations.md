---
title: De Transact-mogelijkheden van micro soft Commercial Marketplace
description: In dit artikel worden de prijs-, facturerings-, facturerings-en uitbetalings overwegingen voor de micro soft Commercial Marketplace Transact-optie beschreven.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: d3e3afecc441a010ff28ef8970a1da7b1e99ffc6
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/26/2020
ms.locfileid: "82160525"
---
# <a name="commercial-marketplace-transact-capabilities"></a>De Transact-mogelijkheden voor commerciële Marketplace

In dit artikel worden de volgende Commerce onderwerpen behandeld voor micro soft Commercial Marketplace:

* Transactie verantwoordelijkheid voor verschillende publicatie opties
* Overzicht van Transact-algemeen
* Facturerings modellen voor trans acties
* Transact-vereisten

## <a name="transactions-by-publishing-option"></a>Optie trans acties per publicatie

De uitgever of micro soft is verantwoordelijk voor het beheer van software licentie transacties voor aanbiedingen in de commerciële Marketplace. De publicatie optie die u voor uw aanbieding kiest, bepaalt wie de trans actie beheert. Zie [uw publicatie optie bepalen](./determine-your-listing-type.md#choose-a-publishing-option) voor Beschik baarheid en uitleg van elke publicatie optie.

### <a name="list-trial-and-byol-publishing-options"></a>Lijst-, proef-en BYOL-publicatie opties

Uitgevers met bestaande mogelijkheden van Commerce kunnen lijst-, proef-en BYOL-publicatie opties kiezen voor promotie-en gebruikers wervings doeleinden. Met deze opties neemt micro soft niet rechtstreeks deel aan de software licentie transacties van de uitgever en zijn er geen transactie kosten gekoppeld. Uitgevers zijn verantwoordelijk voor de ondersteuning van alle aspecten van de software licentie transactie, inclusief, maar niet beperkt tot: order, verwerking, meting, facturering, facturering, betaling en verzameling. Met de lijst-en proef publicatie opties blijven er 100% van de licentie kosten voor Publisher-software van de klant ontvangen.

### <a name="transact-publishing-option"></a>Optie voor het publiceren van Transact

De optie voor het publiceren van Transact maakt gebruik van de mogelijkheden van micro soft commerce en biedt een end-to-end-ervaring van detectie en evaluatie tot aankoop en implementatie. Voor aanbiedingen in de Transact worden gefactureerd tegen een bestaand micro soft-abonnement of een credit card, zodat micro soft Cloud-Marketplace-trans acties kan hosten namens de uitgever.

U kiest de optie Transact wanneer u een nieuwe aanbieding maakt in het partner centrum. Selecteer op de pagina **installatie van aanbieding** onder **Details van installatie**de optie Ja, ik wil graag door micro soft verkopen en micro soft-host-trans acties voor mijn naam hebben. " Deze optie wordt alleen weer gegeven als Transact beschikbaar is voor het type van uw aanbieding.

## <a name="transact-general-overview"></a>Overzicht van Transact-algemeen

Wanneer u de optie voor het publiceren van Transact gebruikt, schakelt micro soft de verkoop van software van derden in en de implementatie van sommige aanbiedings typen aan het Azure-abonnement van de klant. De uitgever moet rekening houden met de facturering van de kosten voor de infra structuur en de eigen software licentie kosten van de uitgever bij het selecteren van een facturerings model en aanbiedings type.

De optie voor het publiceren van Transact wordt momenteel ondersteund voor de volgende aanbiedings typen: Virtual Machines, Azure-toepassingen en SaaS-apps.

### <a name="billing-infrastructure-costs"></a>Kosten van facturerings infrastructuur

**Voor Virtual Machines-en Azure-toepassingen**

Voor Virtual Machines en Azure-toepassingen worden de gebruiks kosten voor Azure-infra structuur in rekening gebracht voor het Azure-abonnement van de klant. De kosten voor het gebruik van de infra structuur zijn geprijsd en worden afzonderlijk van de licentie kosten van de software provider weer gegeven op de factuur van de klant.

**Voor SaaS-apps**

Voor SaaS-apps moet de uitgever rekening met de gebruiks kosten voor Azure-infra structuur en software licentie kosten als één kosten item.  Het wordt weer gegeven als een vast bedrag voor de klant. Het gebruik van de Azure-infra structuur wordt rechtstreeks beheerd en gefactureerd met de partner. De werkelijke gebruiks kosten voor de infra structuur zijn niet zichtbaar voor de klant. Uitgevers willen doorgaans de gebruiks kosten voor Azure-infra structuur bundelen in hun prijzen voor software licenties. Software licentie kosten worden niet gemeten of verbruikt.

## <a name="transact-billing-models"></a>Facturerings modellen voor trans acties

Afhankelijk van de gebruikte transactie optie kunnen de software licentie kosten van de uitgever als volgt worden weer gegeven:  

* Gratis: er worden geen kosten in rekening gebracht voor software licenties.

* Bring Your Own License (BYOL): eventuele toepasselijke kosten voor software licenties worden rechtstreeks beheerd tussen de uitgever en de klant. Micro soft passeert alleen de gebruiks kosten voor Azure-infra structuur. Dit geldt alleen voor Virtual Machines en Azure-toepassingen.

* Betalen naar gebruik: de kosten voor software licenties worden gepresenteerd als een prijs per uur, per kern (vCPU) op basis van de gebruikte Azure-infra structuur. Dit geldt alleen voor Virtual Machines en Azure-toepassingen.

* Abonnements prijzen: de kosten voor software licenties worden weer gegeven als maandelijks of jaarlijks, terugkerend bedrag in rekening gebracht als een vast tarief of per seat. Dit geldt alleen voor SaaS-apps en door Azure-toepassingen beheerde apps.

* Gratis proef versie: er worden gedurende 30 dagen of 90 dagen geen kosten in rekening gebracht voor software licenties.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Gratis en BYOL-prijzen (uw eigen licentie)

Bij het publiceren van een gratis of uw eigen licentie transactie aanbieding speelt micro soft geen rol om de verkoop transactie voor uw software licentie kosten te vergemakkelijken. Net als de opties voor het publiceren van een lijst en proef versie, houdt de uitgever 100% van de software licentie kosten in rekening.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Prijzen voor betalen per gebruik en abonnementen (op site gebaseerd)

Wanneer u een betalen per gebruik-of abonnements transactie aanbieding publiceert, biedt micro soft de technologie en services voor het verwerken van de aankopen, retour neren en terugstortingen van software licenties. In dit scenario wordt micro soft door de uitgever geautoriseerd om als agent te fungeren voor deze doel einden. De uitgever stelt micro soft in staat om de Software Licensing-trans actie te vergemakkelijken, waarbij de toewijzing wordt behouden als verkoper, provider, distributeur en licentie gever.

Micro soft stelt klanten in staat om uitgever software te best Ellen, te bestemmen en te gebruiken, onder voor waarden van zowel de commerciële Marketplace van micro soft als de gebruiksrecht overeenkomst van de uitgever. Uitgevers moeten hun gebruiksrecht overeenkomst voor eind gebruikers opgeven of het [standaard contract](./standard-contract.md) selecteren bij het maken van de aanbieding.

### <a name="free-software-trials"></a>Gratis software-experimenten

Voor scenario's voor het publiceren van publicaties kan de uitgever een software licentie gratis beschikbaar maken gedurende 30 dagen of 90 dagen. Deze functie korting omvat niet de kosten van het gebruik van Azure-infra structuur dat wordt aangestuurd door het gebruik van de partner oplossing.

### <a name="private-offers"></a>Persoonlijke aanbiedingen

Uitgevers kunnen niet alleen gebruikmaken van aanbiedings typen en facturerings modellen om een aanbieding te geld verdienenen, maar ook een persoonlijke aanbieding, compleet met onderhandelde prijzen of aangepaste configuraties. Persoonlijke aanbiedingen worden ondersteund door alle drie de opties voor het publiceren van trans acties.

Met deze optie kunnen hogere of lagere prijzen worden gemaakt dan voor de openbaar beschik bare aanbieding. Persoonlijke aanbiedingen kunnen worden gebruikt om korting te krijgen, of een Premium voor een aanbieding toe te voegen. Persoonlijke aanbiedingen kunnen aan een of meer klanten beschikbaar worden gesteld door een overzicht van hun Azure-abonnement op het niveau van de aanbieding.


### <a name="examples"></a>Voorbeelden

**Betalen per gebruik** 

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

**Neem uw eigen licentie mee (BYOL)**

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

**SaaS-app-abonnement**

Deze optie moet worden geconfigureerd om via micro soft te worden verkocht en kan maandelijks of per jaar worden geprijsd op basis van een vast bedrag.
*    Als u de optie verkopen via micro soft inschakelt voor een SaaS-aanbieding, hebt u de volgende kosten structuur.

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

* **Lagere kosten voor Marketplace-service:** Voor bepaalde SaaS-producten die u op onze commerciële Marketplace publiceert, verlaagt micro soft de service kosten voor Marketplace van 20% (zoals beschreven in de overeenkomst voor micro soft Publisher) tot 10%.  Om uw product in aanmerking te komen, moet u ten minste één van uw producten door micro soft worden aangewezen als een voor bereiding op het IP-adres of door de IP-mede-verkoop prioriteit. Om dit gereduceerde service-tarief voor de Marketplace voor de maand te ontvangen, moet aan de voor waarden worden voldaan ten minste vijf (5) werk dagen vóór het einde van de vorige kalender maand. Lagere kosten voor Marketplace-service zijn niet van toepassing op Vm's, beheerde apps of andere producten die beschikbaar worden gesteld via onze commerciële Marketplace.  Dit lagere tarief voor Marketplace-Services is beschikbaar voor gekwalificeerde aanbiedingen, met licentie kosten die door micro soft zijn verzameld tussen 1 mei 2019 en 30 juni 2020.  Na deze periode wordt de service kosten voor de Marketplace teruggebracht naar de normale hoeveelheid.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Facturering, betaling, facturering en verzamelingen van klanten

**Facturering en betaling**

Publisher kan de voorkeurs facturerings methode van de klant gebruiken voor het leveren van abonnements-of PAYGO software licentie kosten.

**Enter prise overeenkomst** 

Als de voorkeurs facturerings methode van de klant de micro soft-Enterprise Agreement is, worden de kosten voor software licenties gefactureerd op basis van deze facturerings methode als een gespecificeerde kosten, gescheiden van eventuele Azure-specifieke gebruiks kosten.

**Credit cards en maandelijkse factuur** 

Klanten kunnen ook betalen met een credit card en een maandelijkse factuur. In dit geval worden de kosten voor software licenties gefactureerd op dezelfde manier als het Enterprise Agreement scenario, als een gespecificeerde kosten, gescheiden van eventuele Azure-specifieke gebruiks kosten.

Als de klant bijvoorbeeld gebruikmaakt van een credit card:

|Beschrijving    |    Date  |
|----------|----------|
|Bestel periode   | 15 augustus 2018-aug 30, 2018 |
|Einde van de term (maand)   | 30 augustus 2018 |
|Factuur datum | 1 Sept 2018 |
|Betalings datum van klant | 1 Sept 2018 |
|Borg periode (alleen voor credit cards, 30 dagen) | Sept 1 september 2018-september 30, 2018 |
|Begin van verzamelings periode | 1 Sept 2018 |
|Einde van de verzamelings periode (Maxi maal 30 dagen) | Sept 30, 2018 |
|Berekenings datum van betaling (maandelijks op de 15e) | Okt 1, 2018 |
|Uitbetalings datum | 15 oktober 2018 |

Als de klant wordt gekocht met een Enterprise Agreement:

| Beschrijving |    Date  |
|----------|----------|
|Bestel periode | 15 augustus 2018-aug 30, 2018 |
|Einde van de term (kwar taal) | Sept 30, 2018 |
|Factuur datum | 15 oktober 2018 |
|Borg periode (alleen voor credit cards, 30 dagen) | N.v.t. |
|Begin van verzamelings periode | 15 oktober 2018 |
|Einde van verzamelings periode (Maxi maal 90 dagen) | 15 januari 2019 |
|Betalings datum van klant | 30 dec, 2018 |
|Berekenings datum van betaling (maandelijks op de 15e) | 15 januari 2019 |
|Uitbetalings datum | 15 februari 2019 |

**Gratis tegoed en monetaire toezeg ging** 

Sommige klanten kiezen Azure voor een monetaire toezeg ging in de Enterprise Agreement of zijn gratis tegoed voor gebruik met Azure. Hoewel deze tegoeden kunnen worden gebruikt om te betalen voor gebruik van Azure, kunnen ze niet worden gebruikt voor het betalen van licentie kosten voor software licenties.

**Facturering en verzamelingen** 

De facturering van uitgever software licenties wordt weer gegeven met behulp van de klant geselecteerde facturerings methode en volgt de facturerings tijdlijn. Klanten zonder een Enterprise Agreement op locatie worden maandelijks gefactureerd voor software licenties voor Marketplace. Klanten met een Enterprise Agreement worden maandelijks gefactureerd via een factuur die elk kwar taal wordt gepresenteerd.

Als er prijs modellen voor abonnementen of betalen per gebruik zijn geselecteerd, fungeert micro soft als de agent van de uitgever en is verantwoordelijk voor alle aspecten van facturering, betaling en verzameling.

### <a name="publisher-payout-and-reporting"></a>Uitbetaling en rapportage van Uitgever

* Alle software licentie kosten die door micro soft worden verzameld als agent, zijn onderhevig aan 20% transactie kosten, tenzij anders vermeld en worden afgetrokken op het moment van de uitgever.

* Klanten kopen normaal gesp roken gebruik van de Enterprise Agreement of een op een credit card ingeschakelde betalen naar gebruik-overeenkomst. Het overeenkomst type bepaalt de timing van facturering, facturering, verzamelen en uitbetaling.

>[!NOTE]
>Alle rapportage en inzichten voor de optie voor het publiceren van Transact zijn beschikbaar via het gedeelte Analytics van partner Center.

#### <a name="billing-questions-and-support"></a>Vragen en ondersteuning voor facturering

Zie de [Publisher-overeenkomst](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt) (beschikbaar in Partner Center) voor meer informatie en juridisch beleid.

Neem contact op met de [ondersteuning voor commerciële Marketplace-Uitgever](https://partner.microsoft.com/support/v2/?stage=1)om hulp te krijgen bij vragen over facturering.

## <a name="transact-requirements"></a>Transact-vereisten

De Transact-vereisten voor de verschillende aanbiedings typen zijn opgenomen in deze sectie.

### <a name="requirements-for-all-offer-types"></a>Vereisten voor alle aanbiedings typen

* Er zijn een Microsoft-account en financiële informatie vereist voor de optie voor het publiceren van de Transact, ongeacht het prijs model van de aanbieding.
* Verplichte financiële informatie omvat het uitbetalings account en het BTW-profiel.

Zie [uw commerciële Marketplace-account beheren in Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account)voor meer informatie over het instellen van deze accounts.

### <a name="requirements-for-specific-offer-types"></a>Vereisten voor specifieke aanbiedings typen

De optie voor het publiceren van Transact is alleen beschikbaar voor gebruik met de volgende typen Marketplace-aanbiedingen:

**Virtuele machine** 

U kunt kiezen uit gratis, uw eigen licentie of betalen per gebruik-prijs modellen en aanwezig als Sku's die zijn gedefinieerd op het niveau van de aanbieding. Op de Azure-factuur van de klant presenteert micro soft de licentie kosten van de uitgever software afzonderlijk van de onderliggende kosten voor Azure-infra structuur. Kosten voor Azure-infra structuur worden aangestuurd door het gebruik van de uitgever software.

**Azure-toepassingen: oplossings sjabloon of beheerde app** 

U moet een of meer virtuele machines inrichten en de som van de prijzen van de virtuele machine ophalen. Voor beheerde apps in één abonnement kan een maandelijks abonnement met een vaste frequentie worden geselecteerd als het prijs model in plaats van de prijzen van de virtuele machine. In sommige gevallen worden gebruiks kosten voor Azure-infra structuur door gegeven aan de klant, onafhankelijk van software licentie kosten, maar op hetzelfde factuur overzicht. Als u echter een beheerde app-aanbieding voor ISV-infrastructuur kosten configureert, worden de Azure-resources gefactureerd aan de uitgever en ontvangt de klant een vast bedrag dat de kosten van infra structuur, software licenties en beheer Services omvat.

## <a name="next-steps"></a>Volgende stappen

* Bekijk de geschiktheids vereisten in de sectie publicatie opties per aanbiedings type om de selectie en configuratie van uw aanbieding te volt ooien.
* Bekijk de publicatie patronen per winkel voor voor beelden van hoe uw oplossing is gekoppeld aan een type en configuratie van een aanbieding.
