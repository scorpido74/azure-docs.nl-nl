---
title: Aan de slag met auditing
description: Azure SQL database-controle gebruiken om database gebeurtenissen in een audit logboek bij te houden.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 08/22/2019
ms.openlocfilehash: 450f40c8ba49028d99143d7cf2b2995eb354f8fd
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551621"
---
# <a name="get-started-with-sql-database-auditing"></a>Aan de slag met SQL Database Auditing

Controle voor Azure [SQL database](sql-database-technical-overview.md) en [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) houdt database gebeurtenissen bij en schrijft deze naar een audit logboek in uw Azure storage-account, Log Analytics werk ruimte of event hubs. De controlefunctie biedt ook deze mogelijkheden:

- Naleving van wet- en regelgeving, inzicht in activiteiten in de database en in de afwijkingen en discrepanties die kunnen wijzen op problemen voor het bedrijf of vermoedelijke schendingen van de beveiliging.

- Mogelijk maken en faciliteren van nalevingsstandaarden, hoewel dit geen garantie biedt voor naleving. Voor meer informatie over Azure-Program ma's die naleving van standaarden ondersteunen, raadpleegt u de [Vertrouwenscentrum van Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) waarin u de meest recente lijst met SQL database nalevings certificeringen kunt vinden.


> [!NOTE] 
> Dit onderwerp is van toepassing op Azure SQL-servers en op SQL Database- en SQL Data Warehouse-databases die op deze Azure SQL-servers worden gemaakt. Voor het gemak wordt de term 'SQL Database' gebruikt wanneer er wordt verwezen naar zowel SQL Database als SQL Data Warehouse.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a id="subheading-1"></a>Overzicht van de controle van Azure SQL database

U kunt SQL Database Auditing gebruiken voor het volgende:

- Een audittrail van geselecteerde gebeurtenissen **bewaren** . U kunt categorieën van database-acties definiëren die moeten worden gecontroleerd.
- **Rapport** over de database activiteit. U kunt vooraf geconfigureerde rapporten en een dashboard gebruiken om snel aan de slag te gaan met de rapportage bvavan activiteiten en gebeurtenissen.
- Rapporten **analyseren** . U kunt verdachte gebeurtenissen, ongebruikelijke activiteiten en trends vinden.

> [!IMPORTANT]
> Audit logboeken worden geschreven om **blobs toe te voegen** in Azure Blob-opslag in uw Azure-abonnement.
>
> - Alle opslag typen (v1, v2, blob) worden ondersteund.
> - Alle configuraties voor opslag replicatie worden ondersteund.
> - **Premium-opslag** wordt momenteel **niet ondersteund**.
> - **Opslag in VNet** wordt momenteel **niet ondersteund**.
> - **Opslag achter een firewall** wordt momenteel **niet ondersteund**.
> - **Hiërarchische naam ruimte** voor **Azure data Lake Storage Gen2 opslag account** wordt momenteel **niet ondersteund**.

## <a id="subheading-8"></a>Het controle beleid op server niveau versus database niveau definiëren

Er kan een controle beleid worden gedefinieerd voor een specifieke data base of als standaard server beleid:

- Een server beleid is van toepassing op alle bestaande en nieuw gemaakte data bases op de server.

- Als de controle van de *Server-blob is ingeschakeld*, *is deze altijd van toepassing op de data base*. De data base wordt gecontroleerd, ongeacht de controle-instellingen voor de data base.

- Het inschakelen van BLOB auditing in de data base of het Data Warehouse, behalve het inschakelen op de server, *overschrijft of wijzigt geen van* de instellingen van de controle van de server-blob. Beide controles bestaan naast elkaar. Met andere woorden, de data base wordt twee keer parallel gecontroleerd. eenmaal door het Server beleid en eenmaal door het database beleid.

   > [!NOTE]
   > Vermijd het inschakelen van zowel de controle van de server-BLOB als de samen voeging van de data base-blob, tenzij:
    > - U wilt een ander *opslag account* of een *Bewaar periode* voor een specifieke data base gebruiken.
    > - U wilt controle van gebeurtenis typen of categorieën voor een specifieke data base die verschilt van de rest van de data bases op de server. U kunt bijvoorbeeld tabellen invoegen die alleen moeten worden gecontroleerd voor een specifieke data base.
   >
   > Anders wordt u aangeraden alleen BLOB-controle op server niveau in te scha kelen en de controle op database niveau uitgeschakeld te laten voor alle data bases.

## <a id="subheading-2"></a>Controle instellen voor uw data base

In de volgende sectie wordt de configuratie van de controle met behulp van de Azure Portal beschreven.

1. Ga naar de [Azure Portal](https://portal.azure.com).
2. Navigeer naar **controle** onder de kop beveiliging in het deel venster SQL database/server.

    <a id="auditing-screenshot"></a>![navigatie deel venster][1]

3. Als u liever een server controle beleid instelt, kunt u de koppeling **Server instellingen weer geven** op de pagina database controle selecteren. U kunt vervolgens de instellingen voor de controle van de server weer geven of wijzigen. Het controle beleid voor servers is van toepassing op alle bestaande en nieuw gemaakte data bases op deze server.

    ![Navigatievenster][2]

4. Als u de controle wilt inschakelen op het niveau van de data base, schakelt u **controle** in **op**aan.

    Als server controle is ingeschakeld, is de door de data base geconfigureerde controle naast de server controle aanwezig.

    ![Navigatievenster][3]

5. **Nieuw** : u hebt nu meerdere opties voor het configureren van de locatie waar audit logboeken worden geschreven. U kunt Logboeken schrijven naar een Azure-opslag account, naar een Log Analytics-werk ruimte voor het gebruik van Azure Monitor-Logboeken of Event Hub voor gebruik met Event Hub. U kunt een wille keurige combi natie van deze opties configureren en er worden controle logboeken naar elke optie geschreven.

   > [!WARNING]
   > Als u controle inschakelt op Log Analytics, worden er kosten in rekening gebracht op basis van opname tarieven. Houd rekening met de gekoppelde kosten met behulp van deze [optie](https://azure.microsoft.com/pricing/details/monitor/)of overweeg de audit logboeken op te slaan in een Azure-opslag account.

    ![opslag opties](./media/sql-database-auditing-get-started/auditing-select-destination.png)

6. Als u het schrijven van audit logboeken naar een opslag account wilt configureren, selecteert u **opslag** en opent u **opslag Details**. Selecteer het Azure-opslag account waarin de logboeken worden opgeslagen en selecteer vervolgens de Bewaar periode. De oude logboeken worden verwijderd. Klik vervolgens op **OK**.

   > [!IMPORTANT]
   > - De standaard waarde voor de Bewaar periode is 0 (onbeperkte retentie). U kunt deze waarde wijzigen door de schuif regelaar voor **bewaren (dagen)** in **opslag instellingen** te verplaatsen bij het configureren van het opslag account voor controle.
   > - Als u de retentie periode van 0 (onbeperkte retentie) wijzigt naar een andere waarde, moet u er rekening mee houden dat bewaren alleen van toepassing is op de logboeken die zijn geschreven nadat de Bewaar waarde is gewijzigd (tijdens de periode waarin retentie is ingesteld op onbeperkt, blijven behouden, zelfs nadat bewaren is ingeschakeld)

    ![opslagaccount](./media/sql-database-auditing-get-started/auditing_select_storage.png)

7. Als u het schrijven van audit logboeken naar een Log Analytics-werk ruimte wilt configureren, selecteert u **log Analytics (preview)** en opent u **log Analytics Details**. Selecteer of maak de Log Analytics-werk ruimte waar de logboeken worden geschreven en klik vervolgens op **OK**.

    ![Log Analytics-werkruimte](./media/sql-database-auditing-get-started/auditing_select_oms.png)

8. Als u het schrijven van audit logboeken naar een Event Hub wilt configureren, selecteert u **Event hub (preview)** en opent u **Details van Event hub**. Selecteer de Event Hub waar de logboeken worden geschreven en klik vervolgens op **OK**. Zorg ervoor dat de Event Hub zich in dezelfde regio bevindt als de-data base en-server.

    ![Event Hub](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

9. Klik op **Opslaan**.
10. Als u de gecontroleerde gebeurtenissen wilt aanpassen, kunt u dit doen via [Power shell-cmdlets](#subheading-7) of de [rest API](#subheading-9).
11. Nadat u de controle-instellingen hebt geconfigureerd, kunt u de nieuwe functie voor het detecteren van bedreigingen inschakelen en e-mail berichten configureren voor het ontvangen van beveiligings waarschuwingen. Wanneer u detectie van dreigingen gebruikt, ontvangt u proactieve waarschuwingen over afwijkende database activiteiten die kunnen wijzen op mogelijke beveiligings dreigingen. Zie aan de slag [met detectie van bedreigingen](sql-database-threat-detection-get-started.md)voor meer informatie.

> [!IMPORTANT]
> Het is niet mogelijk om controle in te scha kelen op een onderbroken Azure SQL Data Warehouse. Als u deze functie wilt inschakelen, moet u het Data Warehouse opheffen.

> [!WARNING]
> Als u controle inschakelt op een server waarop een Azure SQL Data Warehouse **wordt weer gegeven, wordt het Data Warehouse hervat en opnieuw onderbroken** . Dit kan in rekening worden gebracht.

## <a id="subheading-3"></a>Controle logboeken en-rapporten analyseren

Als u ervoor hebt gekozen om audit logboeken naar Azure Monitor-logboeken te schrijven:

- Gebruik [Azure Portal](https://portal.azure.com).  Open de relevante data base. Klik boven aan de **controle** pagina van de Data Base op **audit logboeken weer geven**.

    ![audit logboeken weer geven](./media/sql-database-auditing-get-started/auditing-view-audit-logs.png)

- Vervolgens hebt u twee manieren om de logboeken weer te geven:
    
    Als u op **log Analytics** boven aan de pagina **controle records** klikt, wordt de weer gave Logboeken in log Analytics werk ruimte geopend, waar u het tijds bereik en de zoek query kunt aanpassen.
    
    ![openen in Log Analytics werk ruimte](./media/sql-database-auditing-get-started/auditing-log-analytics.png)

    Als u op het **dash board weer geven** boven aan de pagina **controle records** klikt, wordt er een dash board geopend met informatie over de audit logboeken, waar u kunt inzoomen op beveiligings inzichten, toegang tot gevoelige gegevens en meer. Dit dash board is ontworpen om u te helpen bij het verkrijgen van beveiligings inzichten voor uw gegevens.
    U kunt ook het tijds bereik en de zoek query aanpassen. 
    Log Analytics dashboard](media/sql-database-auditing-get-started/auditing-view-dashboard.png) ![weer geven

    ![Log Analytics dash board](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard.png)

    ![Log Analytics Security Insights](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard-data.png)
 

- U kunt ook toegang krijgen tot de audit logboeken vanuit Log Analytics Blade. Open uw Log Analytics-werk ruimte en klik onder **algemene** sectie op **Logboeken**. U kunt beginnen met een eenvoudige query, bijvoorbeeld: *Zoek naar SQLSecurityAuditEvents* om de audit logboeken weer te geven.
    Hier kunt u ook [Azure monitor-logboeken](../log-analytics/log-analytics-log-search.md) gebruiken om geavanceerde zoek opdrachten uit te voeren in uw audit logboek gegevens. Met Azure Monitor-Logboeken kunt u in realtime operationeel inzicht krijgen met behulp van geïntegreerde Zoek-en aangepaste Dash boards waarmee u miljoenen records in al uw workloads en servers eenvoudig kunt analyseren. Zie voor aanvullende nuttige informatie over Azure Monitor Zoek taal en-opdrachten in Logboeken [Azure monitor logboeken zoeken](../log-analytics/log-analytics-log-search.md).

Als u ervoor hebt gekozen om audit logboeken naar Event hub te schrijven:

- Als u gegevens van de audit logboeken van Event hub wilt gebruiken, moet u een stroom instellen om gebeurtenissen te gebruiken en deze naar een doel te schrijven. Zie de [documentatie van Azure Event hubs](../event-hubs/index.yml)voor meer informatie.
- Audit Logboeken in Event hub worden vastgelegd in de hoofd tekst van [Apache Avro](https://avro.apache.org/) -gebeurtenissen en opgeslagen met behulp van JSON-indeling met UTF-8-code ring. Als u de audit logboeken wilt lezen, kunt u [Avro-Hulpprogram ma's](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) of gelijksoortige hulp middelen gebruiken waarmee deze indeling wordt verwerkt.

Als u ervoor hebt gekozen om audit logboeken naar een Azure Storage-account te schrijven, zijn er verschillende methoden om de logboeken weer te geven:

> [!NOTE] 
> Controle op [alleen-lezen replica's](sql-database-read-scale-out.md) wordt automatisch ingeschakeld. Voor meer informatie over de hiërarchie van de opslag mappen, naam conventies en logboek indeling raadpleegt u de [SQL database controle logboek indeling](sql-database-audit-log-format.md). 

- Audit logboeken worden geaggregeerd in het account dat u tijdens de installatie hebt gekozen. U kunt audit logboeken verkennen met behulp van een hulp programma zoals [Azure Storage Explorer](https://storageexplorer.com/). In azure Storage worden controle Logboeken opgeslagen als een verzameling BLOB-bestanden in een container met de naam **sqldbauditlogs**. Voor meer informatie over de hiërarchie van de opslag mappen, naam conventies en logboek indeling raadpleegt u de [SQL database controle logboek indeling](https://go.microsoft.com/fwlink/?linkid=829599).

- Gebruik [Azure Portal](https://portal.azure.com).  Open de relevante data base. Klik boven aan de **controle** pagina van de Data Base op **audit logboeken weer geven**.

    ![Navigatievenster][7]

    **Controle records** worden geopend, waaruit u de logboeken kunt weer geven.

  - U kunt specifieke datums weer geven door boven aan de pagina **controle records** op **filter** te klikken.
  - U kunt scha kelen tussen controle records die zijn gemaakt door het *Server controlebeleid* en het *database controlebeleid* door te scha kelen op **controle bron**.
  - U kunt alleen controle records met betrekking tot SQL-injectie weer geven door **alleen controle records voor SQL-injecties weer geven** in te scha kelen.

       ![Navigatievenster][8]

- Gebruik de systeem functie **sys. fn_get_audit_file** (T-SQL) om de controle logboek gegevens in tabel vorm te retour neren. Zie [sys. fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql)voor meer informatie over het gebruik van deze functie.

- **Samenvoeg controle bestanden** in SQL Server Management Studio gebruiken (vanaf SSMS 17):
    1. Selecteer in het menu SSMS **File** > **Open** > **audit bestanden samen voegen**.

        ![Navigatievenster][9]
    2. Het dialoog venster **controle bestanden toevoegen** wordt geopend. Selecteer een van de opties voor **toevoegen** om te kiezen of u de audit bestanden van een lokale schijf wilt samen voegen of uit Azure Storage wilt importeren. U moet uw Azure Storage Details en de account sleutel opgeven.

    3. Nadat alle bestanden die u wilt samen voegen, zijn toegevoegd, klikt u op **OK** om de samenvoeg bewerking te volt ooien.

    4. Het samengevoegde bestand wordt geopend in SSMS, waar u het kunt weer geven en analyseren, en het exporteren naar een XEL-of CSV-bestand of naar een tabel.

- Gebruik Power BI. U kunt audit logboek gegevens in Power BI weer geven en analyseren. Zie [audit logboek gegevens analyseren in Power bi](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/)voor meer informatie en om toegang te krijgen tot een sjabloon die u kunt downloaden.
- Down load de logboek bestanden van uw Azure Storage BLOB-container via de portal of met behulp van een hulp programma zoals [Azure Storage Explorer](https://storageexplorer.com/).
  - Nadat u een logboek bestand lokaal hebt gedownload, dubbelklikt u op het bestand om de logboeken in SSMS te openen, weer te geven en te analyseren.
  - U kunt ook meerdere bestanden tegelijk downloaden via Azure Storage Explorer. Als u dit wilt doen, klikt u met de rechter muisknop op een specifieke submap en selecteert u **Opslaan als** om op te slaan in een lokale map.

- Aanvullende methoden:

  - Nadat u meerdere bestanden of een submap met logboek bestanden hebt gedownload, kunt u deze lokaal samen voegen zoals beschreven in de instructies voor het samen voegen van de opdracht SSMS merge.
  - Audit logboeken voor blobs weer geven via een programma:

    - [Query's uitvoeren op bestanden met uitgebreide gebeurtenissen](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/) met behulp van Power shell.

## <a id="subheading-5"></a>Productie-procedures

<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">Geo-gerepliceerde data bases controleren</a>

Bij geo-gerepliceerde data bases, wanneer u controle inschakelt voor de hoofd database, heeft de secundaire Data Base een identiek controle beleid. Het is ook mogelijk om controle in te stellen voor de secundaire data base door controle in te scha kelen op de **secundaire server**, onafhankelijk van de primaire data base.

- Server niveau (**Aanbevolen**): Schakel de controle op zowel de **primaire server** als de **secundaire server** in. de primaire en secundaire data bases worden elk onafhankelijk gecontroleerd op basis van hun respectieve beleid op server niveau.
- Database niveau: controle op database niveau voor secundaire data bases kan alleen worden geconfigureerd vanuit de instellingen voor de controle van primaire data bases.
  - De controle moet worden ingeschakeld op de *primaire data base zelf*, niet op de server.
  - Nadat de controle is ingeschakeld op de primaire data base, wordt deze ook ingeschakeld op de secundaire data base.

    >[!IMPORTANT]
    >Bij het controleren op database niveau zijn de opslag instellingen voor de secundaire data base gelijk aan die van de primaire data base, waardoor Kruis regionale verkeer wordt veroorzaakt. U wordt aangeraden alleen controle op server niveau in te scha kelen en de controle op database niveau uitgeschakeld te laten voor alle data bases.

### <a id="subheading-6">Opnieuw genereren van de opslag sleutel</a>

In productie zult u uw opslag sleutels waarschijnlijk periodiek vernieuwen. Wanneer u audit logboeken naar Azure Storage schrijft, moet u uw controle beleid opnieuw opslaan bij het vernieuwen van uw sleutels. Het proces is als volgt:

1. **Opslag Details**openen. Selecteer in het vak **toegangs sleutel voor opslag** de optie **secundair**en klik op **OK**. Klik vervolgens boven aan de pagina controle configuratie op **Opslaan** .

    ![Navigatievenster][5]
2. Ga naar de pagina opslag configuratie en Genereer de primaire toegangs sleutel opnieuw.

    ![Navigatievenster][6]
3. Ga terug naar de pagina controle configuratie, schakel de toegangs sleutel voor opslag van secundair naar primair in en klik vervolgens op **OK**. Klik vervolgens boven aan de pagina controle configuratie op **Opslaan** .
4. Ga terug naar de pagina opslag configuratie en Genereer de secundaire toegangs sleutel opnieuw (in voor bereiding voor de vernieuwings cyclus van de volgende sleutel).

## <a name="additional-information"></a>Aanvullende informatie

- Zie de naslag informatie over de [indeling van BLOB-controle logboeken](https://go.microsoft.com/fwlink/?linkid=829599)voor meer informatie over de logboek indeling, de hiërarchie van de opslag map en naam conventies.

    > [!IMPORTANT]
    > Met Azure SQL Database audit worden 4000 tekens gegevens opgeslagen voor teken velden in een controle record. Wanneer de **instructie** of de **data_sensitivity_information** waarden die zijn geretourneerd door een Controleer bare actie meer dan 4000 tekens bevatten, worden alle gegevens na de eerste 4000 tekens **afgekapt en niet gecontroleerd**.

- Audit logboeken worden geschreven om **blobs toe te voegen** in een Azure Blob-opslag op uw Azure-abonnement:
  - **Premium Storage** wordt momenteel **niet ondersteund** door toevoeg-blobs.
  - **Opslag in VNet** wordt momenteel **niet ondersteund**.

- Het standaard controlebeleid bevat alle acties en de volgende set actie groepen, waarmee alle query's en opgeslagen procedures worden gecontroleerd die worden uitgevoerd op de data base, evenals geslaagde en mislukte aanmeldingen:

    BATCH_COMPLETED_GROUP<br>
    SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP<br>
    FAILED_DATABASE_AUTHENTICATION_GROUP

    U kunt controle configureren voor verschillende typen acties en actie groepen met behulp van Power shell, zoals beschreven in de sectie [Manage SQL database auditing using Azure PowerShell](#subheading-7) .

- Wanneer u AAD-verificatie gebruikt, worden records met mislukte aanmeldingen *niet* weer gegeven in het SQL-controle logboek. Als u mislukte aanmeldings controle records wilt weer geven, gaat u naar de [Azure Active Directory-Portal]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md), waarin de details van deze gebeurtenissen worden vastgelegd.


## <a id="subheading-7"></a>Beheer van Azure SQL Server en data bases controleren met behulp van Azure PowerShell

**Power shell-cmdlets (inclusief WHERE-component ondersteuning voor aanvullende filters)** :

- [Controle beleid voor data bases maken of bijwerken (set-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Controle beleid voor servers maken of bijwerken (set-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserveraudit)
- [Database controlebeleid ophalen (Get-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Server controle beleid ophalen (Get-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveraudit)
- [Database controlebeleid verwijderen (Remove-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Server controle beleid verwijderen (Remove-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserveraudit)

Zie [controle en detectie van bedreigingen configureren met Power shell](scripts/sql-database-auditing-and-threat-detection-powershell.md)voor een voor beeld van een script.

## <a id="subheading-8"></a>Beheer van Azure SQL Server en data bases controleren met behulp van REST API

**REST API**:

- [Controle beleid voor data base maken of bijwerken](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Controle beleid voor servers maken of bijwerken](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Controle beleid voor data base ophalen](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/get)
- [Controle beleid voor server ophalen](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

Uitgebreid beleid met de component WHERE ondersteuning voor extra filtering:

- [Beleid voor *uitgebreide* controle van data base maken of bijwerken](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [*Uitgebreid* controle beleid voor servers maken of bijwerken](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [*Uitgebreide* controle beleid voor data base ophalen](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/get)
- [*Uitgebreid* controle beleid voor server ophalen](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

## <a id="subheading-9"></a>Azure SQL Server en database controle beheren met behulp van Azure Resource Manager sjablonen

U kunt Azure SQL database auditing beheren met behulp van [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) sjablonen, zoals in deze voor beelden wordt weer gegeven:

- [Een Azure SQL Server implementeren met controle ingeschakeld om audit logboeken naar een Azure Blob Storage-account te schrijven](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Een Azure SQL Server implementeren met controle ingeschakeld om audit logboeken te schrijven naar Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Een Azure SQL Server implementeren met controle ingeschakeld om audit logboeken te schrijven naar Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> De gekoppelde voor beelden bevinden zich in een externe open bare opslag plaats en worden als ' as is ' gegeven, zonder enige garantie, en worden niet ondersteund onder een ondersteunings programma/service van micro soft.

<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Manage Azure SQL Server and Database auditing using Azure PowerShell]: #subheading-7
[Manage SQL database auditing using REST API]: #subheading-8
[Manage Azure SQL Server and Database auditing using ARM templates]: #subheading-9

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_ssms_2.png
