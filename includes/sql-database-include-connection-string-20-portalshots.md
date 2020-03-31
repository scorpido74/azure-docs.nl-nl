---
title: De verbindingstekenreeks verkrijgen van de Azure-portal
description: De verbindingstekenreeks verkrijgen van de Azure-portal
keywords: sql-verbinding, verbindingstekenreeks
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 6ead2e0ea326b5c3f2e76e7aa9cc4ab3c50d4154
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176115"
---
### <a name="obtain-the-connection-string-from-the-azure-portal"></a>De verbindingstekenreeks verkrijgen van de Azure-portal
Gebruik de [Azure-portal](https://portal.azure.com/) om de verbindingstekenreeks te verkrijgen die nodig is voor uw clientprogramma om te communiceren met Azure SQL Database.

1. Selecteer Alle**SQL-databases** **van services** > .

2. Voer de naam van uw database in in het filtertekstvak linksboven in het **SQL-databasesblad.**

3. Selecteer de rij voor uw database.

4. Nadat het blad voor uw database is weergegeven, selecteert u voor visueel gemak de knoppen **minimaliseren** om de bladen samen te vouwen die u hebt gebruikt voor browsen en databasefiltering.

5. Selecteer **databaseverbindingstekenreeksen**weergeven op het blad voor uw database.

6. Kopieer de juiste verbindingstekenreeks. d.w.z. Als u van plan bent de ADO.NET verbindingsbibliotheek te gebruiken, kopieert u de juiste tekenreeks van het **tabblad ADO.NET.**

    ![De ADO-verbindingstekenreeks voor uw database kopiëren][20-CopyAdoConnectionString]

7. Bewerk de verbindingstekenreeks indien nodig. d.w.z. Uw wachtwoord invoegen in&lt;de&gt;verbindingstekenreeks of "@ servername" van de gebruikersnaam verwijderen als de gebruikersnaam of servernaam te lang is.

8. Plak in een of andere indeling de verbindingstekenreeksgegevens in de code van het clientprogramma.

Zie [Verbindingstekenreeksen en configuratiebestanden](https://msdn.microsoft.com/library/ms254494.aspx)voor meer informatie .

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
