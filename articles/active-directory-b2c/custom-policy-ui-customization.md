---
title: De gebruikersinterface van uw app aanpassen met een aangepast beleid
titleSuffix: Azure AD B2C
description: Meer informatie over het aanpassen van een gebruikersinterface met een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e50d6d0623e87dfa68a7cc9744c3f595ff0179c6
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396383"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>De gebruikersinterface van uw toepassing aanpassen met een aangepast beleid in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Door de stappen in dit artikel te voltooien, maakt u een aanmeldings- en aanmeldingsbeleid met uw merk en uiterlijk. Met Azure Active Directory B2C (Azure AD B2C) krijgt u bijna volledige controle over de HTML- en CSS-inhoud die aan gebruikers wordt gepresenteerd. Wanneer u een aangepast beleid gebruikt, configureert u aanpassing van de gebruikersinterface in XML in plaats van besturingselementen in de Azure-portal te gebruiken.

## <a name="prerequisites"></a>Vereisten

Voer de stappen uit in [Aan de slag met aangepast beleid](custom-policy-get-started.md). U moet een werkend aangepast beleid hebben voor aanmelden en aanmelden met lokale accounts.

[!INCLUDE [active-directory-b2c-html-how-to](../../includes/active-directory-b2c-html-how-to.md)]

### <a name="4-modify-the-extensions-file"></a>4. Het extensiesbestand wijzigen

Als u aanpassing aan de gebruikersinterface wilt configureren, kopieert u de **ContentDefinition** en de onderliggende elementen ervan vanuit het basisbestand naar het extensiebestand.

1. Open het basisbestand van uw beleid. Bijvoorbeeld. <em> `SocialAndLocalAccounts/` </em> Dit basisbestand is een van de beleidsbestanden die zijn opgenomen in het aangepaste startpakket voor beleid, dat u in de vereiste had moeten verkrijgen, [Aan de slag met aangepast beleid](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom).
1. De volledige inhoud van het element **ContentDefinities** zoeken en kopiëren.
1. Open het extensiebestand. *Bijvoorbeeld TrustFrameworkExtensions.xml*. Zoek naar het element **Bouwstenen.** Als het element niet bestaat, voeg je het toe.
1. Plak de volledige inhoud van het element **ContentDefinities** dat u als onderliggend element van het element **Bouwstenen** hebt gekopieerd.
1. Zoek naar het element `Id="api.signuporsignin"` **ContentDefinition** dat in de XML die u hebt gekopieerd, bevat.
1. Wijzig de waarde van **LoadUri** in de URL van het HTML-bestand dat u hebt geüpload naar de opslag. Bijvoorbeeld `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Uw aangepaste beleid moet er uitzien als het volgende codefragment:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Sla het extensiesbestand op.

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. Upload en test uw bijgewerkte aangepaste beleid

#### <a name="51-upload-the-custom-policy"></a>5.1 Het aangepaste beleid uploaden

1. Zorg ervoor dat u de map met uw Azure AD B2C-tenant gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren en de map te kiezen die uw tenant bevat.
1. Zoeken naar en selecteer **Azure AD B2C**.
1. Selecteer **onder Beleid**het Framework Voor **identiteitservaring**.
1. Selecteer **Aangepast beleid uploaden**.
1. Upload het extensiesbestand dat u eerder hebt gewijzigd.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5.2 Het aangepaste beleid testen met **Nu uitvoeren**

1. Selecteer het beleid dat u hebt geüpload en selecteer **Nu uitvoeren**.
1. Je moet je kunnen aanmelden met een e-mailadres.

[!INCLUDE [active-directory-b2c-html-templates](../../includes/active-directory-b2c-html-templates.md)]

## <a name="configure-dynamic-custom-page-content-uri"></a>Dynamische aangepaste pagina-inhoud URI configureren

Met het aangepaste Azure AD B2C-beleid u een parameter verzenden in het URL-pad of een querytekenreeks. Door de parameter door te geven aan uw HTML-eindpunt, kunt u de pagina-inhoud dynamisch wijzigen. U kunt bijvoorbeeld de achtergrondafbeelding op de registratie- of aanmeldingspagina van Azure AD B2C wijzigen, op basis van een parameter die u doorgeeft vanuit uw web- of mobiele toepassing. De parameter kan elke [claimresolver](claim-resolver-overview.md)zijn, zoals de toepassings-id, taal-id of aangepaste querytekenreeksparameter, zoals `campaignId`.

### <a name="sending-query-string-parameters"></a>Querytekenreeksparameters verzenden

Als u querytekenreeksparameters wilt verzenden, voegt `ContentDefinitionParameters` u in het beleid van de [relying party](relyingparty.md)een element toe zoals hieronder wordt weergegeven.

```XML
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        <Parameter Name="lang">{Culture:LanguageName}</Parameter>
        <Parameter Name="appId">{OIDC:ClientId}</Parameter>
    </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ...
</RelyingParty>
```

Wijzig in uw inhoudsdefinitie `LoadUri` `https://<app_name>.azurewebsites.net/home/unified`de waarde van . Uw aangepaste `ContentDefinition` beleid moet er uitzien als het volgende codefragment:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Wanneer Azure AD B2C de pagina laadt, wordt er gebeld naar het eindpunt van uw webserver:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>Uri dynamische pagina-inhoud

Inhoud kan worden getrokken uit verschillende plaatsen op basis van de gebruikte parameters. Stel in uw eindpunt met CORS een mapstructuur in om inhoud te hosten. U de inhoud bijvoorbeeld in de volgende structuur ordenen. *Hoofdmap/map per taal/uw html-bestanden*. Uw aangepaste pagina URI kan er bijvoorbeeld uitzien als:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C verzendt de ISO-code `fr` met twee letters voor de taal, voor Frans:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="next-steps"></a>Volgende stappen

Zie [naslaggids voor aanpassing van de gebruikersinterface voor gebruikersstromen voor](customize-ui-overview.md)meer informatie over ui-elementen die kunnen worden aangepast.
