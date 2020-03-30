---
title: P2S configureren - Exemplaar Beheerd
description: Maak verbinding met een Azure SQL Database Managed Instance met SQL Server Management Studio via een point-to-site verbinding vanaf een on-premises clientcomputer.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268884"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-an-azure-sql-database-managed-instance-from-on-premises"></a>Snelstart: een point-to-site-verbinding met een Azure SQL Database Managed Instance configureren vanuit on-premises

Met deze quickstart u verbinding maken met een Azure SQL Database Managed Instance met [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) via een on-premises clientcomputer via een point-to-site-verbinding. Zie Over point-to-site VPN voor informatie over [point-to-site-verbindingen](../vpn-gateway/point-to-site-about.md)

## <a name="prerequisites"></a>Vereisten

Voor deze snelstartgids geldt het volgende:

- Gebruikt de resources die [een beheerde instantie maken](sql-database-managed-instance-get-started.md) als uitgangspunt.
- Vereist PowerShell 5.1 en AZ PowerShell 1.4.0 of hoger op uw on-premises clientcomputer. Raadpleeg indien nodig de instructies voor [het installeren van de Azure PowerShell-module.](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module)
- Vereist de nieuwste versie van [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) op uw on-premises clientcomputer.

## <a name="attach-a-vpn-gateway-to-your-managed-instance-virtual-network"></a>Een VPN-gateway koppelen aan uw virtuele netwerk van beheerde instantie

1. Open PowerShell op uw on-premises clientcomputer.

2. Kopieer dit PowerShell-script. Dit script koppelt een VPN-gateway aan het virtuele netwerk Managed Instance dat u hebt gemaakt in de quickstart [Van een beheerde instantie](sql-database-managed-instance-get-started.md) maken. Dit script maakt gebruik van de Azure PowerShell Az-module en doet het volgende voor windows- of Linux-gebaseerde hosts:

   - Certificaten maken en installeren op clientmachine
   - Berekent het toekomstige VPN Gateway-subnet-IP-bereik
   - Hiermee maakt u het GatewaySubnet
   - Implementeert de Azure Resource Manager-sjabloon die de VPN-gateway koppelt aan vpn-subnet

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

3. Plak het script in uw PowerShell-venster en geef de vereiste parameters op. De waarden `<subscriptionId>` `<resourceGroup>`voor `<virtualNetworkName>` , en moeten overeenkomen met de waarden die u hebt gebruikt voor de snelstart [van beheerde instantie](sql-database-managed-instance-get-started.md) maken. De waarde `<certificateNamePrefix>` voor kan een string van uw keuze.

4. Voer het PowerShell-script uit.

> [!IMPORTANT]
> Ga niet verder totdat het PowerShell-script is voltooid.

## <a name="create-a-vpn-connection-to-your-managed-instance"></a>Een VPN-verbinding maken met uw beheerde instantie

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Open de brongroep waarin u de virtuele netwerkgateway hebt gemaakt en open vervolgens de virtuele netwerkgatewaybron.
3. Selecteer **Point-to-site-configuratie** en selecteer vervolgens **VPN-client downloaden**.

    ![VPN-client downloaden](./media/sql-database-managed-instance-configure-p2s/download-vpn-client.png)  
4. Haal op uw on-premises clientcomputer de bestanden uit het zip-bestand en open de map met de uitgepakte bestanden.
5. Open de map **' WindowsAmd64** en open het **VpnClientSetupAmd64.exe-bestand.**
6. Als u een **windows-beveiligd uw pc-bericht** ontvangt, klikt u op **Meer informatie** en klikt u vervolgens op **Toch uitvoeren**.

    ![VPN-client installeren](./media/sql-database-managed-instance-configure-p2s/vpn-client-defender.png)\
7. Klik in het dialoogvenster Gebruikersaccountbeheer op **Ja** om door te gaan.
8. Selecteer **Ja** om de VPN-client voor uw virtuele netwerk te installeren in het dialoogvenster waarin naar uw virtuele netwerk wordt verwezen.

## <a name="connect-to-the-vpn-connection"></a>Verbinding maken met de VPN-verbinding

1. Ga naar **VPN** in **Network & Internet** op uw on-premises clientcomputer en selecteer uw managed instance virtueel netwerk om een verbinding met deze VNet tot stand te brengen. In de volgende afbeelding wordt de VNet **MyNewVNet**genoemd.

    ![VPN-verbinding](./media/sql-database-managed-instance-configure-p2s/vpn-connection.png)  
2. Selecteer **Verbinden**.
3. Selecteer **Verbinding maken**in het dialoogvenster .

    ![VPN-verbinding](./media/sql-database-managed-instance-configure-p2s/vpn-connection2.png)  
4. Wanneer u wordt gevraagd dat Verbindingsbeheer een verhoogde bevoegdheid nodig heeft om uw routetabel bij te **werken,** kiest u Doorgaan .
5. Selecteer **Ja** in het dialoogvenster Gebruikersaccountbeheer om door te gaan.

   U hebt een VPN-verbinding met uw Managed Instance VNet tot stand gebracht.

    ![VPN-verbinding](./media/sql-database-managed-instance-configure-p2s/vpn-connection-succeeded.png)  

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>SSMS gebruiken om verbinding te maken met het beheerde exemplaar

1. Open SQL Server Management Studio (SSMS) op de on-premises clientcomputer.
2. Voer in het dialoogvenster **Verbinding maken met server** de volledig gekwalificeerde **hostnaam** voor uw beheerde instantie in het vak **Servernaam** in.
3. Selecteer **SQL Server-verificatie,** geef uw gebruikersnaam en wachtwoord op en selecteer **Verbinding maken**.

    ![ssms verbinden](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

Nadat u verbinding hebt gemaakt, u uw systeem- en gebruikersdatabases bekijken in het knooppunt Databases. U ook verschillende objecten weergeven in de knooppunten Beveiliging, Serverobjecten, Replicatie, Beheer, SQL Server Agent en XEvent Profiler.

## <a name="next-steps"></a>Volgende stappen

- Zie [Een point-to-site-verbinding configureren](sql-database-managed-instance-configure-p2s.md)voor een snelle start met het weermaken van verbinding vanaf een virtuele Azure-machine.
- Zie [Verbinding maken tussen uw toepassing en het beheerde exemplaar van Azure SQL Database](sql-database-managed-instance-connect-app.md) voor een overzicht van de verbindingsopties voor toepassingen.
- Als u een bestaande SQL Server-database wilt herstellen van on-premises naar een beheerde instantie, u de [Azure Database Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md) gebruiken voor migratie of de opdracht [T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) om te herstellen uit een databaseback-upbestand.
