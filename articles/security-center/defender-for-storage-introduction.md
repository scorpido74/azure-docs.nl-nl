---
title: Azure Defender for Storage - de voordelen en functies
description: Kom meer te weten over de voordelen en functies van Azure Defender for Storage.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 4677426337a48d4fde74f61b8a4ad6fcb695f420
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91577812"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Inleiding tot Azure Defender for Storage

**Azure Defender for Storage** detecteert potentieel schadelijke activiteiten in uw Azure Storage-accounts. Uw gegevens kunnen worden beschermd, ongeacht of deze zijn opgeslagen als blobcontainers, bestandsshares of data lakes.

Deze beschermingslaag stelt u in staat om bedreigingen te verhelpen *zonder* dat u een beveiligingsexpert hoeft te zijn, en helpt u bij het beheren van uw beveiligingsbewakingssystemen.


## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Releasestatus:|Algemeen verkrijgbaar (GA)|
|Prijzen:|Voor **Azure Defender for Storage** gelden de prijzen op de [pagina Prijzen](security-center-pricing.md)|
|Beveiligde opslagtypen:|[Blob Storage](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)<br>[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)|
|Clouds:|![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Ja](./media/icons/yes-icon.png) US Gov<br>![Nee](./media/icons/no-icon.png) China Gov, Other Gov|
|||


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Wat voor soort waarschuwingen biedt Azure Defender for Storage?

Beveiligingswaarschuwingen worden geactiveerd in het geval van:

- **Verdachte activiteiten** - het opslagaccount is bijvoorbeeld geopend vanuit een IP-adres dat bekendstaat als een actief afsluitknooppunt of Tor
- **Afwijkend gedrag** - bijvoorbeeld wijzigingen in het toegangspatroon voor een opslagaccount
- **Mogelijk geüploade malware** - hashreputatieanalyse geeft aan dat een geüpload bestand malware bevat

Waarschuwingen bevatten details over het incident waardoor ze zijn geactiveerd evenals aanbevelingen voor het onderzoeken en oplossen van bedreigingen.

> [!TIP]
> U kunt opslagwaarschuwingen simuleren door de instructies in [dit blogbericht](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131) te volgen.


## <a name="what-is-hash-reputation-analysis-for-malware"></a>Wat is hashreputatieanalyse voor malware?

Om te bepalen of een geüpload bestand verdacht is, maakt Azure Defender for Storage gebruik van hashreputatieanalyse, die door [bedreigingsinformatie van Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684) wordt ondersteund. De bedreigingsbeschermingshulpprogramma's scannen de geüploade bestanden niet, maar onderzoeken de opslaglogboeken en vergelijken de hashes van recent geüploade bestanden met die van bekende virussen, Trojaanse paarden, spyware en ransomware. 

Wanneer wordt vermoed dat een bestand malware bevat, wordt in Security Center een waarschuwing weergegeven en kan Security Center de opslageigenaar e-mailen om goedkeuring te vragen om het verdachte bestand te verwijderen. Als u de automatische verwijdering wilt instellen van bestanden die volgens hashreputatieanalyse malware bevatten, implementeert u een [werkstroomautomatisering om waarschuwingen te activeren voor 'malware die mogelijk naar een opslagaccount is geüpload'](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).

> [!NOTE]
> Als u de bedreigingsbeschermingsmogelijkheden van Security Center wilt inschakelen, moet u Azure Defender inschakelen voor het abonnement dat de van toepassing zijnde werkbelastingen bevat.
>
> U kunt **Azure Defender for Storage** inschakelen op abonnementsniveau of op resourceniveau.



## <a name="next-steps"></a>Volgende stappen

In dit artikel bent u meer te weten gekomen over Azure Defender for Storage.

Raadpleeg de volgende artikelen voor gerelateerd materiaal: 

- Een waarschuwing kunt u altijd exporteren, ongeacht of deze door Security Center is gegenereerd of door Security Center is ontvangen vanuit een ander beveiligingsproduct. Als u uw waarschuwingen wilt exporteren naar Azure Sentinel, een extern SIEM of een ander extern hulpprogramma, volgt u de instructies in [Waarschuwingen naar een SIEM exporteren](continuous-export.md).
- [Advanced Defender for Storage inschakelen](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
- [De lijst met waarschuwingen voor Azure Defender for Storage](alerts-reference.md#alerts-azurestorage)
- [De bedreigingsinformatiemogelijkheden van Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684)