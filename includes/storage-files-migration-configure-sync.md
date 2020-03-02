---
title: Azure File Sync configureren
description: Azure File Sync configureren. Een gemeen schappelijk tekst blok, gedeeld tussen migratie documenten.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 53d7af6b43ff24ab12501570385162759f4c7633
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209582"
---
Deze stap is gekoppeld aan alle resources en mappen die u tijdens de vorige stappen hebt ingesteld op uw Windows-Server.

* Meld u aan bij de [Azure-portal](https://portal.azure.com).
* Zoek de resource van de opslag synchronisatie service.
* Maak een nieuwe *synchronisatie groep* in de resource voor de opslag synchronisatie service voor elke Azure-bestands share. In Azure File Sync terminologie wordt de Azure-bestands share een Cloud- *eind punt* in de synchronisatie topologie die u beschrijft bij het maken van een synchronisatie groep. Wanneer u de synchronisatie groep maakt, geeft u deze een vertrouwde naam, zodat u weet welke set bestanden hier wordt gesynchroniseerd. Zorg ervoor dat u verwijst naar de Azure-bestands share met een overeenkomende naam.
* Zodra de synchronisatie groep is gemaakt, wordt er een rij weer gegeven in de lijst met synchronisatie groepen. Klik op de naam (een koppeling) om de inhoud van de synchronisatie groep weer te geven. U ziet uw Azure-bestands share onder ' Cloud-eind punten '.
* Zoek de opdracht knop naar *+ Server eindpunt toevoegen*. De map op de lokale server die u hebt ingericht, wordt het pad voor dit *Server eindpunt*.
