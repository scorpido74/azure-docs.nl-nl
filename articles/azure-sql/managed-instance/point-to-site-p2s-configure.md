---
title: Punt-naar-site-connectiviteit configureren met behulp van SSMS
titleSuffix: Azure SQL Managed Instance
description: Maak verbinding met Azure SQL Managed instance met behulp van SQL Server Management Studio (SSMS) met behulp van een punt-naar-site-verbinding vanaf een on-premises client computer.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, jovanpop
ms.date: 03/13/2019
ms.openlocfilehash: a629d230d63506a163cac0c530a1a8fbfed9627f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325076"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-azure-sql-managed-instance-from-on-premises"></a>Snelstartgids: een punt-naar-site-verbinding naar een met Azure SQL beheerd exemplaar van on-premises configureren
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In deze Quick Start wordt gedemonstreerd hoe u via een punt-naar-site verbinding maakt met behulp van [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) van een on-premises client computer. Zie [about Point-to-site VPN](../../vpn-gateway/point-to-site-about.md)(Engelstalig) voor meer informatie over punt-naar-site-verbindingen.

## <a name="prerequisites"></a>Vereisten

Voor deze snelstartgids geldt het volgende:

- Maakt gebruik van de resources die zijn gemaakt in  [een beheerde instantie maken](instance-create-quickstart.md) als uitgangs punt.
- Power shell 5,1 en Azure PowerShell 1.4.0 of hoger is vereist op uw on-premises client computer. Zie, indien nodig, de instructies voor [het installeren van de Azure PowerShell-module](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module).
- Vereist de nieuwste versie van [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) op uw on-premises client computer.

## <a name="attach-a-vpn-gateway-to-a-virtual-network"></a>Een VPN-gateway koppelen aan een virtueel netwerk

1. Open Power shell op uw on-premises client computer.

2. Kopieer dit Power shell-script. Met dit script wordt een VPN-gateway gekoppeld aan het virtuele netwerk van SQL Managed instance dat u hebt gemaakt in de Quick Start van [een beheerd exemplaar maken](instance-create-quickstart.md) . Dit script maakt gebruik van de Azure PowerShell AZ-module en doet het volgende voor op Windows-of Linux-gebaseerde hosts:

   - Maakt en installeert certificaten op een client computer
   - Hiermee wordt het IP-bereik voor het subnet van de toekomstige VPN-gateway berekend
   - Hiermee wordt het gateway-subnet gemaakt
   - Hiermee wordt de Azure Resource Manager sjabloon geïmplementeerd waarmee de VPN-gateway wordt gekoppeld aan het VPN-subnet

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

3. Plak het script in het Power shell-venster en geef de vereiste para meters op. De waarden voor `<subscriptionId>` , `<resourceGroup>` en `<virtualNetworkName>` moeten overeenkomen met die u hebt gebruikt voor de Snelstartgids [een beheerd exemplaar maken](instance-create-quickstart.md) . De waarde voor `<certificateNamePrefix>` kan een teken reeks van uw keuze zijn.

4. Voer het Power shell-script uit.

> [!IMPORTANT]
> Ga niet verder totdat het Power shell-script is voltooid.

## <a name="create-a-vpn-connection"></a>Een VPN-verbinding maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Open de resource groep waarin u de gateway van het virtuele netwerk hebt gemaakt en open vervolgens de bron van de virtuele netwerk gateway.
3. Selecteer **punt-naar-site-configuratie** en selecteer vervolgens **VPN-client downloaden**.

    ![VPN-client downloaden](./media/point-to-site-p2s-configure/download-vpn-client.png)  
4. Pak de bestanden uit vanuit het zip-bestand op uw on-premises client computer en open vervolgens de map met de uitgepakte bestanden.
5. Open de map **WindowsAmd64** en open het **VpnClientSetupAmd64.exe** -bestand.
6. Als u een bericht van **de Windows-computer met beveiliging** ontvangt, klikt u op **meer info** en vervolgens op **toch uitvoeren**.

    ![VPN-client installeren](./media/point-to-site-p2s-configure/vpn-client-defender.png)
7. Klik in het dialoogvenster Gebruikersaccountbeheer op **Ja** om door te gaan.
8. Selecteer **Ja** in het dialoog venster dat naar uw virtuele netwerk verwijst om de VPN-client voor uw virtuele netwerk te installeren.

## <a name="connect-to-the-vpn-connection"></a>Verbinding maken met de VPN-verbinding

1. Ga naar **VPN** in **netwerk & Internet** op uw on-premises client computer en selecteer het virtuele netwerk van uw SQL Managed instance om een verbinding met dit VNet tot stand te brengen. Het VNet bevindt zich in de volgende afbeelding met de naam **MyNewVNet**.

    ![VPN-verbinding](./media/point-to-site-p2s-configure/vpn-connection.png)  
2. Selecteer **Verbinden**.
3. Selecteer in het dialoog venster **verbinding maken**.

    ![VPN-verbinding](./media/point-to-site-p2s-configure/vpn-connection2.png)  
4. Als u wordt gevraagd of verbindings beheer verhoogde bevoegdheden nodig heeft om uw route tabel bij te werken, kiest u **door gaan**.
5. Selecteer **Ja** in het dialoog venster Gebruikersaccountbeheer om door te gaan.

   U hebt een VPN-verbinding tot stand gebracht met uw SQL Managed instance VNet.

    ![VPN-verbinding](./media/point-to-site-p2s-configure/vpn-connection-succeeded.png)  

## <a name="connect-with-ssms"></a>Verbinden met SSMS

1. Open SQL Server Management Studio op de on-premises client computer.
2. In het dialoog venster **verbinding maken met server** geeft u de volledig gekwalificeerde **hostnaam** voor uw beheerde exemplaar op in het vak **Server naam** .
3. Selecteer **SQL Server verificatie**, geef uw gebruikers naam en wacht woord op en selecteer vervolgens **verbinding maken**.

    ![SSMS Connect](./media/point-to-site-p2s-configure/ssms-connect.png)  

Nadat u verbinding hebt gemaakt, kunt u uw systeem-en gebruikers databases weer geven in het knoop punt data bases. U kunt ook verschillende objecten weer geven in de knoop punten beveiliging, Server objecten, replicatie, beheer, SQL Server Agent en XEvent Profiler.

## <a name="next-steps"></a>Volgende stappen

- Zie [een punt-naar-site-verbinding configureren](point-to-site-p2s-configure.md)voor een Snelstartgids waarin wordt getoond hoe u verbinding maakt vanaf een virtuele Azure-machine.
- Zie [Uw toepassingen verbinden met SQL Managed Instance](connect-application-instance.md) voor een overzicht van de verbindingsopties voor toepassingen.
- Als u een bestaande SQL Server-Data Base van on-premises naar een beheerd exemplaar wilt herstellen, kunt u [Azure database Migration service gebruiken voor migratie](../../dms/tutorial-sql-server-to-managed-instance.md) of de [T-SQL-opdracht herstellen](restore-sample-database-quickstart.md) om een back-upbestand van een Data Base te herstellen.
