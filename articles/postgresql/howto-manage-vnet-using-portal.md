---
title: Virtuele netwerkregels gebruiken - Azure portal - Azure Database for PostgreSQL - Single Server
description: VNet-serviceeindpunten en -regels maken en beheren Azure Database voor PostgreSQL - Single Server met behulp van de Azure-portal
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 413c3a7b6fdcda996d3db548fb53f358eb8c71e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978283"
---
# <a name="create-and-manage-vnet-service-endpoints-and-vnet-rules-in-azure-database-for-postgresql---single-server-by-using-the-azure-portal"></a>VNet-serviceeindpunten en VNet-regels maken en beheren in Azure Database voor PostgreSQL - Single Server met behulp van de Azure-portal
Eindpunten en regels voor Virtual Network -services breiden de privéadresruimte van een virtueel netwerk uit tot uw Azure-database voor PostgreSQL-server. Zie [Azure Database voor PostgreSQL Server VNet-serviceeindpunten voor](concepts-data-access-and-security-vnet.md)een overzicht van Azure Database voor PostgreSQL VNet-serviceeindpunten. VNet-serviceeindpunten zijn beschikbaar in alle ondersteunde regio's voor Azure Database voor PostgreSQL.

> [!NOTE]
> Ondersteuning voor VNet-serviceeindpunten is alleen voor servers met algemeen gebruik en geheugengeoptimaliseerd.
> Als vnet-peering in het geval van VNet-peering door een gemeenschappelijke VNet-gateway met serviceeindpunten stroomt en naar de peer moet stromen, maakt u een ACL/VNet-regel waarmee Azure Virtual Machines in de Gateway VNet toegang krijgen tot de Azure Database voor PostgreSQL-server.


## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Een VNet-regel maken en serviceeindpunten inschakelen in de Azure-portal

1. Klik op de pagina PostgreSQL-server onder het kopje Instellingen op **Verbindingsbeveiliging** om het deelvenster Verbindingsbeveiliging voor Azure Database voor PostgreSQL te openen. 

2. Controleer of het besturingselement Toegang tot Azure-services toestaan is ingesteld op **UIT.**

> [!Important]
> Als u het besturingselement aan aan laat staan, accepteert uw Azure PostgreSQL Database-server communicatie van elk subnet. Het verlaten van de besturingselement ingesteld op AAN kan worden overmatige toegang vanuit een oogpunt van beveiliging. De eindpuntfunctie van de Microsoft Azure Virtual Network-service kan, in coördinatie met de functie virtuele netwerkregel van Azure Database voor PostgreSQL, samen uw beveiligingsoppervlak verminderen.

3. Klik vervolgens op **+ Bestaand virtueel netwerk toevoegen**. Als u geen bestaand VNet hebt, u op **+ Nieuw virtueel netwerk maken** klikken om er een te maken. Zie [Snelstart: een virtueel netwerk maken met de Azure-portal](../virtual-network/quick-create-portal.md)

   ![Azure-portal - klik op Verbindingsbeveiliging](./media/howto-manage-vnet-using-portal/1-connection-security.png)

4. Voer een VNet-regelnaam in, selecteer het abonnement, het virtuele netwerk en de naam Subnet en klik op **Inschakelen**. Hiermee worden automatisch VNet-serviceeindpunten op het subnet ingeschakeld met behulp van de **Microsoft.SQL-servicetag.**

   ![Azure-portal - VNet configureren](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

    Het account moet beschikken over de benodigde machtigingen voor het maken van een virtueel netwerk en een service-eindpunt.

    Serviceeindpunten kunnen onafhankelijk van elkaar worden geconfigureerd op virtuele netwerken, door een gebruiker met schrijftoegang tot het virtuele netwerk.
    
    Om Azure-serviceresources te beveiligen voor een VNet, moet de gebruiker toestemming hebben om "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" te gebruiken voor de subnetten die worden toegevoegd. Deze machtiging is standaard opgenomen in de ingebouwde service-beheerdersrollen en kan worden gewijzigd door aangepaste rollen te maken.
    
    Meer informatie over [ingebouwde rollen](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) en het toewijzen van specifieke machtigingen voor [aangepaste rollen](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).
    
    VNets en Azure-serviceresources kunnen in hetzelfde abonnement of in verschillende abonnementen zitten. Als de VNet- en Azure-serviceresources zich in verschillende abonnementen bevinden, moeten de resources onder dezelfde AD-tenant (Active Directory) staan. Controleer of beide abonnementen de **Microsoft.Sql-resourceprovider** hebben geregistreerd. Voor meer informatie verwijzen [resource-manager-registratie][resource-manager-portal]

   > [!IMPORTANT]
   > Het wordt ten zeerste aanbevolen om dit artikel over serviceeindpuntconfiguraties en -overwegingen te lezen voordat serviceeindpunten worden geconfigureerd. **Eindpunt van de virtuele netwerkservice:** Een [eindpunt van de service voor virtuele netwerken](../virtual-network/virtual-network-service-endpoints-overview.md) is een subnet waarvan de eigenschapswaarden een of meer formele namen van azure-servicetypen bevatten. VNet-serviceseindpunten gebruiken de servicenaam **Microsoft.Sql**, die verwijst naar de Azure-service met de naam SQL Database. Deze servicetag is ook van toepassing op de Azure SQL Database, Azure Database voor PostgreSQL- en MySQL-services. Het is belangrijk op te merken dat bij het toepassen van de **Microsoft.Sql-servicetag** op een VNet-serviceeindpunt serviceeindpunt wordt geconfigureerd voor alle Azure Database-services, waaronder Azure SQL Database, Azure Database voor PostgreSQL en Azure Database voor MySQL-servers op het subnet. 
   > 

5. Zodra u is ingeschakeld, klikt u op **OK** en ziet u dat VNet-serviceeindpunten zijn ingeschakeld, samen met een VNet-regel.

   ![VNet-serviceeindpunten ingeschakeld en VNet-regel gemaakt](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Volgende stappen
- Op dezelfde manier u scripten om [VNet-serviceeindpunten in te schakelen en een VNET-regel voor Azure Database voor PostgreSQL maken met Azure CLI](howto-manage-vnet-using-cli.md).
- Zie [Verbindingsbibliotheken voor Azure Database voor PostgreSQL-server voor](./concepts-connection-libraries.md) hulp bij het maken van verbinding met een Azure Database voor PostgreSQL

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md