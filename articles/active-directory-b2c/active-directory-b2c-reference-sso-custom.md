---
title: Beheer van sessie voor eenmalige aanmelding met aangepaste beleids regels
titleSuffix: Azure AD B2C
description: Meer informatie over het beheren van SSO-sessies met aangepaste beleids regels in Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ee32b13820cb50fc1649672b78b34e7e293d65b5
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950490"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Sessie beheer voor eenmalige aanmelding in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Met eenmalige aanmelding (SSO)-sessie beheer in Azure Active Directory B2C (Azure AD B2C) kan een beheerder de interactie met een gebruiker beheren nadat de gebruiker al is geverifieerd. De beheerder kan bijvoorbeeld bepalen of de selectie van id-providers wordt weer gegeven of dat de gegevens van het lokale account opnieuw moeten worden ingevoerd. In dit artikel wordt beschreven hoe u de SSO-instellingen voor Azure AD B2C kunt configureren.

SSO-sessie beheer bestaat uit twee delen. De eerste behandelt de interactie van de gebruiker rechtstreeks met Azure AD B2C en de andere deals met de interactie van de gebruiker met externe partijen, zoals Facebook. Azure AD B2C geen SSO-sessies die mogelijk door externe partijen worden bewaard, overschrijft of overs Laan. In plaats daarvan wordt de route door Azure AD B2C om naar de externe partij te gaan ' onthouden ', zodat de gebruiker niet opnieuw wordt gevraagd om hun sociale of zakelijke ID-provider te selecteren. De ultieme SSO-beslissing blijft bij de externe partij.

SSO-sessie beheer maakt gebruik van dezelfde semantiek als andere technische profielen in aangepast beleid. Wanneer een Orchestration-stap wordt uitgevoerd, wordt het technische profiel dat is gekoppeld aan de stap, opgevraagd voor een `UseTechnicalProfileForSessionManagement` verwijzing. Als dat het geval is, wordt de SSO-sessie provider waarnaar wordt verwezen, gecontroleerd om te zien of de gebruiker een deel nemer voor de sessie is. Als dit het geval is, wordt de SSO-sessie provider gebruikt om de sessie opnieuw in te vullen. Op dezelfde manier wordt de provider gebruikt voor het opslaan van gegevens in de sessie als er een SSO-sessie provider is opgegeven, wanneer de uitvoering van een Orchestration-stap is voltooid.

Azure AD B2C heeft een aantal SSO-sessie providers gedefinieerd die kunnen worden gebruikt:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

SSO-beheer klassen worden opgegeven met behulp van het `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />`-element van een technisch profiel.

## <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Zoals de naam bepaalt, heeft deze provider niets. Deze provider kan worden gebruikt om SSO-gedrag voor een specifiek technisch profiel te onderdrukken.

## <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Deze provider kan worden gebruikt voor het opslaan van claims in een sessie. Er wordt doorgaans verwezen naar deze provider in een technisch profiel dat wordt gebruikt voor het beheren van lokale accounts. Wanneer u de DefaultSSOSessionProvider gebruikt om claims op te slaan in een sessie, moet u ervoor zorgen dat claims die moeten worden teruggestuurd naar de toepassing of worden gebruikt door voor waarden in volgende stappen, worden opgeslagen in de sessie of worden uitgebreid door een lees bewerking van het gebruikers profiel in uitvoermap. Zo zorgt u ervoor dat de verificatie traject niet kan worden uitgevoerd op ontbrekende claims.

```XML
<TechnicalProfile Id="SM-AAD">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="newUser" />
        <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
    </PersistedClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true" />
    </OutputClaims>
</TechnicalProfile>
```

Gebruik het `<PersistedClaims>`-element van het technische profiel om claims toe te voegen aan de sessie. Wanneer de provider wordt gebruikt om de sessie opnieuw in te vullen, worden de permanente claims toegevoegd aan de claim verzameling. `<OutputClaims>` wordt gebruikt voor het ophalen van claims uit de sessie.

## <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Deze provider wordt gebruikt om het scherm ' ID-provider kiezen ' te onderdrukken. Er wordt meestal verwezen naar een technisch profiel dat is geconfigureerd voor een externe ID-provider, zoals Facebook.

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Deze provider wordt gebruikt voor het beheren van de Azure AD B2C SAML-sessies tussen apps en externe SAML-id-providers.

```XML
<TechnicalProfile Id="SM-Reflector-SAML">
    <DisplayName>Session Management Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="IncludeSessionIndex">false</Item>
        <Item Key="RegisterServiceProviders">false</Item>
    </Metadata>
</TechnicalProfile>
```

Het technische profiel bevat twee meta gegevens items:

| Item | Default Value | Mogelijke waarden | Beschrijving
| --- | --- | --- | --- |
| IncludeSessionIndex | waar | waar/onwaar | Hiermee wordt de provider aangegeven dat de sessie-index moet worden opgeslagen. |
| RegisterServiceProviders | waar | waar/onwaar | Geeft aan dat de provider alle SAML-service providers moet registreren waarvoor een bevestiging is verleend. |

Wanneer u de provider gebruikt voor het opslaan van een SAML ID-provider sessie, moeten de bovenstaande items beide onwaar zijn. Wanneer u de provider gebruikt voor het opslaan van de B2C SAML-sessie, moeten de bovenstaande items waar zijn of worden wegge laten, omdat de standaard waarden waar zijn. Voor het afmelden van SAML-sessies moet `SessionIndex` en `NameID` zijn voltooid.

