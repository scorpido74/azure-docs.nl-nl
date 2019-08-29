---
title: Beveiligings overwegingen voor SQL Server in azure | Microsoft Docs
description: Dit onderwerp bevat algemene richt lijnen voor het beveiligen van SQL Server die worden uitgevoerd op een virtuele machine van Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6b2f9853c2699b69a0c9be13e6925a4b30f358f7
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102031"
---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Veiligheidsoverwegingen voor SQL Server in Azure Virtual Machines

Dit onderwerp bevat algemene beveiligings richtlijnen die u helpen bij het opzetten van beveiligde toegang tot SQL Server exemplaren in een virtuele Azure-machine (VM).

Azure voldoet aan verschillende industriële voor schriften en standaarden waarmee u een compatibele oplossing kunt bouwen met SQL Server uitgevoerd op een virtuele machine. Zie [Vertrouwenscentrum van Azure](https://azure.microsoft.com/support/trust-center/)voor meer informatie over de naleving van de regelgeving met Azure.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>Toegang tot de SQL-VM beheren

Wanneer u uw SQL Server virtuele machine maakt, moet u overwegen hoe u zorgvuldig kunt bepalen wie toegang heeft tot de machine en SQL Server. Over het algemeen moet u het volgende doen:

- Beperk de toegang tot SQL Server tot alleen de toepassingen en clients die er behoefte aan hebben.
- Volg de aanbevolen procedures voor het beheren van gebruikers accounts en wacht woorden.

In de volgende secties vindt u suggesties voor het door lopen van deze punten.

## <a name="secure-connections"></a>Beveiligde verbindingen

Wanneer u een SQL Server virtuele machine maakt met een galerie-afbeelding, biedt de **SQL Server connectiviteits** optie u de keuze van **lokale (binnen VM)** , **privé (binnen Virtual Network)** of **openbaar (Internet)** .

![SQL Server connectiviteit](./media/virtual-machines-windows-sql-security/sql-vm-connectivity-option.png)

Voor de beste beveiliging kiest u de meest beperkende optie voor uw scenario. Als u bijvoorbeeld een toepassing uitvoert die toegang heeft tot SQL Server op dezelfde VM, is **lokaal** de veiligste keuze. Als u een Azure-toepassing uitvoert waarvoor toegang tot de SQL Server vereist is, wordt de communicatie met **privé** alleen SQL Server binnen de opgegeven [Azure-Virtual Network](../../../virtual-network/virtual-networks-overview.md). Als u **open bare** (Internet) toegang tot de SQL Server virtuele machine nodig hebt, moet u ervoor zorgen dat u andere aanbevolen procedures in dit onderwerp volgt om uw aanval Surface Area te verminderen.

De geselecteerde opties in de portal gebruiken binnenkomende beveiligings regels op de [netwerk beveiligings groep](../../../virtual-network/security-overview.md) van de VM (NSG) om netwerk verkeer naar uw virtuele machine toe te staan of te weigeren. U kunt nieuwe regels voor binnenkomende NSG wijzigen of maken om verkeer toe te staan voor de SQL Server poort (standaard 1433). U kunt ook specifieke IP-adressen opgeven die via deze poort mogen communiceren.

![Regels voor netwerkbeveiligingsgroepen](./media/virtual-machines-windows-sql-security/sql-vm-network-security-group-rules.png)

Naast NSG-regels om netwerk verkeer te beperken, kunt u ook de Windows Firewall op de virtuele machine gebruiken.

Als u eind punten met het klassieke implementatie model gebruikt, verwijdert u alle eind punten op de virtuele machine als u deze niet gebruikt. Zie [de ACL voor een eind punt beheren](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint)voor instructies over het gebruik van acl's met eind punten. Dit is niet nodig voor Vm's die gebruikmaken van Resource Manager.

Ten slotte kunt u versleutelde verbindingen inschakelen voor het exemplaar van de SQL Server data base-engine in uw virtuele Azure-machine. Configureer het SQL Server-exemplaar met een ondertekend certificaat. Zie versleutelde [verbindingen met de data base-engine en de](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) syntaxis van de [verbindings reeks](https://msdn.microsoft.com/library/ms254500.aspx)inschakelen voor meer informatie.

## <a name="use-a-non-default-port"></a>Een niet-standaard poort gebruiken

SQL Server luistert standaard naar een bekende poort 1433. Configureer SQL Server om te Luis teren naar een niet-standaard poort, zoals 1401, voor een betere beveiliging. Als u een installatie kopie van SQL Server galerie inricht in de Azure Portal, kunt u deze poort opgeven op de Blade **SQL Server instellingen** .

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Als u dit na de inrichting wilt configureren, hebt u twee opties:

- Voor virtuele machines van Resource Manager kunt u **beveiliging** selecteren op basis van de resource van de [virtuele SQL-machines](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource). Dit biedt een optie om de poort te wijzigen.

  ![TCP-poort wijziging in Portal](./media/virtual-machines-windows-sql-security/sql-vm-change-tcp-port.png)

- Voor klassieke Vm's of voor SQL Server Vm's die niet zijn ingericht met de portal, kunt u de poort hand matig configureren door extern verbinding te maken met de virtuele machine. Zie [een server configureren om te Luis teren naar een specifieke TCP-poort](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port)voor de configuratie stappen. Als u deze hand matige techniek gebruikt, moet u ook een Windows Firewall regel toevoegen om binnenkomend verkeer op die TCP-poort toe te staan.

> [!IMPORTANT]
> Het is een goed idee om een niet-standaard poort op te geven als uw SQL Server poort is geopend voor open bare Internet verbindingen.

Wanneer SQL Server op een niet-standaard poort luistert, moet u de poort opgeven wanneer u verbinding maakt. Denk bijvoorbeeld aan een scenario waarbij het IP-adres van de server 13.55.255.255 is en SQL Server luistert op poort 1401. Als u verbinding wilt maken met SQL Server, `13.55.255.255,1401` geeft u in de Connection String op.

## <a name="manage-accounts"></a>Accounts beheren

U wilt niet dat aanvallers eenvoudig account namen of wacht woorden raden. Gebruik de volgende tips om te helpen:

- Maak een uniek lokaal Administrator-account dat niet de naam **Administrator**heeft.

- Gebruik complexe sterke wacht woorden voor al uw accounts. Zie een artikel met [een sterk wacht woord maken](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password) voor meer informatie over het maken van een sterk wacht woord.

- In azure wordt standaard Windows-verificatie geselecteerd tijdens de installatie van de virtuele machine SQL Server. Daarom is de **sa** -aanmelding uitgeschakeld en wordt er een wacht woord toegewezen door Setup. U wordt aangeraden de **sa** -aanmelding niet te gebruiken of in te scha kelen. Als u een SQL-aanmelding nodig hebt, gebruikt u een van de volgende strategieën:

  - Maak een SQL-account met een unieke naam die het lidmaatschap van de **sysadmin** heeft. U kunt dit doen vanuit de portal door **SQL-verificatie** in te scha kelen tijdens het inrichten.

    > [!TIP] 
    > Als u geen SQL-verificatie inschakelt tijdens het inrichten, moet u de verificatie modus hand matig wijzigen in **SQL Server en Windows-verificatie modus**. Zie voor meer informatie [Server verificatie modus wijzigen](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Als u de **sa** -aanmelding moet gebruiken, schakelt u de aanmelding na het inrichten in en wijst u een nieuw sterk wacht woord toe.

## <a name="follow-on-premises-best-practices"></a>Volg de aanbevolen procedures voor het uitvoeren van on-premises

Naast de procedures die in dit onderwerp worden beschreven, raden we u aan de traditionele lokale beveiligings procedures te controleren en te implementeren, indien van toepassing. Zie [beveiligings overwegingen voor een SQL Server-installatie](https://docs.microsoft.com/sql/sql-server/install/security-considerations-for-a-sql-server-installation) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie [Aanbevolen procedures voor de prestaties van SQL Server in Azure virtual machines](virtual-machines-windows-sql-performance.md)als u ook geïnteresseerd bent in Aanbevolen procedures voor de prestaties.

Zie [SQL Server op azure virtual machines Overview](virtual-machines-windows-sql-server-iaas-overview.md)voor andere onderwerpen met betrekking tot het uitvoeren van SQL Server in azure vm's. Als u vragen hebt over virtuele machines met SQL Server, raadpleegt u [Veelgestelde vragen](virtual-machines-windows-sql-server-iaas-faq.md).

