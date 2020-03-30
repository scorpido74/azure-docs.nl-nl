---
title: Een verwijderd opslagaccount herstellen
description: Meer informatie over het herstellen van een verwijderd opslagaccount
author: todmccoy
manager: dcscontentpm
ms.service: storage
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: rogarana
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: 05465d4a03335ac607ba8981116c66fd6dac9416
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252638"
---
# <a name="how-to-recover-a-deleted-storage-account"></a>Een verwijderd opslagaccount herstellen

Azure Storage biedt gegevenstolerantie via geautomatiseerde replica's, maar voorkomt niet dat gebruikers of toepassingscode gegevens beschadigen, zowel per ongeluk als kwaadwillig. Voor het behouden van gegevensgetrouwheid tijdens gevallen van toepassings- of gebruikersfouten zijn meer geavanceerde technieken nodig, zoals het kopiëren van de gegevens naar een secundaire opslaglocatie met een controlelogboek.

In de volgende tabel vindt u een overzicht van het bereik van herstel van het opslagaccount, afhankelijk van de replicatiestrategie.

| |LRS|ZRS|GRS|RA - GRS|
|---|---|---|---|---|
|Azure Resource Manager voor opslagaccount|Ja|Ja|Ja|Ja|
|Klassiek opslagaccount|Ja|Ja|Ja|Ja|

Verzamel de volgende informatie en dien een ondersteuningsverzoek in bij Microsoft Support:

* Naam van opslagaccount
* Schrapdatum
* Opslagaccountgebied
* Hoe is het opslagaccount verwijderd?
* Welke methode heeft het opslagaccount verwijderd? (Portal, PowerShell, enz.)

Belangrijke punten

* Het kan over het algemeen tot 15 dagen duren vanaf het moment van verwijdering voordat de opslagservice garbage collection uitvoert, dus het herstel van opslagaccounts kan niet worden hersteld met een SLA.
* Microsoft Support probeert de container/account op basis van de beste inspanning te herstellen en kan het herstel niet garanderen.

> [!NOTE]
> Het herstel kan niet succesvol zijn als het account opnieuw is gemaakt. Als u het account al opnieuw hebt gemaakt, moet u het account eerst verwijderen voordat herstel kan worden geprobeerd.
