---
title: 'Azure Defender voor opslag: de voor delen en functies'
description: Meer informatie over de voor delen en functies van Azure Defender voor opslag.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 31d1bde1555f98164ccba32d4615ba51837c5ef7
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936752"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Inleiding tot Azure Defender voor opslag

**Azure Defender voor opslag** detecteert mogelijk schadelijke activiteiten op uw Azure Storage-accounts. Uw gegevens kunnen worden beveiligd, ongeacht of deze zijn opgeslagen als blob-containers, bestands shares of gegevens-meren.

Deze beveiligingslaag biedt u de mogelijkheid om bedreigingen te verhelpen *zonder* dat u een beveiligings expert hoeft te zijn, en helpt u bij het beheren van uw systemen voor beveiligings bewaking.


## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Release status:|Algemeen beschikbaar (GA)|
|Koers|**Azure Defender voor opslag** wordt gefactureerd, zoals wordt weer gegeven op [de pagina met prijzen](security-center-pricing.md)|
|Beveiligde opslag typen|[Blob Storage](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)<br>[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)|
|Clouds|![Yes](./media/icons/yes-icon.png) Commerciële Clouds<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) China gov, andere gov|
|||


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Wat voor soort waarschuwingen biedt Azure Defender voor opslag?

Beveiligings waarschuwingen worden geactiveerd wanneer zich:

- **Verdachte activiteit** : bijvoorbeeld: het opslag account is geopend vanuit een IP-adres dat een actief eind punt van Tor wordt genoemd
- **Afwijkend gedrag** : bijvoorbeeld wijzigingen in het toegangs patroon op een opslag account
- **Potentiële malware geüpload** -hash-reputatie analyse geeft aan dat een geüpload bestand malware bevat

Waarschuwingen bevatten details over het incident waarmee ze zijn geactiveerd, evenals aanbevelingen voor het onderzoeken en oplossen van bedreigingen.

> [!TIP]
> U kunt opslag waarschuwingen simuleren door de instructies in [dit blog bericht](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131)te volgen.


## <a name="what-is-hash-reputation-analysis-for-malware"></a>Wat is hash-reputatie analyse voor malware?

Om te bepalen of een geüpload bestand verdacht is, gebruikt Azure Defender voor opslag hash-reputatie analyse die wordt ondersteund door [micro soft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684). Met de hulpprogram ma's voor beveiliging tegen bedreigingen worden de geüploade bestanden niet gescand, en worden de opslag logboeken onderzocht en worden de hashes van recent geüploade bestanden vergeleken met die van bekende virussen, Trojaanse paarden, spyware en Ransomware. 

Wanneer een bestand wordt verdacht om malware te bevatten, wordt in Security Center een waarschuwing weer gegeven en kan de opslag eigenaar voor goed keuring e-mail verzenden om het verdachte bestand te verwijderen. Als u dit automatisch verwijderen van bestanden wilt instellen die door hash-reputatie analyses worden aangegeven, implementeert u een [automatisering op werk stromen om waarschuwingen te activeren met mogelijke malware die is geüpload naar een opslag account](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).

> [!NOTE]
> Als u de mogelijkheden voor bedreigings beveiliging van Security Center wilt inschakelen, moet u Azure Defender inschakelen op het abonnement dat de toepasselijke werk belastingen bevat.
>
> U kunt **Azure Defender voor opslag** inschakelen op abonnements niveau of op het niveau van de resource.



## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over Azure Defender voor opslag.

Raadpleeg de volgende artikelen voor gerelateerde materialen: 

- Of een waarschuwing wordt gegenereerd door Security Center, of door Security Center van een ander beveiligings product is ontvangen, kunt u het exporteren. Als u uw waarschuwingen wilt exporteren naar Azure Sentinel, eventuele SIEM van derden of een ander extern hulp programma, volgt u de instructies in [waarschuwingen exporteren naar een Siem](continuous-export.md).
- [Geavanceerde Defender voor opslag inschakelen](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
- [De lijst met Azure Defender voor opslag waarschuwingen](alerts-reference.md#alerts-azurestorage)
- [De Threat Intelligence-mogelijkheden van micro soft](https://go.microsoft.com/fwlink/?linkid=2128684)