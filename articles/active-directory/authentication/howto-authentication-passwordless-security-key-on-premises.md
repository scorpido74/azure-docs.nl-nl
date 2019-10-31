---
title: Wachtwoord beveiligings sleutel aanmelden bij on-premises resources (preview)-Azure Active Directory
description: Aanmelden met een wacht woord toestaan bij on-premises resources (preview-versie)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42c42bb1e76d854723c8a88e9c3e2c104464beb0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164859"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-preview"></a>Aanmelden met een wacht woord toestaan bij on-premises resources (preview-versie)

Dit document richt zich op het inschakelen van verificatie zonder wacht woord voor on-premises resources voor omgevingen met zowel **Azure AD join** als **hybride Azure AD gekoppelde** Windows 10-apparaten. Deze functionaliteit biedt naadloze eenmalige aanmelding (SSO) voor on-premises resources met behulp van met micro soft compatibele beveiligings sleutels.

|     |
| --- |
| FIDO2-beveiligings sleutels zijn een open bare preview-functie van Azure Active Directory. Zie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie over Previews|
|     |

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>Eenmalige aanmelding voor on-premises resources met behulp van FIDO2-sleutels

Azure Active Directory (AD) kan Kerberos ticket granting tickets (Tgt's) uitgeven voor een of meer van uw Active Directory domeinen. Met deze functionaliteit kunnen gebruikers zich aanmelden bij Windows met moderne referenties zoals FIDO2-beveiligings sleutels en toegang hebben tot traditionele Active Directory gebaseerde resources. Kerberos-Service tickets en-autorisatie blijven door uw on-premises Active Directory domein controllers worden beheerd.

Er wordt een Azure AD Kerberos-server object gemaakt in uw on-premises Active Directory en vervolgens veilig gepubliceerd naar Azure Active Directory. Het object is niet gekoppeld aan een fysieke server. Het is gewoon een resource die kan worden gebruikt door Azure Active Directory voor het genereren van Kerberos ticket granting tickets (Tgt's) voor uw Active Directory-domein.

![Een TGT en PRT ophalen uit Azure AD en AD DS](./media/howto-authentication-passwordless-on-premises/fido2-tgt-exchange-process.png)

1. De gebruiker meldt zich aan bij hun Windows 10-apparaat met een FIDO2-beveiligings sleutel en verifieert deze bij Azure AD.
1. Azure AD controleert de directory op een Kerberos-server sleutel die overeenkomt met het on-premises AD-domein van de gebruiker.
   1. Azure AD genereert een Kerberos TGT voor het on-premises AD-domein van de gebruiker. De TGT bevat alleen de SID van de gebruiker. Er zijn geen autorisatie gegevens opgenomen in de TGT.
1. De TGT wordt samen met het primaire vernieuwings token van Azure AD (PRT) naar de client geretourneerd.
1. De client computer neemt contact op met een on-premises AD-domein controller en handelt de gedeeltelijke TGT voor een volledig gevormd TGT.
1. De client computer heeft nu een Azure AD-PRT en een volledige Active Directory TGT en kan toegang krijgen tot zowel Cloud-als on-premises resources.

## <a name="requirements"></a>Vereisten

Organisaties moeten de stappen in het artikel [wacht woordloze beveiligings sleutel aanmelden op Windows 10-apparaten (preview-versie) inschakelen](howto-authentication-passwordless-security-key.md) voordat u de stappen in dit artikel hebt voltooid.

Organisaties moeten voldoen aan de volgende software vereisten.

- Op apparaten moet Windows 10 Insider build 18945 of hoger worden uitgevoerd
- Upgrade uitvoeren naar de nieuwste versie van [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect)
- Volledige patches voor Windows Server 2016/2019-domein controllers om de belasting van de on-premises verificatie aanvragen af te handelen.

### <a name="supported-scenarios"></a>Ondersteunde scenario's

- Het scenario ondersteunt eenmalige aanmelding (SSO) voor beide:
   - Cloud resources zoals Office 365 en andere toepassingen waarvoor SAML is ingeschakeld.
   - On-premises resources en geïntegreerde Windows-verificatie voor websites, waaronder websites en share point-sites waarvoor IIS-verificatie is vereist, en/of bronnen die gebruikmaken van NTLM-verificatie.

### <a name="unsupported-scenarios"></a>Niet-ondersteunde scenario's

De volgende scenario's worden niet ondersteund:

- De implementatie van Windows Server Active Directory Domain Services (AD DS) toegevoegd aan een domein (alleen on-premises apparaten) wordt **niet ondersteund**.
- RDP-, VDI-en Citrix-scenario's worden **niet ondersteund** met behulp van de beveiligings sleutel.
- S/MIME wordt **niet ondersteund** met behulp van de beveiligings sleutel.
- ' Uitvoeren als ' wordt **niet ondersteund** met behulp van de beveiligings sleutel.
- Aanmelden bij een server met behulp van een beveiligings sleutel wordt **niet ondersteund**.

## <a name="create-kerberos-server-object"></a>Een Kerberos-server object maken

Beheerders gebruiken Power shell-hulpprogram ma's van de Azure AD Connect server voor het maken van een Azure AD Kerberos-server object in hun on-premises Directory. U moet deze stappen uitvoeren in elk domein en forest in uw organisatie die Azure AD-gebruikers bevatten.

1. Voer een upgrade uit naar de nieuwste versie van Azure AD Connect. In deze instructies wordt ervan uitgegaan dat u Azure AD Connect al hebt geconfigureerd om uw hybride omgeving te ondersteunen.
1. Open op de Azure AD Connect-server een Power shell-prompt met verhoogde bevoegdheid en ga naar C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\
1. Voer de volgende Power shell-opdrachten uit om een nieuw Azure AD Kerberos-server object te maken in zowel uw on-premises Active Directory domein als Azure Active Directory Tenant.

> [!NOTE]
> Vervang "contoso.corp.com" in het volgende voor beeld door uw on-premises Active Directory domein naam.

```PowerShell
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

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>De Azure AD Kerberos-server weer geven en controleren

U kunt de zojuist gemaakte Azure AD Kerberos-server weer geven en controleren met de volgende opdracht. 

```PowerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

Met deze opdracht worden de eigenschappen van de Azure AD Kerberos-server uitgevoerd. U kunt de eigenschappen controleren om te controleren of alles in goede staat is.

| Eigenschap | Beschrijving |
| --- | --- |
| Id | De unieke ID van het object van de AD-domein controller. Deze ID wordt soms aangeduid als ' sleuf ' of ' vertakking-ID '. |
| DomainDnsName | De DNS-domein naam van de Active Directory-domein. |
| ComputerAccount | Het object computer account van het object van de Azure AD Kerberos-server (de domein controller). |
| User account | Het uitgeschakelde gebruikers account-object dat de Azure AD Kerberos server TGT-versleutelings sleutel bevat. De DN van dit account is: <br> CN = krbtgt_AzureAD, CN = Users < domein-DN > |
| Versie | De sleutel versie van de TGT-versleutelings sleutel van de Azure AD Kerberos-server. De versie wordt toegewezen wanneer de sleutel wordt gemaakt. De versie wordt vervolgens verhoogd telkens wanneer de sleutel wordt gedraaid. De stappen zijn gebaseerd op meta gegevens van replicatie en zijn waarschijnlijk groter dan één. De eerste versie kan bijvoorbeeld 192272 zijn. De eerste keer dat de sleutel wordt gedraaid, kan de versie worden voortgezet in 212621. Het is belang rijk om te controleren of de versie van het on-premises object en de CloudKeyVersion voor het Cloud object hetzelfde zijn. |
| KeyUpdatedOn | De datum en tijd waarop de TGT-versleutelings sleutel van de Azure AD Kerberos-server is bijgewerkt/gemaakt. |
| KeyUpdatedFrom | De domein controller waarop de TGT-versleutelings sleutel van de Azure AD Kerberos-server voor het laatst is bijgewerkt. |
| CloudId | De ID van het Azure AD-object. Moet overeenkomen met de bovenstaande ID. |
| CloudDomainDnsName | De DomainDnsName van het Azure AD-object. Moet overeenkomen met de bovenstaande DomainDnsName. |
| CloudKeyVersion | De versie van het Azure AD-object. Moet overeenkomen met de bovenstaande versie. |
| CloudKeyUpdatedOn | De KeyUpdatedOn van het Azure AD-object. Moet overeenkomen met de bovenstaande KeyUpdatedOn. |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>De Azure AD Kerberos-server sleutel draaien

De Azure AD Kerberos-server versleuteling krbtgt-sleutels moeten regel matig worden gedraaid. U kunt het beste dezelfde planning volgen die u gebruikt om alle andere krbtgt-sleutels van Active Directory-domein controller te draaien.

> [!WARNING]
> Er zijn andere hulpprogram ma's die de krbtgt-sleutels kunnen draaien, maar u moet de hulpprogram ma's in dit document gebruiken om de krbtgt-sleutels van uw Azure AD Kerberos-server te roteren. Dit zorgt ervoor dat de sleutels worden bijgewerkt in zowel on-premises AD als Azure AD.

```PowerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>De Azure AD Kerberos-server verwijderen

Als u het scenario wilt herstellen en de Azure AD Kerberos-server wilt verwijderen uit zowel on-premises Active Directory als Azure Active Directory, voert u de volgende opdracht uit.  

```PowerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>Scenario's met meerdere forests en meerdere domeinen

Het object Azure AD Kerberos-server wordt weer gegeven in azure AD als een KerberosDomain-object. Elke on-premises Active Directory domein wordt weer gegeven als één KerberosDomain-object in azure AD.

Uw organisatie heeft bijvoorbeeld een Active Directory forest met twee domeinen, contoso.com en fabrikam.com. Als u ervoor kiest om Azure AD toe te staan Kerberos ticket granting tickets (TGT) uit te geven voor het hele forest, zijn er twee KerberosDomain-objecten in azure AD. Eén KerberosDomain-object voor contoso.com en een voor fabrikam.com. Als u meerdere Active Directory-forests hebt, hebt u voor elk domein in elk forest één KerberosDomain-object.

U moet de stappen uitvoeren in een [Kerberos-server object maken](#create-kerberos-server-object) in elk domein en forest in uw organisatie die Azure AD-gebruikers bevatten.

## <a name="known-behavior"></a>Bekend gedrag

Aanmelden met FIDO wordt geblokkeerd als uw wacht woord is verlopen. De verwachting is dat de gebruiker het wacht woord opnieuw moet instellen voordat u zich kunt aanmelden met behulp van FIDO.

## <a name="troubleshooting-and-feedback"></a>Problemen oplossen en feedback

Als u feedback wilt delen of problemen ondervindt tijdens het vooraf bekijken van deze functie, kunt u deze delen via de Windows feedback hub-app.

1. Start de **feedback-hub** en zorg ervoor dat u bent aangemeld.
1. Feedback verzenden in de volgende categorisatie:
   1. Categorie: beveiliging en privacy
   1. Subcategorie: FIDO
1. Als u logboeken wilt vastleggen, gebruikt u de optie: **mijn probleem opnieuw maken**

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="does-this-work-in-my-on-premises-environment"></a>Werkt dit in mijn on-premises omgeving?

Deze functie werkt niet voor een pure on-premises Active Directory Domain Services-omgeving (AD DS).

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Mijn organisatie vereist twee ledige verificatie voor toegang tot resources, wat kan ik doen om deze vereiste te ondersteunen?

Beveiligings sleutels zijn verkrijgbaar in verschillende vorm factoren. Neem contact op met de fabrikant van het apparaat van belang om te bespreken hoe hun apparaten kunnen worden ingeschakeld met een pincode of biometrische als een tweede factor.

### <a name="can-admins-set-up-security-keys"></a>Kunnen beheerders beveiligings sleutels instellen?

We werken aan deze mogelijkheid voor algemene Beschik baarheid (GA) van deze functie.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Waar kan ik zoeken naar compatibele beveiligings sleutels?

[FIDO2-beveiligings sleutels](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>Wat moet ik doen als ik mijn beveiligings sleutel kwijt ben?

U kunt sleutels uit de Azure Portal verwijderen door te navigeren naar de pagina beveiligings gegevens en de beveiligings sleutel te verwijderen.

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>Ik kan FIDO niet direct gebruiken nadat ik een hybride Azure AD-computer heb gemaakt

Bij een schone installatie van een hybride Azure AD-computer die is toegevoegd aan het post domein toevoegen en opnieuw opstarten, moet u zich aanmelden met een wacht woord en wachten tot het beleid is gesynchroniseerd voordat u FIDO kunt gebruiken om u aan te melden.

- Controleer de huidige status door `dsregcmd /status` te typen in een opdracht venster en controleer of zowel `AzureAdJoined` als `DomainJoined` worden weer gegeven `YES`.
- Deze vertraging is een bekende beperking voor apparaten die lid zijn van een domein en niet FIDO specifiek.

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>Ik kan geen SSO ontvangen van mijn NTLM-netwerk bron na aanmelding bij FIDO en een prompt voor referenties ophalen

Zorg ervoor dat er voldoende domein controllers zijn geïnstalleerd om te reageren op de tijd om uw resource aanvraag te verwerken. Als u wilt controleren of u een domein controller kunt zien waarop de functie wordt uitgevoerd, controleert u de uitvoer van NLTEST/dsgetdc: contoso/keylist/kdc.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over wacht woorden](concept-authentication-passwordless.md)
