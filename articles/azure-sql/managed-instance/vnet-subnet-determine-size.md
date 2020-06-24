---
title: Vereiste subnet-grootte bepalen & bereik
titleSuffix: Azure SQL Managed Instance
description: In dit onderwerp wordt beschreven hoe u de grootte berekent van het subnet waar Azure SQL Managed instance wordt geïmplementeerd.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 02/22/2019
ms.openlocfilehash: 8d1073dbcced9532390776a23dd17c1f572cce40
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84686678"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>Vereiste subnet grootte bepalen & bereik voor Azure SQL Managed instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed instance moet worden geïmplementeerd in een [virtueel Azure-netwerk (VNet)](../../virtual-network/virtual-networks-overview.md).

Het aantal beheerde instanties dat kan worden geïmplementeerd in het subnet van een VNet, is afhankelijk van de grootte van het subnet (subnetmasker).

Wanneer u een beheerd exemplaar maakt, wijst Azure een aantal virtuele machines toe, afhankelijk van de laag die u hebt geselecteerd tijdens het inrichten. Omdat deze virtuele machines zijn gekoppeld aan uw subnet, zijn er IP-adressen nodig. Azure kan extra virtuele machines toewijzen om hoge Beschik baarheid te garanderen tijdens reguliere bewerkingen en onderhoud van services. Als gevolg hiervan is het aantal vereiste IP-adressen in een subnet groter dan het aantal beheerde exemplaren in dat subnet.

Een beheerd exemplaar heeft standaard Mini maal 16 IP-adressen in een subnet nodig en kan Maxi maal 256 IP-adressen gebruiken. Als gevolg hiervan kunt u een subnetmasker tussen/28 en/24 gebruiken bij het definiëren van de IP-adresbereiken van uw subnet. Een netwerkmask-bit van/28 (14 hosts per netwerk) is een goede grootte voor een implementatie met één algemeen doel of essentieel bedrijf. Een mask-bit van/27 (30 hosts per netwerk) is ideaal voor meerdere implementaties van SQL-beheerde exemplaren binnen hetzelfde VNet. Met de masker-bit-instellingen van/26 (62 hosts) en/24 (254-hosts) kunt u het VNet verder verg Roten of verkleinen om extra beheerde instanties te ondersteunen.

> [!IMPORTANT]
> Een subnet-grootte met 16 IP-adressen is het bare minimum met een beperkt potentieel waarbij een schaal bewerking, zoals vCore grootte, niet wordt ondersteund. Het kiezen van een subnet met het voor voegsel/27 of het langste voor voegsel wordt sterk aanbevolen.

## <a name="determine-subnet-size"></a>De grootte van het subnet bepalen

Als u van plan bent meerdere beheerde exemplaren in het subnet te implementeren en op de subnet-grootte te optimaliseren, gebruikt u deze para meters om een berekening te maken:

- Azure gebruikt vijf IP-adressen in het subnet voor eigen behoeften
- Elk exemplaar voor algemeen gebruik heeft twee adressen nodig
- Elk bedrijfskritische exemplaar heeft vier adressen nodig

**Voor beeld**: u bent van plan drie algemene en twee bedrijfskritische beheerde instanties te hebben. Dit betekent dat u 5 + 3 * 2 + 2 * 4 = 19 IP-adressen nodig hebt. Als IP-bereiken worden gedefinieerd in de bevoegdheden van 2, hebt u het IP-bereik van 32 (2 ^ 5) IP-adressen nodig. Daarom moet u het subnet reserveren met subnetmasker/27.

> [!IMPORTANT]
> De hierboven weer gegeven berekening zal verouderd raken met verdere verbeteringen.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is Azure SQL Managed instance?](sql-managed-instance-paas-overview.md)voor een overzicht.
- Meer informatie over de [connectiviteits architectuur voor een door SQL beheerd exemplaar](connectivity-architecture-overview.md).
- Zie hoe [u een VNet maakt waarin u een door SQL beheerd exemplaar gaat implementeren](virtual-network-subnet-create-arm-template.md).
- Zie [Configure a Custom DNS](custom-dns-configure.md)(Engelstalig) voor DNS-problemen.
