---
title: VNet-eindpunten en regels voor afzonderlijke en samengevoegde databases
description: Markeer een subnet als eindpunt van de service voor virtuele netwerken. Vervolgens wordt het eindpunt als een virtuele netwerkregel voor de ACL uw Azure SQL Database. U SQL Database accepteert vervolgens communicatie van alle virtuele machines en andere knooppunten op het subnet.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 11/14/2019
ms.openlocfilehash: 4faead13c10171c31e76fe2dd59be32a93a12f86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124753"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-database-servers"></a>Eindpunten en regels voor virtuele netwerkservice gebruiken voor databaseservers

*Virtuele netwerkregels* zijn een beveiligingsfunctie voor firewalls die bepaalt of de databaseserver voor uw enkele databases en elastische groep in Azure [SQL Database](sql-database-technical-overview.md) of voor uw databases in SQL [Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) communicatie accepteert die vanuit bepaalde subnetten in virtuele netwerken worden verzonden. In dit artikel wordt uitgelegd waarom de functie voor virtuele netwerkregel soms de beste optie is om veilig communicatie toe te staan naar uw Azure SQL Database en SQL Data Warehouse.

> [!IMPORTANT]
> Dit artikel is van toepassing op Azure SQL-server en op zowel SQL Database- als SQL Data Warehouse-databases die zijn gemaakt op de Azure SQL-server. Voor het gemak wordt de term 'SQL Database' gebruikt wanneer er wordt verwezen naar zowel SQL Database als SQL Data Warehouse. Dit artikel is *niet* van toepassing op een **beheerde instantieimplementatie** in Azure SQL Database omdat er geen serviceeindpunt aan is gekoppeld.

Als u een virtuele netwerkregel wilt maken, moet er eerst een eindpunt voor [de virtuele netwerkservice][vm-virtual-network-service-endpoints-overview-649d] zijn waarnaar de regel moet worden verwezen.

## <a name="how-to-create-a-virtual-network-rule"></a>Een virtuele netwerkregel maken

Als u alleen een virtuele netwerkregel maakt, u verder gaan naar de stappen en uitleg [later in dit artikel.](#anchor-how-to-by-using-firewall-portal-59j)

<!--<a name="anch-details-about-vnet-rules-38q"/> -->

## <a name="details-about-virtual-network-rules"></a>Details over virtuele netwerkregels

In deze sectie worden verschillende details over virtuele netwerkregels beschreven.

### <a name="only-one-geographic-region"></a>Slechts één geografische regio

Elk eindpunt van de service voor virtuele netwerken is van toepassing op slechts één Azure-regio. Het eindpunt stelt andere regio's niet in staat om communicatie van het subnet te accepteren.

Elke virtuele netwerkregel is beperkt tot de regio waarop het onderliggende eindpunt van toepassing is.

### <a name="server-level-not-database-level"></a>Serverniveau, niet databaseniveau

Elke virtuele netwerkregel is van toepassing op uw hele Azure SQL Database-server, niet alleen op één bepaalde database op de server. Met andere woorden, virtuele netwerkregel is van toepassing op serverniveau, niet op databaseniveau.

- Ip-regels kunnen daarentegen op beide niveaus van toepassing zijn.

### <a name="security-administration-roles"></a>Rollen beveiligingsbeheer

Er is een scheiding van beveiligingsrollen in het beheer van eindpunten van de Virtual Network-service. Actie is vereist vanuit elk van de volgende rollen:

- **Netwerkbeheerder:** &nbsp; schakel het eindpunt in.
- **Databasebeheerder:** &nbsp; werk de lijst met toegangscontrole (ACL) bij om het opgegeven subnet toe te voegen aan de SQL Database-server.

*RBAC-alternatief:*

De rollen van netwerkbeheerder en databasebeheerder hebben meer mogelijkheden dan nodig zijn om virtuele netwerkregels te beheren. Slechts een subset van hun mogelijkheden is nodig.

U hebt de mogelijkheid om [RBAC (Role-based access control)][rbac-what-is-813s] in Azure te gebruiken om één aangepaste rol te maken die alleen de benodigde subset van mogelijkheden heeft. De aangepaste rol kan worden gebruikt in plaats van de netwerkbeheerder of de databasebeheerder. Het oppervlak van uw beveiligingsbelichting is lager als u een gebruiker toevoegt aan een aangepaste rol, versus de gebruiker toe te voegen aan de andere twee belangrijke beheerdersrollen.

> [!NOTE]
> In sommige gevallen bevinden de Azure SQL Database en het VNet-subnet zich in verschillende abonnementen. In deze gevallen moet u zorgen voor de volgende configuraties:
> - Beide abonnementen moeten zich in dezelfde Azure Active Directory-tenant bevinden.
> - De gebruiker heeft de vereiste machtigingen om bewerkingen te starten, zoals het inschakelen van serviceeindpunten en het toevoegen van een VNet-subnet aan de opgegeven server.
> - Beide abonnementen moeten de Microsoft.Sql-provider hebben geregistreerd.

## <a name="limitations"></a>Beperkingen

Voor Azure SQL Database heeft de functie virtuele netwerkregels de volgende beperkingen:

- In de firewall voor uw SQL Database verwijst elke virtuele netwerkregel naar een subnet. Al deze subnetten waarnaar wordt verwezen, moeten worden gehost in dezelfde geografische regio die de SQL-database host.

- Elke Azure SQL Database-server kan maximaal 128 ACL-vermeldingen hebben voor een bepaald virtueel netwerk.

- Virtuele netwerkregels zijn alleen van toepassing op virtuele azure-netwerken voor Azure Resource Manager; en niet aan [klassieke implementatiemodelnetwerken.][arm-deployment-model-568f]

- Als u eindpunten voor virtuele netwerkservices inschakelt in Azure SQL Database, worden ook de eindpunten voor de MySQL- en PostgreSQL Azure-services ingeschakeld. Met eindpunten AAN kunnen pogingen om verbinding te maken van de eindpunten naar uw MySQL- of PostgreSQL-exemplaren echter mislukken.
  - De onderliggende reden is dat MySQL en PostgreSQL waarschijnlijk geen virtuele netwerkregel hebben geconfigureerd. U moet een virtuele netwerkregel configureren voor Azure Database voor MySQL en PostgreSQL en de verbinding wordt geslaagd.

- Op de firewall zijn IP-adresbereiken wel van toepassing op de volgende netwerkitems, maar virtuele netwerkregels niet:
  - [Site-to-Site (S2S) virtual private network (VPN)][vpn-gateway-indexmd-608y]
  - On-premises via [ExpressRoute][expressroute-indexmd-744v]

### <a name="considerations-when-using-service-endpoints"></a>Overwegingen bij het gebruik van Service-eindpunten

Controleer bij het gebruik van serviceeindpunten voor Azure SQL Database de volgende overwegingen:

- **Uitgaande naar Azure SQL Database Public IP's is vereist:** Network Security Groups (NSGs) moeten worden geopend voor Azure SQL Database IP's om connectiviteit mogelijk te maken. U dit doen met [NSG-servicetags](../virtual-network/security-overview.md#service-tags) voor Azure SQL Database.

### <a name="expressroute"></a>ExpressRoute

Als u [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) gebruikt vanuit uw on-premises netwerk voor openbare peering of Microsoft-peering, moet u de NAT IP-adressen opgeven die worden gebruikt. Voor openbare peering gebruikt elk ExpressRoute-circuit standaard twee NAT IP-adressen. Deze worden toegepast op Azure-serviceverkeer wanneer het verkeer het Microsoft Azure-backbone-netwerk binnenkomt. Voor Microsoft-peering worden de NAT IP-adressen die worden gebruikt opgegeven door de klant of de serviceprovider. Voor toegang tot uw serviceresources moet u deze openbare IP-adressen toestaan in de instelling voor IP-firewall voor de resource. Wanneer u op zoek bent naar de IP-adressen van uw ExpressRoute-circuit voor openbare peering, opent u [een ondersteuningsticket met ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via de Azure-portal. Meer informatie over [NAT voor openbare peering en Microsoft-peering met ExpressRoute.](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)
  
Als u communicatie vanuit uw circuit naar Azure SQL Database wilt toestaan, moet u IP-netwerkregels maken voor de openbare IP-adressen van uw NAT.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Impact van het gebruik van VNet-serviceeindpunten met Azure-opslag

Azure Storage heeft dezelfde functie geïmplementeerd waarmee u de connectiviteit met uw Azure Storage-account beperken. Als u ervoor kiest deze functie te gebruiken met een Azure Storage-account dat wordt gebruikt door Azure SQL Server, u problemen ondervinden. Vervolgens volgt een lijst en discussie over Azure SQL Database- en Azure SQL Data Warehouse-functies die hierdoor worden beïnvloed.

### <a name="azure-sql-data-warehouse-polybase"></a>Azure SQL Data Warehouse PolyBase

PolyBase wordt vaak gebruikt om gegevens te laden in Azure SQL Data Warehouse vanuit Azure Storage-accounts. Als het Azure Storage-account waarvan u gegevens laadt, alleen toegang tot een set VNet-subnetten beperkt, wordt de verbinding tussen PolyBase en het account verbroken. Volg de onderstaande stappen voor het inschakelen van scenario's voor het importeren en exporteren van PolyBase met Azure SQL Data Warehouse dat verbinding maakt met Azure Storage dat is beveiligd met VNet:

#### <a name="prerequisites"></a>Vereisten

- Installeer Azure PowerShell met behulp van deze [handleiding](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Als u een v1- of blobopslagaccount voor algemene doeleinden hebt, moet u eerst upgraden naar v2 voor algemene doeleinden met behulp van deze [handleiding.](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)
- U moet **vertrouwde Microsoft-services toegang** hebben tot dit opslagaccount dat is ingeschakeld onder het instellingenmenu firewalls voor Azure Storage-account **Firewalls en Virtuele netwerken.** Raadpleeg deze [gids](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) voor meer informatie.

> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. De AzureRM-module blijft bugfixes ontvangen tot ten minste december 2020.  De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek. Zie De nieuwe Azure [PowerShell Az-module introduceren](/powershell/azure/new-azureps-module-az)voor meer informatie over de compatibiliteit ervan.

#### <a name="steps"></a>Stappen

1. Registreer in PowerShell **uw Azure SQL Server** als host van uw Azure SQL Data Warehouse-exemplaar met Azure Active Directory (AAD):

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

1. Maak een **v2-opslagaccount voor algemene doeleinden** met deze [handleiding](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

   > [!NOTE]
   > - Als u een v1- of blobopslagaccount voor algemene doeleinden hebt, moet u **eerst upgraden naar v2** met behulp van deze [handleiding.](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)
   > - Raadpleeg deze [handleiding](https://docs.microsoft.com/azure/storage/data-lake-storage/known-issues)voor bekende problemen met Azure Data Lake Storage Gen2.
    
1. Navigeer onder uw opslagaccount naar **Toegangsbeheer (IAM)** en klik op **Roltoewijzing toevoegen**. Wijs **De RBAC-rol van Opslagblob-gegevensbijdrage** toe aan uw Azure SQL Server als host van uw Azure SQL Data Warehouse dat u hebt geregistreerd bij Azure Active Directory (AAD) als in stap#1.

   > [!NOTE]
   > Alleen leden met eigenaarbevoegdheid kunnen deze stap uitvoeren. Raadpleeg deze [handleiding](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)voor verschillende ingebouwde rollen voor Azure-resources.
  
1. **Polybase-connectiviteit met het Azure Storage-account:**

   1. Maak een **[databasemastersleutel](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql)** als u er nog niet eerder een hebt gemaakt:

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Databasescoped-referenties maken met **ID = 'Managed Service Identity':**

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       > - Het is niet nodig om SECRET op te geven met azure storage-toegangssleutel, omdat dit mechanisme [Managed Identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) onder de covers gebruikt.
       > - De naam van de identiteit moet **'Managed Service Identity'** zijn voor PolyBase-connectiviteit om te werken met Azure Storage-account dat is beveiligd met VNet.

   1. Maak externe gegevensbron met `abfss://` een schema voor het maken van verbinding met uw v2-opslagaccount voor algemene doeleinden met PolyBase:

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       > - Als u al externe tabellen hebt die zijn gekoppeld aan v1- of blobopslagaccount voor algemene doeleinden, moet u deze externe tabellen eerst laten vallen en vervolgens de bijbehorende externe gegevensbron neerzetten. Maak vervolgens externe `abfss://` gegevensbron met een schema dat verbinding maakt met een v2-opslagaccount voor algemene doeleinden zoals hierboven en maak alle externe tabellen opnieuw met behulp van deze nieuwe externe gegevensbron. U [de wizard Scripts genereren en publiceren](https://docs.microsoft.com/sql/ssms/scripting/generate-and-publish-scripts-wizard) gebruiken om voor alle externe tabellen voor eenvoudig create-scripts te genereren.
       > - Zie voor `abfss://` meer informatie over de regeling deze [gids](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction-abfs-uri).
       > - Voor meer informatie over CREATE EXTERNAL DATA SOURCE, raadpleeg deze [handleiding](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql).

   1. Query als normaal met behulp van [externe tabellen](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="azure-sql-database-blob-auditing"></a>Azure SQL Database Blob-controle

Blob-controle duwt controlelogboeken naar uw eigen opslagaccount. Als dit opslagaccount de functie VNet Service-eindpunten gebruikt, wordt de verbinding met Azure SQL Database met het opslagaccount verbroken.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Een VNet Firewall-regel toevoegen aan uw server zonder VNet-serviceeindpunten in te schakelen

Lang geleden, voordat deze functie werd verbeterd, moest u VNet-serviceeindpunten inschakelen voordat u een live VNet-regel in de firewall kon implementeren. De eindpunten hadden betrekking op een bepaald VNet-subnet met een Azure SQL Database. Maar vanaf januari 2018 u deze vereiste omzeilen door de **ignoremissingvnetserviceendpoint-vlag** in te stellen.

Alleen het instellen van een Firewall-regel helpt de server niet te beveiligen. U moet ook VNet-serviceeindpunten inschakelen om de beveiliging van kracht te laten worden. Wanneer u serviceeindpunten inschakelt, wordt uw VNet-subnet uitvalvrij totdat de overgang van Uitgeschakeld naar Aan is voltooid. Dit geldt vooral in de context van grote VNets. U de **ignoremissingvnetserviceendpoint-vlag** gebruiken om de downtime tijdens de overgang te verminderen of te elimineren.

U de **ignoremissingvnetserviceendpoint-vlag** instellen met PowerShell. Zie PowerShell voor meer informatie [om een eindpunt en regel voor de Virtual Network-service voor Azure SQL Database te maken.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]

## <a name="errors-40914-and-40615"></a>Fouten 40914 en 40615

Verbindingsfout 40914 heeft betrekking op *virtuele netwerkregels,* zoals opgegeven in het firewalldeelvenster in de Azure-portal. Fout 40615 is vergelijkbaar, behalve dat het betrekking heeft op *IP-adresregels* op de Firewall.

### <a name="error-40914"></a>Fout 40914

*Berichttekst:* Kan de server niet openen '*[servernaam]*' gevraagd door de login. Client heeft geen toegang tot de server.

*Foutbeschrijving:* De client bevindt zich in een subnet met eindpunten voor virtuele netwerkservers. De Azure SQL Database-server heeft echter geen regel voor het virtuele netwerk die het subnet het recht geeft om te communiceren met SQL Database.

*Foutoplossing:* Gebruik in het deelvenster Firewall van de Azure-portal het besturingselement voor virtuele netwerkregels om [een virtuele netwerkregel](#anchor-how-to-by-using-firewall-portal-59j) voor het subnet toe te voegen.

### <a name="error-40615"></a>Fout 40615

*Berichttekst:* Kan de{0}server niet openen ' ' gevraagd door de login. Client met IP-adres '{1}' is niet toegestaan om toegang te krijgen tot de server.

*Foutbeschrijving:* De client probeert verbinding te maken vanaf een IP-adres dat niet is geautoriseerd om verbinding te maken met de Azure SQL Database-server. Op de firewall van de server is geen regel voor IP-adressen ingesteld die een client toestemming geeft om vanaf het opgegeven IP-adres te communiceren met de SQL-database.

*Foutoplossing:* Voer het IP-adres van de client in als IP-regel. Doe dit met behulp van het deelvenster Firewall in de Azure Portal.

<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Portal kan een virtuele netwerkregel maken

In deze sectie ziet u hoe u de [Azure-portal][http-azure-portal-link-ref-477t] gebruiken om een *virtuele netwerkregel* te maken in uw Azure SQL Database. De regel vertelt uw SQL-database om communicatie te accepteren van een bepaald subnet dat is getagd als eindpunt van de *Virtual Network-service.*

> [!NOTE]
> Als u een serviceeindpunt wilt toevoegen aan de VNet-firewallregels van uw Azure SQL Database-server, moet u eerst controleren of serviceeindpunten zijn ingeschakeld voor het subnet.
>
> Als serviceeindpunten niet zijn ingeschakeld voor het subnet, vraagt de portal u om deze in te schakelen. Klik op de knop **Inschakelen** op hetzelfde blad waarop u de regel toevoegt.

## <a name="powershell-alternative"></a>PowerShell-alternatief

Een script kan ook virtuele netwerkregels maken met PowerShell-cmdlet **New-AzSqlServerVirtualNetworkRule** of [vnet-vnet van het AZ-netwerk.](/cli/azure/network/vnet#az-network-vnet-create) Zie PowerShell als u geïnteresseerd bent [om een eindpunt en regel voor de Virtual Network-service voor Azure SQL Database te maken.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]

## <a name="rest-api-alternative"></a>REST API-alternatief

Intern roepen de PowerShell-cmdlets voor SQL VNet-acties REST-API's aan. U de REST API's rechtstreeks bellen.

- [Virtuele netwerkregels: bewerkingen][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Vereisten

U moet al een subnet hebben dat is gelabeld met de specifieke *eindpuntnaam* van de Virtual Network-service die relevant is voor Azure SQL Database.

- De relevante typenaam van eindpunt is **Microsoft.Sql**.
- Zie [Uw subnet is een eindpunt][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]als uw subnet mogelijk niet is getagd met de typenaam.

<a name="a-portal-steps-for-vnet-rule-200" />

## <a name="azure-portal-steps"></a>Azure-portalstappen

1. Meld u aan bij [Azure Portal][http-azure-portal-link-ref-477t].

2. Zoek naar **SQL-servers**en selecteer deze en selecteer vervolgens uw server. Selecteer **onder Beveiliging** **Firewalls en virtuele netwerken**.

3. Stel het **besturingselement Toegang tot Azure-services toestaan** in op UIT.

    > [!IMPORTANT]
    > Als u de besturingselementset aan laat staan, accepteert uw Azure SQL Database-server communicatie van elk subnet binnen de Azure-grens, d.w.z. afkomstig van een van de IP-adressen die wordt herkend als adressen binnen bereiken die zijn gedefinieerd voor Azure-datacenters. Het verlaten van de besturingselement ingesteld op AAN kan worden overmatige toegang vanuit een oogpunt van beveiliging. De eindpuntfunctie van de Microsoft Azure Virtual Network-service kan, in coördinatie met de functie virtuele netwerkregel van SQL Database, samen uw beveiligingsoppervlak verkleinen.

4. Klik **op het besturingselement + Bestaande** besturingselement toevoegen in de sectie **Virtuele netwerken.**

    ![Klik op bestaand toevoegen (subneteindpunt, als SQL-regel).][image-portal-firewall-vnet-add-existing-10-png]

5. Vul in het nieuwe deelvenster **Maken/bijwerken** de besturingselementen in met de namen van uw Azure-bronnen.

    > [!TIP]
    > U moet het juiste **adresvoorvoegsel** voor uw subnet opnemen. U de waarde vinden in het portaal.
    > Navigeren door **alle bronnen** &gt; **Alle typen** &gt; **virtuele netwerken**. Het filter toont uw virtuele netwerken. Klik op uw virtuele netwerk en klik vervolgens op **Subnetten**. De kolom **ADRESBEREIK** heeft het adresvoorvoegsel dat u nodig hebt.

    ![Velden invullen voor nieuwe regel.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Klik op de knop **OK** onder in het deelvenster.

7. Zie de resulterende virtuele netwerkregel in het firewalldeelvenster.

    ![Zie de nieuwe regel in het firewalldeelvenster.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> De volgende statussen of staten zijn van toepassing op de regels:
> - **Klaar:** Geeft aan dat de bewerking die u hebt gestart, is geslaagd.
> - **Mislukt:** Geeft aan dat de bewerking die u hebt gestart, is mislukt.
> - **Verwijderd:** Is alleen van toepassing op de bewerking Verwijderen en geeft aan dat de regel is verwijderd en niet langer van toepassing is.
> - **InProgress:** Geeft aan dat de bewerking aan de gang is. De oude regel is van toepassing terwijl de bewerking zich in deze status bevindt.

<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Verwante artikelen:

- [Service-eindpunten voor een virtueel Azure-netwerk][vm-virtual-network-service-endpoints-overview-649d]
- [Azure SQL Database-firewallregels op serverniveau en op databaseniveau][sql-db-firewall-rules-config-715d]

De functie virtuele netwerkregel voor Azure SQL Database is eind september 2017 beschikbaar.

## <a name="next-steps"></a>Volgende stappen

- [Gebruik PowerShell om een eindpunt voor virtuele netwerkservices te maken en vervolgens een virtuele netwerkregel voor Azure SQL Database.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Virtuele netwerkregels: bewerkingen][rest-api-virtual-network-rules-operations-862r] met REST API's

<!-- Link references, to images. -->
[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png
[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png
[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/management/deployment-models.md
[expressroute-indexmd-744v]: ../expressroute/index.yml
[rbac-what-is-813s]:../role-based-access-control/overview.md
[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md
[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md
[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100
[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
[rest-api-virtual-network-rules-operations-862r]: https://docs.microsoft.com/rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON
- Azure CLI
- ARM templates
-->
