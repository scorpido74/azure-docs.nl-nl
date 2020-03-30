---
title: Azure Data Lake Storage Gen1 vergelijking met Azure Storage Blob | Microsoft Documenten
description: Azure Data Lake Storage Gen1 vergelijking met Azure Storage Blob
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: b199525b-84de-4f79-9eb6-69a613b8b217
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 7c958c3ed4d6ddaabd87f053005fcfc1eba8c842
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438724"
---
# <a name="comparing-azure-data-lake-storage-gen1-and-azure-blob-storage"></a>Azure Data Lake Storage Gen1 en Azure Blob-opslag vergelijken

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)] 

De tabel in dit artikel geeft een overzicht van de verschillen tussen Azure Data Lake Storage Gen1 en Azure Blob Storage langs enkele belangrijke aspecten van de verwerking van big data. Azure Blob Storage is een schaalbare objectstore voor algemene doeleinden die is ontworpen voor een breed scala aan opslagscenario's. Azure Data Lake Storage Gen1 is een hyper-scale repository die is geoptimaliseerd voor big data analytics workloads.

|  | Azure Data Lake Storage Gen1 | Azure Blob Storage |
| --- | --- | --- |
| Doel |Geoptimaliseerde opslag voor big data analytics-workloads |Objectstore voor algemene doeleinden voor een breed scala aan opslagscenario's, waaronder big data-analyse |
| Use Cases |Batch-, interactieve, streaming-analyse- en machine learning-gegevens zoals logbestanden, IoT-gegevens, klikstromen, grote gegevenssets |Elk type tekst of binaire gegevens, zoals back-end van toepassingen, back-upgegevens, mediaopslag voor streaming en gegevens voor algemene doeleinden. Daarnaast volledige ondersteuning voor analytics-workloads; batch-, interactieve, streaming-analyse- en machine learning-gegevens zoals logbestanden, IoT-gegevens, klikstromen, grote gegevenssets |
| Belangrijkste concepten |Data Lake Storage Gen1-account bevat mappen, die op hun beurt gegevens bevatten die als bestanden zijn opgeslagen |Opslagaccount heeft containers, die op hun beurt gegevens in de vorm van blobs heeft |
| Structuur |Hiërarchisch bestandssysteem |Objectopslag met platte naamruimte |
| API |REST-API via HTTPS |REST API via HTTP/HTTPS |
| API aan serverzijde |[WebHDFS-compatibele REST API](https://msdn.microsoft.com/library/azure/mt693424.aspx) |[Azure Blob Storage REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx) |
| Hadoop File System Client |Ja |Ja |
| Gegevensbewerkingen - Verificatie |Gebaseerd op [Azure Active Directory Identities](../active-directory/develop/authentication-scenarios.md) |Gebaseerd op gedeelde geheimen - [Accounttoegangssleutels](../storage/common/storage-account-keys-manage.md) en [handtekeningsleutels voor gedeelde toegang](../storage/common/storage-dotnet-shared-access-signature-part-1.md). |
| Gegevensbewerkingen - Verificatieprotocol |OAuth 2.0. Aanroepen moeten een geldige JWT (JSON-webtoken) bevatten die is uitgegeven door Azure Active Directory |HMAC (Hash-based Message Authentication Code) . Oproepen moeten een SHA-256-hash met Basis64 bevatten voor een deel van de HTTP-aanvraag. |
| Gegevensbewerkingen - Autorisatie |POSIX Access Control Lists (ACL's).  ACL's op basis van Azure Active Directory Identities kunnen worden ingesteld op bestands- en mapniveau. |Autorisatie op accountniveau : [Accounttoegangssleutels gebruiken](../storage/common/storage-account-keys-manage.md)<br>Voor account-, container- of blobautorisatie - [Beheer handtekeningsleutels voor gedeelde toegang](../storage/common/storage-dotnet-shared-access-signature-part-1.md) |
| Gegevensbewerkingen - Controle |Beschikbaar. Zie [hier](data-lake-store-diagnostic-logs.md) voor informatie. |Beschikbaar |
| Versleutelingsgegevens in rust |<ul><li>Transparant, serverzijde</li> <ul><li>Met servicebeheerde sleutels</li><li>Met door de klant beheerde sleutels in Azure KeyVault</li></ul></ul> |<ul><li>Transparant, serverzijde</li> <ul><li>Met servicebeheerde sleutels</li><li>Met door de klant beheerde sleutels in Azure KeyVault (voorbeeld)</li></ul><li>Clientversleuteling</li></ul> |
| Beheerbewerkingen (bijv. Account maken) |[RBAC (Role-based access control)](../role-based-access-control/overview.md) van Azure voor accountbeheer |[RBAC (Role-based access control)](../role-based-access-control/overview.md) van Azure voor accountbeheer |
| Ontwikkelaar SDKs |.NET, Java, Python, Node.js |.Net, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS |
| Prestaties van analytics-workloads |Geoptimaliseerde prestaties voor parallelle analytics-workloads. Hoge doorvoer en IOPS. |Geoptimaliseerde prestaties voor parallelle analytics-workloads. |
| Groottelimieten |Geen beperkingen op accountgroottes, bestandsgrootte somof aantal bestanden |Zie [Schaalbaarheidsdoelen voor standaardopslagaccounts](../storage/common/scalability-targets-standard-account.md) en [schaalbaarheids- en prestatiedoelen voor Blob-opslag voor](../storage/blobs/scalability-targets.md)specifieke limieten. Grotere accountlimieten beschikbaar door contact op te nemen met [Azure Support](https://azure.microsoft.com/support/faq/) |
| Geo-redundantie |Lokaal redundant (meerdere kopieën van gegevens in één Azure-regio) |Lokaal redundant (LRS), zone redundant (ZRS), wereldwijd redundant (GRS), read-access wereldwijd redundant (RA-GRS). Zie [hier](../storage/common/storage-redundancy.md) voor meer informatie |
| Servicestatus |Algemeen verkrijgbaar |Algemeen verkrijgbaar |
| Regionale beschikbaarheid |Zie [hier](https://azure.microsoft.com/regions/#services) |Beschikbaar in alle Azure-regio's |
| Prijs |Zie [Prijzen](https://azure.microsoft.com/pricing/details/data-lake-store/) |Zie [Prijzen](https://azure.microsoft.com/pricing/details/storage/) |


