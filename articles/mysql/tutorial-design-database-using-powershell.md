---
title: 'Zelf studie: een server Azure PowerShell-Azure Database for MySQL ontwerpen'
description: In deze zelf studie wordt uitgelegd hoe u Azure Database for MySQL-server en-data base maakt en beheert met behulp van Power shell.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurepowershell
ms.topic: tutorial
ms.date: 04/29/2020
ms.custom: mvc
ms.openlocfilehash: 6bb3c25d4d4d24e626ad210c78c6ac64c560e43e
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614387"
---
# <a name="tutorial-design-an-azure-database-for-mysql-using-powershell"></a>Zelf studie: een Azure Database for MySQL ontwerpen met behulp van Power shell

Azure Database voor MySQL is een relationele databaseservice in de Microsoft Cloud die is gebaseerd op de MySQL Community Edition database engine. In deze zelf studie gebruikt u Power shell en andere hulpprogram ma's om te leren hoe u:

> [!div class="checklist"]
> - Een Azure Database voor MySQL maken
> - De serverfirewall configureren
> - Het [opdracht regel programma mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) gebruiken om een Data Base te maken
> - Voorbeeldgegevens laden
> - Querygegevens
> - Gegevens bijwerken
> - Gegevens terugzetten

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

In het volgende voor beeld wordt een MySQL-server in de regio **VS-West** met de naam **mydemoserver** in de resource groep **myresourcegroup** gemaakt met de aanmelding van de server beheerder van **myadmin**. Het is een generatie-5-server in de prijs categorie algemeen gebruik met twee vCores en geografisch redundante back-ups ingeschakeld. Documenteer het wacht woord dat in de eerste regel van het voor beeld wordt gebruikt, aangezien dit het wacht woord is voor het account van de MySQL-Server beheerder.

> [!TIP]
> Een servernaam wordt toegewezen aan een DNS-naam en moet wereldwijd uniek zijn in Azure.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

De **SKU** -parameter waarde volgt de **\_vCores van de\_prijs categorie-laag** , zoals wordt weer gegeven in de volgende voor beelden.

- `-Sku B_Gen5_1` komt overeen met Basic, Gen 5 en 1 vCore. Deze optie is de kleinst beschikbare SKU.
- `-Sku GP_Gen5_32` komt overeen met Algemeen gebruik, Gen 5 en 32 vCores.
- `-Sku MO_Gen5_2` komt overeen met Geoptimaliseerd voor geheugen, Gen 5 en 2 vCores.

Zie [Azure database for MySQL prijs categorieën](./concepts-pricing-tiers.md)voor informatie over geldige **SKU** -waarden per regio en voor lagen.

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

```azurepowershell-interactive
mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-database"></a>Een database maken

Zodra u met de server bent verbonden, maakt u een lege database.

```sql
mysql> CREATE DATABASE mysampledb;
```

Voer bij de prompt de volgende opdracht uit om verbinding te maken met de zojuist gemaakte database:

```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Tabellen maken in de database

U weet nu hoe u verbinding kunt maken met de Azure Database for MySQL-database en dus is het tijd om enkele eenvoudige taken uit te voeren.

We gaan eerst een tabel maken en hierin enkele gegevens laden. Laten we een tabel maken waarin voorraadgegevens worden opgeslagen.

```sql
CREATE TABLE inventory (
  id serial PRIMARY KEY,
  name VARCHAR(50),
  quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Gegevens laden in de tabellen

De volgende stap bestaat uit het toevoegen van gegevens aan de tabel. Voer bij de opdrachtprompt de volgende query uit om enkele rijen met gegevens in te voegen.

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150);
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

We hebben nu twee rijen met voorbeeldgegevens in de tabel die u eerder hebt gemaakt.

## <a name="query-and-update-the-data-in-the-tables"></a>De gegevens in de tabellen opvragen en bijwerken

Voer de volgende query uit om gegevens op te halen uit de databasetabel.

```sql
SELECT * FROM inventory;
```

U kunt ook de gegevens in de tabellen bijwerken.

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

De rij wordt dan meteen bijgewerkt wanneer u gegevens ophaalt.

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Een database herstellen naar een eerder tijdstip

U kunt de server naar een eerder tijdstip herstellen. De herstelde gegevens worden gekopieerd naar een nieuwe server en de bestaande server blijft ongewijzigd. Als een tabel bijvoorbeeld per ongeluk wordt verwijderd, kunt u de herstel bewerking op het tijdstip herstellen. Vervolgens kunt u de ontbrekende tabel en gegevens ophalen van de herstelde kopie van de server.

Als u de server wilt herstellen, `Restore-AzMySqlServer` gebruikt u de Power shell-cmdlet.

### <a name="run-the-restore-command"></a>Voer de opdracht herstellen uit

Voer het volgende voor beeld uit in Power shell om de server te herstellen.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMySqlServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

Wanneer u een server naar een eerder tijdstip herstelt, wordt er een nieuwe server gemaakt. De oorspronkelijke server en de bijbehorende data bases van het opgegeven tijdstip worden naar de nieuwe server gekopieerd.

De waarden voor de locatie en de prijs categorie voor de herstelde server blijven hetzelfde als de oorspronkelijke server.

Nadat het herstel proces is voltooid, zoekt u de nieuwe server en controleert u of de gegevens correct zijn hersteld. De nieuwe server heeft dezelfde aanmeldings naam en hetzelfde wacht woord voor de server beheerder op het moment dat de herstel bewerking werd gestart. Het wacht woord kan worden gewijzigd op de pagina **overzicht** van de nieuwe server.

De nieuwe server die tijdens het herstellen is gemaakt, heeft geen VNet-service-eind punten die bestonden op de oorspronkelijke server. Deze regels moeten afzonderlijk worden ingesteld voor de nieuwe server. Firewall regels van de oorspronkelijke server worden hersteld.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een back-up maken van een Azure Database for MySQL-server en deze herstellen met Power shell](howto-restore-server-powershell.md)
