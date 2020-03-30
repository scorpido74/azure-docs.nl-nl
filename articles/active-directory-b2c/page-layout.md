---
title: Versie van pagina-indeling
titleSuffix: Azure AD B2C
description: Versiegeschiedenis van de pagina-indeling voor aanpassing van de gebruikersinterface in aangepast beleid.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3d0cb06f84fdd96d099e05f55ba62c37cb1192c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183972"
---
# <a name="page-layout-versions"></a>Versie van pagina-indeling

Pagina-indelingspakketten worden periodiek bijgewerkt om correcties en verbeteringen in hun pagina-elementen op te nemen. In het volgende wijzigingslogboek worden de wijzigingen opgegeven die in elke versie zijn ingevoerd.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="200"></a>2.0.0

- Zelfverklaarde pagina (`selfasserted`)
  - Ondersteuning toegevoegd voor [weergavebesturingselementen](display-controls.md) in aangepast beleid.

## <a name="120"></a>1.2.0

- Alle pagina's
  - Toegankelijkheidsoplossingen
  - U het `data-preload="true"` kenmerk nu [toevoegen aan uw HTML-tags](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) om de belastingsvolgorde voor CSS en JavaScript te beheren.
    - Laad gekoppelde CSS-bestanden op hetzelfde moment als uw HTML-sjabloon, zodat deze niet 'flikkert' tussen het laden van de bestanden.
    - Bepaal de volgorde `script` waarin uw tags worden opgehaald en uitgevoerd voordat de pagina wordt geladen.
  - E-mailveld `type=email` is nu en mobiele toetsenborden geven de juiste suggesties
  - Ondersteuning voor Chrome vertalen
- Uniforme en zelfverklaarde pagina's
  - De velden gebruikersnaam/e-mail `form` en wachtwoord gebruiken nu het HTML-element om Edge en Internet Explorer (IE) in staat te stellen deze informatie goed op te slaan.

## <a name="110"></a>1.1.0

- Uitzonderingspagina (globale uitzondering)
  - Toegankelijkheidsoplossing
  - Het standaardbericht verwijderd wanneer er geen contactpersoon uit het beleid is opgenomen
  - Standaard CSS verwijderd
- MFA-pagina (multifactor)
  - Knop 'Code bevestigen' verwijderd
  - Het invoerveld voor de code bevat nu slechts invoer tot zes (6) tekens
  - De pagina probeert automatisch de ingevoerde code te verifiëren wanneer een 6-cijferige code wordt ingevoerd, zonder dat er op een knop hoeft te worden geklikt
  - Als de code verkeerd is, wordt het invoerveld automatisch gewist
  - Na drie (3) pogingen met een onjuiste code stuurt B2C een fout terug naar de relying party
  - Toegankelijkheidsoplossingen
  - Standaard CSS verwijderd
- Zelfverklaarde pagina (selfasserted)
  - Waarschuwing verwijderen annuleren
  - CSS-klasse voor foutelementen
  - Foutlogica weergeven/verbergen verbeterd
  - Standaard CSS verwijderd
- Unified SSP (unifiedssp)
  - Toegevoegd houd me aangemeld (KMSI) controle

## <a name="100"></a>1.0.0

- Eerste release

## <a name="next-steps"></a>Volgende stappen

Zie [De gebruikersinterface van uw toepassing aanpassen met een aangepast beleid](custom-policy-ui-customization.md)voor meer informatie over het aanpassen van de gebruikersinterface van uw toepassingen.
