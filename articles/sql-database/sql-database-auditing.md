---
title: Azure SQL-controle
description: Gebruik Azure SQL-databasecontrole om databasegebeurtenissen bij te houden in een controlelogboek.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/27/2020
ms.custom: azure-synapse
ms.openlocfilehash: 9e8aa9bbbdf166ba0caf29cd0bce22b8ed321e4e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685184"
---
# <a name="azure-sql-auditing"></a>Azure SQL-controle

Controle voor Azure [SQL Database](sql-database-technical-overview.md) en [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) houdt databasegebeurtenissen bij en schrijft deze naar een controlelogboek in uw Azure-opslagaccount, Log Analytics-werkruimte of Gebeurtenishubs. 

De controlefunctie biedt ook deze mogelijkheden:

- Naleving van wet- en regelgeving, inzicht in activiteiten in de database en in de afwijkingen en discrepanties die kunnen wijzen op problemen voor het bedrijf of vermoedelijke schendingen van de beveiliging.

- Mogelijk maken en faciliteren van nalevingsstandaarden, hoewel dit geen garantie biedt voor naleving. Zie het [Azure Trust Center,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) waar u de meest recente lijst met SQL Database-nalevingscertificeringen vinden, voor meer informatie over Azure-programma's die naleving van standaarden ondersteunen.

> [!NOTE] 
> Dit onderwerp is van toepassing op zowel Azure SQL Database als Azure Synapse Analytics-databases. Voor de eenvoud wordt SQL Database gebruikt wanneer u verwijst naar zowel Azure SQL Database als Azure Synapse Analytics.

## <a name="overview"></a><a id="overview"></a>Overzicht

U kunt SQL Database Auditing gebruiken voor het volgende:

- **Houd** een controlespoor van geselecteerde gebeurtenissen. U kunt categorieën van database-acties definiëren die moeten worden gecontroleerd.
- **Rapport over** databaseactiviteit. U kunt vooraf geconfigureerde rapporten en een dashboard gebruiken om snel aan de slag te gaan met de rapportage bvavan activiteiten en gebeurtenissen.
- **Rapporten analyseren.** U kunt verdachte gebeurtenissen, ongebruikelijke activiteiten en trends vinden.

> [!IMPORTANT]
> - Azure SQL Database auditing is geoptimaliseerd voor beschikbaarheid & prestaties. Tijdens zeer hoge activiteit azure SQL Database kunnen bewerkingen worden uitgevoerd en kan sommige gecontroleerde gebeurtenissen niet worden geregistreerd.

#### <a name="auditing-limitations"></a>Controlebeperkingen

- **Premium-opslag** wordt momenteel **niet ondersteund**.
- **Hiërarchische naamruimte** voor **Azure Data Lake Storage Gen2-opslagaccount** wordt momenteel niet **ondersteund**.
- Controle inschakelen op een onderbroken **Azure SQL Data Warehouse** wordt niet ondersteund. Als u controle wilt inschakelen, hervat u het gegevensmagazijn.

## <a name="define-server-level-vs-database-level-auditing-policy"></a><a id="server-vs-database-level"></a>Serverniveau versus controlebeleid op databaseniveau definiëren

Een controlebeleid kan worden gedefinieerd voor een specifieke database of als standaardserverbeleid:

- Er is een serverbeleid van toepassing op alle bestaande en nieuw gemaakte databases op de server.

- Als *serverblob-controle is ingeschakeld,* is dit *altijd van toepassing op de database*. De database wordt gecontroleerd, ongeacht de instellingen voor databasecontrole.

- Als u blobcontrole inschakelt in de database of het gegevensmagazijn, wordt deze *niet* op de server ingeschakeld, maar wordt geen van de instellingen van de serverblobcontrole overschreven of gewijzigd. Beide audits zullen naast elkaar bestaan. Met andere woorden, de database wordt twee maal parallel gecontroleerd; een keer door het serverbeleid en eenmaal door het databasebeleid.

   > [!NOTE]
   > U moet voorkomen dat u zowel serverblobauditing als databaseblobcontrole samen inschakelt, tenzij:
    > - U wilt een ander *opslagaccount,* *bewaarperiode* of *Log Analytics Workspace* gebruiken voor een specifieke database.
    > - U wilt gebeurtenistypen of -categorieën controleren voor een specifieke database die verschilt van de rest van de databases op de server. U bijvoorbeeld tabelinserts hebben die alleen moeten worden gecontroleerd voor een specifieke database.
   >
   > Anders raden we u aan alleen blobcontrole op serverniveau in te schakelen en de controle op databaseniveau uitgeschakeld te laten voor alle databases.

## <a name="set-up-auditing-for-your-server"></a><a id="setup-auditing"></a>Controle instellen voor uw server

Het standaardcontrolebeleid omvat alle acties en de volgende set actiegroepen, die alle query's en opgeslagen procedures die zijn uitgevoerd tegen de database controleren, evenals succesvolle en mislukte aanmeldingen:
  
  - BATCH_COMPLETED_GROUP
  - SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
  - FAILED_DATABASE_AUTHENTICATION_GROUP
  
U controle configureren voor verschillende typen acties en actiegroepen met PowerShell, zoals beschreven in de [sectie SQL-databasecontrole beheren met Azure PowerShell.](#manage-auditing)

Azure SQL Database Audit slaat 4000 tekens gegevens op voor tekenvelden in een controlerecord. Wanneer de **instructie** of de **data_sensitivity_information-waarden** die zijn geretourneerd uit een controleerbare actie meer dan 4000 tekens bevatten, worden alle gegevens die verder gaan dan de eerste 4000 tekens, **afgekapt en niet gecontroleerd**.
In de volgende sectie wordt de configuratie van controle beschreven met behulp van de Azure-portal.

1. Ga naar de [Azure-portal.](https://portal.azure.com)
2. Navigeer naar **Controle onder** de kop Beveiliging in het sql-database/servervenster.
3. Als u liever een servercontrolebeleid instelt, u de koppeling **Serverinstellingen weergeven** selecteren op de pagina databasecontrole. U vervolgens de controle-instellingen van de server weergeven of wijzigen. Servercontrolebeleid is van toepassing op alle bestaande en nieuw gemaakte databases op deze server.

    ![Navigatievenster](./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png)

4. Als u de controle op databaseniveau liever inschakelt, schakelt u **Controle** over op **AAN**. Als servercontrole is ingeschakeld, bestaat de door de database geconfigureerde audit naast de servercontrole.

5. U hebt meerdere opties voor het configureren waar auditlogs worden geschreven. U logboeken schrijven naar een Azure-opslagaccount, naar een Log Analytics-werkruimte voor verbruik door Azure Monitor-logboeken (voorbeeld) of naar gebeurtenishub voor verbruik met gebeurtenishub (voorbeeld). U elke combinatie van deze opties configureren en er worden controlelogboeken naar elk geschreven.
  
   ![opslagopties](./media/sql-database-auditing-get-started/auditing-select-destination.png)
   
### <a name=""></a><a id="audit-storage-destination">Controle naar opslagbestemming</a>

Als u het schrijven van controlelogboeken wilt configureren voor een opslagaccount, selecteert u **Opslaggegevens** en **opent u Opslaggegevens**. Selecteer het Azure-opslagaccount waar logboeken worden opgeslagen en selecteer vervolgens de bewaarperiode. Klik vervolgens op **OK**. Logboeken die ouder zijn dan de bewaartermijn worden verwijderd.

- De standaardwaarde voor bewaartermijn is 0 (onbeperkte retentie). U deze waarde wijzigen door de schuifregelaar **Retentie (Dagen)** in **Opslaginstellingen** te verplaatsen wanneer u het opslagaccount configureert voor controle.
  - Als u de bewaartermijn wijzigt van 0 (onbeperkte retentie) naar een andere waarde, moet u er rekening mee houden dat bewaartijd alleen van toepassing is op logboeken die zijn geschreven nadat de bewaarwaarde is gewijzigd (logboeken die zijn geschreven tijdens de periode waarin de bewaartermijn is ingesteld op onbeperkt, behouden blijven, zelfs nadat retentie is ingeschakeld).

  ![opslagaccount](./media/sql-database-auditing-get-started/auditing_select_storage.png)

#### <a name="remarks"></a>Opmerkingen

- Controlelogboeken worden geschreven om **Blobs toe** te schrijven in een Azure Blob-opslag op uw Azure-abonnement
- Als u een onveranderlijklogboekarchief wilt configureren voor de controlegebeurtenissen op server- of databaseniveau, volgt u de [instructies van Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes) (Zorg ervoor dat u Extra **toevoegenen toestaan** hebt geselecteerd wanneer u de onveranderlijke blobopslag configureert).
- U controlelogboeken schrijven naar een Azure Storage-account achter een VNet of firewall. Voor specifieke instructies zie, [Schrijf audit naar een opslagaccount achter VNet en firewall](create-auditing-storage-account-vnet-firewall.md).
- Nadat u uw controle-instellingen hebt geconfigureerd, u de nieuwe functie voor bedreigingsdetectie inschakelen en e-mails configureren om beveiligingswaarschuwingen te ontvangen. Wanneer u bedreigingsdetectie gebruikt, ontvangt u proactieve waarschuwingen over afwijkende databaseactiviteiten die potentiële beveiligingsbedreigingen kunnen aangeven. Zie [Aan de slag met bedreigingsdetectie](sql-database-threat-detection-get-started.md)voor meer informatie.
- Zie de naslagverwijzing naar de [blobcontrolelogboeknota](https://go.microsoft.com/fwlink/?linkid=829599)voor meer informatie over de logboekindeling, hiërarchie van de opslagmap en naamgevingsconventies.
- Bij het gebruik van AAD-verificatie worden mislukte aanmeldingsrecords *niet* weergegeven in het SQL-controlelogboek. Als u mislukte inlogcontrolerecords wilt weergeven, moet u de [Azure Active Directory-portal]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)bezoeken, die details van deze gebeurtenissen registreert.
- Controle op [alleen-lezen replica's](sql-database-read-scale-out.md) wordt automatisch ingeschakeld. Zie de [SQL Database Audit Log Format](sql-database-audit-log-format.md)voor meer informatie over de hiërarchie van de opslagmappen, naamgevingsconventies en logboekindeling. 

### <a name=""></a><a id="audit-log-analytics-destination">Controleren op de doelbestemming Log Analytics</a>
  
Als u het schrijven van controlelogboeken wilt configureren voor een werkruimte log Analytics, selecteert u **Log Analytics (Voorbeeld)** en opent **u Logboekanalysegegevens**. Selecteer of maak de werkruimte Log Analytics waar logboeken worden geschreven en klik op **OK**.
   
   ![LogAnalyticsworkspace](./media/sql-database-auditing-get-started/auditing_select_oms.png)

### <a name=""></a><a id="audit-event-hub-destination">Controle naar de bestemming Gebeurtenishub</a>

> [!WARNING]
> Als u controle inschakelt op een server met een SQL-pool, **wordt de SQL-groep hervat en opnieuw onderbroken,** waardoor factureringskosten kunnen worden inrekening.
> Het inschakelen van controle op een onderbroken SQL-pool is niet mogelijk. Als u deze wilt inschakelen, pauzeert u de SQL-groep.

Als u het schrijven van controlelogboeken wilt configureren op een gebeurtenishub, selecteert u **Gebeurtenishub (Voorbeeld)** en opent **u details van gebeurtenishub**. Selecteer de gebeurtenishub waar logboeken worden geschreven en klik op **OK**. Zorg ervoor dat de gebeurtenishub zich in dezelfde regio bevindt als uw database en server.

   ![Eventhub (Eventhub)](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

## <a name="analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>Controlelogboeken en rapporten analyseren

Als u ervoor kiest om controlelogboeken te schrijven naar Azure Monitor-logboeken:

- Gebruik de [Azure-portal](https://portal.azure.com).  Open de relevante database. Klik boven aan de **controlepagina** van de database op **Controlelogboeken weergeven**.

    ![controlelogboeken weergeven](./media/sql-database-auditing-get-started/auditing-view-audit-logs.png)

- Vervolgens u de logboeken op twee manieren bekijken:
    
    Als u boven aan de pagina **Controlerecords** op **Logboekanalyse** klikt, opent u de weergave Logboeken in de werkruimte Log Analytics, waar u het tijdsbereik en de zoekopdracht aanpassen.
    
    ![openen in de werkruimte Log Analytics](./media/sql-database-auditing-get-started/auditing-log-analytics.png)

    Als u boven aan de pagina **Controlerecords** op **Het dashboard Weergeven** klikt, wordt een dashboard geopend met informatie over controlelogboeken, waar u inzoomen op beveiligingsinzichten, toegang tot gevoelige gegevens en meer. Dit dashboard is ontworpen om u te helpen beveiligingsinzichten voor uw gegevens te verkrijgen.
    U ook het tijdsbereik en de zoekopdracht aanpassen. 
    ![Dashboard Logboekanalyse weergeven](media/sql-database-auditing-get-started/auditing-view-dashboard.png)

    ![Dashboard logboekanalyse](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard.png)

    ![Beveiligingsinzichten van Log Analytics](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard-data.png)
 

- U ook toegang krijgen tot de controlelogboeken van Log Analytics blade. Open de werkruimte Log Analytics en klik onder **Algemene** sectie op **Logboeken**. U beginnen met een eenvoudige query, zoals: *zoek op SQLSecurityAuditEvents* om de controlelogboeken weer te geven.
    Vanaf hier u azure [monitor-logboeken](../log-analytics/log-analytics-log-search.md) ook gebruiken om geavanceerde zoekopdrachten uit te voeren op uw controlelogboekgegevens. Azure Monitor-logboeken bieden u realtime operationele inzichten met behulp van geïntegreerde zoek- en aangepaste dashboards om gemakkelijk miljoenen records te analyseren op al uw workloads en servers. Zie [Azure Monitor-logboeken zoekreferentie](../log-analytics/log-analytics-log-search.md)voor aanvullende nuttige informatie over Azure Monitor-logboeken .

Als u ervoor kiest om controlelogboeken naar gebeurtenishub te schrijven:

- Als u controlelogboekgegevens van gebeurtenishub wilt gebruiken, moet u een stream instellen om gebeurtenissen te consumeren en naar een doel te schrijven. Zie [Azure Event Hubs Documentation](../event-hubs/index.yml)voor meer informatie.
- Controlelogboeken in Gebeurtenishub worden vastgelegd in de behuizing van [Apache Avro-gebeurtenissen](https://avro.apache.org/) en opgeslagen met JSON-opmaak met UTF-8-codering. Als u de controlelogboeken wilt lezen, u [Avro Tools](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) of vergelijkbare hulpprogramma's gebruiken die dit formaat verwerken.

Als u ervoor hebt gekozen om controlelogboeken naar een Azure-opslagaccount te schrijven, zijn er verschillende methoden die u gebruiken om de logboeken weer te geven:

- Controlelogboeken worden samengevoegd in het account dat u tijdens de installatie hebt gekozen. U controlelogboeken verkennen met behulp van een hulpprogramma zoals [Azure Storage Explorer.](https://storageexplorer.com/) In Azure-opslag worden controlelogboeken opgeslagen als een verzameling blobbestanden in een container met de naam **sqldbauditlogs.** Zie de [SQL Database Audit Log Format](https://go.microsoft.com/fwlink/?linkid=829599)voor meer informatie over de hiërarchie van de opslagmappen, naamgevingsconventies en logboekindeling.

- Gebruik de [Azure-portal](https://portal.azure.com).  Open de relevante database. Klik boven aan de **controlepagina** van de database op **Controlelogboeken weergeven**.

    ![Navigatievenster](./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png)

    Er worden **controlerecords** geopend, waarvan u de logboeken bekijken.

  - U specifieke datums weergeven door boven aan de pagina **Controlerecords** op **Filter** te klikken.
  - U schakelen tussen controlerecords die zijn gemaakt door het *servercontrolebeleid* en het *controlebeleid van* de database door **de controlebron te**schakelen.
  - U alleen SQL-injectiegerelateerde controlerecords weergeven door **het selectievakje Alleen controlerecords voor SQL-injecties weergeven.**

       ![Navigatievenster]( ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png)

- Gebruik de systeemfunctie **sys.fn_get_audit_file** (T-SQL) om de controleloggegevens in tabelindeling te retourneren. Zie [sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql)voor meer informatie over het gebruik van deze functie.

- Gebruik **Controlebestanden samenvoegen** in SQL Server Management Studio (te beginnen met SSMS 17):
    1. Selecteer in het menu SSMS de optie Controlebestanden**voor samenvoegen** > **invoegen** **.** > 

        ![Navigatievenster](./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png)
    2. Het dialoogvenster **Controlebestanden toevoegen** wordt geopend. Selecteer een van de **opties Toevoegen** om te kiezen of u controlebestanden wilt samenvoegen vanaf een lokale schijf of deze wilt importeren uit Azure Storage. U moet uw Azure Storage-gegevens en accountsleutel opgeven.

    3. Nadat alle bestanden zijn toegevoegd, klikt u op **OK** om de samenvoegbewerking te voltooien.

    4. Het samengevoegde bestand wordt geopend in SSMS, waar u het bekijken en analyseren, en het exporteren naar een XEL- of CSV-bestand of naar een tabel.

- Gebruik Power BI. U controlelogboekgegevens bekijken en analyseren in Power BI. Zie [Controlelogboekgegevens in Power BI analyseren](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/)voor meer informatie en voor toegang tot een downloadbare sjabloon.
- Download logboekbestanden uit uw Azure Storage blob-container via de portal of met behulp van een hulpprogramma zoals [Azure Storage Explorer.](https://storageexplorer.com/)
  - Nadat u een logboekbestand lokaal hebt gedownload, dubbelklikt u op het bestand om de logboeken in SSMS te openen, weer te geven en te analyseren.
  - U ook meerdere bestanden tegelijk downloaden via Azure Storage Explorer. Klik hiervoor met de rechtermuisknop op een specifieke submap en selecteer **Opslaan als** opslaan in een lokale map.

- Aanvullende methoden:

  - Nadat u meerdere bestanden of een submap met logboekbestanden hebt gedownload, u deze lokaal samenvoegen zoals beschreven in de eerder beschreven instructies voor Controlebestanden van SSMS Samenvoegen.
  - Blob-controlelogboeken programmatisch weergeven:

    - [Query Extended Events Files](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/) met PowerShell.

## <a name="production-practices"></a><a id="production-practices"></a>Productiepraktijken

<!--The description in this section refers to preceding screen captures.-->

#### <a name="auditing-geo-replicated-databases"></a>Geo-gerepliceerde databases controleren

Met geo-gerepliceerde databases heeft de secundaire database een identiek controlebeleid wanneer u controle inschakelt op de primaire database. Het is ook mogelijk om auditing in te stellen op de secundaire database door controle op de **secundaire server**in te schakelen , onafhankelijk van de primaire database.

- Serverniveau (**aanbevolen**): Schakel controle in op zowel de **primaire server** als de **secundaire server** - de primaire en secundaire databases worden elk onafhankelijk gecontroleerd op basis van hun respectieve serverbeleid.
- Databaseniveau: controle op databaseniveau voor secundaire databases kan alleen worden geconfigureerd vanuit primaire databasecontrole-instellingen.
  - Controle moet worden ingeschakeld op de *primaire database zelf,* niet op de server.
  - Nadat auditing is ingeschakeld in de primaire database, wordt deze ook ingeschakeld in de secundaire database.

    >[!IMPORTANT]
    >Met controle op databaseniveau zijn de opslaginstellingen voor de secundaire database identiek aan die van de primaire database, waardoor cross-regional verkeer ontstaat. We raden u aan alleen controle op serverniveau in te schakelen en de controle op databaseniveau uitgeschakeld te laten voor alle databases.

#### <a name="storage-key-regeneration"></a>Regeneratie van opslagsleutels

In de productie zult u uw opslagsleutels waarschijnlijk periodiek vernieuwen. Wanneer u controlelogboeken schrijft voor Azure-opslag, moet u uw controlebeleid opnieuw opslaan wanneer u uw sleutels ververst. Het proces is als volgt:

1. Opslaggegevens **openen**. Selecteer secundair en klik in het vak **Opslagtoegangssleutel** op **Secundair**en klik op **OK**. Klik vervolgens boven aan de controleconfiguratiepagina op **Opslaan.**

    ![Navigatievenster](./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png)
2. Ga naar de pagina opslagconfiguratie en regenereerde de primaire toegangssleutel.

    ![Navigatievenster](./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png)
3. Ga terug naar de controleconfiguratiepagina, schakel de opslagtoegangssleutel over van secundair naar primair en klik op **OK.** Klik vervolgens boven aan de controleconfiguratiepagina op **Opslaan.**
4. Ga terug naar de pagina opslagconfiguratie en regenereerde de secundaire toegangssleutel (ter voorbereiding van de vernieuwingscyclus van de volgende toets).

## <a name="manage-azure-sql-server-and-database-auditing"></a><a id="manage-auditing"></a>Azure SQL Server- en Databasecontrole beheren

#### <a name="using-azure-powershell"></a>Azure PowerShell gebruiken

**PowerShell-cmdlets (inclusief WHERE-clausuleondersteuning voor extra filtering)**:

- [Databasecontrolebeleid maken of bijwerken (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Servercontrolebeleid maken of bijwerken (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [Databasecontrolebeleid (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Servercontrolebeleid ophalen (Get-azsqlserveraudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [Databasecontrolebeleid verwijderen (controle van azsqldatabaseverwijderen)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Servercontrolebeleid verwijderen (controle-azsqlservercontrole verwijderen)](/powershell/module/az.sql/remove-azsqlserveraudit)

Zie [Controle- en bedreigingsdetectie configureren met PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md)voor een voorbeeld van een script.

#### <a name="using-rest-api"></a>REST API gebruiken

**REST API**:

- [Controlebeleid voor database maken of bijwerken](/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Servercontrolebeleid maken of bijwerken](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Beleid voor databasecontrole](/rest/api/sql/database%20auditing%20settings/get)
- [Servercontrolebeleid ophalen](/rest/api/sql/server%20auditing%20settings/get)

Uitgebreid beleid met WHERE-clausuleondersteuning voor extra filtering:

- [*Uitgebreide* controlebeleid voor database maken of bijwerken](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [*Uitgebreid* controlebeleid voor server maken of bijwerken](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [*Uitgebreide* controlebeleid voor database's](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Uitgebreid *controlebeleid* voor server krijgen](/rest/api/sql/server%20auditing%20settings/get)

#### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen gebruiken

U Azure SQL-databasecontrole beheren met [Azure Resource Manager-sjablonen,](../azure-resource-manager/management/overview.md) zoals in deze voorbeelden wordt weergegeven:

- [Een Azure SQL Server implementeren met controle ingeschakeld om controlelogboeken te schrijven naar Azure Blob-opslagaccount](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Een Azure SQL Server implementeren met controle ingeschakeld om controlelogboeken te schrijven naar Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Een Azure SQL Server implementeren met controle ingeschakeld om controlelogboeken naar gebeurtenishubs te schrijven](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> De gekoppelde monsters bevinden zich op een externe openbare opslagplaats en worden 'as is' geleverd, zonder garantie, en worden niet ondersteund onder een Microsoft-ondersteuningsprogramma/-service.
