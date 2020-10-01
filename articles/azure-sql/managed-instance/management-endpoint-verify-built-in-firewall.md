---
title: Poort beveiliging in de ingebouwde firewall verifiëren
description: Meer informatie over het controleren van de ingebouwde firewall beveiliging in Azure SQL Managed instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: 35d0053d8c45547eaa5e89e6da15fcaf0343d6c5
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91617295"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>De ingebouwde firewall van Azure SQL Managed Instance controleren
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Voor de [verplichte binnenkomende beveiligings regels](connectivity-architecture-overview.md#mandatory-inbound-security-rules) van Azure SQL Managed instance zijn beheer poorten 9000, 9003, 1438, 1440 en 1452 vereist om te kunnen worden geopend vanuit **elke bron** in de netwerk beveiligings groep (NSG) die een SQL-beheerd exemplaar beveiligt. Hoewel deze poorten zijn geopend op het niveau van de NSG, worden ze op netwerk niveau beschermd door de ingebouwde firewall.

## <a name="verify-firewall"></a>Firewall controleren

Als u deze poorten wilt controleren, gebruikt u een hulp programma voor beveiligings scanner om deze poorten te testen. In de volgende scherm afbeelding ziet u hoe u een van deze hulpprogram ma's gebruikt.

![De ingebouwde firewall controleren](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over SQL Managed instance en Connectivity de [Azure SQL Managed instance connectivity-architectuur](connectivity-architecture-overview.md).
