---
title: SQL on-demand (voorbeeld)
description: Meer informatie over Synapse SQL on-demand in Azure Synapse Analytics.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 8d4de424d5d4d6da1ee80e04b35e63ae29df57c8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424907"
---
# <a name="sql-on-demand-preview-in-azure-synapse-analytics"></a>SQL on-demand (preview) in Azure Synapse Analytics 

Elke Azure Synapse Analytics-werkruimte (preview) wordt geleverd met SQL on-demand (preview)-eindpunten die u gebruiken om gegevens in het meer op te vragen.

SQL on-demand is een queryservice over de gegevens in uw gegevensmeer. Het stelt u in staat om toegang te krijgen tot uw gegevens via de volgende functionaliteiten:
 
- Een bekende T-SQL-syntaxis om gegevens op te vragen zonder gegevens te hoeven kopiëren of laden naar een gespecialiseerde winkel. 
- Geïntegreerde connectiviteit via de T-SQL-interface die een breed scala aan business intelligence- en ad-hoc querytools biedt, waaronder de populairste stuurprogramma's. 

SQL on-demand is een gedistribueerd gegevensverwerkingssysteem, gebouwd voor grote schaal van gegevens en compute. SQL on-demand stelt u in staat om uw Big Data in enkele seconden tot minuten te analyseren, afhankelijk van de werkbelasting. Dankzij de ingebouwde fouttolerantie voor queryuitvoering biedt het systeem hoge betrouwbaarheids- en slagingspercentages, zelfs voor langlopende query's met grote gegevenssets.

SQL on-demand is serverless, dus er is geen infrastructuur om in te stellen of clusters te onderhouden. Een standaardeindpunt voor deze service wordt geleverd binnen elke Azure Synapse-werkruimte, zodat u gegevens opvragen zodra de werkruimte is gemaakt. Er zijn geen kosten in rekening gebracht voor gereserveerde resources, u wordt alleen in rekening gebracht voor de gegevens die worden gescand door query's die u uitvoert, vandaar dat dit model een echt pay-per-use-model is.  

Als u Spark in uw gegevenspijplijn gebruikt voor gegevensvoorbereiding, reiniging of verrijking, u [alle Spark-tabellen die](develop-storage-files-spark-tables.md) u in het proces hebt gemaakt, rechtstreeks vanuit SQL on-demand opvragen. Gebruik [Private Link](../security/how-to-connect-to-workspace-with-private-links.md) om uw SQL on-demand eindpunt naar uw [beheerde werkruimte VNet](../security/synapse-workspace-managed-vnet.md)te brengen.  

## <a name="who-is-sql-on-demand-for"></a>Wie is SQL on-demand voor

Als u gegevens in het datalake wilt verkennen, er inzichten uit wilt halen of uw bestaande pijplijn voor gegevenstransformatie wilt optimaliseren, u profiteren van het gebruik van SQL on-demand. Het is geschikt voor de volgende scenario's:

- Basisdetectie en -verkenning - Snel redeneren over de gegevens in verschillende formaten (Parket, CSV, JSON) in uw datalake, zodat u plannen hoe u er inzichten uit halen.
- Logisch datawarehouse – Bied een relationele abstractie bovenop ruwe of ongelijksoortige gegevens zonder gegevens te verplaatsen en te transformeren, waardoor uw gegevens altijd up-to-date kunnen worden bekeken.
- Gegevenstransformatie - Eenvoudige, schaalbare en performante manier om gegevens in het meer te transformeren met Behulp van T-SQL, zodat deze kan worden ingevoerd naar BI en andere hulpprogramma's, of kan worden geladen in een relationele gegevensopslag (Synapse SQL-databases, Azure SQL Database, enz.).

Verschillende professionele rollen kunnen profiteren van SQL on-demand:

- Data Engineers kunnen het meer verkennen, gegevens transformeren en voorbereiden met behulp van deze service en hun pijplijnen voor gegevenstransformatie vereenvoudigen. Voor meer informatie, check deze [tutorial](tutorial-data-analyst.md).
- Gegevens Wetenschappers kunnen snel redeneren over de inhoud en structuur van de gegevens in het meer, dankzij functies zoals OPENROWSET en automatische schema-gevolgtrekking.
- Data-analisten kunnen [gegevens en Spark-tabellen verkennen](develop-storage-files-spark-tables.md) die zijn gemaakt door Gegevenswetenschappers of Gegevenstechnici met behulp van bekende T-SQL-taal of hun favoriete tools, die on-demand verbinding kunnen maken met SQL.
- BI-professionals kunnen snel [Power BI-rapporten maken bovenop gegevens in de meer-](tutorial-connect-power-bi-desktop.md) en Spark-tabellen.

## <a name="what-do-i-need-to-do-to-start-using-it"></a>Wat moet ik doen om het te gaan gebruiken?

SQL on-demand eindpunt wordt geleverd binnen elke Azure Synapse-werkruimte. U een werkruimte maken en direct gegevens opvragen met behulp van tools die u kent.

## <a name="client-tools"></a>Clienthulpprogramma's

SQL on-demand stelt bestaande SQL ad-hoc query's en business intelligence-tools in staat om gebruik te maken van het datalake. Omdat het bekende T-SQL-syntaxis biedt, kan elk hulpprogramma dat geschikt is om SQL-aanbiedingen voor TDS-verbinding [vast te stellen, verbinding maken met en synapse SQL](connect-overview.md) on-demand querymen. U verbinding maken met Azure Data Studio en ad-hocquery's uitvoeren of verbinding maken met Power BI om binnen enkele minuten inzicht te krijgen.

## <a name="is-full-t-sql-supported"></a>Wordt volledige T-SQL ondersteund?

SQL on-demand biedt T-SQL-query's, die in sommige aspecten enigszins is verbeterd/uitgebreid om rekening te houden met ervaringen rond het opvragen van semi-gestructureerde en ongestructureerde gegevens. Bovendien worden sommige aspecten van de T-SQL-taal niet ondersteund vanwege het ontwerp van SQL on-demand, als voorbeeld wordt DML-functionaliteit momenteel niet ondersteund.

- Werkbelasting kan worden georganiseerd met behulp van bekende concepten:
- Databases - SQL on-demand eindpunt kan meerdere databases hebben.
- Schema's - In een database kunnen er een of meerdere objecteigendomsgroepen zijn die schema's worden genoemd.
- Weergaven
- Externe bronnen – gegevensbronnen, bestandsindelingen en tabellen

Beveiliging kan worden afgedwongen met:

- Aanmeldingen en gebruikers
- Referenties om de toegang tot opslagaccounts te beheren
- Machtigingen per objectniveau verlenen, weigeren en intrekken
- Integratie van Azure Active Directory

Ondersteunde T-SQL:

- Volledig [SELECT-oppervlak](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) wordt ondersteund, inclusief een meerderheid van de SQL-functies
- CETAS - EXTERNE TABEL MAKEN ALS SELECTEERT
- DDL-verklaringen met betrekking tot weergaven en beveiliging alleen

SQL on-demand heeft geen lokale opslag, alleen metadata-objecten worden opgeslagen in databases. Daarom wordt T-SQL met betrekking tot de volgende concepten niet ondersteund:

- Tabellen
- Triggers
- Gematerialiseerde weergaven
- Andere DDL-verklaringen dan die met betrekking tot weergaven en beveiliging
- DML-instructies

### <a name="extensions"></a>Extensies

Sql on-demand breidt de bestaande [OPENROWSET-functie](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) uit door de volgende mogelijkheden toe te voegen om een soepele ervaring op te stellen voor het opvragen van gegevens die zich in bestanden in het datalake bevinden:

[Meerdere bestanden of mappen opvragen](develop-storage-files-overview.md#query-multiple-files-or-folders)

[PARQUET-bestandsindeling](develop-storage-files-overview.md#parquet-file-format)

[Extra opties voor het werken met afgebakende tekst (veldterminator, rijterminator, escape char)](develop-storage-files-overview.md#additional-options-for-working-with-delimited-text)

[Een gekozen subset van kolommen lezen](develop-storage-files-overview.md#read-a-chosen-subset-of-columns)

[Schema-gevolgtrekking](develop-storage-files-overview.md#schema-inference)

[bestandsnaam, functie](develop-storage-files-overview.md#filename-function)

[filepath, functie](develop-storage-files-overview.md#filepath-function)

[Werken met complexe typen en geneste of herhaalde gegevensstructuren](develop-storage-files-overview.md#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="security"></a>Beveiliging

SQL on-demand biedt mechanismen om de toegang tot uw gegevens te beveiligen.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integratie en meervoudige verificatie

SQL on-demand stelt u in staat om de identiteit van databasegebruikers en andere Microsoft-services centraal te beheren met [Azure Active Directory-integratie.](../../sql-database/sql-database-Azure AD-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) Deze mogelijkheid vereenvoudigt het beheer van machtigingen en verbetert de beveiliging. Azure Active Directory (Azure AD) ondersteunt [multi-factor authenticatie](../../sql-database/sql-database-ssms-mfa-authentication-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (MFA) om de gegevens- en toepassingsbeveiliging te verhogen en tegelijkertijd één aanmeldingsproces te ondersteunen.

#### <a name="authentication"></a>Verificatie

SQL on-demand authenticatie verwijst naar hoe gebruikers hun identiteit bewijzen wanneer ze verbinding maken met het eindpunt. Er worden twee soorten verificatie ondersteund:

- **SQL-verificatie**

  Deze verificatiemethode gebruikt een gebruikersnaam en wachtwoord.

- **Azure Active Directory-verificatie:**

  Deze verificatiemethode maakt gebruik van identiteiten die worden beheerd door Azure Active Directory. Voor Azure AD-gebruikers kan multi-factor authenticatie worden ingeschakeld. Gebruik active directory-verificatie (geïntegreerde beveiliging) [waar mogelijk.](/sql/relational-databases/security/choose-an-authentication-mode?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

#### <a name="authorization"></a>Autorisatie

Autorisatie verwijst naar wat een gebruiker kan doen binnen een SQL on-demand database en wordt beheerd door de databaserollidmaatschappen van uw gebruikersaccount en machtigingen op objectniveau.

Als SQL-verificatie wordt gebruikt, bestaat de SQL-gebruiker alleen in SQL on-demand en worden machtigingen beperkt tot de objecten in SQL on-demand. Toegang tot securable objecten in andere services (zoals Azure Storage) kan niet rechtstreeks aan SQL-gebruiker worden verleend, omdat deze alleen bestaan in het bereik van SQL on-demand. De SQL-gebruiker moet een van de [ondersteunde autorisatietypen](develop-storage-files-storage-access-control.md#supported-storage-authorization-types) gebruiken om toegang te krijgen tot de bestanden.

Als Azure AD-verificatie wordt gebruikt, kan een gebruiker zich aanmelden bij SQL on-demand en andere services, zoals Azure Storage, en kan hij machtigingen verlenen aan de Azure AD-gebruiker.

### <a name="access-to-storage-accounts"></a>Toegang tot opslagaccounts

Een gebruiker die is aangemeld bij de SQL on-demand-service moet gemachtigd zijn om toegang te krijgen tot de bestanden in Azure Storage en deze op te vragen. SQL on-demand ondersteunt de volgende autorisatietypen:

- **Shared access signature (SAS)** biedt gedelegeerde toegang tot bronnen in het opslagaccount. Met een SAS u klanten toegang verlenen tot bronnen in het opslagaccount, zonder accountsleutels te delen. Een SAS geeft u gedetailleerde controle over het type toegang dat u verleent aan clients die het SAS hebben: geldigheidsinterval, verleende machtigingen, acceptabel IP-adresbereik, aanvaardbaar protocol (https/http).

- **Gebruikersidentiteit** (ook wel 'doorgeefinformatie' genoemd) is een autorisatietype waarbij de identiteit van de Azure AD-gebruiker die is aangemeld bij SQL on-demand wordt gebruikt om de toegang tot de gegevens te autoriseren. Voordat u toegang krijgt tot de gegevens, moet azure-opslagbeheerder machtigingen verlenen aan azure AD-gebruiker voor toegang tot de gegevens. Dit autorisatietype maakt gebruik van de Azure AD-gebruiker die is aangemeld bij SQL on-demand, daarom wordt deze niet ondersteund voor SQL-gebruikerstypen.

## <a name="next-steps"></a>Volgende stappen
Aanvullende informatie over endpoint-verbinding en querybestanden vindt u in de volgende artikelen: 
- [Verbinding maken met uw eindpunt](connect-overview.md)
- [Uw bestanden opvragen](develop-storage-files-overview.md)
