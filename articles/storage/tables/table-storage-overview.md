---
title: Inleiding tot Table-opslag - Objectopslag in Azure | Microsoft Docs
description: Sla gestructureerde gegevens op in de cloud met Azure Table Storage, een oplossing voor NoSQL-gegevensopslag.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.devlang: dotnet
ms.topic: overview
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 73813ae06858df62a88a08bb2f4f8f9f49940b1a
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93316167"
---
# <a name="what-is-azure-table-storage-"></a>Wat is Azure Table Storage? 

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Azure Table Storage is een service die gestructureerde NoSQL-gegevens opslaat in de cloud en zo een sleutel/kenmerkarchiefontwerp zonder schema biedt. Omdat Table Storage schemaloos is, kunt u uw gegevens eenvoudig aanpassen naarmate de behoeften van uw toepassing veranderen. Toegang tot Table Storage-gegevens is snel en kostenefficiënt voor veel soorten toepassingen en doorgaans goedkoper dan traditionele SQL voor vergelijkbare gegevensvolumes.

U kunt Table Storage gebruiken voor de opslag van flexibele gegevenssets, zoals gebruikersgegevens voor webtoepassingen, adresboeken, apparaatgegevens of andere soorten metagegevens die uw service nodig heeft. In elke tabel kunt u een willekeurig aantal entiteiten opslaan. Een opslagaccount kan een onbeperkt aantal tabellen bevatten, tot de maximale capaciteit van het opslagaccount.

[!INCLUDE [storage-table-concepts-include](../../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>Volgende stappen

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) is een gratis, zelfstandige app van Microsoft waarmee u visueel met Azure Storage-gegevens kunt werken in Windows, macOS en Linux.

* [Aan de slag met Azure Table Storage in .NET](../../cosmos-db/tutorial-develop-table-dotnet.md)

* Bekijk de naslagdocumentatie over de Table Service voor meer informatie over beschikbare API's:

    * [Naslaginformatie over de Storage-clientbibliotheek voor .NET](/dotnet/api/overview/azure/storage)

    * [Naslaginformatie over REST API](/rest/api/storageservices/)