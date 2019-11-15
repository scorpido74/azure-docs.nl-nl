---
title: Prestaties bewaken en verbeteren
description: De Azure SQL Database biedt prestatie hulpprogramma's waarmee u gebieden kunt identificeren die de huidige prestaties van de query kunnen verbeteren.
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 11/14/2019
ms.openlocfilehash: c710e2b7c4376fc1725032c86cd02d9a6c8cafac
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74090352"
---
# <a name="monitor-and-improve-performance"></a>Prestaties bewaken en verbeteren

Azure SQL Database identificeert mogelijke problemen in uw data base en raadt acties aan waarmee de prestaties van uw werk belasting kunnen worden verbeterd door intelligente afstemmings acties en aanbevelingen te bieden.

## <a name="performance-tuning-options"></a>Opties voor het afstemmen van prestaties

Opties voor het afstemmen van de prestaties die beschikbaar zijn Azure SQL Database zijn beschikbaar in het navigatie menu voor de data base onder ' intelligente prestaties ':

| Optie prestaties afstemmen | Ondersteuning voor één data base en gepoolde data base | Ondersteuning voor instance data base |
| :----------------------------- | ----- | ----- |
| **[Overzicht van prestaties](#performance-overview)** : alle prestatie activiteiten voor uw data base bewaken. | Ja | Nee | 
| **[Aanbevelingen voor prestaties](#performance-recommendations)** : toont aanbevelingen voor prestaties die de prestaties van uw werk belasting kunnen verbeteren. | Ja | Nee | 
| **[Query Performance Insight](#query-performance-insight)** : toont de prestaties van de meest gebruikte query's voor de data base. | Ja | Nee | 
| **[Automatische optimalisatie](#automatic-tuning)** : gebruik Azure SQL database om de prestaties van uw data base automatisch te optimaliseren. | Ja | Nee | 

## <a name="performance-overview"></a>Overzicht van prestaties

Deze weer gave biedt een overzicht van de prestaties van uw data base en helpt u bij het afstemmen van prestaties en het oplossen van problemen. 

![Prestaties](./media/sql-database-performance/performance.png)

* De tegel **aanbevelingen** biedt een overzicht van de aanbevelingen voor het afstemmen van uw data base (de eerste drie aanbevelingen worden weer gegeven als er meer zijn). Als u op deze tegel klikt, gaat u naar **[aanbevelingen voor prestaties](#performance-recommendations)** . 
* De tegel **activiteit afstemmen** biedt een samen vatting van de lopende en voltooide afstemmings acties voor uw data base, waarmee u een kort overzicht krijgt van de geschiedenis van de afstemmings activiteit. Als u op deze tegel klikt, gaat u naar de weer gave volledige afstemmings geschiedenis voor uw data base.
* De tegel **automatisch afstemmen** toont de [configuratie voor automatisch afstemmen](sql-database-automatic-tuning-enable.md) van uw data base (afstemmings opties die automatisch worden toegepast op uw data base). Als u op deze tegel klikt, wordt het dialoog venster automatiserings configuratie geopend.
* De tegel **database query's** bevat een samen vatting van de query prestaties voor uw data base (algemeen DTU-gebruik en meest voorkomende query's in de resource). Als u op deze tegel klikt, gaat u **[query Performance Insight](#query-performance-insight)** .

## <a name="performance-recommendations"></a>Aanbevelingen voor prestaties

Deze pagina biedt intelligente [afstemmings aanbevelingen](sql-database-advisor.md) waarmee de prestaties van uw data base kunnen worden verbeterd. De volgende typen aanbevelingen worden op deze pagina weer gegeven:

* Aanbevelingen voor het maken of verwijderen van indexen.
* Aanbevelingen voor het identificeren van schema problemen in de-data base.
* Aanbevelingen wanneer query's kunnen profiteren van query's met para meters.

![Prestaties](./media/sql-database-performance/recommendations.png)

U kunt ook de volledige geschiedenis van afstemmings acties vinden die in het verleden zijn toegepast.

Meer informatie over het vinden van aanbevelingen voor het Toep assen van prestaties in het artikel over het [zoeken naar en Toep](sql-database-advisor-portal.md) assen van aanbevelingen.

## <a name="query-performance-insight"></a>Inzicht in queryprestaties

Met [query Performance Insight](sql-database-query-performance.md) kunt u minder tijd best Eden aan het oplossen van problemen met database prestaties door het volgende te bieden:

* Dieper inzicht in uw data bases Resource-verbruik (DTU). 
* Het belangrijkste CPU-verbruik van query's, die mogelijk kunnen worden afgestemd op betere prestaties. 
* De mogelijkheid om in te zoomen op de details van een query. 

  ![Prestatie dashboard](./media/sql-database-query-performance/performance.png)

Meer informatie over deze pagina vindt u in het artikel **[query Performance Insight gebruiken](sql-database-query-performance.md)** .

## <a name="automatic-tuning"></a>Automatisch instellen

Azure SQL-data bases kunnen de prestaties van de data base automatisch afstemmen door [prestatie aanbevelingen](sql-database-advisor.md)toe te passen. Lees voor meer informatie het [artikel automatisch afstemmen](sql-database-automatic-tuning.md). Lees [hoe u automatisch afstemmen inschakelt](sql-database-automatic-tuning-enable.md)om het in te scha kelen.

## <a name="additional-resources"></a>Aanvullende resources

* [Azure SQL Database richt lijnen voor prestaties voor afzonderlijke data bases](sql-database-performance-guidance.md)
* [Wanneer moet een elastische pool worden gebruikt?](sql-database-elastic-pool-guidance.md)
