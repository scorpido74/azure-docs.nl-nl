---
title: Controlelijst voor Azure AD-implementatie
description: Checklist voor het implementeren van Azure Active Directory-functies
services: active-directory
ms.service: active-directory
ms.subservice: ''
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: f84226a631014b51338d47887fe3bafc969dc571
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063642"
---
# <a name="azure-active-directory-feature-deployment-guide"></a>Handleiding voor azure Active Directory-functieimplementatie

Het kan ontmoedigend lijken om Azure Active Directory (Azure AD) voor uw organisatie te implementeren en veilig te houden. In dit artikel worden veelvoorkomende taken geïdentificeerd die klanten nuttig vinden om gefaseerd te voltooien, in de loop van 30, 60, 90 dagen of meer, om hun beveiligingshouding te verbeteren. Zelfs organisaties die Azure AD al hebben geïmplementeerd, kunnen deze handleiding gebruiken om ervoor te zorgen dat ze het meeste uit hun investering halen.

Een goed geplande en uitgevoerde identiteitsinfrastructuur maakt de weg vrij voor veilige toegang tot uw productiviteitsworkloads en gegevens alleen door bekende gebruikers en apparaten.

Daarnaast kunnen klanten hun [identiteitsveilige score](identity-secure-score.md) controleren om te zien hoe uitgelijnd ze zijn op de best practices van Microsoft. Controleer uw veilige score voor en na de uitvoering van deze aanbevelingen om te zien hoe goed u het doet in vergelijking met anderen in uw branche en met andere organisaties van uw omvang.

## <a name="prerequisites"></a>Vereisten

Veel van de aanbevelingen in deze handleiding kunnen worden geïmplementeerd met Azure AD Free of helemaal geen licentie. Wanneer licenties vereist zijn, geven we aan welke licentie minimaal vereist is om de taak te volbrengen.

Aanvullende informatie over licenties is te vinden op de volgende pagina's:

* [Azure AD-licenties](https://azure.microsoft.com/pricing/details/active-directory/)
* [Microsoft 365 Zakelijk](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)
* [Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)
* [Richtlijnen voor Azure AD B2B-licenties](../b2b/licensing-guidance.md)

## <a name="phase-1-build-a-foundation-of-security"></a>Fase 1: Bouw een fundament van veiligheid

In deze fase stellen beheerders basislijnbeveiligingsfuncties in om een veiligere en gebruiksvriendelijkebasis in Azure AD te maken voordat we normale gebruikersaccounts importeren of maken. Deze fundamentele fase zorgt ervoor dat u vanaf het begin in een veiligere staat verkeert en dat uw eindgebruikers slechts één keer aan nieuwe concepten hoeven te worden voorgesteld.

| Taak | Detail | Vereiste licentie |
| ---- | ------ | ---------------- |
| [Meer dan één globale beheerder aanwijzen](../users-groups-roles/directory-emergency-access.md) | Wijs ten minste twee permanente globale beheerdersaccounts toe voor gebruik in de cloud voor gebruik als er een noodsituatie is. Deze accounts worden niet dagelijks gebruikt en moeten lange en complexe wachtwoorden hebben. | Azure AD Free |
| [Gebruik waar mogelijk niet-globale administratieve rollen](../users-groups-roles/directory-assign-admin-roles.md) | Geef uw beheerders alleen de toegang die ze nodig hebben tot alleen de gebieden waartoe ze toegang nodig hebben. Niet alle beheerders hoeven globale beheerders te zijn. | Azure AD Free |
| [Privileged Identity Management inschakelen voor het gebruik van beheerderstaken](../privileged-identity-management/pim-getting-started.md) | Privileged Identity Management inschakelen om het beheervan de rol gebruik te volgen. | Azure AD Premium P2 |
| [Selfservice voor wachtwoordherstel implementeren](../authentication/howto-sspr-deployment.md) | Verminder helpdeskoproepen voor wachtwoordresets door medewerkers in staat te stellen hun eigen wachtwoorden opnieuw in te stellen met behulp van beleid dat u als beheerder beheert. | |
| [Een lijst met aangepaste aangepaste geblokkeerde wachtwoorden maken](../authentication/howto-password-ban-bad-configure.md) | Voorkomen dat gebruikers wachtwoorden maken die veelvoorkomende woorden of zinnen uit uw organisatie of gebied bevatten. | |
| [On-premises integratie inschakelen met Azure AD-wachtwoordbeveiliging](../authentication/concept-password-ban-bad-on-premises.md) | Breid de lijst met verboden wachtwoorden uit naar uw on-premises directory, om ervoor te zorgen dat wachtwoorden die on-premises zijn ingesteld, ook voldoen aan de algemene en tenantspecifieke lijst met verboden wachtwoorden. | Azure AD Premium P1 |
| [Wachtwoordrichtlijnen van Microsoft inschakelen](https://www.microsoft.com/research/publication/password-guidance/) | Stel ervoor dat gebruikers hun wachtwoord niet in een vast schema hoeven te wijzigen, schakel complexiteitsvereisten uit en uw gebruikers zijn meer geneigd om hun wachtwoorden te onthouden en ze iets te bewaren dat veilig is. | Azure AD Free |
| [Periodieke wachtwoordresets voor gebruikersaccounts in de cloud uitschakelen](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire) | Periodieke wachtwoordresets moedigen uw gebruikers aan om hun bestaande wachtwoorden te verhogen. Gebruik de richtlijnen in het document voor wachtwoordrichtlijnen van Microsoft en spiegel uw on-premises beleid voor alleen in de cloud. | Azure AD Free |
| [Azure Active Directory smart lockout aanpassen](../authentication/howto-password-smart-lockout.md) | Voorkomen dat uitsluitingen van cloudgebruikers worden gerepliceerd naar on-premises Active Directory-gebruikers | |
| [Extranet Smart Lockout inschakelen voor AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) | AD FS extranet lockout beschermt tegen brute force wachtwoord raden aanvallen, terwijl geldige AD FS-gebruikers blijven hun accounts te gebruiken. | |
| [Azure AD-verificatie met meerdere factor implementeren met beleid voor voorwaardelijke toegang](../authentication/howto-mfa-getstarted.md) | Gebruikers vereisen dat gebruikers tweestapsverificatie uitvoeren wanneer ze toegang krijgen tot gevoelige toepassingen met behulp van beleid voor voorwaardelijke toegang. | Azure AD Premium P1 |
| [Azure Active Directory Identity Protection inschakelen](../identity-protection/overview-identity-protection.md) | Schakel het bijhouden van risicovolle aanmeldingen en gecompromitteerde referenties voor gebruikers in uw organisatie in. | Azure AD Premium P2 |
| [Gebruik risicodetecties om meervoudige verificatie en wachtwoordwijzigingen te activeren](../authentication/tutorial-risk-based-sspr-mfa.md) | Inschakelen van automatisering die gebeurtenissen kan activeren, zoals multi-factor authenticatie, wachtwoord reset, en het blokkeren van aanmeldingen op basis van risico.Enable automation that can trigger events such as multi-factor authentication, password reset, and blocking of sign-ins based on risk. | Azure AD Premium P2 |
| [Geconvergeerde registratie inschakelen voor het opnieuw instellen van selfservicewachtwoorden en Azure AD Multi-Factor Authentication (voorbeeld)](../authentication/concept-registration-mfa-sspr-converged.md) | Laat uw gebruikers zich registreren vanuit één gemeenschappelijke ervaring voor zowel Azure Multi-Factor Authentication als selfservice wachtwoordreset. | Azure AD Premium P1 |

## <a name="phase-2-import-users-enable-synchronization-and-manage-devices"></a>Fase 2: Gebruikers importeren, synchronisatie inschakelen en apparaten beheren

Vervolgens voegen we toe aan de basis die in fase 1 is gelegd door onze gebruikers te importeren en synchronisatie mogelijk te maken, gasttoegang te plannen en ons voor te bereiden op extra functionaliteit.

| Taak | Detail | Vereiste licentie |
| ---- | ------ | ---------------- |
| [Azure AD Connect installeren](../connect/active-directory-aadconnect-select-installation.md) | Bereid u voor om gebruikers van uw bestaande on-premises directory te synchroniseren met de cloud. | Azure AD Free |
| [Wachtwoordhashsynchronisatie implementeren](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md) | Wachtwoordhashes synchroniseren om wachtwoordwijzigingen te repliceren, slechte wachtwoorddetectie en -herstel te accepteren en gelekte referentierapportage. | Azure AD Free |
| [Terugschrijven van wachtwoorden implementeren](../authentication/howto-sspr-writeback.md) | Toestaan dat wachtwoordwijzigingen in de cloud worden teruggeschreven naar een on-premises Windows Server Active Directory-omgeving. | Azure AD Premium P1 |
| [Azure AD Connect-status implementeren](../connect-health/active-directory-aadconnect-health.md) | Schakel bewaking van belangrijke statusstatistieken in voor uw Azure AD Connect-servers, AD FS-servers en domeincontrollers. | Azure AD Premium P1 |
| [Licenties toewijzen aan gebruikers per groepslidmaatschap in Azure Active Directory](../users-groups-roles/licensing-groups-assign.md) | Bespaar tijd en moeite door licentiegroepen te maken die functies per groep in- of uitschakelen in plaats van per gebruiker in te stellen. | |
| [Een abonnement maken voor de toegang van gastgebruikers](../b2b/what-is-b2b.md) | Werk samen met gastgebruikers door ze zich te laten aanmelden bij uw apps en services met hun eigen werk, school of sociale identiteit. | [Richtlijnen voor Azure AD B2B-licenties](../b2b/licensing-guidance.md) |
| [Bepaal de strategie voor apparaatbeheer](../devices/overview.md) | Bepaal wat uw organisatie toestaat met betrekking tot apparaten. Registreren vs deelnemen, Bring Your Own Device vs bedrijf verstrekt. | |
| [Windows Hello voor Bedrijven implementeren in uw organisatie](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) | Voorbereiden op verificatie zonder wachtwoord met Windows Hello | |
| [Wachtwoordloze verificatiemethoden implementeren voor uw gebruikers](../authentication/concept-authentication-passwordless.md) | Uw gebruikers voorzien van handige wachtwoordloze verificatiemethoden | Azure AD Premium P1 |

## <a name="phase-3-manage-applications"></a>Fase 3: Toepassingen beheren

Terwijl we doorgaan met het voortbouwen op de vorige fasen, identificeren we kandidaat-toepassingen voor migratie en integratie met Azure AD en voltooien we de installatie van die toepassingen.

| Taak | Detail | Vereiste licentie |
| ---- | ------ | ---------------- |
| Uw toepassingen identificeren | Identificeer toepassingen die in uw organisatie worden gebruikt: on-premises, SaaS-toepassingen in de cloud en andere bedrijfstoepassingen. Bepaal of deze toepassingen kunnen en moeten worden beheerd met Azure AD. | Geen licentie vereist |
| [Ondersteunde SaaS-toepassingen integreren in de galerie](../manage-apps/add-application-portal.md) | Azure AD heeft een galerie met duizenden vooraf geïntegreerde toepassingen. Sommige toepassingen die uw organisatie gebruikt, bevinden zich waarschijnlijk in de galerie die rechtstreeks vanuit de Azure-portal toegankelijk is. | Azure AD Free |
| [Application Proxy gebruiken om on-premises toepassingen te integreren](../manage-apps/application-proxy-add-on-premises-application.md) | Met Application Proxy kunnen gebruikers on-premises toepassingen openen door zich aan te melden met hun Azure AD-account. | |

## <a name="phase-4-audit-privileged-identities-complete-an-access-review-and-manage-user-lifecycle"></a>Fase 4: Bevoorrechte identiteiten controleren, een toegangscontrole voltooien en de levenscyclus van gebruikers beheren

In fase 4 worden beheerders de minste bevoegdhedenbeginselen voor beheer gehandhaafd, hun eerste toegangsbeoordelingen voltooid en wordt de algemene levenscyclustaken van gebruikers mogelijk gemaakt.

| Taak | Detail | Vereiste licentie |
| ---- | ------ | ---------------- |
| [Het gebruik van privileged identity management afdwingen](../privileged-identity-management/pim-security-wizard.md) | Verwijder beheerdersrollen van normale dagelijkse gebruikersaccounts. Maak administratieve gebruikers in aanmerking om hun rol te gebruiken na het slagen voor een multi-factor authenticatie controle, het verstrekken van een zakelijke rechtvaardiging, of het aanvragen van goedkeuring van aangewezen fiatteurs. | Azure AD Premium P2 |
| [Een toegangscontrole voor Azure AD-maprollen in PIM voltooien](../privileged-identity-management/pim-how-to-start-security-review.md) | Werk samen met uw beveiligings- en leiderschapsteams om een toegangsbeoordelingsbeleid te maken om de beheerderstoegang te controleren op basis van het beleid van uw organisatie. | Azure AD Premium P2 |
| [Dynamisch groepslidmaatschapsbeleid implementeren](../users-groups-roles/groups-dynamic-membership.md) | Gebruik dynamische groepen om gebruikers automatisch aan groepen toe te wijzen op basis van hun kenmerken van HR (of uw bron van waarheid), zoals afdeling, titel, regio en andere kenmerken. |  |
| [Groepsgebaseerde toepassingsinrichting implementeren](../manage-apps/what-is-access-management.md) | Gebruik groepsgebaseerde toegangsbeheerinrichting om gebruikers automatisch in te richten op SaaS-toepassingen. |  |
| [Gebruikersinrichting en deprovisioning automatiseren](../app-provisioning/user-provisioning.md) | Verwijder handmatige stappen uit de levenscyclus van uw werknemersaccount om onbevoegde toegang te voorkomen. Identiteiten synchroniseren van uw bron van waarheid (HR-systeem) naar Azure AD. |  |

## <a name="next-steps"></a>Volgende stappen

[Azure AD-licentie- en prijsgegevens](https://azure.microsoft.com/pricing/details/active-directory/)

[Configuraties voor identiteit en apparaattoegang](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Gemeenschappelijk aanbevolen beleid voor identiteits- en apparaattoegangs](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)
