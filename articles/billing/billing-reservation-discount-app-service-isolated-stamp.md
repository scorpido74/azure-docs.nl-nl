---
title: Hoe reserveringskortingen worden toegepast op Azure App Service Isolated-zegels
description: Ontdek hoe reserveringskortingen worden toegepast op Azure App Service Isolated-zegels.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 759f83001353957f23b917440b68719b54690bea
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718807"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-isolated-stamps"></a>Hoe reserveringskortingen worden toegepast op Azure App Service Isolated-zegels

Wanneer u gereserveerde capaciteit hebt gekocht om de zegelkosten van App Service Isolated te dekken, wordt de reserveringskorting automatisch toegepast op de zegelkosten in een bepaalde regio. De reserveringskorting geldt voor het gebruik dat door de zegelkostenmeter van App Service Isolated wordt bijgehouden. Werkrollen, extra front-ends en overige resources die aan de zegel zijn gekoppeld, worden als voorheen gefactureerd op basis van het normale tarief.

## <a name="reservation-discount-application"></a>Toepassing van reserveringskorting

De korting op de zegelkosten voor App Service Isolated wordt op uurbasis toegepast op actieve Isolated-zegels. Als u een zegel geen vol uur implementeert, gaat de resterende gereserveerde capaciteit voor dat uur verloren. Deze restcapaciteit kan niet worden overgedragen.

Na aankoop wordt de door u gekochte reservering gekoppeld aan een App Service Isolated-zegel die wordt uitgevoerd in een specifieke regio. Als u deze zegel afsluit, wordt de reserveringskorting automatisch toegepast op andere zegels die in de betreffende regio worden uitgevoerd. Als er geen zegels aanwezig zijn, wordt de reservering toegepast op de volgende zegel die in de regio wordt gemaakt.

Als een zegel geen volledig uur wordt uitgevoerd, wordt de reservering gedurende de resterende tijd in dat uur automatisch toegepast op andere in aanmerking komende zegels in dezelfde regio.

## <a name="choose-a-stamp-type---windows-or-linux"></a>Een type zegel kiezen: Windows of Linux

Standaard wordt voor een lege Isolated-zegel de Windows-zegelmeter gebruikt. Dit is bijvoorbeeld het geval wanneer er geen werkrollen zijn geïmplementeerd. Deze meter blijft in gebruik als er Windows-werkrollen worden geïmplementeerd. De meter wordt echter gewijzigd in een Linux-zegelmeter als u een Linux-werkrol implementeert. Als er zowel Linux- als Windows-werkrollen zijn geïmplementeerd, wordt voor de zegel de Windows-meter gebruikt.

De zegelmeter kan tijdens de levensduur dus veranderen van Windows in Linux en omgekeerd. Reserveringen zijn echter specifiek voor een bepaald besturingssysteem. U moet daarom een reservering kopen die ondersteuning biedt voor de werkrollen die u via de zegel wilt implementeren. Voor zegels met uitsluitend Windows-werkrollen en voor zegels met gemengde werkrollen wordt de Windows-reservering gebruikt. Voor zegels met alleen Linux-werkrollen wordt gebruikgemaakt van de Linux-reservering.

U moet dus alleen een Linux-reservering kopen als u van plan bent via de zegel _uitsluitend_ Linux-werkrollen te implementeren.

## <a name="discount-examples"></a>Kortingsvoorbeelden

De volgende voorbeelden laten zien hoe de korting op de zegelkosten voor gereserveerde instanties van App Service Isolated voor verschillende implementaties wordt toegepast.

- **Voorbeeld 1**: U koopt één instantie van gereserveerde App Service Isolated-zegelcapaciteit voor een regio zonder Isolated-zegels. U implementeert een nieuwe zegel in de betreffende regio en betaalt hiervoor het tarief voor gereserveerde capaciteit.
- **Voorbeeld 2**: U koopt één instantie van gereserveerde App Service Isolated-zegelcapaciteit voor een regio waarin al een Isolated-zegel is geïmplementeerd. U betaalt voor de geïmplementeerde zegel vanaf nu het tarief voor gereserveerde capaciteit.
- **Voorbeeld 3**: U koopt één instantie van gereserveerde App Service Isolated-zegelcapaciteit voor een regio waarin al een Isolated-zegel is geïmplementeerd. U betaalt voor de geïmplementeerde zegel vanaf nu het tarief voor gereserveerde capaciteit. Later verwijdert u de bestaande zegel en implementeert u een nieuwe zegel. U betaalt voor de nieuwe zegel het tarief voor gereserveerde capaciteit. De korting kan niet worden overgedragen als er geen geïmplementeerde zegels zijn.
- **Voorbeeld 4**: U koopt één instantie van Linux-specifieke gereserveerde App Service Isolated-zegelcapaciteit voor een bepaalde regio en implementeert daar vervolgens een nieuwe zegel. Als de zegel aanvankelijk zonder werkrollen wordt geïmplementeerd, wordt de Windows-zegelmeter gebruikt. Er wordt geen korting toegepast. Wanneer de eerste Linux-werkrol via de zegel wordt geïmplementeerd, wordt er overgeschakeld naar de Linux-zegelmeter en is de reserveringskorting van kracht. Als er later via de zegel een Windows-werkrol wordt geïmplementeerd, wordt er teruggeschakeld naar de Windows-zegelmeter. De korting voor Linux-specifieke gereserveerde App Service Isolated-zegelcapaciteit vervalt dan weer.

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure-reserveringen beheren](billing-manage-reserved-vm-instance.md) voor meer informatie over het beheren van een reservering.
- Zie [Prepay for Azure App Service Isolated Stamp Fee with reserved capacity](billing-prepay-app-service-isolated-stamp.md) (Vooraf betalen voor gereserveerde Azure App Service Isolated-zegelcapaciteit) voor meer informatie over hoe u geld bespaart door vooraf gereserveerde App Service Isolated-zegelcapaciteit te kopen.
- Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:
  - [Wat zijn Azure-reserveringen?](billing-save-compute-costs-reservations.md)
  - [Reserveringen beheren in Azure](billing-manage-reserved-vm-instance.md)
  - [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](billing-understand-reserved-instance-usage.md)
  - [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](billing-understand-reserved-instance-usage-ea.md)
