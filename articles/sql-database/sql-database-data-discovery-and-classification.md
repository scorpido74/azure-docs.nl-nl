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
ms.openlocfilehash: 599b2a280e386e49eb114f448f55b17ed7e823d7
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616759"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Gegevensdetectie & classificatie voor Azure SQL Database en Azure Synapse Analytics

&-classificatie voor gegevensdetectie is ingebouwd in Azure SQL Database. Het biedt geavanceerde mogelijkheden voor het ontdekken, classificeren, labelen en rapporteren van de gevoelige gegevens in uw databases.

Uw meest gevoelige gegevens kunnen zakelijke, financiële, gezondheidszorg- of persoonlijke gegevens bevatten. Het ontdekken en classificeren van deze gegevens kan een centrale rol spelen in de informatiebeschermingsaanpak van uw organisatie. Dit kan dienen als infrastructuur om:

- Helpen om te voldoen aan de normen voor gegevensprivacy en vereisten voor naleving van de regelgeving.
- Verschillende beveiligingsscenario's, zoals monitoring (auditing) en waarschuwingen over afwijkende toegang tot gevoelige gegevens.
- Controle van de toegang tot en verharding van de beveiliging van databases die zeer gevoelige gegevens bevatten.

Data Discovery &-classificatie maakt deel uit van het [Advanced Data Security-aanbod,](sql-database-advanced-data-security.md) een uniform pakket voor geavanceerde SQL-beveiligingsmogelijkheden. U datadetectie & classificatie openen en beheren via het centrale **SQL Advanced Data Security-gedeelte** van de Azure-portal.

> [!NOTE]
> Dit artikel heeft betrekking op Azure SQL Database en Azure Synapse Analytics. Voor de eenvoud gebruiken we SQL *Database* hier om te verwijzen naar zowel SQL Database als Azure Synapse. Zie [SQL Data Discovery and Classification](https://go.microsoft.com/fwlink/?linkid=866999)voor informatie over SQL Server (on-premises).

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>Wat is datadiscovery-& classificatie?

Data Discovery & classificatie introduceert een reeks geavanceerde services en nieuwe SQL Database-mogelijkheden. Het vormt een nieuw informatiebeveiligingsparadigma voor SQL Database, gericht op het beschermen van de gegevens en niet alleen op de database. Het paradigma omvat:

- **Ontdekking en aanbevelingen:** De classificatieengine scant uw database en identificeert kolommen die mogelijk gevoelige gegevens bevatten. Het biedt u vervolgens een eenvoudige manier om aanbevolen classificatie via de Azure-portal te bekijken en toe te passen.

- **Etikettering:** U labels voor gevoeligheidsclassificatie voortdurend toepassen op kolommen met behulp van nieuwe metagegevenskenmerken die zijn toegevoegd aan de SQL-databaseengine. Deze metagegevens kunnen vervolgens worden gebruikt voor geavanceerde, op gevoeligheid gebaseerde controle- en beveiligingsscenario's.

- **Gevoeligheid voor resultaatseten van query's:** De gevoeligheid van een queryresultaatset wordt in realtime berekend voor controledoeleinden.

- **Zichtbaarheid:** U de status van databaseclassificatie weergeven in een gedetailleerd dashboard in de Azure-portal. U ook een rapport downloaden in Excel-indeling om te gebruiken voor nalevings- en controledoeleinden en andere behoeften.

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>Gevoelige kolommen ontdekken, classificeren en labelen

In deze sectie worden de stappen beschreven voor:

- Kolommen met gevoelige gegevens in uw database ontdekken, classificeren en labelen.
- De huidige classificatiestatus van uw database weergeven en rapporten exporteren.

De classificatie bevat twee metagegevenskenmerken:

- **Labels:** de belangrijkste classificatiekenmerken die worden gebruikt om het gevoeligheidsniveau te definiëren van de gegevens die in de kolom zijn opgeslagen.  
- **Informatietypen:** kenmerken die meer gedetailleerde informatie geven over het type gegevens dat in de kolom is opgeslagen.

### <a name="define-and-customize-your-classification-taxonomy"></a>Uw classificatietaxonomie definiëren en aanpassen

Data Discovery & Classificatie wordt geleverd met een ingebouwde set gevoeligheidslabels en een ingebouwde set informatietypen en detectielogica. U kunt deze taxonomie zelf aanpassen en een reeks en rangschikking van classificatieconstructies definiëren voor uw omgeving.

U definieert en past uw classificatietaxonomie aan op één centrale plaats voor uw gehele Azure-organisatie. Die locatie bevindt zich in [Azure Security Center,](https://docs.microsoft.com/azure/security-center/security-center-intro)als onderdeel van uw beveiligingsbeleid. Alleen iemand met beheerdersrechten op de rootmanagementgroep van de organisatie kan deze taak uitvoeren.

Als onderdeel van beleidsbeheer voor SQL-informatiebescherming u aangepaste labels definiëren, rangschikken en koppelen aan een geselecteerde set informatietypen. U ook uw eigen aangepaste informatietypen toevoegen en deze configureren met tekenreekspatronen. De patronen worden toegevoegd aan de detectielogica voor het identificeren van dit type gegevens in uw databases.

Meer informatie over het aanpassen en beheren van uw beleid vindt u in de handleiding van het [SQL Information Protection-beleid.](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409)

Nadat het beleid voor de hele organisatie is gedefinieerd, u afzonderlijke databases blijven classificeren met behulp van uw aangepaste beleid.

### <a name="classify-your-sql-database"></a>Uw SQL-database classificeren

1. Ga naar de [Azure-portal.](https://portal.azure.com)

2. Ga naar **Geavanceerde gegevensbeveiliging** onder het kopje **Beveiliging** in het deelvenster Azure SQL Database. Selecteer **Geavanceerde gegevensbeveiliging**en selecteer vervolgens de **&-classificatiekaart gegevensdetectie.**

   ![Deelvenster Geavanceerde gegevensbeveiliging in Azure-portal](./media/sql-data-discovery-and-classification/data_classification.png)

3. Op de pagina **Gegevensdetectie & classificatie** bevat het tabblad **Overzicht** een overzicht van de huidige classificatiestatus van de database. Het overzicht bevat een gedetailleerde lijst met alle geclassificeerde kolommen, die u ook filteren om alleen specifieke schemaonderdelen, informatietypen en labels weer te geven. Als u nog geen kolommen hebt geclassificeerd, [gaat u naar stap 5](#step-5).

   ![Samenvatting van de huidige classificatiestatus](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Als u een rapport wilt downloaden in Excel-indeling, selecteert u **Exporteren** in het bovenste menu van het deelvenster.

5. <a id="step-5"></a>Als u wilt beginnen met het classificeren van uw gegevens, selecteert u het tabblad **Classificatie** op de pagina **Gegevensdetectie & classificatie.**

    De classificatieengine scant uw database op kolommen met mogelijk gevoelige gegevens en biedt een lijst met aanbevolen kolomclassificaties.

6. Classificatieaanbevelingen weergeven en toepassen:

   - Als u de lijst met aanbevolen kolomclassificaties wilt weergeven, selecteert u het deelvenster Aanbevelingen onder aan het deelvenster.

   - Als u een aanbeveling voor een bepaalde kolom wilt accepteren, schakelt u het selectievakje in de linkerkolom van de desbetreffende rij in. Als u alle aanbevelingen als geaccepteerd wilt markeren, schakelt u het selectievakje linksliggen in de koptekst van de aanbevelingentabel in.

       ![Uit de lijst met classificatieaanbevelingen bekijken en selecteren](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Als u de geselecteerde aanbevelingen wilt toepassen, selecteert **u Geselecteerde aanbevelingen accepteren**.

7. U kolommen ook handmatig classificeren, als alternatief of naast de op aanbevelingen gebaseerde classificatie:

   1. Selecteer **Classificatie toevoegen** in het bovenste menu van het deelvenster.

   1. Selecteer in het contextvenster dat wordt geopend het schema, de tabel en de kolom die u wilt classificeren en het informatietype en het gevoeligheidslabel.

   1. Selecteer **Classificatie toevoegen** onder aan het contextvenster.

      ![Een kolom selecteren om te classificeren](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Als u uw classificatie wilt voltooien en de databasekolommen met de nieuwe classificatiemetagegevens permanent wilt **labelen** (tag) met de nieuwe classificatiemetagegevens, selecteert u Opslaan in het bovenste menu van het venster.

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Toegang tot gevoelige gegevens controleren

Een belangrijk aspect van het informatiebeschermingsparadigma is de mogelijkheid om de toegang tot gevoelige gegevens te controleren. [Azure SQL Database Auditing](sql-database-auditing.md) is verbeterd om een nieuw `data_sensitivity_information`veld op te nemen in het controlelogboek genaamd . In dit veld worden de gevoeligheidsclassificaties (labels) van de gegevens die door een query zijn geretourneerd, bijeen. Hier volgt een voorbeeld:

![Controlelogboek](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Machtigingen

Deze ingebouwde rollen kunnen de gegevensclassificatie van een Azure SQL-database lezen:

- Eigenaar
- Lezer
- Inzender
- SQL-beveiligingsbeheer
- Beheerder van gebruikerstoegang

Deze ingebouwde rollen kunnen de gegevensclassificatie van een Azure SQL-database wijzigen:

- Eigenaar
- Inzender
- SQL-beveiligingsbeheer

Meer informatie over op rollen gebaseerde machtigingen in [RBAC voor Azure-bronnen](https://docs.microsoft.com/azure/role-based-access-control/overview).

## <a name="manage-classifications"></a><a id="manage-classification"></a>Classificaties beheren

U T-SQL, een REST API of PowerShell gebruiken om classificaties te beheren.

### <a name="use-t-sql"></a>T-SQL gebruiken

U T-SQL gebruiken om kolomclassificaties toe te voegen of te verwijderen en om alle classificaties voor de hele database op te halen.

> [!NOTE]
> Wanneer u T-SQL gebruikt om labels te beheren, is er geen validatie dat labels die u aan een kolom toevoegt, bestaan in het informatiebeveiligingsbeleid van de organisatie (de set labels die worden weergegeven in de portalaanbevelingen). Dus, het is aan jou om dit te valideren.

Zie de volgende verwijzingen voor informatie over het gebruik van T-SQL voor classificaties:

- De classificatie van een of meer kolommen toevoegen of bijwerken: [GEVOELIGHEIDsclassificatie toevoegen](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- De classificatie verwijderen uit een of meer kolommen: [CLASSIFICATIE DROPGEVOELIGHEID](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Om alle classificaties in de database te bekijken: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-the-rest-api"></a>De REST-API gebruiken

U de REST API gebruiken om classificaties en aanbevelingen programmatisch te beheren. De gepubliceerde REST API ondersteunt de volgende bewerkingen:

- [Maken of bijwerken:](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate)hiermee wordt het gevoeligheidslabel van de opgegeven kolom gemaakt of bijgewerkt.
- [Delete:](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete)Hiermee verwijdert u het gevoeligheidslabel van de opgegeven kolom.
- [Aanbeveling uitschakelen:](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation)schakelt aanbevelingen voor gevoeligheid op de opgegeven kolom uit.
- [Aanbeveling inschakelen:](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation)Hiermee worden gevoeligheidsaanbevelingen voor de opgegeven kolom ingeschakeld. (Aanbevelingen zijn standaard ingeschakeld op alle kolommen.)
- [Get:](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get)Krijgt het gevoeligheidslabel van de opgegeven kolom.
- [Lijst huidig op database:](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase)de huidige gevoeligheidslabels van de opgegeven database worden weergegeven.
- [Lijst aanbevolen op database:](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase)wordt de aanbevolen gevoeligheidslabels van de opgegeven database weergegeven.

### <a name="use-powershell-cmdlets"></a>PowerShell-cmdlets gebruiken
U PowerShell gebruiken om classificaties en aanbevelingen voor Azure SQL Database en beheerde exemplaren te beheren.

#### <a name="powershell-cmdlets-for-sql-database"></a>PowerShell-cmdlets voor SQL-database

- [Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Classificatie van set-AzSqlDatabaseSensitivity](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Classificatie van gegevens over de gevoeligheid van AzSqlDatabase verwijderen](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityAanbeveling](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Aanbeveling voor inschakeling azSqlDatabaSesensitivite](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Aanbeveling voor gevoeligheid voor azsqldatabase uitschakelen](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-managed-instances"></a>PowerShell-cmdlets voor beheerde exemplaren

- [Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Classificatie van set-AzSqlInstanceDatabaseSensitivity](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Classificatie van gegevens over de gevoeligheid van AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Inschakelbare aanbeveling voor gegevensgevoeligheid van AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Aanbeveling voor gevoeligheid voor azsqlinstancedatabase uitschakelen](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)


## <a name="next-steps"></a><a id="next-steps"></a>Volgende stappen

- Meer informatie over [geavanceerde gegevensbeveiliging](sql-database-advanced-data-security.md).
- Overweeg [Azure SQL Database Auditing](sql-database-auditing.md) te configureren voor het bewaken en controleren van toegang tot uw geclassificeerde gevoelige gegevens.
- Zie Detectie, classificeren, labelen & sql-gegevens beveiligen voor een presentatie met gegevensdetectie en -classificatie : zie [SQL-gegevens ontdekken, classificeren, labelen & beveiligen | Blootgestelde gegevens](https://www.youtube.com/watch?v=itVi9bkJUNc).
