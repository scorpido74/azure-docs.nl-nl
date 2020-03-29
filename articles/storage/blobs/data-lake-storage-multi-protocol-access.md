---
title: Toegang met meerdere protocollen op Azure Data Lake Storage | Microsoft Documenten
description: Gebruik Blob API's en toepassingen die Blob API's gebruiken met Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e3997fc215637175165402a926bffc6ac8d02771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914855"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Toegang met meerdere protocollen op Azure Data Lake Storage

Blob-API's werken nu met accounts met een hiërarchische naamruimte. Dit ontgrendelt het ecosysteem van hulpprogramma's, toepassingen en services, evenals verschillende Blob-opslagfuncties voor accounts met een hiërarchische naamruimte.

Tot voor kort moest u mogelijk afzonderlijke opslagoplossingen voor objectopslag en analyseopslag onderhouden. Dat komt omdat Azure Data Lake Storage Gen2 beperkte ecosysteemondersteuning had. Het had ook beperkte toegang tot Blob-servicefuncties, zoals diagnostische logboekregistratie. Een gefragmenteerde opslagoplossing is moeilijk te onderhouden omdat u gegevens tussen accounts moet verplaatsen om verschillende scenario's te realiseren. Dat hoef je niet meer te doen.

Met toegang tot meerdere protocollen op Data Lake Storage u met uw gegevens werken met behulp van het ecosysteem van tools, toepassingen en services. Dit omvat ook tools en toepassingen van derden. U ze aanwijzen naar accounts met een hiërarchische naamruimte zonder deze te hoeven wijzigen. Deze toepassingen werken *zoals* ze Blob API's noemen, omdat Blob API's nu kunnen werken op gegevens in accounts met een hiërarchische naamruimte.

Blob-opslagfuncties zoals [diagnostische logboekregistratie,](../common/storage-analytics-logging.md) [toegangslagen](storage-blob-storage-tiers.md)en [blob-opslaglevenscyclusbeheerbeleid](storage-lifecycle-management-concepts.md) werken nu met accounts met een hiërarchische naamruimte. Daarom u hiërarchische naamruimten op uw blob-opslagaccounts inschakelen zonder de toegang tot deze belangrijke functies te verliezen. 

> [!NOTE]
> Multiprotocol toegang op Data Lake Storage is algemeen beschikbaar en is beschikbaar in alle regio's. Sommige Azure-services of Blob-opslagfuncties die zijn ingeschakeld met toegang tot meerdere protocollen, blijven in preview.  In deze artikelen wordt een overzicht van de huidige ondersteuning voor Blob-opslagfuncties en Azure-service-integraties. 
>
> [Blob-opslagfuncties beschikbaar in Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
>
>[Azure-services die Azure Data Lake Storage Gen2 ondersteunen](data-lake-storage-supported-azure-services.md)

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Hoe multiprotocoltoegang voor gegevensopslag werkt

Blob API's en Data Lake Storage Gen2 API's kunnen werken op dezelfde gegevens in opslagaccounts met een hiërarchische naamruimte. Data Lake Storage Gen2 leidt Blob API's door de hiërarchische naamruimte, zodat u de voordelen krijgen van eersteklas directorybewerkingen en POSIX-compatibele toegangscontrolelijsten (ACL's). 

![Toegang met meerdere protocollen op Data Lake Storage conceptueel](./media/data-lake-storage-interop/interop-concept.png) 

Bestaande hulpprogramma's en toepassingen die de Blob API gebruiken, krijgen deze voordelen automatisch. Ontwikkelaars hoeven ze niet te wijzigen. Data Lake Storage Gen2 past consequent acl. directory- en bestandsniveau toe, ongeacht het protocol dat hulpprogramma's en toepassingen gebruiken om toegang te krijgen tot de gegevens. 

## <a name="see-also"></a>Zie ook

- [Blob-opslagfuncties beschikbaar in Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Azure-services die Azure Data Lake Storage Gen2 ondersteunen](data-lake-storage-supported-azure-services.md)
- [Open source-platforms die Azure Data Lake Storage Gen2 ondersteunen](data-lake-storage-supported-open-source-platforms.md)
- [Bekende problemen met Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)




