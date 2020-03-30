---
title: Dynamische gegevensmaskering
description: Dynamische gegevensmaskering beperkt de blootstelling aan gevoelige gegevens door deze te maskeren aan niet-bevoorrechte gebruikers voor SQL Database en Azure Synapse
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/06/2020
tags: azure-synpase
ms.openlocfilehash: e5b281d59245d8fbd32b18f4ac5fe577fc7ff309
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192911"
---
# <a name="dynamic-data-masking-for-azure-sql-database-and-azure-synapse-analytics"></a>Dynamische gegevensmaskering voor Azure SQL Database en Azure Synapse Analytics

Met dynamische gegevensmaskering in SQL Database wordt de blootstelling van gevoelige gegevens beperkt door deze gegevens te maskeren voor niet-gemachtigde gebruikers. 

Dynamische gegevensmaskering helpt onbevoegde toegang tot gevoelige gegevens te voorkomen, doordat klanten kunnen aangeven hoeveel van de gevoelige gegevens mag worden vrijgegeven, met minimale gevolgen voor de toepassingslaag. Dit is een beveiligingsfunctie op basis van beleid. De gevoelige gegevens in de resultatenset van een query die is uitgevoerd op toegewezen databasevelden worden verborgen, terwijl de gegevens in de database niet worden gewijzigd.

Een servicemedewerker van een callcenter kan bellers bijvoorbeeld identificeren met meerdere cijfers van hun creditcardnummer, maar deze gegevensitems mogen niet volledig worden blootgesteld aan de servicevertegenwoordiger. Er kan een maskerregel worden gedefinieerd die alle, behalve de laatste vier cijfers van een creditcardnummer in de resultatenset van een query maskeert. Als ander voorbeeld kan een geschikt gegevensmasker worden gedefinieerd om persoonlijk identificeerbare informatie (PII)-gegevens te beschermen, zodat een ontwikkelaar productieomgevingen kan opvragen voor probleemoplossingsdoeleinden zonder de nalevingsvoorschriften te schenden.

## <a name="dynamic-data-masking-basics"></a>Basisprincipes voor het maskeren van dynamische gegevens

U stelt een dynamisch gegevensmaskerbeleid in de Azure-portal in door de dynamische gegevensmaskerbewerking te selecteren in uw SQL Database-configuratieblad of -instellingenblad. Deze functie kan niet worden ingesteld met portal voor Azure Synapse (Gebruik Powershell of REST API)

### <a name="dynamic-data-masking-permissions"></a>Machtigingen voor het maskeren van dynamische gegevens

Dynamische gegevensmaskering kan worden geconfigureerd door de rollen Azure SQL Database-beheerder, serverbeheerder of [SQL Security Manager.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager)

### <a name="dynamic-data-masking-policy"></a>Dynamisch gegevensmaskerbeleid

* **SQL-gebruikers uitgesloten van maskeren** - Een set SQL-gebruikers of AAD-identiteiten die ongemaskerde gegevens in de SQL-queryresultaten krijgen. Gebruikers met beheerdersbevoegdheden zijn altijd uitgesloten van maskeren en zien de oorspronkelijke gegevens zonder masker.
* **Maskeren regels** - Een set regels die de aangewezen velden definiëren die moeten worden gemaskeerd en de maskerfunctie die wordt gebruikt. De aangewezen velden kunnen worden gedefinieerd met de naam van het databaseschema, de tabelnaam en de kolomnaam.
* **Maskerenfuncties** - Een reeks methoden die de blootstelling van gegevens voor verschillende scenario's regelen.

| Maskeren, functie | Maskeren logica |
| --- | --- |
| **Standaard** |**Volledige maskering op basis van de gegevenstypen van de aangewezen velden**<br/><br/>• Gebruik XXXX of minder Xs als de grootte van het veld kleiner is dan 4 tekens voor tekenreeksgegevenstypen (nchar, ntext, nvarchar).<br/>• Gebruik een nulwaarde voor numerieke gegevenstypen (bigint, bit, decimal, int, geld, numeriek, smallint, smallmoney, tinyint, float, real).<br/>• Gebruik 01-01-1900 voor datum/tijd gegevenstypen (datum, datum2, datumtijd, datumverschuiving, smalldate, tijd).<br/>• Voor sql-variant wordt de standaardwaarde van het huidige type gebruikt.<br/>• Voor XML \<wordt het document masked/> gebruikt.<br/>• Gebruik een lege waarde voor speciale gegevenstypen (tijdstempeltabel, hierarchyid, GUID, binair, afbeelding, varbinary ruimtelijke types). |
| **Creditcard** |**Maskermethode, die de laatste vier cijfers van de aangewezen velden blootlegt** en een constante tekenreeks toevoegt als voorvoegsel in de vorm van een creditcard.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **Email** |**Maskermethode, die de eerste letter blootstelt en het domein vervangt door XXX.com** met behulp van een constant voorvoegsel in de vorm van een e-mailadres.<br/><br/>aXX@XXXX.com |
| **Willekeurig getal** |**Maskermethode, die een willekeurig getal genereert** op basis van de geselecteerde grenzen en werkelijke gegevenstypen. Als de aangewezen grenzen gelijk zijn, is de maskerfunctie een constant getal.<br/><br/>![Navigatiedeelvenster](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Aangepaste tekst** |**Maskermethode, die de eerste en laatste tekens blootlegt** en een aangepaste opvullingstekenreeks in het midden toevoegt. Als de oorspronkelijke tekenreeks korter is dan het blootgestelde voorvoegsel en achtervoegsel, wordt alleen de opvullingstekenreeks gebruikt. <br/>voorvoegsel[padding]achtervoegsel<br/><br/>![Navigatiedeelvenster](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Aanbevolen velden om te maskeren

De DDM aanbevelingen engine, vlaggen bepaalde velden uit uw database als potentieel gevoelige velden, die goede kandidaten voor maskeren kan zijn. In het dynamic data masking blade in de portal ziet u de aanbevolen kolommen voor uw database. Het enige wat u hoeft te doen is op **Masker toevoegen** voor een of meer kolommen en vervolgens **opslaan** om een masker voor deze velden toe te passen.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Dynamische gegevensmaskering instellen voor uw database met PowerShell-cmdlets

Zie [Azure SQL Database Cmdlets](https://docs.microsoft.com/powershell/module/az.sql).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Dynamische gegevensmaskering voor uw database instellen met restapi

Zie [Operations for Azure SQL Database](https://docs.microsoft.com/rest/api/sql/).
