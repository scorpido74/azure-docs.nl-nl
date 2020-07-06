---
title: Azure Storage-tabel gegevens versleutelen | Microsoft Docs
description: Meer informatie over het coderen van tabel gegevens in azure Storage.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/11/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: f56946702011968a0fcb31f6fbecbaacdc89ea42
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "60326000"
---
# <a name="encrypt-table-data"></a>Tabel gegevens versleutelen
De client bibliotheek van .NET Azure Storage ondersteunt versleuteling van eigenschappen van teken reeks entiteiten voor INSERT-en Replace-bewerkingen. De versleutelde teken reeksen worden als binaire eigenschappen opgeslagen op de service en worden teruggeconverteerd naar teken reeksen na ontsleuteling.    

Voor tabellen, naast het versleutelings beleid, moeten gebruikers de eigenschappen opgeven die moeten worden versleuteld. U kunt dit doen door ofwel een [EncryptProperty]-kenmerk op te geven (voor POCO-entiteiten die zijn afgeleid van TableEntity) of een Encryption resolver in de aanvraag opties. Een Encryption resolver is een gemachtigde die een partitie sleutel, een rij-en een eigenschaps naam gebruikt en een Booleaanse waarde retourneert die aangeeft of die eigenschap moet worden versleuteld. Tijdens de versleuteling gebruikt de client bibliotheek deze informatie om te bepalen of een eigenschap moet worden versleuteld tijdens het schrijven naar de kabel. De gemachtigde biedt ook de mogelijkheid van logica om te bepalen hoe eigenschappen worden versleuteld. (Bijvoorbeeld als X, vervolgens versleutelings eigenschap A; anders eigenschappen A en B versleutelen.) Het is niet nodig om deze informatie op te geven tijdens het lezen of doorzoeken van entiteiten.

## <a name="merge-support"></a>Ondersteuning voor samen voegen

Samen voegen wordt momenteel niet ondersteund. Omdat een subset van eigenschappen mogelijk eerder is versleuteld met een andere sleutel, hoeft u alleen de nieuwe eigenschappen samen te voegen en de meta gegevens bij te werken als gevolg van gegevens verlies. Samen voegen vereist het maken van extra service aanroepen om de vooraf bestaande entiteit van de service te lezen of een nieuwe sleutel per eigenschap te gebruiken, die beide niet geschikt zijn om prestatie redenen.     

Zie [versleuteling aan client zijde en Azure Key Vault voor Microsoft Azure Storage](../common/storage-client-side-encryption.md)voor meer informatie over het versleutelen van tabel gegevens.  

## <a name="next-steps"></a>Volgende stappen

- [Tabel ontwerp patronen](table-storage-design-patterns.md)
- [Relaties modelleren](table-storage-design-modeling.md)
- [Relaties modelleren](table-storage-design-modeling.md)
- [Ontwerp voor gegevenswijziging](table-storage-design-for-modification.md)
