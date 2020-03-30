---
title: Beveiligingsoverwegingen voor SQL Server in Azure | Microsoft Documenten
description: Dit onderwerp biedt algemene richtlijnen voor het beveiligen van SQL Server die wordt uitgevoerd in een Azure Virtual Machine.
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
ms.openlocfilehash: f5ea0ddff38532b119d8d984f2dabd6d898b44a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77031353"
---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Veiligheidsoverwegingen voor SQL Server in virtuele Azure-machines

Dit onderwerp bevat algemene beveiligingsrichtlijnen die helpen bij het tot stand brengen van veilige toegang tot SQL Server-exemplaren in een virtuele Azure-machine (VM).

Azure voldoet aan verschillende branchevoorschriften en -standaarden waarmee u een compatibele oplossing bouwen met SQL Server die in een virtuele machine wordt uitgevoerd. Zie [Azure Trust Center](https://azure.microsoft.com/support/trust-center/)voor informatie over naleving van de regelgeving met Azure.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>Toegang tot de SQL VM beheren

Bedenk bij het maken van uw virtuele SQL Server-machine hoe u zorgvuldig bepalen wie toegang heeft tot de machine en tot SQL Server. In het algemeen moet u het volgende doen:

- Beperk de toegang tot SQL Server tot alleen de toepassingen en clients die deze nodig hebben.
- Volg aanbevolen procedures voor het beheren van gebruikersaccounts en wachtwoorden.

De volgende secties geven suggesties over het denken door middel van deze punten.

## <a name="secure-connections"></a>Beveiligde verbindingen

Wanneer u een virtuele SQL Server-machine maakt met een galerijafbeelding, u met de optie **SQL Server Connectivity** kiezen uit **Lokaal (binnen VM),** **Privé (binnen virtueel netwerk)** of **Openbaar (internet).**

![SQL Server-connectiviteit](./media/virtual-machines-windows-sql-security/sql-vm-connectivity-option.png)

Kies voor de beste beveiliging de meest beperkende optie voor uw scenario. Als u bijvoorbeeld een toepassing uitvoert die toegang krijgt tot SQL Server op dezelfde VM, is **Lokaal** de veiligste keuze. Als u een Azure-toepassing uitvoert waarvoor toegang tot de SQL Server vereist is, beveiligt **Privé** de communicatie naar SQL Server alleen binnen het opgegeven [Azure Virtual Network.](../../../virtual-network/virtual-networks-overview.md) Als u **openbare** (internet)toegang tot de SQL Server VM nodig hebt, moet u andere aanbevolen procedures in dit onderwerp volgen om uw aanvalsoppervlak te verminderen.

De geselecteerde opties in de portal gebruiken binnenkomende beveiligingsregels voor de [netwerkbeveiligingsgroep](../../../virtual-network/security-overview.md) (NSG) van de VM om netwerkverkeer toe te staan of te weigeren naar uw virtuele machine. U nieuwe binnenkomende NSG-regels wijzigen of maken om verkeer naar de SQL Server-poort toe te staan (standaard 1433). U ook specifieke IP-adressen opgeven die via deze poort mogen communiceren.

![Regels voor netwerkbeveiligingsgroepen](./media/virtual-machines-windows-sql-security/sql-vm-network-security-group-rules.png)

Naast NSG-regels om het netwerkverkeer te beperken, u ook de Windows Firewall op de virtuele machine gebruiken.

Als u eindpunten gebruikt met het klassieke implementatiemodel, verwijdert u eindpunten op de virtuele machine als u ze niet gebruikt. Zie [ACL beheren op een eindpunt](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint)voor instructies over het gebruik van ACL's met eindpunten. Dit is niet nodig voor VM's die de Resource Manager gebruiken.

Tot slot u overwegen versleutelde verbindingen in te schakelen voor het exemplaar van de SQL Server Database Engine in uw virtuele Azure-machine. SQL-server-instantie configureren met een ondertekend certificaat. Zie Versleutelde verbindingen inschakelen voor de syntaxis [databaseengine](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) en [verbindingstekenreeks](https://msdn.microsoft.com/library/ms254500.aspx)voor meer informatie .

## <a name="encryption"></a>Versleuteling

Beheerde schijven bieden serverzijdeversleuteling en Azure-schijfversleuteling. [Server Side Encryption](/azure/virtual-machines/windows/disk-encryption) biedt encryptie-at-rest en beschermt uw gegevens om te voldoen aan uw organisatorische beveiligings- en nalevingsverplichtingen. [Azure Disk Encryption](/azure/security/fundamentals/azure-disk-encryption-vms-vmss) maakt gebruik van Bitlocker- of DM-Crypt-technologie en integreert met Azure Key Vault om zowel het besturingssysteem als de gegevensschijven te versleutelen. 

## <a name="use-a-non-default-port"></a>Een niet-standaardpoort gebruiken

Standaard gebruikt SQL Server een bekende poort, namelijk 1433. Configureer SQL Server voor meer beveiliging om te luisteren op een niet-standaardpoort, zoals 1401. Als u een SQL Server-galerieafbeelding inde Azure-portal indient, u deze poort opgeven in het **sql-server-instellingenblad.**

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Als u dit wilt configureren na inrichten, hebt u twee opties:

- Voor Vm's voor Resourcebeheer u **Beveiliging** selecteren in de [SQL-bron voor virtuele machines.](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) Dit biedt een optie om de poort te wijzigen.

  ![TCP-poortwijziging in portal](./media/virtual-machines-windows-sql-security/sql-vm-change-tcp-port.png)

- Voor klassieke VM's of voor SQL Server VM's die niet met de portal zijn ingericht, u de poort handmatig configureren door op afstand verbinding te maken met de VM. Zie Een server [configureren om te luisteren op een specifieke TCP-poort voor](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port)de configuratiestappen. Als u deze handmatige techniek gebruikt, moet u ook een Windows Firewall-regel toevoegen om binnenkomend verkeer op die TCP-poort toe te staan.

> [!IMPORTANT]
> Het opgeven van een niet-standaardpoort is een goed idee als uw SQL Server-poort openstaat voor openbare internetverbindingen.

Wanneer SQL Server luistert op een niet-standaardpoort, moet u de poort opgeven wanneer u verbinding maakt. Denk bijvoorbeeld aan een scenario waarin het IP-adres van de server 13.55.255.255 is en SQL Server luistert op poort 1401. Als u verbinding wilt maken `13.55.255.255,1401` met SQL Server, geeft u dit op in de verbindingstekenreeks.

## <a name="manage-accounts"></a>Accounts beheren

U wilt niet dat aanvallers gemakkelijk accountnamen of wachtwoorden raden. Gebruik de volgende tips om te helpen:

- Maak een uniek lokaal beheerdersaccount met de naam **Administrator**.

- Gebruik complexe sterke wachtwoorden voor al uw accounts. Zie [Een sterk wachtwoordartikel maken](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password) voor meer informatie over het maken van een sterk wachtwoord.

- Azure selecteert standaard Windows-verificatie tijdens de installatie van SQL Server Virtual Machine. Daarom is de **SA-login** uitgeschakeld en wordt een wachtwoord toegewezen door setup. Wij raden u aan de **SA-login** niet te gebruiken of in te schakelen. Als u een SQL-login moet hebben, gebruikt u een van de volgende strategieën:

  - Maak een SQL-account met een unieke naam met **een sysadmin-lidmaatschap.** U dit doen vanuit de portal door **SQL-verificatie** in te schakelen tijdens het inrichten.

    > [!TIP] 
    > Als u SQL-verificatie niet inschakelt tijdens het inrichten, moet u de verificatiemodus handmatig wijzigen in **SQL Server en Windows-verificatiemodus.** Zie [Serververificatiemodus wijzigen](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode)voor meer informatie .

  - Als u de **SA-login** moet gebruiken, schakelt u de login in na inlevering en wijst u een nieuw sterk wachtwoord toe.

## <a name="additional-best-practices"></a>Aanvullende aanbevolen procedures

Naast de praktijken die in dit onderwerp worden beschreven, raden we u aan de aanbevolen beveiligingsprocedures van zowel traditionele on-premises beveiligingspraktijken als best practices voor beveiliging van virtuele machines te bekijken en te implementeren. 

Zie [Beveiligingsoverwegingen voor een SQL Server-installatie](/sql/sql-server/install/security-considerations-for-a-sql-server-installation) en het [beveiligingscentrum](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database)voor meer informatie over on-premises beveiligingspraktijken. 

Zie het [beveiligingsoverzicht voor virtuele machines](/azure/security/fundamentals/virtual-machines-overview)voor meer informatie over beveiliging van virtuele machines.


## <a name="next-steps"></a>Volgende stappen

Als u ook geïnteresseerd bent in best practices rond prestaties, raadpleegt u [Aanbevolen procedures voor prestaties voor SQL Server in Azure Virtual Machines.](virtual-machines-windows-sql-performance.md)

Zie [SQL Server op Azure Virtual Machines-overzicht](virtual-machines-windows-sql-server-iaas-overview.md)voor andere onderwerpen met betrekking tot het uitvoeren van SQL Server in Azure VM's. Als u vragen hebt over virtuele machines met SQL Server, raadpleegt u [Veelgestelde vragen](virtual-machines-windows-sql-server-iaas-faq.md).

