---
title: Beheerde instantie bepaalt vnet/subnetgrootte
description: In dit onderwerp wordt beschreven hoe u de grootte van het subnet berekent waarin de beheerde Azure Sql Database Managed Instances worden geïmplementeerd.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 02/22/2019
ms.openlocfilehash: 7f0ef26343284b7b668e71676114586f4bec8b9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825745"
---
# <a name="determine-vnet-subnet-size-for-azure-sql-database-managed-instance"></a>VNet-subnetgrootte voor Azure SQL Database Managed Instance bepalen

Azure SQL Database Managed Instance moet worden geïmplementeerd binnen een Virtual [Network (Azure Virtual Network).](../virtual-network/virtual-networks-overview.md)

Het aantal beheerde exemplaren dat kan worden geïmplementeerd in het subnet van VNet is afhankelijk van de grootte van het subnet (subnetbereik).

Wanneer u een beheerde instantie maakt, wijst Azure een aantal virtuele machines toe, afhankelijk van de laag die u tijdens de inrichting hebt geselecteerd. Omdat deze virtuele machines zijn gekoppeld aan uw subnet, vereisen ze IP-adressen. Om een hoge beschikbaarheid te garanderen tijdens reguliere bewerkingen en serviceonderhoud, kan Azure extra virtuele machines toewijzen. Als gevolg hiervan is het aantal vereiste IP-adressen in een subnet groter dan het aantal beheerde exemplaren in dat subnet.

Volgens het ontwerp heeft een beheerde instantie minimaal 16 IP-adressen in een subnet nodig en kan het maximaal 256 IP-adressen gebruiken. Als gevolg hiervan u een subnetmaskers tussen /28 en /24 gebruiken bij het definiëren van uw subnet IP-bereiken. Een netwerkmaskerbit van /28 (14 hosts per netwerk) is een goede grootte voor één algemeen doel of bedrijfskritieke implementatie. Een maskerbit van /27 (30 hosts per netwerk) is ideaal voor meerdere Managed Instance-implementaties binnen hetzelfde VNet. Met de bitinstellingen van /26 (62 hosts) en /24 (254 hosts) u verder uit het VNet schalen om extra beheerde exemplaren te ondersteunen.

> [!IMPORTANT]
> Een subnetgrootte met 16 IP-adressen is het absolute minimum met een beperkt potentieel waarbij een schaalbewerking zoals vCore-groottewijziging niet wordt ondersteund. Het kiezen van subnet met het voorvoegsel /27 of langste voorvoegsel is een aanrader.

## <a name="determine-subnet-size"></a>Subnetgrootte bepalen

Als u van plan bent meerdere beheerde exemplaren in het subnet te implementeren en moet optimaliseren op subnetgrootte, gebruikt u deze parameters om een berekening te vormen:

- Azure gebruikt vijf IP-adressen in het subnet voor zijn eigen behoeften
- Elke instantie voor algemeen gebruik heeft twee adressen nodig
- Elke bedrijfskritieke instantie heeft vier adressen nodig

**Voorbeeld:** U bent van plan drie algemene en twee bedrijfskritieke beheerde instanties te hebben. Dat betekent dat je 5 + 3 * 2 + 2 * 4 = 19 IP-adressen nodig hebt. Aangezien IP-bereiken zijn gedefinieerd in kracht van 2, hebt u het IP-bereik van 32 (2^5) IP-adressen nodig. Daarom moet u het subnet reserveren met subnetmasker van /27.

> [!IMPORTANT]
> De hierboven weergegeven berekening zal verouderd raken met verdere verbeteringen.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is een beheerde instantie voor](sql-database-managed-instance.md)een overzicht .
- Meer informatie over [connectiviteitsarchitectuur voor beheerde instantie](sql-database-managed-instance-connectivity-architecture.md).
- Bekijk hoe [u VNet maakt waar u Beheerde exemplaren implementeert](sql-database-managed-instance-create-vnet-subnet.md)
- Zie [Een aangepaste DNS configureren](sql-database-managed-instance-custom-dns.md) voor DNS-problemen
