---
title: Azure Data Share-terminologie
description: Meer informatie over algemene termen die worden gebruikt om resources te beschrijven die worden gebruikt in Azure Data Share (gegevensprovider, gegevensverbruiker, gegevensshare, abonnement delen, momentopname, uitnodiging, ontvanger.)
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 33532380d8f98df44029eeea998130d1da5fdafd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73468552"
---
# <a name="azure-data-share-concepts"></a>Azure-concepten voor het delen van gegevens 

Azure Data Share introduceert een aantal nieuwe terminologie met betrekking tot het delen van gegevens. In dit artikel worden enkele veelgebruikte termen uitgelegd die u tijdens de service zien gebruiken. 

## <a name="data-provider"></a>Gegevensprovider

Een dataprovider is de organisatie die gegevens deelt met hun consumenten. Meestal kan de gegevensprovider een eigenaar of een curator van de gegevens zijn. Dataproviders willen gegevens van verschillende soorten delen. Enkele voorbeelden van gegevens die een gegevensprovider mogelijk wil delen, bevatten ruwe gegevens, zoals gegevens over verkooppunten of tijdreeksen. Een gegevensprovider kan ook vooraf verwerkte, samengestelde gegevens willen delen die al analyses en inzichten bevatten. 

## <a name="data-consumer"></a>Gegevensconsument 

Een gegevensconsument is de organisatie die gegevens ontvangt van een gegevensprovider. De gegevensconsument wil mogelijk de gedeelde gegevens met zijn eigen gegevens bundelen om inzichten te verkrijgen. In sommige gevallen ontvangt de gegevensconsument gegevens die al zijn verwerkt. 

## <a name="data-share"></a>Data Share

Een gegevensaandeel is een groep gegevenssets die als één entiteit worden gedeeld. Gegevenssets kunnen afkomstig zijn van een aantal Azure-gegevensbronnen die worden ondersteund door Azure Data Share. Azure Data Share ondersteunt momenteel Azure Blob Storage en Azure Data Lake Store. 

## <a name="share-subscription"></a>Abonnement delen 

Een aandelenabonnement wordt gemaakt wanneer een gegevensconsument een uitnodiging voor gegevensshare van een gegevensprovider accepteert. Gegevensproviders kunnen abonnementen voor actieve delen bekijken door te navigeren naar **Verzonden aandelen** in hun Azure Data Share-account en **Abonnementen delen te**selecteren.

Een gegevensconsument kan controleren of hij of zij een actief aandelenabonnement heeft door naar **ontvangen aandelen** te navigeren en de status van zijn ontvangen aandelen te bekijken. 

## <a name="snapshot"></a>Momentopname

Een momentopname kan worden gemaakt door een gegevensconsument wanneer hij een uitnodiging voor gegevensshare accepteert. Wanneer ze een uitnodiging accepteren, kunnen ze een volledige momentopname van de gegevens activeren die met hen worden gedeeld. De momentopname is een kopie van de gegevens op het moment dat de gegevensconsument de momentopname heeft gegenereerd. 

Er zijn twee soorten momentopnamen - volledig en incrementeel. Een volledige momentopname bevat alle gegevens binnen het gegevensaandeel. Een incrementele momentopname bevat alle gegevens die zijn bijgewerkt/toegevoegd sinds de laatste momentopname is geactiveerd. 

## <a name="snapshot-settings-in-azure-data-share"></a>Momentopnameinstellingen in Azure-gegevensshare
 
Een gegevensprovider kan een momentopnameinstelling inschakelen voor een gegevensshare. Met deze instelling kunnen gegevensconsumenten incrementele updates ontvangen wanneer deze zich voordoen. Deze instelling moet worden ingeschakeld als de gegevensprovider wil dat hun gegevensconsumenten updates ontvangen van gegevens die zijn gedeeld. 

Als een gegevensprovider deze instelling inschakelt, kan een herhalingsinterval worden geselecteerd. Het recidief-interval kan per uur of per dag zijn. 

Een gegevensconsument heeft de mogelijkheid om zich aan te melden voor dit momentopnameschema om incrementele updates te ontvangen, waaronder alle gegevens die zijn gewijzigd sinds ze voor het eerst een nieuwe momentopname hebben gegenereerd. 

## <a name="invitation"></a>Uitnodiging

Een gegevensprovider kan meerdere ontvangers uitnodigen voor hun gegevensaandeel. Ze kunnen dit doen door ontvangers toe te voegen aan het delen van gegevens. Uitnodigingen kunnen ook worden toegevoegd nadat een gegevensshare is gemaakt. 

Een gegevensprovider kan een uitnodiging verwijderen nadat deze is verzonden als deze niet is geaccepteerd. Als de gegevensleverancier een uitnodiging verwijdert en deze nog niet is geaccepteerd, kan de gegevensconsument deze niet accepteren. 

Uitnodigingen kunnen tot vijf keer per dag worden afgekeurd. 

## <a name="recipient"></a>Ontvanger

Een ontvanger is iemand die een uitnodiging voor een gegevensaandeel ontvangt. Doorgaans voegt een gegevensprovider ontvangers toe aan het delen van gegevens die ze maken. Zodra de ontvanger van een uitnodiging de uitnodiging accepteert, wordt hij een gegevensconsument.  

## <a name="next-steps"></a>Volgende stappen

Ga door met het [delen van uw gegevenszelfstudie](share-your-data.md) om te leren hoe u gegevens delen.
