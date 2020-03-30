---
title: Groepsclaims configureren voor toepassingen met Azure Active Directory | Microsoft Documenten
description: Informatie over het configureren van groepsclaims voor gebruik met Azure AD.
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
ms.openlocfilehash: b8708aec1137836516852135412c4c7cec2feba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408399"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>Groepsclaims configureren voor toepassingen met Azure Active Directory (Public Preview)

Azure Active Directory kan een gebruikersgroeplidmaatschapsinformatie in tokens verstrekken voor gebruik binnen toepassingen.  Twee hoofdpatronen worden ondersteund:

- Groepen die zijn geïdentificeerd met het kenmerk Azure Active Directory-object-id (OID) (Algemeen beschikbaar)
- Groepen die zijn geïdentificeerd met sAMAccountName- of GroupSID-kenmerken voor gesynchroniseerde groepen en gebruikers van Active Directory (AD) (Public Preview)

> [!IMPORTANT]
> Er zijn een aantal kanttekeningen te noteren voor deze preview-functionaliteit:
>
>- Ondersteuning voor het gebruik van SID-kenmerken (sAMAccountName en beveiligings-id) die zijn gesynchroniseerd vanaf on-premises, is ontworpen om het verplaatsen van bestaande toepassingen van AD FS en andere identiteitsproviders mogelijk te maken. Groepen die worden beheerd in Azure AD bevatten niet de kenmerken die nodig zijn om deze claims uit te zenden.
>- In grotere organisaties kan het aantal groepen waarvan een gebruiker lid is, de limiet overschrijden die Azure Active Directory aan een token toevoegt. 150 groepen voor een SAML-token en 200 voor een JWT. Dit kan leiden tot onvoorspelbare resultaten. Als uw gebruikers grote aantallen groepslidmaatschappen hebben, raden we u aan de optie te gebruiken om de groepen die worden uitgezonden in claims te beperken tot de relevante groepen voor de toepassing.  
>- Voor nieuwe toepassingsontwikkeling of in gevallen waarin de toepassing hiervoor kan worden geconfigureerd en waar geen ondersteuning voor geneste groepen vereist is, raden we u aan dat in-app-autorisatie is gebaseerd op toepassingsrollen in plaats van groepen.  Dit beperkt de hoeveelheid informatie die in het token moet worden opgenomen, is veiliger en scheidt gebruikerstoewijzing van de app-configuratie.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>Aanvragen groeperen voor aanvragen die migreren van AD FS en andere identiteitsproviders

Veel toepassingen die zijn geconfigureerd om te verifiëren met AD FS, zijn afhankelijk van groepslidmaatschapsgegevens in de vorm van Windows AD-groepskenmerken.   Deze kenmerken zijn de groep sAMAccountName, die mogelijk is gekwalificeerd door-domeinnaam, of de Windows Group Security Identifier (GroupSID).  Wanneer de toepassing wordt gefedereerd met AD FS, gebruikt AD FS de functie TokenGroepen om de groepslidmaatschappen voor de gebruiker op te halen.

Een app die is verplaatst van AD FS heeft claims in dezelfde indeling nodig. Groeps- en rolclaims kunnen worden uitgestoten vanuit Azure Active Directory met de domeingekwalificeerde sAMAccountName of de GroupSID die is gesynchroniseerd vanuit Active Directory in plaats van de Azure Active Directory-objectID van de groep.

De ondersteunde indelingen voor groepsclaims zijn:

- **Objectid azure Active Directory Group** (beschikbaar voor alle groepen)
- **sAMAccountName** (beschikbaar voor groepen die zijn gesynchroniseerd vanuit Active Directory)
- **NetbiosDomain\sAMAccountName** (beschikbaar voor groepen die zijn gesynchroniseerd vanuit Active Directory)
- **DNSDomainName\sAMAccountName** (beschikbaar voor groepen die zijn gesynchroniseerd vanuit Active Directory)
- **Beveiligings-id voor on-premises groepen** (beschikbaar voor groepen die zijn gesynchroniseerd vanuit Active Directory)

> [!NOTE]
> SID-kenmerken van sAMAccountName en On Premises Group zijn alleen beschikbaar op groepsobjecten die zijn gesynchroniseerd vanuit Active Directory.   Ze zijn niet beschikbaar in groepen die zijn gemaakt in Azure Active Directory of Office365.   Toepassingen die zijn geconfigureerd in Azure Active Directory om gesynchroniseerde on-premises groepskenmerken te krijgen, krijgen ze alleen voor gesynchroniseerde groepen.

## <a name="options-for-applications-to-consume-group-information"></a>Opties voor toepassingen om groepsgegevens te gebruiken

Toepassingen kunnen het eindpunt van de MS-grafiekgroepen aanroepen om groepsinformatie voor de geverifieerde gebruiker te verkrijgen. Deze oproep zorgt ervoor dat alle groepen waarvan een gebruiker lid is, beschikbaar zijn, zelfs als er een groot aantal groepen bij betrokken zijn.  Groepsopsomming is dan onafhankelijk van tokengroottebeperkingen.

Als een bestaande toepassing echter groepsinformatie verwacht te verbruiken via claims, kan Azure Active Directory worden geconfigureerd met een aantal verschillende claimindelingen.  Overweeg de volgende opties:

- Bij het gebruik van groepslidmaatschap voor autorisatiedoeleinden in de toepassing verdient het de voorkeur om de GroepsobjectID te gebruiken. De groepsobjectid is onveranderlijk en uniek in Azure Active Directory en beschikbaar voor alle groepen.
- Als u de on-premises groep sAMAccountName gebruikt voor autorisatie, gebruikt u domeingekwalificeerde namen.  Er is minder kans dat namen botsen. sAMAccountName kan uniek zijn binnen een Active Directory-domein, maar als meer dan één Active Directory-domein is gesynchroniseerd met een Azure Active Directory-tenant, is er de mogelijkheid dat meerdere groepen dezelfde naam hebben.
- Overweeg [toepassingsrollen](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) te gebruiken om een laag van indirection te bieden tussen het groepslidmaatschap en de toepassing.   De toepassing maakt vervolgens interne autorisatiebeslissingen op basis van rolclams in het token.
- Als de toepassing is geconfigureerd om groepskenmerken op te halen die zijn gesynchroniseerd vanuit Active Directory en een groep deze kenmerken niet bevat, wordt deze niet opgenomen in de claims.
- Groepsclaims in tokens bevatten geneste groepen, behalve wanneer ze de optie gebruiken om de groepsclaims te beperken tot groepen die aan de toepassing zijn toegewezen.  Als een gebruiker lid is van GroupB en GroupB lid is van GroupA, dan zullen de groepsclaims voor de gebruiker zowel GroupA als GroupB bevatten. Wanneer de gebruikers van een organisatie een groot aantal groepslidmaatschappen hebben, kan het aantal groepen dat in het token wordt vermeld, de tokengrootte vergroten.  Azure Active Directory beperkt het aantal groepen dat het in een token uitstraalt tot 150 voor SAML-beweringen en 200 voor JWT.  Als een gebruiker lid is van een groter aantal groepen, worden de groepen weggelaten en wordt in plaats daarvan een koppeling naar het eindpunt van de grafiek om groepsinformatie te verkrijgen opgenomen.

## <a name="prerequisites-for-using-group-attributes-synchronized-from-active-directory"></a>Vereisten voor het gebruik van groepskenmerken die zijn gesynchroniseerd vanuit Active Directory

Groepslidmaatschapclaims kunnen worden uitgezonden in tokens voor elke groep als u de ObjectId-indeling gebruikt. Als u groepsclaims wilt gebruiken in andere indelingen dan de groepsobjectid, moeten de groepen worden gesynchroniseerd vanuit Active Directory met Azure AD Connect.

Er zijn twee stappen om Azure Active Directory te configureren om groepsnamen voor Active Directory-groepen uit te zenden.

1. **Groepsnamen uit Active Directory synchroniseren** Voordat Azure Active Directory de groepsnamen of on-premises groep SID in groeps- of rolclaims kan uitzenden, moeten de vereiste kenmerken worden gesynchroniseerd vanuit Active Directory.  U moet Azure AD Connect-versie 1.2.70 of hoger uitvoeren.   Eerdere versies van Azure AD Connect dan 1.2.70 synchroniseren de groepsobjecten uit Active Directory, maar bevatten niet de vereiste groepsnaamkenmerken.  Upgrade naar de huidige versie.

2. **De toepassingsregistratie in Azure Active Directory configureren om groepsclaims op te nemen in tokens** Groepsclaims kunnen worden geconfigureerd in de sectie Ondernemingstoepassingen van de portal of met behulp van het toepassingsmanifest in de sectie Toepassingsregistraties.  Zie 'De Azure Active Directory-toepassingsregistratie configureren voor groepskenmerken' hieronder om groepsclaims te configureren in het toepassingsmanifest.

## <a name="add-group-claims-to-tokens-for-saml-applications-using-sso-configuration"></a>Groepsclaims toevoegen aan tokens voor SAML-toepassingen met behulp van SSO-configuratie

Als u groepsclaims voor een SAML-toepassing voor galerijen of niet-galeries wilt configureren, opent u **Enterprise-toepassingen,** klikt u op de toepassing in de lijst, selecteert u **Configuratie voor één aanmelding**en selecteert u **vervolgens Gebruikerskenmerken & Claims**.

Klik op **Een groepsclaim toevoegen**  

![claims UI](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

De keuzerondjes gebruiken om te selecteren welke groepen in het token moeten worden opgenomen

![claims UI](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Selectie | Beschrijving |
|----------|-------------|
| **Alle groepen** | Zendt beveiligingsgroepen en distributielijsten en rollen uit.  |
| **Beveiligingsgroepen** | Zendt beveiligingsgroepen uit waarvan de gebruiker lid is in de groepsclaim |
| **Directory-rollen** | Als de gebruiker directoryrollen toegewezen krijgt, worden ze uitgezonden als een 'wids'-claim (groepenclaim worden niet uitgezonden) |
| **Groepen die aan de toepassing zijn toegewezen** | Zendt alleen de groepen uit die expliciet aan de toepassing zijn toegewezen en de gebruiker is lid van |

Als u bijvoorbeeld alle beveiligingsgroepen wilt uitzenden waarvan de gebruiker lid is, selecteert u Beveiligingsgroepen

![claims UI](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Als u groepen wilt uitzenden met Active Directory-kenmerken die zijn gesynchroniseerd vanuit Active Directory in plaats van Azure AD-objectID's, selecteert u de vereiste indeling in de vervolgkeuzelijst. Alleen groepen die zijn gesynchroniseerd met Active Directory worden opgenomen in de claims.

![claims UI](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

Als u alleen groepen wilt uitzenden die aan de toepassing zijn toegewezen, selecteert u **Groepen die aan de toepassing zijn toegewezen**

![claims UI](media/how-to-connect-fed-group-claims/group-claims-ui-4-1.png)

Groepen die aan de toepassing zijn toegewezen, worden opgenomen in het token.  Andere groepen waarvan de gebruiker lid is, worden weggelaten.  Met deze optie worden geneste groepen niet opgenomen en moet de gebruiker een direct lid zijn van de groep die aan de toepassing is toegewezen.

Als u de groepen wilt wijzigen die aan de toepassing zijn toegewezen, selecteert u de toepassing in de lijst **Ondernemingstoepassingen** en klikt u vervolgens op **Gebruikers en groepen** in het linkernavigatiemenu van de toepassing.

Zie het document [Een gebruiker of groep toewijzen aan een bedrijfsapp](../../active-directory/manage-apps/assign-user-or-group-access-portal.md) voor meer informatie over het beheren van groepstoewijzing aan toepassingen.

### <a name="advanced-options"></a>Geavanceerde opties

De manier waarop groepsclaims worden uitgezonden, kan worden gewijzigd door de instellingen onder Geavanceerde opties

De naam van de groepsclaim aanpassen: Als deze optie is geselecteerd, kan een ander claimtype worden opgegeven voor groepsclaims.   Voer het claimtype in het veld Naam en de optionele naamruimte voor de claim in het veld naamruimte in.

![claims UI](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Voor sommige toepassingen moeten de groepslidmaatschapsgegevens worden weergegeven in de claim 'rol'. U de groepen van de gebruiker optioneel als rollen uitzenden door het vak 'Groepen een rolclaims uitzenden' in te schakelen.

![claims UI](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Als de optie om groepsgegevens uit te zenden als rollen wordt gebruikt, worden alleen groepen weergegeven in de rolclaim.  Toepassingsrollen die de gebruiker is toegewezen, worden niet weergegeven in de rolclaim.

### <a name="edit-the-group-claims-configuration"></a>De configuratie van groepsclaims bewerken

Zodra een groepsclaimconfiguratie is toegevoegd aan de configuratie van gebruikerskenmerken & claims, wordt de optie om een groepsclaim toe te voegen grijs weergegeven.  Als u de configuratie van groepsclaim wilt wijzigen, klikt u op de groepsclaim in de lijst **Aanvullende claims.**

![claims UI](media/how-to-connect-fed-group-claims/group-claims-ui-7.png)

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>De Azure AD-toepassingsregistratie configureren voor groepskenmerken

Groepsclaims kunnen ook worden geconfigureerd in de sectie [Optionele claims](../../active-directory/develop/active-directory-optional-claims.md) van het [toepassingsmanifest](../../active-directory/develop/reference-app-manifest.md).

1. Selecteer->Manifest in de portal ->Azure Active Directory -> Application Registrations->Selecteren

2. Groepslidmaatschapsclaims inschakelen door de groupMembershipClaim te wijzigen

Geldige waarden zijn:

| Selectie | Beschrijving |
|----------|-------------|
| **"Alle"** | Beveiligingsgroepen, distributielijsten en rollen uitzendt |
| **"SecurityGroup"** | Zendt beveiligingsgroepen uit waarvan de gebruiker lid is in de groepsclaim |
| **"DirectoryRole** | Als de gebruiker directoryrollen toegewezen krijgt, worden ze uitgezonden als een 'wids'-claim (groepenclaim worden niet uitgezonden) |
| **"ApplicationGroup** | Zendt alleen de groepen uit die expliciet aan de toepassing zijn toegewezen en de gebruiker is lid van |

   Bijvoorbeeld:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Standaard worden groepsobjectinstellingen uitgestoten in de groepsclaimwaarde.  Als u de claimwaarde wilt wijzigen om kenmerken van de lokalengroep te bevatten of om het claimtype in rol te wijzigen, gebruikt u de configuratie OptionalClaims als volgt:

3. Stel optionele claims voor groepsnaamconfiguratie in.

   Als u wilt dat de groepen in het token de ad-groepskenmerken op locatie bevatten, geeft u op op welk tokentype optionele claim moet worden toegepast in de optionele sectie claims.  Er kunnen meerdere tokentypen worden weergegeven:

   - idToken voor het OIDC ID-token
   - accessToken voor het OAuth/OIDC-toegangstoken
   - Saml2Token voor SAML-tokens.

   > [!NOTE]
   > Het Saml2Token-type is van toepassing op zowel SAML1.1- als SAML2.0-formaattokens

   Wijzig voor elk relevant tokentype de claim groepen om de sectie OptionalClaims in het manifest te gebruiken. Het schema OptionalClaims is als volgt:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Optioneel claimschema | Waarde |
   |----------|-------------|
   | **Naam:** | Moet "groepen" |
   | **Bron:** | Niet gebruikt. Null weglaten of opgeven |
   | **Essentiële:** | Niet gebruikt. Onwaar weglaten of opgeven |
   | **additionalProperties:** | Lijst met extra eigenschappen.  Geldige opties zijn "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   In additionalProperties zijn slechts één van "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name" vereist.  Als er meer dan één aanwezig is, wordt de eerste gebruikt en alle anderen genegeerd.

   Voor sommige toepassingen is groepsinformatie over de gebruiker vereist in de rolclaim.  Als u het claimtype wilt wijzigen in een groepsclaim in een rolclaim, voegt u 'emit_as_roles' toe aan extra eigenschappen.  De groepswaarden worden weergegeven in de rolclaim.

   > [!NOTE]
   > Als 'emit_as_roles' wordt gebruikt, worden toepassingsrollen die zijn geconfigureerd en die de gebruiker toegewezen heeft, niet weergegeven in de rolclaim

### <a name="examples"></a>Voorbeelden

Groepen uitzenden als groepsnamen in OAuth-toegangstokens in dnsDomainName\SAMAccountName-indeling

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Groepsnamen uitzenden die moeten worden geretourneerd in de netbiosDomain\samAccountName-indeling als de rolclaim in SAML- en OIDC ID-tokens:

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

[Een gebruiker of groep toewijzen aan een bedrijfsapp](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)

[Rolclaims configureren](../../active-directory/develop/active-directory-enterprise-app-role-management.md)
