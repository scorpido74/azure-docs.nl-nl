---
title: Azure-opslagtabelgegevens versleutelen | Microsoft Documenten
description: Meer informatie over tabelgegevensversleuteling in Azure-opslag.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/11/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: f56946702011968a0fcb31f6fbecbaacdc89ea42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60326000"
---
# <a name="encrypt-table-data"></a>Tabelgegevens versleutelen
De .NET Azure Storage Client Library ondersteunt versleuteling van tekenreeksentiteiteigenschappen voor het invoegen en vervangen van bewerkingen. De versleutelde tekenreeksen worden opgeslagen op de service als binaire eigenschappen, en ze worden omgezet terug naar strings na decryptie.    

Voor tabellen moeten gebruikers naast het versleutelingsbeleid ook de eigenschappen opgeven die moeten worden versleuteld. Dit kan worden gedaan door een kenmerk [EncryptProperty] op te geven (voor POCO-entiteiten die afkomstig zijn van TableEntity) of een versleutelingsresolver in aanvraagopties. Een versleutelingsresolver is een gemachtigde die een partitiesleutel, rijsleutel en eigendomsnaam neemt en een Booleaan retourneert die aangeeft of die eigenschap moet worden versleuteld. Tijdens versleuteling gebruikt de clientbibliotheek deze informatie om te beslissen of een eigenschap moet worden versleuteld tijdens het schrijven naar de draad. De gemachtigde voorziet ook in de mogelijkheid van logica rond hoe eigenschappen worden versleuteld. (Als X bijvoorbeeld eigenschap A versleutelt; anders eigenschappen A en B versleutelen.) Het is niet nodig om deze informatie te verstrekken tijdens het lezen of opvragen van entiteiten.

## <a name="merge-support"></a>Ondersteuning voor samenvoegen

Samenvoegen wordt momenteel niet ondersteund. Omdat een subset van eigenschappen mogelijk eerder is versleuteld met een andere sleutel, resulteert het samenvoegen van de nieuwe eigenschappen en het bijwerken van de metagegevens in gegevensverlies. Het samenvoegen vereist extra serviceoproepen om de reeds bestaande entiteit uit de service te lezen, of het gebruik van een nieuwe sleutel per eigenschap, die beide niet geschikt zijn om prestatieredenen.     

Zie [Client-Side Encryption en Azure Key Vault voor Microsoft Azure Storage voor](../common/storage-client-side-encryption.md)informatie over het versleutelen van tabelgegevens.  

## <a name="next-steps"></a>Volgende stappen

- [Tabelontwerppatronen](table-storage-design-patterns.md)
- [Relaties modelleren](table-storage-design-modeling.md)
- [Relaties modelleren](table-storage-design-modeling.md)
- [Ontwerp voor gegevenswijziging](table-storage-design-for-modification.md)
