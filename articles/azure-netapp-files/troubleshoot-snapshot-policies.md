---
title: Problemen met het momentopname beleid voor Azure NetApp Files oplossen | Microsoft Docs
description: Hierin worden fout berichten en oplossingen beschreven die u kunnen helpen bij het oplossen van problemen met momentopname beleids beheer voor Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: 1d7a91de8fa505fe4c2b06eea59f3acc2ae1f7e8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91343314"
---
# <a name="troubleshoot-snapshot-policies"></a>Problemen met het momentopname beleid oplossen

In dit artikel worden de fout scenario's beschreven die u kunt tegen komen bij het beheren van Azure NetApp Files snap shot-beleids regels. Het bevat ook oplossingen die u kunnen helpen bij het oplossen van de problemen.

## <a name="snapshot-policy-creation-failing-with-invalid-snapshot-policy-name"></a>Het maken van het momentopname beleid mislukt met een ongeldige naam voor het momentopname beleid

Er treedt een fout op tijdens het maken van het momentopname beleid als de naam van uw momentopname beleid ongeldig is. De volgende richt lijnen zijn van toepassing op naam van het momentopname beleid:  

* De naam van het momentopname beleid mag geen niet-ASCII of speciale tekens bevatten.
* De naam van het momentopname beleid moet beginnen met een letter of een cijfer en mag alleen letters, cijfers, onderstrepings tekens (' _ ') en afbreek streepjes ('-') bevatten.
* De naam van het momentopname beleid moet tussen 1 en 64 tekens lang zijn.  

U moet de naam van het momentopname beleid herzien volgens de richt lijnen.  

## <a name="snapshot-policy-creation-failing-with-invalid-values"></a>Het maken van het momentopname beleid mislukt met ongeldige waarden 

Azure NetApp Files kan geen momentopname beleid maken als u een ongeldige waarde voor een veld opgeeft, zoals `Number of snapshots to keep` of `Minute on the hour to take snapshot` .  
 
De geldige waarden zijn als volgt:   

* De waarde moet een geldig getal zijn.
* De waarde moet tussen 0 en 59.

Zorg ervoor dat er een geldige waarde wordt gegeven voor de velden.

## <a name="snapshot-policy-creation-failing-with-total-number-of-snapshots-to-keep-exceeds-255-error"></a>Het maken van het momentopname beleid mislukt met het fout bericht ' totale aantal moment opnamen dat moet worden bewaard overschrijdt 255 ' 

Elk volume kan [Maxi maal 255 moment opnamen](azure-netapp-files-resource-limits.md)hebben. Het maximum omvat de som van de moment opnamen van elk uur, dagelijks, wekelijks en maandelijks. U moet de `Snapshots to keep` waarde verlagen en het opnieuw proberen.

## <a name="assigning-policy-to-a-volume-failing-with-total-snapshot-policy-is-over-the-max-255-error"></a>Beleid toewijzen aan een volume met een fout ' het totaal beleid voor moment opnamen is groter dan de maximum ' 255 '

Elk volume kan [Maxi maal 255 moment opnamen](azure-netapp-files-resource-limits.md)hebben. Wanneer de som van alle on-demand-, uur-, dagelijkse, wekelijkse en maandelijkse moment opnamen het maximum overschrijdt, treedt er een fout op. Verklein de `snapshots to keep` waarde of verwijder een aantal moment opnamen op aanvraag en probeer het opnieuw.

## <a name="next-steps"></a>Volgende stappen  

* [Momentopname beleid beheren](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)
