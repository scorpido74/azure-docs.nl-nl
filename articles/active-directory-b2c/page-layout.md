---
title: Versie van pagina-indeling
titleSuffix: Azure AD B2C
description: Versie geschiedenis van de pagina-indeling voor UI-aanpassing in aangepast beleid.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4548b50e4168f260cb401c40dd4e61192cea1015
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87489534"
---
# <a name="page-layout-versions"></a>Versie van pagina-indeling

Pagina-indelings pakketten worden regel matig bijgewerkt met oplossingen en verbeteringen in hun pagina-elementen. In het volgende wijzigingslog bestand worden de wijzigingen aangegeven die in elke versie zijn geïntroduceerd.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="210"></a>2.1.0

- Zelfbevestigende pagina ( `selfasserted` )
  - Lokalisatie-en toegankelijkheids oplossingen.
- Unified SSP-pagina ( `unifiedssp` )
  - Er is ondersteuning toegevoegd voor meerdere registratie koppelingen.
  - Er is ondersteuning toegevoegd voor gebruikers invoer validatie volgens de predicaat regels die in het beleid zijn gedefinieerd.

## <a name="200"></a>2.0.0

- Zelfbevestigende pagina ( `selfasserted` )
  - Er is ondersteuning toegevoegd voor [besturings elementen voor weer gave](display-controls.md) in aangepast beleid.

## <a name="120"></a>1.2.0

- Alle pagina's
  - Toegankelijkheids oplossingen
  - U kunt nu het `data-preload="true"` kenmerk toevoegen aan [uw HTML-tags](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) om de laad volgorde voor CSS en Java script te bepalen.
    - Laad gekoppelde CSS-bestanden op hetzelfde moment als uw HTML-sjabloon zodat er geen Flik kering is tussen het laden van de bestanden.
    - De volg orde bepalen waarin uw `script` Tags worden opgehaald en uitgevoerd voordat de pagina wordt geladen.
  - Het veld e-mail is nu `type=email` en mobiele toetsen borden bieden de juiste suggesties
  - Ondersteuning voor Chrome-vertaling
- Geïntegreerde en zelf bevestigde pagina's
  - De velden gebruikers naam/e-mail adres en wacht woord gebruiken nu het `form` HTML-element zodat Edge en Internet Explorer (IE) deze gegevens op de juiste manier kunnen opslaan.
- Zelfbevestigende pagina
  - Er is een Configureer bare validatie vertraging voor gebruikers invoer toegevoegd voor verbeterde gebruikers ervaring.

## <a name="110"></a>1.1.0

- Uitzonderings pagina (globalexception)
  - Toegankelijkheids oplossing
  - Het standaard bericht is verwijderd wanneer er geen contact is van het beleid
  - Standaard-CSS verwijderd
- MFA-pagina (multi-factor)
  - De knop voor het bevestigen van de code is verwijderd
  - Het invoer veld voor de code heeft nu alleen invoer van Maxi maal zes (6) tekens
  - Op de pagina wordt automatisch geprobeerd om te controleren of de code die u hebt ingevoerd wanneer een 6-cijferige code wordt ingevoerd, zonder dat u hoeft te klikken op een knop
  - Als de code onjuist is, wordt het invoer veld automatisch gewist
  - Na drie (3) pogingen met een onjuiste code stuurt B2C een fout terug naar de Relying Party
  - Toegankelijkheids oplossingen
  - Standaard-CSS verwijderd
- Zelfbevestigende pagina (selfasserted)
  - Waarschuwing bij annuleren verwijderd
  - CSS-klasse voor fout elementen
  - Fout logica weer geven/verbergen is verbeterd
  - Standaard-CSS verwijderd
- Unified SSP (unifiedssp)
  - Besturings element voor behoud van aangemeld (KMSI) toegevoegd

## <a name="100"></a>1.0.0

- Eerste release

## <a name="next-steps"></a>Volgende stappen

Zie [de gebruikers interface van uw toepassing aanpassen met behulp van een aangepast beleid](custom-policy-ui-customization.md)voor meer informatie over het aanpassen van de gebruikers interface van uw toepassingen in een aangepast beleid.
