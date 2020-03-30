---
title: Een opslagsynchronisatieservice implementeren
description: Implementatie van de Azure File Sync cloudbron. Een storage sync service. Een gemeenschappelijk tekstblok, gedeeld tussen migratiedocumenten.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fe74bae95784dcb63a80ee7e280c02de2eadeb5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124717"
---
In deze stap hebt u uw Azure-abonnementsgegevens nodig.

De kernbron voor het configureren van Azure File Sync wordt een 'Storage Sync Service' genoemd.
We raden u aan er slechts één te implementeren voor alle servers in het bedrijf die nu of in de toekomst dezelfde set bestanden synchroniseren. Maak alleen meerdere Storage Sync Services als u verschillende sets servers hebt die nooit gegevens mogen uitwisselen. (bijvoorbeeld: synchroniseer dezelfde Azure-bestandsshare). Anders is één Storage Sync-service de beste praktijk.

Kies een Azure-regio voor uw Storage Sync Service die zich dicht bij uw kantoorlocatie bevindt. Alle andere cloudresources moeten in dezelfde regio worden geïmplementeerd.
Als u het beheer wilt vereenvoudigen, maakt u een nieuwe brongroep in uw abonnement waarin synchronisatie- en opslagbronnen worden beheerd.

In het volgende artikel wordt beschreven hoe u een Storage Sync-service implementeert. Volg alleen dit deel van de dokter. Er zullen links naar andere subsecties van dit document in latere stappen.

[Meer informatie over het implementeren van een Storage Sync Service.](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)
