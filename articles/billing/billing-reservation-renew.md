---
title: Automatisch Azure-reserveringen verlengen
description: Lees hoe u automatisch Azure-reserveringen kunt verlengen, zodat u reserveringskortingen blijft ontvangen.
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: banders
ms.openlocfilehash: c19c6af68bcde753ec9bed990e08aa81eabdd37d
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "68679456"
---
# <a name="automatically-renew-reservations"></a>Automatisch reserveringen verlengen

U kunt reserveringen verlengen om automatisch een vervanging aan te schaffen wanneer een bestaande reservering verloopt. Automatische verlenging vormt een eenvoudige manier om ervoor te zorgen dat u reserveringskortingen blijft ontvangen. Automatische verlenging zorgt er ook voor dat u de vervaldatum van een reservering niet nauwlettend in de gaten hoeft te houden. Met automatische verlenging voorkomt u dat u besparingen misloopt, doordat u reserveringen niet handmatig hoeft te verlengen. De instelling voor verlenging is standaard uitgeschakeld. U kunt de instelling voor verlenging op elk gewenst moment in- of uitschakelen, tot de vervaldatum van de bestaande reservering.

Als u een reservering verlengt, wordt er een nieuwe reservering gemaakt wanneer de bestaande reservering verloopt. De termijn voor de bestaande reservering wordt niet verlengd.

Als u gebruik wilt maken van automatische verlenging, kunt u zich hier op elk moment voor aanmelden. De verlengingsprijs is 30 dagen vóór de vervaldatum van de bestaande reservering beschikbaar. Wanneer u de verlenging meer dan 30 dagen vóór de vervaldatum van de reservering inschakelt, ontvangt u 30 dagen vóór de vervaldatum een e-mail met gedetailleerde informatie over de verlengingskosten. De prijs van de reservering kan veranderen tussen het moment waarop u de verlengingsprijs vastzet en het moment van de daadwerkelijke verlenging. Als dat het geval is, geldt het laagste van de twee bedragen als uw verlengingsprijs. U kunt de reserveringshoeveelheid nog wijzigen. Als u dit doet, wordt de verlenging bijgewerkt zodat de marktprijs wordt gebruikt die is ingesteld op het moment waarop de hoeveelheid werd gewijzigd.

U bent niet verplicht om uw reserveringen te verlengen en u kunt zich op elk gewenst moment voordat de bestaande reservering verloopt, afmelden voor de verlenging.

## <a name="set-up-renewal"></a>Verlenging instellen

Ga naar Azure-portal > **Reserveringen**.

1. Selecteer de reservering.
2. Klik op **Verlenging**.
3. Selecteer **Automatisch een nieuwe reservering aanschaffen na vervaldatum**.  
  ![Voorbeeld van verlenging van een reservering](./media/billing-reservation-renew/reservation-renewal.png)

## <a name="if-you-dont-renew"></a>Wat er gebeurt als u uw reserveringen niet verlengt

Uw services worden dan nog steeds uitgevoerd zoals normaal. Na het verlopen van de reserveringen worden u tarieven voor betalen per gebruik in rekening gebracht voor uw gebruik.

## <a name="required-renewal-permissions"></a>Machtigingen die zijn vereist voor verlenging

Als u een reservering wilt verlengen, moet u aan de volgende voorwaarden voldoen:

- U moet eigenaar zijn van de bestaande reservering.
- Als de reservering is bedoeld voor één abonnement of resourcegroep, moet u eigenaar van het abonnement zijn.
- Als het abonnement een gedeeld bereik heeft, moet u eigenaar van het abonnement zijn.

## <a name="default-renewal-settings"></a>Standaardinstellingen voor verlenging

Standaard neemt de verlenging alle eigenschappen van de verlopende reservering over. Als u een verlenging voor een reservering aanschaft, heeft die dezelfde SKU, dezelfde regio, hetzelfde bereik, hetzelfde factureringsabonnement, dezelfde termijn en dezelfde hoeveelheid als de oude reservering.

U kunt de aankoophoeveelheid voor de verlenging van de reserveringen echter bijwerken om uw besparingen te optimaliseren.

## <a name="when-the-new-reservation-is-purchased"></a>Wanneer de nieuwe reservering wordt aangeschaft

Wanneer de bestaande reservering verloopt, wordt er een nieuwe reservering aangeschaft. We proberen te voorkomen dat u moet wachten voordat u gebruik kunt maken van de nieuwe reservering. Dankzij deze continuïteit blijven uw kosten voorspelbaar en blijft u kortingen ontvangen.

## <a name="changing-parent-reservation-after-setting-renewal"></a>Het wijzigen van de oude reservering na het instellen van de verlenging

De verlenging van de reservering wordt geannuleerd als u een van de volgende zaken wijzigt in de verlopende reservering:

- Splitsen
- Samenvoegen
- De reservering overdragen van het ene account naar het andere
- De reservering overdragen van een WebDirect-abonnement naar een EA-abonnement (Enterprise Agreement) of een andere aankoopmethode
- De inschrijving verlengen

Bij de verlenging neemt de nieuwe reservering het bereik en de instelling voor de flexibiliteit van de instantiegrootte over van de verlopende reservering.

## <a name="new-reservation-permissions"></a>Machtigingen voor de nieuwe reservering

Azure kopieert de machtigingen voor de verlopende reservering naar de nieuwe reservering. De accountbeheerder voor het abonnement van de reserveringsaankoop heeft ook toegang tot de nieuwe reservering.

## <a name="potential-renewal-problems"></a>Potentiële problemen bij de verlenging

Azure kan de verlenging mogelijk niet verwerken als:

- de betaling niet kan worden geïnd;
- er een systeemfout optreedt tijdens de verlenging;
- de verlopende SKU niet actief is tijdens de verlenging;
- het EA wordt verlengd in een ander EA.

U ontvangt een e-mailmelding als een van de voorgaande omstandigheden zich voordoet en de verlenging wordt gedeactiveerd.

## <a name="renewal-notification"></a>Melding over verlenging

Naar wie de e-mails worden verzonden, is afhankelijk van uw aankoopmethode:

- Klanten met een Enterprise Agreement: Er worden e-mailberichten verzonden naar de contactpersonen voor meldingen, die zijn ingesteld in de EA-portal.
- Klanten met een afzonderlijk abonnement met tarieven voor betalen per gebruik: Er worden e-mailberichten verzonden naar gebruikers die als accountbeheerder zijn ingesteld.
- Klanten van een Cloud Solution Provider: Er worden e-mailberichten verzonden naar de contactpersoon voor meldingen van onze partner.

## <a name="next-steps"></a>Volgende stappen
- Zie [Wat zijn Azure-reserveringen?](billing-save-compute-costs-reservations.md) voor meer informatie over Azure-reserveringen.
