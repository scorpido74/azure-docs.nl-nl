---
title: Prestatie-aanbevelingen toepassen
description: Gebruik de Azure Portal om prestatie aanbevelingen te vinden waarmee de prestaties van uw Azure SQL Database kunnen worden geoptimaliseerd.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/19/2018
ms.openlocfilehash: b0452d51dc472e100ef52536d8e3814ff395292b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79214166"
---
# <a name="find-and-apply-performance-recommendations"></a>Aanbevelingen voor prestaties zoeken en Toep assen

U kunt de Azure Portal gebruiken om prestatie aanbevelingen te vinden die de prestaties van uw Azure SQL Database kunnen optimaliseren of om een probleem te corrigeren dat in uw workload is geïdentificeerd. Met de pagina met de **prestatie aanbeveling** in azure Portal kunt u de beste aanbevelingen vinden op basis van de mogelijke impact.

## <a name="viewing-recommendations"></a>Aanbevelingen weer geven

Als u aanbevelingen voor prestaties wilt weer geven en Toep assen, hebt u de juiste machtigingen [op rollen gebaseerd toegangs beheer](../role-based-access-control/overview.md) in azure nodig. **Reader**Voor het weer geven van de aanbevelingen en de **eigenaar**van de **SQL DB-Inzender** **zijn machtigingen vereist** voor het uitvoeren van acties. indexen maken of verwijderen en het maken van de index annuleren.

Gebruik de volgende stappen om aanbevelingen voor prestaties te vinden op Azure Portal:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Ga naar **alle services** > **SQL-data bases**en selecteer uw data base.
3. Navigeer naar **Aanbevolen prestaties** om de beschik bare aanbevelingen voor de geselecteerde Data Base weer te geven.

Aanbevelingen voor prestaties worden weer gegeven in de tabel zoals die wordt weer gegeven op de volgende afbeelding:

![Aanbevelingen](./media/sql-database-advisor-portal/recommendations.png)

Aanbevelingen worden gesorteerd op de mogelijke invloed op de prestaties van de volgende categorieën:

| Impact | Beschrijving |
|:--- |:--- |
| Hoog |Aanbevelingen met hoge impact moeten de belangrijkste prestatie-impact bieden. |
| Middelgroot |Aanbevelingen voor normale impact moeten de prestaties verbeteren, maar niet aanzienlijk. |
| Laag |De aanbevelingen met weinig effect moeten betere prestaties leveren dan zonder, maar de verbeteringen zijn mogelijk niet aanzienlijk. |

> [!NOTE]
> Azure SQL Database moet activiteiten ten minste een dag bewaken om enkele aanbevelingen te kunnen identificeren. Het Azure SQL Database kan gemakkelijker worden geoptimaliseerd voor consistente query patronen dan voor wille keurige spotty-bursts. Als er momenteel geen aanbevelingen beschikbaar zijn, wordt op de pagina **prestatie aanbeveling** een bericht weer gegeven waarin wordt uitgelegd waarom.

U kunt ook de status van de historische bewerkingen weer geven. Selecteer een aanbeveling of status om meer informatie weer te geven.

Hier volgt een voor beeld van aanbeveling voor het maken van een index in de Azure Portal.

![Index maken](./media/sql-database-advisor-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Aanbevelingen Toep assen

Azure SQL Database biedt u volledige controle over de manier waarop aanbevelingen worden ingeschakeld met een van de volgende drie opties:

* Afzonderlijke aanbevelingen afzonderlijk Toep assen.
* Schakel automatisch afstemmen in om aanbevelingen automatisch toe te passen.
* Als u een aanbeveling hand matig wilt implementeren, voert u het aanbevolen T-SQL-script uit voor uw data base.

Selecteer een aanbeveling om de details ervan weer te geven en klik vervolgens op **script weer geven** om de exacte details te bekijken van de manier waarop de aanbeveling is gemaakt.

De data base blijft online terwijl de aanbeveling wordt toegepast: door gebruik te maken van de prestatie aanbeveling of automatisch afstemmen maakt u nooit een Data Base offline.

### <a name="apply-an-individual-recommendation"></a>Een afzonderlijke aanbeveling Toep assen

U kunt de aanbevelingen een voor een bekijken en accepteren.

1. Selecteer een aanbeveling op de pagina **aanbevelingen** .
2. Klik op de pagina **Details** op de knop **Toep assen** .

   ![Aanbeveling Toep assen](./media/sql-database-advisor-portal/apply.png)

De geselecteerde aanbeveling wordt toegepast op de data base.

### <a name="removing-recommendations-from-the-list"></a>Aanbevelingen verwijderen uit de lijst

Als uw lijst met aanbevelingen items bevat die u uit de lijst wilt verwijderen, kunt u de aanbeveling negeren:

1. Selecteer een aanbeveling in de lijst met **aanbevelingen** om de details te openen.
2. Klik op de pagina **Details** op **negeren** .

Desgewenst kunt u verwijderde items weer toevoegen aan de lijst met **aanbevelingen** :

1. Klik op de pagina **aanbevelingen** op **Genegeerde weer gave**.
2. Selecteer een verwijderd item uit de lijst om de details ervan weer te geven.
3. Klik eventueel op **ongedaan maken verwijderen** om de index weer toe te voegen aan de hoofd lijst met **aanbevelingen**.

> [!NOTE]
> Als SQL Database [automatisch afstemmen](sql-database-automatic-tuning.md) is ingeschakeld en u hand matig een aanbeveling uit de lijst hebt verwijderd, wordt deze aanbeveling nooit automatisch toegepast. Het verwijderen van een aanbeveling is een handige manier voor gebruikers om automatisch afstemmen in te scha kelen wanneer is vereist dat een specifieke aanbeveling niet moet worden toegepast.
> U kunt dit gedrag herstellen door verwijderde aanbevelingen weer toe te voegen aan de lijst met aanbevelingen door de optie verwijderen ongedaan maken te selecteren.

### <a name="enable-automatic-tuning"></a>Automatisch instellen inschakelen

U kunt de Azure SQL Database zo instellen dat aanbevelingen automatisch worden geïmplementeerd. Wanneer er aanbevelingen beschikbaar komen, worden ze automatisch toegepast. Net als bij alle aanbevelingen die worden beheerd door de service, wordt de aanbeveling teruggedraaid als de invloed van de prestaties negatief is.

1. Klik op de pagina **aanbevelingen** op **automatiseren**:

   ![Advisor-instellingen](./media/sql-database-advisor-portal/settings.png)
2. Selecteer de acties die moeten worden geautomatiseerd:

   ![Aanbevolen indexen](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Houd er rekening mee dat **DROP_INDEX** optie momenteel niet compatibel is met toepassingen die gebruikmaken van partitie switches en index hints.

Wanneer u de gewenste configuratie hebt geselecteerd, klikt u op Toep assen.

### <a name="manually-apply-recommendations-through-t-sql"></a>Aanbevelingen hand matig Toep assen via T-SQL

Selecteer een aanbeveling en klik vervolgens op **script weer geven**. Voer dit script uit op uw data base om de aanbeveling hand matig toe te passen.

*Indexen die hand matig worden uitgevoerd, worden niet bewaakt en gevalideerd voor de prestaties van de service* , zodat u deze indexen na het maken kunt bewaken om te controleren of ze zo nodig prestatie voordelen bieden en aanpassen of verwijderen. Zie [Create Index (Transact-SQL) (Engelstalig)](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql)voor meer informatie over het maken van indexen. Daarnaast blijven hand matig toegepaste aanbevelingen actief en worden deze weer gegeven in de lijst met aanbevelingen voor 24-48 uur. voordat het systeem ze automatisch intrekt. Als u een aanbeveling eerder wilt verwijderen, kunt u deze hand matig negeren.

### <a name="canceling-recommendations"></a>Aanbevelingen annuleren

Aanbevelingen met de status in **behandeling**, **valideren**of **geslaagd** kunnen worden geannuleerd. Aanbevelingen met de status **bezig met uitvoeren** kunnen niet worden geannuleerd.

1. Selecteer een aanbeveling in het gebied **afstemmings geschiedenis** om de pagina met **Details over aanbevelingen** te openen.
2. Klik op **Annuleren** om het proces van het Toep assen van de aanbeveling af te breken.

## <a name="monitoring-operations"></a>Bewerkingen controleren

Het Toep assen van een aanbeveling wordt mogelijk niet onmiddellijk uitgevoerd. De portal biedt Details over de status van aanbeveling. Hieronder ziet u mogelijke statussen die een index kan hebben:

| Status | Beschrijving |
|:--- |:--- |
| In behandeling |De opdracht aanbeveling Toep assen is ontvangen en is gepland voor uitvoering. |
| Uitvoeren |De aanbeveling wordt toegepast. |
| Valideren |Aanbeveling is toegepast en de service meet de voor delen. |
| Geslaagd |De aanbeveling is toegepast en de voor delen zijn gemeten. |
| Fout |Er is een fout opgetreden tijdens het Toep assen van de aanbeveling. Dit kan een tijdelijk probleem zijn, of mogelijk een schema wijziging aan de tabel en het script is niet meer geldig. |
| Herstellen |De aanbeveling is toegepast, maar is niet-uitgevoerd en wordt automatisch teruggezet. |
| Hersteld |De aanbeveling is hersteld. |

Klik in de lijst op een aanbeveling in het proces om meer informatie weer te geven:

![Aanbevolen indexen](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Een aanbeveling herstellen

Als u de aanbevelingen voor prestaties hebt gebruikt om de aanbeveling toe te passen (wat betekent dat u het T-SQL-script niet hand matig hebt uitgevoerd), wordt de wijziging automatisch teruggezet als de prestaties negatief zijn. Als u alleen een aanbeveling wilt herstellen, kunt u het volgende doen:

1. Selecteer een geslaagde aanbeveling in het gebied **afstemmings geschiedenis** .
2. Klik op de pagina **aanbeveling Details** op **herstellen** .

![Aanbevolen indexen](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Prestatie-impact van index aanbevelingen controleren

Nadat de aanbevelingen zijn geïmplementeerd (momenteel alleen de aanbevelingen van index bewerkingen en para meters), kunt u op de pagina aanbevelings Details op **query Insights** klikken om [query prestaties](sql-database-query-performance.md) te openen en de invloed op de prestaties van uw meest voorkomende query's te bekijken.

![Prestatie-impact bewaken](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>Samenvatting

Azure SQL Database biedt aanbevelingen voor het verbeteren van de SQL database prestaties. Door T-SQL-scripts te bieden, kunt u hulp krijgen bij het optimaliseren van uw data base en uiteindelijk de query prestaties verbeteren.

## <a name="next-steps"></a>Volgende stappen

Controleer uw aanbevelingen en pas deze toe om de prestaties te verfijnen. Data base-workloads zijn dynamisch en veranderen voortdurend. Azure SQL Database blijft controleren en aanbevelingen bieden waarmee u de prestaties van uw data base kunt verbeteren.

* Zie [automatisch afstemmen](sql-database-automatic-tuning.md) voor meer informatie over het automatisch afstemmen van Azure SQL database.
* Bekijk de [aanbevelingen voor prestaties](sql-database-advisor.md) voor een overzicht van Azure SQL database prestatie aanbevelingen.
* Zie [query performance Insights](sql-database-query-performance.md) voor meer informatie over het weer geven van de prestatie-impact van uw meest voorkomende query's.

## <a name="additional-resources"></a>Aanvullende resources

* [Query Store](https://msdn.microsoft.com/library/dn817826.aspx)
* [INDEX MAKEN](https://msdn.microsoft.com/library/ms188783.aspx)
* [Op rollen gebaseerd toegangsbeheer](../role-based-access-control/overview.md)
