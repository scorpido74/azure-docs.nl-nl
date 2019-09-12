---
title: Azure-reserve ringen automatisch vernieuwen
description: Meer informatie over hoe u Azure-reserve ringen automatisch kunt vernieuwen om de reserverings kortingen te blijven ontvangen.
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: banders
ms.openlocfilehash: c19c6af68bcde753ec9bed990e08aa81eabdd37d
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679456"
---
# <a name="automatically-renew-reservations"></a>Reserve ringen automatisch vernieuwen

U kunt reserve ringen vernieuwen om automatisch een vervanging te kopen wanneer een bestaande reserve ring verloopt. Automatische verlenging biedt een eenvoudige manier om reserve ringen te blijven ontvangen. U kunt er ook voor zorgen dat de verval datum van een reserve ring nauw keurig moet worden gecontroleerd. Met automatische verlenging voor komt u dat verlies voordelen worden bespaard door niet hand matig te verlengen. De instelling voor het vernieuwen is standaard uitgeschakeld. Hiermee schakelt u de instelling voor het vernieuwen in of uit, tot het verlopen van de bestaande reserve ring.

Bij het vernieuwen van een reserve ring wordt een nieuwe reserve ring gemaakt wanneer de bestaande reserve ring verloopt. De periode van de bestaande reserve ring wordt niet verlengd.

Meld u aan om op elk gewenst moment automatisch te verlengen. De vernieuwings prijs is 30 dagen vóór het verstrijken van de bestaande reserve ring beschikbaar. Wanneer u de verlenging meer dan 30 dagen vóór de verval datum van de reserve ring inschakelt, ontvangt u een e-mail met een gedetailleerde verlengings kosten van 30 dagen voor de verval datum. De prijs van de reserve ring kan veranderen tussen de tijd dat u de vernieuwings prijs en de verlengings tijd vergren delen. Als dat het geval is, zijn de kosten voor verlenging de laagste van de twee kosten. U kunt wijzigingen aanbrengen in de reserverings hoeveelheid. Als u dit doet, wordt de verlenging bijgewerkt om de prijs in de markt te gebruiken die is ingesteld op het moment dat de hoeveelheid wordt gewijzigd.

Er is geen verplichting om te verlengen en u kunt de verlenging op elk gewenst moment afmelden voordat de bestaande reserve ring verloopt.

## <a name="set-up-renewal"></a>Verlenging instellen

Ga naar Azure Portal > **reserve ringen**.

1. Selecteer de reserve ring.
2. Klik op **vernieuwen**.
3. Selecteer **automatisch een nieuwe reserve ring kopen na verloop datum**.  
  ![Voor beeld van reserverings verlenging](./media/billing-reservation-renew/reservation-renewal.png)

## <a name="if-you-dont-renew"></a>Als u niet verlengt

Uw services blijven normaal worden uitgevoerd. U betaalt betalen naar gebruik-tarieven voor uw verbruik wanneer de reserve ring verloopt.

## <a name="required-renewal-permissions"></a>Vereiste verlengings machtigingen

De volgende voor waarden zijn vereist voor het vernieuwen van een reserve ring:

- U moet een eigenaar zijn van de bestaande reserve ring.
- U moet een eigenaar van het abonnement zijn als de reserve ring is beperkt tot één abonnement of resource groep.
- U moet een eigenaar van het abonnement zijn als het een gedeeld bereik heeft.

## <a name="default-renewal-settings"></a>Standaard instellingen voor vernieuwen

Standaard neemt het vernieuwen alle eigenschappen over van de verlopende reserve ring. Een aankoop van een reserve ring moet dezelfde SKU, regio, bereik, facturerings abonnement, termijn en hoeveelheid hebben.

U kunt echter de aankoop hoeveelheid voor de vernieuwings reservering bijwerken om uw besparingen te optimaliseren.

## <a name="when-the-new-reservation-is-purchased"></a>Wanneer de nieuwe reserve ring wordt aangeschaft

Er wordt een nieuwe reserve ring aangeschaft wanneer de bestaande reserve ring verloopt. We proberen een vertraging tussen de twee reserve ringen te voor komen. Continuïteit zorgt ervoor dat uw kosten voorspelbaar zijn en dat u nog steeds kortingen krijgt.

## <a name="changing-parent-reservation-after-setting-renewal"></a>Bovenliggende Reserve ring wijzigen na instelling van verlenging

Als u een van de volgende wijzigingen aanbrengt in de verval reservering, wordt de reserverings verlenging geannuleerd:

- Splitsen
- Samenvoegen
- Overdracht van de reserve ring van het ene account naar het andere
- De reserve ring van een webdirect-abonnement overbrengen naar een EA-abonnement (Enter prise Agreement) of een andere aankoop methode
- De inschrijving vernieuwen

De nieuwe reserve ring neemt het bereik en de flexibiliteits instelling voor de grootte van de instantie over van de verlopende reserve ring tijdens het vernieuwen.

## <a name="new-reservation-permissions"></a>Nieuwe reserverings machtigingen

Azure kopieert de machtigingen van de verlopende reserve ring naar de nieuwe reserve ring. Daarnaast heeft de account beheerder van het abonnement van de reserverings aankoop toegang tot de nieuwe reserve ring.

## <a name="potential-renewal-problems"></a>Mogelijke problemen met de vernieuwing

Azure kan de vernieuwing mogelijk niet verwerken als:

- Betaling kan niet worden verzameld
- Er treedt een systeem fout op tijdens het vernieuwen
- De verlopende SKU is niet actief tijdens het verlengen
- De EA wordt vernieuwd naar een andere EA

U ontvangt een e-mail melding als een van de voor gaande voor waarden wordt weer gegeven en de verlenging wordt uitgeschakeld.

## <a name="renewal-notification"></a>Melding over verlenging

E-mail berichten worden naar verschillende mensen verzonden, afhankelijk van uw aankoop methode:

- EA-klanten: e-mail berichten worden verzonden naar de contact persoon voor meldingen die is ingesteld op de EA-Portal.
- Individuele abonnements klanten met betalen per gebruik-tarieven: e-mail berichten worden verzonden naar gebruikers die als account beheerder zijn ingesteld.
- Klanten van Cloud Solution Provider: e-mail berichten worden verzonden naar de contact persoon van de partner melding.

## <a name="next-steps"></a>Volgende stappen
- Zie [Wat zijn Azure Reservations?](billing-save-compute-costs-reservations.md) voor meer informatie over Azure Reservations.
