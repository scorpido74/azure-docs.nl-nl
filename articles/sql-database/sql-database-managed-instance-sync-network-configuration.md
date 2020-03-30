---
title: Azure App Service - netwerkconfiguratie synchroniseren
description: In dit artikel wordt besproken hoe u uw netwerkconfiguratie voor Azure App Service-hostingplan synchroniseren.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 12/13/2018
ms.openlocfilehash: e7263d6a7716caf9f53e8496c6fb02b6d17b5509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73687897"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Netwerkconfiguratie synchroniseren voor Azure App Service-hostingplan

Het kan voorkomen dat u uw app hebt [geïntegreerd met een Azure Virtual Network,](../app-service/web-sites-integrate-with-vnet.md)maar dat u geen verbinding maken met Beheerde instantie. Een ding dat u proberen is het vernieuwen van de netwerkconfiguratie voor uw serviceplan.

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Netwerkconfiguratie synchroniseren voor app-hostingplan

Voer hiervoor de volgende stappen uit:  

1. Ga naar uw App Service-abonnement voor webapps.

   ![app-serviceplan](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Klik **op Netwerken** en klik hier om te **beheren.**

   ![serviceplan beheren](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Selecteer uw **VNet** en klik op **Netwerk synchroniseren**.

   ![synchronisatienetwerk](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Wacht tot de synchronisatie is gedaan.
  
   ![synchronisatie gedaan](./media/sql-database-managed-instance-sync-networking/sync-done.png)

U bent nu klaar om te proberen uw verbinding met uw beheerde instantie te herstellen.

## <a name="next-steps"></a>Volgende stappen

- Zie [Managed Instance VNet-architectuur](sql-database-managed-instance-connectivity-architecture.md) en Het configureren van [bestaande VNet](sql-database-managed-instance-configure-vnet-subnet.md)voor informatie over het configureren van uw VNet voor beheerde instantie.
