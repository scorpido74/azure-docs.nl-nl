---
title: Overzicht van automatische afstemming
description: Azure SQL Database analyseert SQL-query en past zich automatisch aan de werkbelasting van gebruikers aan.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/30/2020
ms.openlocfilehash: 7488fd5f8a572788933856f03bb0ad4351885704
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80518229"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Automatische afstemming in Azure SQL-database

Azure SQL Database automatic tuning biedt topprestaties en stabiele workloads door continue prestatieafstemming op basis van AI en machine learning.

Automatische afstemming is een volledig beheerde intelligente prestatieservice die gebruik maakt van ingebouwde intelligentie om query's die in een database worden uitgevoerd, continu te controleren en verbetert automatisch hun prestaties. Dit wordt bereikt door de database dynamisch aan te passen aan de veranderende workloads en tuning-aanbevelingen toe te passen. Automatische afstemming leert horizontaal van alle databases op Azure via AI en verbetert dynamisch zijn tuning-acties. Hoe langer een database wordt uitgevoerd met automatische tuning op, hoe beter het presteert.

Automatische afstemming van Azure SQL Database is mogelijk een van de belangrijkste functies die u inschakelen om stabiele en maximaal presterende databaseworkloads te bieden.

## <a name="what-can-automatic-tuning-do-for-you"></a>Wat kan automatisch stemmen voor u doen

- Geautomatiseerde prestatieafstemming van Azure SQL-databases
- Geautomatiseerde verificatie van prestatieverbeteringen
- Geautomatiseerde terugdraaiing en zelfcorrectie
- Tuning geschiedenis
- Actie T-SQL-scripts afstemmen voor handmatige implementaties
- Proactieve bewaking van de prestaties van de werkbelasting
- Capaciteit uitschalen op honderdduizenden databases
- Positieve impact op de resources van DevOps en de totale eigendomskosten

## <a name="safe-reliable-and-proven"></a>Veilig, betrouwbaar en bewezen

Tuning-bewerkingen die worden toegepast op databases in Azure SQL Database zijn volledig veilig voor de prestaties van uw meest intense workloads. Het systeem is ontworpen met zorg om niet te interfereren met de gebruikersworkloads. Geautomatiseerde tuning aanbevelingen worden alleen toegepast op de momenten van een laag gebruik. Het systeem kan ook automatisch afstemmen tijdelijk uitschakelen om de werkbelastingprestaties te beschermen. In dat geval wordt het bericht 'Uitgeschakeld door het systeem' weergegeven in azure-portal. Automatische afstemming beschouwt workloads met de hoogste resourceprioriteit.

Automatische tuningmechanismen zijn volwassen en zijn geperfectioneerd op enkele miljoenen databases die op Azure worden uitgevoerd. De toegepaste geautomatiseerde tuningbewerkingen worden automatisch geverifieerd om ervoor te zorgen dat de prestaties van de werkbelasting positief worden verbeterd. Teruggevallen prestatieaanbevelingen worden dynamisch gedetecteerd en snel teruggedraaid. Door de opgenomen tuninggeschiedenis bestaat er een duidelijk spoor van tuningverbeteringen die zijn aangebracht in elke Azure SQL-database.

![Hoe werkt automatische tuning](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Azure SQL Database automatic tuning deelt de kernlogica met de SQL Server automatic tuning engine. Zie AUTOMATISCHE afstemming van [SQL Server](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)voor aanvullende technische informatie over het ingebouwde intelligentiemechanisme.

Zie de ingesloten video voor een overzicht van hoe automatisch stemmen werkt en voor typische gebruiksscenario's:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-automatic-tuning/player]

## <a name="enable-automatic-tuning"></a>Automatisch instellen inschakelen

U [automatische afstemming inschakelen voor afzonderlijke en gepoolde databases in de Azure-portal](sql-database-automatic-tuning-enable.md) of met de WIJZIGING [DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) T-SQL-instructie. U schakelt automatische afstemming in een beheerde instantie-implementatie in met behulp van de [wijzigingsdatabase](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-mi-current) T-SQL-instructie.

## <a name="automatic-tuning-options"></a>Automatische afstemmingsopties

Automatische afstemmingsopties die beschikbaar zijn in Azure SQL Database zijn:

| Optie automatisch afstemmen | Ondersteuning voor één database en gepoolde database | Ondersteuning voor instantiedatabase |
| :----------------------------- | ----- | ----- |
| **CREATE INDEX** - Identificeert indexen die de prestaties van uw werkbelasting kunnen verbeteren, maakt indexen en controleert automatisch of de prestaties van query's zijn verbeterd. | Ja | Nee |
| **DROP INDEX** - Identificeert dagelijks redundante en dubbele indexen, met uitzondering van unieke indexen en indexen die lange tijd (>90 dagen) niet zijn gebruikt. Houd er rekening mee dat deze optie niet compatibel is met toepassingen met behulp van partitieschakelen en indexhints. Het laten vallen van ongebruikte indexen wordt niet ondersteund voor Premium- en Business Critical-servicelagen. | Ja | Nee |
| **FORCE LAST GOOD PLAN** (automatische plancorrectie) - Identificeert SQL-query's met behulp van een uitvoeringsplan dat langzamer is dan het vorige goede plan en query's met behulp van het laatst bekende goede plan in plaats van het opnieuw vorige plan. | Ja | Ja |

### <a name="automatic-tuning-for-single-and-pooled-databases"></a>Automatische afstemming voor afzonderlijke en gepoolde databases

Automatische afstemming voor afzonderlijke en gepoolde databases maakt gebruik van de **aanbevelingen CREATE INDEX,** **DROP INDEX**en **FORCE LAST GOOD PLAN database** advisor om de prestaties van uw database te optimaliseren. Zie [Aanbevelingen voor databaseadviseur in de Azure-portal](sql-database-advisor-portal.md), in [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction)en in de [REST-API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning)voor meer informatie.

U afstemmingsaanbevelingen handmatig toepassen via de Azure-portal of u automatisch afstemmen autonoom tuningaanbevelingen voor u laten toepassen. De voordelen van het autonoom toepassen van het systeem tuning aanbevelingen voor u is dat het automatisch valideert er een positieve winst op de prestaties van de werkbelasting, en als er geen significante prestatieverbetering gedetecteerd, zal het automatisch terug de tuning aanbeveling. Houd er rekening mee dat in het geval van query's die worden beïnvloed door tuningaanbevelingen die niet vaak worden uitgevoerd, de validatiefase tot 72 uur kan duren.

In het geval dat u tuningaanbevelingen toepast via T-SQL, zijn de automatische prestatievalidatie en omkeringsmechanismen niet beschikbaar. Aanbevelingen die op deze wijze worden toegepast, blijven actief en worden weergegeven in de lijst met tuningaanbevelingen voor 24-48 uur. voordat het systeem ze automatisch optrekt. Als u een aanbeveling eerder wilt verwijderen, u deze verwijderen uit azure-portal.

Automatische afstemmingsopties kunnen onafhankelijk worden ingeschakeld of uitgeschakeld per database, of ze kunnen worden geconfigureerd op SQL Database-servers en worden toegepast op elke database die instellingen van de server erft. SQL Database-servers kunnen Azure-standaardinstellingen overnemen voor automatische afstemmingsinstellingen. Azure-standaardinstellingen zijn op dit moment ingesteld op FORCE_LAST_GOOD_PLAN is ingeschakeld, CREATE_INDEX is ingeschakeld en DROP_INDEX is uitgeschakeld.

> [!IMPORTANT]
> Vanaf maart worden wijzigingen in Azure-standaardinstellingen voor automatische afstemming als volgt van kracht:
>
> - Nieuwe Azure-standaardinstellingen worden FORCE_LAST_GOOD_PLAN = ingeschakeld, CREATE_INDEX = uitgeschakeld en DROP_INDEX = uitgeschakeld.
> - Bestaande servers zonder geconfigureerde automatische stemvoorkeuren worden automatisch geconfigureerd om de nieuwe Azure-standaardinstellingen te erven. Dit geldt voor alle klanten die momenteel serverinstellingen hebben voor automatische afstemming in een ongedefinieerde status.
> - Nieuwe servers die zijn gemaakt, worden automatisch geconfigureerd om de nieuwe Azure-standaardinstellingen te erven (in tegenstelling tot eerder toen de automatische tuningconfiguratie in een niet-gedefinieerde status was bij het maken van nieuwe servers).

Het configureren van automatische tuningopties op een server en het overnemen van instellingen voor databases die behoren tot de bovenliggende server is een aanbevolen methode voor het configureren van automatische afstemming, omdat het beheer van automatische afstemmingsopties voor een groot aantal databases vereenvoudigt.

Zie [E-mailmeldingen voor automatisch afstemmen voor](sql-database-automatic-tuning-email-notifications.md)meer informatie over het maken van e-mailmeldingen voor automatische afstemming.

### <a name="automatic-tuning-for-instance-databases"></a>Automatische afstemming bijvoorbeeld databases

Automatische afstemming van bijvoorbeeld databases in een beheerde instantieimplementatie ondersteunt alleen **FORCE LAST GOOD PLAN**. Zie [Automatische afstemming introduceert automatische plancorrectie](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) en automatische [plancorrectie](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning?view=sql-server-ver15#automatic-plan-correction)voor meer informatie over het configureren van automatische tuningopties via T-SQL.

## <a name="next-steps"></a>Volgende stappen

- Zie [Artificial Intelligence tunes Azure SQL-databases](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/)voor meer informatie over ingebouwde intelligentie die wordt gebruikt bij automatische tuning.
- Zie [Automatisch indexeren van miljoenen databases in Microsoft Azure SQL Database](https://www.microsoft.com/en-us/research/uploads/prod/2019/02/autoindexing_azuredb.pdf)voor meer informatie over hoe automatisch stemmen onder de motorkap werkt.
