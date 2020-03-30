---
title: Toepassingsontwikkeling - Azure Database voor MySQL
description: Introduceert ontwerpoverwegingen die een ontwikkelaar moet volgen bij het schrijven van toepassingscode om verbinding te maken met Azure Database voor MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 02ce6b00b6555f849d162b9f3b381c0ab358d712
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532838"
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Overzicht van toepassingsontwikkeling voor Azure Database voor MySQL 
In dit artikel worden ontwerpoverwegingen besproken die een ontwikkelaar moet volgen bij het schrijven van toepassingscode om verbinding te maken met Azure Database voor MySQL. 

> [!TIP]
> Zie Uw [eerste Azure Database voor MySQL-database](tutorial-design-database-using-portal.md) ontwerpen voor een zelfstudie waarin u laat zien hoe u een server maakt, servereigenschappen weergeven, database en verbinding maken en query's maken met behulp van workbench en mysql.exe.

## <a name="language-and-platform"></a>Taal en platform
Er zijn codevoorbeelden beschikbaar voor verschillende programmeertalen en platforms. U koppelingen naar de codevoorbeelden vinden op: [Connectiviteitsbibliotheken die worden gebruikt om verbinding te maken met Azure Database voor MySQL](concepts-connection-libraries.md)

## <a name="tools"></a>Hulpprogramma's
Azure Database voor MySQL maakt gebruik van de MySQL-communityversie, compatibel met MySQL common management tools zoals Workbench of MySQL-hulpprogramma's zoals mysql.exe, [phpMyAdmin](https://www.phpmyadmin.net/), [Navicat](https://www.navicat.com/products/navicat-for-mysql)en anderen. U ook de Azure-portal, Azure CLI en REST-API's gebruiken om te communiceren met de databaseservice.

## <a name="resource-limitations"></a>Resourcebeperkingen
Azure Database voor MySQL beheert de bronnen die beschikbaar zijn voor een server met behulp van twee verschillende mechanismen: 
- Resources Governance.
- Handhaving van limieten.

## <a name="security"></a>Beveiliging
Azure Database voor MySQL biedt bronnen voor het beperken van de toegang, het beveiligen van gegevens, het configureren van gebruikers en rollen en het bewaken van activiteiten in een MySQL-database.

## <a name="authentication"></a>Authentication
Azure Database voor MySQL ondersteunt serververificatie van gebruikers en aanmeldingen.

## <a name="resiliency"></a>Flexibiliteit
Wanneer er een tijdelijke fout optreedt tijdens het maken van een MySQL-database, moet uw code de oproep opnieuw proberen. We raden u aan de logica van het opnieuw proberen te gebruiken, zodat de SQL-database niet wordt overweldigd met meerdere clients die tegelijkertijd opnieuw worden geprobeerd.

- Codevoorbeelden: zie voorbeelden voor de taal van uw keuze voor codevoorbeelden die [worden gebruikt om verbinding te maken met Azure Database voor MySQL](concepts-connection-libraries.md)

## <a name="managing-connections"></a>Verbindingen beheren
Databaseverbindingen zijn een beperkte bron, dus we raden verstandig gebruik van verbindingen aan bij het openen van uw MySQL-database om betere prestaties te bereiken.
- Toegang tot de database met behulp van verbinding pooling of permanente verbindingen.
- Toegang tot de database met behulp van korte levensduur van de verbinding. 
- Gebruik logica opnieuw proberen in uw toepassing op het punt van de verbindingspoging om fouten als gevolg van gelijktijdige verbindingen op te vangen, waardoor het maximaal toegestane bereik is bereikt. Stel in de logica opnieuw proberen een korte vertraging in en wacht vervolgens op een willekeurige tijd voordat de extra verbindingspogingen worden uitgevoerd.