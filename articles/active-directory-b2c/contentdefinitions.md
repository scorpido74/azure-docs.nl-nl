---
title: ContentDefinitions
titleSuffix: Azure AD B2C
description: Geef het element ContentDefinities van een aangepast beleid op in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 074a0a39090e22a29f778fc1c99060848c6bfd99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051494"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

U de look en feel van elk [zelfgeclaimd technisch profiel](self-asserted-technical-profile.md)aanpassen. Azure Active Directory B2C (Azure AD B2C) voert code uit in de browser van uw klant en maakt gebruik van een moderne aanpak genaamd Cross-Origin Resource Sharing (CORS).

Als u de gebruikersinterface wilt aanpassen, geeft u een URL op in het element **ContentDefinition** met aangepaste HTML-inhoud. In het zelfverklaarde technische profiel of **OrchestrationStep**wijst u naar die inhoudsdefinitie-id. De inhoudsdefinitie kan een element **LocalizedResourcesReferences** bevatten dat een lijst met gelokaliseerde resources opgeeft die moeten worden geladen. Azure AD B2C combineert elementen van de gebruikersinterface met de HTML-inhoud die vanaf de URL wordt geladen en presenteert vervolgens de pagina aan de gebruiker.

Het element **ContentDefinities** bevat URL's voor HTML5-sjablonen die kunnen worden gebruikt in een gebruikersreis. De URI van de HTML5-pagina wordt gebruikt voor een opgegeven gebruikersinterfacestap. Bijvoorbeeld de aanmelding of aanmelding, wachtwoordreset of foutpagina's. U het uiterlijk wijzigen door de LoadUri voor het HTML5-bestand te overschrijven. U nieuwe inhoudsdefinities maken op basis van uw behoeften. Dit element kan een verwijzing naar gelokaliseerde resources bevatten naar de lokalisatie-id die is opgegeven in het element [Lokalisatie.](localization.md)

In het volgende voorbeeld worden de inhoudsdefinitie-id en de definitie van gelokaliseerde bronnen weergegeven:

```XML
<ContentDefinition Id="api.localaccountsignup">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Local account sign up page</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    ...
```

De metagegevens van het zelfgeclaimde technische profiel **localAccountSignUpWithLogonEmail** bevatten de contentdefinition-id **ContentDefinitionReferenceId** ingesteld op`api.localaccountsignup`

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    ...
  </Metadata>
  ...
```

## <a name="contentdefinition"></a>ContentDefinitie

Het element **ContentDefinition** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Ja | Een id voor een inhoudsdefinitie. De waarde is een waarde die later op deze pagina is opgegeven in de sectie **Id's van inhoudsdefinitie.** |

Het element **ContentDefinition** bevat de volgende elementen:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| LoadUri (LoadUri) | 1:1 | Een tekenreeks die de URL van de HTML5-pagina bevat voor de inhoudsdefinitie. |
| RecoveryUri (RecoveryUri) | 1:1 | Een tekenreeks die de URL van de HTML-pagina bevat voor het weergeven van een fout met betrekking tot de inhoudsdefinitie. Momenteel niet gebruikt, moet `~/common/default_page_error.html`de waarde . |
| DataUri (DataUri) | 1:1 | Een tekenreeks die de relatieve URL van een HTML-bestand bevat die de gebruikerservaring biedt om voor de stap aan te roepen. |
| Metagegevens | 0:1 | Een verzameling sleutel-/waardeparen die de metagegevens bevat die worden gebruikt door de inhoudsdefinitie. |
| Verwijzingen naar gelokaliseerde resources | 0:1 | Een verzameling van gelokaliseerde resources referenties. Gebruik dit element om de lokalisatie van een gebruikersinterface en claimkenmerk aan te passen. |

### <a name="datauri"></a>DataUri (DataUri)

Het **element DataUri** wordt gebruikt om de pagina-id op te geven. Azure AD B2C gebruikt de pagina-id om UI-elementen en JavaScript aan de clientzijde te laden en te starten. De notatie van `urn:com:microsoft:aad:b2c:elements:page-name:version`de waarde is . In de volgende tabel worden de pagina-id's weergegeven die u gebruiken.

| Pagina-id | Beschrijving |
| ----- | ----------- |
| `globalexception` | Hiermee wordt een foutpagina weergegeven wanneer een uitzondering of een fout wordt opgetreden. |
| `providerselection`, `idpselection` | Hier worden de identiteitsproviders weergegeven waaruit gebruikers kunnen kiezen tijdens het aanmelden.  |
| `unifiedssp` | Hiermee wordt een formulier weergegeven voor het aanmelden met een lokaal account dat is gebaseerd op een e-mailadres of een gebruikersnaam. Deze waarde biedt ook de "houd me aanmeldingsfunctionaliteit" en "Uw wachtwoord vergeten?" Link. |
| `unifiedssd` | Hiermee wordt een formulier weergegeven voor het aanmelden met een lokaal account dat is gebaseerd op een e-mailadres of een gebruikersnaam. |
| `multifactor` | Verifieert telefoonnummers met behulp van tekst of spraak tijdens het aanmelden of aanmelden. |
| `selfasserted` | Hiermee wordt een formulier weergegeven om gegevens van een gebruiker te verzamelen. Hiermee kunnen gebruikers bijvoorbeeld hun profiel aanmaken of bijwerken. |

### <a name="select-a-page-layout"></a>Een pagina-indeling selecteren

U [JavaScript-client-side](javascript-samples.md) code `contract` `elements` inschakelen door tussen en het paginatype in te voegen. Bijvoorbeeld `urn:com:microsoft:aad:b2c:elements:contract:page-name:version`.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Het [versiegedeelte](page-layout.md) `DataUri` van het pakket geeft het pakket aan van inhoud dat HTML, CSS en JavaScript bevat voor de gebruikersinterface-elementen in uw beleid. Als u JavaScript-clientcode wilt inschakelen, moeten de elementen waarop u uw JavaScript baseert onveranderlijk zijn. Als ze niet onveranderlijk zijn, kunnen wijzigingen onverwacht gedrag veroorzaken op uw gebruikerspagina's. Als u deze problemen wilt voorkomen, dwingt u het gebruik van een pagina-indeling af en geeft u een pagina-indelingsversie op. Dit zorgt ervoor dat alle inhoudsdefinities waarop u uw JavaScript hebt gebaseerd, onveranderlijk zijn. Zelfs als u niet van plan bent JavaScript in te schakelen, moet u nog steeds de pagina-indelingsversie voor uw pagina's opgeven.

In het volgende voorbeeld `selfasserted` ziet `1.2.0`u de **DataUri** van de versie:

```xml
<ContentDefinition Id="api.localaccountpasswordreset">
<LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
<RecoveryUri>~/common/default_page_error.html</RecoveryUri>
<DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
<Metadata>
    <Item Key="DisplayName">Local account change password page</Item>
</Metadata>
</ContentDefinition>
```

#### <a name="migrating-to-page-layout"></a>Migreren naar pagina-indeling

De notatie van de `contract`waarde moet het woord bevatten: _urn:com:microsoft:aad:b2c:elementen:**contract**:pagenaam:versie_. Als u een pagina-indeling wilt opgeven in uw aangepaste beleid dat een oude **DataUri-waarde** gebruikt, gebruikt u de volgende tabel om te migreren naar de nieuwe indeling.

| Oude DataUri-waarde | Nieuwe DataUri-waarde |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0` |


### <a name="metadata"></a>Metagegevens

Een **element metagegevens** bevat de volgende elementen:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Item | 0:n | De metagegevens die betrekking hebben op de inhoudsdefinitie. |

Het **item-element** van het element **Metagegevens** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Sleutel | Ja | De metadata sleutel.  |

#### <a name="metadata-keys"></a>Metagegevenstoetsen

Inhoudsdefinitie ondersteunt volgende metagegevens:

| Sleutel | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| DisplayName | Nee | Een tekenreeks die de naam van de inhoudsdefinitie bevat. |

### <a name="localizedresourcesreferences"></a>Verwijzingen naar gelokaliseerde resources

Het element **LocalizedResourcesReferences** bevat de volgende elementen:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Referentiepunt gelokaliseerde resources | 1:n | Een lijst met gelokaliseerde resourceverwijzingen voor de inhoudsdefinitie. |

Het element **LocalizedResourcesReference** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Taal | Ja | Een tekenreeks die een ondersteunde taal bevat voor het beleid per RFC 5646 - Tags voor het identificeren van talen. |
| Referentie-id gelokaliseerde resources | Ja | De id van het element **Gelokaliseerde resources.** |

In het volgende voorbeeld wordt een definitie van aanmeldings- of aanmeldingsinhoud weergegeven met een verwijzing naar lokalisatie voor Engels, Frans en Spaans:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
    <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="api.signuporsignin.rf" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
</LocalizedResourcesReferences>
</ContentDefinition>
```

Zie [Lokalisatie](localization.md)voor meer informatie over het toevoegen van lokalisatieondersteuning aan uw inhoudsdefinities.

## <a name="content-definition-ids"></a>Inhoudsdefinitie-iD's

Het kenmerk ID van het element **ContentDefinition** geeft het type pagina op dat betrekking heeft op de inhoudsdefinitie. Het element definieert de context die een aangepaste HTML5/CSS-sjabloon gaat toepassen. In de volgende tabel worden de set id's voor inhoudsdefinitie beschreven die wordt herkend door het Identity Experience Framework en de paginatypen die daarop betrekking hebben. U uw eigen inhoudsdefinities maken met een willekeurige id.

| Id | Standaardsjabloon | Beschrijving |
| -- | ---------------- | ----------- |
| **api.fout** | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Foutpagina** - Geeft een foutpagina weer wanneer een uitzondering of een fout wordt opgetreden. |
| **api.idpselections** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Selectiepagina identiteitsprovider** - Geeft een lijst van identiteitsproviders waaruit gebruikers kunnen kiezen tijdens het aanmelden. De opties zijn meestal aanbieders van bedrijfsidentiteiten, aanbieders van sociale identiteit, zoals Facebook en Google+, of lokale accounts. |
| **api.idpselections.signup** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Selectie van identiteitsproviders voor aanmelden** - Geeft een overzicht van identiteitsproviders waaruit gebruikers kunnen kiezen tijdens het aanmelden. De opties zijn meestal aanbieders van bedrijfsidentiteiten, aanbieders van sociale identiteit, zoals Facebook en Google+, of lokale accounts. |
| **api.localaccountpasswordreset** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Wachtwoordpagina vergeten** - Hiermee geeft u een formulier weer dat gebruikers moeten invullen om een wachtwoordreset te starten. |
| **api.localaccountsignin** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Aanmeldingspagina voor lokale accounts** - geeft een formulier weer om u aan te melden met een lokaal account dat is gebaseerd op een e-mailadres of een gebruikersnaam. Het formulier kan een tekstinvoervak en een wachtwoordinvoervak bevatten. |
| **api.localaccountsignup** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Aanmeldingspagina voor lokale accounts** - geeft een formulier weer voor het aanmelden voor een lokaal account dat is gebaseerd op een e-mailadres of een gebruikersnaam. Het formulier kan verschillende invoerbesturingselementen bevatten, zoals: een tekstinvoervak, een wachtwoordinvoervakje, een keuzerondje, vervolgkeuzevakken met één keuze optie en selectievakjes voor meerdere knoppen. |
| **api.phonefactor** | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Pagina voor meervoudige verificatie** - Verifieert telefoonnummers, met behulp van tekst of spraak, tijdens het aanmelden of aanmelden. |
| **api.selfasserted** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Aanmeldingspagina voor sociale account** - Geeft een formulier weer dat gebruikers moeten invullen wanneer ze zich aanmelden met een bestaand account van een aanbieder van sociale identiteit. Deze pagina is vergelijkbaar met de vorige aanmeldingspagina voor sociale wachtwoorden, met uitzondering van de velden voor wachtwoordinvoer. |
| **api.selfasserted.profileupdate** | [updateprofile.cshtml](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Pagina met profielupdate** - Geeft een formulier weer waarmee gebruikers toegang hebben om hun profiel bij te werken. Deze pagina is vergelijkbaar met de aanmeldingspagina voor sociale wachtwoorden, met uitzondering van de invoervelden voor wachtwoorden. |
| **api.signuporsignin** | [unified.cshtml](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Uniforme aanmeldings- of aanmeldingspagina** - Hiermee wordt het aanmeldings- en aanmeldingsproces van de gebruiker verwerkt. Gebruikers kunnen zakelijke identiteitsproviders, aanbieders van sociale identiteit, zoals Facebook of Google+, of lokale accounts gebruiken. |

## <a name="next-steps"></a>Volgende stappen

Zie voor een voorbeeld van het aanpassen van de gebruikersinterface met behulp van inhoudsdefinities:

[De gebruikersinterface van uw toepassing aanpassen met een aangepast beleid](custom-policy-ui-customization.md)
