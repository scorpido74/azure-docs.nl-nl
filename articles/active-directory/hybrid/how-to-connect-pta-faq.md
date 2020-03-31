---
title: 'Azure AD Connect: verificatie voor doorgeefgegevens - veelgestelde vragen | Microsoft Documenten'
description: Antwoorden op veelgestelde vragen over Azure Active Directory Pass-through Authentication
services: active-directory
keywords: Azure AD Connect Pass-Through Authentication, installeer Active Directory, vereiste onderdelen voor Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59bf7ae5914f5cc886d95f25b36abccfdf09c4a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331286"
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Azure Active Directory Pass-Through-verificatie: veelgestelde vragen

In dit artikel worden veelgestelde vragen over Azure Active Directory (Azure AD) Pass-through Authentication beantwoord. Blijf terugkomen op bijgewerkte inhoud.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Welke van de methoden om u aan te melden bij Azure AD, Pass-through Authentication, password hash synchronization en Active Directory Federation Services (AD FS), moet ik kiezen?

Bekijk [deze handleiding](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) voor een vergelijking van de verschillende Azure AD-aanmeldingsmethoden en hoe u de juiste aanmeldingsmethode voor uw organisatie kiezen.

## <a name="is-pass-through-authentication-a-free-feature"></a>Is Pass-through Authentication een gratis functie?

Pass-through Authentication is een gratis functie. U hebt geen betaalde edities van Azure AD nodig om het te gebruiken.

## <a name="is-pass-through-authentication-available-in-the-microsoft-azure-germany-cloud-and-the-microsoft-azure-government-cloud"></a>Is Pass-through Authentication beschikbaar in de [Microsoft Azure Germany cloud](https://www.microsoft.de/cloud-deutschland) en de Microsoft Azure Government [cloud?](https://azure.microsoft.com/features/gov/)

Nee. Pass-through Authentication is alleen beschikbaar in het wereldwijde exemplaar van Azure AD.

## <a name="does-conditional-access-work-with-pass-through-authentication"></a>Werkt [voorwaardelijke toegang](../active-directory-conditional-access-azure-portal.md) met pass-through-verificatie?

Ja. Alle mogelijkheden voor voorwaardelijke toegang, waaronder Azure Multi-Factor Authentication, werken met Pass-through Authentication.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Ondersteunt Pass-through Authentication "Alternatieve ID" als gebruikersnaam, in plaats van "userPrincipalName"?
Aanmelden met een niet-UPN-waarde, zoals een alternatieve e-mail, wordt momenteel getest in private preview voor zowel pass-through authentication (PTA) als password hash sync (PHS).

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>Werkt wachtwoordhashsynchronisatie als een terugval naar Pass-through Authentication?

Nee. Pass-through Authentication _mislukt niet_ automatisch naar wachtwoordhashsynchronisatie. Om aanmeldingsfouten bij gebruikers te voorkomen, moet u Pass-through Authentication configureren voor [hoge beschikbaarheid.](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="what-happens-when-i-switch-from-password-hash-synchronization-to-pass-through-authentication"></a>Wat gebeurt er als ik overstap van wachtwoordhashsynchronisatie naar Pass-through Authentication?

Wanneer u Azure AD Connect gebruikt om de aanmeldingsmethode over te schakelen van wachtwoordhashsynchronisatie naar pass-through-verificatie, wordt pass-through-verificatie de primaire aanmeldingsmethode voor uw gebruikers in beheerde domeinen. Houd er rekening mee dat de wachtwoordhashes van alle gebruikers die eerder zijn gesynchroniseerd met wachtwoordhashsynchronisatie, nog steeds worden opgeslagen in Azure AD.

## <a name="can-i-install-an-azure-ad-application-proxy-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Kan ik een [Azure AD Application Proxy-connector](../manage-apps/application-proxy.md) installeren op dezelfde server als een Pass-through Authentication Agent?

Ja. De nieuwe versies van de Pass-through Authentication Agent, versie 1.5.193.0 of hoger, ondersteunen deze configuratie.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Welke versies van Azure AD Connect en Pass-through Authentication Agent hebt u nodig?

Als deze functie werkt, hebt u versie 1.1.750.0 of hoger nodig voor Azure AD Connect en 1.5.193.0 of hoger voor de verificatieagent voor doorgeefverificatie. Installeer alle software op servers met Windows Server 2012 R2 of hoger.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>Wat gebeurt er als het wachtwoord van mijn gebruiker is verlopen en ze proberen in te loggen met pass-through-verificatie?

Als u [wachtwoordterugschrijfinformatie](../authentication/concept-sspr-writeback.md) hebt geconfigureerd voor een specifieke gebruiker en als de gebruiker zich aanmeldt met pass-through-verificatie, kunnen ze hun wachtwoorden wijzigen of opnieuw instellen. De wachtwoorden worden zoals verwacht teruggeschreven naar on-premises Active Directory.

Als u geen wachtwoordterugschrijfinformatie hebt geconfigureerd voor een specifieke gebruiker of als de gebruiker geen geldige Azure AD-licentie heeft toegewezen, kan de gebruiker zijn wachtwoord niet bijwerken in de cloud. Ze kunnen hun wachtwoord niet bijwerken, zelfs niet als hun wachtwoord is verlopen. De gebruiker ziet in plaats daarvan dit bericht: "Uw organisatie staat u niet toe om uw wachtwoord op deze site bij te werken. Werk het bij volgens de methode die door uw organisatie wordt aanbevolen, of vraag uw beheerder of u hulp nodig hebt." De gebruiker of de beheerder moet zijn wachtwoord opnieuw instellen in de on-premises Active Directory.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Hoe beschermt Pass-through Authentication u tegen brute-force wachtwoordaanvallen?

[Lees informatie over Smart Lockout](../authentication/howto-password-smart-lockout.md).

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Wat communiceren pass-through-verificatieagents via poorten 80 en 443?

- De verificatieagents doen HTTPS-verzoeken via poort 443 voor alle functiebewerkingen.
- De verificatieagents doen HTTP-verzoeken via poort 80 om de TLS/SSL-certificaatintrekkingslijsten (CRL's) te downloaden.

     >[!NOTE]
     >Recente updates hebben het aantal poorten dat de functie vereist, verminderd. Als u oudere versies van Azure AD Connect of de verificatieagent hebt, houdt u deze poorten ook open: 5671, 8080, 9090, 9091, 9350, 9352 en 10100-10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Kunnen de pass-through-verificatiemedewerkers communiceren via een uitgaande webproxyserver?

Ja. Als Web Proxy Auto-Discovery (WPAD) is ingeschakeld in uw on-premises omgeving, proberen verificatiemedewerkers automatisch een webproxyserver in het netwerk te vinden en te gebruiken.

Als u geen WPAD in uw omgeving hebt, u proxy-informatie (zoals hieronder weergegeven) toevoegen om een verificatieagent voor doorgeefstatus te laten communiceren met Azure AD:
- Configureer proxy-informatie in Internet Explorer voordat u de doorpasverificatieagent op de server installeert. Hiermee u de installatie van de verificatieagent voltooien, maar deze wordt nog steeds weergegeven als **Inactief** op de beheerportal.
- Navigeer op de server naar 'C:\Program Files\Microsoft Azure AD Connect Authentication Agent'.
- Bewerk het configuratiebestand 'AzureADConnectAuthenticationAgentService' en voeg de\:volgende regels toe (vervang 'http -contosoproxy.com:8080' door uw werkelijke proxyadres):

```
   <system.net>
      <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy
            usesystemdefault="true"
            proxyaddress="http://contosoproxy.com:8080"
            bypassonlocal="true"
         />
     </defaultProxy>
   </system.net>
```

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Kan ik twee of meer Pass-through Authentication Agents op dezelfde server installeren?

Nee, u slechts één pass-through-verificatieagent op één server installeren. Als u Pass-through Authentication wilt configureren voor hoge beschikbaarheid, [volgt u de instructies hier.](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="do-i-have-to-manually-renew-certificates-used-by-pass-through-authentication-agents"></a>Moet ik certificaten die worden gebruikt door Pass-through Authentication Agents handmatig verlengen?

De communicatie tussen elke Pass-through Authentication Agent en Azure AD wordt beveiligd met behulp van verificatie op basis van certificaten. Deze [certificaten worden om de paar maanden automatisch vernieuwd door Azure AD.](how-to-connect-pta-security-deep-dive.md#operational-security-of-the-authentication-agents) Het is niet nodig om deze certificaten handmatig te vernieuwen. U oudere verlopen certificaten naar behoefte opschonen.

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>Hoe verwijder ik een verificatieagent voor doorgeefgegevens?

Zolang een Pass-through Authentication Agent actief is, blijft deze actief en verwerkt deze voortdurend aanmeldingsverzoeken van gebruikers. Als u een verificatieagent wilt verwijderen, gaat u naar **het Configuratiescherm -> Programma's -> programma's en onderdelen** en verwijdert u zowel de Microsoft Azure AD **Connect-verificatieagent** als de Microsoft Azure AD Connect Agent Updater-programma's. **Microsoft Azure AD Connect Agent Updater**

Als u het verificatieblad pass-through controleert in het [Azure Active Directory-beheercentrum](https://aad.portal.azure.com) nadat u de vorige stap hebt voltooid, ziet u de verificatieagent die wordt weergegeven als **Inactief**. Dit wordt _verwacht_. De verificatieagent wordt na een paar dagen automatisch uit de lijst verwijderd.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Ik gebruik AD FS al om me aan te melden bij Azure AD. Hoe schakel ik deze over naar Pass-through Authentication?

Als u migreert van AD FS (of andere federatietechnologieën) naar Pass-through Authentication, raden we u ten zeerste aan onze gedetailleerde implementatiehandleiding te volgen die [hier](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true)wordt gepubliceerd.

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>Kan ik Pass-through Authentication gebruiken in een Active Directory-omgeving met meerdere forest's?

Ja. Multiforest-omgevingen worden ondersteund als er forestvertrouwensrelaties zijn tussen uw Active Directory-forests en als de routeringsplaats voor naamachtervoegsel correct is geconfigureerd.

## <a name="does-pass-through-authentication-provide-load-balancing-across-multiple-authentication-agents"></a>Biedt Pass-through Authentication taakverdeling voor meerdere verificatieagents?

Nee, het installeren van meerdere Pass-through Authentication Agents zorgt alleen voor [hoge beschikbaarheid.](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) Het biedt geen deterministische taakverdeling tussen de verificatieagents. Elke verificatieagent (willekeurig) kan een bepaalde aanmeldingsaanvraag van een gebruiker verwerken.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Hoeveel pass-through verificatieagents moet ik installeren?

Het installeren van meerdere Pass-through Authentication Agents zorgt voor [een hoge beschikbaarheid.](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) Het biedt echter geen deterministische taakverdeling tussen de verificatieagents.

Houd rekening met de piek en de gemiddelde belasting van aanmeldingsaanvragen die u verwacht te zien op uw tenant. Als benchmark kan één verificatieagent 300 tot 400 verificaties per seconde verwerken op een standaard 4-core CPU, 16 GB RAM-server.

Als u het netwerkverkeer wilt schatten, gebruikt u de volgende richtlijnen voor het formaat:
- Elk verzoek heeft een laadvermogen grootte van (0,5K + 1K * num_of_agents) bytes; d.w.z., gegevens van Azure AD naar de verificatieagent. Hier geeft 'num_of_agents' het aantal verificatieagents aan dat op uw tenant is geregistreerd.
- Elke respons heeft een laadvermogen van 1K bytes; d.w.z., gegevens van de verificatieagent naar Azure AD.

Voor de meeste klanten zijn twee of drie verificatiemedewerkers in totaal voldoende voor hoge beschikbaarheid en capaciteit. Installeer verificatieagents dicht bij uw domeincontrollers om de aanmeldingslatentie te verbeteren.

>[!NOTE]
>Er is een systeemlimiet van 40 verificatieagents per tenant.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Kan ik de eerste verificatieagent voor doorgeefkunde installeren op een andere server dan de server waarop Azure AD Connect wordt uitgevoerd?

Nee, dit scenario wordt _niet_ ondersteund.

## <a name="why-do-i-need-a-cloud-only-global-administrator-account-to-enable-pass-through-authentication"></a>Waarom heb ik een global administrator-account nodig om pass-through-verificatie in te schakelen?

Het wordt aanbevolen pass-through-verificatie in te schakelen of uit te schakelen met behulp van een global administrator-account met alleen de cloud. Meer informatie over [het toevoegen van een global administrator-account met alleen cloud](../active-directory-users-create-azure-portal.md). Als u dit op deze manier doet, zorgt u ervoor dat u niet buitengesloten wordt van uw huurder.

## <a name="how-can-i-disable-pass-through-authentication"></a>Hoe kan ik pass-through-verificatie uitschakelen?

Voer de wizard Azure AD Connect opnieuw uit en wijzig de aanmeldingsmethode van de gebruiker van Pass-through Authentication naar een andere methode. Met deze wijziging schakelt pass-through-verificatie op de tenant uit en verwijdert u de verificatieagent van de server. U moet de verificatieagents handmatig verwijderen van de andere servers.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Wat gebeurt er als ik een pass-through-verificatieagent verwijder?

Als u een pass-through-verificatieagent van een server verwijdert, wordt de server niet meer geaccepteerd voor aanmeldingsverzoeken. Als u wilt voorkomen dat de aanmeldingsmogelijkheid van de gebruiker op uw tenant wordt afgebroken, moet u ervoor zorgen dat er een andere verificatieagent wordt uitgevoerd voordat u een verificatieagent voor doorgeefgegevens verwijdert.

## <a name="i-have-an-older-tenant-that-was-originally-setup-using-ad-fs--we-recently-migrated-to-pta-but-now-are-not-seeing-our-upn-changes-synchronizing-to-azure-ad--why-are-our-upn-changes-not-being-synchronized"></a>Ik heb een oudere tenant die oorspronkelijk was ingesteld met behulp van AD FS.  We zijn onlangs gemigreerd naar PTA, maar zien onze UPN-wijzigingen nu niet synchroniseren met Azure AD.  Waarom worden onze UPN-wijzigingen niet gesynchroniseerd?

A: Onder de volgende omstandigheden kunnen uw on-premises UPN-wijzigingen niet worden gesynchroniseerd als:

- Uw Azure AD-tenant is gemaakt vóór 15 juni 2015
- U bent in eerste instantie gefedeerd met uw Azure AD-tenant met AD FS voor verificatie
- U bent overgestapt naar beheerde gebruikers die PTA gebruiken als verificatie

Dit komt omdat het standaardgedrag van huurders die vóór 15 juni 2015 zijn gemaakt, upn-wijzigingen moest blokkeren.  Als u UPN-wijzigingen moet ongedaan maken, moet u de volgende PowerShell-cmdlt uitvoeren:  

`Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $True`

Tenants die na 15 juni 2015 zijn gemaakt, hebben het standaardgedrag van het synchroniseren van UPN-wijzigingen.   



## <a name="next-steps"></a>Volgende stappen
- [Huidige beperkingen](how-to-connect-pta-current-limitations.md): Ontdek welke scenario's worden ondersteund en welke niet.
- [Snel aan de slag:](how-to-connect-pta-quick-start.md)aan de slag met Azure AD Pass-through Authentication.
- [Migreren van AD FS naar Pass-through Authentication](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) - Een gedetailleerde handleiding om te migreren van AD FS (of andere federatietechnologieën) naar Pass-through Authentication.
- [Smart Lockout:](../authentication/howto-password-smart-lockout.md)meer informatie over het configureren van de Smart Lockout-mogelijkheid op uw tenant om gebruikersaccounts te beschermen.
- [Technische diepe duik:](how-to-connect-pta-how-it-works.md)Begrijp hoe de Pass-through Authentication-functie werkt.
- [Problemen oplossen:](tshoot-connect-pass-through-authentication.md)meer informatie over het oplossen van veelvoorkomende problemen met de functie Doorgeefverificatie.
- [Security deep dive](how-to-connect-pta-security-deep-dive.md): Krijg diepgaande technische informatie over de Pass-through Authentication-functie.
- [Azure AD Seamless SSO:](how-to-connect-sso.md)Meer informatie over deze aanvullende functie.
- [UserVoice:](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect)gebruik het Azure Active Directory Forum om nieuwe functieaanvragen in te dienen.

