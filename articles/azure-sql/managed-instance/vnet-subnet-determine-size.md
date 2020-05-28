---
title: Vereiste subnet-grootte bepalen & bereik
titleSuffix: Azure SQL Managed Instance
description: In dit onderwerp wordt beschreven hoe u de grootte berekent van het subnet waarin de door Azure SQL beheerde exemplaren worden geïmplementeerd.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 02/22/2019
ms.openlocfilehash: 5eebde1fc95cb50f8ff7c13b6cbc411484ddf943
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84050931"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>Vereiste subnet grootte bepalen & bereik voor Azure SQL Managed instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed instance moet worden geïmplementeerd in een [virtueel Azure-netwerk (VNet)](../../virtual-network/virtual-networks-overview.md).

Het aantal SQL-beheerde instanties dat kan worden geïmplementeerd in het subnet van VNet, is afhankelijk van de grootte van het subnet (subnetmasker).

Wanneer u een SQL Managed instance maakt, wijst Azure een aantal virtuele machines toe, afhankelijk van de laag die u hebt geselecteerd tijdens het inrichten. Omdat deze virtuele machines zijn gekoppeld aan uw subnet, zijn er IP-adressen nodig. Azure kan extra virtuele machines toewijzen om hoge Beschik baarheid te garanderen tijdens reguliere bewerkingen en onderhoud van services. Als gevolg hiervan is het aantal vereiste IP-adressen in een subnet groter dan het aantal SQL Managed instances in dat subnet.

Een SQL Managed instance vereist standaard Mini maal 16 IP-adressen in een subnet en kan Maxi maal 256 IP-adressen gebruiken. Als gevolg hiervan kunt u een subnetmasker tussen/28 en/24 gebruiken bij het definiëren van de IP-adresbereiken van uw subnet. Een netwerkmask van/28 (14 hosts per netwerk) is een goede grootte voor een implementatie met één algemeen doel of essentieel bedrijf. Een mask-bit van/27 (30 hosts per netwerk) is ideaal voor een aantal implementaties van een SQL-beheerde instantie binnen hetzelfde VNet. Met de masker-bit-instellingen van/26 (62 hosts) en/24 (254-hosts) kunt u het VNet verder verg Roten of verkleinen om extra SQL-beheerde instanties te ondersteunen.

> [!IMPORTANT]
> Een subnet-grootte met 16 IP-adressen is het bare minimum met een beperkt potentieel waarbij een schaal bewerking, zoals vCore grootte, niet wordt ondersteund. Het kiezen van een subnet met het voor voegsel/27 of het langste voor voegsel wordt sterk aanbevolen.

## <a name="determine-subnet-size"></a>De grootte van het subnet bepalen

Als u van plan bent meerdere door SQL beheerde exemplaren in het subnet te implementeren en de grootte van het subnet wilt optimaliseren, gebruikt u deze para meters om een berekening te maken:

- Azure gebruikt vijf IP-adressen in het subnet voor eigen behoeften
- Voor elk Algemeen-exemplaar zijn twee adressen nodig
- Voor elk Bedrijfskritiek-exemplaar zijn vier adressen nodig

**Voor beeld**: u bent van plan drie algemeen en twee bedrijfskritiek SQL Managed instances. Dit betekent dat u 5 + 3 * 2 + 2 * 4 = 19 IP-adressen nodig hebt. Als IP-bereiken worden gedefinieerd in de macht van 2, hebt u het IP-bereik van 32 (2 ^ 5) IP-adressen nodig. Daarom moet u het subnet reserveren met subnetmasker/27.

> [!IMPORTANT]
> De berekening die hierboven wordt weer gegeven, zal verouderd raken met verdere verbeteringen.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is een SQL-beheerd exemplaar](sql-managed-instance-paas-overview.md)voor een overzicht.
- Meer informatie over de [connectiviteits architectuur voor een door SQL beheerd exemplaar](connectivity-architecture-overview.md).
- Zie [VNet maken waarin u SQL Managed instances gaat implementeren](virtual-network-subnet-create-arm-template.md)
- Zie [Configuring a Custom DNS](custom-dns-configure.md) (Engelstalig) voor DNS-problemen.
