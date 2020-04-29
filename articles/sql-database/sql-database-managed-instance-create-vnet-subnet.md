---
title: Een virtueel netwerk voor een beheerd exemplaar maken
description: In dit artikel wordt beschreven hoe u een virtueel netwerk maakt waarmee u Azure SQL Database beheerde instantie kunt implementeren.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 09/12/2019
ms.openlocfilehash: 0ce88f9a61b8aa7c2588a6e077d694afa6fb8631
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878847"
---
# <a name="create-a-virtual-network-for-azure-sql-database-managed-instance"></a>Een virtueel netwerk maken voor Azure SQL Database beheerde instantie

In dit artikel wordt uitgelegd hoe u een geldig virtueel netwerk en subnet maakt, waar u Azure SQL Database beheerde instantie kunt implementeren.

Azure SQL Database beheerde instantie moet worden geïmplementeerd in een virtueel Azure- [netwerk](../virtual-network/virtual-networks-overview.md). Deze implementatie maakt gebruik van de volgende scenario's:

- Beveiligd privé IP-adres
- Rechtstreeks vanuit een on-premises netwerk verbinding maken met een beheerd exemplaar
- Een beheerd exemplaar verbinden met een gekoppelde server of een ander on-premises gegevens archief
- Een beheerd exemplaar verbinden met Azure-resources  

> [!NOTE]
> U moet [de grootte van het subnet voor het beheerde exemplaar bepalen](sql-database-managed-instance-determine-size-vnet-subnet.md) voordat u de eerste instantie implementeert. U kunt het formaat van het subnet niet wijzigen nadat u de resources in hebt geplaatst.
>
> Als u van plan bent een bestaand virtueel netwerk te gebruiken, moet u die netwerk configuratie aanpassen aan uw beheerde exemplaar. Zie [een bestaand virtueel netwerk voor een beheerd exemplaar wijzigen](sql-database-managed-instance-configure-vnet-subnet.md)voor meer informatie.
>
> Nadat een beheerd exemplaar is gemaakt, wordt het beheerde exemplaar of VNet naar een andere resource groep of een ander abonnement niet ondersteund.  Het is ook niet mogelijk om het beheerde exemplaar naar een ander subnet te verplaatsen.
>

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

De eenvoudigste manier om een virtueel netwerk te maken en te configureren, is door een sjabloon voor Azure Resource Manager implementatie te gebruiken.

1. Meld u aan bij Azure Portal.

2. Selecteer de knop **implementeren naar Azure** :

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Met deze knop opent u een formulier dat u kunt gebruiken om de netwerk omgeving te configureren waarin u een beheerd exemplaar kunt implementeren.

   > [!Note]
   > Met deze Azure Resource Manager sjabloon wordt een virtueel netwerk met twee subnetten geïmplementeerd. Een subnet met de naam **ManagedInstances**is gereserveerd voor het beheerde exemplaar en heeft een vooraf geconfigureerde route tabel. Het andere subnet, dat **standaard**wordt genoemd, wordt gebruikt voor andere resources die toegang moeten hebben tot het beheerde exemplaar (bijvoorbeeld Azure virtual machines).

3. Configureer de netwerk omgeving. Op het volgende formulier kunt u para meters van uw netwerk omgeving configureren:

   ![Resource Manager-sjabloon voor het configureren van het Azure-netwerk](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

   U kunt de namen van het virtuele netwerk en subnetten wijzigen en de IP-bereiken aanpassen die aan uw netwerk bronnen zijn gekoppeld. Nadat u de knop **aanschaffen** hebt geselecteerd, wordt in dit formulier uw omgeving gemaakt en geconfigureerd. Als u niet twee subnetten nodig hebt, kunt u de standaard waarde verwijderen.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is een beheerd exemplaar?](sql-database-managed-instance.md)voor een overzicht.
- Meer informatie over [connectiviteits architectuur in het beheerde exemplaar](sql-database-managed-instance-connectivity-architecture.md).
- Meer informatie over het [wijzigen van een bestaand virtueel netwerk voor een beheerd exemplaar](sql-database-managed-instance-configure-vnet-subnet.md).
- Voor een zelf studie waarin wordt getoond hoe u een virtueel netwerk maakt, een beheerd exemplaar maakt en een Data Base herstelt vanuit een back-up van de data base, raadpleegt u [een Azure SQL database beheerd exemplaar maken](sql-database-managed-instance-get-started.md).
- Zie [Configuring a Custom DNS](sql-database-managed-instance-custom-dns.md)(Engelstalig) voor DNS-problemen.
