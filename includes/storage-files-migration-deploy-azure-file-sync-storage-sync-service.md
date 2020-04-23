---
title: Een opslag synchronisatie service implementeren
description: De Azure File Sync Cloud resource implementeren. Een opslag synchronisatie service. Een gemeen schappelijk tekst blok, gedeeld tussen migratie documenten.
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
In deze stap hebt u de referenties van uw Azure-abonnement nodig.

De basis bron voor het configureren van Azure File Sync wordt een ' opslag synchronisatie service ' genoemd.
We raden u aan om slechts één te implementeren voor alle servers in het bedrijf die nu of in de toekomst synchroniseren met dezelfde set bestanden. Maak meerdere opslag synchronisatie Services als u verschillende sets servers hebt die nooit gegevens mogen uitwisselen. (bijvoorbeeld: Synchroniseer dezelfde Azure-bestands share). Anders is één opslag synchronisatie service de best practice.

Kies een Azure-regio voor uw opslag synchronisatie service die zich bevindt in de buurt van uw kantoor locatie. Alle andere cloud resources moeten in dezelfde regio worden geïmplementeerd.
Als u het beheer wilt vereenvoudigen, maakt u een nieuwe resource groep in uw abonnement die is gesynchroniseerd en opslag resources.

In het volgende artikel wordt beschreven hoe u een opslag synchronisatie service implementeert. Volg alleen dit deel van het document. In latere stappen vindt u koppelingen naar andere subsecties van dit document.

[Meer informatie over het implementeren van een opslag synchronisatie service.](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)
