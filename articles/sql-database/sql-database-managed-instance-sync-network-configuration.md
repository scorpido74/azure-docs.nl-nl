---
title: Azure App Service-netwerk configuratie synchroniseren
description: In dit artikel wordt beschreven hoe u uw netwerk configuratie synchroniseert voor Azure App Service hosting abonnement.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 12/13/2018
ms.openlocfilehash: 0065b5c7ddf8e19897f76aeb49300ec7a6809b30
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772366"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>De netwerk configuratie voor het hosting abonnement voor Azure App Service synchroniseren

Het kan gebeuren dat u [uw app hebt geïntegreerd met een Azure-Virtual Network](../app-service/web-sites-integrate-with-vnet.md), maar u kunt geen verbinding maken met het beheerde exemplaar. U kunt proberen om de netwerk configuratie voor uw service abonnement te vernieuwen.

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Netwerk configuratie voor App Service hosting abonnement synchroniseren

Voer hiervoor de volgende stappen uit:  

1. Ga naar uw web apps App Service plan.

   ![app service-plan](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Klik op **netwerken** en klik vervolgens op **Klik hier om te beheren**.

   ![service abonnement beheren](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Selecteer uw **VNet** en klik op **netwerk synchroniseren**.

   ![netwerk synchroniseren](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Wacht totdat de synchronisatie is voltooid.
  
   ![synchronisatie is voltooid](./media/sql-database-managed-instance-sync-networking/sync-done.png)

U kunt nu proberen om de verbinding met uw beheerde exemplaar te herstellen.

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over het configureren van uw VNet voor beheerde exemplaren [beheerde exemplaar VNet-architectuur](sql-database-managed-instance-connectivity-architecture.md) en [het configureren van bestaande vnet](sql-database-managed-instance-configure-vnet-subnet.md).
