---
title: Selecteer een pagina-indeling-Azure Active Directory B2C
description: Meer informatie over het selecteren van een pagina-indeling in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b8bf44893bf23502aaf8c446d9e6d7c9022bfce3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425643"
---
# <a name="select-a-page-layout-in-azure-active-directory-b2c-using-custom-policies"></a>Selecteer een pagina-indeling in Azure Active Directory B2C aangepaste beleids regels gebruiken

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

U kunt Java script-client-side code inschakelen in het beleid voor Azure Active Directory B2C (Azure AD B2C), ongeacht of u gebruikers stromen of aangepaste beleids regels gebruikt. Als u Java script wilt inschakelen voor uw toepassingen, moet u een element toevoegen aan uw [aangepaste beleid](active-directory-b2c-overview-custom.md), een pagina-indeling selecteren en [b2clogin.com](b2clogin.md) in uw aanvragen gebruiken.

Een pagina-indeling is een koppeling van elementen die Azure AD B2C biedt en de inhoud die u opgeeft.

In dit artikel wordt beschreven hoe u een pagina-indeling selecteert in Azure AD B2C door deze te configureren in een aangepast beleid.

> [!NOTE]
> Als u Java script wilt inschakelen voor gebruikers stromen, raadpleegt u [Java script en pagina-indelings versies in azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="replace-datauri-values"></a>Gegevens-URI die waarden vervangen

In uw aangepaste beleidsregels, hebt u mogelijk [ContentDefinitions](contentdefinitions.md) waarmee de HTML-sjablonen gebruikt in de gebruikersbeleving worden gedefinieerd. De **ContentDefinition** bevat een **gegevens-URI die** die verwijst naar de pagina-elementen die is geleverd door Azure AD B2C. De **LoadUri** is het relatieve pad naar de HTML en CSS-inhoud die u opgeeft.

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

Als u een pagina-indeling wilt selecteren, wijzigt u de **DataUri** -waarden in uw [ContentDefinitions](contentdefinitions.md) in uw beleid. Door het overschakelen van de oude **gegevens-URI die** waarden naar de nieuwe waarden, bent u een onveranderbaar pakket te selecteren. Het voor deel van het gebruik van dit pakket is dat u weet dat het niet wordt gewijzigd en dat uw pagina onverwacht gedrag veroorzaakt.

Als u een pagina-indeling wilt opgeven in uw aangepaste beleids regels die gebruikmaken van een oude **DataUri** -waarde, voegt u `contract` in tussen `elements` en het pagina Type (bijvoorbeeld `selfasserted`) en geeft u het versie nummer op. Bijvoorbeeld:

| Oude gegevens-URI die waarde | Nieuwe gegevens-URI die waarde |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0` |

## <a name="version-change-log"></a>Logboek van versie wijzigingen

Pagina-indelings pakketten worden regel matig bijgewerkt met oplossingen en verbeteringen in hun pagina-elementen. In het volgende wijzigingslog bestand worden de wijzigingen aangegeven die in elke versie zijn geïntroduceerd.

### <a name="200"></a>2.0.0

- Zelfbevestigende pagina (`selfasserted`)
  - Er is ondersteuning toegevoegd voor [besturings elementen voor weer gave](display-controls.md) in aangepast beleid.

### <a name="120"></a>1.2.0

- Alle pagina's
  - Toegankelijkheids oplossingen
  - U kunt nu het kenmerk `data-preload="true"` toevoegen in uw HTML-tags om de laad volgorde voor CSS en Java script te bepalen. Scenario's zijn onder andere:
    - Gebruik dit op uw CSS-koppeling om de CSS op hetzelfde moment als uw HTML te laden zodat het niet ' Flik keren ' is tussen het laden van de bestanden
    - Met dit kenmerk kunt u de volg orde bepalen waarin uw script tags worden opgehaald en uitgevoerd voordat de pagina wordt geladen
  - Het veld e-mail is nu `type=email` en mobiele toetsen borden bieden de juiste suggesties
  - Ondersteuning voor Chrome-vertaling
- Geïntegreerde en zelfbevestigende pagina
  - De velden gebruikers naam/e-mail adres en wacht woord gebruiken nu het HTML-element van het formulier.  Hierdoor kan Edge en Internet Explorer deze gegevens op de juiste manier opslaan

### <a name="110"></a>1.1.0

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

### <a name="100"></a>1.0.0

- Eerste release

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe u de gebruikersinterface van uw toepassingen kunt aanpassen [aanpassen van de gebruikersinterface van uw toepassing met behulp van een aangepast beleid in Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
