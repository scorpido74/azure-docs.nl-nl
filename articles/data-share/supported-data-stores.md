---
title: Ondersteunde gegevensarchieven in Azure-gegevensshare
description: Meer informatie over de gegevensopslag die worden ondersteund voor het gebruik van Azure Data Share.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 624bb45de3e2ff184326949611d437f71f3e2def
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501801"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Ondersteunde gegevensarchieven in Azure-gegevensshare

Azure Data Share biedt het delen van open en flexibele gegevens, inclusief de mogelijkheid om te delen van en naar verschillende gegevensopslag. Gegevensproviders kunnen gegevens delen van één type gegevensarchief en hun gegevens waarop consumenten kunnen kiezen in welk gegevensarchief ze gegevens willen ontvangen. 

In dit artikel vindt u meer informatie over de uitgebreide set Azure-gegevensopslag die worden ondersteund in Azure Data Share. U ook informatie vinden over de combinaties van gegevenswinkels die kunnen worden gebruikt door gegevensleveranciers en gegevensconsumenten. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Welke gegevensopslag worden ondersteund in Azure Data Share? 

In de onderstaande tabel worden de ondersteunde gegevensbronnen voor Azure Data Share beschreven. 

| Gegevensarchief | Delen op basis van momentopnamen | In-place delen 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ | |
| Azure Data Lake Storage Gen1 |✓ | |
| Azure Data Lake Storage Gen2 |✓ ||
| Azure SQL Database |Openbare preview | |
| Azure Synapse Analytics (voorheen Azure SQL DW) |Openbare preview | |
| Azure Data Explorer | |Openbare preview |

## <a name="data-store-support-matrix"></a>Ondersteuningsmatrix voor gegevensopslag

Azure Data Share biedt gegevensconsumenten flexibiliteit bij het kiezen van een gegevensarchief om gegevens te accepteren. Gegevens die worden gedeeld vanuit Azure SQL Database kunnen bijvoorbeeld worden ontvangen in Azure Data Lake Store Gen2, Azure SQL Database of Azure Synapse Analytics. Klanten kunnen kiezen in welke indeling ze gegevens willen ontvangen bij het configureren van een ontvangen gegevensaandeel. 

In de onderstaande tabel worden verschillende combinaties en keuzes beschreven die gegevens die consumenten hebben bij het accepteren en configureren van hun gegevensaandeel. Zie hoe u [gegevenstoewijzingen configureert](how-to-configure-mapping.md)voor meer informatie over het configureren van gegevenssettoewijzingen.

|  | Azure Blob Storage | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | Azure SQL Database | Azure Synapse Analytics 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage | ✓ || ✓|
| Azure Data Lake Storage Gen1 | ✓ | | ✓|
| Azure Data Lake Storage Gen2 | ✓ | | ✓|
| Azure SQL Database | ✓ | | ✓| ✓| ✓|
| Azure Synapse Analytics (voorheen Azure SQL DW) | ✓ | | ✓| ✓| ✓|

## <a name="share-from-a-storage-account"></a>Delen vanuit een opslagaccount
Azure Data Share ondersteunt het delen van bestanden, mappen en bestandssystemen van Azure Data Lake Gen1 en Azure Data Lake Gen2. Het ondersteunt ook het delen van blobs, mappen en containers vanuit Azure Blob Storage. Alleen blokblob wordt momenteel ondersteund. Wanneer mappen worden gedeeld in het delen op basis van momentopnamen, kan de consument van gegevens ervoor kiezen om een volledige kopie van de share-gegevens te maken of incrementele momentopnamemogelijkheden te gebruiken om alleen nieuwe of bijgewerkte bestanden te kopiëren. Bestaande bestanden met dezelfde naam worden overschreven.

## <a name="share-from-a-sql-based-source"></a>Delen vanuit een SQL-bron
Azure Data Share ondersteunt het delen van tabellen of weergaven vanuit Azure SQL Database en Azure Synapse Analytics (voorheen Azure SQL DW). Gegevensconsumenten kunnen ervoor kiezen om de gegevens te accepteren in Azure Data Lake Store Gen2 of Azure Blob Storage als csv- of parketbestand. Houd er standaard rekening mee dat bestandsindelingen standaard csv zijn. De gegevensconsument kan ervoor kiezen om de gegevens indien gewenst in parketformaat te ontvangen. Dit kan worden gedaan in de instellingen voor het toewijzen van gegevens sets bij het ontvangen van de gegevens. 

Wanneer u gegevens accepteert in Azure Data Lake Store Gen2 of Azure Blob Storage, overschrijven volledige momentopnamen de inhoud van het doelbestand. 

Een gegevensconsument kan ervoor kiezen om gegevens te ontvangen in een tabel naar keuze. Als de doeltabel in dit scenario nog niet bestaat, maakt Azure Data Share in dit scenario de SQL-tabel met het bronschema. Als er al een doeltabel met dezelfde naam bestaat, wordt deze verwijderd en overschreven met de laatste volledige momentopname. Bij het toewijzen van de doeltabel kunnen een alternatief schema en tabelnaam worden opgegeven. Incrementele momentopnamen worden momenteel niet ondersteund. 

Delen vanuit SQL-gebaseerde bronnen heeft vereisten met betrekking tot firewallregels en machtigingen. Raadpleeg de sectie vereisten van het delen van [uw gegevens](share-your-data.md) tutorial voor meer informatie.

## <a name="share-from-azure-data-explorer"></a>Delen vanuit Azure Data Explorer
Azure Data Share ondersteunt de mogelijkheid om databases te delen vanuit Azure Data Explorer-clusters. Gegevensprovider kan delen op database- of clusterniveau. Wanneer de gegevensconsument op databaseniveau worden gedeeld, heeft hij alleen toegang tot de specifieke database(s) die door de gegevensprovider worden gedeeld. Wanneer de gegevensconsument op clusterniveau wordt gedeeld, heeft hij toegang tot alle databases uit het cluster van de provider, inclusief toekomstige databases die door de gegevensprovider zijn gemaakt.

Om toegang te krijgen tot gedeelde databases, moet de consument van gegevens een eigen Azure Data Explorer-cluster hebben. Het Azure Data Explorer-cluster van gegevensgebruikers moet worden gevonden in hetzelfde Azure-datacenter als het Azure Data Explorer-cluster van de gegevensprovider. Wanneer de relatie voor delen tot stand wordt gebracht, maakt Azure Data Share een symbolische koppeling tussen de provider en de Azure Data Explorer-clusters van de consument.

Azure Data Explorer ondersteunt twee manieren van gegevensopname: batch en streaming. Gegevens ontvangen van batch in de gedeelde database zal verschijnen tussen een paar seconden tot een paar minuten aan de kant van de gegevens consument. Het kan tot 24 uur duren voordat gegevens die via streaming worden ontvangen, aan de kant van de gegevensconsument worden weergegeven. 

## <a name="next-steps"></a>Volgende stappen

Ga door met het [delen van uw gegevenszelfstudie](share-your-data.md) om te leren hoe u gegevens delen.
