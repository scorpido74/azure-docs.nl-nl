---
title: Groeps claims configureren voor toepassingen met Azure Active Directory | Microsoft Docs
description: Informatie over het configureren van groepclaims voor gebruik met Azure AD.
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.subservice: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: 3cb53656adb1dbeb5e5597d02edfe5be4dbec6a8
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170484"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>Groeps claims configureren voor toepassingen met Azure Active Directory (open bare preview)

Azure Active Directory kunt een groepslid maatschap voor gebruikers gegevens opgeven in tokens voor gebruik in toepassingen.  Er worden twee hoofd patronen ondersteund:

- Groepen die zijn geïdentificeerd met behulp van het OID-kenmerk (Azure Active Directory object-id) (algemeen beschikbaar)
- Groepen die zijn geïdentificeerd door sAMAccountName of GroupSID Attributes voor Active Directory (AD) gesynchroniseerde groepen en gebruikers (open bare preview)

> [!IMPORTANT]
> Er zijn een aantal opmerkingen voor deze preview-functie:
>
>- Ondersteuning voor het gebruik van sAMAccountName-en SID-kenmerken (Security Identifier) die vanaf on-premises worden gesynchroniseerd, is ontworpen om bestaande toepassingen van AD FS en andere id-providers te verplaatsen. Groepen die worden beheerd in azure AD bevatten niet de kenmerken die nodig zijn om deze claims te verzenden.
>- In grotere organisaties mag het aantal groepen waarvan een gebruiker lid is, de limiet overschrijden die Azure Active Directory zal toevoegen aan een token. 150 groepen voor een SAML-token en 200 voor een JWT. Dit kan leiden tot onvoorspelbare resultaten. Als dit een mogelijk probleem is, raden wij u aan om te testen en, indien nodig, te wachten tot we uitbrei dingen toevoegen zodat u de claims kunt beperken tot de relevante groepen voor de toepassing.  
>- Voor het ontwikkelen van nieuwe toepassingen, of in gevallen waarin de toepassing kan worden geconfigureerd en de ondersteuning voor geneste groepen is niet vereist, wordt aangeraden in-app-autorisatie gebaseerd op toepassings rollen in plaats van groepen.  Hiermee beperkt u de hoeveelheid gegevens die in het token moeten worden gezet, veiliger en scheidt u de gebruikers toewijzing van de app-configuratie.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>Claims groeperen voor toepassingen die worden gemigreerd van AD FS en andere id-providers

Veel toepassingen die zijn geconfigureerd voor verificatie met AD FS, zijn afhankelijk van de gegevens van groepslid maatschappen in de vorm van Windows AD-groeps kenmerken.   Deze kenmerken zijn de groep sAMAccountName, die kunnen worden gekwalificeerd door-Domain name of de Windows-groep beveiligings-id (GroupSID).  Wanneer de toepassing is federatieve met AD FS, gebruikt AD FS de functie TokenGroups om de groepslid maatschappen voor de gebruiker op te halen.

Om te voldoen aan het token dat een app zou ontvangen van AD FS, kunnen groeps-en rol claims worden verzonden met het domein gekwalificeerde sAMAccountName in plaats van de Azure Active Directory objectID van de groep.

De ondersteunde indelingen voor groepclaims zijn:

- **ObjectId voor Azure Active Directory groep** (Beschikbaar voor alle groepen)
- **sAMAccountName** (Beschikbaar voor groepen die zijn gesynchroniseerd vanuit Active Directory)
- **NetbiosDomain\sAMAccountName** (Beschikbaar voor groepen die zijn gesynchroniseerd vanuit Active Directory)
- **DNSDomainName\sAMAccountName** (Beschikbaar voor groepen die zijn gesynchroniseerd vanuit Active Directory)
- **Beveiligings-id van on-premises groep** (Beschikbaar voor groepen die zijn gesynchroniseerd vanuit Active Directory)

> [!NOTE]
> sAMAccountName-en on-premises groep SID-kenmerken zijn alleen beschikbaar voor groeps objecten die zijn gesynchroniseerd vanuit Active Directory.   Ze zijn niet beschikbaar voor groepen die zijn gemaakt in Azure Active Directory of Office365.   Toepassingen die in Azure Active Directory zijn geconfigureerd om gesynchroniseerde on-premises groeps kenmerken te krijgen, krijgen ze alleen voor gesynchroniseerde groepen.

## <a name="options-for-applications-to-consume-group-information"></a>Opties voor het verbruik van groeps gegevens in toepassingen

Toepassingen kunnen groeps gegevens verkrijgen door het eind punt van de grafiek groepen aan te roepen om het groepslid maatschap voor de geverifieerde gebruiker op te halen. Deze aanroep zorgt ervoor dat alle groepen waarvan een gebruiker lid is, beschikbaar zijn, zelfs wanneer er sprake is van een groot aantal groepen en de toepassing alle groepen moet opsommen waarvan de gebruiker lid is.  Groeps inventarisatie wordt vervolgens onafhankelijk van beperkingen voor de token grootte.

Als een bestaande toepassing al groeps gegevens zou moeten gebruiken via claims in het token dat wordt ontvangen, kan Azure Active Directory echter worden geconfigureerd met een aantal verschillende claim opties die voldoen aan de behoeften van de toepassing.  Houd rekening met de volgende opties:

- Wanneer u groepslid maatschap gebruikt voor autorisatie doeleinden in de toepassing, is het raadzaam om de groep ObjectID te gebruiken. Dit is onveranderbaar en uniek in Azure Active Directory en beschikbaar voor alle groepen.
- Als de on-premises groep sAMAccountName voor autorisatie gebruikt, gebruikt u domein gekwalificeerde namen.  Er zijn minder kans op problemen met namen. sAMAccountName zelf kan uniek zijn binnen een Active Directory domein, maar als meer dan één Active Directory domein is gesynchroniseerd met een Azure Active Directory Tenant, kan er meer dan één groep dezelfde naam hebben.
- U kunt [toepassings rollen](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) gebruiken om een laag van omleiding te bieden tussen het groepslid maatschap en de toepassing.   De toepassing brengt vervolgens interne autorisatie beslissingen op basis van de functie-en schelpen in het token.
- Als de toepassing is geconfigureerd voor het ophalen van groeps kenmerken die zijn gesynchroniseerd vanuit Active Directory en een groep bevat deze kenmerken niet, maar deze worden niet opgenomen in de claims.
- Groepeer claims in tokens bevatten geneste groepen.   Als een gebruiker lid is van GroupB en GroupB lid is van GroepA, bevatten de groeps claims voor de gebruiker zowel GroepA als GroupB. Voor organisaties met een intensief gebruik van geneste groepen en gebruikers met grote aantallen groepslid maatschappen kan het aantal groepen dat in het token wordt vermeld, de token grootte verg Roten.   Azure Active Directory beperkt het aantal groepen dat in een token wordt meegeleid naar 150 voor SAML-bevestigingen en 200 voor JWT om te voor komen dat tokens te groot worden.  Als een gebruiker lid is van een groter aantal groepen dan de limiet, worden de groepen verzonden samen met een koppeling naar het eind punt van de grafiek om groeps gegevens op te halen.

> Vereisten voor het gebruik van groeps kenmerken gesynchroniseerd vanuit Active Directory:   De groepen moeten worden gesynchroniseerd vanuit Active Directory met behulp van Azure AD Connect.

Er zijn twee stappen voor het configureren van Azure Active Directory voor het verzenden van groeps namen voor Active Directory groepen.

1. **Groeps namen synchroniseren vanuit Active Directory** Voordat Azure Active Directory de groeps namen of on-premises groeps-SID in groeps-of Rolgroepen kunt verzenden, moeten de vereiste kenmerken worden gesynchroniseerd vanuit Active Directory.  U moet Azure AD Connect versie 1.2.70 of hoger uitvoeren.   Vóór versie 1.2.70 van Azure AD Connect worden de groeps objecten vanuit Active Directory gesynchroniseerd, maar bevat de vereiste groeps naam kenmerken standaard niet.  U moet een upgrade uitvoeren naar de huidige versie.

2. **De registratie van de toepassing in azure Active Directory zo configureren dat groeps claims worden toegevoegd aan tokens** Groeps claims kunnen worden geconfigureerd in de sectie bedrijfs toepassingen van de portal voor een galerie-of niet-galerij SAML SSO-toepassing, of met behulp van het manifest van de toepassing in de sectie toepassings registraties.  Zie voor het configureren van groeps claims in het manifest van de toepassing ' de Azure Active Directory toepassings registratie voor groeps kenmerken configureren ' hieronder.

## <a name="configure-group-claims-for-saml-applications-using-sso-configuration"></a>Groeps claims configureren voor SAML-toepassingen met SSO-configuratie

Als u groeps claims wilt configureren voor een SAML-toepassing in een galerie of niet-galerie, opent u bedrijfs toepassingen, klikt u op de toepassing in de lijst en selecteert u configuratie van eenmalige aanmelding.

Selecteer het bewerkings pictogram naast groepen die zijn geretourneerd in het token

![claim GEBRUIKERSINTERFACE](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

Gebruik de keuze rondjes om te selecteren welke groepen in het token moeten worden opgenomen

![claim GEBRUIKERSINTERFACE](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Selectie | Description |
|----------|-------------|
| **Alle groepen** | Beveiligings groepen en distributie lijsten meedeelt.   De functie zorgt er ook voor dat Directory rollen de gebruiker wordt toegewezen om te worden verzonden in een ' WIDS-claim, en eventuele toepassings rollen waaraan de gebruiker is toegewezen in de rol claim. |
| **Beveiligings groepen** | Verzendt beveiligings groepen waarvan de gebruiker lid is in de groeps claim |
| **Distributie lijsten** | Verzendt distributie lijsten waarvan de gebruiker lid is |
| **Directory-rollen** | Als er adreslijst rollen aan de gebruiker worden toegewezen, worden ze verzonden als een ' WIDS-claim (groeps claim wordt niet verzonden) |

Als u bijvoorbeeld alle beveiligings groepen wilt genereren waarvan de gebruiker lid is, selecteert u beveiligings groepen

![claim GEBRUIKERSINTERFACE](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Als u groepen wilt verzenden met Active Directory kenmerken die zijn gesynchroniseerd vanuit Active Directory in plaats van met Azure AD Objectid's, selecteert u de gewenste indeling in de vervolg keuzelijst.  Hiermee vervangt u de object-ID in de claims met teken reeks waarden die groeps namen bevatten.   Alleen groepen die zijn gesynchroniseerd vanuit Active Directory worden opgenomen in de claims.

![claim GEBRUIKERSINTERFACE](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

### <a name="advanced-options"></a>Geavanceerde opties

De manier waarop groeps claims worden verzonden, kunnen worden gewijzigd door de instellingen onder Geavanceerde opties

De naam van de groepclaim aanpassen:  Als deze is geselecteerd, kan een ander claim type worden opgegeven voor groepclaims.   Geef het claim type op in het veld naam en de optionele naam ruimte voor de claim in het veld naam ruimte.

![claim GEBRUIKERSINTERFACE](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Voor sommige toepassingen moet de gegevens van het groepslid maatschap worden weer gegeven in de claim van de rol. U kunt desgewenst de gebruikers groepen als rollen verzenden door het selectie vakje groepen een rol claimen verzenden in te scha kelen.

![claim GEBRUIKERSINTERFACE](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Als de optie voor het verzenden van groeps gegevens als rollen wordt gebruikt, worden alleen groepen weer gegeven in de rol claim.  Alle toepassings rollen waaraan de gebruiker is toegewezen, worden niet weer gegeven in de claim.

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>De registratie van de Azure AD-toepassing voor groeps kenmerken configureren

Groepclaims kunnen ook worden geconfigureerd in de sectie [optionele claims](../../active-directory/develop/active-directory-optional-claims.md) van het [toepassings manifest](../../active-directory/develop/reference-app-manifest.md).

1. In de portal-> Azure Active Directory-> toepassings registraties-> Selecteer toepassing-> manifest

2. Claims voor groepslid maatschap inschakelen door de groupMembershipClaim te wijzigen

   De geldige waarden zijn:

   - Hele
   - Beveiligings groep
   - "DistributionList"
   - "DirectoryRole"

   Bijvoorbeeld:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Standaard wordt groeps Objectid's verzonden in de claim waarde van de groep.  Als u de claim waarde wilt wijzigen zodat deze lokale groeps kenmerken bevat, of als u het claim type wilt wijzigen in rol, gebruikt u de OptionalClaims-configuratie als volgt:

3. Stel groeps naam configuratie optionele claims in.

   Als u wilt dat de groepen in het token de on-premises AD-groeps kenmerken bevatten in de sectie optionele claims, geeft u op welk token type optionele claim moet worden toegepast op, de naam van de optionele claim die is aangevraagd en eventuele aanvullende eigenschappen.  Meerdere token typen kunnen worden weer gegeven:

   - idToken voor het OIDC-ID-token
   - accessToken voor het OAuth/OIDC-toegangs token
   - Saml2Token voor SAML-tokens.

   > [!NOTE]
   > Het type Saml2Token is van toepassing op zowel SAML 1.1-als SAML 2.0-indelings tokens

   Wijzig voor elk relevant token type de OptionalClaims-sectie in het manifest. Het OptionalClaims-schema is als volgt:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Optioneel claim schema | Value |
   |----------|-------------|
   | **naam** | Moet ' groups ' zijn |
   | **Bron** | Niet gebruikt. Null-waarde weglaten of opgeven |
   | **downloaden** | Niet gebruikt. Weglaten of onwaar opgeven |
   | **additionalProperties:** | Lijst met aanvullende eigenschappen.  Geldige opties zijn "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   In additionalProperties is slechts één van ' sam_account_name ', ' dns_domain_and_sam_account_name ', ' netbios_domain_and_sam_account_name ' vereist.  Als er meer dan een aanwezig is, wordt de eerste gebruikt en worden alle andere genegeerd.

   Voor sommige toepassingen is groeps informatie over de gebruiker in de rol claim vereist.  Als u het claim type wilt wijzigen van een groepclaim in een groepclaim, voegt u ' emit_as_roles ' toe aan extra eigenschappen.  De groeps waarden worden verzonden in de rol claim.

   > [!NOTE]
   > Als ' emit_as_roles ' wordt gebruikt, worden de toepassings rollen die zijn geconfigureerd dat de gebruiker is toegewezen, niet weer gegeven in de rol claim

### <a name="examples"></a>Voorbeelden

Groepen verzenden als groeps namen in OAuth-toegangs tokens in de dnsDomainName\SAMAccountName-indeling

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Groeps namen verzenden die moeten worden geretourneerd in netbiosDomain\samAccountName-indeling als de rol claim in SAML-en OIDC-ID-tokens:

```json
"optionalClaims": {
    "saml2Token": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }],

    "idToken": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }]
 }
 ```

## <a name="next-steps"></a>Volgende stappen

[Wat is hybride identiteit?](whatis-hybrid-identity.md)
