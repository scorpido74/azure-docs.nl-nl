---
title: Gegevensdetectie en -classificatie
description: Gegevensdetectie & classificatie voor Azure SQL Database en Azure Synapse Analytics
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
titleSuffix: Azure SQL Database and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: 98b21969ce86f84b20fbe78dbdf3002769ef65f5
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387866"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Gegevensdetectie & classificatie voor Azure SQL Database en Azure Synapse Analytics

Gegevensdetectie & classificatie biedt geavanceerde mogelijkheden die zijn ingebouwd in Azure SQL Database voor **het ontdekken,** **classificeren,** **labelen van** & het**labelen** van de gevoelige gegevens in uw databases.

Het ontdekken en classificeren van uw meest gevoelige gegevens (business, financial, healthcare, personally identificeerbare gegevens (PII), enzovoort.) kan een centrale rol spelen in uw organisatieinformatiebescherming. Dit kan dienen als infrastructuur om:

- Te helpen voldoen aan standaarden op het gebied van gegevensbescherming en aan vereisten voor naleving van regelgeving.
- Verschillende beveiligingsscenario's, zoals monitoring (auditing) en waarschuwingen over afwijkende toegang tot gevoelige gegevens.
- De toegang tot en verbetering van de beveiliging van databases met uiterst gevoelige gegevens te beheren.

Data Discovery & Classificatie maakt deel uit van het ADS-aanbod [(Advanced Data Security),](sql-database-advanced-data-security.md) een uniform pakket voor geavanceerde SQL-beveiligingsmogelijkheden. gegevensdetectie & classificatie kan worden geopend en beheerd via de centrale SQL ADS-portal.

> [!NOTE]
> Dit document heeft betrekking op Azure SQL Database en Azure Synapse. Voor de eenvoud wordt SQL Database gebruikt bij het verwijzen naar zowel SQL Database als Azure Synapse. Zie SQL Data Discovery [and Classification](https://go.microsoft.com/fwlink/?linkid=866999)voor SQL Server (on-premises).

## <a name="what-is-data-discovery--classification"></a><a id="subheading-1"></a>Wat is gegevensdetectie & classificatie

Data Discovery & Classificatie introduceert een reeks geavanceerde services en nieuwe SQL-mogelijkheden, die een nieuw SQL Information Protection-paradigma vormen dat gericht is op het beschermen van de gegevens, niet alleen de database:

- **Aanbevelingen voor &**

  De classificatieengine scant uw database en identificeert kolommen met mogelijk gevoelige gegevens. Vervolgens kunt u in Azure Portal op een gemakkelijke manier de juiste classificatieaanbevelingen controleren en toepassen.

- **Labeling**

  Gevoeligheidsclassificatielabels kunnen voortdurend worden getagd op kolommen met behulp van nieuwe classificatiemetagegevenskenmerken die in de SQL Engine zijn geïntroduceerd. Deze metagegevens kunnen daarna worden gebruikt voor geavanceerde audit- en beschermingscenario’s op basis van gevoeligheid.

- **Gevoeligheid voor queryresultaatset**

  De gevoeligheid van de queryresultatenset wordt in realtime berekend voor controledoeleinden.

- **Zichtbaarheid**

  De status van de databaseclassificatie kan worden weergegeven in een gedetailleerd dashboard in de portal. Daarnaast kunt u een rapport (in Excel-indeling) downloaden dat kan worden gebruikt voor naleving en controle en voor andere behoeften.

## <a name="discover-classify--label-sensitive-columns"></a><a id="subheading-2"></a>& labelgevoelige kolommen ontdekken, classificeren

In de volgende sectie worden de stappen beschreven voor het ontdekken, classificeren en labelen van kolommen met gevoelige gegevens in uw database, evenals het bekijken van de huidige classificatiestatus van uw database en het exporteren van rapporten.

De classificatie bevat twee metagegevenskenmerken:

- Labels : de belangrijkste classificatiekenmerken die worden gebruikt om het gevoeligheidsniveau te definiëren van de gegevens die in de kolom zijn opgeslagen.  
- Informatietypen : geef extra granulariteit aan het type gegevens dat in de kolom is opgeslagen.

## <a name="define-and-customize-your-classification-taxonomy"></a>Uw classificatietaxonomie definiëren en aanpassen

Data Discovery & Classificatie wordt geleverd met een ingebouwde set gevoeligheidslabels en een ingebouwde set informatietypen en detectielogica. U hebt nu de mogelijkheid om deze taxonomie aan te passen en een set en rangschikking van classificatieconstructies specifiek voor uw omgeving te definiëren.

Definitie en aanpassing van uw classificatietaxonomie gebeurt op één centrale plaats voor uw gehele Azure-tenant. Die locatie bevindt zich in [Azure Security Center,](https://docs.microsoft.com/azure/security-center/security-center-intro)als onderdeel van uw beveiligingsbeleid. Alleen iemand met beheerdersrechten op de hoofdbeheergroep Tenant kan deze taak uitvoeren.

Als onderdeel van het beleidsbeheer informatiebescherming u aangepaste labels definiëren, rangschikken en koppelen aan een geselecteerde set informatietypen. U kunt ook uw eigen aangepaste informatietypen toevoegen en deze configureren met tekenreekspatronen, die worden toegevoegd aan de detectielogica voor het identificeren van dit type gegevens in uw databases.
Meer informatie over het aanpassen en beheren van uw beleid vindt u in de [handleiding informatiebescherming.](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409)

Zodra het tenantbrede beleid is gedefinieerd, u doorgaan met de classificatie van afzonderlijke databases met behulp van uw aangepaste beleid.

## <a name="classify-your-sql-database"></a>Uw SQL-database classificeren

1. Ga naar de [Azure-portal.](https://portal.azure.com)

2. Navigeer naar **Geavanceerde gegevensbeveiliging** onder het kopje Beveiliging in het deelvenster Azure SQL Database. Klik hierom geavanceerde gegevensbeveiliging in te schakelen en klik vervolgens op de **& classificatiekaart gegevensdetectie.**

   ![Een database scannen](./media/sql-data-discovery-and-classification/data_classification.png)

3. Het tabblad **Overzicht** bevat een overzicht van de huidige classificatiestatus van de database, inclusief een gedetailleerde lijst met alle geclassificeerde kolommen, die u ook filteren om alleen specifieke schemaonderdelen, informatietypen en labels weer te geven. Als u nog geen kolommen hebt geclassificeerd, [gaat u naar stap 5](#step-5).

   ![Samenvatting van de huidige classificatiestatus](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Als u een rapport wilt downloaden in Excel-indeling, klikt u op de optie **Exporteren** in het bovenste menu van het venster.

5. <a id="step-5"></a>Als u wilt beginnen met het classificeren van uw gegevens, klikt u op het **tabblad Classificatie** boven aan het venster.

6. De classificatieengine scant uw database op kolommen met mogelijk gevoelige gegevens en biedt een lijst met **aanbevolen kolomclassificaties.** Ga als volgt te werk om classificatieaanbevelingen te bekijken en toe te passen:

   - Als u de lijst met aanbevolen kolomclassificaties wilt bekijken, klikt u op het deelvenster Aanbevelingen onder aan het venster

   - Bekijk de lijst met aanbevelingen om een aanbeveling voor een specifieke kolom te accepteren, schakel het selectievakje in de linkerkolom van de desbetreffende rij in. U ook *alle aanbevelingen* markeren als geaccepteerd door het selectievakje in de koptekst van de aanbevelingentabel in te schakelen.

       ![Aanbevelingslijst controleren](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Als u de geselecteerde aanbevelingen wilt toepassen, klikt u op de blauwe knop **Geselecteerde aanbevelingen accepteren.**

7. U kolommen ook **handmatig classificeren** als alternatief, of bovendien voor de classificatie op basis van aanbevelingen:

   - Klik op **Classificatie toevoegen** in het bovenste menu van het venster.

   - Selecteer in het contextvenster dat wordt geopend het schema > tabel > kolom die u wilt classificeren en het informatietype en het gevoeligheidslabel. Klik vervolgens op de blauwe **knop Classificatie toevoegen** onder aan het contextvenster.

      ![Kolom selecteren om te classificeren](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Als u uw classificatie wilt voltooien en de databasekolommen met de nieuwe classificatiemetagegevens voortdurend wilt labelen (taggen), klikt u op **Opslaan** in het bovenste menu van het venster.

## <a name="auditing-access-to-sensitive-data"></a><a id="subheading-3"></a>Toegang tot gevoelige gegevens controleren

Een belangrijk aspect van het paradigma van informatiebescherming is de mogelijkheid om de toegang tot gevoelige gegevens te controleren. [Azure SQL Database Auditing](sql-database-auditing.md) is uitgebreid met een nieuw veld in het controlelogboek genaamd *data_sensitivity_information*, dat de gevoeligheidsclassificaties (labels) van de werkelijke gegevens registreert die door de query zijn geretourneerd.

![Controlelogboek](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="subheading-4"></a>Machtigingen

De volgende ingebouwde rollen kunnen de gegevensclassificatie van `Owner` `Reader`een `Contributor` `SQL Security Manager` Azure `User Access Administrator`SQL-database lezen: , , en .

De volgende ingebouwde rollen kunnen de gegevensclassificatie van `Owner` `Contributor`een `SQL Security Manager`Azure SQL-database wijzigen: , ,

Meer informatie over [RBAC voor Azure-bronnen](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a name="manage-classifications"></a><a id="subheading-5"></a>Classificaties beheren

# <a name="t-sql"></a>[T-SQL](#tab/azure-t-sql)
U T-SQL gebruiken om kolomclassificaties toe te voegen/verwijderen en alle classificaties voor de hele database op te halen.

> [!NOTE]
> Wanneer u T-SQL gebruikt om labels te beheren, is er geen validatie dat labels die aan een kolom zijn toegevoegd, bestaan in het beleid voor organisatie-informatiebescherming (de set labels die worden weergegeven in de portalaanbevelingen). Het is dus aan u om dit te valideren.

- De classificatie van een of meer kolommen toevoegen/bijwerken: [GEVOELIGHEIDsclassificatie TOEVOEGEN](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- De classificatie verwijderen uit een of meer kolommen: [CLASSIFICATIE DROPGEVOELIGHEID](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Bekijk alle classificaties in de database: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

# <a name="rest-apis"></a>[Rest API's](#tab/azure-rest-api)
U REST API's gebruiken om classificaties en aanbevelingen programmatisch te beheren. De gepubliceerde REST API's ondersteunen de volgende bewerkingen:

- [Het](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) gevoeligheidslabel van een bepaalde kolom maken of bijwerken : het gevoeligheidslabel van een bepaalde kolom maken of bijwerken
- [Verwijderen](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) - Verwijdert het gevoeligheidslabel van een bepaalde kolom
- [Aanbeveling uitschakelen](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation) - Schakelt aanbevelingen voor gevoeligheid op een bepaalde kolom uit
- [Aanbeveling inschakelen](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation) : gevoeligheidsaanbevelingen voor een bepaalde kolom inschakelen (aanbevelingen zijn standaard ingeschakeld op alle kolommen)
- [Get](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get) - Krijgt het gevoeligheidslabel van een bepaalde kolom
- [Lijst Huidig op database](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase) - Krijgt de huidige gevoeligheidslabels van een bepaalde database
- [Lijst aanbevolen door database](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) - Krijgt de aanbevolen gevoeligheidslabels van een bepaalde database

# <a name="powershell-cmdlet"></a>[PowerShell-cmdlet](#tab/azure-powelshell)
U PowerShell gebruiken om classificaties en aanbevelingen voor Azure SQL Database en Managed Instance te beheren.

### <a name="powershell-cmdlet-for-azure-sql-database"></a>PowerShell-cmdlet voor Azure SQL-database
- [Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Classificatie van set-AzSqlDatabaseSensitivity](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Classificatie van gegevens over de gevoeligheid van AzSqlDatabase verwijderen](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityAanbeveling](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Aanbeveling voor inschakeling azSqlDatabaSesensitivite](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Aanbeveling voor gevoeligheid voor azsqldatabase uitschakelen](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

### <a name="powershell-cmdlets-for-managed-instance"></a>PowerShell-cmdlets voor beheerde instantie
- [Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Classificatie van set-AzSqlInstanceDatabaseSensitivity](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Classificatie van gegevens over de gevoeligheid van AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Inschakelbare aanbeveling voor gegevensgevoeligheid van AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Aanbeveling voor gevoeligheid voor azsqlinstancedatabase uitschakelen](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

---

## <a name="next-steps"></a><a id="subheading-6"></a>Volgende stappen

- Meer informatie over [geavanceerde gegevensbeveiliging](sql-database-advanced-data-security.md).
- Overweeg [Azure SQL Database Auditing](sql-database-auditing.md) te configureren voor het bewaken en controleren van toegang tot uw geclassificeerde gevoelige gegevens.
- Zie Gegevensdetectie & classificatie voor een YouTube-presentatie met gegevensdetectie & classificatie: [ontdekken, classificeren, labelen & SQL-gegevens beveiligen | Blootgestelde gegevens](https://www.youtube.com/watch?v=itVi9bkJUNc).

<!--Anchors-->
[What is data discovery & classification]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Permissions]: #subheading-4
[Manage classifications]: #subheading-5
[Next Steps]: #subheading-6
