---
title: Opties voor gegevensoverdracht naar Azure met behulp van een toestel | Microsoft Documenten
description: Meer informatie over het kiezen van het juiste toestel om gegevens over te zetten naar Azure
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 9456d975ffb6d7726166f9badbcb8fe0ccbe5256
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68965347"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>StorSimple vergelijken met overdrachtsopties van Azure File Sync en Data Box Edge 

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]
 
Dit document biedt een overzicht van opties voor on-premises gegevensoverdracht naar Azure, waarbij wordt vergeleken met: Data Box Edge vs. Azure File Sync vs. StorSimple 8000-serie.

- **[Data Box Edge](/azure/databox-online/data-box-edge-overview)** – Data Box Edge is een on-premises netwerkapparaat dat gegevens naar en uit Azure verplaatst en edge compute met AI heeft om gegevens vooraf te verwerken tijdens het uploaden. Data Box Gateway is een virtuele versie van het apparaat met dezelfde mogelijkheden voor gegevensoverdracht.
- **[Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide)** – Azure File Sync kan worden gebruikt om de bestandsshares van uw organisatie in Azure Files te centraliseren, terwijl de flexibiliteit, prestaties en compatibiliteit van een on-premises bestandsserver behouden blijven. Door Azure File Sync wordt Windows Server getransformeerd in een snelle cache van uw Azure-bestandsshare. De algemene beschikbaarheid van Azure File Sync werd eerder in 2018 aangekondigd.
- **[StorSimple](/azure/storsimple/storsimple-overview)** – StorSimple is een hybride apparaat waarmee ondernemingen hun opslaginfrastructuur voor primaire opslag, gegevensbescherming, archivering en noodherstel op één oplossing kunnen consolideren door nauw te integreren met Azure-opslag. De levenscyclus van het product voor StorSimple is [hier](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)te vinden.

## <a name="comparison-summary"></a>Vergelijkingsoverzicht

|                           |StorSimple 8000   |Azure File Sync   |Data Box Edge           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|Overzicht         |Gelaagde hybride opslag en archivering|Algemene bestandsserveropslag met cloudtiering en multi-site synchronisatie.  |Opslagoplossing voor het vooraf verwerken van gegevens en deze via het netwerk naar Azure verzenden.        |
|Scenario's        |Bestandsserver, archivering, back-updoel |Bestandsserver, archivering (multi-site)   |Gegevensoverdracht, gegevens voorverwerking inclusief ML-gevolgtrekking, IoT, archivering    |
|Edge-rekenproces     |Niet beschikbaar |Niet beschikbaar |Ondersteunt het uitvoeren van containers met Azure IoT Edge    |
|Vormfactor      |Fysiek apparaat   |Agent geïnstalleerd op Windows Server |Fysiek apparaat   |
|Hardware         |Fysiek apparaat van Microsoft als onderdeel van de service | Klant verstrekt |Fysiek apparaat van Microsoft als onderdeel van de service  |
|Gegevensindeling      |Aangepaste indeling   |Bestanden         |Blobs of bestanden    |
|Protocolondersteuning |iSCSI          |SMB, NFS    | SMB of NFS      |
|Prijzen          |[StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/)  |[Data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure Data Box Edge](/azure/databox-online/data-box-edge-overview) en Azure Data Box [Gateway](/azure/databox-online/data-box-gateway-overview)
- Meer informatie over [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide)
