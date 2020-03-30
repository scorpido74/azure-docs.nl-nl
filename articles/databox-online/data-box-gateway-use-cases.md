---
title: Use cases van Microsoft Azure Data Box Gateway | Microsoft Documenten
description: Beschrijft de use cases voor Azure Data Box Gateway, een oplossing voor opslag van virtuele apparaten waarmee u gegevens overbrengen naar Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/02/2019
ms.author: alkohli
ms.openlocfilehash: e72113313e27949819db567c550401b1f051473f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022678"
---
# <a name="use-cases-for-azure-data-box-gateway"></a>Gebruiksvoorbeelden voor Azure Data Box Gateway

Azure Data Box Gateway is een cloudopslaggatewayapparaat dat zich op uw locatie bevindt en uw afbeelding, media en andere gegevens naar Azure stuurt. Deze cloud storage gateway is een virtuele machine ingericht in uw hypervisor. U schrijft gegevens naar dit virtuele apparaat met behulp van de NFS- en SMB-protocollen, die vervolgens naar Azure worden verzendt. In dit artikel vindt u een gedetailleerde beschrijving van de scenario's waarin u dit apparaat implementeren.

Gebruik Data Box Gateway voor de volgende scenario's:

- Om continu enorme hoeveelheden data in te nemen.
- Voor cloud archivering van gegevens op een veilige en efficiënte manier.
- Voor incrementele gegevensoverdracht via het netwerk nadat de eerste bulkoverdracht is uitgevoerd met behulp van Data Box.

Elk van deze scenario's wordt in detail beschreven in de volgende secties.


## <a name="continuous-data-ingestion"></a>Continue gegevensopname

Een van de belangrijkste voordelen van Data Box Gateway is de mogelijkheid om continu gegevens in te nemen in het apparaat om naar de cloud te kopiëren, ongeacht de gegevensgrootte.

Als de gegevens naar het gateway-apparaat worden geschreven, worden de gegevens door het apparaat geüpload naar Azure Storage. Het apparaat beheert automatisch de opslag door de bestanden lokaal te verwijderen met behoud van de metagegevens wanneer het een bepaalde drempel bereikt. Door een lokale kopie van de metagegevens te bewaren, kan het gatewayapparaat de wijzigingen alleen uploaden wanneer het bestand wordt bijgewerkt. De gegevens die naar uw gatewayapparaat worden geüpload, moeten volgens de richtlijnen in [waarschuwingen voor het uploaden van gegevens](data-box-gateway-limits.md#data-upload-caveats)zijn.

Als het apparaat zich vult met gegevens, begint het de invallende snelheid (indien nodig) te beperken om de snelheid te evenaren waarmee gegevens naar de cloud worden geüpload. Om de continue inname op het apparaat te controleren, gebruikt u waarschuwingen. Deze waarschuwingen worden verhoogd zodra de beperking begint en worden gewist zodra de beperking is gestopt.

## <a name="cloud-archival-of-data"></a>Cloudarchivering van gegevens

Gebruik Data Box Gateway wanneer u uw gegevens voor lange termijn in de cloud wilt bewaren. U de **opslaglaag Archief** gebruiken voor langdurige bewaring.

De archieflaag is geoptimaliseerd om zelden geopende gegevens gedurende ten minste 180 dagen op te slaan. De **archieflaag** biedt de laagste opslagkosten, maar heeft de hoogste toegangskosten. Ga voor meer informatie naar [De toegangslaag voor archief](/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier).

### <a name="move-data-to-archive-tier"></a>Gegevens verplaatsen naar archieflaag

Controleer voordat u begint of u een uitgevoerd Data Box Gateway-apparaat hebt. Volg de stappen die worden beschreven in [Zelfstudie: Bereid u voor om Azure Data Box Gateway te implementeren](data-box-gateway-deploy-prep.md) en blijf doorgaan naar de volgende zelfstudie totdat u een operationeel apparaat hebt.

- Gebruik het Data Box Gateway-apparaat om gegevens naar Azure te uploaden via de gebruikelijke overdrachtsprocedure zoals beschreven in [Gegevens overdracht via Data Box Gateway.](data-box-gateway-deploy-add-shares.md)
- Nadat de gegevens zijn geüpload, moet u deze verplaatsen naar de archieflaag. U de blob-laag op twee manieren instellen: Azure PowerShell-script of een Azure Storage Lifecycle Management-beleid.  
    - Als u Azure PowerShell gebruikt, voert u de volgende [stappen](/azure/databox/data-box-how-to-set-data-tier#use-azure-powershell-to-set-the-blob-tier) uit om de gegevens naar de laag Archiveren te verplaatsen.
    - Als u Azure Lifecycle Management gebruikt, voert u de volgende stappen uit om de gegevens naar de laag Archiveren te verplaatsen.
        - [Registreer u](/azure/storage/common/storage-lifecycle-management-concepts) voor de preview van de Blob Lifecycle management-service om de archieflaag te gebruiken.
        - Gebruik het volgende beleid om [gegevens te archiveren over inname.](/azure/storage/blobs/storage-lifecycle-management-concepts#archive-data-after-ingest)
- Zodra de blobs zijn gemarkeerd als Archief, kunnen ze niet meer worden gewijzigd door de gateway, tenzij ze worden verplaatst naar warme of koude laag. Als het bestand zich in de lokale opslag bevindt, worden wijzigingen in de lokale kopie (inclusief verwijderingen) niet geüpload naar de archieflaag.
- Als u gegevens wilt lezen in archiefopslag, moet deze worden gerehydrateerd door de bloblaag te wijzigen in warm of koel. [Als u het aandeel](data-box-gateway-manage-shares.md#refresh-shares) op de gateway vernieuwt, wordt de blob niet gehydrateerd.

Voor meer informatie vindt u meer informatie over het [beheren van de levenscyclus van Azure Blob-opslag](/azure/storage/common/storage-lifecycle-management-concepts).

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>Initiële bulkoverdracht gevolgd door incrementele overdracht

Gebruik Data Box en Data Box Gateway samen wanneer u een bulkupload van een grote hoeveelheid gegevens wilt uploaden, gevolgd door incrementele overdrachten. Gebruik Data Box voor de bulkoverdracht in een offlinemodus (eerste seed) en Data Box Gateway voor incrementele overdrachten (doorlopende feed) via het netwerk.

### <a name="seed-the-data-with-data-box"></a>De gegevens met gegevensvak zaaien

Volg deze stappen om de gegevens naar gegevensvak te kopiëren en te uploaden naar Azure Storage.

1. [Bestel uw databox.](/azure/databox/data-box-deploy-ordered)
2. [Stel uw gegevensvak in.](/azure/databox/data-box-deploy-set-up)
3. [Kopieer gegevens naar gegevensvak via SMB](/azure/databox/data-box-deploy-copy-data).
4. [Retourneer het gegevensvak en controleer de gegevens uploadnaar Azure.](/azure/databox/data-box-deploy-picked-up)
5. Zodra de gegevensupload naar Azure is voltooid, moeten alle gegevens in Azure-opslagcontainers staan. Ga in het opslagaccount voor Gegevensvak naar de container Blob (en Bestand) om te zien of alle gegevens worden gekopieerd. Noteer de naam van de container, want u gebruikt deze naam later. In de volgende schermafbeelding `databox` wordt bijvoorbeeld de container gebruikt voor de incrementele overdracht.

    ![Container met gegevens op gegevensvak](media/data-box-gateway-use-cases/data-container1.png)

Deze bulkoverdracht voltooit de eerste zaaifase.

### <a name="ongoing-feed-with-data-box-gateway"></a>Doorlopende feed met Data Box Gateway

Volg deze stappen voor de lopende opname door Data Box Gateway.

1. Maak een cloudshare op Data Box Gateway. Met dit aandeel worden alle gegevens automatisch geüpload naar het Azure Storage-account. Ga naar **Shares** in uw Data Box Gateway-bron en klik op **+ Delen toevoegen**.

    ![Klik op +Delen toevoegen](media/data-box-gateway-use-cases/add-share1.png)

2. Zorg ervoor dat dit kaarten deelt met de container die de ingezaaide gegevens bevat. Kies bij **Blobcontainer selecteren** **bestaande optie Bestaande gebruiken** en blader naar de container waar de gegevens uit Gegevensvak zijn overgedragen.

    ![Instellingen voor delen](media/data-box-gateway-use-cases/share-settings-select-existing-container1.png)

3. Nadat het aandeel is gemaakt, vernieuwt u het aandeel. Met deze bewerking wordt het on-premises delen met de inhoud van azure vernieuwd.

    ![Aandeel vernieuwen](media/data-box-gateway-use-cases/refresh-share1.png)

    Wanneer het aandeel wordt gesynchroniseerd, uploadt de Data Box Gateway de incrementele wijzigingen als de bestanden op de client zijn gewijzigd.

## <a name="next-steps"></a>Volgende stappen

- Lees de [Systeemvereisten voor Data Box Gateway](data-box-gateway-system-requirements.md).
- Begrijp de [limieten voor Data Box Gateway](data-box-gateway-limits.md).
- Implementeer [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) in de Azure Portal.