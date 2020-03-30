---
title: Een virtueel netwerk maken voor beheerde instantie
description: In dit artikel wordt beschreven hoe u een virtueel netwerk maakt waarin u Azure SQL Database Managed Instance implementeren.
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
ms.openlocfilehash: 9f115d51657993562642391a235de79420aa434a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823365"
---
# <a name="create-a-virtual-network-for-azure-sql-database-managed-instance"></a>Een virtueel netwerk maken voor Azure SQL Database Managed Instance

In dit artikel wordt uitgelegd hoe u een geldig virtueel netwerk en subnet maakt waar u Azure SQL Database Managed Instance implementeren.

Azure SQL Database Managed Instance moet worden geïmplementeerd binnen een [virtueel Azure-netwerk.](../virtual-network/virtual-networks-overview.md) Met deze implementatie worden de volgende scenario's mogelijk:

- Veilig privé-IP-adres
- Rechtstreeks verbinding maken met een beheerde instantie vanuit een on-premises netwerk
- Een beheerde instantie verbinden met gekoppelde server of een ander on-premises gegevensarchief
- Een beheerde instantie verbinden met Azure-bronnen  

> [!Note]
> U moet [de grootte van het subnet voor Beheerde instantie bepalen](sql-database-managed-instance-determine-size-vnet-subnet.md) voordat u de eerste instantie implementeert. U het formaat van het subnet niet wijzigen nadat u de resources erin hebt geplaatst.
>
> Als u van plan bent een bestaand virtueel netwerk te gebruiken, moet u die netwerkconfiguratie aanpassen aan uw beheerde instantie. Zie [Een bestaand virtueel netwerk wijzigen voor Beheerde instantie voor](sql-database-managed-instance-configure-vnet-subnet.md)meer informatie.
>
> Nadat een beheerde instantie is gemaakt, wordt het verplaatsen van de beheerde instantie of VNet naar een andere resourcegroep of abonnement niet ondersteund.


## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

De eenvoudigste manier om een virtueel netwerk te maken en te configureren, is door een Azure Resource Manager-implementatiesjabloon te gebruiken.

1. Meld u aan bij Azure Portal.

2. Selecteer de knop **Implementeren naar Azure:**

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Met deze knop opent u een formulier dat u gebruiken om de netwerkomgeving te configureren waar u Beheerde instantie implementeren.

   > [!Note]
   > Deze sjabloon Azure Resource Manager implementeert een virtueel netwerk met twee subnetten. Eén subnet, **ManagedInstances**genaamd, is gereserveerd voor Beheerde instantie en heeft een vooraf geconfigureerde routetabel. Het andere subnet, **standaard**genoemd, wordt gebruikt voor andere bronnen die toegang moeten krijgen tot beheerde instantie (bijvoorbeeld Azure Virtual Machines).

3. Configureer de netwerkomgeving. In het volgende formulier u parameters van uw netwerkomgeving configureren:

   ![Resourcebeheersjabloon voor het configureren van het Azure-netwerk](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

   U de namen van het virtuele netwerk en de subnetten wijzigen en de IP-bereiken aanpassen die zijn gekoppeld aan uw netwerkbronnen. Nadat u de knop **Aankoop** hebt geselecteerd, wordt uw omgeving in dit formulier gemaakt en geconfigureerd. Als u geen twee subnetten nodig hebt, u de standaardversie verwijderen.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is een beheerde instantie voor](sql-database-managed-instance.md)een overzicht.
- Meer informatie over [connectiviteitsarchitectuur in Managed Instance](sql-database-managed-instance-connectivity-architecture.md).
- Meer informatie over het [wijzigen van een bestaand virtueel netwerk voor Beheerde instantie](sql-database-managed-instance-configure-vnet-subnet.md).
- Zie Een Azure SQL Database Managed Instance maken voor een zelfstudie die laat zien hoe u een virtueel netwerk maakt, een beheerde instantie maakt en een database herstelt van een databaseback.For a tutorial that shows how to create a virtual network, create a Managed Instance, create a Managed Instance and restore a database from a database backup, see [Create an Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- Zie [Een aangepaste DNS configureren](sql-database-managed-instance-custom-dns.md)voor DNS-problemen.
