---
title: UPN-wijzigingen (Azure User Principle name) plannen en oplossen
description: Inzicht in bekende problemen en oplossingen voor UPN-wijzigingen
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: how-to
ms.date: 03/13/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: d11be1d971922095d4a1ace1c81c763134b4e58c
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743334"
---
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>Wijzigingen in de naam van de gebruikersnaam plannen en oplossen in Azure Active Directory

Een UPN (User Principal Name) is een kenmerk dat een internetcommunicatiestandaard is voor gebruikersaccounts. Een UPN bestaat uit een UPN-voorvoegsel (de naam van het gebruikersaccount) en een UPN-achtervoegsel (een DNS-domeinnaam). Het voorvoegsel voegt het achtervoegsel toe met het symbool "@". Bijvoorbeeld someone@example.com. Een UPN moet uniek zijn tussen alle beveiligingshoofdobjecten binnen een mapforest. 

> [!NOTE]
> Voor ontwikkelaars raden we u aan de gebruikersobject-ID te gebruiken als onveranderlijke id in plaats van UPN. Als uw toepassingen momenteel UPN gebruiken, raden we u aan de UPN zo in te stellen dat het primaire e-mailadres van de gebruiker overeenkomt om hun ervaring te verbeteren.<br> **In een hybride omgeving is het belangrijk dat de UPN voor een gebruiker identiek is in de on-premises directory en in Azure Active Directory.**

**In dit artikel wordt ervan uitgegaan dat u UPN als gebruikers-id gebruikt. Het richt zich op planning voor UPN-wijzigingen en herstelt van problemen die het gevolg kunnen zijn van UPN-wijzigingen.**

## <a name="learn-about-upns-and-upn-changes"></a>Meer informatie over UPN's en UPN-wijzigingen
Aanmeldingspagina's vragen gebruikers vaak om hun e-mailadres in te voeren wanneer de vereiste waarde eigenlijk hun UPN is. Daarom moet u ervoor zorgen dat de UPN van gebruikers op elk gewenst moment hun primaire e-mailadres wordt gewijzigd.

De primaire e-mailadressen van gebruikers kunnen om verschillende redenen veranderen:

* bedrijfsrebranding

* werknemers verhuizen naar verschillende bedrijfsdivisies 

* fusies en overnames

* wijzigingen in de naam van de werknemer

### <a name="types-of-upn-changes"></a>Typen UPN-wijzigingen

U een UPN wijzigen door het voorvoegsel, het achtervoegsel of beide te wijzigen.

* **Het voorvoegsel wijzigen**.

   *  Als de naam van een persoon bijvoorbeeld is gewijzigd, u de accountnaam wijzigen:  
BSimon@contoso.com aanBJohnson@contoso.com

   * U ook de bedrijfsstandaard voor voorvoegsels wijzigen:  
Bsimon@contoso.com aanBritta.Simon@contoso.com

* **Het achtervoegsel wijzigen.** <br>

    Als een persoon bijvoorbeeld van scheiding verandert, u zijn domein wijzigen: 

   * Britta.Simon@contoso.comAanBritta.Simon@contosolabs.com <br>
     of<br>
    * Britta.Simon@corp.contoso.comAanBritta.Simon@labs.contoso.com 

Wijzig de UPN van de gebruiker telkens wanneer het primaire e-mailadres voor een gebruiker wordt bijgewerkt. Ongeacht de reden voor de e-mailwijziging, de UPN moet altijd worden bijgewerkt om aan te passen.

Controleer tijdens de eerste synchronisatie van Active Directory naar Azure AD of de e-mails van de gebruikers identiek zijn aan hun UPN's.

### <a name="upns-in-active-directory"></a>UPN's in Active Directory

In Active Directory is het standaard UPN-achtervoegsel de DNS-naam van het domein waar u het gebruikersaccount hebt gemaakt. In de meeste gevallen is dit de domeinnaam die u registreert als het bedrijfsdomein op het internet. Als u het gebruikersaccount in het contoso.com domein maakt, is de standaard UPN

username@contoso.com

 U echter [meer UPN-achtervoegsels toevoegen](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain) met Active Directory-domeinen en vertrouwensrelaties. 

U bijvoorbeeld labs.contoso.com toevoegen en de UPN's en e-mail van de gebruikers dat laten weerspiegelen. Ze zouden dan

username@labs.contoso.com.

>[!IMPORTANT]
> Als UPN's in Active directory en Azure Active Directory niet overeenkomen, ontstaan er problemen. Als u [het achtervoegsel in Active Directory wijzigt,](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)moet u ervoor zorgen dat een overeenkomende aangepaste domeinnaam is [toegevoegd en geverifieerd op Azure AD.](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain) 

![Een schermafbeelding van geverifieerde domeinen](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>UPN's in Azure Active Directory

Gebruikers melden zich aan bij Azure AD met de waarde in hun kenmerk UserPrincipalName. 

Wanneer u Azure AD gebruikt in combinatie met uw on-premises Active Directory, worden gebruikersaccounts gesynchroniseerd met behulp van de Azure AD Connect-service. Standaard gebruikt de wizard Azure AD Connect het kenmerk userPrincipalName uit de on-premises Active Directory als upn in Azure AD. U het wijzigen in een ander kenmerk in een aangepaste installatie.

Het is belangrijk dat u een gedefinieerd proces hebt wanneer u een UPN (User Principal Name) van één gebruiker of voor uw hele organisatie bijwerkt. 

Zie de bekende problemen en tijdelijke oplossingen in dit document.

Wanneer u gebruikersaccounts synchroniseert van Active Directory naar Azure AD, controleert u of de UPN's in Active Directory-kaart worden geverifieerd naar geverifieerde domeinen in Azure AD.

![Schermafbeelding van geverifieerde domeinen](./media/howto-troubleshoot-upn-changes/verified-domains.png)

Als de waarde van het kenmerk userPrincipalName niet overeenkomt met een geverifieerd domein in Azure AD, vervangt het synchronisatieproces het achtervoegsel door een standaard waarde van onmicrosoft.com.


### <a name="roll-out-bulk-upn-changes"></a>Wijzigingen in bulk UPN

Volg de [best practices voor een pilot](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) voor bulk UPN-wijzigingen. Heb ook een getest rollback-plan voor het terugdraaien van UPN's als u problemen vindt die niet snel kunnen worden opgelost. Zodra uw pilot wordt uitgevoerd, u beginnen met het targeten van kleine sets gebruikers met verschillende organisatorische rollen en hun specifieke sets apps of apparaten.

Gaan door deze eerste subset van gebruikers geeft u een goed idee van wat gebruikers moeten verwachten als onderdeel van de verandering. Voeg deze informatie toe aan de communicatie van uw gebruikers.

Maak een gedefinieerde procedure voor het wijzigen van UPN's op individuele gebruikers als onderdeel van normale bewerkingen. We raden u aan een geteste procedure te hebben die documentatie bevat over bekende problemen en tijdelijke oplossingen.

In de volgende secties worden mogelijke bekende problemen en tijdelijke oplossingen beschreven wanneer UPN's worden gewijzigd.

## <a name="apps-known-issues-and-workarounds"></a>Bekende problemen en tijdelijke oplossingen voor apps

[Software as a service (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) en Line of Business (LoB)-toepassingen vertrouwen vaak op UPN's om gebruikers te vinden en gebruikersprofielinformatie op te slaan, inclusief rollen. Toepassingen die [Just in Time-inrichting](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) gebruiken om een gebruikersprofiel te maken wanneer gebruikers zich voor de eerste keer aanmelden bij de app, kunnen worden beïnvloed door wijzigingen in UPN.

**Bekend probleem**<br>
Als u de UPN van een gebruiker wijzigt, kan de relatie tussen de Azure AD-gebruiker en het gebruikersprofiel dat op de toepassing is gemaakt, worden verbreken. Als de toepassing [Just in Time-provisioning](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)gebruikt, kan deze een gloednieuw gebruikersprofiel maken. Hiervoor moet de toepassingsbeheerder handmatige wijzigingen aanbrengen om deze relatie op te lossen.

**Oplossing**<br>
[Met Azure AD Automated User Provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) u automatisch uw gebruikersidentiteiten maken, onderhouden en verwijderen in ondersteunde cloudtoepassingen. Het configureren van geautomatiseerde gebruikersinrichting voor uw toepassingen werkt UPN's automatisch bij op de toepassingen. Test de toepassingen als onderdeel van de progressieve implementatie om te valideren dat ze niet worden beïnvloed door UPN-wijzigingen.
Als u een ontwikkelaar bent, u overwegen [SCIM-ondersteuning toe te voegen aan uw toepassing](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups) om automatische gebruikersvoorziening vanuit Azure Active Directory in te schakelen. 

## <a name="managed-devices-known-issues-and-workarounds"></a>Bekende problemen en tijdelijke oplossingen voor beheerde apparaten

Door [uw apparaten naar Azure AD te brengen,](https://docs.microsoft.com/azure/active-directory/devices/overview)maximaliseert u de productiviteit van uw gebruikers door middel van single sign-on (SSO) in uw cloud en on-premises resources.

### <a name="azure-ad-joined-devices"></a>Azure AD-gekoppelde apparaten

[Azure AD-apparaten worden](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join) rechtstreeks verbonden met Azure AD en stellen gebruikers in staat om zich aan te melden bij het apparaat met behulp van de identiteit van hun organisatie.

**Bekende problemen** <br>
Gebruikers kunnen problemen ondervinden met toepassingen die afhankelijk zijn van Azure AD voor verificatie.

**Oplossing** <br>
Geef voldoende tijd voor de UPN-wijziging om te synchroniseren met Azure AD. Zodra u controleert of de nieuwe UPN wordt weergegeven in de Azure AD-portal, vraagt u de gebruiker de tegel 'Andere gebruiker' te selecteren om zich aan te melden met zijn nieuwe UPN. U ook verifiëren via [PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0). Nadat u zich hebt aangemeld bij hun nieuwe UPN, worden verwijzingen naar het oude UPN mogelijk nog steeds weergegeven in de Windows-instelling 'Toegang tot werk of school'.

![Schermafbeelding van geverifieerde domeinen](./media/howto-troubleshoot-upn-changes/other-user.png)

### <a name="hybrid-azure-ad-joined-devices"></a>Hybride Azure AD-gekoppelde apparaten

[Hybride Azure AD-apparaten worden](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join-hybrid) samengevoegd met Active Directory en Azure AD. U Hybride Azure AD-lid implementeren als uw omgeving een on-premises Active Directory-voetafdruk heeft en u ook wilt profiteren van de mogelijkheden van Azure AD.

**Bekende problemen** 

Windows 10 Hybrid Azure AD-apparaten krijgen waarschijnlijk te maken met onverwachte opnieuw opstarten en toegangsproblemen.

Als gebruikers zich bij Windows aanmelden voordat de nieuwe UPN is gesynchroniseerd met Azure AD, of een bestaande Windows-sessie blijven gebruiken, kunnen ze eenmalige aanmeldingsproblemen ondervinden bij toepassingen die Azure AD gebruiken voor verificatie als Voorwaardelijke toegang is geconfigureerd om het gebruik van hybride samengevoegde apparaten af te dwingen om toegang te krijgen tot bronnen. 

Daarnaast verschijnt het volgende bericht, waardoor een herstart na een minuut wordt geforceerd. 

"Uw pc wordt automatisch in één minuut opnieuw opgestart. Windows liep in een probleem en moet opnieuw opstarten. U moet dit bericht nu sluiten en uw werk opslaan".

**Oplossing** 

Het apparaat moet worden losgekoppeld vanuit Azure AD en opnieuw worden opgestart. Na het opnieuw opstarten wordt het apparaat automatisch weer lid van Azure AD en moet de gebruiker zich aanmelden met de nieuwe UPN door de tegel 'Andere gebruiker' te selecteren. Als u de a-join van een apparaat wilt loskoppelen vanuit Azure AD, voert u de volgende opdracht uit bij een opdrachtprompt:

**dsregcmd /leave**

De gebruiker moet [zich opnieuw inschrijven](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision) voor Windows Hello for Business als deze wordt gebruikt. Windows 7- en 8.1-apparaten worden niet beïnvloed door dit probleem nadat UPN is gewijzigd.

## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>Bekende problemen en oplossingen voor Microsoft Authenticator

Uw organisatie kan het gebruik van de [Microsoft Authenticator-app](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview) vereisen om u aan te melden en toegang te krijgen tot organisatorische toepassingen en gegevens. Hoewel een gebruikersnaam in de app kan worden weergegeven, is het account niet ingesteld om te functioneren als verificatiemethode totdat de gebruiker het registratieproces heeft voltooid.

De [Microsoft Authenticator-app](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview) heeft vier hoofdfuncties:

* Meervoudige verificatie via een pushmelding of verificatiecode

* Fungeren als verificatiemakelaar op iOS- en Android-apparaten om één aanmelding te bieden voor toepassingen die [brokered-verificatie](https://docs.microsoft.com/azure/active-directory/develop/brokered-auth) gebruiken

* Apparaatregistratie (ook bekend als Workplace Join) bij Azure AD, wat een vereiste is voor andere functies zoals Intune-appbeveiliging en apparaatregistratie/-beheer,

* Telefoonaanmelden, waarvoor MFA en apparaatregistratie vereist zijn.

### <a name="multi-factor-authentication-with-android-devices"></a>Multi-Factor Authenticatie met Android-apparaten

De Microsoft Authenticator-app biedt een out-of-band verificatieoptie. In plaats van een geautomatiseerd telefoongesprek of sms naar de gebruiker te plaatsen tijdens het aanmelden, pusht [Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) een melding naar de Microsoft Authenticator-app op de smartphone of tablet van de gebruiker. De gebruiker tikt gewoon op Goedkeuren (of voert een pincode of biometrische gegevens in en tikt op 'Authenticeren') in de app om de aanmelding te voltooien.

**Bekende problemen** 

Wanneer u de UPN van een gebruiker wijzigt, wordt het oude UPN nog steeds weergegeven op het gebruikersaccount en wordt er mogelijk geen melding ontvangen. [Verificatiecodes](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-faq) blijven werken.

**Oplossing**

Als er een melding wordt ontvangen, instrueert u de gebruiker de melding te verwijderen, opent u de authenticator-app, tikt u op de optie Controleren op meldingen en keurt u de MFA-prompt goed. Hierna wordt de UPN die op het account wordt weergegeven, bijgewerkt. Let op: de bijgewerkte UPN kan worden weergegeven als een nieuw account, dit is te wijten aan andere Authenticator functionaliteit wordt gebruikt. Voor meer informatie verwijzen wij u naar de extra bekende problemen in dit artikel.

### <a name="brokered-authentication"></a>Gebrokereerde verificatie

Op Android en iOS makelaars zoals Microsoft Authenticator inschakelen:

* Single sign-on (SSO) - Uw gebruikers hoeven zich niet bij elke toepassing aan te melden.

* Apparaatidentificatie - De makelaar heeft toegang tot het apparaatcertificaat dat is gemaakt op het apparaat toen het op de werkplek werd samengevoegd.

* Verificatie van toepassingsidentificatie - Wanneer een toepassing de makelaar aanroept, geeft deze de omleidings-URL door en controleert de makelaar deze.

Daarnaast kunnen toepassingen deelnemen aan meer geavanceerde functies, zoals [voorwaardelijke toegang,](https://docs.microsoft.com/azure/active-directory/conditional-access/)en ondersteunt [Microsoft Intune-scenario's](https://docs.microsoft.com/azure/active-directory/develop/msal-net-use-brokers-with-xamarin-apps).

**Bekende problemen**<br>
Gebruiker wordt gepresenteerd met meer interactieve authenticatie prompts op nieuwe toepassingen die broker-assisted sign-in gebruiken als gevolg van een mismatch tussen de login_hint doorgegeven door de toepassing en de UPN opgeslagen op de makelaar.

**Oplossing** <br> De gebruiker moet het account handmatig verwijderen van Microsoft Authenticator en een nieuwe aanmelding starten vanuit een broker-ondersteunde toepassing. Het account wordt automatisch toegevoegd na de eerste verificatie.

### <a name="device-registration"></a>Apparaatregistratie

De Microsoft Authenticator-app is verantwoordelijk voor de registratie van het apparaat bij Azure AD. Met apparaatregistratie kan het apparaat zich verifiëren bij Azure AD en is dit een vereiste voor de volgende scenario's:

* Intune-app-beveiliging

* Inschrijving voor apparaten inschrijven

* Aanmelden via telefoon

**Bekende problemen**<br>
Wanneer u de UPN wijzigt, wordt er een nieuw account met de nieuwe UPN weergegeven in de Microsoft Authenticator-app, terwijl het account met het oude UPN nog steeds wordt vermeld. Bovendien wordt de oude UPN weergegeven in de sectie Apparaatregistratie in de app-instellingen. Er is geen wijziging in de normale functionaliteit van apparaatregistratie of de afhankelijke scenario's.

**Oplossing** <br> Als u alle verwijzingen naar de oude UPN in de Microsoft Authenticator-app wilt verwijderen, geeft u de gebruiker opdracht om zowel de oude als de nieuwe accounts handmatig te verwijderen uit Microsoft Authenticator, zich opnieuw te registreren voor MFA en opnieuw bij het apparaat te komen.

### <a name="phone-sign-in"></a>Aanmelden via telefoon

Met aanmelding via de telefoon kunnen gebruikers zich aanmelden bij Azure AD zonder wachtwoord. Om zich aan te melden bij de telefoon, moet de gebruiker zich registreren voor MFA via de Authenticator-app en vervolgens direct aanmelden via de telefoon inschakelen op Authenticator. Als onderdeel van de configuratie registreert het apparaat zich bij Azure AD.

**Bekende problemen** <br>
Gebruikers kunnen geen telefoonaanmelding gebruiken omdat ze geen melding ontvangen. Als de gebruiker op Controleren op meldingen tikt, krijgt hij of zij een foutmelding.

**Oplossing**<br>
De gebruiker moet het vervolgkeuzemenu selecteren in het account dat is ingeschakeld voor aanmelding via telefoon en de optie Aanmelden voor de telefoon uitschakelen. Indien gewenst kan aanmelden via de telefoon opnieuw worden ingeschakeld.

## <a name="security-key-fido2-known-issues-and-workarounds"></a>Bekende problemen en oplossingen voor beveiligingssleutel (FIDO2)

**Bekende problemen** <br>
Wanneer meerdere gebruikers op dezelfde toets zijn geregistreerd, wordt in het aanmeldingsscherm een accountselectiepagina weergegeven waar het oude UPN wordt weergegeven. Aanmeldingen met beveiligingssleutels worden niet beïnvloed door wijzigingen in UPN.  

**Oplossing**<br>
Als u verwijzingen naar oude UPN's wilt verwijderen, moeten gebruikers [de beveiligingssleutel opnieuw instellen en opnieuw registreren.](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key#known-issues)

## <a name="onedrive-known-issues-and-workarounds"></a>Bekende problemen en oplossingen voor OneDrive

OneDrive-gebruikers staan erom bekend problemen te ondervinden nadat UPN is gewijzigd. Zie [Hoe wijzigingen van UPN van invloed zijn op de ONEDrive-URL en OneDrive-functies](https://docs.microsoft.com/onedrive/upn-changes)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende bronnen:
* [Azure AD Connect: ontwerpconcepten](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts)

* [Populatie van Azure AD UserPrincipalName](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-userprincipalname)

* [Microsoft identity platform ID-tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
