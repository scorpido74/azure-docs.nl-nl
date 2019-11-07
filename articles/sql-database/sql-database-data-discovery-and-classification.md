---
title: '& Classificatie Azure SQL Database en SQL Data Warehouse voor gegevens detectie'
description: Classificatie van Azure SQL Database en gegevens detectie &
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 08/22/2019
ms.openlocfilehash: 9bcb8ce69a65892109702f0f0a409310945b6781
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690764"
---
# <a name="azure-sql-database-and-sql-data-warehouse-data-discovery--classification"></a>& Classificatie Azure SQL Database en SQL Data Warehouse voor gegevens detectie

Gegevens detectie & classificatie biedt geavanceerde mogelijkheden die zijn ingebouwd in Azure SQL Database voor het **detecteren**, **classificeren**, **labelen** & **beveiligen** van gevoelige gegevens in uw data bases.

Het detecteren en classificeren van uw meest gevoelige gegevens (zakelijke, financiële, gezondheids zorg, persoonlijke Identificeer bare gegevens (PII), enzovoort.) kan een draai functie in uw organisatie voor gegevens beveiliging van stature afspelen. Dit kan dienen als infra structuur voor:

- Helpt te voldoen aan de normen voor gegevens beveiliging en naleving van regelgeving.
- Diverse beveiligings scenario's, zoals controle (controle) en waarschuwingen over afwijkende toegang tot gevoelige gegevens.
- Het beheren van de toegang tot en het beveiligen van de beveiliging van data bases met zeer gevoelige gegevens.

Gegevens detectie & classificatie maakt deel uit van de aanbieding voor [geavanceerde gegevens beveiliging](sql-database-advanced-data-security.md) (ADS), een uniform pakket voor geavanceerde SQL-beveiligings mogelijkheden. gegevens detectie & classificatie kan worden gebruikt en beheerd via de centrale SQL ADS-Portal.

> [!NOTE]
> Dit document is gekoppeld aan Azure SQL Database en Azure SQL Data Warehouse. Voor het gemak wordt de term 'SQL Database' gebruikt wanneer er wordt verwezen naar zowel SQL Database als SQL Data Warehouse. Zie [SQL-gegevens detectie en-classificatie](https://go.microsoft.com/fwlink/?linkid=866999)voor SQL Server (on-premises).

## <a id="subheading-1"></a>Wat is de classificatie van gegevens detectie &

Gegevens detectie & classificatie introduceert een aantal geavanceerde services en nieuwe SQL-mogelijkheden, waarmee u een nieuw SQL Information Protection-paradigma kunt vormen dat is gericht op het beveiligen van de gegevens, niet alleen de Data Base:

- **Aanbevelingen voor detectie &**

  De classificatie-engine scant uw data base en identificeert kolommen die mogelijk gevoelige gegevens bevatten. Vervolgens krijgt u een eenvoudige manier om de juiste classificatie aanbevelingen te controleren en toe te passen via de Azure Portal.

- **Labels**

  Labels voor de gevoeligheids classificatie kunnen permanent worden gelabeld op kolommen met behulp van nieuwe classificatie meta gegevens kenmerken die zijn geïntroduceerd in de SQL-engine. Deze meta gegevens kunnen vervolgens worden gebruikt voor geavanceerde op gevoeligheid gebaseerde controle-en beveiligings scenario's.

- **Gevoeligheid van query resultaten instellen**

  De gevoeligheid van de resultatenset van de query wordt in realtime berekend voor controle doeleinden.

- **Zichtbaarheid**

  De classificatie status van de data base kan worden weer gegeven in een gedetailleerd dash board in de portal. Daarnaast kunt u een rapport (in Excel-indeling) downloaden dat moet worden gebruikt voor naleving & controle doeleinden, evenals andere behoeften.

## <a id="subheading-2"></a>& Label gevoelige kolommen detecteren, classificeren

In de volgende sectie worden de stappen beschreven voor het detecteren, classificeren en labelen van kolommen met gevoelige gegevens in uw data base, en het weer geven van de huidige classificatie status van uw data base en het exporteren van rapporten.

De classificatie bevat twee meta gegevens kenmerken:

- Labels: de belangrijkste classificatie kenmerken, die worden gebruikt voor het definiëren van het gevoeligheids niveau van de gegevens die zijn opgeslagen in de kolom.  
- Informatie typen: bieden een extra granulatie voor het type gegevens dat in de kolom wordt opgeslagen.

## <a name="define-and-customize-your-classification-taxonomy"></a>Uw classificatie taxonomie definiëren en aanpassen

De classificatie van SQL-gegevens detectie & wordt geleverd met een ingebouwde set van gevoeligheids labels en een ingebouwde set met gegevens typen en detectie logica. U hebt nu de mogelijkheid om deze taxonomie aan te passen en een set en classificatie van classificatie constructies te definiëren die specifiek zijn voor uw omgeving.

De definitie en aanpassing van uw classificatie taxonomie wordt uitgevoerd op één centrale locatie voor uw hele Azure-Tenant. Deze locatie bevindt zich in [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), als onderdeel van uw beveiligings beleid. Alleen iemand met beheerders rechten voor de hoofd beheer groep van de Tenant kan deze taak uitvoeren.

Als onderdeel van het beheer van Information Protection-beleid kunt u aangepaste labels definiëren, ze rangschikken en koppelen aan een geselecteerde set met gegevens typen. U kunt ook uw eigen aangepaste gegevens typen toevoegen en ze configureren met teken reeks patronen die worden toegevoegd aan de detectie logica voor het identificeren van dit type gegevens in uw data bases.
Meer informatie over het aanpassen en beheren van uw beleid in de [hand leiding voor Information Protections beleid](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Zodra het beleid voor de hele Tenant is gedefinieerd, kunt u door gaan met de classificatie van afzonderlijke data bases met behulp van uw aangepaste beleids regel.

## <a name="classify-your-sql-database"></a>Uw SQL Database classificeren

1. Ga naar de [Azure Portal](https://portal.azure.com).

2. Navigeer naar **geavanceerde gegevens beveiliging** onder de kop beveiliging in het deel venster Azure SQL database. Klik om geavanceerde gegevens beveiliging in te scha kelen en klik vervolgens op de kaart **gegevens detectie & classificatie** .

   ![Een Data Base scannen](./media/sql-data-discovery-and-classification/data_classification.png)

3. Het tabblad **overzicht** bevat een samen vatting van de huidige classificatie status van de data base, met inbegrip van een gedetailleerde lijst met alle geclassificeerde kolommen, die u ook kunt filteren om alleen specifieke schema onderdelen, gegevens typen en labels weer te geven. Als u nog geen kolommen hebt geclassificeerd, [gaat u verder met stap 5](#step-5).

   ![Samen vatting van huidige classificatie status](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Als u een rapport in Excel-indeling wilt downloaden, klikt u op de optie **exporteren** in het bovenste menu van het venster.

   ![Exporteren naar Excel](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png)

5. <a id="step-5"></a>Klik op het **tabblad classificatie** boven aan het venster om te beginnen met het classificeren van uw gegevens.

    ![U gegevens classificeren](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png)

6. De classificatie-engine scant uw Data Base op kolommen met mogelijk gevoelige gegevens en bevat een lijst met **Aanbevolen kolom classificaties**. Aanbevelingen voor classificaties bekijken en Toep assen:

   - Als u de lijst met aanbevolen kolom classificaties wilt weer geven, klikt u op het deel venster aanbevelingen onder aan het venster:

      ![Uw gegevens classificeren](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png)

   - Bekijk de lijst met aanbevelingen: Schakel het selectie vakje in de linkerkolom van de relevante rij in om een aanbeveling voor een specifieke kolom te accepteren. U kunt *alle aanbevelingen* ook markeren als geaccepteerd door het selectie vakje in de koptekst van de tabel met aanbevelingen in te scha kelen.

       ![Lijst met aanbevelingen controleren](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Als u de geselecteerde aanbevelingen wilt Toep assen, klikt u op de knop blauwe **geselecteerde aanbevelingen accepteren** .

      ![Aanbevelingen Toep assen](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png)

7. U kunt ook **hand matig kolommen classificeren** als een andere, of als aanvulling op de op aanbeveling gebaseerde classificatie:

   - Klik op **classificatie toevoegen** in het bovenste menu van het venster.

      ![Classificatie hand matig toevoegen](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png)

   - In het context venster dat wordt geopend, selecteert u het schema > tabel > kolom die u wilt classificeren, en het gegevens type en gevoeligheids label. Klik vervolgens op de knop **classificatie toevoegen** onder aan het context venster.

      ![Selecteer de kolom die u wilt classificeren](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Klik op **Opslaan** in het bovenste menu van het venster om de classificatie te volt ooien en de database kolommen persistent te labelen (tag) met de nieuwe meta gegevens van de classificatie.

   ![Opslaan](./media/sql-data-discovery-and-classification/10_data_classification_save.png)

## <a id="subheading-3"></a>Toegang tot gevoelige gegevens controleren

Een belang rijk aspect van het gegevens beveiligings model is de mogelijkheid om toegang tot gevoelige gegevens te bewaken. [Azure SQL database controle](sql-database-auditing.md) is uitgebreid met een nieuw veld in het audit logboek met de naam *data_sensitivity_information*, waarmee de gevoeligheids classificaties (labels) worden geregistreerd van de werkelijke gegevens die door de query zijn geretourneerd.

![Audit logboek](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a id="subheading-4"></a>Gegevens classificatie beheren met T-SQL

U kunt T-SQL gebruiken om kolom classificaties toe te voegen of te verwijderen, en alle classificaties voor de hele Data Base op te halen.

> [!NOTE]
> Wanneer u T-SQL gebruikt voor het beheren van labels, is er geen validatie waarbij labels die worden toegevoegd aan een kolom, bestaan in het organisatie Information Protection-beleid (de set labels die in de portal aanbevelingen worden weer gegeven). Daarom is het raadzaam om dit te valideren.

- De classificatie van een of meer kolommen toevoegen/bijwerken: [gevoeligheids classificatie toevoegen](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- De classificatie verwijderen uit een of meer kolommen: [gevoeligheids classificatie voor neerzetten](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Alle classificaties weer geven in de Data Base: [sys. sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="manage-classifications-using-rest-apis"></a>Classificaties beheren met rest-Api's

U kunt ook REST-Api's gebruiken om classificaties programmatisch te beheren. De gepubliceerde REST Api's ondersteunen de volgende bewerkingen:

- [Maken of bijwerken](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) : Hiermee wordt het gevoeligheids label van een bepaalde kolom gemaakt of bijgewerkt
- [Verwijderen](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) : Hiermee verwijdert u het gevoeligheids label van een bepaalde kolom
- [Aanbeveling uitschakelen](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation) : Hiermee worden de gevoeligheids aanbevelingen voor een bepaalde kolom uitgeschakeld
- [Aanbeveling inschakelen](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation) : Hiermee schakelt u gevoeligheids aanbevelingen in voor een bepaalde kolom (aanbevelingen zijn standaard ingeschakeld voor alle kolommen)
- [Get](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get) -haalt het gevoeligheids label van een bepaalde kolom op
- [Lijst huidige per data base](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase) : Hiermee worden de huidige gevoeligheids labels van een bepaalde data base opgehaald

- [Lijst aanbevolen door data base](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) : Hiermee wordt de aanbevolen gevoeligheids labels van een bepaalde data base opgehaald

## <a name="manage-data-discovery-and-classification-using-azure-powershell"></a>Gegevens detectie en-classificatie beheren met Azure PowerShell

U kunt Power shell gebruiken om alle aanbevolen kolommen op te halen in een Azure-SQL database en een beheerd exemplaar.

### <a name="powershell-cmdlets-for-azure-sql-database"></a>Power shell-cmdlets voor Azure SQL database

- [Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)

### <a name="powershell-cmdlets-for-managed-instance"></a>Power shell-cmdlets voor een beheerd exemplaar

- [Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)

## <a name="permissions"></a>Machtigingen

De volgende ingebouwde rollen kunnen de gegevens classificatie van een Azure SQL database: `Owner`, `Reader`, `Contributor`, `SQL Security Manager` en `User Access Administrator`lezen.

De volgende ingebouwde rollen kunnen de gegevens classificatie wijzigen van een Azure SQL database: `Owner`, `Contributor``SQL Security Manager`.

Meer informatie over [RBAC voor Azure-resources](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a id="subheading-5"></a>Volgende stappen

- Meer informatie over [geavanceerde gegevens beveiliging](sql-database-advanced-data-security.md).
- Overweeg het configureren van [Azure SQL database controle](sql-database-auditing.md) voor het bewaken en controleren van de toegang tot uw geclassificeerde gevoelige gegevens.

<!--Anchors-->
[SQL data discovery & classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Automated/Programmatic classification]: #subheading-4
[Next Steps]: #subheading-5
