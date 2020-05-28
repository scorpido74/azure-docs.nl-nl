---
title: Punt-naar-site-connectiviteit configureren met behulp van SSMS
titleSuffix: Azure SQL Managed Instance
description: Maak verbinding met Azure SQL Managed instance met behulp van SQL Server Management Studio (SSMS) met behulp van een punt-naar-site-verbinding vanaf een on-premises client computer.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova, jovanpop
ms.date: 03/13/2019
ms.openlocfilehash: 4e56454142cdbc91f621ca20532e689d5c6e6458
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84047991"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-azure-sql-managed-instance-from-on-premises"></a>Snelstartgids: een punt-naar-site-verbinding naar een met Azure SQL beheerd exemplaar van on-premises configureren
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In deze Quick Start wordt gedemonstreerd hoe u via een punt-naar-site verbinding maakt met behulp van [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) van een on-premises client computer. Zie [about Point-to-site VPN](../../vpn-gateway/point-to-site-about.md) (Engelstalig) voor meer informatie over punt-naar-site-verbindingen

## <a name="prerequisites"></a>Vereisten

Voor deze snelstartgids geldt het volgende:

- Maakt gebruik van de resources die zijn gemaakt in [een SQL Managed instance maken](instance-create-quickstart.md) als uitgangs punt.
- Power shell 5,1 en AZ Power shell 1.4.0 of hoger is vereist op uw on-premises client computer. Zie, indien nodig, de instructies voor [het installeren van de Azure PowerShell-module](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module).
- Vereist de nieuwste versie van [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) op de on-premises client computer.

## <a name="attach-a-vpn-gateway-to-virtual-network"></a>Een VPN-gateway koppelen aan een virtueel netwerk

1. Open Power shell op uw on-premises client computer.

2. Kopieer dit Power shell-script. Met dit script wordt een VPN Gateway gekoppeld aan het virtuele netwerk van SQL Managed instance dat u hebt gemaakt in de Snelstartgids [een door SQL beheerd exemplaar maken](instance-create-quickstart.md) . Dit script maakt gebruik van de Azure PowerShell AZ-module en voert de volgende handelingen uit voor op Windows of Linux gebaseerde hosts:

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

3. Plak het script in het Power shell-venster en geef de vereiste para meters op. De waarden voor `<subscriptionId>` , `<resourceGroup>` en `<virtualNetworkName>` moeten overeenkomen met die u hebt gebruikt voor de Snelstartgids voor het maken van een [SQL Managed instance](instance-create-quickstart.md) . De waarde voor `<certificateNamePrefix>` kan een teken reeks van uw keuze zijn.

4. Voer het Power shell-script uit.

> [!IMPORTANT]
> Ga niet verder totdat het Power shell-script is voltooid.

## <a name="create-a-vpn-connection"></a>Een VPN-verbinding maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Open de resource groep waarin u de gateway van het virtuele netwerk hebt gemaakt en open vervolgens de bron van de virtuele netwerk gateway.
3. Selecteer **punt-naar-site-configuratie** en selecteer vervolgens **VPN-client downloaden**.

    ![VPN-client downloaden](./media/point-to-site-p2s-configure/download-vpn-client.png)  
4. Pak de bestanden uit vanuit het zip-bestand op uw on-premises client computer en open vervolgens de map met de uitgepakte bestanden.
5. Open de map**WindowsAmd64** en open het bestand **VpnClientSetupAmd64. exe** .
6. Als u een bericht van **de Windows-computer met beveiliging** ontvangt, klikt u op **meer info** en vervolgens op **toch uitvoeren**.

    ![VPN-client installeren](./media/point-to-site-p2s-configure/vpn-client-defender.png)\
7. Klik in het dialoogvenster Gebruikersaccountbeheer op **Ja** om door te gaan.
8. Selecteer **Ja** in het dialoog venster dat naar uw virtuele netwerk verwijst om de VPN-client voor uw virtuele netwerk te installeren.

## <a name="connect-to-the-vpn-connection"></a>Verbinding maken met de VPN-verbinding

1. Ga naar **VPN** in **netwerk & Internet** op uw on-premises client computer en selecteer het virtuele netwerk van uw SQL Managed instance om een verbinding met dit VNet tot stand te brengen. Het VNet bevindt zich in de volgende afbeelding met de naam **MyNewVNet**.

    ![VPN-verbinding](./media/point-to-site-p2s-configure/vpn-connection.png)  
2. Selecteer **Verbinding maken**.
3. Selecteer in het dialoog venster **verbinding maken**.

    ![VPN-verbinding](./media/point-to-site-p2s-configure/vpn-connection2.png)  
4. Als u wordt gevraagd om een verhoogde bevoegdheid nodig om de route tabel bij te werken, klikt u op **door gaan**.
5. Selecteer **Ja** in het dialoog venster Gebruikersaccountbeheer om door te gaan.

   U hebt een VPN-verbinding tot stand gebracht met uw SQL Managed instance VNet.

    ![VPN-verbinding](./media/point-to-site-p2s-configure/vpn-connection-succeeded.png)  

## <a name="connect-with-ssms"></a>Verbinden met SSMS

1. Open SQL Server Management Studio (SSMS) op de on-premises client computer.
2. Voer in het dialoog venster **verbinding maken met server** de volledig gekwalificeerde **HOSTNAAM** voor uw SQL Managed instance in het vak **Server naam** in.
3. Selecteer **SQL Server verificatie**, geef uw gebruikers naam en wacht woord op en selecteer vervolgens **verbinding maken**.

    ![ssms verbinden](./media/point-to-site-p2s-configure/ssms-connect.png)  

Nadat u verbinding hebt gemaakt, kunt u uw systeem-en gebruikers databases weer geven in het knoop punt data bases. U kunt ook verschillende objecten weer geven in de knoop punten beveiliging, Server objecten, replicatie, beheer, SQL Server Agent en XEvent Profiler.

## <a name="next-steps"></a>Volgende stappen

- Zie [een punt-naar-site-verbinding configureren](point-to-site-p2s-configure.md)voor een Snelstartgids waarin wordt getoond hoe u verbinding maakt vanaf een virtuele Azure-machine.
- Zie [Connect your applications to SQL Managed instance](connect-application-instance.md)(Engelstalig) voor een overzicht van de verbindings opties voor toepassingen.
- Als u een bestaande SQL Server-Data Base van on-premises wilt herstellen naar een SQL-beheerd exemplaar, kunt u de [Azure database Migration service (DMS) voor migratie](../../dms/tutorial-sql-server-to-managed-instance.md) of de [T-SQL-opdracht](restore-sample-database-quickstart.md) voor terugzetten gebruiken om een back-upbestand van een Data Base te herstellen.
