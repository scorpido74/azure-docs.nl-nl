---
title: Azure Data Lake Analytics beheren met behulp van de Azure-portal
description: In dit artikel wordt beschreven hoe u de Azure-portal gebruiken om GegevensLake Analytics-accounts, gegevensbronnen, gebruikers & taken te beheren.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 8a6b7cb3fd45e17b84519efcaa826b569083e156
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265699"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-portal"></a>Azure Data Lake Analytics beheren met de Azure-portal
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

In dit artikel wordt beschreven hoe u Azure Data Lake Analytics-accounts, gegevensbronnen, gebruikers en taken beheert met behulp van de Azure-portal.


<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Data Lake Analytics-accounts beheren

### <a name="create-an-account"></a>Een account maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Klik op Een > resource**Intelligence + analytics** > **Data Lake Analytics** **maken.**
3. Selecteer waarden voor de volgende items: 
   1. **Naam:** de naam van het Data Lake Analytics-account.
   2. **Abonnement:** het Azure-abonnement dat voor het account wordt gebruikt.
   3. **Resourcegroep:** de Azure-brongroep waarin het account kan worden gemaakt. 
   4. **Locatie:** het Azure-datacenter voor het Data Lake Analytics-account. 
   5. **Data Lake Store**: de standaardwinkel die moet worden gebruikt voor het Data Lake Analytics-account. Het Azure Data Lake Store-account en het Data Lake Analytics-account moeten zich op dezelfde locatie bevinden.
4. Klik **op Maken**. 

### <a name="delete-a-data-lake-analytics-account"></a>Een Data Lake Analytics-account verwijderen

Voordat u een Data Lake Analytics-account verwijdert, verwijdert u het standaard Data Lake Store-account.

1. Ga in de Azure-portal naar uw Data Lake Analytics-account.
2. Klik **op Verwijderen**.
3. Typ de accountnaam.
4. Klik **op Verwijderen**.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Gegevensbronnen beheren

Data Lake Analytics ondersteunt de volgende gegevensbronnen:

* Data Lake Store
* Azure Storage

U Gegevensverkenner gebruiken om door gegevensbronnen te bladeren en basisbewerkingen voor bestandsbeheer uit te voeren. 

### <a name="add-a-data-source"></a>Een gegevensbron toevoegen

1. Ga in de Azure-portal naar uw Data Lake Analytics-account.
2. Klik **op Gegevensbronnen**.
3. Klik **op Gegevensbron toevoegen**.
    
   * Als u een Data Lake Store-account wilt toevoegen, hebt u de accountnaam en toegang tot het account nodig om het account te kunnen opvragen.
   * Als u Azure Blob-opslag wilt toevoegen, hebt u het opslagaccount en de accountsleutel nodig. Om ze te vinden, ga je naar het opslagaccount in de portal.

## <a name="set-up-firewall-rules"></a>Firewallregels instellen

U Data Lake Analytics gebruiken om de toegang tot uw Data Lake Analytics-account op netwerkniveau verder te vergrendelen. U een firewall inschakelen, een IP-adres opgeven of een IP-adresbereik definiëren voor uw vertrouwde clients. Nadat u deze metingen hebt ingeschakeld, kunnen alleen clients met de IP-adressen binnen het gedefinieerde bereik verbinding maken met de winkel.

Als andere Azure-services, zoals Azure Data Factory of VM's, verbinding maken met het Data Lake Analytics-account, controleert u of **Azure Services toestaan** is **ingeschakeld.** 

### <a name="set-up-a-firewall-rule"></a>Een firewallregel instellen

1. Ga in de Azure-portal naar uw Data Lake Analytics-account.
2. Klik in het menu aan de linkerkant op **Firewall**.

## <a name="add-a-new-user"></a>Een nieuwe gebruiker toevoegen

U de **wizard Gebruiker toevoegen** gebruiken om eenvoudig nieuwe Data Lake-gebruikers in te richten.

1. Ga in de Azure-portal naar uw Data Lake Analytics-account.
2. Klik aan de linkerkant onder **Aan de slag**op Wizard Gebruiker **toevoegen**.
3. Selecteer een gebruiker en klik op **Selecteren**.
4. Selecteer een rol en klik op **Selecteren**. Als u een nieuwe ontwikkelaar wilt instellen om Azure Data Lake te gebruiken, selecteert u de rol **Data Lake Analytics Developer.**
5. Selecteer de toegangscontrolelijsten (ACL's) voor de U-SQL-databases. Als u tevreden bent met uw keuzes, klikt u op **Selecteren**.
6. Selecteer de ACL's voor bestanden. Wijzig voor het standaardarchief de ACL's niet voor de hoofdmap '/' en voor de/systeemmap. Klik **op Selecteren**.
7. Controleer alle geselecteerde wijzigingen en klik op **Uitvoeren**.
8. Klik op **Gereed**.

## <a name="manage-role-based-access-control"></a>Toegangsbeheer op basis van rollen beheren

Net als andere Azure-services u RBAC (Role-Based Access Control) gebruiken om te bepalen hoe gebruikers met de service omgaan.

De standaard RBAC-rollen hebben de volgende mogelijkheden:
* **Eigenaar**: Kan taken indienen, taken controleren, taken van elke gebruiker annuleren en het account configureren.
* **Bijdrager:** Kan taken indienen, taken controleren, taken van elke gebruiker annuleren en het account configureren.
* **Reader**: Kan taken controleren.

Gebruik de rol Data Lake Analytics Developer om U-SQL-ontwikkelaars in staat te stellen de Data Lake Analytics-service te gebruiken. U de rol Data Lake Analytics Developer gebruiken om:
* Vacatures indienen.
* Monitor de status van de taak en de voortgang van taken die door een gebruiker zijn ingediend.
* Bekijk de U-SQL-scripts van taken die door een gebruiker zijn ingediend.
* Annuleer alleen je eigen vacatures.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Gebruikers of beveiligingsgroepen toevoegen aan een Data Lake Analytics-account

1. Ga in de Azure-portal naar uw Data Lake Analytics-account.
2. Klik **op Toegangsbesturingselement (IAM)** > **Roltoewijzing toevoegen**.
3. Selecteer een rol.
4. Voeg een gebruiker toe.
5. Klik op **OK**.

>[!NOTE]
>Als een gebruiker of een beveiligingsgroep taken moet indienen, heeft hij of zij ook toestemming nodig voor het winkelaccount. Zie [Beveiligde gegevens die zijn opgeslagen in Data Lake Store](../data-lake-store/data-lake-store-secure-data.md)voor meer informatie.
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Taken beheren

### <a name="submit-a-job"></a>Een taak indienen

1. Ga in de Azure-portal naar uw Data Lake Analytics-account.

2. Klik op **Nieuwe taak**. Configureer voor elke taak:

    1. **Naam van**de taak: de naam van de taak.
    2. **Prioriteit**: Lagere getallen hebben een hogere prioriteit. Als twee taken in de wachtrij staan, wordt deze eerst uitgevoerd met een lagere prioriteitswaarde.
    3. **Parallelisme**: Het maximum aantal rekenprocessen dat moet worden gereserveerd voor deze taak.

3. Klik op **Taak verzenden**.

### <a name="monitor-jobs"></a>Taken controleren

1. Ga in de Azure-portal naar uw Data Lake Analytics-account.
2. Klik **op Alle taken weergeven**. Een lijst van alle actieve en recent voltooide taken in het account wordt weergegeven.
3. Klik optioneel op **Filter** om u te helpen de taken te vinden op **tijdbereik,** **taaknaam**en **auteurswaarden.** 

### <a name="monitoring-pipeline-jobs"></a>Pijplijntaken controleren
Taken die deel uitmaken van een pijplijn werken samen, meestal opeenvolgend, om een specifiek scenario te realiseren. U bijvoorbeeld een pijplijn hebben die het gebruik van klanten reinigt, uitheert, transformeert, verzamelt voor klantinzichten. Pijplijntaken worden geïdentificeerd met de eigenschap 'Pijplijn' toen de taak werd ingediend. Taken die zijn gepland met ADF V2, worden deze eigenschap automatisch gevuld. 

Ga als lid van het Nieuwe Programma een lijst met U-SQL-taken weergeven die deel uitmaken van pijplijnen: 

1. Ga in de Azure-portal naar uw Data Lake Analytics-accounts.
2. Klik **op Job Insights**. Het tabblad 'Alle taken' wordt standaard weergegeven, met een lijst met lopende, in de wachtrij en beëindigde taken.
3. Klik op het tabblad **Pijplijntaken.** Er wordt een lijst met pijplijntaken weergegeven, samen met geaggregeerde statistieken voor elke pijplijn.

### <a name="monitoring-recurring-jobs"></a>Terugkerende taken controleren
Een terugkerende taak is een taak die dezelfde bedrijfslogica heeft, maar elke keer dat deze wordt uitgevoerd verschillende invoergegevens gebruikt. Idealiter moeten terugkerende taken altijd slagen en een relatief stabiele uitvoeringstijd hebben; monitoring van dit gedrag zal helpen ervoor te zorgen dat het werk gezond is. Terugkerende taken worden geïdentificeerd met de eigenschap 'Herhaling'. Taken die zijn gepland met ADF V2, worden deze eigenschap automatisch gevuld.

Ga als lid van het Nieuwe U-SQL-overzicht naar een lijst met terugkerende U-SQL-taken: 

1. Ga in de Azure-portal naar uw Data Lake Analytics-accounts.
2. Klik **op Job Insights**. Het tabblad 'Alle taken' wordt standaard weergegeven, met een lijst met lopende, in de wachtrij en beëindigde taken.
3. Klik op het tabblad **Terugkerende taken.** Er wordt een lijst met terugkerende taken weergegeven, samen met geaggregeerde statistieken voor elke terugkerende taak.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Data Lake Analytics](data-lake-analytics-overview.md)
* [Azure Data Lake Analytics beheren met Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Azure Data Lake Analytics beheren met behulp van beleid](data-lake-analytics-account-policies.md)
