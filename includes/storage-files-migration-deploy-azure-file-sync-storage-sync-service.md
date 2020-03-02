---
title: Een opslag synchronisatie service implementeren
description: De Azure File Sync Cloud resource implementeren. Een opslag synchronisatie service. Een gemeen schappelijk tekst blok, gedeeld tussen migratie documenten.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 4468d8c6b57b38bdbaaa01208cfc4915f734f6bb
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209452"
---
In deze stap hebt u de referenties van uw Azure-abonnement nodig. Het Azure-abonnement dat u gebruikt, kan afwijken van de versie die u gebruikt voor StorSimple.

De basis bron voor het configureren van Azure File Sync wordt een ' opslag synchronisatie service ' genoemd.
We raden u aan om slechts één te implementeren voor alle servers in het bedrijf die nu of in de toekomst synchroniseren met dezelfde set bestanden. Als u meer dan één StorSimple-apparaat hebt, kunt u overwegen om een opslag synchronisatie service resource te maken voor elk van deze apparaten. U moet echter meerdere opslag synchronisatie Services maken als u verschillende sets servers hebt die nooit gegevens mogen uitwisselen. Anders is één opslag synchronisatie service de best practice.

Kies een Azure-regio voor uw opslag synchronisatie service die zich bevindt in de buurt van uw kantoor locatie. Alle andere cloud resources moeten in dezelfde regio worden geïmplementeerd.
Best Practice is het maken van een nieuwe resource groep in uw abonnement op het bedrijf en opslag resources voor eenvoudiger beheer.

In het volgende artikel wordt beschreven hoe u een opslag synchronisatie service implementeert. Volg alleen dit deel van het document. In latere stappen vindt u koppelingen naar andere subsecties van dit document.

[Meer informatie over het implementeren van een opslag synchronisatie service.](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)
