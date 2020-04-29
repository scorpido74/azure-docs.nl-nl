---
title: Een verwijderd opslagaccount herstellen
description: Meer informatie over het herstellen van een verwijderd opslag account
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78252638"
---
# <a name="how-to-recover-a-deleted-storage-account"></a>Een verwijderd opslagaccount herstellen

Azure Storage biedt gegevens tolerantie via geautomatiseerde replica's, maar niet voor komen dat gebruikers of toepassings code gegevens beschadigen, ongeacht of ze per ongeluk of opzettelijk zijn beschadigd. Voor het bijhouden van gegevens kwaliteit tijdens exemplaren van de toepassing of de gebruikers fout zijn meer geavanceerde technieken vereist, zoals het kopiëren van de gegevens naar een secundaire opslag locatie met een audit logboek.

De volgende tabel bevat een overzicht van het bereik van herstel van opslag accounts, afhankelijk van de replicatie strategie.

| |LRS|ZRS|GRS|RA-GRS|
|---|---|---|---|---|
|Azure Resource Manager van opslag account|Ja|Ja|Ja|Ja|
|Klassiek opslag account|Ja|Ja|Ja|Ja|

Verzamel de volgende informatie en verwerk een ondersteunings aanvraag met Microsoft Ondersteuning:

* Naam van opslagaccount
* Datum van verwijdering
* Regio van opslag account
* Hoe is het opslag account verwijderd?
* Welke methode heeft het opslag account verwijderd? (Portal, Power shell, enz.)

Belang rijke punten

* Het kan over het algemeen tot wel vijf tien dagen duren vanaf het moment van verwijderen van de opslag service om garbagecollection uit te voeren, zodat herstel van opslag accounts niet kan worden hersteld met een SLA.
* Microsoft Ondersteuning probeert de container/het account te herstellen op basis van de beste inspanningen en kan het herstel niet garanderen.

> [!NOTE]
> Het herstel kan niet worden uitgevoerd als het account opnieuw is gemaakt. Als u het account al opnieuw hebt gemaakt, moet u het eerst verwijderen voordat u het herstel kunt uitvoeren.
