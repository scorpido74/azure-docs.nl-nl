---
title: Wat zijn Azure-reserveringen?
description: Meer informatie over Azure-reserveringen en prijzen om geld te besparen op uw virtuele machines, SQL-databases, Azure Cosmos DB en andere resourcekosten.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: banders
ms.openlocfilehash: 6277a7e7dc5891a3bc67c298a31344284c92e31d
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235638"
---
# <a name="what-are-azure-reservations"></a>Wat zijn Azure-reserveringen?

Azure-reserveringen helpen u geld te besparen door een toezegging te doen voor een abonnement van één of drie jaar voor meerdere producten. Door u vast te leggen krijgt u korting op de resources die u gebruikt. Met reserveringen kunt u tot wel 72% besparen op uw resourcekosten ten opzichte van de prijzen voor betalen per gebruik. Reserveringen voorzien in een korting op de factuur en zijn niet van invloed op de runtime-status van uw resources. Nadat u een reservering hebt aangeschaft, is de korting automatisch van toepassing op de overeenkomende resource.

U kunt vooraf of maandelijks voor een reservering betalen. De totale kosten van betalingen vooraf en per maand voor reserveringen zijn hetzelfde en u hoeft ook geen toeslag te betalen wanneer u voor maandelijks betalen kiest. Maandelijkse betaling is beschikbaar voor Azure-reserveringen, niet voor producten van derden.

U kunt een reservering kopen via [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Redenen om een reservering te kopen

Als u een consistent resourcegebruik hebt dat reserveringen ondersteunt, biedt de aanschaf van een reservering u de mogelijkheid om uw kosten te verlagen. Wanneer u bijvoorbeeld continu instanties van een service uitvoert zonder een reservering te gebruiken, wordt u gefactureerd met tarieven voor betalen per gebruik. Wanneer u een reservering koopt, krijgt u direct korting op de reservering. De resources worden niet langer gefactureerd met de tarieven voor betalen per gebruik.

## <a name="how-reservation-discount-is-applied"></a>De manier waarop reserveringskorting wordt toegepast

Na de aankoop is de reserveringskorting automatisch van toepassing op het resourcegebruik dat overeenkomt met de kenmerken die u selecteert wanneer u de reservering koopt. Kenmerken zijn onder andere de SKU, regio's (indien van toepassing) en het bereik. Met het bereik voor een reservering selecteert u waarop de reserveringskorting van toepassing is.

Raadpleeg [Toepassing van korting voor de gereserveerde instantie](reservation-discount-application.md) voor meer informatie over hoe korting wordt toegepast.

Zie [Het bereik van reserveringen bepalen](prepare-buy-reservation.md#scope-reservations) voor meer informatie over de werking van een reserveringsbereik.


## <a name="flexibility-with-azure-reservations"></a>Flexibiliteit met Azure-reserveringen

Azure-reserveringen bieden flexibiliteit om te voldoen aan uw evoluerende behoeften. U kunt een reservering inwisselen voor een andere reservering van hetzelfde type. U kunt ook een restitutie (tot USD 50.000 in 12 maanden (doorlopend)) voor een reservering aanvragen als u deze niet meer nodig hebt. De maximumlimiet van de restitutie geldt voor alle reserveringen binnen het bereik van uw overeenkomst met Microsoft.

Zie [Selfservice voor omruiling en terugbetaling van Azure-reserveringen](exchange-and-refund-azure-reservations.md) voor meer informatie


## <a name="charges-covered-by-reservation"></a>Kosten die onder de reservering vallen

- **Gereserveerde VM-instantie**: een reservering dekt alleen de rekenkosten van de virtuele machine. Aanvullende kosten voor software, Windows, netwerken of opslag vallen hier niet onder.
- **Gereserveerde Azure Storage-capaciteit**: onder een reservering valt de opslagcapaciteit voor standaardopslagaccounts voor Blob Storage of Azure Data Lake Gen2 Storage. De tarieven voor bandbreedte of transacties vallen niet onder de reservering.
- **Gereserveerde Azure Cosmos DB-capaciteit**: onder een reservering valt de doorvoer die voor uw resources is ingericht. De opslag- en netwerkkosten vallen hier niet onder.
- **Gereserveerde SQL Database-vCore**: alleen de rekenkosten worden opgenomen in een reservering. De SQL-licentie wordt afzonderlijk gefactureerd.
- **SQL Data Warehouse**: onder een reservering valt het cDWU-gebruik. Een reservering omvat geen opslag- en netwerkkosten die samenhangen met het gebruik van SQL Data Warehouse.
- **Azure Databricks**: onder een reservering valt alleen het DBU-gebruik. Andere kosten, bijvoorbeeld voor berekeningen, opslag en netwerken, worden afzonderlijk toegepast.
- **App Service-zegelkosten**: onder een reservering valt het gebruik van zegels. De reservering is niet van toepassing op werkrollen, dus alle andere resources die aan de zegel zijn gekoppeld, worden afzonderlijk in rekening gebracht.
- **Azure Database for MySQL**: alleen de rekenkosten worden opgenomen in een reservering. Onder een reservering vallen niet de kosten voor software, netwerk of opslag die betrekking hebben op de MySQL Database-server.
- **Azure Database for PostgreSQL**: alleen de rekenkosten worden opgenomen in een reservering. Onder een reservering vallen niet de kosten voor software, netwerk of opslag die betrekking hebben op de PostgreSQL Database-servers.
- **Azure Database for MariaDB**: alleen de rekenkosten worden opgenomen in een reservering. Onder een reservering vallen niet de kosten voor software, netwerk of opslag die betrekking hebben op de MariaDB Database-server.
- **Azure Data Explorer**: onder een reservering vallen de wijzigingen in markeringen. Onder een reservering vallen niet de kosten voor berekeningen, netwerk of opslag die betrekking hebben op de clusters.
- **Azure Cache voor Redis** - alleen de rekenkosten worden opgenomen in een reservering. Onder een reservering vallen niet de kosten voor netwerk of opslag die betrekking hebben op de Redis-cache-exemplaren.
- **Azure Dedicated Host**: alleen de rekenkosten zijn opgenomen bij de toegewezen host.
- **Azure Disk Storage-reserveringen**: een reservering dekt alleen Premium-SSD's met de grootte P30 of hoger. De reservering geldt niet voor andere schijftypen of schijfgrootten kleiner dan P30.

Softwareabonnementen:

- **SUSE Linux**: onder een reservering vallen de kosten voor softwareabonnementen. De kortingen zijn alleen van toepassing op SUSE-meters, en niet op het gebruik van virtuele machines.
- **Red Hat-abonnementen**: onder een reservering vallen de kosten voor softwareabonnementen. De kortingen zijn alleen van toepassing op RedHat-meters, en niet op het gebruik van virtuele machines.
- **Azure VMware-oplossing van CloudSimple**: onder een reservering vallen de VMWare CloudSimple-knooppunten. Er kunnen nog steeds aanvullende softwarekosten van toepassing zijn.
- **Azure Red Hat OpenShift**: een reservering is van toepassing op de OpenShift-kosten, niet op de kosten voor de Azure-infrastructuur.

Voor virtuele Windows-machines en SQL Database is de reserveringskorting niet van toepassing op de softwarekosten. U kunt de licentiekosten dekken via [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).


## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over Azure-reserveringen met de volgende artikelen:
    - [Azure-reserveringen beheren](manage-reserved-vm-instance.md)
    - [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](understand-reserved-instance-usage.md)
    - [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](understand-reserved-instance-usage-ea.md)
    - [Kosten van Windows-software zijn niet inbegrepen in reserveringen](reserved-instance-windows-software-costs.md)
    - [Azure-reserveringen in het CSP-programma (Cloud Solution Provider) van het Partnercentrum](/partner-center/azure-reservations)

- Meer informatie over reserveringen voor serviceplannen:
    - [Virtual Machines met Azure Reserved VM Instances](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Azure Cosmos DB-resources met gereserveerde Azure Cosmos DB-capaciteit](../../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Rekenresources van SQL Database met gereserveerde capaciteit voor Azure SQL Database](../../sql-database/sql-database-reserved-capacity.md) Meer informatie over reserveringen voor software-abonnementen:
    - [Red Hat-softwareabonnementen vanuit Azure-reserveringen](../../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [SUSE-softwareabonnementen vanuit Azure-reserveringen](../../virtual-machines/linux/prepay-suse-software-charges.md)
