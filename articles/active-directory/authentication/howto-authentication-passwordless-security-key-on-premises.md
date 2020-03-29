---
title: Aanmelden zonder wachtwoordbeveiliging bij on-premises bronnen (voorbeeld) - Azure Active Directory
description: Meer informatie over het inschakelen van aanmelding met een wachtwoordloze beveiligingssleutel bij on-premises bronnen met Azure Active Directory (voorbeeld)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b29f84931c169ffe1c2c81d5e32201cbc63fc88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942880"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-with-azure-active-directory-preview"></a>Aanmelding met wachtwoordloze beveiligingssleutel inschakelen bij on-premises bronnen met Azure Active Directory (voorbeeld)

Dit document is gericht op het inschakelen van wachtwoordloze verificatie voor on-premises resources voor omgevingen met zowel **Azure AD-samengevoegde** als **hybride Azure AD-apparaten.** Deze functionaliteit biedt naadloze single sign-on (SSO) aan on-premises resources met behulp van Microsoft-compatibele beveiligingssleutels.

|     |
| --- |
| FIDO2-beveiligingssleutels zijn een openbare preview-functie van Azure Active Directory. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) meer informatie over voorvertoningen|
|     |

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>SSO naar on-premises resources met FIDO2-sleutels

Azure Active Directory (AD) kan Kerberos Ticket Granting Tickets (TGA's) uitgeven voor een of meer van uw Active Directory-domeinen. Met deze functionaliteit kunnen gebruikers zich aanmelden bij Windows met moderne referenties zoals FIDO2-beveiligingssleutels en toegang krijgen tot traditionele Active Directory-gebaseerde bronnen. Kerberos Service Tickets en autorisatie worden nog steeds beheerd door uw on-premises Active Directory-domeincontrollers.

Een Azure AD Kerberos Server-object wordt gemaakt in uw on-premises Active Directory en vervolgens veilig gepubliceerd in Azure Active Directory. Het object is niet gekoppeld aan fysieke servers. Het is gewoon een bron die kan worden gebruikt door Azure Active Directory om Kerberos TGA's te genereren voor uw Active Directory-domein.

![Een ticket (TGT) ophalen van Azure AD en AD DS](./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png)

1. Gebruikers meldt zich aan bij hun Windows 10-apparaat met een FIDO2-beveiligingssleutel en verifieert bij Azure AD.
1. Azure AD controleert de map op een Kerberos-serversleutel die overeenkomt met het on-premises AD-domein van de gebruiker.
   1. Azure AD genereert een Kerberos TGT voor het on-premises AD-domein van de gebruiker. De TGT bevat alleen de SID van de gebruiker. Er zijn geen autorisatiegegevens opgenomen in de TGT.
1. De TGT wordt teruggestuurd naar de client, samen met hun Azure AD Primary Refresh Token (PRT).
1. De clientmachine neemt contact op met een on-premises AD-domeincontroller en verruilt de gedeeltelijke TGT voor een volledig gevormde TGT.
1. De clientmachine heeft nu een Azure AD PRT en een volledige Active Directory TGT en heeft toegang tot zowel cloud- als on-premises resources.

## <a name="requirements"></a>Vereisten

Organisaties moeten de stappen uitvoeren om [het wachtwoordloze beveiligingssleutelteken in te schakelen naar Windows 10-apparaten (voorbeeld)](howto-authentication-passwordless-security-key.md) voordat ze de stappen in dit artikel voltooien.

Organisaties moeten ook voldoen aan de volgende softwarevereisten.

- Apparaten moeten Windows 10 Insider Build 18945 of nieuwer gebruiken.
- U moet versie 1.4.32.0 of hoger van [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect)hebben.
  - Zie [De juiste verificatiemethode kiezen voor uw azure Active Directory hybride identiteitsoplossing](../../security/fundamentals/choose-ad-authn.md) en Selecteer welk installatietype u wilt gebruiken voor Azure AD Connect voor meer informatie over de beschikbare azure [AD-verificatieopties.](../hybrid/how-to-connect-install-select-installation.md)
- Op uw Windows Server-domeincontrollers moeten de volgende patches zijn geïnstalleerd:
    - Voor Windows Server 2016 -https://support.microsoft.com/help/4534307/windows-10-update-kb4534307
    - Voor Windows Server 2019 -https://support.microsoft.com/help/4534321/windows-10-update-kb4534321

### <a name="supported-scenarios"></a>Ondersteunde scenario's

Het scenario ondersteunt eenmalige aanmelding (SSO) in beide volgende scenario's:

- Voor cloudresources zoals Office 365 en andere SAML-toepassingen.
- Voor on-premises bronnen en Windows-geïntegreerde verificatie naar websites. De bronnen kunnen websites en SharePoint-sites bevatten waarvoor IIS-verificatie vereist is, en/of bronnen die NTLM-verificatie gebruiken.

### <a name="unsupported-scenarios"></a>Niet-ondersteunde scenario's

De volgende scenario's worden niet ondersteund:

- Het AD DS-domein (Windows Server Active Directory Domain Services) is samengevoegd (on-premises apparaten) implementatie.
- RDP-, VDI- en Citrix-scenario's met behulp van een beveiligingssleutel.
- S/MIME met behulp van een beveiligingssleutel.
- "Run as" met behulp van een beveiligingssleutel.
- Log in bij een server met behulp van beveiligingssleutel.

## <a name="create-kerberos-server-object"></a>Kerberos-serverobject maken

Beheerders gebruiken PowerShell-hulpprogramma's van hun Azure AD Connect-server om een Azure AD Kerberos Server-object te maken in hun on-premises directory. Voer de volgende stappen uit in elk domein en forest in uw organisatie die Azure AD-gebruikers bevatten:

1. Upgrade naar de nieuwste versie van Azure AD Connect. De instructies gaan ervan uit dat u Azure AD Connect al hebt geconfigureerd om uw hybride omgeving te ondersteunen.
1. Open op de Azure AD Connect Server een verhoogde PowerShell-prompt en navigeer naar`C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. Voer de volgende PowerShell-opdrachten uit om een nieuw Azure AD Kerberos-serverobject te maken in zowel uw on-premises Active Directory-domein als Azure Active Directory-tenant.

> [!NOTE]
> Vervang `contoso.corp.com` in het volgende voorbeeld uw on-premises Active Directory-domeinnaam.

```powerShell
Import-Module ".\AzureAdKerberos.psd1"

# Specify the on-premises Active Directory domain. A new Azure AD
# Kerberos Server object will be created in this Active Directory domain.
$domain = "contoso.corp.com"

# Enter an Azure Active Directory global administrator username and password.
$cloudCred = Get-Credential

# Enter a domain administrator username and password.
$domainCred = Get-Credential

# Create the new Azure AD Kerberos Server object in Active Directory
# and then publish it to Azure Active Directory.
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>De Azure AD Kerberos Server weergeven en verifiëren

U de nieuw gemaakte Azure AD Kerberos Server weergeven en verifiëren met de volgende opdracht:

```powerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

Met deze opdracht worden de eigenschappen van de Azure AD Kerberos Server uitgevoerd. U de eigenschappen bekijken om te controleren of alles in orde is.

| Eigenschap | Beschrijving |
| --- | --- |
| Id | De unieke id van het AD DS DC-object. Deze ID wordt soms aangeduid als het "slot" of het is "branch ID". |
| DomainDnsName | De DNS-domeinnaam van het Active Directory-domein. |
| ComputerAccount | Het computeraccountobject van het Azure AD Kerberos Server-object (de DC). |
| Gebruikersaccount | Het uitgeschakelde gebruikersaccountobject met de A2Gt-versleutelingssleutel Azure AD Kerberos Server. De DN van deze rekening is`CN=krbtgt_AzureAD,CN=Users,<Domain-DN>` |
| KeyVersion | De belangrijkste versie van de Azure AD Kerberos Server TGT-versleutelingssleutel. De versie wordt toegewezen wanneer de sleutel wordt gemaakt. De versie wordt vervolgens verhoogd elke keer dat de toets wordt gedraaid. De stappen zijn gebaseerd op replicatiemeta-gegevens en waarschijnlijk groter dan één. De eerste *KeyVersion* kan bijvoorbeeld *192272*zijn. De eerste keer dat de toets wordt gedraaid, kan de versie doorstromen naar *212621*. Het belangrijkste om te controleren is dat de *KeyVersion* voor het on-premises object en de *CloudKeyVersion* voor het cloudobject hetzelfde zijn. |
| KeyUpdatedOn | De datum en tijd waarop de TGT-versleutelingssleutel azure AD Kerberos Server is bijgewerkt of gemaakt. |
| KeyUpdatedFrom | De DC waar de Azure AD Kerberos Server TGT-versleutelingssleutel voor het laatst is bijgewerkt. |
| CloudId (CloudId) | De id van het Azure AD-object. Moet overeenkomen met de ID hierboven. |
| CloudDomainDnsName | De *DomainDnsName* van het Azure AD-object. Moet overeenkomen met de *DomainDnsName* hierboven. |
| CloudKeyVersion | De *keyversion* van het Azure AD-object. Moet overeenkomen met de *KeyVersion* hierboven. |
| CloudKeyUpdatedon | De *KeyUpdatedOn* van het Azure AD-object. Moet overeenkomen met de *KeyUpdatedOn* hierboven. |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>De Azure AD Kerberos Server-toets roteren

De Krbtgt-versleutelingssleutels van Azure AD Kerberos Server moet regelmatig worden gedraaid. Het wordt aanbevolen dat u hetzelfde schema volgt dat u gebruikt om alle andere Krbtgt-toetsen van Active Directory Domain Controller te roteren.

> [!WARNING]
> Er zijn andere hulpprogramma's die de krbtgt-toetsen kunnen roteren, maar u moet de in dit document vermelde hulpprogramma's gebruiken om de krbtgt-sleutels van uw Azure AD Kerberos Server te roteren. Dit zorgt ervoor dat de sleutels worden bijgewerkt in zowel on-premises AD als Azure AD.

```powerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>De Azure AD Kerberos-server verwijderen

Als u het scenario wilt terugdraaien en de Azure AD Kerberos Server wilt verwijderen uit zowel de on-premises Active Directory als de Azure Active Directory, voert u de volgende opdracht uit:

```powerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>Scenario's voor meerdere bossen en meerdere domeinen

Het Azure AD Kerberos-serverobject wordt in Azure AD weergegeven als een *KerberosDomain-object.* Elk on-premises Active Directory-domein wordt weergegeven als één *KerberosDomain-object* in Azure AD.

Uw organisatie heeft bijvoorbeeld een Active Directory-forest met twee domeinen `contoso.com` en `fabrikam.com`. Als u ervoor kiest azure AD toe te staan Kerberos-tga's uit te geven voor het hele forest, zijn er twee *KerberosDomain-objecten* in Azure AD. Eén *KerberosDomain-object* voor `contoso.com` `fabrikam.com`, en één voor . Als u meerdere Active Directory-forests hebt, is er één *KerberosDomain-object* voor elk domein in elk forest.

U moet de stappen uitvoeren om [Kerberos-serverobject](#create-kerberos-server-object) te maken in elk domein en forest in uw organisatie die Azure AD-gebruikers bevatten.

## <a name="known-behavior"></a>Bekend gedrag

Aanmelden bij FIDO wordt geblokkeerd als uw wachtwoord is verlopen. De verwachting is dat de gebruiker zijn wachtwoord opnieuw instelt voordat hij met FIDO kan inloggen.

## <a name="troubleshooting-and-feedback"></a>Probleemoplossing en feedback

Als u feedback wilt delen of problemen wilt ondervinden tijdens het bekijken van deze functie, deelt u via de Windows Feedback Hub-app de volgende stappen:

1. Start **de feedbackhub** en zorg ervoor dat je bent aangemeld.
1. Feedback verzenden onder de volgende indeling:
   - Categorie: Beveiliging en privacy
   - Subcategorie: FIDO
1. Als u logboeken wilt vastleggen, gebruikt u de optie om **mijn probleem opnieuw te maken**

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="does-this-work-in-my-on-premises-environment"></a>Werkt dit in mijn on-premises omgeving?

Deze functie werkt niet voor een pure on-premises Active Directory Domain Services (AD DS)-omgeving.

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Mijn organisatie vereist tweefactorauthenticatie om toegang te krijgen tot bronnen. Wat kan ik doen om deze eis te ondersteunen?

Beveiligingssleutels zijn er in verschillende vormfactoren. Neem contact op met de fabrikant van het apparaat van belang om te bespreken hoe hun apparaten kunnen worden ingeschakeld met een pincode of biometrische factor als tweede factor.

### <a name="can-admins-set-up-security-keys"></a>Kunnen beheerders beveiligingssleutels instellen?

We werken aan deze mogelijkheid voor algemene beschikbaarheid (GA) van deze functie.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Waar kan ik terecht voor het vinden van conforme beveiligingssleutels?

[FIDO2-beveiligingssleutels](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>Wat moet ik doen als ik mijn beveiligingssleutel verlies?

U sleutels uit de Azure-portal verwijderen door naar de pagina **Beveiligingsgegevens** te navigeren en de beveiligingssleutel te verwijderen.

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>Ik kan FIDO niet direct gebruiken nadat ik een hybride Azure AD-aangesloten machine heb gemaakt

Als u een hybride Azure AD-lidmachine hebt geïnstalleerd, moet u zich na het domeinjoin- en herstartproces aanmelden met een wachtwoord en wachten tot het beleid is gesynchroniseerd voordat u FIDO gebruiken om u aan te melden.

- Controleer uw huidige `dsregcmd /status` status door in een opdrachtvenster te typen en controleer of zowel *AzureAdJoined* als *DomainJoined* *JA weergeven.*
- Deze vertraging is een bekende beperking voor domeinsamengevoegde apparaten en is niet FIDO-specifiek.

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>Ik kan SSO niet naar mijn NTLM-netwerkbron brengen nadat ik me heb aanmelden bij FIDO en een referentieprompt kan krijgen

Zorg ervoor dat er voldoende domeincontrollers zijn gepatcht om tijdig te reageren om uw resourceaanvraag te onderhouden. Als u wilt controleren of u een domeincontroller `nltest /dsgetdc:contoso /keylist /kdc`zien waarop de functie wordt uitgevoerd, controleert u de uitvoer van.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over wachtwoordloos](concept-authentication-passwordless.md)
