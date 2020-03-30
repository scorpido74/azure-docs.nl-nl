---
title: Beheerde ingebouwde firewall ontdekken
description: Meer informatie over het verifiëren van ingebouwde firewallbeveiliging in Azure SQL Database Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 555ef56aafa37a1e1d384f945b04f9237adc5f7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821807"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>Ingebouwde firewall van Managed Instance controleren

De [verplichte inkomende beveiligingsregels](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules) voor beheerinstantie vereisen dat beheerpoorten 9000, 9003, 1438, 1440, 1452 worden geopend vanuit **Elke bron** op de Network Security Group (NSG) die het beheerde exemplaar beschermt. Hoewel deze poorten op NSG-niveau open zijn, worden ze op netwerkniveau beschermd door de ingebouwde firewall.

## <a name="verify-firewall"></a>Firewall verifiëren

Als u deze poorten wilt verifiëren, gebruikt u een beveiligingsscannertool om deze poorten te testen. De volgende screenshot laat zien hoe je een van deze tools te gebruiken.

![Ingebouwde firewall verifiëren](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>Volgende stappen

Zie [Azure SQL Database Managed Instance Connectivity Architecture](sql-database-managed-instance-connectivity-architecture.md)voor meer informatie over beheerde exemplaren en connectiviteit.