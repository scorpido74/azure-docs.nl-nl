---
title: Application Development-Azure Database for MySQL
description: Hierin worden ontwerp overwegingen beschreven die een ontwikkelaar moet volgen bij het schrijven van toepassings code om verbinding te maken met Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 74abf680223d562522a11ecb8999fedb37de9907
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770268"
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Overzicht van toepassings ontwikkeling voor Azure Database for MySQL 
In dit artikel worden ontwerp overwegingen beschreven die een ontwikkelaar moet volgen bij het schrijven van toepassings code om verbinding te maken met Azure Database for MySQL. 

> [!TIP]
> Zie [uw eerste Azure database for MySQL data base ontwerpen](tutorial-design-database-using-portal.md) voor een zelf studie waarin wordt beschreven hoe u een server maakt, een firewall op de server maakt, eigenschappen van de server bekijkt, een Data Base maakt en verbinding maakt met behulp van Workbench en MySQL. exe.

## <a name="language-and-platform"></a>Taal en platform
Er zijn codevoorbeelden beschikbaar voor verschillende programmeertalen en platforms. U vindt koppelingen naar de code voorbeelden op: [connectiviteits bibliotheken die worden gebruikt om verbinding te maken met Azure database for MySQL](concepts-connection-libraries.md)

## <a name="tools"></a>Tools
Azure Database for MySQL maakt gebruik van de MySQL-Community-versie die compatibel is met de algemene beheer hulpprogramma's van MySQL, zoals Workbench of MySQL-hulpprogram ma's zoals MySQL. exe, [phpMyAdmin](https://www.phpmyadmin.net/), [Navicat](https://www.navicat.com/products/navicat-for-mysql)en anderen. U kunt ook de Azure Portal, Azure CLI en REST-Api's gebruiken om te communiceren met de database service.

## <a name="resource-limitations"></a>Resourcebeperkingen
Azure Database for MySQL beheert de bronnen die beschikbaar zijn voor een server met behulp van twee verschillende mechanismen: 
- Resources governance.
- Afdwinging van limieten.

## <a name="security"></a>Beveiliging
Azure Database for MySQL biedt bronnen voor het beperken van toegang, het beveiligen van gegevens, het configureren van gebruikers en rollen en het bewaken van activiteiten in een MySQL-data base.

## <a name="authentication"></a>Verificatie
Azure Database for MySQL ondersteunt Server verificatie van gebruikers en aanmeldingen.

## <a name="resiliency"></a>Flexibiliteit
Wanneer er een tijdelijke fout optreedt tijdens het verbinden met een MySQL-data base, moet uw code de aanroep opnieuw proberen. U wordt aangeraden de logica back-ups uit te voeren, zodat het SQL database niet wordt overbelast door meerdere clients tegelijk opnieuw te proberen.

- Code voorbeelden: voor code voorbeelden die een nieuwe pogings logica illustreren, zie voor beelden voor de taal van uw keuze op: [connectiviteits bibliotheken die worden gebruikt om verbinding te maken met Azure database for MySQL](concepts-connection-libraries.md)

## <a name="managing-connections"></a>Verbindingen beheren
Database verbindingen zijn een beperkte resource, daarom raden we u aan het gebruik van verbindingen bij het openen van uw MySQL-data base te gebruiken om betere prestaties te verkrijgen.
- Toegang tot de data base met behulp van verbindings groepen of permanente verbindingen.
- Toegang tot de data base met behulp van een korte levens duur van de verbinding. 
- Gebruik de logica voor opnieuw proberen in uw toepassing op het punt van de verbindings poging om fouten te ondervangen die het gevolg zijn van het aantal gelijktijdige verbindingen dat is toegestaan. Stel in de logica voor opnieuw proberen een korte vertraging in en wacht vervolgens op een wille keurige tijd voordat de extra Verbindings pogingen.