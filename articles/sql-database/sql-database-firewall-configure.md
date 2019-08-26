---
title: IP-firewall regels voor Azure SQL Database en Data Warehouse | Microsoft Docs
description: Informatie over het configureren van een SQL database of SQL Data Warehouse Firewall met IP-firewall regels op server niveau voor het beheren van toegang en het configureren van een enkele of gegroepeerde Data Base met IP-firewall regels op database niveau.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 39d2dae28bde8ff35408733a1af886c302ec79bf
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568186"
---
# <a name="azure-sql-database-and-sql-data-warehouse-ip-firewall-rules"></a>Azure SQL Database en SQL Data Warehouse IP-firewall regels

Microsoft Azure [SQL database](sql-database-technical-overview.md) en [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) bieden een relationele database service voor Azure en andere toepassingen op internet. Om uw gegevens te beschermen, verhinderen firewalls alle toegang tot de databaseserver totdat u opgeeft welke computers zijn gemachtigd. De firewall verleent toegang tot databases op basis van het IP-adres waar de aanvraag vandaan komt.

> [!NOTE]
> Dit artikel is van toepassing op Azure SQL Server en op zowel SQL Database-als SQL Data Warehouse-data bases die zijn gemaakt op de Azure SQL-Server. Voor het gemak wordt de term 'SQL Database' gebruikt wanneer er wordt verwezen naar zowel SQL Database als SQL Data Warehouse.
> [!IMPORTANT]
> Dit artikel is *niet* van toepassing op **Azure SQL database Managed instance**. Raadpleeg het volgende artikel over het [maken van een verbinding met een beheerd exemplaar](sql-database-managed-instance-connect-app.md) voor meer informatie over de benodigde netwerk configuratie.

## <a name="virtual-network-rules-as-alternatives-to-ip-rules"></a>Virtuele netwerk regels als alternatieven voor IP-regels

Naast de IP-regels beheert de firewall ook regels voor het *virtuele netwerk*. De regels voor virtuele netwerken zijn gebaseerd op Virtual Network Service-eind punten. De regels voor virtuele netwerken kunnen in sommige gevallen de voor keur geven aan IP-regels. Zie [Virtual Network Service-eind punten en-regels voor Azure SQL database voor](sql-database-vnet-service-endpoint-rule-overview.md)meer informatie.

## <a name="overview"></a>Overzicht

In eerste instantie wordt alle toegang tot uw Azure SQL-Server geblokkeerd door de firewall van SQL Database. Als u toegang wilt krijgen tot een database server, moet u een of meer IP-firewall regels op server niveau opgeven waarmee toegang tot uw Azure SQL-Server mogelijk wordt. Gebruik de IP-firewall regels om op te geven welke IP-adresbereiken van Internet zijn toegestaan en of Azure-toepassingen kunnen proberen verbinding te maken met uw Azure SQL-Server.

Als u selectief toegang wilt verlenen tot slechts een van de databases op uw Azure SQL-server, moet u voor de vereiste database een regel op databaseniveau maken. Geef een IP-adres bereik voor de data base-IP-firewall regel op die zich buiten het IP-adres bereik bevindt dat is opgegeven in de IP-firewall regel op server niveau, en zorg ervoor dat het IP-adres van de client binnen het bereik ligt dat is opgegeven in de regel op database niveau.

> [!IMPORTANT]
> SQL Data Warehouse ondersteunt alleen IP-firewall regels op server niveau en biedt geen ondersteuning voor IP-firewall regels op database niveau.

Verbindingspogingen van internet en Azure moeten eerst de firewall passeren voordat ze de Azure SQL-server of SQL Database kunnen bereiken, zoals in het volgende diagram wordt weergegeven:

   ![Diagram met beschrijving van firewallconfiguratie.][1]

- **IP-firewall regels op server niveau:**

  Met deze regels kunnen clients toegang krijgen tot uw volledige Azure SQL-Server, dat wil zeggen, alle data bases op dezelfde SQL Database Server. Deze regels worden opgeslagen in de **hoofd**database. IP-firewall regels op server niveau kunnen worden geconfigureerd met behulp van de portal of met behulp van Transact-SQL-instructies. Als u IP-firewall regels op server niveau wilt maken met behulp van de Azure Portal of Power shell, moet u de eigenaar van het abonnement of een mede werker van het abonnement zijn. Als u een IP-firewall regel op server niveau wilt maken met behulp van Transact-SQL, moet u verbinding maken met het SQL Database-exemplaar als de principal-aanmelding op server niveau of de beheerder van Azure Active Directory (dit betekent dat er eerst een IP-firewall regel op server niveau moet worden gemaakt door een gebruiker met machtigingen op Azure-niveau).

- **IP-firewall regels op database niveau:**

  Met deze regels kunnen clients toegang krijgen tot bepaalde (beveiligde) data bases op dezelfde SQL Database Server. U kunt deze regels maken voor elke Data Base (met inbegrip van de **hoofd** database) en ze worden opgeslagen in de afzonderlijke data bases. IP-firewall regels op database niveau voor hoofd-en gebruikers databases kunnen alleen worden gemaakt en beheerd met behulp van Transact-SQL-instructies en pas nadat u de eerste firewall op server niveau hebt geconfigureerd. Als u een IP-adres bereik opgeeft in de IP-firewall regel op database niveau buiten het bereik dat is opgegeven in de IP-firewall regel op server niveau, hebben alleen clients met IP-adressen in het bereik op database niveau toegang tot de data base. U kunt Maxi maal 128 IP-firewall regels op database niveau voor een Data Base hebben. Zie het voor beeld verderop in dit artikel voor meer informatie over het configureren van IP-firewall regels op database niveau en Zie [sp_set_database_firewall_rule (Azure SQL database)](https://msdn.microsoft.com/library/dn270010.aspx).

### <a name="recommendation"></a>Aanbeveling

Micro soft raadt u aan om de IP-firewall regels op database niveau wanneer mogelijk te gebruiken om de beveiliging te verbeteren en om uw data base draagbaarer te maken. Gebruik IP-firewall regels op server niveau voor beheerders en wanneer u veel data bases met dezelfde toegangs vereisten hebt en u niet de tijd wilt best Eden aan het configureren van elke Data Base afzonderlijk.

> [!IMPORTANT]
> Windows Azure SQL Database ondersteunt Maxi maal 128 IP-firewall regels.
> [!NOTE]
> Voor meer informatie over draagbare databases in de context van bedrijfscontinuïteit raadpleegt u [Authentication requirements for disaster recovery](sql-database-geo-replication-security-config.md) (Verificatievereisten voor herstel na noodgevallen).

### <a name="connecting-from-the-internet"></a>Verbinding maken vanuit internet

Wanneer een computer via Internet verbinding probeert te maken met uw database server, controleert de firewall eerst het oorspronkelijke IP-adres van de aanvraag op basis van de IP-firewall regels op database niveau voor de data base die door de verbinding wordt aangevraagd:

- Als het IP-adres van de aanvraag binnen een van de bereiken ligt die zijn opgegeven in de IP-firewall regels op database niveau, wordt de verbinding verleend aan de SQL Database die de regel bevat.
- Als het IP-adres van de aanvraag niet binnen een van de bereiken ligt die zijn opgegeven in de IP-firewall regel op database niveau, worden de IP-firewall regels op server niveau gecontroleerd. Als het IP-adres van de aanvraag binnen een van de bereiken ligt die zijn opgegeven in de IP-firewall regels op server niveau, wordt de verbinding verleend. IP-firewall regels op server niveau zijn van toepassing op alle SQL-data bases op de Azure SQL-Server.  
- Als het IP-adres van de aanvraag niet binnen de bereiken valt die zijn opgegeven in een van de IP-firewall regels op database niveau of op server niveau, mislukt de verbindings aanvraag.

> [!NOTE]
> Voor toegang tot Azure SQL Database vanuit de lokale computer moet u ervoor zorgen dat de firewall op uw netwerk en lokale computer uitgaande communicatie op TCP-poort 1433 toestaat.

### <a name="connecting-from-azure"></a>Verbinding maken vanuit Azure

Azure-verbindingen moeten zijn ingeschakeld opdat toepassingen vanuit Azure verbinding kunnen maken met uw Azure SQL-server. Wanneer een toepassing vanuit Azure probeert verbinding te maken met uw databaseserver, verifieert de firewall of Azure-verbindingen zijn toegestaan. Een firewall instelling met het begin-en eind adres gelijk aan 0.0.0.0 geeft aan dat Azure-verbindingen zijn toegestaan. Als de verbindingspoging niet is toegestaan, zal de aanvraag de Azure SQL Database-server niet bereiken.

> [!IMPORTANT]
> Met deze optie configureert u de firewall zo dat alle verbindingen vanuit Azure zijn toegestaan, inclusief verbindingen vanuit de abonnementen van andere klanten. Wanneer u deze optie selecteert, zorg dan dat uw aanmeldings- en gebruikersmachtigingen de toegang beperken tot alleen geautoriseerde gebruikers.

## <a name="creating-and-managing-ip-firewall-rules"></a>IP-firewall regels maken en beheren

De eerste firewall instelling op server niveau kan worden gemaakt met behulp van de [Azure Portal](https://portal.azure.com/) of programmatisch met behulp van [Azure POWERSHELL](https://docs.microsoft.com/powershell/module/az.sql), [Azure cli](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)of de [rest API](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate). Volgende IP-firewall regels op server niveau kunnen worden gemaakt en beheerd met behulp van deze methoden en via Transact-SQL.

> [!IMPORTANT]
> IP-firewall regels op database niveau kunnen alleen worden gemaakt en beheerd met behulp van Transact-SQL.

Om de prestaties te verbeteren, worden IP-firewall regels op server niveau tijdelijk opgeslagen in de cache op database niveau. Zie [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx) als u de cache wilt vernieuwen.

> [!TIP]
> U kunt [SQL database controle](sql-database-auditing.md) gebruiken om wijzigingen in de firewall op server niveau en op database niveau te controleren.

## <a name="manage-server-level-ip-firewall-rules-using-the-azure-portal"></a>IP-firewall regels op server niveau beheren met behulp van de Azure Portal

Als u een IP-firewall regel op server niveau wilt instellen in de Azure Portal, gaat u naar de pagina overzicht voor uw Azure SQL database of de overzichts pagina voor uw SQL Database-Server.

> [!TIP]
> Zie [een Data Base maken met behulp van de Azure Portal](sql-database-single-database-get-started.md)voor een zelf studie.

### <a name="from-database-overview-page"></a>Van data base-overzichts pagina

1. Als u een IP-firewall regel op server niveau wilt instellen op de overzichts pagina van de data base, klikt u op de werk balk op **Server firewall instellen** , zoals wordt weer gegeven in de volgende afbeelding: De pagina **Firewallinstellingen** voor de SQL Database-server wordt geopend.

      ![Server-IP-firewall regel](./media/sql-database-get-started-portal/server-firewall-rule.png)

2. Klik op **IP van client toevoegen** op de werk balk om het IP-adres toe te voegen van de computer die u momenteel gebruikt en klik vervolgens op **Opslaan**. Er wordt een IP-firewall regel op server niveau gemaakt voor uw huidige IP-adres.

      ![IP-firewall regel op server niveau instellen](./media/sql-database-get-started-portal/server-firewall-rule-set.png)

### <a name="from-server-overview-page"></a>Van Server overzicht pagina

De overzichts pagina voor uw server wordt geopend, met de volledig gekwalificeerde server naam (zoals **mynewserver20170403.database.Windows.net**) en biedt opties voor verdere configuratie.

1. Als u een regel op server niveau wilt instellen op de pagina overzicht van server, klikt u in het menu links onder instellingen op **firewall** .

2. Klik op **IP van client toevoegen** op de werk balk om het IP-adres toe te voegen van de computer die u momenteel gebruikt en klik vervolgens op **Opslaan**. Er wordt een IP-firewall regel op server niveau gemaakt voor uw huidige IP-adres.

## <a name="manage-ip-firewall-rules-using-transact-sql"></a>IP-firewall regels beheren met behulp van Transact-SQL

| Catalogusweergave of opgeslagen procedure | Niveau | Description |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Server |Hiermee worden de huidige IP-firewall regels op server niveau weer gegeven |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Server |Hiermee worden IP-firewall regels op server niveau gemaakt of bijgewerkt |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Server |Hiermee worden IP-firewall regels op server niveau verwijderd |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Database |Hiermee worden de huidige IP-firewall regels op database niveau weer gegeven |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Database |Hiermee worden de IP-firewall regels op database niveau gemaakt of bijgewerkt |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Databases |Hiermee verwijdert u IP-firewall regels op database niveau |

In de volgende voor beelden worden de bestaande regels gecontroleerd, een bereik van IP-adressen op de server Contoso ingeschakeld en een IP-firewall regel verwijderd:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Voeg vervolgens een IP-firewall regel op server niveau toe.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Als u een IP-firewall regel op server niveau wilt verwijderen, voert u de opgeslagen procedure sp_delete_firewall_rule uit. In het volgende voor beeld wordt de regel met de naam ContosoFirewallRule verwijderd:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

## <a name="manage-server-level-ip-firewall-rules-using-azure-powershell"></a>IP-firewall regels op server niveau beheren met Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek.

| Cmdlet | Niveau | Description |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Server |Retourneert de huidige firewallregels op serverniveau |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Server |Maakt een nieuwe firewallregel op serverniveau |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Server |Werkt de eigenschappen van een bestaande firewallregel op serverniveau bij |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Server |Verwijdert firewallregels op serverniveau |

In het volgende voor beeld wordt een IP-firewall regel op server niveau ingesteld met behulp van Power shell:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> Voor Power shell-voor beelden in de context van een snel starten raadpleegt u [Create DB-Power shell](sql-database-powershell-samples.md) en [een afzonderlijke data base maken en een SQL database IP-firewall regel op server niveau configureren met behulp van Power shell](scripts/sql-database-create-and-configure-database-powershell.md)

## <a name="manage-server-level-ip-firewall-rules-using-azure-cli"></a>IP-firewall regels op server niveau beheren met Azure CLI

| Cmdlet | Niveau | Description |
| --- | --- | --- |
|[AZ SQL Server firewall-Rule Create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Server|Hiermee maakt u een server-IP-firewall regel|
|[AZ SQL Server firewall-Rule List](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Server|Een lijst met de IP-firewall regels op een server|
|[AZ SQL Server firewall-Rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Server|Toont de details van een IP-firewall regel|
|[AZ SQL Server firewall-Rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Server|Hiermee wordt een IP-firewall regel bijgewerkt|
|[AZ SQL Server firewall-Rule Delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Server|Hiermee verwijdert u een IP-firewall regel|

In het volgende voor beeld wordt een IP-firewall regel op server niveau ingesteld met behulp van Azure CLI:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Voor een voor beeld van een Azure CLI in de context van een Quick Start raadpleegt u [Create DB-Azure cli](sql-database-cli-samples.md) en [een enkele data base maken en een SQL database IP-firewall regel configureren met behulp van de Azure cli](scripts/sql-database-create-and-configure-database-cli.md)

## <a name="manage-server-level-ip-firewall-rules-using-rest-api"></a>IP-firewall regels op server niveau beheren met REST API

| API | Niveau | Description |
| --- | --- | --- |
| [List Firewall Rules](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) (Lijst met firewallregels) |Server |Hiermee worden de huidige IP-firewall regels op server niveau weer gegeven |
| [Firewallregels maken of bijwerken](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |Server |Hiermee worden IP-firewall regels op server niveau gemaakt of bijgewerkt |
| [Delete Firewall Rule](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) (Firewallregel verwijderen) |Server |Hiermee worden IP-firewall regels op server niveau verwijderd |
| [Firewall regels ophalen](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | Server | Hiermee worden IP-firewall regels op server niveau opgehaald |

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Server niveau versus IP-firewall regels op database niveau

V. Moeten gebruikers van een Data Base volledig worden geïsoleerd van een andere data base?
Zo ja, toegang verlenen met behulp van IP-firewall regels op database niveau. Dit voor komt het gebruik van IP-firewall regels op server niveau, waarmee toegang via de firewall wordt toegestaan voor alle data bases, waardoor de diepte van uw verdedigingen wordt verminderd.

V. Hebben gebruikers op het IP-adres toegang tot alle data bases nodig?
Gebruik IP-firewall regels op server niveau om het aantal keren dat u IP-firewall regels moet configureren te verminderen.

V. Heeft de persoon of het team dat de IP-firewall regels configureert alleen toegang via de Azure Portal, Power shell of de REST API?
U moet IP-firewall regels op server niveau gebruiken. IP-firewall regels op database niveau kunnen alleen worden geconfigureerd met behulp van Transact-SQL.  

V. Is de persoon of het team dat de IP-firewall regels voor het configureren van een hoog niveau op database niveau niet toegestaan?
Gebruik IP-firewall regels op server niveau. Het configureren van IP-firewall regels op database niveau met behulp van Transact `CONTROL DATABASE` -SQL, vereist ten minste machtigingen op database niveau.  

V. Worden de IP-firewall regels door de persoon of het team geconfigureerd of gecontroleerd, en worden de IP-firewall regels voor veel (mogelijk 100s) van data bases centraal beheerd?
Deze selectie is afhankelijk van uw behoeften en omgeving. IP-firewall regels op server niveau zijn mogelijk gemakkelijker te configureren, maar met scripts kunnen regels op database niveau worden geconfigureerd. En zelfs als u IP-firewall regels op server niveau gebruikt, moet u mogelijk de IP-firewall regels op database niveau controleren om te zien of gebruikers `CONTROL` met machtigingen voor de data base IP-firewall regels op database niveau hebben gemaakt.

V. Kan ik een combi natie van IP-firewall regels op server niveau en op database niveau gebruiken?
Ja. Sommige gebruikers, zoals beheerders, hebben mogelijk IP-firewall regels op server niveau nodig. Andere gebruikers, zoals gebruikers van een database toepassing, kunnen mogelijk IP-firewall regels op database niveau hebben.

## <a name="troubleshooting-the-database-firewall"></a>Problemen met de databasefirewall oplossen

Houd rekening met de volgende punten als toegang tot de service Microsoft Azure SQL Database niet werkt zoals verwacht:

- **Lokale firewall configuratie:**

  Voordat de computer toegang kan krijgen tot Azure SQL Database, moet u mogelijk een firewall-uitzonde ring op uw computer maken voor TCP-poort 1433. Als u verbindingen maakt binnen de grenzen van de Azure-cloud, moet u mogelijk aanvullende poorten openen. Zie voor meer informatie de **SQL database: Buiten het gedeelte van de [poorten boven 1433 voor ADO.net 4,5 en SQL database.](sql-database-develop-direct-route-ports-adonet-v12.md)**

- **Netwerkadresomzetting (NAT):**

  Als gevolg van NAT kan het IP-adres dat door uw computer wordt gebruikt om verbinding te maken met Azure SQL Database, afwijken van het IP-adres dat wordt weer gegeven in de IP-configuratie-instellingen van uw computer. Als u het IP-adres wilt zien dat door uw computer wordt gebruikt om verbinding te maken met Azure, meldt u zich aan bij de portal en gaat u naar het tabblad **Configureren** op de server die als host fungeert voor uw database. In de sectie **Toegestane IP-adressen** wordt het **Huidige IP-adres van client** weergegeven. Klik op **Toevoegen** aan de **Toegestane IP-adressen** als u wilt toestaan dat deze computer toegang heeft tot de server.

- **Wijzigingen in de acceptatie lijst zijn nog niet doorgevoerd:**

  Het kan een vertraging van vijf minuten duren voordat wijzigingen in de configuratie van de Azure SQL Database firewall van kracht worden.

- **De aanmelding is niet geautoriseerd of er is een onjuist wacht woord gebruikt:**

  Als een aanmelding geen machtigingen heeft op de Azure SQL Database Server of het gebruikte wacht woord onjuist is, wordt de verbinding met de Azure SQL Database-Server geweigerd. Door een firewallinstellingen te maken, krijgen clients alleen de mogelijkheid om te proberen verbinding te maken met de server. Elke client moet alsnog de benodigde beveiligingsreferenties opgeven. Zie [data bases, aanmeldingen en gebruikers in Azure SQL database beheren](sql-database-manage-logins.md)voor meer informatie over het voorbereiden van aanmeldingen.

- **Dynamisch IP-adres:**

  Als u een Internet verbinding hebt met dynamische IP-adres sering en u problemen ondervindt met het verkrijgen van de firewall, kunt u een van de volgende oplossingen proberen:
  
  - Vraag uw Internet provider (ISP) naar het IP-adres bereik dat is toegewezen aan uw client computers die toegang hebben tot de Azure SQL Database Server en voeg vervolgens het IP-adres bereik toe als een IP-firewall regel.
  - Neem in plaats daarvan statische IP-adressen op voor uw client computers en voeg de IP-adres toe als IP-firewall regels.

## <a name="next-steps"></a>Volgende stappen

- Bevestigen dat de bedrijfs netwerk omgeving binnenkomende communicatie toestaat van de compute IP-adresbereiken (inclusief SQL-bereiken) die worden gebruikt door de Microsoft Azure data centers. Het kan nodig zijn om deze IP-adressen te white list. Zie [Microsoft Azure Data Center IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653)  
- Zie [een Azure-SQL database maken](sql-database-single-database-get-started.md)voor een snelle start voor het maken van een IP-firewall regel op server niveau.
- Voor hulp bij het maken van een verbinding met een Azure SQL-database vanuit open-source toepassingen of toepassingen van derden raadpleegt u [Client quick-start code samples to SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx) (Snelstartcodevoorbeelden voor clients met SQL Database).
- Voor informatie over aanvullende poorten die u mogelijk moet openen, raadpleegt u **de SQL database: Buiten het gedeelte van de [poorten boven 1433 voor ADO.net 4,5 en SQL database](sql-database-develop-direct-route-ports-adonet-v12.md)**
- Zie [uw data base beveiligen](sql-database-security-overview.md) voor een overzicht van Azure SQL database beveiliging

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
