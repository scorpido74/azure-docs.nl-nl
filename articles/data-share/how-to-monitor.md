---
title: Azure-gegevensshare controleren
description: Meer informatie over het bewaken van de uitnodigingsstatus, het delen van abonnementen en momentopnamegeschiedenis in Azure-gegevensshare
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 15089dd99de0471c244a6c0d93931438442599a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73490516"
---
# <a name="monitor-azure-data-share"></a>Azure Data Share bewaken  

In dit artikel wordt uitgelegd hoe u uw gegevensshares controleren met Azure Data Share. Als dataprovider bent u in staat om verschillende aspecten van uw relaties met het delen van gegevens te monitoren. Gegevens zoals of uw gegevens consumenten uw uitnodiging voor het gegevensaandeel hebben geaccepteerd, evenals of ze een aandelenabonnement hebben gemaakt en uw gegevens zijn gaan gebruiken, zijn allemaal beschikbaar om te controleren. 

Als gegevensconsument u de momentopnamen controleren die zijn geactiveerd in uw Azure-abonnement. 

## <a name="monitor-invitation-status"></a>De uitnodigingsstatus controleren

Bekijk de status van uitnodigingen voor gegevensdelen door te navigeren naar Verzonden shares -> uitnodigingen. 

![Uitnodigingsstatus](./media/invitation-status.png "Uitnodigingsstatus") 

Er zijn drie staten die uw uitnodiging kan worden in:

* In behandeling - Ontvanger van gegevensdelen heeft de uitnodiging nog niet geaccepteerd.
* Geaccepteerd - Ontvanger van gegevensdelen heeft de uitnodiging geaccepteerd.
* Afgewezen - Ontvanger van gegevensdelen heeft de uitnodiging afgewezen.

> [!IMPORTANT]
> Als u een uitnodiging verwijdert nadat deze al is geaccepteerd, is deze niet gelijk aan het intrekken van de toegang. Als u wilt voorkomen dat toekomstige momentopnamen worden gekopieerd naar uw opslagaccount voor gegevensconsumenten, moet u de toegang intrekken via het tabblad *Abonnementen delen.* 

## <a name="monitor-share-subscriptions"></a>Abonnementen op delen controleren

Bekijk de status van uw aandelenabonnementen door te navigeren naar Verzonden aandelen -> Aandelenabonnementen. Dit geeft u informatie over actieve abonnementen die zijn gemaakt door uw gegevensconsumenten nadat u uw uitnodiging hebt geaccepteerd. U toekomstige updates voor uw gegevensconsument stoppen door het aandelenabonnement te selecteren en *Intrekken te*selecteren. 

## <a name="snapshot-history"></a>Momentopnamegeschiedenis 

Op het tabblad Geschiedenis u de momentopnamen bekijken die zijn gekopieerd naar de tenant van uw gegevensconsument. U de frequentie en duur van elk momentopnameinterval controleren. 

![Momentopnamegeschiedenis](./media/sent-shares.png "Momentopnamegeschiedenis") 

U meer details over elke momentopname bekijken door op de startdatum van de uitvoering te klikken. 

Tot 30 dagen van momentopnamegeschiedenis wordt standaard weergegeven. Als u meer dan 30 dagen geschiedenis wilt zien, navigeert u naar De diagnostische instellingen voor controle > en selecteert **u Diagnostische instelling toevoegen.** U moet een opslagaccount selecteren om deze logboeken op te slaan. 

![Momentopnamegeschiedenis](./media/diagnostic-settings.png "Diagnostische instellingen") 

## <a name="next-steps"></a>Volgende stappen 

Meer informatie over [de terminologie voor Azure Data Share](terminology.md)