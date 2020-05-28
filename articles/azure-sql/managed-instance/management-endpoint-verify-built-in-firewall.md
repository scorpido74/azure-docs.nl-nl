---
title: Poort beveiliging in ingebouwde firewall verifiëren
description: Meer informatie over het controleren van de ingebouwde firewall beveiliging in Azure SQL Managed instance.
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
ms.openlocfilehash: 04a630e51a68fab8f6c60262fbd14fad2e4aef1c
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045072"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>De ingebouwde firewall van het door Azure SQL beheerde exemplaar controleren
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Voor de [verplichte binnenkomende beveiligings regels](connectivity-architecture-overview.md#mandatory-inbound-security-rules) van Azure SQL Managed instance zijn beheer poorten 9000, 9003, 1438, 1440 en 1452 vereist die moeten worden geopend vanuit **elke bron** in de netwerk beveiligings groep (NSG) die het SQL Managed instance beveiligt. Hoewel deze poorten zijn geopend op het niveau van de NSG, worden ze op netwerk niveau beschermd door de ingebouwde firewall.

## <a name="verify-firewall"></a>Firewall controleren

Als u deze poorten wilt controleren, gebruikt u een hulp programma voor beveiligings scanner om deze poorten te testen. In de volgende scherm afbeelding ziet u hoe u een van deze hulpprogram ma's gebruikt.

![De ingebouwde firewall controleren](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>Volgende stappen

Zie [connectiviteits architectuur voor Azure SQL Managed instance](connectivity-architecture-overview.md)voor meer informatie over door SQL beheerde instanties en connectiviteit.
