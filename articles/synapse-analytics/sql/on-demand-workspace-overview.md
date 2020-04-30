---
title: SQL op aanvraag (preview-versie)
description: Meer informatie over Synapse SQL on-demand in azure Synapse Analytics.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 8d4de424d5d4d6da1ee80e04b35e63ae29df57c8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424907"
---
# <a name="sql-on-demand-preview-in-azure-synapse-analytics"></a>SQL on-demand (preview) in azure Synapse Analytics 

Elke Azure Synapse Analytics-werk ruimte (preview) wordt geleverd met SQL on-demand-eind punten (preview) die u kunt gebruiken om gegevens op te vragen in het Lake.

SQL on-demand is een query service over de gegevens in uw data Lake. Hiermee hebt u toegang tot uw gegevens via de volgende functies:
 
- Een bekende T-SQL-syntaxis om gegevens op te vragen, zonder dat u gegevens naar een gespecialiseerde Store hoeft te kopiëren of te laden. 
- Geïntegreerde connectiviteit via de T-SQL-interface waarmee u beschikt over een breed scala aan business intelligence en ad-hoc hulp middelen voor query's, met inbegrip van de populairste Stuur Programma's. 

SQL on-demand is een systeem voor gedistribueerde gegevens verwerking, gebouwd voor grote schaal van gegevens en reken kracht. Met SQL on-Demand kunt u uw Big data binnen enkele seconden tot minuten analyseren, afhankelijk van de werk belasting. Dankzij de ingebouwde fout tolerantie voor het uitvoeren van query's biedt het systeem hoge betrouw baarheid en succes tarieven, zelfs voor langlopende query's waarbij grote gegevens sets worden gebruikt.

SQL on-demand is serverloze, daarom is er geen infra structuur om te installeren of clusters te onderhouden. Er wordt een standaard eindpunt voor deze service gegeven binnen elke Azure Synapse-werk ruimte, zodat u een query kunt uitvoeren op gegevens zodra de werk ruimte is gemaakt. Er worden geen kosten in rekening gebracht voor de gereserveerde resources, u wordt alleen gefactureerd voor de gegevens die worden gescand door query's die u uitvoert, daarom is dit model een echt model voor betalen per gebruik.  

Als u Spark gebruikt in uw gegevens pijplijn, kunt u bij het voorbereiden van gegevens, het opschonen of verrijken [een query uitvoeren op alle Spark-tabellen](develop-storage-files-spark-tables.md) die u in het proces hebt gemaakt, rechtstreeks vanuit SQL op aanvraag. Gebruik [persoonlijke koppeling](../security/how-to-connect-to-workspace-with-private-links.md) om uw SQL on-demand-eind punt naar uw [beheerde werk ruimte VNet](../security/synapse-workspace-managed-vnet.md)te brengen.  

## <a name="who-is-sql-on-demand-for"></a>Wie is SQL op aanvraag voor

Als u gegevens in de data Lake wilt verkennen, inzicht wilt krijgen in de gegevens en uw bestaande gegevens transformatie pijplijn wilt optimaliseren, kunt u profiteren van het gebruik van SQL op aanvraag. Het is geschikt voor de volgende scenario's:

- Basis detectie en-ontdekking: snelle reden voor de gegevens in verschillende indelingen (Parquet, CSV, JSON) in uw data Lake, zodat u kunt plannen hoe u inzichten uit het bestand haalt.
- Logisch data warehouse: bieden een relationele samen vatting boven op onbewerkte gegevens, zonder gegevens te verplaatsen en te transformeren, waardoor altijd actuele weer gave van uw gegevens mogelijk is.
- Gegevens transformatie: eenvoudige, schaal bare en beste manier om gegevens in het Lake te transformeren met behulp van T-SQL, zodat deze kan worden ingevoerd in BI en andere hulpprogram ma's of worden geladen in een relationele gegevens opslag (Synapse SQL-data bases, Azure SQL Database, enzovoort).

Andere professionele rollen kunnen profiteren van SQL on-demand:

- Data engineers kunnen het Lake verkennen, gegevens transformeren en voorbereiden met behulp van deze service en de gegevens transformatie pijplijnen vereenvoudigen. Raadpleeg deze [zelf studie](tutorial-data-analyst.md)voor meer informatie.
- Gegevens wetenschappers kunnen snel de oorzaak zijn van de inhoud en de structuur van de gegevens in het Lake, met functies zoals OPENROWSET en automatisch schema deinterferentie.
- Gegevens analisten kunnen [gegevens en Spark-tabellen](develop-storage-files-spark-tables.md) die zijn gemaakt door gegevens wetenschappers of gegevens technici verkennen met behulp van vertrouwde T-SQL-taal of hun favoriete hulp middelen, die verbinding kunnen maken met SQL op aanvraag.
- Met BI-professionals kunnen snel [Power bi rapporten worden gemaakt op gegevens in de tabellen Lake](tutorial-connect-power-bi-desktop.md) en Spark.

## <a name="what-do-i-need-to-do-to-start-using-it"></a>Wat moet ik doen om aan de slag te gaan met het gebruik ervan?

SQL op aanvraag-eind punt wordt in elke Azure Synapse-werk ruimte vermeld. U kunt een werk ruimte maken en direct query's uitvoeren op gegevens met hulp middelen waarmee u bekend bent.

## <a name="client-tools"></a>Client hulpprogramma's

Met SQL on-demand kunnen bestaande SQL ad-hoc query's en business intelligence-hulpprogram ma's in de data Lake worden getikt. Omdat het een vertrouwde T-SQL-syntaxis biedt, kan een hulp programma waarmee SQL-aanbiedingen voor de TDS-verbinding kunnen worden [gemaakt, verbinding maken met en Synapse SQL](connect-overview.md) op aanvraag. U kunt verbinding maken met Azure Data Studio en ad-hoc query's uitvoeren of verbinding maken met Power BI om inzicht te krijgen in een paar minuten.

## <a name="is-full-t-sql-supported"></a>Wordt de volledige T-SQL ondersteund?

SQL on-Demand biedt T-SQL-query's voor een surface area, wat in sommige aspecten iets verbeterd/uitgebreid is om te voorzien in de ervaring met het uitvoeren van query's op semi-gestructureerde en ongestructureerde gegevens. Bovendien worden sommige aspecten van de T-SQL-taal niet ondersteund vanwege het ontwerp van SQL on-demand, omdat DML-functionaliteit op dit moment niet wordt ondersteund.

- De werk belasting kan worden georganiseerd met behulp van bekende concepten:
- Data bases: SQL op aanvraag-eind punt kan meerdere data bases bevatten.
- Schema's: in een Data Base kunnen er een of meer eigendoms groepen voor objecten worden aangeduid met de naam schemas.
- Weergaven
- Externe bronnen – gegevens bronnen, bestands indelingen en tabellen

Beveiliging kan worden afgedwongen met:

- Aanmeldingen en gebruikers
- Referenties voor het beheren van de toegang tot opslag accounts
- Machtigingen per object niveau toekennen, weigeren en intrekken
- Integratie van Azure Active Directory

Ondersteunde T-SQL:

- Volledige [selectie](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Surface Area wordt ondersteund, met inbegrip van een meerderheid van SQL-functies
- CETAS: EXTERNE TABEL MAKEN ALS SELECTEREN
- DDL-instructies met betrekking tot weer gaven en beveiliging

SQL on-demand heeft geen lokale opslag, alleen meta gegevens objecten worden opgeslagen in data bases. Daarom wordt T-SQL met betrekking tot de volgende concepten niet ondersteund:

- Tabellen
- Triggers
- Gerealiseerde weer gaven
- Andere DDL-instructies dan die van weer gaven en beveiliging
- DML-instructies

### <a name="extensions"></a>Uitbreidingen

SQL on demand breidt de bestaande [OPENrowset](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) -functie uit door de volgende mogelijkheden toe te voegen om een goede ervaring te bieden bij het uitvoeren van query's in bestanden in data Lake:

[Meerdere bestanden of mappen opvragen](develop-storage-files-overview.md#query-multiple-files-or-folders)

[PARQUET-bestands indeling](develop-storage-files-overview.md#parquet-file-format)

[Aanvullende opties voor het werken met gescheiden tekst (veld Terminator, rij-Terminator, escape-teken)](develop-storage-files-overview.md#additional-options-for-working-with-delimited-text)

[Een gekozen subset kolommen lezen](develop-storage-files-overview.md#read-a-chosen-subset-of-columns)

[Schema-deinterferentie](develop-storage-files-overview.md#schema-inference)

[de functie filename](develop-storage-files-overview.md#filename-function)

[de functie filepath](develop-storage-files-overview.md#filepath-function)

[Werken met complexe typen en geneste of herhaalde gegevens structuren](develop-storage-files-overview.md#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="security"></a>Beveiliging

SQL on Demand biedt mechanismen om de toegang tot uw gegevens te beveiligen.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integratie en meervoudige verificatie

Met SQL on-Demand kunt u de identiteiten van database gebruikers en andere micro soft-services centraal beheren met [Azure Active Directory-integratie](../../sql-database/sql-database-Azure AD-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Deze mogelijkheid vereenvoudigt het beheer van machtigingen en verbetert de beveiliging. Azure Active Directory (Azure AD) ondersteunt [multi-factor Authentication](../../sql-database/sql-database-ssms-mfa-authentication-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (MFA) om gegevens-en toepassings beveiliging te verbeteren tijdens het ondersteunen van een proces voor eenmalige aanmelding.

#### <a name="authentication"></a>Verificatie

SQL on-demand-verificatie verwijst naar hoe gebruikers hun identiteit bewijzen wanneer ze verbinding maken met het eind punt. Twee soorten verificatie worden ondersteund:

- **SQL-verificatie**

  Deze verificatie methode maakt gebruik van een gebruikers naam en wacht woord.

- **Azure Active Directory-verificatie**:

  Deze verificatie methode maakt gebruik van identiteiten die worden beheerd door Azure Active Directory. Voor Azure AD-gebruikers kan multi-factor Authentication worden ingeschakeld. Gebruik [waar mogelijk](/sql/relational-databases/security/choose-an-authentication-mode?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)Active Directory verificatie (geïntegreerde beveiliging).

#### <a name="authorization"></a>Autorisatie

Autorisatie verwijst naar wat een gebruiker kan doen binnen een SQL on-demand-data base en wordt beheerd door de databaserol lidmaatschappen en object machtigingen van uw gebruikers account.

Als SQL-verificatie wordt gebruikt, bestaat de SQL-gebruiker alleen in SQL on-demand en zijn de machtigingen van toepassing op de objecten in SQL op aanvraag. Toegang tot Beveilig bare objecten in andere services (zoals Azure Storage) kan niet rechtstreeks aan SQL-gebruikers worden verleend omdat deze alleen binnen het bereik van SQL on-demand bestaat. De SQL-gebruiker moet een van de [ondersteunde autorisatie typen](develop-storage-files-storage-access-control.md#supported-storage-authorization-types) gebruiken voor toegang tot de bestanden.

Als Azure AD-verificatie wordt gebruikt, kan een gebruiker zich aanmelden bij SQL on-demand en andere services, zoals Azure Storage, en kan deze machtigingen verlenen aan de gebruiker van Azure AD.

### <a name="access-to-storage-accounts"></a>Toegang tot opslag accounts

Een gebruiker die is aangemeld bij de SQL-service op aanvraag moet gemachtigd zijn om toegang te krijgen tot de bestanden in Azure Storage en om deze op te vragen. SQL on-demand ondersteunt de volgende autorisatie typen:

- **Shared Access Signature (SAS)** biedt gedelegeerde toegang tot resources in het opslag account. Met een SAS kunt u clients toegang verlenen tot bronnen in een opslag account, zonder dat u account sleutels hoeft te delen. Een SAS geeft u gedetailleerde controle over het type toegang dat u verleent aan clients met de SAS: geldigheids interval, verleende machtigingen, acceptabel IP-adres bereik, acceptabel Protocol (https/http).

- De **gebruikers-id** (ook wel pass-through genoemd) is een autorisatie type waarbij de identiteit van de Azure AD-gebruiker die is aangemeld bij SQL on-demand wordt gebruikt om toegang tot de gegevens te verlenen. Voordat de gegevens worden geopend, moet Azure Storage beheerder machtigingen verlenen aan de Azure AD-gebruiker voor toegang tot de gegevens. Dit autorisatie type maakt gebruik van de Azure AD-gebruiker die op aanvraag is aangemeld bij SQL. dit wordt daarom niet ondersteund voor SQL-gebruikers typen.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over eindpunt verbinding en query bestanden vindt u in de volgende artikelen: 
- [Verbinding maken met uw eind punt](connect-overview.md)
- [Query's uitvoeren op uw bestanden](develop-storage-files-overview.md)
