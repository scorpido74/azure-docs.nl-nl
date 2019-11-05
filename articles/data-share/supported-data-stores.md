---
title: Ondersteunde gegevens archieven in azure data share
description: Meer informatie over de gegevens archieven die worden ondersteund voor het gebruik van Azure data share.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 762cea6dce3e0c6be3f5e977c5f9de806ca0880e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516394"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Ondersteunde gegevens archieven in azure data share

Azure data share biedt open en flexibel delen van gegevens, met inbegrip van de mogelijkheid om te delen vanuit en naar verschillende gegevens archieven. Gegevens providers kunnen gegevens delen van het ene type gegevens archief en hun gegevens gebruikers kunnen kiezen met welke gegevens opslag gegevens moeten worden ontvangen. 

In dit artikel vindt u meer informatie over de uitgebreide set met Azure-gegevens archieven die worden ondersteund in azure data share. U kunt ook informatie vinden over de combi Naties van gegevens archieven die kunnen worden gebruikt door gegevens providers en gegevens gebruikers. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Welke gegevens archieven worden ondersteund in azure data share? 

De onderstaande tabel bevat een overzicht van de ondersteunde gegevens bronnen voor Azure data share. 

| Gegevensarchief | Op moment opnamen gebaseerd delen | Delen op één plek 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ | |
| Azure Data Lake Storage Gen1 |✓ | |
| Azure Data Lake Storage Gen2 |✓ ||
| Azure SQL Database |Open bare preview | |
| Azure SQL Data Warehouse |Open bare preview | |
| Azure Data Explorer | |[Beperkte preview](https://aka.ms/azuredatasharepreviewsignup) |

## <a name="data-store-support-matrix"></a>Ondersteunings matrix voor gegevens opslag

Azure-gegevens share biedt gegevens gebruikers flexibiliteit bij het bepalen van een gegevens archief om gegevens te accepteren in. Gegevens die vanuit Azure SQL Database worden gedeeld, kunnen bijvoorbeeld worden ontvangen in Azure Data Lake Store Gen2, Azure SQL Database of Azure SQL Data Warehouse. Klanten kunnen kiezen in welke indeling gegevens moeten worden ontvangen bij het configureren van een ontvangen gegevens share. 

De onderstaande tabel bevat informatie over verschillende combi Naties en keuzen die gebruikers van gegevens hebben wanneer ze hun gegevens delen accepteren en configureren. Zie [toewijzing van gegevensset configureren](how-to-configure-mapping.md)voor meer informatie over het configureren van gegevensset-toewijzingen.

|  | Azure Blob Storage | Azure SQL Data Lake gen1 | Azure SQL Data Lake Gen2 | Azure SQL Database | Azure SQL Data Warehouse 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ ||✓|
| Azure Data Lake Storage Gen1 |✓ | |✓|
| Azure Data Lake Storage Gen2 |✓ | |✓|
| Azure SQL Database |✓ | |✓|✓|✓|
| Azure SQL Data Warehouse |✓ | |✓|✓|✓|

## <a name="next-steps"></a>Volgende stappen

Ga door naar de zelf studie [uw gegevens delen](share-your-data.md) voor meer informatie over het delen van gegevens.
