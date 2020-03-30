---
title: Gebruikerstoegang beheren in Azure Active Directory B2C | Microsoft Documenten
description: Meer informatie over het identificeren van minderjarigen, het verzamelen van geboortedatum- en land-/regiogegevens en het accepteren van gebruiksvoorwaarden in uw toepassing met Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f04a3fea3801f917a3ae4aced04ef3824d1cfa82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184516"
---
# <a name="manage-user-access-in-azure-active-directory-b2c"></a>Gebruikerstoegang beheren in Azure Active Directory B2C

In dit artikel wordt besproken hoe u gebruikerstoegang tot uw toepassingen beheert met Azure Active Directory B2C (Azure AD B2C). Toegangsbeheer in uw toepassing omvat:

- Identificeren van minderjarigen en het beheren van de toegang van gebruikers tot uw toepassing.
- Ouderlijke toestemming vereisen voor minderjarigen om uw toepassingen te gebruiken.
- Het verzamelen van geboorte- en land-/regiogegevens van gebruikers.
- Het vastleggen van een gebruiksvoorwaardenovereenkomst en gating-toegang.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="control-minor-access"></a>Kleine toegang beheren

Toepassingen en organisaties kunnen besluiten minderjarigen te blokkeren voor het gebruik van toepassingen en services die niet zijn gericht op deze doelgroep. Als alternatief kunnen aanvragen en organisaties besluiten minderjarigen te accepteren en vervolgens de ouderlijke toestemming te beheren, en toegestane ervaringen te bieden voor minderjarigen, zoals bepaald door bedrijfsregels en toegestaan door regelgeving.

Als een gebruiker als minderjarige wordt geïdentificeerd, u de gebruikersstroom in Azure AD B2C instellen op een van de drie opties:

- **Stuur een ondertekende JWT-id_token terug naar de toepassing:** De gebruiker wordt geregistreerd in de directory en een token wordt teruggestuurd naar de toepassing. De toepassing gaat dan door het toepassen van bedrijfsregels. De aanvraag kan bijvoorbeeld doorgaan met een procedure voor ouderlijke toestemming. Als u deze methode wilt gebruiken, kiest u ervoor om de **leeftijdsgroep** en **de toestemmingprovidedForMinor-claims** van de toepassing te ontvangen.

- **Stuur een niet-ondertekend JSON-token naar de toepassing:** Azure AD B2C waarschuwt de toepassing dat de gebruiker minderjarig is en geeft de status van de ouderlijke toestemming van de gebruiker. De toepassing gaat dan door het toepassen van bedrijfsregels. Een JSON-token voltooit geen succesvolle verificatie met de toepassing. De toepassing moet de niet-geverifieerde gebruiker verwerken volgens de claims in het JSON-token, waaronder **naam,** **e-mail,** **leeftijdsgroep**en **consentProvidedForMinor**.

- **De gebruiker blokkeren:** als een gebruiker minderjarig is en er geen ouderlijke toestemming is verleend, kan Azure AD B2C de gebruiker laten weten dat deze persoon is geblokkeerd. Er wordt geen token uitgegeven, de toegang wordt geblokkeerd en het gebruikersaccount wordt niet gemaakt tijdens een registratiereis. Om deze melding te implementeren, biedt u een geschikte HTML/CSS-inhoudspagina om de gebruiker te informeren en de juiste opties te presenteren. De aanvraag voor nieuwe registraties is niet nodig.

## <a name="get-parental-consent"></a>Ouderlijke toestemming krijgen

Afhankelijk van de toepassingsverordening moet ouderlijke toestemming mogelijk worden verleend door een gebruiker die als volwassene is geverifieerd. Azure AD B2C biedt geen ervaring om de leeftijd van een persoon te verifiëren en vervolgens een geverifieerde volwassene toe te staan ouderlijke toestemming te verlenen aan een minderjarige. Deze ervaring moet worden verstrekt door de toepassing of een andere dienstverlener.

Het volgende is een voorbeeld van een gebruikersstroom voor het verzamelen van ouderlijke toestemming:

1. Een [Microsoft Graph API-bewerking](https://docs.microsoft.com/graph/use-the-api) identificeert de gebruiker als een minderjarige en retourneert de gebruikersgegevens naar de toepassing in de vorm van een niet-ondertekend JSON-token.

2. De toepassing verwerkt het JSON-token en toont een scherm aan de minderjarige, waarbij hen wordt op de hoogte gebracht dat ouderlijke toestemming vereist is en online toestemming van een ouder wordt gevraagd.

3. Azure AD B2C toont een aanmeldingstraject waarop de gebruiker zich normaal kan aanmelden en geeft een token uit aan de toepassing die is ingesteld op **legalAgeGroupClassification = "minorWithParentalConsent".** De toepassing verzamelt het e-mailadres van de ouder en controleert of de ouder een volwassene is. Hiervoor wordt een vertrouwde bron gebruikt, zoals een nationaal id-kantoor, licentieverificatie of creditcardbewijs. Als de verificatie is geslaagd, vraagt de toepassing de minderjarige zich aan te melden met behulp van de Azure AD B2C-gebruikersstroom. Als toestemming wordt geweigerd (bijvoorbeeld als **legalAgeGroupClassification = "minorWithoutParentalConsent"**) wordt geweigerd), retourneert Azure AD B2C een JSON-token (geen login) naar de toepassing om het toestemmingsproces opnieuw te starten. Het is optioneel mogelijk om de gebruikersstroom aan te passen, zodat een minderjarige of een volwassene weer toegang heeft tot het account van een minderjarige door een registratiecode te sturen naar het e-mailadres van de minderjarige of het e-mailadres van de volwassene.

4. De applicatie biedt een optie aan de minderjarige om de toestemming in te trekken.

5. Wanneer de minderjarige of de volwassene de toestemming intrekt, kan de Microsoft Graph API worden gebruikt om toestemming te **wijzigenMitsForMinor** om **te weigeren**. Als alternatief kan de toepassing ervoor kiezen om een minderjarige te verwijderen waarvan de toestemming is ingetrokken. Het is optioneel mogelijk om de gebruikersstroom aan te passen, zodat de geverifieerde minderjarige (of ouder die het account van de minderjarige gebruikt) de toestemming kan intrekken. Azure AD B2C registreert **toestemmingProvidedForMinor** zoals **geweigerd**.

Zie [Gebruikersbrontype](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/user)voor meer informatie over **legalAgeGroupClassification**, **consentProvidedForMinor**en **ageGroup**. Zie [Aangepaste kenmerken gebruiken om informatie over uw consumenten te verzamelen](user-flow-custom-attributes.md)voor meer informatie over aangepaste kenmerken. Wanneer u uitgebreide kenmerken aankaart met de Microsoft Graph API, moet u de lange versie van het kenmerk gebruiken, zoals *extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth:* *2011-01-01T00:00:00Z*.

## <a name="gather-date-of-birth-and-countryregion-data"></a>Gegevens over de geboortedatum en land/regio verzamelen

Toepassingen kunnen vertrouwen op Azure AD B2C om de geboortedatum (DOB) en land/regio-informatie van alle gebruikers te verzamelen tijdens de registratie. Als deze informatie nog niet bestaat, kan de toepassing deze aanvragen bij de gebruiker tijdens de volgende verificatie (aanmeldingstraject). Gebruikers kunnen niet verder gaan zonder hun DOB en land/regio informatie te verstrekken. Azure AD B2C gebruikt de informatie om te bepalen of de persoon als minderjarig wordt beschouwd volgens de wettelijke normen van dat land/regio.

Een aangepaste gebruikersstroom kan dob- en land-/regio-informatie verzamelen en Azure AD B2C-claimtransformatie gebruiken om de **leeftijdsgroep** te bepalen en het resultaat (of de dob- en land-/regio-informatie rechtstreeks) in de directory voort te houden.

In de volgende stappen wordt de logica weergegeven die wordt gebruikt om **leeftijdsgroepen** te berekenen vanaf de geboortedatum van de gebruiker:

1. Probeer het land te vinden op de landcode in de lijst. Als het land niet wordt gevonden, valt u terug naar **Standaard**.

2. Als het **minorconsent-knooppunt** aanwezig is in het landelement:

    a. Bereken de datum waarop de gebruiker geboren moet zijn om als een volwassene te worden beschouwd. Als de huidige datum bijvoorbeeld 14 maart 2015 is en **MinorConsent** 18 is, moet de geboortedatum niet later zijn dan 14 maart 2000.

    b. Vergelijk de minimale geboortedatum met de werkelijke geboortedatum. Als de minimale geboortedatum vóór de geboortedatum van de gebruiker ligt, wordt de berekening **minor** geretourneerd als de berekening van de leeftijdsgroep.

3. Als het **minornoconsentvereist** knooppunt aanwezig is in het landelement, herhaalt u de stappen 2a en 2b met de waarde van **MinorNoConsentRequired.** De output van 2b retourneert **MinorNoConsentRequired** als de minimale geboortedatum vóór de geboortedatum van de gebruiker ligt.

4. Als geen van beide berekeningen true oplevert, wordt bij de berekening **de berekening als resultaat Voor volwassenen**.

Als een toepassing op betrouwbare wijze DOB- of land-/regiogegevens heeft verzameld met andere methoden, kan de toepassing de Graph API gebruiken om de gebruikersrecord met deze informatie bij te werken. Bijvoorbeeld:

- Als bekend is dat een gebruiker een volwassene is, werkt u de **leeftijdsgroep** van het directorykenmerk bij met een waarde van **Adult**.
- Als bekend is dat een gebruiker een minderjarige is, werkt u de **leeftijdsgroep** van het directorykenmerk bij met een waarde van **Minor** en stelt u **toestemming providedForMinor**in, naar gelang van het geval.

Zie [Leeftijd gating gebruiken in Azure AD B2C](basic-age-gating.md)voor meer informatie over het verzamelen van DOB-gegevens.

## <a name="capture-terms-of-use-agreement"></a>Gebruiksvoorwaarden vastleggen

Wanneer u uw toepassing ontwikkelt, legt u gewoonlijk de acceptatie van gebruiksvoorwaarden van gebruikers binnen hun toepassingen vast zonder of slechts kleine deelname van de gebruikersmap. Het is echter mogelijk om een Azure AD B2C-gebruikersstroom te gebruiken om de acceptatievoorwaarden van een gebruiker te verzamelen, de toegang te beperken als de acceptatie niet wordt toegestaan en de acceptatie van toekomstige wijzigingen in de gebruiksvoorwaarden af te dwingen, op basis van de datum van de laatste acceptatie en de datum van de laatste versie van de gebruiksvoorwaarden.

**Gebruiksvoorwaarden** kunnen ook 'Toestemming geven om gegevens met derden te delen' bevatten. Afhankelijk van de lokale regelgeving en bedrijfsregels u de acceptatie van beide voorwaarden door een gebruiker samen verzamelen, of u de gebruiker toestaan de ene voorwaarde te accepteren en niet de andere.

In de volgende stappen wordt beschreven hoe u de gebruiksvoorwaarden beheren:

1. Noteer de acceptatie van de gebruiksvoorwaarden en de datum van aanvaarding met behulp van de Graph API en uitgebreide kenmerken. U dit doen door gebruik te maken van zowel ingebouwde als aangepaste gebruikersstromen. We raden u aan de **extension_termsOfUseConsentDateTime** en extension_termsOfUseConsentVersion kenmerken te maken en **te** gebruiken.

2. Maak een vereist selectievakje met het label 'Gebruiksvoorwaarden accepteren' en neem het resultaat op tijdens het aanmelden. U dit doen door gebruik te maken van zowel ingebouwde als aangepaste gebruikersstromen.

3. Azure AD B2C slaat de gebruiksvoorwaardenovereenkomst en de acceptatie van de gebruiker op. U de Graph API gebruiken om op te vragen voor de status van elke gebruiker door het extensiekenmerk te lezen dat wordt gebruikt om het antwoord op te nemen (lees bijvoorbeeld **termenOfUseTestDateDateTime).** U dit doen door gebruik te maken van zowel ingebouwde als aangepaste gebruikersstromen.

4. Acceptatie van bijgewerkte gebruiksvoorwaarden vereisen door de datum van aanvaarding te vergelijken met de datum van de laatste versie van de gebruiksvoorwaarden. U de datums alleen vergelijken met behulp van een aangepaste gebruikersstroom. Gebruik het uitgebreide kenmerk **extension_termsOfUseConsentDateTime**en vergelijk de waarde met de claim van **termsOfUseTextUpdateDateTime**. Als de acceptatie oud is, forceer dan een nieuwe acceptatie door een zelfverklaard scherm weer te geven. Anders blokkeert u de toegang met behulp van beleidslogica.

5. Acceptatie van bijgewerkte gebruiksvoorwaarden vereisen door het versienummer van de aanvaarding te vergelijken met het meest recente geaccepteerde versienummer. U versienummers alleen vergelijken met behulp van een aangepaste gebruikersstroom. Gebruik het uitgebreide kenmerk **extension_termsOfUseConsentDateTime**en vergelijk de waarde met de claim van **extension_termsOfUseConsentVersion**. Als de acceptatie oud is, forceer dan een nieuwe acceptatie door een zelfverklaard scherm weer te geven. Anders blokkeert u de toegang met behulp van beleidslogica.

U acceptatievoorwaarden vastleggen in de volgende scenario's:

- Een nieuwe gebruiker meldt zich aan. De gebruiksvoorwaarden worden weergegeven en het acceptatieresultaat wordt opgeslagen.
- Een gebruiker meldt zich aan die eerder de meest recente of actieve gebruiksvoorwaarden heeft geaccepteerd. De gebruiksvoorwaarden worden niet weergegeven.
- Een gebruiker meldt zich aan die de laatste of actieve gebruiksvoorwaarden nog niet heeft geaccepteerd. De gebruiksvoorwaarden worden weergegeven en het acceptatieresultaat wordt opgeslagen.
- Een gebruiker meldt zich aan die al een oudere versie van de gebruiksvoorwaarden heeft geaccepteerd, die nu worden bijgewerkt naar de nieuwste versie. De gebruiksvoorwaarden worden weergegeven en het acceptatieresultaat wordt opgeslagen.

In de volgende afbeelding wordt de aanbevolen gebruikersstroom weergegeven:

![Stroomdiagram met de aanbevolen acceptatiegebruikersstroom](./media/manage-user-access/user-flow.png)

Het volgende is een voorbeeld van een op DateTime gebaseerde gebruiksvoorwaarden in een claim:

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentDateTime" TransformationMethod="GetCurrentDateTime">
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="currentDateTime" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequired" TransformationMethod="IsTermsOfUseConsentRequired">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="termsOfUseConsentDateTime" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="termsOfUseTextUpdateDateTime" DataType="dateTime" Value="2098-01-30T23:03:45" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="result" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Het volgende is een voorbeeld van een versie gebaseerde gebruiksvoorwaarden toestemming in een claim:

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetEmptyTermsOfUseConsentVersionForNewUser" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value=""/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentVersion" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="V1"/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="compareTo" DataType="string" Value="V1" />
      <InputParameter Id="operator" DataType="string" Value="not equal" />
      <InputParameter Id="ignoreCase" DataType="string" Value="true" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

## <a name="next-steps"></a>Volgende stappen

- Zie Gebruikersgegevens beheren voor meer informatie over het verwijderen en exporteren van [gebruikersgegevens.](manage-user-data.md)
- Zie Een aangepast beleid voor [B2C IEF - Aanmelden en aanmelden met 'Gebruiksvoorwaarden' prompt](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-sign-up-versioned-tou)voor een voorbeeldbeleid dat een gebruiksvoorwaarden implementeert.
