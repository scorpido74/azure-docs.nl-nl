---
title: Prestatie-aanbevelingen toepassen
description: Gebruik de Azure-portal om prestatieaanbevelingen te vinden die de prestaties van uw Azure SQL-database kunnen optimaliseren.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214166"
---
# <a name="find-and-apply-performance-recommendations"></a>Prestatieaanbevelingen zoeken en toepassen

U de Azure-portal gebruiken om prestatieaanbevelingen te vinden die de prestaties van uw Azure SQL-database kunnen optimaliseren of om een probleem te verhelpen dat in uw werkbelasting is vastgesteld. **Met de aanbevelingspagina voor prestaties** in Azure-portal u de belangrijkste aanbevelingen vinden op basis van hun potentiële impact.

## <a name="viewing-recommendations"></a>Aanbevelingen weergeven

Als u prestatieaanbevelingen wilt weergeven en toepassen, hebt u de juiste [op rollen gebaseerde toegangsbeheermachtigingen](../role-based-access-control/overview.md) in Azure nodig. **Reader,** **SQL DB Contributor-machtigingen** zijn vereist om aanbevelingen weer te geven en **SQL** **DB-inzendermachtigingen** zijn vereist om acties uit te voeren. indexen maken of laten vallen en indexcreatie annuleren.

Gebruik de volgende stappen om prestatieaanbevelingen te vinden op azure portal:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Ga naar**SQL-databases** **van Alle services** > en selecteer uw database.
3. Navigeer naar **Prestatieaanbeveling** om beschikbare aanbevelingen voor de geselecteerde database weer te geven.

Prestatieaanbevelingen worden weergegeven in de tabel die vergelijkbaar is met die op de volgende afbeelding:

![Aanbevelingen](./media/sql-database-advisor-portal/recommendations.png)

Aanbevelingen worden gesorteerd op hun potentiële impact op de prestaties in de volgende categorieën:

| Impact | Beschrijving |
|:--- |:--- |
| Hoog |Aanbevelingen met een hoge impact moeten de belangrijkste impact op de prestaties hebben. |
| Middelgroot |Aanbevelingen voor middellange impact moeten de prestaties verbeteren, maar niet wezenlijk. |
| Laag |Aanbevelingen met een lage impact moeten betere prestaties leveren dan zonder, maar verbeteringen zijn mogelijk niet significant. |

> [!NOTE]
> Azure SQL Database moet activiteiten ten minste een dag controleren om een aantal aanbevelingen te kunnen identificeren. De Azure SQL Database kan gemakkelijker optimaliseren voor consistente querypatronen dan voor willekeurige vlekkerige uitbarstingen van activiteit. Als aanbevelingen momenteel niet beschikbaar zijn, geeft de **aanbevelingspagina Prestaties** een bericht waarin wordt uitgelegd waarom.

U ook de status van de historische bewerkingen bekijken. Selecteer een aanbeveling of status om meer informatie te bekijken.

Hier volgt een voorbeeld van aanbeveling 'Index maken' in de Azure-portal.

![Index maken](./media/sql-database-advisor-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Aanbevelingen toepassen

Azure SQL Database geeft u volledige controle over hoe aanbevelingen worden ingeschakeld met behulp van een van de volgende drie opties:

* Pas individuele aanbevelingen één voor één toe.
* Schakel de automatische afstemming in om automatisch aanbevelingen toe te passen.
* Als u een aanbeveling handmatig wilt implementeren, voert u het aanbevolen T-SQL-script uit tegen uw database.

Selecteer een aanbeveling om de details te bekijken en klik vervolgens op **Script weergeven** om de exacte details van de manier waarop de aanbeveling wordt gemaakt te bekijken.

De database blijft online terwijl de aanbeveling wordt toegepast - met behulp van prestatieaanbeveling of automatische afstemming haalt nooit een database offline.

### <a name="apply-an-individual-recommendation"></a>Een individuele aanbeveling toepassen

U aanbevelingen één voor één bekijken en accepteren.

1. Selecteer op de pagina **Aanbevelingen** een aanbeveling.
2. Klik op de pagina **Details** op **Knop Toepassen.**

   ![Aanbeveling toepassen](./media/sql-database-advisor-portal/apply.png)

Geselecteerde aanbevelingwordt toegepast in de database.

### <a name="removing-recommendations-from-the-list"></a>Aanbevelingen uit de lijst verwijderen

Als uw lijst met aanbevelingen items bevat die u uit de lijst wilt verwijderen, u de aanbeveling verwijderen:

1. Selecteer een aanbeveling in de lijst **met aanbevelingen** om de details te openen.
2. Klik **op Verwijderen** op de pagina **Details.**

Indien gewenst u verwijderde items weer toevoegen aan de lijst **Aanbevelingen:**

1. Klik op de pagina **Aanbevelingen** op **Weergave verwijderd**.
2. Selecteer een verwijderd item in de lijst om de details te bekijken.
3. Klik eventueel op **Verwijderen ongedaan maken** om de index weer toe te voegen aan de hoofdlijst met **aanbevelingen**.

> [!NOTE]
> Houd er rekening mee dat als SQL Database [Automatic tuning](sql-database-automatic-tuning.md) is ingeschakeld en als u handmatig een aanbeveling uit de lijst hebt verwijderd, een dergelijke aanbeveling nooit automatisch zal worden toegepast. Het verwijderen van een aanbeveling is een handige manier voor gebruikers om automatische afstemming ingeschakeld in gevallen waarin vereist dat een specifieke aanbeveling niet moet worden toegepast.
> U dit gedrag terugdraaien door verwijderde aanbevelingen terug te voegen aan de lijst Aanbevelingen door de optie Verwijderen ongedaan maken te selecteren.

### <a name="enable-automatic-tuning"></a>Automatisch instellen inschakelen

U instellen dat de Azure SQL Database automatisch aanbevelingen implementeert. Zodra aanbevelingen beschikbaar komen, worden ze automatisch toegepast. Zoals bij alle aanbevelingen die door de service worden beheerd, wordt de aanbeveling teruggedraaid als de prestatie-impact negatief is.

1. Klik op de pagina **Aanbevelingen** op **Automatiseren:**

   ![Instellingen voor adviseurs](./media/sql-database-advisor-portal/settings.png)
2. Selecteer acties die u wilt automatiseren:

   ![Aanbevolen indexen](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Houd er rekening mee dat **DROP_INDEX** optie momenteel niet compatibel is met toepassingen met behulp van partitieschakelen en indexhints.

Nadat u de gewenste configuratie hebt geselecteerd, klikt u op Toepassen.

### <a name="manually-apply-recommendations-through-t-sql"></a>Aanbevelingen handmatig toepassen via T-SQL

Selecteer een aanbeveling en klik op **Script weergeven**. Voer dit script uit tegen uw database om de aanbeveling handmatig toe te passen.

*Indexen die handmatig worden uitgevoerd, worden niet gecontroleerd en gevalideerd op prestatie-impact door de service,* dus er wordt voorgesteld dat u deze indexen na het maken controleert om te controleren of ze prestatiewinst bieden en deze indien nodig aanpassen of verwijderen. Zie [INDEX MAKEN (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql)voor meer informatie over het maken van indexen. Bovendien blijven handmatig toegepaste aanbevelingen actief en worden ze weergegeven in de lijst met aanbevelingen voor 24-48 uur. voordat het systeem ze automatisch optrekt. Als u een aanbeveling eerder wilt verwijderen, u deze handmatig verwijderen.

### <a name="canceling-recommendations"></a>Aanbevelingen annuleren

Aanbevelingen die in **behandeling**zijn, **valideren**of **successtatus** hebben, kunnen worden geannuleerd. Aanbevelingen met de status **Uitvoeren** kunnen niet worden geannuleerd.

1. Selecteer een aanbeveling in het gebied **Tuninggeschiedenis** om de pagina **aanbevelingendetails** te openen.
2. Klik **op Annuleren** om het proces van het toepassen van de aanbeveling af te breken.

## <a name="monitoring-operations"></a>Controlewerkzaamheden

Het toepassen van een aanbeveling gebeurt mogelijk niet onmiddellijk. Het portaal geeft details over de status van de aanbeveling. De volgende zijn mogelijke staten die een index kan worden in:

| Status | Beschrijving |
|:--- |:--- |
| In behandeling |De opdracht Aanbeveling toepassen is ontvangen en is gepland voor uitvoering. |
| Uitvoeren |De aanbeveling wordt toegepast. |
| Valideren |Aanbeveling werd met succes toegepast en de dienst is het meten van de voordelen. |
| Geslaagd |Aanbeveling werd met succes toegepast en voordelen zijn gemeten. |
| Fout |Er is een fout opgetreden tijdens het proces van toepassing van de aanbeveling. Dit kan een tijdelijk probleem zijn, of mogelijk een schemawijziging in de tabel en het script is niet langer geldig. |
| Terugkeren |De aanbeveling werd toegepast, maar wordt beschouwd als niet-performant en wordt automatisch teruggedraaid. |
| Teruggekeerd |De aanbeveling werd teruggedraaid. |

Klik op een in-process aanbeveling uit de lijst om meer informatie te bekijken:

![Aanbevolen indexen](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Een aanbeveling terugdraaien

Als u de prestatieaanbevelingen hebt gebruikt om de aanbeveling toe te passen (wat betekent dat u het T-SQL-script niet handmatig hebt uitgevoerd), wordt de wijziging automatisch teruggedraaid als de prestatie-impact negatief wordt gevonden. Als u om welke reden dan ook gewoon een aanbeveling wilt terugdraaien, u het volgende doen:

1. Selecteer een met succes toegepaste aanbeveling in het gebied **Tuning history.**
2. Klik **op Terugdraaien** op de pagina **aanbevelingsdetails.**

![Aanbevolen indexen](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Monitor prestatie-impact van indexaanbevelingen

Nadat aanbevelingen zijn geïmplementeerd (momenteel, alleen indexbewerkingen en parameteraanbevelingen), u op **Query-inzichten** klikken op de pagina aanbevelingsdetails om [QueryPrestatie-inzichten](sql-database-query-performance.md) te openen en de prestatie-impact van uw topquery's te bekijken.

![Prestatie-impact bewaken](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>Samenvatting

Azure SQL Database bevat aanbevelingen voor het verbeteren van de sql-databaseprestaties. Door T-SQL-scripts aan te bieden, krijgt u hulp bij het optimaliseren van uw database en uiteindelijk het verbeteren van de queryprestaties.

## <a name="next-steps"></a>Volgende stappen

Controleer uw aanbevelingen en blijf deze toepassen om de prestaties te verfijnen. Databaseworkloads zijn dynamisch en veranderen voortdurend. Azure SQL Database blijft controleren en aanbevelingen geven die de prestaties van uw database mogelijk kunnen verbeteren.

* Zie [Automatisch afstemmen](sql-database-automatic-tuning.md) voor meer informatie over de automatische afstemming in Azure SQL Database.
* Zie [Prestatieaanbevelingen](sql-database-advisor.md) voor een overzicht van prestatieaanbevelingen voor Azure SQL Database.
* Zie [Queryprestatiestatistieken](sql-database-query-performance.md) voor meer informatie over het bekijken van de prestatie-impact van uw topquery's.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Query Store](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [Op rollen gebaseerd toegangscontrole](../role-based-access-control/overview.md)
