---
title: Controle van beheerde exemplaren
description: Meer informatie over hoe u aan de slag met Azure SQL Database managed instance auditing met T-SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/27/2020
ms.openlocfilehash: 405ac27fad3c24d3064f11476f452ad00abb9b02
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387764"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Aan de slag met controleren van beheerd exemplaar voor Azure SQL Database

[Beheerde instantiecontrole](sql-database-managed-instance.md) houdt databasegebeurtenissen bij en schrijft deze naar een controlelogboek in uw Azure-opslagaccount. De controlefunctie biedt ook deze mogelijkheden:

- Naleving van wet- en regelgeving, inzicht in activiteiten in de database en in de afwijkingen en discrepanties die kunnen wijzen op problemen voor het bedrijf of vermoedelijke schendingen van de beveiliging.
- Mogelijk maken en faciliteren van nalevingsstandaarden, hoewel dit geen garantie biedt voor naleving. Zie het [Azure Trust Center,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) waar u de meest recente lijst met SQL Database-nalevingscertificeringen vinden, voor meer informatie over Azure-programma's die naleving van standaarden ondersteunen.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Controle voor uw server instellen op Azure-opslag

In de volgende sectie wordt de configuratie van controle op uw beheerde instantie beschreven.

1. Ga naar de [Azure-portal.](https://portal.azure.com)
2. Maak een Azure **Storage-container** waarin controlelogboeken worden opgeslagen.

   1. Navigeer naar de Azure Storage waar u uw controlelogboeken wilt opslaan.

      > [!IMPORTANT]
      > Gebruik een opslagaccount in dezelfde regio als de beheerde instantie om lezen/schrijven tussen regio's te voorkomen.

   1. Ga in het opslagaccount naar **Overzicht** en klik op **Blobs**.

      ![Azure Blob-widget](./media/sql-managed-instance-auditing/1_blobs_widget.png)

   1. Klik in het bovenste menu op **+ Container** om een nieuwe container te maken.

      ![Pictogram Blobcontainer maken](./media/sql-managed-instance-auditing/2_create_container_button.png)

   1. Geef een **containernaam**op, stel openbaar toegangsniveau in op **Privé**en klik op **OK**.

      ![Blobcontainerconfiguratie maken](./media/sql-managed-instance-auditing/3_create_container_config.png)
  > [!IMPORTANT]
  > Klanten die een onveranderlijklogboekarchief willen configureren voor hun controlegebeurtenissen op server- of databaseniveau, moeten de [instructies van Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes) volgen (Zorg ervoor dat u Extra toevoegen **toestaan** hebt geselecteerd wanneer u de onveranderlijke blobopslag configureert)
  
3. Na het maken van de container voor de Audit-logboeken zijn er twee manieren om deze te configureren als doel voor de controlelogboeken: [het gebruik van T-SQL](#blobtsql) of [het gebruik van de SQL Server Management Studio (SSMS) UI:](#blobssms)

   - <a id="blobtsql"></a>Blogopslag configureren voor controlelogboeken met T-SQL:

     1. Klik in de lijst met containers op de nieuw gemaakte container en klik vervolgens op **Containereigenschappen**.

        ![Knop Eigenschappen van Blob-container](./media/sql-managed-instance-auditing/4_container_properties_button.png)

     1. Kopieer de container-URL door op het kopieerpictogram te klikken en sla de URL (bijvoorbeeld in Kladblok) op voor toekomstig gebruik. De URL-indeling van de container moet`https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![URL voor blobcontainerkopiëren](./media/sql-managed-instance-auditing/5_container_copy_name.png)

     1. Een Azure Storage **SAS-token** genereren om beheerde instantiecontroletoegangsrechten toe te kennen aan het opslagaccount:

        - Navigeer naar het Azure Storage-account waar u de container in de vorige stap hebt gemaakt.

        - Klik op **Handtekening voor gedeelde toegang** in het menu Opslaginstellingen.

          ![Pictogram handtekening voor gedeelde toegang in het menu Opslaginstellingen](./media/sql-managed-instance-auditing/6_storage_settings_menu.png)

        - Configureer de SAS als volgt:

          - **Toegestane services:** Blob

          - **Begindatum**: om problemen in de tijdzone te voorkomen, wordt aanbevolen om de datum van gisteren te gebruiken

          - **Einddatum:** kies de datum waarop dit SAS-token verloopt

            > [!NOTE]
            > Verleng het token na afloop om controlefouten te voorkomen.

          - Klik op **SAS genereren**.
            
            ![SAS-configuratie](./media/sql-managed-instance-auditing/7_sas_configure.png)

        - Nadat u op SAS genereren hebt geklikt, wordt het SAS-token onderaan weergegeven. Kopieer het token door op het kopieerpictogram te klikken en op te slaan (bijvoorbeeld in Kladblok) voor toekomstig gebruik.

          ![SAS-token kopiëren](./media/sql-managed-instance-auditing/8_sas_copy.png)

          > [!IMPORTANT]
          > Verwijder het vraagteken ("?") teken vanaf het begin van het token.

     1. Maak verbinding met uw beheerde instantie via SQL Server Management Studio (SSMS) of een ander ondersteund hulpprogramma.

     1. Voer de volgende T-SQL-instructie uit om **een nieuwe referentie** te maken met de CONTAINER-URL en SAS-token die u in de vorige stappen hebt gemaakt:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Voer de volgende T-SQL-instructie uit om een nieuwe serveraudit te maken (kies uw eigen auditnaam, gebruik de container-URL die u in de vorige stappen hebt gemaakt). Als dit `RETENTION_DAYS` niet is opgegeven, is standaardinstelling 0 (onbeperkte retentie):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
        GO
        ```

        1. Doorgaan met [het maken van een serverauditspecificatie of databasecontrolespecificatie](#createspec)

   - <a id="blobssms"></a>Blob-opslag configureren voor controlelogboeken met de SQL Server Management Studio (SSMS) 18 (Voorbeeld):

     1. Maak verbinding met de beheerde instantie met sql server beheerstudio (SSMS) Gebruikersinterface.

     1. Vouw de hoofdnotitie van de Object Explorer uit.

     1. Vouw het **beveiligingsknooppunt** uit, klik met de rechtermuisknop op het knooppunt **Audits** en klik op 'Nieuwe controle':

        ![Beveiligings- en controleknooppunt uitbreiden](./media/sql-managed-instance-auditing/10_mi_SSMS_new_audit.png)

     1. Zorg ervoor dat 'URL' is geselecteerd in **de plaats Controle** en klik op **Bladeren:**

        ![Blader door Azure Storage](./media/sql-managed-instance-auditing/11_mi_SSMS_audit_browse.png)

     1. (Optioneel) Meld u aan bij uw Azure-account:

        ![Aanmelden bij Azure](./media/sql-managed-instance-auditing/12_mi_SSMS_sign_in_to_azure.png)

     1. Selecteer een abonnement, een opslagaccount en blobcontainer in de vervolgkeuzevakken of maak uw eigen container door op **Maken**te klikken. Klik op **OK**na afloop:

        ![Azure-abonnement, opslagaccount en blobcontainer selecteren](./media/sql-managed-instance-auditing/13_mi_SSMS_select_subscription_account_container.png)

     1. Klik **op OK** in het dialoogvenster Controle maken.

4. <a id="createspec"></a>Nadat u de Blob-container hebt geconfigureerd als doel voor de controlelogboeken, maakt en schakelt u een serverauditspecificatie of databaseauditspecificatie in zoals u dat voor SQL Server zou doen:

   - [T-SQL-handleiding voor servercontrole-controle maken](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [T-SQL-handleiding voor databasecontrole-controle maken](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

5. Schakel de serveraudit in die u in stap 3 hebt gemaakt:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Meer informatie:

- [Verschillen tussen afzonderlijke databases, elastische pools en beheerde instanties in Azure SQL-database en databases in SQL Server controleren](#auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server)
- [SERVERAUDIT MAKEN](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [CONTROLE VAN DE SERVER WIJZIGEN](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hub-or-azure-monitor-logs"></a>Controle voor uw server instellen op gebeurtenishub- of Azure-monitorlogboeken

Controlelogboeken van een beheerde instantie kunnen worden verzonden naar even hubs of Azure Monitor-logboeken. In deze sectie wordt beschreven hoe u dit configureert:

1. Navigeer in de [Azure Portal](https://portal.azure.com/) naar de beheerde instantie.

2. Klik op **Diagnostische instellingen**.

3. Klik op **Diagnostische gegevens inschakelen**. Als diagnostische gegevens al zijn ingeschakeld, wordt de *diagnostische instelling +Toevoegen* in plaats daarvan weergegeven.

4. Selecteer **SQLSecurityAuditEvents** in de lijst met logboeken.

5. Selecteer een bestemming voor de controlegebeurtenissen - Gebeurtenishub, Azure Monitor-logboeken of beide. Configureer voor elk doel de vereiste parameters (bijvoorbeeld Log Analytics-werkruimte).

6. Klik op **Opslaan**.

    ![Diagnostische instellingen configureren](./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png)

7. Maak verbinding met de beheerde instantie met **SQL Server Management Studio (SSMS)** of een andere ondersteunde client.

8. Voer de volgende T-SQL-instructie uit om een serveraudit te maken:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Maak en schakel een serverauditspecificatie of databaseauditspecificatie in zoals u dat voor SQL Server zou doen:

   - [T-SQL-handleiding voor servercontrole-controle maken](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [T-SQL-handleiding voor databasecontrole-controle maken](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Schakel de serveraudit in die in stap 8 is gemaakt:
 
    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Controlelogboeken consumeren

### <a name="consume-logs-stored-in-azure-storage"></a>Logboeken gebruiken die zijn opgeslagen in Azure Storage

Er zijn verschillende methoden die u gebruiken om blob-controlelogboeken weer te geven.

- Gebruik de `sys.fn_get_audit_file` systeemfunctie (T-SQL) om de controleloggegevens in tabelindeling te retourneren. Zie de [documentatie sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql)voor meer informatie over het gebruik van deze functie.

- U controlelogboeken verkennen met behulp van een hulpprogramma zoals [Azure Storage Explorer.](https://azure.microsoft.com/features/storage-explorer/) In Azure-opslag worden controlelogboeken opgeslagen als een verzameling blobbestanden in een container die is gedefinieerd om de controlelogboeken op te slaan. Zie de [naslagverwijzing](https://go.microsoft.com/fwlink/?linkid=829599)voor de opslagmap, naamgevingsconventies en logboekindeling voor meer informatie over de hiërarchie van de opslagmap, naamgevingsconventies en logboekindeling.

- Raadpleeg voor een volledige lijst met verbruiksmethoden voor controlelogboeken de controle van de [SQL-database.](sql-database-auditing.md)

### <a name="consume-logs-stored-in-event-hub"></a>Logboeken gebruiken die zijn opgeslagen in gebeurtenishub

Als u controlelogboekgegevens van gebeurtenishub wilt gebruiken, moet u een stream instellen om gebeurtenissen te consumeren en naar een doel te schrijven. Zie Azure Event Hubs Documentation voor meer informatie.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Logboeken gebruiken en analyseren die zijn opgeslagen in Azure Monitor-logboeken

Als controlelogboeken zijn geschreven naar Azure Monitor-logboeken, zijn deze beschikbaar in de werkruimte Log Analytics, waar u geavanceerde zoekopdrachten op de controlegegevens uitvoeren. Navigeer als uitgangspunt naar de werkruimte Log Analytics en klik onder *sectie Algemeen* `search "SQLSecurityAuditEvents"` op *Logboeken* en voer een eenvoudige query in, zoals: om de controlelogboeken weer te geven.  

Azure Monitor-logboeken bieden u realtime operationele inzichten met behulp van geïntegreerde zoek- en aangepaste dashboards om gemakkelijk miljoenen records te analyseren op al uw workloads en servers. Zie [Azure Monitor-logboeken zoekreferentie](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)voor aanvullende nuttige informatie over Azure Monitor-logboeken .

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server"></a>Verschillen tussen databases in Azure SQL-database en databases in SQL Server controleren

De belangrijkste verschillen tussen controle in databases in Azure SQL Database en databases in SQL Server zijn:

- Met de beheerde optie voor het implementeren van instance `.xel` in Azure SQL Database werkt auditing op serverniveau en worden logboekbestanden opgeslagen in Azure Blob-opslag.
- In sql server on-premises / virtuele machines werkt audit op serverniveau, maar worden gebeurtenissen opgeslagen in bestandssysteem/windows-gebeurtenislogboeken.

XEvent-controle in beheerde instantie ondersteunt Azure Blob-opslagdoelen. Bestands- en windowslogboeken **worden niet ondersteund.**

De belangrijkste verschillen `CREATE AUDIT` in de syntaxis voor controle naar Azure Blob-opslag zijn:

- Er wordt `TO URL` een nieuwe syntaxis opgegeven en hiermee `.xel` u de URL opgeven van de Azure blob-opslagcontainer waar de bestanden worden geplaatst.
- Er wordt `TO EXTERNAL MONITOR` een nieuwe syntaxis verstrekt om zelfs hub- en Azure-monitorlogboekendoelen in te schakelen.
- De `TO FILE` syntaxis **wordt niet ondersteund** omdat SQL Database geen toegang heeft tot Windows-bestandsshares.
- De optie Afsluiten wordt **niet ondersteund.**
- `queue_delay`van 0 wordt **niet ondersteund**.

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg voor een volledige lijst met verbruiksmethoden voor controlelogboeken de controle van de [SQL-database.](sql-database-auditing.md)
- Zie het [Azure Trust Center,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) waar u de meest recente lijst met SQL Database-nalevingscertificeringen vinden, voor meer informatie over Azure-programma's die naleving van standaarden ondersteunen.

<!--Image references-->









