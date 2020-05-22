---
title: Ingebouwde firewall voor beheerde instanties detecteren
description: Meer informatie over het controleren van de ingebouwde firewall beveiliging in Azure SQL Database Managed instance.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 91b8b37de69a3f08bf11650e0328b616d8633e7a
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83769918"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>Ingebouwde firewall van Managed Instance controleren

Voor de [verplichte binnenkomende beveiligings regels](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules) voor het beheerde exemplaar zijn beheer poorten 9000, 9003, 1438, 1440 en 1452 vereist die moeten worden geopend vanaf **een bron** in de netwerk beveiligings groep (NSG) die het beheerde exemplaar beveiligt. Hoewel deze poorten zijn geopend op het niveau van de NSG, worden ze op netwerk niveau beschermd door de ingebouwde firewall.

## <a name="verify-firewall"></a>Firewall controleren

Als u deze poorten wilt controleren, gebruikt u een hulp programma voor beveiligings scanner om deze poorten te testen. In de volgende scherm afbeelding ziet u hoe u een van deze hulpprogram ma's gebruikt.

![De ingebouwde firewall controleren](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over beheerde exemplaren en connectiviteit de [Azure SQL database Managed instance connectivity-architectuur](sql-database-managed-instance-connectivity-architecture.md).