---
title: ContentDefinitions-Azure Active Directory B2C | Microsoft Docs
description: Geef het ContentDefinitions-element van een aangepast beleid in Azure Active Directory B2C op.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3a940c7676a59ed85d5cf16c76f72a12ce0026d5
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77136274"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

U kunt het uiterlijk van elk [zelf bevestigd technisch profiel](self-asserted-technical-profile.md)aanpassen. Azure Active Directory B2C (Azure AD B2C) voert code uit in de browser van uw klant en maakt gebruik van een moderne benadering genoemd cross-Origin Resource Sharing (CORS).

Als u de gebruikers interface wilt aanpassen, geeft u een URL op in het **ContentDefinition** -element met aangepaste HTML-inhoud. In het zelfondertekende technische profiel of **OrchestrationStep**, wijst u naar die inhouds definitie-id. De inhouds definitie kan een **LocalizedResourcesReferences** -element bevatten dat een lijst bevat met gelokaliseerde resources die moeten worden geladen. Azure AD B2C combineert elementen van de gebruikersinterface met de HTML-inhoud die vanaf de URL wordt geladen en presenteert vervolgens de pagina aan de gebruiker.

Het **ContentDefinitions** -element bevat URL'S naar HTML5-sjablonen die kunnen worden gebruikt in de reis van een gebruiker. De HTML5-pagina-URI wordt gebruikt voor een opgegeven gebruikers interface stap. Bijvoorbeeld het aanmelden of aanmelden, het opnieuw instellen van het wacht woord of de fout pagina's. U kunt het uiterlijk wijzigen door de LoadUri voor het HTML5-bestand te overschrijven. U kunt nieuwe inhouds definities maken op basis van uw behoeften. Dit element kan een gelokaliseerde bronnen verwijzing bevatten naar de lokalisatie-id die is opgegeven in het [lokalisatie](localization.md) -element.

In het volgende voor beeld ziet u de definitie-id van de inhoud en de definitie van gelokaliseerde bronnen:

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

De meta gegevens van het zelfondertekende technische profiel voor **LocalAccountSignUpWithLogonEmail** bevatten de inhouds definitie-id **ContentDefinitionReferenceId** ingesteld op `api.localaccountsignup`

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


## <a name="contentdefinition"></a>ContentDefinition

Het element **ContentDefinition** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Ja | Een id voor een inhouds definitie. De waarde is één opgegeven in de sectie **inhouds definitie-id's** verderop op deze pagina. |

Het **ContentDefinition** -element bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | Een teken reeks die de URL van de HTML5-pagina voor de inhouds definitie bevat. |
| RecoveryUri | 0:1 | Een teken reeks die de URL van de HTML-pagina bevat voor het weer geven van een fout met betrekking tot de inhouds definitie. |
| DataUri | 1:1 | Een teken reeks die de relatieve URL bevat van een HTML-bestand dat de gebruikers ervaring biedt voor het aanroepen van de stap. |
| Metagegevens | 1:1 | Een verzameling sleutel-waardeparen die de meta gegevens bevat die worden gebruikt door de inhouds definitie. |
| LocalizedResourcesReferences | 0:1 | Een verzameling gelokaliseerde bronnen verwijzingen. Gebruik dit element om de lokalisatie van een gebruikers interface en claims-kenmerk aan te passen. |

### <a name="datauri"></a>DataUri

Het element **DataUri** wordt gebruikt om de pagina-id op te geven. Azure AD B2C gebruikt de pagina-id om UI-elementen en Java script aan de client zijde te laden en te initiëren. De notatie van de waarde is `urn:com:microsoft:aad:b2c:elements:page-name:version`.  De volgende tabel geeft een lijst van de waarden en beschrijvingen van de pagina-id's die u kunt gebruiken.

| Waarde |   Beschrijving |
| ----- | ----------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | Hiermee wordt een fout pagina weer gegeven wanneer een uitzonde ring of een fout wordt aangetroffen. |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | Een lijst met de id-providers waaruit gebruikers kunnen kiezen tijdens het aanmelden. |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | Geeft een formulier weer voor het aanmelden met een lokaal account dat is gebaseerd op een e-mail adres of een gebruikers naam. Deze waarde biedt ook de functie ' aanmeld functionaliteit blijven ' en ' uw wacht woord verg eten? ' gekoppeld. |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | Geeft een formulier weer voor het aanmelden met een lokaal account dat is gebaseerd op een e-mail adres of een gebruikers naam. |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | Hiermee worden telefoon nummers gecontroleerd met behulp van tekst of spraak tijdens het registreren of aanmelden. |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | Hiermee wordt een formulier weer gegeven waarmee gebruikers hun profiel kunnen maken of bijwerken. |


### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

Het **LocalizedResourcesReferences** -element bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| LocalizedResourcesReference | 1: n | Een lijst met gelokaliseerde resource verwijzingen voor de inhouds definitie. |

Het **LocalizedResourcesReferences** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Taal | Ja | Een teken reeks die een ondersteunde taal voor het beleid bevat per RFC 5646-Tags voor het identificeren van talen. |
| LocalizedResourcesReferenceId | Ja | De id van het **LocalizedResources** -element. |

In het volgende voor beeld ziet u de definitie voor registratie of aanmeldings inhoud:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
</ContentDefinition>
```

In het volgende voor beeld wordt een registratie-of aanmeldings definitie weer gegeven met een verwijzing naar lokalisatie voor Engels, Frans en Spaans:

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

Zie [lokalisatie](localization.md)voor meer informatie over het toevoegen van lokalisatie ondersteuning aan uw inhouds definities.

## <a name="content-definition-ids"></a>Inhouds definitie-Id's

Het kenmerk ID van het element **ContentDefinition** geeft het type pagina aan dat is gekoppeld aan de inhouds definitie. Het element definieert de context waarin een aangepaste HTML5/CSS-sjabloon wordt toegepast. De volgende tabel beschrijft de set met inhouds definitie-Id's die worden herkend door het Framework voor identiteits ervaring en de pagina typen die aan hen zijn gerelateerd. U kunt uw eigen inhouds definities met een wille keurige ID maken.

| Id | Standaard sjabloon | Beschrijving |
| -- | ---------------- | ----------- |
| **API. error** | [uitzonde ring. cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Fout pagina** : er wordt een fout pagina weer gegeven wanneer een uitzonde ring of een fout wordt aangetroffen. |
| **API. idpselections** | [idpSelector. cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | Pagina voor het selecteren van de **identiteits provider** : geeft een lijst van id-providers waaruit gebruikers kunnen kiezen tijdens het aanmelden. De opties zijn doorgaans ondernemings-id-providers, sociale id-providers zoals Facebook en Google + of lokale accounts. |
| **API. idpselections. signup** | [idpSelector. cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Selectie van ID-provider voor registratie** van id-providers die gebruikers kunnen kiezen tijdens het aanmelden. De opties zijn doorgaans ondernemings-id-providers, sociale id-providers zoals Facebook en Google + of lokale accounts. |
| **API. localaccountpasswordreset** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Pagina verg eten wacht woord** : hier wordt een formulier weer gegeven dat gebruikers moeten volt ooien om het opnieuw instellen van een wacht woord te initiëren. |
| **API. localaccountsignin** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | De **aanmeldings pagina van een lokaal account** : geeft een formulier weer voor het aanmelden met een lokaal account dat is gebaseerd op een e-mail adres of een gebruikers naam. Het formulier kan een tekstvak voor tekst invoer en wacht woord bevatten. |
| **API. localaccountsignup** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | De pagina voor het registreren van een **lokaal account** : hier wordt een formulier weer gegeven voor het aanmelden voor een lokaal account dat is gebaseerd op een e-mail adres of een gebruikers naam. Het formulier kan verschillende invoer besturings elementen bevatten, zoals een tekstinvoervak, een vak voor het invoeren van een wacht woord, een keuze rondje, vervolg keuze vakjes en meervoudige selectie vakjes. |
| **API. Phone factor** | [multifactor-1.0.0. cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Multi-factor Authentication-pagina** : verifieert telefoon nummers, met behulp van tekst of spraak tijdens het registreren of aanmelden. |
| **API. selfasserted** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Aanmeldings pagina voor sociaal account** : Hiermee wordt een formulier weer gegeven dat gebruikers moeten volt ooien wanneer ze zich aanmelden met een bestaand account van een sociale ID-provider. Deze pagina is vergelijkbaar met de volgende aanmeldings pagina voor het sociaal account, met uitzonde ring van de velden voor wacht woord invoer. |
| **API. selfasserted. profileupdate** | [updateprofile. cshtml](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Pagina Profiel bijwerken** : hier wordt een formulier weer gegeven dat gebruikers kunnen gebruiken om hun profiel bij te werken. Deze pagina is vergelijkbaar met de registratie pagina voor het sociaal account, met uitzonde ring van de velden voor het invoeren van wacht woorden. |
| **API. signuporsignin** | [Unified. cshtml](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Unified Sign-up-of aanmeldings pagina** : Hiermee wordt het aanmelden en aanmelden door de gebruiker verwerkt. Gebruikers kunnen ondernemings-id-providers, sociale id-providers, zoals Facebook of Google + of lokale accounts gebruiken. |

