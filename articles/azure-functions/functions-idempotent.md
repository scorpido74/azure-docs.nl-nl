---
title: Azure-functies ontwerpen voor identieke invoer
description: Azure-functies bouwen om idempotent te zijn
author: craigshoemaker
ms.author: cshoe
ms.date: 9/12/2019
ms.topic: article
ms.openlocfilehash: 15af60ac5a862e6fb20e65ba6fbb92482420b7c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74226860"
---
# <a name="designing-azure-functions-for-identical-input"></a>Azure-functies ontwerpen voor identieke invoer

De realiteit van event-driven en message-based architectuur dicteert de noodzaak om identieke verzoeken te accepteren met behoud van gegevensintegriteit en systeemstabiliteit.

Ter illustratie, overweeg een lift oproep knop. Als u op de knop drukt, licht deze op en wordt er een lift naar uw verdieping gestuurd. Even later komt er iemand anders in de lobby. Deze persoon glimlacht naar je en drukt een tweede keer op de verlichte knop. Je glimlacht terug en grinnikt naar jezelf als je eraan herinnerd dat het commando om een lift te bellen is idempotent.

Een druk op een liftoproepknop een tweede, derde of vierde keer heeft geen invloed op het eindresultaat. Wanneer u op de knop drukt, ongeacht het aantal keren, wordt de lift naar uw verdieping gestuurd. Idempotente systemen, zoals de lift, resulteren in hetzelfde resultaat, ongeacht hoe vaak identieke commando's worden uitgegeven.

Als het gaat om het bouwen van toepassingen, overweeg dan de volgende scenario's:

- Wat gebeurt er als uw voorraadbeheertoepassing hetzelfde product meerdere tijd probeert te verwijderen?
- Hoe gedraagt uw personeelstoepassing zich als er meer dan één verzoek is om een werknemersrecord voor dezelfde persoon te maken?
- Waar gaat het geld naartoe als uw bankapp 100 verzoeken krijgt om dezelfde opname te maken?

Er zijn veel contexten waarin aanvragen voor een functie identieke opdrachten kunnen ontvangen. Enkele situaties zijn:

- Beleid dat hetzelfde verzoek verzendt, vele malen opnieuw proberen
- Opdrachten in cache afgespeeld naar de toepassing
- Toepassingsfouten die meerdere identieke aanvragen verzenden

Om de gegevensintegriteit en de systeemstatus te beschermen, bevat een idempotente toepassing logica die mogelijk de volgende gedragingen bevat:

- Controleren van het bestaan van gegevens voordat u probeert een verwijdering uit te voeren
- Controleren of er al gegevens zijn voordat u een maakactie probeert uit te voeren
- Logica combineren die uiteindelijke consistentie in gegevens creëert
- Gelijktijdigheidsbesturingselementen
- Duplicatiedetectie
- Validatie van gegevensversheid
- Beveiligingslogica om invoergegevens te verifiëren

Uiteindelijk idempotentie wordt bereikt door ervoor te zorgen een bepaalde actie is mogelijk en wordt slechts eenmaal uitgevoerd.
