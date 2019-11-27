---
title: Een Azure-bestands share gebruiken met Azure Storage | Microsoft Docs
description: Informatie over hoe u een Azure-bestandsshare gebruikt met Windows en Windows Server.
author: todmccoy
manager: dcscontentpm
ms.service: storage
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: rogarana
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: e1e581134b4e3821659c43bb7f55003239594bd9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233790"
---
# <a name="how-to-recover-a-deleted-storage-account"></a>Een verwijderd opslag account herstellen

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
