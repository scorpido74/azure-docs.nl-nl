---
title: De connection string ophalen uit de Azure Portal
description: De connection string ophalen uit de Azure Portal
keywords: SQL-verbinding, connection string
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 6ead2e0ea326b5c3f2e76e7aa9cc4ab3c50d4154
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67176115"
---
### <a name="obtain-the-connection-string-from-the-azure-portal"></a>De connection string ophalen uit de Azure Portal
Gebruik de [Azure Portal](https://portal.azure.com/) om de Connection String te verkrijgen die nodig zijn om uw client programma te laten communiceren met Azure SQL database.

1. Selecteer **alle services**  >  **SQL-data bases**.

2. Voer de naam van uw data base in het tekstvak filter in de linkerbovenhoek van de Blade **SQL-data bases** in.

3. Selecteer de rij voor uw data base.

4. Wanneer de Blade voor de data base wordt weer gegeven, selecteert u de knoppen **minimaliseren** om de Blades die u hebt gebruikt voor bladeren en database filters samen te vouwen.

5. Selecteer **database verbindings reeksen weer geven**op de Blade voor uw data base.

6. Kopieer de juiste connection string. Als u van plan bent de ADO.NET-verbindings bibliotheek te gebruiken, kopieert u de juiste teken reeks van het tabblad **ADO.net** .

    ![De ADO-connection string voor uw data base kopiëren][20-CopyAdoConnectionString]

7. Bewerk de connection string naar wens. Plaats bijvoorbeeld uw wacht woord in de connection string of verwijder ' @ &lt; servername &gt; ' uit de gebruikers naam als de gebruikers naam of de server een te lange waarde heeft.

8. Plak in de ene indeling of een andere de connection string informatie in de code van uw client programma.

Zie [verbindings reeksen en configuratie bestanden](https://msdn.microsoft.com/library/ms254494.aspx)voor meer informatie.

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
