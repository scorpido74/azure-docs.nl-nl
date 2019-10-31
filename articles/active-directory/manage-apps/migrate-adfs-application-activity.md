---
title: Gebruik het activiteiten rapport om AD FS-apps te verplaatsen naar Azure Active Directory | Microsoft Docs '
description: Met het rapport activiteit van de Active Directory Federation Services-toepassing (AD FS) kunt u snel toepassingen migreren van AD FS naar Azure Active Directory (Azure AD). Dit hulp programma voor migratie van AD FS identificeert compatibiliteit met Azure AD en biedt migratie richtlijnen.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/30/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10a267811b7e7ac8715b6823a24c2b3a1f0d430c
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73180535"
---
# <a name="use-the-ad-fs-application-activity-report-preview-to-migrate-applications-to-azure-ad"></a>Het rapport AD FS toepassings activiteit (preview) gebruiken voor het migreren van toepassingen naar Azure AD

Veel organisaties gebruiken Active Directory Federation Services (AD FS) voor het bieden van eenmalige aanmelding bij Cloud toepassingen. Er zijn belang rijke voor delen om uw AD FS-toepassingen naar Azure AD te verplaatsen voor verificatie, met name op het gebied van kosten beheer, risico beheer, productiviteit, naleving en governance. Maar u begrijpt welke toepassingen compatibel zijn met Azure AD en het identificeren van specifieke migratie stappen kan tijdrovend zijn.

Met het rapport AD FS toepassings activiteit (preview) in de Azure Portal kunt u snel zien welke van uw toepassingen kunnen worden gemigreerd naar Azure AD. Het evalueert alle AD FS-toepassingen voor compatibiliteit met Azure AD, controleert op eventuele problemen en biedt hulp bij het voorbereiden van afzonderlijke toepassingen voor migratie. Met het rapport AD FS toepassings activiteit kunt u het volgende doen:

* **Ontdek AD FS toepassingen en bereik uw migratie.** In het rapport AD FS toepassings activiteit wordt een lijst weer gegeven met alle AD FS toepassingen in uw organisatie en wordt de gereedheid voor migratie naar Azure AD aangegeven.
* **Prioriteiten voor de migratie van toepassingen.** Ontvang het aantal unieke gebruikers dat zich heeft aangemeld bij de toepassing in de afgelopen 1, 7 of 30 dagen om te helpen de ernst of het risico van het migreren van de toepassing te bepalen.
* **Voer migratie tests uit en los problemen op.** De rapportage service voert automatisch tests uit om te bepalen of een toepassing gereed is om te migreren. De resultaten worden weer gegeven in het rapport AD FS toepassings activiteit als migratie status. Als er mogelijke problemen met de migratie worden geïdentificeerd, krijgt u specifieke richt lijnen voor het oplossen van de problemen.

De gegevens voor de AD FS toepassings activiteit zijn beschikbaar voor gebruikers aan wie een van deze beheerders rollen is toegewezen: globale beheerder, rapport lezer, beveiligings lezer, toepassings beheerder of Cloud toepassings beheerder.

## <a name="prerequisites"></a>Vereisten

* Uw organisatie moet momenteel AD FS gebruiken om toegang te krijgen tot toepassingen.
* Azure AD Connect Health moet zijn ingeschakeld in uw Azure AD-Tenant.
   * [Meer informatie over Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)
   * [Aan de slag met het instellen van Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>AD FS toepassingen detecteren die kunnen worden gemigreerd 

Het rapport AD FS toepassings activiteit is beschikbaar in de Azure Portal onder Azure AD- **gebruik & Insights** -rapportage. Het rapport AD FS toepassings activiteit analyseert elke AD FS toepassing om te bepalen of deze kan worden gemigreerd, of dat er extra controle nodig is. 

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met een beheerdersrol die toegang heeft tot AD FS toepassings activiteit gegevens (globale beheerder, rapport lezer, beveiligings lezer, toepassings beheerder of Cloud toepassings beheerder).

2. Selecteer **Azure Active Directory**en selecteer vervolgens **bedrijfs toepassingen**.

3. Selecteer onder **activiteit**de optie **gebruik & Insights (preview)** en selecteer vervolgens **AD FS toepassings activiteit** om een lijst met alle AD FS toepassingen in uw organisatie te openen.

   ![Toepassings activiteit AD FS](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. Bekijk voor elke toepassing in de lijst AD FS toepassings activiteit de **migratie status**:

   * Als **u klaar bent om te migreren** , wordt de configuratie van de AD FS-toepassing volledig ondersteund in azure AD en kan deze worden gemigreerd.

   * **Beoordeling van vereisten** betekent dat sommige instellingen van de toepassing kunnen worden gemigreerd naar Azure AD, maar u moet de instellingen die niet kunnen worden gemigreerd, controleren.

   * **Aanvullende stappen vereist** betekent dat Azure AD geen enkele van de instellingen van de toepassing ondersteunt, zodat de toepassing niet kan worden gemigreerd in de huidige status.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>De gereedheid van een toepassing voor migratie evalueren 

1. Klik in de lijst AD FS toepassings activiteit op de status in de kolom **migratie status** om de migratie gegevens te openen. U ziet een samen vatting van de configuratie tests die zijn geslaagd, samen met eventuele mogelijke migratie problemen.

   ![Migratie Details](media/migrate-adfs-application-activity/migration-details.png)

2. Klik op een bericht om meer details over de migratie regel te openen. Zie de tabel met [configuratie tests voor AD FS](#ad-fs-application-configuration-tests) hieronder voor een volledige lijst van de geteste eigenschappen.

   ![Details van de migratie regel](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>Toepassings configuratie tests AD FS

De volgende tabel bevat alle configuratie tests die worden uitgevoerd op AD FS toepassingen.

|Eigenschap  |Status  |Beschrijving  |
|---------|---------|---------|
|Er is ten minste één niet-migreerbaar regel gedetecteerd voor AdditionalAuthentication.       | Pass/Warning          | De Relying Party in AD FS gebruikt een on-premises MFA-provider. Als u wilt overstappen op Azure AD, raden we u aan om over te stappen op de integratie van Azure MFA of aangepaste besturings elementen met een externe MFA-provider. Meer  [informatie over Azure MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).        |
|Relying Party heeft AdditionalWSFedEndpoint ingesteld op waar.       | Geslaagd/mislukt          | Met de Relying Party in AD FS kunt u meerdere eind punten voor de WS-inschakel bevestiging toestaan. Azure AD ondersteunt alleen (1) een van deze vandaag. [Laat het ons weten](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints)als u een scenario hebt waarbij de migratie wordt geblokkeerd.     |
|Relying Party heeft AllowedAuthenticationClassReferences ingesteld.       | Geslaagd/mislukt          | Dit is een instelling in AD FS waarmee u kunt opgeven of de toepassing zo is geconfigureerd dat alleen bepaalde verificatie typen worden toegestaan. Azure AD biedt geen ondersteuning voor dit vandaag. [Laat het ons weten](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication)als u een scenario hebt waarbij de migratie wordt geblokkeerd.          |
|AlwaysRequireAuthentication is ingesteld.      | Geslaagd/mislukt          | Of de toepassing is geconfigureerd voor het negeren van SSO-cookies en ' altijd vragen om verificatie '. Momenteel niet ondersteund door Azure AD.          |
|Relying Party heeft AutoUpdateEnabled ingesteld op waar       | Pass/Warning          | Dit is een instelling in AD FS waarmee u kunt opgeven of AD FS zo is geconfigureerd dat de toepassing automatisch wordt bijgewerkt op basis van wijzigingen in de federatieve meta gegevens. Azure AD ondersteunt dit vandaag nog niet, maar de migratie van de toepassing naar Azure AD moet niet worden geblokkeerd.           |
|Voor Relying Party is meerdere ClaimsProviders ingeschakeld       | Geslaagd/mislukt          | De Relying Party is geconfigureerd voor de bron claims van een andere claim provider dan Active Directory.  Dit wordt niet ondersteund in azure AD. [Laat het ons weten](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)als u een scenario hebt waarbij de migratie wordt geblokkeerd.          |
|DelegationAuthorization is geldig      | Geslaagd/mislukt          | Voor de toepassing zijn aangepaste autorisatie regels voor delegering gedefinieerd. Azure AD biedt geen ondersteuning voor dit vandaag.  [Laat het ons weten](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695747-allow-to-define-delegation-authorization-rules)als u een scenario hebt waarbij de migratie wordt geblokkeerd.          |
|Relying Party heeft ADFSRPImpersonationAuthorizationRules       | Pass/Warning          | Er zijn aangepaste autorisatie regels voor imitatie gedefinieerd voor de toepassing. Azure AD ondersteunt dit vandaag nog niet, maar de migratie van de toepassing naar Azure AD moet niet worden geblokkeerd.          |
|Er is ten minste één niet-migreerbaar regel gedetecteerd voor IssuanceAuthorization.       | Pass/Warning          | Voor de toepassing zijn aangepaste autorisatie regels voor uitgifte in AD FS gedefinieerd. Azure AD biedt ondersteuning voor deze functionaliteit met voorwaardelijke toegang voor Azure AD. Meer [informatie over voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/overview). In azure AD kunt u ook de toegang tot de toepassing beperken door gebruikers of groepen die zijn toegewezen aan de toepassing. Meer [informatie over het toewijzen van gebruikers en groepen voor toegang tot toepassingen](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups). Raadpleeg de onderstaande tabel voor het [testen van claim regels](#check-the-results-of-claim-rule-tests)voor meer informatie over de onbewerkte IssuanceAuthorization-regels die zijn geconfigureerd in AD FS.           |
|Er is ten minste één niet-migreerbaar regel gedetecteerd voor IssuanceTransform.       | Pass/Warning          | Voor de toepassing zijn aangepaste transformatie regels voor uitgifte opgegeven in AD FS. Azure AD biedt ondersteuning voor het aanpassen van de claims die zijn uitgegeven in het token. Zie claims aanpassen die zijn [uitgegeven in het SAML-token voor zakelijke toepassingen voor](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)meer informatie.  Raadpleeg de onderstaande tabel voor het [testen van claim regels](#check-the-results-of-claim-rule-tests)voor meer informatie over de onbewerkte IssuanceTransformation-regels die zijn geconfigureerd in AD FS.          |
|Relying Party heeft MonitoringEnabled ingesteld op waar.       | Pass/Warning          | Dit is een instelling in AD FS waarmee u kunt opgeven of AD FS is geconfigureerd voor het bewaken van een federatieve meta gegevens voor deze toepassing. Dit wordt niet ondersteund in azure AD, maar de migratie van de toepassing naar Azure AD moet niet worden geblokkeerd.          |
|Relying Party heeft NotBeforeSkew ingesteld.      | Pass/Warning          | AD FS maakt een tijd verschil mogelijk op basis van de NotBefore-en NotOnOrAfter-tijden in het SAML-token. Azure AD ondersteunt dit vandaag nog niet, maar de migratie van de toepassing naar Azure AD moet niet worden geblokkeerd.          |
|Relying Party heeft RequestMFAFromClaimsProviders ingesteld op waar.       | Pass/Warning          | Dit is een instelling in AD FS waarmee u kunt opgeven of de toepassing is vastgelegd voor een andere claim provider en MFA vereist. Als u wilt overstappen op Azure AD, raden we u aan om over te stappen op de integratie van Azure MFA of aangepaste besturings elementen met een MFA-provider van derden. Meer  [informatie over Azure MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).          |
|Relying Party heeft SignedSamlRequestsRequired ingesteld op waar       | Geslaagd/mislukt          | De toepassing is geconfigureerd in AD FS om de hand tekening in de SAML-aanvraag te verifiëren. Dit is een optionele instelling die de migratie niet kan blok keren. Azure Active Directory gebruikt de antwoord-Url's om de service provider te valideren.  [Laat het ons weten](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature)als u een scenario hebt waarbij de migratie wordt geblokkeerd.          |
|TokenLifetimeCheckResult        | Geslaagd/mislukt         | De toepassing is geconfigureerd voor een aangepaste levens duur van tokens. De standaard waarde is AD FS 1 uur. Azure AD ondersteunt deze functionaliteit met behulp van voorwaardelijke toegang. Zie [verificatie sessie beheer met voorwaardelijke toegang configureren](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)voor meer informatie.          |
|Relying Party is ingesteld om claims te versleutelen. Dit wordt ondersteund door Azure AD       | Door          | Met Azure AD kunt u het token verzenden naar de toepassing versleutelen. Zie [Azure AD SAML-token versleuteling configureren](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)voor meer informatie.          |
|Relying Party is ingesteld voor het versleutelen van NameID in het SAML-token.      | Geslaagd/mislukt          | De toepassing is geconfigureerd voor het versleutelen van de NameID-claim in het SAML-token. Met Azure AD kunt u het volledige token versleutelen dat naar de toepassing wordt verzonden. Versleuteling van specifieke claims wordt nog niet ondersteund. Zie [Azure AD SAML-token versleuteling configureren](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)voor meer informatie.         |

## <a name="check-the-results-of-claim-rule-tests"></a>De resultaten van claim regel tests controleren

Als u een claim regel hebt geconfigureerd voor de toepassing in AD FS, biedt de ervaring een gedetailleerde analyse voor alle claim regels. U ziet welke claim regels kunnen worden verplaatst naar Azure AD en die verder moeten worden geëvalueerd.

1. Klik in de lijst AD FS toepassings activiteit op de status in de kolom **migratie status** om de migratie gegevens te openen. U ziet een samen vatting van de configuratie tests die zijn geslaagd, samen met eventuele mogelijke migratie problemen.

2. Vouw op de pagina **Details van migratie regel** de resultaten uit om details over mogelijke migratie problemen weer te geven en meer richt lijnen te krijgen. Voor een gedetailleerde lijst met alle geteste claim regels raadpleegt u de tabel met [de resultaten van claim regel tests controleren](#check-the-results-of-claim-rule-tests) hieronder.

   In het onderstaande voor beeld ziet u de details van de migratie regel voor de regel IssuanceTransform. De lijst bevat de specifieke onderdelen van de claim die moeten worden beoordeeld en geadresseerd voordat u de toepassing kunt migreren naar Azure AD.

   ![Meer informatie over de migratie regel](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>Claim regel testen

De volgende tabel bevat alle claim regel tests die worden uitgevoerd op AD FS toepassingen.

|Eigenschap  |Beschrijving  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | De instructie voor waarde gebruikt reguliere expressies om te evalueren of de claim overeenkomt met een bepaald patroon.  Voor een vergelijk bare functionaliteit in azure AD kunt u een vooraf gedefinieerde trans formatie gebruiken, zoals IfEmpty (), StartWith (), contains (), onder andere. Zie [claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)voor meer informatie.          |
|UNSUPPORTED_CONDITION_CLASS      | De voor waarde-instructie heeft meerdere voor waarden die moeten worden geëvalueerd voordat de uitgifte-instructie kan worden uitgevoerd. Azure AD kan deze functionaliteit ondersteunen met de transformatie functies van de claim waarbij u meerdere claim waarden kunt evalueren.  Zie [claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)voor meer informatie.          |
|UNSUPPORTED_RULE_TYPE      | De claim regel kan niet worden herkend. Zie [claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)voor meer informatie over het configureren van claims in azure AD.          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | De instructie condition maakt gebruik van een uitgever die niet wordt ondersteund in azure AD. Op dit moment kunnen Azure AD geen claims van een andere Active Directory of Azure AD in de bron opslaan. Als dit verhindert u het migreren van toepassingen naar Azure AD, [laat het ons dan weten](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).         |
|UNSUPPORTED_CONDITION_FUNCTION      | De instructie voor waarde gebruikt een statistische functie om een enkele claim te verlenen of toe te voegen, ongeacht het aantal overeenkomsten.  In azure AD kunt u het kenmerk van een gebruiker evalueren om te bepalen welke waarde moet worden gebruikt voor de claim met functies zoals IfEmpty (), StartWith (), contains (), onder andere. Zie [claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)voor meer informatie.          |
|RESTRICTED_CLAIM_ISSUED      | De instructie condition maakt gebruik van een claim die is beperkt in azure AD. U kunt mogelijk een beperkte claim uitgeven, maar u kunt de bron niet wijzigen of een trans formatie Toep assen. Zie voor meer informatie [claims aanpassen die zijn verzonden in tokens voor een specifieke app in azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).          |
|EXTERNAL_ATTRIBUTE_STORE      | De uitgifte-instructie gebruikt een andere kenmerk opslag dan Active Directory. Op dit moment kunnen Azure AD geen claims van een andere Active Directory of Azure AD in de bron opslaan. Als dit verhindert u het migreren van toepassingen naar Azure AD, [laat het ons dan weten](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).          |
|UNSUPPORTED_ISSUANCE_CLASS      | De uitgifte-instructie gebruikt ADD om claims toe te voegen aan de set binnenkomende claims. In azure AD kan dit worden geconfigureerd als meerdere claim transformaties.  Zie [claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)voor meer informatie.         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | De uitgifte-instructie gebruikt reguliere expressies voor het transformeren van de waarde van de claim die moet worden verzonden. Voor een vergelijk bare functionaliteit in azure AD kunt u vooraf gedefinieerde trans formatie, zoals extractie (), Trim (), ToLower, onder andere gebruiken. Zie [claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)voor meer informatie.          |


## <a name="next-steps"></a>Volgende stappen

- [Toepassingen beheren met Azure Active Directory](what-is-application-management.md)
- [Toegang tot apps beheren](what-is-access-management.md)
- [Azure AD Connect-federatie](../hybrid/how-to-connect-fed-whatis.md)
