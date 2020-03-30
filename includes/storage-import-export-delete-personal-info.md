---
title: bestand opnemen
description: bestand opnemen
services: azure-policy
author: craigshoemaker
ms.service: azure-policy
ms.topic: include
ms.date: 05/18/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: e6a0ded137162328fd446b65ddb4a15fa6f1db88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176248"
---
## <a name="deleting-personal-information"></a>Persoonlijke gegevens verwijderen

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

Persoonlijke informatie is relevant voor de import/export service (via de portal en API) tijdens import- en exportactiviteiten. Gegevens die tijdens deze processen worden gebruikt, zijn onder andere:

- Naam van contactpersoon
- Telefoonnummer
- Email
- Adres
- Plaats
- Postcode
- Status
- Land/Provincie/Regio
- Stations-ID
- Accountnummer van transporteur
- Volgnummer van verzending

Wanneer een import/exporttaak wordt gemaakt, geven gebruikers contactgegevens en een verzendadres op. Persoonlijke gegevens worden opgeslagen op maximaal twee verschillende locaties: in de taak en optioneel in de portalinstellingen. Persoonlijke gegevens worden alleen opgeslagen in portalinstellingen als u het selectievakje label, **Verzender en retouradres standaard** inschakelt tijdens de sectie *Verzendgegevens retourneren* van het exportproces.

Persoonlijke contactgegevens kunnen op de volgende manieren worden verwijderd:

- Gegevens die bij de taak zijn opgeslagen, worden met de taak verwijderd. Gebruikers kunnen taken handmatig verwijderen en voltooide taken worden na 90 dagen automatisch verwijderd. U de taken handmatig verwijderen via de REST API of de Azure-portal. Als u de taak in de Azure-portal wilt verwijderen, gaat u naar uw taak importeren/exporteren en klikt u op *Verwijderen* vanaf de opdrachtbalk. Voor meer informatie over het verwijderen van een import/exporttaak via REST API verwijzen we u naar [Een import/exporttaak verwijderen.](../articles/storage/common/storage-import-export-cancelling-and-deleting-jobs.md)

- Contactgegevens die zijn opgeslagen in de portalinstellingen kunnen worden verwijderd door de portalinstellingen te verwijderen. U portalinstellingen verwijderen door de volgende stappen te volgen:
  - Meld u aan bij [Azure Portal](https://portal.azure.com).
  - Klik op *Settings* het ![pictogram Instellingen azure-instellingen](media/storage-import-export-delete-personal-info/azure-settings-icon.png)
  - Klik *op Alle instellingen exporteren* (om `.json` uw huidige instellingen in een bestand op te slaan).
  - Klik *op Alle instellingen en privédashboards verwijderen* om alle instellingen te verwijderen, inclusief opgeslagen contactgegevens.

Voor meer informatie, bekijk het Privacybeleid van Microsoft in [het Trust Center](https://www.microsoft.com/trustcenter)