---
title: We verplaatsen vm-afbeeldingen (virtual machine) naar beheerde schijfopslag in de Azure Marketplace
description: Om snellere, betrouwbaardere opslag en ondersteuning te bieden voor nieuwe marketplace-functies en -mogelijkheden, verplaatsen we vm-afbeeldingen van Marketplace naar beheerde schijfopslag.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 17b418ab82cc88b4368efe247b06c0c8bbb27453
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285109"
---
# <a name="were-moving-virtual-machine-vm-images-on-azure-marketplace-to-managed-disk-storage"></a>We verplaatsen VM-afbeeldingen (virtual machine) op Azure Marketplace naar beheerde schijfopslag

Om snellere, betrouwbaardere opslag en ondersteuning te bieden voor nieuwe marketplace-functies en -mogelijkheden, verplaatsen we vm-afbeeldingen van Marketplace naar beheerde schijfopslag.

Vanaf 2 januari 2020 verplaatsen we VM-afbeeldingen gefaseerd naar beheerde schijfopslag. In de eerste fase verplaatsen we alleen afbeeldingen zonder nieuwe implementaties of het uitvoeren van VM's in de afgelopen 90 dagen. Voordat we een van de afbeeldingen verplaatsen, sturen we een e-mail om de uitgever te laten weten welke afbeeldingen worden verplaatst en wanneer ze worden verplaatst.

Uitgevers of consumenten hoeven geen actie te ondernemen en gebruikers worden niet beïnvloed. De marktplaatsaanbiedingen blijven beschikbaar en klanten kunnen nog steeds beheerde VM's implementeren vanuit deze afbeeldingen, tijdens en na de verhuizing.

Mocht u vragen hebben, neem dan [contact met ons op.](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff)

## <a name="faqs"></a>Veelgestelde vragen

### <a name="would-the-users-of-my-vm-images-experience-an-outage"></a>Zouden de gebruikers van mijn VM-afbeeldingen een storing ervaren?

Gebruikers van de VM-afbeeldingen zullen geen storing ervaren. 

In de eerste fase verplaatsen we alleen VM-afbeeldingen die geen vm's hebben. Aangezien er geen gebruikers voor deze beelden zijn, zal er geen invloed zijn. Ook voor de volgende fasen zal er geen gevolgen zijn voor de gebruikers.

### <a name="how-long-does-it-take-for-the-process-to-complete"></a>Hoe lang duurt het voordat het proces is voltooid?

Het kan tot 24 uur duren voordat de migratie is voltooid.

### <a name="do-i-need-to-take-any-action"></a>Moet ik actie ondernemen?

Nee. Uitgevers of consumenten hoeven geen actie te ondernemen.

### <a name="do-i-have-to-update-my-system-to-call-the-cloud-portal-apis-in-a-different-way-after-they-are-moved-to-managed-disk-storage"></a>Moet ik mijn systeem bijwerken om de API's van de Cloud Portal op een andere manier te bellen nadat ze zijn verplaatst naar beheerde schijfopslag?

Nee. Uw bestaande API-aanroepen blijven werken.

### <a name="would-all-my-vm-images-be-moved-to-managed-disk-at-the-same-time"></a>Zouden al mijn VM-afbeeldingen tegelijkertijd naar Managed Disk worden verplaatst?

We verplaatsen al uw VM-afbeeldingen op dezelfde dag. We zullen u hiervan op de hoogte stellen zodra ze zijn verplaatst.

### <a name="can-i-request-to-schedule-the-move-of-my-vm-images-to-a-later-time"></a>Kan ik vragen om de verplaatsing van mijn VM-afbeeldingen naar een later tijdstip te plannen?

We raden u aan de afbeeldingen op de geplande datum te verplaatsen. Mocht u zich echter zorgen maken, neem dan contact met ons op om de verhuizing te verzetten.

### <a name="can-i-publish-updates-to-my-vm-images-during-the-move"></a>Kan ik tijdens de verhuizing updates van mijn VM-afbeeldingen publiceren?

Updates van de VM-afbeeldingen kunnen niet worden uitgevoerd tijdens de verhuizing.

### <a name="will-the-publishing-process-change-after-my-vm-image-is-moved-to-managed-disk"></a>Wordt het publicatieproces gewijzigd nadat mijn VM-afbeelding is verplaatst naar Beheerde schijf?

Nee, het publicatieproces blijft hetzelfde. 

### <a name="can-the-publisher-move-their-offers-to-managed-disk"></a>Kan de uitgever zijn aanbiedingen verplaatsen naar Managed Disk?

Nee, de uitgevers kunnen hun aanbiedingen niet verplaatsen naar Managed Disk. Ze zullen moeten wachten en hun beelden worden automatisch verplaatst. We sturen meldingen naar de uitgever voordat we wijzigingen aanbrengen.
