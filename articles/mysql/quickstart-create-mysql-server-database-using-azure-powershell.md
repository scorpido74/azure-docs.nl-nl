---
title: 'Snelstartgids: een Azure PowerShell-Azure Database for MySQL maken'
description: In deze Quick Start wordt beschreven hoe u Power shell gebruikt om een Azure Database for MySQL server te maken in een Azure-resource groep.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurepowershell
ms.topic: quickstart
ms.date: 04/28/2020
ms.custom: mvc
ms.openlocfilehash: 2e12da29a8388bf4a232930c3737be7ddce80d12
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611939"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-powershell"></a>Snelstartgids: een Azure Database for MySQL-server maken met behulp van Power shell

In deze Quick Start wordt beschreven hoe u Power shell gebruikt om een Azure Database for MySQL server te maken in een Azure-resource groep. U kunt Power shell gebruiken om Azure-resources interactief of in scripts te maken en te beheren.

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis](https://azure.microsoft.com/free/) account aan voordat u begint.

Als u Power shell lokaal wilt gebruiken, moet u voor dit artikel de AZ Power shell-module installeren en verbinding maken met uw Azure-account met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) . Zie [Install Azure PowerShell](/powershell/azure/install-az-ps)(Engelstalig) voor meer informatie over het installeren van de AZ Power shell-module.

> [!IMPORTANT]
> Hoewel de AZ. MySql Power shell-module in preview is, moet u deze afzonderlijk van de AZ Power shell-module installeren met `Install-Module -Name Az.MySql -AllowPrerelease`behulp van de volgende opdracht:.
> Zodra de AZ. MySql Power shell-module algemeen beschikbaar is, wordt het onderdeel van toekomstige AZ Power shell-module releases en is deze systeem eigen beschikbaar vanuit Azure Cloud Shell.

Als dit de eerste keer is dat u de Azure Database for MySQL-service gebruikt, moet u de resource provider **micro soft. DBforMySQL** registreren.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforMySQL
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Als u meerdere Azure-abonnementen hebt, kiest u het juiste abonnement waarin de resources moeten worden gefactureerd. Selecteer een specifieke abonnements-ID met behulp van de cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een [Azure-resource groep](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) met behulp van de cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en groepsgewijs worden beheerd.

In het volgende voor beeld wordt een resource groep met de naam **myresourcegroup** gemaakt in de regio **VS-West** .

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Een Azure-database voor MySQL-server maken

Maak een Azure Database for MySQL-server met `New-AzMySqlServer` de cmdlet. Een server kan meerdere databases beheren. Een aparte database wordt doorgaans gebruikt voor elk project of voor elke gebruiker.

De volgende tabel bevat een lijst met veelgebruikte para meters en voorbeeld waarden `New-AzMySqlServer` voor de cmdlet.

|        **Instelling**         | **Voorbeeldwaarde** |                                                                                                                                                             **Beschrijving**                                                                                                                                                              |
| -------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Naam                       | mydemoserver     | Kies een wereld wijd unieke naam in azure die uw Azure Database for MySQL server aanduidt. De server naam mag alleen letters, cijfers en het koppel teken (-) bevatten. Alle hoofd letters die zijn opgegeven, worden automatisch omgezet in kleine letters tijdens het maken. en moet 3 tot 63 tekens lang zijn. |
| ResourceGroupName          | myResourceGroup  | Geef de naam op van de Azure-resourcegroep.                                                                                                                                                                                                                                                                                            |
| Sku                        | GP_Gen5_2        | De naam van de SKU. Volgt de vCores van de **prijs\_categorie-tier\_Compute-generatie** in steno. Zie de informatie in de volgende tabel voor meer informatie over de SKU-para meter.                                                                                                                                           |
| BackupRetentionDay         | 7                | Hoe lang een back-up moet worden bewaard. De eenheid is dagen. Het bereik is 7-35.                                                                                                                                                                                                                                                                       |
| GeoRedundantBackup         | Ingeschakeld          | Of geografisch redundante back-ups moeten worden ingeschakeld voor deze server. Deze waarde kan niet worden ingeschakeld voor servers in de prijs categorie Basic en kan niet worden gewijzigd nadat de server is gemaakt. Toegestane waarden: Ingeschakeld, Uitgeschakeld.                                                                                                      |
| Locatie                   | westus           | De Azure-regio voor de server.                                                                                                                                                                                                                                                                                                         |
| SslEnforcement             | Ingeschakeld          | Hiermee wordt aangegeven of SSL moet worden ingeschakeld of niet voor deze server. Toegestane waarden: Ingeschakeld, Uitgeschakeld.                                                                                                                                                                                                                                                 |
| StorageInMb                | 51.200            | De opslagcapaciteit van de server (eenheid is MB). Een geldige StorageInMb is mini maal 5120 MB groot en neemt toe met een verhoging van 1024 MB. Zie [Azure database for MySQL prijs categorieën](./concepts-pricing-tiers.md)voor meer informatie over limieten voor de opslag grootte.                                                                               |
| Versie                    | 5.7              | De hoofdversie van MySQL.                                                                                                                                                                                                                                                                                                                 |
| AdministratorUserName      | myadmin          | De gebruikersnaam voor aanmelding als beheerder. De aanmeldingsnaam van de beheerder mag niet **azure_superuser**, **admin**, **administrator**, **root**, **guest** of **public** zijn.                                                                                                                                                                                            |
| AdministratorLoginPassword | `<securestring>` | Het wacht woord van de beheerder gebruiker in de vorm van een veilige teken reeks. Dit wachtwoord moet tussen 8 en 128 tekens bevatten. Uw wachtwoord moet tekens bevatten uit drie van de volgende categorieën: Nederlandse hoofdletters, Nederlandse kleine letters, cijfers (0-9) en niet-alfanumerieke tekens.                                       |

De **SKU** -parameter waarde volgt de **\_vCores van de\_prijs categorie-laag** , zoals wordt weer gegeven in de volgende voor beelden.

- `-Sku B_Gen5_1` komt overeen met Basic, Gen 5 en 1 vCore. Deze optie is de kleinst beschikbare SKU.
- `-Sku GP_Gen5_32` komt overeen met Algemeen gebruik, Gen 5 en 32 vCores.
- `-Sku MO_Gen5_2` komt overeen met Geoptimaliseerd voor geheugen, Gen 5 en 2 vCores.

Zie [Azure database for MySQL prijs categorieën](./concepts-pricing-tiers.md)voor informatie over geldige **SKU** -waarden per regio en voor lagen.

In het volgende voor beeld wordt een MySQL-server in de regio **VS-West** met de naam **mydemoserver** in de resource groep **myresourcegroup** gemaakt met de aanmelding van de server beheerder van **myadmin**. Het is een generatie-5-server in de prijs categorie algemeen gebruik met twee vCores en geografisch redundante back-ups ingeschakeld. Documenteer het wacht woord dat in de eerste regel van het voor beeld wordt gebruikt, aangezien dit het wacht woord is voor het account van de MySQL-Server beheerder.

> [!TIP]
> Een servernaam wordt toegewezen aan een DNS-naam en moet wereldwijd uniek zijn in Azure.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

U kunt de prijs categorie Basic gebruiken als Light Compute en I/O voldoende zijn voor uw werk belasting.

> [!IMPORTANT]
> Servers die zijn gemaakt in de prijs categorie Basic kunnen niet later worden geschaald naar algemeen gebruik of geoptimaliseerd voor geheugen en kunnen niet geografisch worden gerepliceerd.

## <a name="configure-a-firewall-rule"></a>Een firewallregel configureren

Een Azure Database for MySQL firewall regel op server niveau maken met behulp van de `New-AzMySqlFirewallRule` -cmdlet. Met een firewall regel op server niveau kan een externe toepassing, zoals het `mysql` opdracht regel programma of MySQL Workbench, verbinding maken met uw server via de firewall van de Azure database for MySQL-service.

In het volgende voor beeld wordt een firewall regel gemaakt met de naam **AllowMyIP** , die verbindingen van een specifiek IP-adres, 192.168.0.1, toestaat. Vervang een IP-adres of bereik van IP-adressen die overeenkomen met de locatie van waaruit u verbinding maakt.

```azurepowershell-interactive
New-AzMySqlFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> Verbindingen met Azure Database voor MySQL communiceren via poort 3306. Als u verbinding probeert te maken vanuit een bedrijfsnetwerk, wordt uitgaand verkeer via poort 3306 mogelijk niet toegestaan. In dit scenario kunt u alleen verbinding maken met de server als uw IT-afdeling poort 3306 opent.

## <a name="configure-ssl-settings"></a>SSL-instellingen configureren

Standaard worden SSL-verbindingen tussen uw server en clienttoepassingen afgedwongen. Deze standaard zorgt voor de beveiliging van _in-Motion_ gegevens door de gegevens stroom via internet te versleutelen. Voor deze snelstart schakelen we SSL-verbindingen uit voor uw server. Zie [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (SSL-connectiviteit in uw toepassing configureren om veilig verbinding te maken met Azure-database voor MySQL) voor meer informatie.

> [!WARNING]
> Dit wordt afgeraden voor productieservers.

In het volgende voor beeld wordt SSL op uw Azure Database for MySQL server uitgeschakeld.

```azurepowershell-interactive
Update-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -SslEnforcement Disabled
```

## <a name="get-the-connection-information"></a>De verbindingsgegevens ophalen

Als u verbinding met uw server wilt maken, moet u hostgegevens en toegangsreferenties opgeven. Gebruik het volgende voor beeld om de verbindings gegevens te bepalen. Noteer de waarden voor **FullyQualifiedDomainName** en **Administrator Login**.

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.mysql.database.azure.com       myadmin
```

## <a name="connect-to-the-server-using-the-mysql-command-line-tool"></a>Verbinding maken met de server met behulp van het opdracht regel programma mysql

Maak verbinding met uw server met `mysql` behulp van het opdracht regel programma. Zie [mysql-community-down loads](https://dev.mysql.com/downloads/shell/)voor het downloaden en installeren van het opdracht regel programma. U kunt ook een vooraf geïnstalleerde versie van het `mysql` opdracht regel programma in azure Cloud shell openen door de knop **try it** te selecteren in een code voorbeeld in dit artikel. Andere manieren om toegang te krijgen tot Azure Cloud Shell zijn om de knop **>_** te selecteren op de werk balk rechtsboven in de Azure portal of door naar [shell.Azure.com](https://shell.azure.com/)te gaan.

1. Maak verbinding met de server met `mysql` behulp van het opdracht regel programma.

   ```azurepowershell-interactive
   mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
   ```

1. Server status weer geven.

   ```sql
   mysql> status
   ```

    ```Output
    C:\Users\>mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
    Enter password: *************
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65512
    Server version: 5.6.42.0 MySQL Community Server (GPL)

    Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    mysql> status
    --------------
    mysql  Ver 14.14 Distrib 5.7.29, for Win64 (x86_64)

    Connection id:          65512
    Current database:
    Current user:           myadmin@myipaddress
    SSL:                    Not in use
    Using delimiter:        ;
    Server version:         5.6.42.0 MySQL Community Server (GPL)
    Protocol version:       10
    Connection:             mydemoserver.mysql.database.azure.com via TCP/IP
    Server characterset:    latin1
    Db     characterset:    latin1
    Client characterset:    utf8
    Conn.  characterset:    utf8
    TCP port:               3306
    Uptime:                 1 hour 2 min 12 sec

    Threads: 7  Questions: 952  Slow queries: 0  Opens: 66  Flush tables: 3  Open tables: 16  Queries per second avg: 0.255
    --------------

    mysql>
    ```

Zie [MySQL 5.7 Reference Manual - Chapter 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) (MySQL 5.7-handleiding, hoofdstuk 4.5.1) voor aanvullende opdrachten.

## <a name="connect-to-the-server-using-mysql-workbench"></a>Verbinding maken met de server via MySQL Workbench

1. Start de toepassing MySQL Workbench op uw clientcomputer. Zie [MySQL Workbench downloaden](https://dev.mysql.com/downloads/workbench/)om MySQL Workbench te downloaden en te installeren.

1. Voer in het dialoogvenster **Nieuwe verbinding instellen** de volgende gegevens in op het tabblad **Parameters**:

   ![nieuwe verbinding instellen](./media/quickstart-create-mysql-server-database-using-azure-powershell/setup-new-connection.png)

    |    **Instelling**    |           **Voorgestelde waarde**           |                      **Beschrijving**                       |
    | ----------------- | --------------------------------------- | ---------------------------------------------------------- |
    | Verbindingsnaam   | Mijn verbinding                           | Geef een label op voor deze verbinding                        |
    | Verbindingsmethode | Standard (TCP/IP)                       | TCP/IP-protocol gebruiken om verbinding te maken met Azure Database for MySQL |
    | Hostnaam          | `mydemoserver.mysql.database.azure.com` | Server naam die u eerder hebt genoteerd                           |
    | Poort              | 3306                                    | De standaard poort voor MySQL                                 |
    | Gebruikersnaam          | myadmin@mydemoserver                    | De aanmeldings gegevens van de server beheerder die u eerder hebt genoteerd                |
    | Wachtwoord          | *************                           | Gebruik het wacht woord voor het beheerders account dat u eerder hebt geconfigureerd      |

1. Als u wilt testen of de para meters juist zijn geconfigureerd, klikt u op de knop **verbinding testen** .

1. Selecteer de verbinding om verbinding te maken met de server.

## <a name="clean-up-resources"></a>Resources opschonen

Als de resources die u in deze Quick Start hebt gemaakt, niet nodig zijn voor een andere Snelstartgids of zelf studie, kunt u ze verwijderen door het volgende voor beeld uit te voeren.

> [!CAUTION]
> In het volgende voor beeld worden de opgegeven resource groep en alle resources erin verwijderd.
> Als resources buiten het bereik van deze Quick Start in de opgegeven resource groep bestaan, worden ze ook verwijderd.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Als u alleen de server wilt verwijderen die u in deze Quick Start hebt gemaakt zonder de `Remove-AzMySqlServer` resource groep te verwijderen, gebruikt u de cmdlet.

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een Azure Database for MySQL ontwerpen met behulp van Power shell](tutorial-design-database-using-powershell.md)
