---
title: Hoe reserveringskortingen van toepassing zijn op Azure SQL Data Warehouse | Microsoft Docs
description: Leer hoe reserveringskortingen van toepassing zijn op Azure SQL Data Warehouse om geld te besparen.
services: billing
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: banders
ms.openlocfilehash: 0671843b09cd90123b856b94db2618bac617dd5b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75995778"
---
# <a name="how-reservation-discounts-apply-to-azure-sql-data-warehouse"></a>Hoe reserveringskortingen van toepassing zijn op Azure SQL Data Warehouse

Nadat u gereserveerde capaciteit voor Azure SQL Data Warehouse hebt gekocht, wordt de reserveringskorting automatisch toegepast op datawarehouses in die regio. De reserveringskorting geldt voor het gebruik dat door de cDWU-meter van SQL Data Warehouse wordt gegenereerd. Opslag en netwerkgebruik worden in rekening gebracht volgens betalen-per-gebruiktarieven.

## <a name="reservation-discount-application"></a>Toepassing van reserveringskorting

De korting voor gereserveerde capaciteit van SQL Data Warehouse wordt elk uur toegepast op warehouses die actief zijn. Als u een uur lang geen warehouse hebt geïmplementeerd, gaat de gereserveerde capaciteit voor dat uur verloren. Deze restcapaciteit kan niet worden overgedragen.

Na aankoop wordt de reservering die u aanschaft, vergeleken met het SQL Data Warehouse-gebruik dat door actieve warehouses op een bepaald moment wordt gegenereerd. Als u bepaalde warehouses afsluit, zijn de reserveringskortingen automatisch van toepassing op andere overeenkomende warehouses.

Voor warehouses die geen volledig uur worden uitgevoerd, wordt de reservering automatisch toegepast op andere overeenkomende instanties in dat uur.

## <a name="discount-examples"></a>Kortingsvoorbeelden

De volgende voorbeelden laten zien hoe de korting voor gereserveerde capaciteit van SQL Data Warehouse wordt toegepast, afhankelijk van de implementaties.

- **Voor beeld 1**: u koopt 5 eenheden van 100 gereserveerde cDWU capaciteit. U voert een uur lang een DW1500c-instantie van SQL Data Warehouse uit. In dit geval wordt het gebruik gegenereerd voor 15 eenheden van een gebruik van 100 cDWU. De reserveringskorting geldt voor de vijf eenheden die u hebt gebruikt. Er worden kosten in rekening gebracht op basis van betalen-per-gebruiktarieven voor de resterende 10 gebruikte eenheden van 100 cDWU-gebruik.

- **Voor beeld 2**: u hebt 5 eenheden van 100 cDWU gereserveerde capaciteit aangeschaft. U voert een uur lang twee DW100c-instanties van SQL Data Warehouse uit. In dit geval worden twee gebruiksgebeurtenissen gegenereerd voor 1 eenheid 100 cDWU-gebruik. Beide gebruiksgebeurtenissen krijgen kortingen voor gereserveerde capaciteit. De resterende 3 eenheden van 100 cDWU gereserveerde capaciteit gaan verloren en worden niet overgedragen voor toekomstig gebruik.

- **Voor beeld 3**: u koopt 1 eenheid van 100 gereserveerde cDWU capaciteit. U voert twee DW100c-instanties van SQL Data Warehouse uit. Elk instantie wordt 30 minuten uitgevoerd. In dit geval krijgen beide gebruiksgebeurtenissen kortingen voor gereserveerde capaciteit. Er wordt geen gebruik in rekening gebracht op basis van betalen-per-gebruiktarieven.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

- Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

- [Wat zijn Azure-reserveringen?](save-compute-costs-reservations.md)
- [Reserveringstransacties weergeven](view-reservations.md)
- [Reserveringstransacties en gebruik via API ophalen](reservation-apis.md)
- [Reserveringen beheren](manage-reserved-vm-instance.md)
