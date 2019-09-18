---
title: Selecteer een pagina-indeling-Azure Active Directory B2C
description: Meer informatie over het selecteren van een pagina-indeling in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 612d2e3a9a5a324f7d6d8e1b63b6b7e297047239
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063843"
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

Als u een pagina-indeling wilt selecteren, wijzigt u de **DataUri** -waarden in uw [ContentDefinitions](contentdefinitions.md) in uw beleid. Door het overschakelen van de oude **gegevens-URI die** waarden naar de nieuwe waarden, bent u een onveranderbaar pakket te selecteren. Het voordeel van het gebruik van dit pakket is dat u weet het wordt niet wijzigen en leiden tot onverwacht gedrag op de pagina.

Als u een pagina-indeling wilt instellen, gebruikt u de volgende tabel om **DataUri** -waarden te vinden.

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
