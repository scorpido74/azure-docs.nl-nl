---
title: Voorbereiden op de aankoop van een Azure-reservering
description: Meer informatie over belangrijke punten voordat u een Azure-reservering koopt.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: banders
ms.openlocfilehash: 1f5ca2d43356eab98cffe8414c00d97e5744739a
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235652"
---
# <a name="prepare-to-buy-a-reservation"></a>Voorbereiden op de aankoop van een reservering

Azure-reserveringen helpen u geld te besparen door een toezegging te doen voor een abonnement van één of drie jaar voor veel Azure-resources. Bekijk vóórdat u een toezegging doet om een reservering aan te schaffen, eerst de volgende secties om u voor te bereiden op de aankoop.

## <a name="who-can-buy-a-reservation"></a>Wie kan een reservering kopen

Als u een abonnement wilt kopen, moet u een eigenaarsrol voor het abonnement hebben in een zakelijke abonnement (MS-AZR-0017P of MS-AZR-0148P) of een abonnement waarvoor wordt betaald per gebruik (MS-AZR-0003P of MS-AZR-0023P) of een abonnement in een Microsoft-klantovereenkomst. Cloud Solution Providers kunnen Azure-reserveringen aanschaffen via Azure Portal of het  [Partnercentrum](/partner-center/azure-reservations) .

Enterprise Agreement-klanten (EA) kunnen het aantal aankopen voor EA-beheerders beperken door de optie **Gereserveerde instanties toevoegen** in de EA-portal uit te schakelen. EA-beheerders moeten een abonnementseigenaar zijn voor minimaal één EA-abonnement om een reservering te kunnen aanschaffen. De optie is nuttig voor bedrijven die hun reserveringen voor verschillende kostenplaatsen willen laten aanschaffen door een gecentraliseerd team. Na de aankoop kunnen gecentraliseerde teams kostenplaatseigenaars aan de reserveringen toevoegen. Eigenaars kunnen de reservering vervolgens aan hun abonnementen koppelen. Het centraleteam heeft geen toegang als abonnementseigenaar nodig tot de locatie waar de reservering wordt aangeschaft.

Reserveringskorting is alleen van toepassing op resources die zijn gekoppeld aan abonnementen die via Enterprise, een Cloud Solution Provider (CSP), een Microsoft-klantovereenkomst of afzonderlijk abonnementen met tarieven voor betalen per gebruik zijn aangeschaft.

## <a name="scope-reservations"></a>Reserveringen koppelen

U kunt een reservering aan een abonnement of aan resourcegroepen koppelen. Bij het instellen van het bereik voor een reservering selecteert u op welk punt de reserveringskorting van toepassing is. Wanneer u de reservering aan een resourcegroep koppelt, is reserveringskorting alleen van toepassing op de resourcegroep, niet op het hele abonnement.

### <a name="reservation-scoping-options"></a>Opties voor het koppelen van reserveringen

U beschikt over drie opties om een reservering te koppelen, afhankelijk van uw behoeften:

- **Bereik van één resourcegroep**: de reserveringskorting wordt alleen toegepast op de overeenkomende resources in de geselecteerde resourcegroep.
- **Bereik van één abonnement**: de reserveringskorting wordt toegepast op de overeenkomende resources in het geselecteerde abonnement.
- **Gedeeld bereik**: de reserveringskorting wordt toegepast op overeenkomende resources binnen in aanmerking komende abonnementen die zich in de factureringscontext bevinden. Voor Enterprise Agreement-klanten is de inschrijving de factureringscontext. Voor Microsoft-klantovereenkomst-klanten is het factuurbereik het factureringsprofiel. Voor afzonderlijke abonnementen met tarieven voor betalen per gebruik is het factureringsbereik alle in aanmerking komende abonnementen die zijn gemaakt door de accountbeheerder.

Wanneer u reserveringskorting op uw gebruik toepast, wordt de reservering in deze volgorde door Azure verwerkt:

1. Reserveringen die worden gekoppeld aan een resourcegroep
2. Reserveringen met één bereik
3. Reserveringen met gedeeld bereik

Voor één resourcegroep is reserveringskorting vanuit meerdere reserveringen beschikbaar, afhankelijk van de manier waarop u uw reserveringen hebt gekoppeld.

U kunt het bereik altijd bijwerken nadat u een reservering hebt gekocht. Ga hiervoor naar de reservering, klik op **Configuratie** en koppel de reservering opnieuw. Het opnieuw koppelen van een reservering wordt niet als commerciële transactie beschouwd. Uw reserveringstermijn blijft ongewijzigd. Zie [Het bereik bijwerken na aanschaf van een reservering](manage-reserved-vm-instance.md#change-the-reservation-scope) voor meer informatie over het bijwerken van het bereik.

![Voorbeeld van het wijzigen van een reserveringsbereik](./media/prepare-buy-reservation/rescope-reservation-resource-group.png)

## <a name="purchase-reservations"></a>Reserveringen aanschaffen

U kunt reserveringen aanschaffen bij de Azure-portal, API's, PowerShell, CLI. Lees de volgende artikelen die van toepassing zijn, wanneer u klaar bent om een reservering aan te schaffen:

- [App Service](prepay-app-service-isolated-stamp.md)
- [Azure Cache voor Redis](../../azure-cache-for-redis/cache-reserved-pricing.md)
- [Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Databricks](prepay-databricks-reserved-capacity.md)
- [Data Explorer](../../data-explorer/pricing-reserved-capacity.md)
- [Disk Storage](../../virtual-machines/linux/disks-reserved-capacity.md)
- [Dedicated Host](../../virtual-machines/prepay-dedicated-hosts-reserved-instances.md)
- [Softwareabonnementen](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Storage](../../storage/blobs/storage-blob-reserved-capacity.md)
- [SQL Database](../../sql-database/sql-database-reserved-capacity.md)
- [SQL Data Warehouse](prepay-sql-data-warehouse-charges.md)
- [Virtuele machines](../../virtual-machines/windows/prepay-reserved-vm-instances.md)

## <a name="next-steps"></a>Volgende stappen

- [Reserveringen voor Azure-resources beheren](manage-reserved-vm-instance.md)
