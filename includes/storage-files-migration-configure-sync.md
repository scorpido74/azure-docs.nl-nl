---
title: Azure-bestandssynchronisatie configureren
description: Azure-bestandssynchronisatie configureren. Een gemeenschappelijk tekstblok, gedeeld tussen migratiedocumenten.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 53d7af6b43ff24ab12501570385162759f4c7633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209582"
---
Met deze stap worden alle bronnen en mappen die u tijdens de vorige stappen op uw Windows Server hebt ingesteld, aan elkaar koppelt.

* Meld u aan bij de [Azure-portal](https://portal.azure.com).
* Zoek uw Storage Sync Service-bron.
* Maak een nieuwe *synchronisatiegroep* binnen de Storage Sync Service-bron voor elke Azure-bestandsshare. In de terminologie azure file sync wordt het Azure-bestandsaandeel een eindpunt in de *synchronisatietopologie* die u beschrijft bij het maken van een synchronisatiegroep. Terwijl u de synchronisatiegroep maakt, geeft u deze een bekende naam, zodat u herkent welke set bestanden hier synchroniseert. Zorg ervoor dat u verwijst naar de Azure-bestandsshare met een overeenkomende naam.
* Zodra de synchronisatiegroep is gemaakt, wordt er een rij voor weergegeven in de lijst met synchronisatiegroepen. Klik op de naam (een koppeling) om de inhoud van de synchronisatiegroep weer te geven. U ziet uw Azure-bestandsshare onder 'Cloud-eindpunten'.
* Zoek de opdrachtknop + *Servereindpunt toevoegen*. De map op de lokale server die u hebt ingericht, wordt het pad voor dit *servereindpunt.*
