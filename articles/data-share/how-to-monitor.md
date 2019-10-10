---
title: Preview van Azure data share bewaken
description: Meer informatie over het controleren van de status van de uitnodiging, het delen van abonnementen en de geschiedenis van moment opnamen in azure data share preview.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 22c95f1ac541e1288494ed85cc9654d42780ea60
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169122"
---
# <a name="monitor-azure-data-share-preview"></a>Preview van Azure data share bewaken 

In dit artikel wordt uitgelegd hoe u uw gegevens shares kunt bewaken met behulp van de preview-versie van Azure data share. Als gegevens provider kunt u verschillende aspecten van uw relaties voor het delen van gegevens bewaken. Details over de vraag of uw gegevens gebruikers uw uitnodiging voor de gegevens share hebben geaccepteerd en of ze een share abonnement hebben gemaakt en dat ze uw gegevens kunnen gebruiken, zijn allemaal beschikbaar voor bewaking. 

Als gegevens verbruiker kunt u de moment opnamen bewaken die zijn geactiveerd in uw Azure-abonnement. 

## <a name="monitor-invitation-status"></a>Status van uitnodiging bewaken

Bekijk de status van uw uitnodigingen voor gegevens delen door te navigeren naar verzonden shares->-uitnodigingen. 

(./media/invitation-status.png "Status uitnodiging") voor ![uitnodigingen] 

Er zijn drie statussen die uw uitnodiging kan bevinden:

* In behandeling: de ontvanger van het gegevens gedeelte heeft de uitnodiging nog niet geaccepteerd.
* Geaccepteerd: ontvanger van gegevens delen heeft de uitnodiging geaccepteerd.
* Afgewezen-ontvanger van gegevens delen heeft de uitnodiging afgewezen.

> [!IMPORTANT]
> Als u een uitnodiging verwijdert nadat deze al is geaccepteerd, is deze niet gelijk aan het intrekken van de toegang. Als u wilt voor komen dat toekomstige moment opnamen naar het opslag account van uw data-consumers worden gekopieerd, moet u de toegang intrekken via het tabblad *abonnementen delen* . 

## <a name="monitor-share-subscriptions"></a>Share abonnementen bewaken

Bekijk de status van uw abonnementen voor delen door te navigeren naar verzonden shares->-abonnementen. Dit geeft u details over actieve abonnementen die door uw gegevens gebruikers zijn gemaakt nadat u uw uitnodiging hebt geaccepteerd. U kunt toekomstige updates voor uw gegevens verbruiker stoppen door het abonnement delen te selecteren en *intrekken*te selecteren. 

## <a name="snapshot-history"></a>Momentopname geschiedenis 

Op het tabblad geschiedenis kunt u de moment opnamen weer geven die zijn gekopieerd naar de Tenant van uw gegevens verbruiker. U kunt de frequentie en duur van elk moment opname-interval bewaken. 

![](./media/sent-shares.png "Geschiedenis") van momentopname geschiedenis 

U kunt meer informatie over de uitvoering van elke moment opname bekijken door te klikken op de begin datum van de uitvoering. 

Er worden standaard Maxi maal 30 dagen van de momentopname geschiedenis weer gegeven. Als u meer dan 30 dagen voor de geschiedenis wilt zien, gaat u naar bewaking-> Diagnostische instellingen en selecteert u **Diagnostische instelling toevoegen**. U moet een opslag account selecteren om deze logboeken op te slaan in. 

(./media/diagnostic-settings.png "Diagnostische instellingen") voor ![momentopname geschiedenis] 

## <a name="next-steps"></a>Volgende stappen 

Meer informatie over de [terminologie van Azure data share](terminology.md)