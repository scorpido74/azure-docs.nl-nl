---
title: Typen voor conflictoplossing en oplossingsbeleid in Azure Cosmos DB
description: In dit artikel worden de conflictcategorieën en het beleid voor conflictoplossing beschreven in Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: a8ee72f46e1789088e779c10a0824262469ffde8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441993"
---
# <a name="conflict-types-and-resolution-policies"></a>Conflicttypen en oplossingsbeleid

Conflicten en conflictoplossingsbeleid zijn van toepassing als uw Azure Cosmos DB-account is geconfigureerd met meerdere schrijfregio's.

Voor Azure Cosmos-accounts die zijn geconfigureerd met meerdere schrijfregio's, kunnen updateconflicten optreden wanneer schrijvers tegelijkertijd hetzelfde item in meerdere regio's bijwerken. Updateconflicten kunnen van de volgende drie typen zijn:

* **Conflicten invoegen**: deze conflicten kunnen optreden wanneer een toepassing tegelijkertijd twee of meer items met dezelfde unieke index in twee of meer regio's invoegt. Dit conflict kan bijvoorbeeld optreden met een ID-eigenschap.

* **Conflicten vervangen:** deze conflicten kunnen optreden wanneer een toepassing hetzelfde item gelijktijdig in twee of meer regio's bijwerkt.

* **Conflicten verwijderen:** deze conflicten kunnen optreden wanneer een toepassing tegelijkertijd een item in een regio verwijdert en het in een andere regio bijwerkt.

## <a name="conflict-resolution-policies"></a>Beleid voor conflictoplossing

Azure Cosmos DB biedt een flexibel beleidsgestuurd mechanisme om schrijfconflicten op te lossen. U kiezen uit twee beleid voor conflictoplossing op een Azure Cosmos-container:

* **Last Write Wins (LWW)**: Dit resolutiebeleid maakt standaard gebruik van een door het systeem gedefinieerde timestampeigenschap. Het is gebaseerd op het tijdsynchronisatieklokprotocol. Als u de SQL API gebruikt, u een andere aangepaste numerieke eigenschap opgeven (bijvoorbeeld uw eigen notie van een tijdstempel) die moet worden gebruikt voor conflictoplossing. Een aangepaste numerieke eigenschap wordt ook wel het *conflictoplossingspad*genoemd . 

  Als twee of meer items conflicteren bij het invoegen of vervangen van bewerkingen, wordt het item met de hoogste waarde voor het conflictoplossingspad de winnaar. Het systeem bepaalt de winnaar als meerdere items dezelfde numerieke waarde hebben voor het conflictoplossingspad. Alle regio's zijn gegarandeerd te convergeren naar een enkele winnaar en eindigen met dezelfde versie van het vastgelegde item. Wanneer er sprake is van het verwijderen van conflicten, wint de verwijderde versie altijd over het invoegen of vervangen van conflicten. Dit resultaat gebeurt ongeacht de waarde van het conflictoplossingspad.

  > [!NOTE]
  > Last Write Wins is het standaard conflictoplossingsbeleid en gebruikt timestamp `_ts` voor de volgende API's: SQL, MongoDB, Cassandra, Gremlin en Table. Aangepaste numerieke eigenschap is alleen beschikbaar voor SQL API.

  Zie [voorbeelden die gebruikmaken van LWW-conflictoplossingsbeleid](how-to-manage-conflicts.md)voor meer informatie.

* **Aangepast:** dit oplossingsbeleid is ontworpen voor toepassingsgedefinieerde semantiek voor het oplossen van conflicten. Wanneer u dit beleid instelt op uw Azure Cosmos-container, moet u ook een *doorvoegingsprocedure*registreren. Deze procedure wordt automatisch aangeroepen wanneer conflicten worden gedetecteerd onder een databasetransactie op de server. Het systeem biedt precies één keer garantie voor de uitvoering van een samenvoegprocedure als onderdeel van het toezeggingsprotocol.  

  Als u uw container configureert met de optie aangepaste oplossing en u een samenvoegprocedure niet op de container registreert of als de samenvoegprocedure een uitzondering maakt bij runtime, worden de conflicten naar de *conflictfeed*geschreven. Uw toepassing moet vervolgens handmatig de conflicten in de conflictfeed oplossen. Zie voor meer informatie [voorbeelden van het gebruik van het aangepaste oplossingsbeleid en het gebruik van de conflictfeed](how-to-manage-conflicts.md).

  > [!NOTE]
  > Het aangepaste conflictoplossingsbeleid is alleen beschikbaar voor SQL API-accounts.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het configureren van beleid voor conflictoplossing:

* [Multimaster configureren in uw toepassingen](how-to-multi-master.md)
* [Beleid voor conflictoplossing beheren](how-to-manage-conflicts.md)
* [Hoe te lezen uit de conflicten feed](how-to-manage-conflicts.md#read-from-conflict-feed)
