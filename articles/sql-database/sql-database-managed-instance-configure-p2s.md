---
title: Een door P2S beheerd exemplaar configureren
description: Verbinding maken met een Azure SQL Database beheerd exemplaar met behulp van SQL Server Management Studio met behulp van een punt-naar-site-verbinding vanaf een on-premises client computer.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova, jovanpop
ms.date: 03/13/2019
ms.openlocfilehash: 30b2ba92174996ea2bae34e7553a3258d8ebee27
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79268884"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-an-azure-sql-database-managed-instance-from-on-premises"></a>Snelstartgids: een punt-naar-site-verbinding naar een door Azure SQL Database beheerd exemplaar van on-premises configureren

In deze Quick start ziet u hoe u via een punt-naar-site verbinding maakt met behulp van [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) van een on-premises client computer via een Azure SQL database Managed instance. Zie [about Point-to-site VPN](../vpn-gateway/point-to-site-about.md) (Engelstalig) voor meer informatie over punt-naar-site-verbindingen

## <a name="prerequisites"></a>Vereisten

Voor deze snelstartgids geldt het volgende:

- Maakt gebruik van de gemaakte resources [een beheerd exemplaar maken](sql-database-managed-instance-get-started.md) als uitgangs punt.
- Power shell 5,1 en AZ Power shell 1.4.0 of hoger is vereist op uw on-premises client computer. Zie, indien nodig, de instructies voor [het installeren van de Azure PowerShell-module](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module).
- Vereist de nieuwste versie van [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) op de on-premises client computer.

## <a name="attach-a-vpn-gateway-to-your-managed-instance-virtual-network"></a>Een VPN-gateway koppelen aan uw virtuele netwerk van het beheerde exemplaar

1. Open Power shell op uw on-premises client computer.

2. Kopieer dit Power shell-script. Met dit script wordt een VPN Gateway gekoppeld aan het virtuele netwerk van het beheerde exemplaar dat u hebt gemaakt in de Snelstartgids [een beheerd exemplaar maken](sql-database-managed-instance-get-started.md) . Dit script maakt gebruik van de Azure PowerShell AZ-module en gaat als volgt te werk voor op Windows of Linux gebaseerde hosts:

   - Maakt en installeert certificaten op de client computer
   - Hiermee wordt het volgende IP-adres voor VPN Gateway subnet berekend
   - Hiermee maakt u de GatewaySubnet
   - Hiermee wordt de Azure Resource Manager sjabloon geïmplementeerd waarmee de VPN Gateway wordt gekoppeld aan het VPN-subnet

     ```powershell
     $scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/attach-vpn-gateway'

     $parameters = @{
       subscriptionId = '<subscriptionId>'
       resourceGroupName = '<resourceGroupName>'
       virtualNetworkName = '<virtualNetworkName>'
       certificateNamePrefix  = '<certificateNamePrefix>'
       }

     Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/attachVPNGateway.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters, $scriptUrlBase
     ```

3. Plak het script in het Power shell-venster en geef de vereiste para meters op. De waarden voor `<subscriptionId>`, `<resourceGroup>`en `<virtualNetworkName>` moeten overeenkomen met die u hebt gebruikt voor de Snelstartgids voor het maken van een [beheerd exemplaar](sql-database-managed-instance-get-started.md) . De waarde voor `<certificateNamePrefix>` kan een teken reeks van uw keuze zijn.

4. Voer het Power shell-script uit.

> [!IMPORTANT]
> Ga niet verder totdat het Power shell-script is voltooid.

## <a name="create-a-vpn-connection-to-your-managed-instance"></a>Een VPN-verbinding maken met uw beheerde exemplaar

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Open de resource groep waarin u de gateway van het virtuele netwerk hebt gemaakt en open vervolgens de bron van de virtuele netwerk gateway.
3. Selecteer **punt-naar-site-configuratie** en selecteer vervolgens **VPN-client downloaden**.

    ![VPN-client downloaden](./media/sql-database-managed-instance-configure-p2s/download-vpn-client.png)  
4. Pak de bestanden uit vanuit het zip-bestand op uw on-premises client computer en open vervolgens de map met de uitgepakte bestanden.
5. Open de map**WindowsAmd64** en open het bestand **VpnClientSetupAmd64. exe** .
6. Als u een bericht van **de Windows-computer met beveiliging** ontvangt, klikt u op **meer info** en vervolgens op **toch uitvoeren**.

    ![VPN-client installeren](./media/sql-database-managed-instance-configure-p2s/vpn-client-defender.png)\
7. Klik in het dialoogvenster Gebruikersaccountbeheer op **Ja** om door te gaan.
8. Selecteer **Ja** in het dialoog venster dat naar uw virtuele netwerk verwijst om de VPN-client voor uw virtuele netwerk te installeren.

## <a name="connect-to-the-vpn-connection"></a>Verbinding maken met de VPN-verbinding

1. Ga naar **VPN** in **netwerk & Internet** op uw on-premises client computer en selecteer het virtuele netwerk van het beheerde exemplaar om een verbinding met dit VNet tot stand te brengen. Het VNet bevindt zich in de volgende afbeelding met de naam **MyNewVNet**.

    ![VPN-verbinding](./media/sql-database-managed-instance-configure-p2s/vpn-connection.png)  
2. Selecteer **Verbinden**.
3. Selecteer in het dialoog venster **verbinding maken**.

    ![VPN-verbinding](./media/sql-database-managed-instance-configure-p2s/vpn-connection2.png)  
4. Als u wordt gevraagd om een verhoogde bevoegdheid nodig om de route tabel bij te werken, klikt u op **door gaan**.
5. Selecteer **Ja** in het dialoog venster Gebruikersaccountbeheer om door te gaan.

   U hebt een VPN-verbinding tot stand gebracht met uw beheerde exemplaar-VNet.

    ![VPN-verbinding](./media/sql-database-managed-instance-configure-p2s/vpn-connection-succeeded.png)  

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>SSMS gebruiken om verbinding te maken met het beheerde exemplaar

1. Open SQL Server Management Studio (SSMS) op de on-premises client computer.
2. In het dialoog venster **verbinding maken met server** geeft u de volledig gekwalificeerde **hostnaam** voor uw beheerde exemplaar op in het vak **Server naam** .
3. Selecteer **SQL Server verificatie**, geef uw gebruikers naam en wacht woord op en selecteer vervolgens **verbinding maken**.

    ![ssms verbinden](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

Nadat u verbinding hebt gemaakt, kunt u uw systeem-en gebruikers databases weer geven in het knoop punt data bases. U kunt ook verschillende objecten weer geven in de knoop punten beveiliging, Server objecten, replicatie, beheer, SQL Server Agent en XEvent Profiler.

## <a name="next-steps"></a>Volgende stappen

- Zie [een punt-naar-site-verbinding configureren](sql-database-managed-instance-configure-p2s.md)voor een Snelstartgids waarin wordt getoond hoe u verbinding maakt vanaf een virtuele Azure-machine.
- Zie [Verbinding maken tussen uw toepassing en het beheerde exemplaar van Azure SQL Database](sql-database-managed-instance-connect-app.md) voor een overzicht van de verbindingsopties voor toepassingen.
- Als u een bestaande SQL Server-Data Base van on-premises naar een beheerd exemplaar wilt herstellen, kunt u de [Azure database Migration service (DMS) voor migratie](../dms/tutorial-sql-server-to-managed-instance.md) of de [T-SQL-opdracht voor terugzetten](sql-database-managed-instance-get-started-restore.md) gebruiken om een back-upbestand van een Data Base te herstellen.
