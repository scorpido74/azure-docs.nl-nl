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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "67176248"
---
## <a name="deleting-personal-information"></a>Persoonlijke gegevens verwijderen

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

Persoonlijke gegevens zijn relevant voor de import/export-service (via de portal en API) tijdens de import-en export bewerkingen. De gegevens die tijdens deze processen worden gebruikt, zijn:

- Naam van contactpersoon
- Telefoonnummer
- E-mail
- Adres
- Plaats
- Postcode
- Status
- Land/Provincie/Regio
- Stations-ID
- Accountnummer van transporteur
- Volgnummer van verzending

Wanneer een import/export-taak wordt gemaakt, bieden gebruikers contact gegevens en een verzend adres. Persoonlijke gegevens worden op Maxi maal twee verschillende locaties opgeslagen: in de taak en optioneel in de Portal instellingen. Persoonlijke gegevens worden alleen opgeslagen in de Portal-instellingen als u het selectie vakje inschakelt, moet u de **vervoerder en het adres van de afzender als standaard waarde opslaan** tijdens het gedeelte *Verzend gegevens retour* van het export proces.

Persoonlijke contact gegevens kunnen op de volgende manieren worden verwijderd:

- Gegevens die met de taak zijn opgeslagen, worden met de taak verwijderd. Gebruikers kunnen taken hand matig verwijderen en voltooide taken worden na 90 dagen automatisch verwijderd. U kunt de taken hand matig verwijderen via de REST API of de Azure Portal. Als u de taak in de Azure Portal wilt verwijderen, gaat u naar uw import/export-taak en klikt u op *verwijderen* in de opdracht balk. Zie [een import/export-taak verwijderen](../articles/storage/common/storage-import-export-cancelling-and-deleting-jobs.md)voor meer informatie over het verwijderen van een import/export-taak via rest API.

- De contact gegevens die in de Portal instellingen zijn opgeslagen, kunnen worden verwijderd door de Portal instellingen te verwijderen. U kunt de Portal-instellingen verwijderen door de volgende stappen uit te voeren:
  - Meld u aan bij de [Azure-portal](https://portal.azure.com).
  - Klik op het *Settings* pictogram instellingen ![het pictogram Azure-instellingen](media/storage-import-export-delete-personal-info/azure-settings-icon.png)
  - Klik op *alle instellingen exporteren* (om uw huidige instellingen op te `.json` slaan in een bestand).
  - Klik op *alle instellingen en persoonlijke Dash boards verwijderen* om alle instellingen te verwijderen, inclusief opgeslagen contact gegevens.

Raadpleeg het privacybeleid van micro soft in het [vertrouwens centrum](https://www.microsoft.com/trustcenter) voor meer informatie