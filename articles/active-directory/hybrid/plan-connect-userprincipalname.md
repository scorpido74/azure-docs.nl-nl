---
title: Populatie van Azure AD UserPrincipalName
description: In het volgende document wordt beschreven hoe het kenmerk UserPrincipalName wordt ingevuld.
author: billmath
ms.subservice: hybrid
ms.author: billmath
ms.date: 06/26/2018
ms.topic: conceptual
ms.workload: identity
ms.service: active-directory
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: c198b329f07c5c7459f25165b2dc0a3bfa032276
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60382068"
---
# <a name="azure-ad-userprincipalname-population"></a>Populatie van Azure AD UserPrincipalName

In dit artikel wordt beschreven hoe het kenmerk UserPrincipalName wordt ingevuld in Azure Active Directory (Azure AD).
De kenmerkwaarde UserPrincipalName is de Azure AD-gebruikersnaam voor de gebruikersaccounts.

## <a name="upn-terminology"></a>UPN-terminologie
De volgende terminologie wordt gebruikt in dit artikel:

|Termijn|Beschrijving|
|-----|-----|
|Eerste domein|Het standaarddomein (onmicrosoft.com) in de Azure AD-tenant. Bijvoorbeeld, contoso.onmicrosoft.com.|
|Microsoft Online-e-mailrouteradres (MOERA)|Azure AD berekent de MOERA vanuit het kenmerk Azure &lt;AD MailNickName en het initiële azure AD-domein als MailNickName&gt;&#64;&lt;eerste domein.&gt;|
|On-premises mailNickName-kenmerk|Een kenmerk in Active Directory, waarvan de waarde de alias van een gebruiker in een Exchange-organisatie vertegenwoordigt.|
|On-premises e-mailkenmerk|Een kenmerk in Active Directory, waarvan de waarde het e-mailadres van een gebruiker vertegenwoordigt|
|Primair SMTP-adres|Het primaire e-mailadres van een Exchange-geadresseerdobject. SMTP:user\@contoso.com.|
|Alternatieve aanmeldings-id|Een on-premises attribuut dat niet door UserPrincipalName is gebruikt, zoals e-mailkenmerk, dat wordt gebruikt voor aanmelding.|

## <a name="what-is-userprincipalname"></a>Wat is UserPrincipalName?
UserPrincipalName is een kenmerk dat een inlognaam in internetstijl is voor een gebruiker op basis van de internetstandaard [RFC 822.](https://www.ietf.org/rfc/rfc0822.txt) 

### <a name="upn-format"></a>UPN-indeling
Een UPN bestaat uit een UPN-voorvoegsel (de naam van het gebruikersaccount) en een UPN-achtervoegsel (een DNS-domeinnaam). Het voorvoegsel wordt samengevoegd met het\@achtervoegsel met behulp van het " symbool "" . Bijvoorbeeld "iemand\@example.com". Een UPN moet uniek zijn tussen alle beveiligingshoofdobjecten binnen een mapforest. 

## <a name="upn-in-azure-ad"></a>UPN in Azure AD 
De UPN wordt door Azure AD gebruikt om gebruikers toe te staan zich aan te melden.  De UPN die een gebruiker kan gebruiken, is afhankelijk van het feit of het domein is geverifieerd.  Als het domein is geverifieerd, mag een gebruiker met dat achtervoegsel zich aanmelden bij Azure AD.  

Het kenmerk wordt gesynchroniseerd door Azure AD Connect.  Tijdens de installatie u de domeinen bekijken die zijn geverifieerd en de domeinen die dat niet hebben gedaan.

   ![Niet-geverifieerde domeinen](./media/plan-connect-userprincipalname/unverifieddomain.png) 

## <a name="alternate-login-id"></a>Alternatieve aanmeldings-id
In sommige omgevingen zijn eindgebruikers mogelijk alleen op de hoogte van hun e-mailadres en niet van hun UPN.  Het gebruik van e-mailadres kan te wijten zijn aan een bedrijfsbeleid of een on-premises afhankelijkheid van zakelijke toepassingen.

Met alternatieve inlog-id u een aanmeldingservaring configureren waarbij gebruikers zich kunnen aanmelden met een ander kenmerk dan hun UPN, zoals e-mail.

Als u Alternatieve aanmeldings-id met Azure AD wilt inschakelen, zijn er geen aanvullende configuraties nodig bij het gebruik van Azure AD Connect. Alternatieve id kan rechtstreeks vanuit de wizard worden geconfigureerd. Zie azure AD-aanmeldingsconfiguratie voor uw gebruikers onder de sectie Synchroniseren. Selecteer onder de vervolgkeuzelijst **Gebruikersnaam** het kenmerk voor Alternatieve inlog-id.

![Niet-geverifieerde domeinen](./media/plan-connect-userprincipalname/altloginid.png)  

Zie [Alternatieve aanmeldings-id configureren](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) en [aanmeldingsconfiguratie voor Azure AD voor](how-to-connect-install-custom.md#azure-ad-sign-in-configuration) meer informatie

## <a name="non-verified-upn-suffix"></a>Niet-geverifieerd UPN-achtervoegsel
Als het kenmerk UserPrincipalName/Alternate login ID-achtervoegsel niet is geverifieerd met Azure AD-tenant, wordt de kenmerkwaarde Azure AD UserPrincipalName ingesteld op MOERA. Azure AD berekent de MOERA vanuit het kenmerk Azure AD &lt;MailNickName &lt;en&gt;het initiële azure AD-domein als MailNickName&gt;&#64;eerste domein.

## <a name="verified-upn-suffix"></a>Geverifieerd UPN-achtervoegsel
Als het kenmerk UserPrincipalName/Alternate login ID-achtervoegsel is geverifieerd met de Azure AD-tenant, is de kenmerkwaarde Azure AD UserPrincipalName dezelfde als het kenmerk UserPrincipalName/Alternate.

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Waardeberekening van kenmerk Azure AD MailNickName
Omdat de kenmerkwaarde Azure AD UserPrincipalName kan worden ingesteld op MOERA, is het belangrijk om te begrijpen hoe de kenmerkwaarde azure AD MailNickName, het MOERA-voorvoegsel, wordt berekend.

Wanneer een gebruikersobject voor de eerste keer wordt gesynchroniseerd met een Azure AD-tenant, controleert Azure AD de volgende items in de gegeven volgorde en stelt u de kenmerkwaarde MailNickName in op de eerste bestaande:

- On-premises mailNickName-kenmerk
- Voorvoegsel van primair SMTP-adres
- Voorvoegsel van on-premises e-mailkenmerk
- Voorvoegsel van on-premises userPrincipalName-kenmerk/Alternatieve aanmeldings-id
- Voorvoegsel van secundair smtp-adres

Wanneer de updates voor een gebruikersobject worden gesynchroniseerd met de Azure AD-tenant, werkt Azure AD de kenmerkwaarde MailNickName alleen bij in het geval er een update is voor de eigenschapwaarde on-premises e-mailNickName.

>[!IMPORTANT]
>Azure AD berekent alleen de kenmerkwaarde UserPrincipalName opnieuw in het geval een update naar het kenmerk UserPrincipalName/Alternate login ID-waarde wordt gesynchroniseerd met de Azure AD-tenant. 
>
>Wanneer Azure AD het kenmerk UserPrincipalName opnieuw berekent, wordt ook de MOERA opnieuw berekend. 

## <a name="upn-scenarios"></a>UPN-scenario's
Hieronder volgen voorbeeldscenario's voor de manier waarop de UPN wordt berekend op basis van het opgegeven scenario.

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>Scenario 1: Niet-geverifieerd UPN-achtervoegsel – eerste synchronisatie

![Scenario1](./media/plan-connect-userprincipalname/example1.png)

On-Premises gebruikersobject:
- mailNickName &lt;: niet ingesteld&gt;
- proxyAdressen :SMTP:us1@contoso.com{ }
- Mail:us2@contoso.com
- userPrincipalName us3@contoso.com: '

Het gebruikersobject voor het eerst gesynchroniseerd met Azure AD-tenant
- Stel het kenmerk Azure AD MailNickName in op het primaire SMTP-adresvoorvoegsel.
- STEL MOERA &lt;in&gt; op &lt;MailNickName&#64;eerste domein.&gt;
- Stel het kenmerk Azure AD UserPrincipalName in op MOERA.

Gebruikersobject Azure AD-tenant:
- MailNickName : ons1           
- UserPrincipalName :us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>Scenario 2: Niet-geverifieerd UPN-achtervoegsel – on-premises e-mailNickName-kenmerk instellen

![Scenario2](./media/plan-connect-userprincipalname/example2.png)

On-Premises gebruikersobject:
- mailNickName : ons4
- proxyAdressen :SMTP:us1@contoso.com{ }
- Mail:us2@contoso.com
- userPrincipalName :us3@contoso.com

Update synchroniseren op kenmerk on-premises e-mailNickName naar Azure AD-tenant
- Azure AD MailNickName-kenmerk bijwerken met het kenmerk On-premises e-mailNickName.
- Omdat er geen update is voor het kenmerk On-premises userPrincipalName, wordt er geen wijziging aangebracht in het kenmerk Azure AD UserPrincipalName.

Gebruikersobject Azure AD-tenant:
- MailNickName : ons4
- UserPrincipalName :us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>Scenario 3: Niet-geverifieerd UPN-achtervoegsel – on-premises kenmerk userPrincipalName bijwerken

![Scenario3](./media/plan-connect-userprincipalname/example3.png)

On-Premises gebruikersobject:
- mailNickName : ons4
- proxyAdressen :SMTP:us1@contoso.com{ }
- Mail:us2@contoso.com
- userPrincipalName :us5@contoso.com

Update synchroniseren op kenmerk on-premises userPrincipalName naar Azure AD-tenant
- Update op on-premises userPrincipalName-kenmerk activeert herberekening van het kenmerk MOERA en Azure AD UserPrincipalName.
- STEL MOERA &lt;in&gt; op &lt;MailNickName&#64;eerste domein.&gt;
- Stel het kenmerk Azure AD UserPrincipalName in op MOERA.

Gebruikersobject Azure AD-tenant:
- MailNickName : ons4
- UserPrincipalName :us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-primary-smtp-address-and-on-premises-mail-attribute"></a>Scenario 4: Niet-geverifieerd UPN-achtervoegsel – primaire SMTP-adres en on-premises e-mailkenmerk bijwerken

![Scenario4](./media/plan-connect-userprincipalname/example4.png)

On-Premises gebruikersobject:
- mailNickName : ons4
- proxyAdressen :SMTP:us6@contoso.com{ }
- Mail:us7@contoso.com
- userPrincipalName :us5@contoso.com

Update synchroniseren op on-premises e-mailkenmerk en primair SMTP-adres naar Azure AD-tenant
- Na de eerste synchronisatie van het gebruikersobject hebben updates van het on-premises e-mailkenmerk en het primaire SMTP-adres geen invloed op het Azure AD MailNickName of het kenmerk UserPrincipalName.

Gebruikersobject Azure AD-tenant:
- MailNickName : ons4
- UserPrincipalName :us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>Scenario 5: Geverifieerd UPN-achtervoegsel – on-premises kenmerkPrincipalName-kenmerk achtervoegsel bijwerken

![Scenario5](./media/plan-connect-userprincipalname/example5.png)

On-Premises gebruikersobject:
- mailNickName : ons4
- proxyAdressen :SMTP:us6@contoso.com{ }
- Mail:us7@contoso.com
- userPrincipalName :us5@verified.contoso.com

Update synchroniseren op basis van kenmerk PrincipalName van gebruiker Aan de Azure AD-tenant
- Update op on-premises userPrincipalName-kenmerk activeert herberekening van het kenmerk Azure AD UserPrincipalName.
- Stel het kenmerk Azure AD UserPrincipalName in op het kenmerk On-premises userPrincipalName, omdat het UPN-achtervoegsel is geverifieerd met de Azure AD-tenant.

Gebruikersobject Azure AD-tenant:
- MailNickName : ons4     
- UserPrincipalName :us5@verified.contoso.com

## <a name="next-steps"></a>Volgende stappen
- [Uw on-premises directory's integreren met Azure Active Directory](whatis-hybrid-identity.md)
- [Aangepaste installatie van Azure AD Connect](how-to-connect-install-custom.md)
