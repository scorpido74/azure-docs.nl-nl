---
title: Azure SQL Database beheerde instantie bepalen VNet/subnet-grootte | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe u de grootte berekent van het subnet waarin de Azure SQL Database beheerde exemplaren worden geïmplementeerd.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 02/22/2019
ms.openlocfilehash: 167e243b1fe4ea5ba9403ac3ca1fcea42f02f59a
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273559"
---
# <a name="determine-vnet-subnet-size-for-azure-sql-database-managed-instance"></a>De grootte van het VNet-subnet bepalen voor het beheerde exemplaar van Azure SQL Database

Azure SQL Database beheerde instantie moet worden geïmplementeerd in een Azure [Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md).

Het aantal beheerde exemplaren dat in het subnet van VNet kan worden geïmplementeerd, is afhankelijk van de grootte van het subnet (subnetmasker).

Wanneer u een beheerd exemplaar maakt, wijst Azure een aantal virtuele machines toe, afhankelijk van de laag die u hebt geselecteerd tijdens het inrichten. Omdat deze virtuele machines zijn gekoppeld aan uw subnet, zijn er IP-adressen nodig. Azure kan extra virtuele machines toewijzen om hoge Beschik baarheid te garanderen tijdens reguliere bewerkingen en onderhoud van services. Als gevolg hiervan is het aantal vereiste IP-adressen in een subnet groter dan het aantal beheerde exemplaren in dat subnet.

Een beheerd exemplaar heeft standaard Mini maal 16 IP-adressen in een subnet nodig en kan Maxi maal 256 IP-adressen gebruiken. Als gevolg hiervan kunt u een subnetmasker tussen/28 en/24 gebruiken bij het definiëren van de IP-adresbereiken van uw subnet. Een netwerkmask van/28 (14 hosts per netwerk) is een goede grootte voor een implementatie met één algemeen doel of essentieel bedrijf. Een mask-bit van/27 (30 hosts per netwerk) is ideaal voor meerdere implementaties van beheerde exemplaren binnen hetzelfde VNet. Met de masker-bit-instellingen van/26 (62 hosts) en/24 (254-hosts) kunt u het VNet verder verg Roten of verkleinen om extra beheerde instanties te ondersteunen.

> [!IMPORTANT]
> Een subnet-grootte met 16 IP-adressen is het bare minimum met een beperkt potentieel waarbij een schaal bewerking, zoals vCore grootte, niet wordt ondersteund. Het kiezen van een subnet met het voor voegsel/27 of het langste voor voegsel wordt sterk aanbevolen.

## <a name="determine-subnet-size"></a>De grootte van het subnet bepalen

Als u van plan bent meerdere beheerde exemplaren in het subnet te implementeren en op de subnet-grootte te optimaliseren, gebruikt u deze para meters om een berekening te maken:

- Azure gebruikt vijf IP-adressen in het subnet voor eigen behoeften
- Voor elk Algemeen-exemplaar zijn twee adressen nodig
- Voor elk Bedrijfskritiek-exemplaar zijn vier adressen nodig

**Voor beeld**: U bent van plan drie Algemeen en twee Bedrijfskritiek beheerde instanties te hebben. Dit betekent dat u 5 + 3 * 2 + 2 * 4 = 19 IP-adressen nodig hebt. Als IP-bereiken worden gedefinieerd in de macht van 2, hebt u het IP-bereik van 32 (2 ^ 5) IP-adressen nodig. Daarom moet u het subnet reserveren met subnetmasker/27.

> [!IMPORTANT]
> De berekening die hierboven wordt weer gegeven, zal verouderd raken met verdere verbeteringen.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is een beheerd exemplaar](sql-database-managed-instance.md)voor een overzicht.
- Meer informatie over [connectiviteits architectuur voor een beheerd exemplaar](sql-database-managed-instance-connectivity-architecture.md).
- Zie [VNet maken waarin u beheerde exemplaren gaat implementeren](sql-database-managed-instance-create-vnet-subnet.md)
- Zie [Configuring a Custom DNS](sql-database-managed-instance-custom-dns.md) (Engelstalig) voor DNS-problemen.
