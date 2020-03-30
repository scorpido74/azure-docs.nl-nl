---
title: Het activiteitenrapport gebruiken om AD FS-apps naar Azure Active Directory te verplaatsen | Microsoft Docs'
description: Met het rapport Ad FS-toepassingsactiviteiten (Active Directory Federation Services) u toepassingen snel migreren van AD FS naar Azure Active Directory (Azure AD). Deze migratietool voor AD FS identificeert compatibiliteit met Azure AD en geeft migratierichtlijnen.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/14/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 333e440fdd5f5062dda45fb12a83543c63e66c04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978037"
---
# <a name="use-the-ad-fs-application-activity-report-preview-to-migrate-applications-to-azure-ad"></a>Het ad FS-toepassingsactiviteitsrapport (voorbeeld) gebruiken om toepassingen te migreren naar Azure AD

Veel organisaties gebruiken Active Directory Federation Services (AD FS) om één aanmelding te bieden voor cloudtoepassingen. Uw AD FS-toepassingen kunnen worden verplaatst naar Azure AD voor verificatie, met name op het gebied van kostenbeheer, risicobeheer, productiviteit, compliance en governance. Maar het kan tijdrovend zijn om te begrijpen welke toepassingen compatibel zijn met Azure AD en specifieke migratiestappen te identificeren.

Met het AD FS-toepassingsactiviteitenrapport (voorbeeld) in de Azure-portal u snel bepalen welke van uw toepassingen kunnen worden gemigreerd naar Azure AD. Het beoordeelt alle AD FS-toepassingen op compatibiliteit met Azure AD, controleert op eventuele problemen en geeft richtlijnen voor het voorbereiden van afzonderlijke toepassingen voor migratie. Met het activiteitenrapport AD FS-toepassingen u het als:

* **Ontdek AD FS-toepassingen en bereik uw migratie.** In het rapport AD FS-toepassingsactiviteit worden alle AD FS-toepassingen in uw organisatie weergegeven en wordt aangegeven dat ze klaar zijn voor migratie naar Azure AD.
* **Geef prioriteit aan toepassingen voor migratie.** Ontvang het aantal unieke gebruikers dat zich in de afgelopen 1, 7 of 30 dagen bij de toepassing heeft aangemeld om de kritiek of het risico van het migreren van de toepassing te bepalen.
* **Voer migratietests uit en los problemen op.** De rapportageservice voert automatisch tests uit om te bepalen of een toepassing klaar is om te migreren. De resultaten worden weergegeven in het ad FS-toepassingsactiviteitsrapport als migratiestatus. Als potentiële migratieproblemen worden geïdentificeerd, krijgt u specifieke richtlijnen voor het aanpakken van de problemen.

De activiteitsgegevens van de AD FS-toepassing zijn beschikbaar voor gebruikers die een van deze beheerdersrollen toegewezen krijgen: globale beheerder, rapportlezer, beveiligingslezer, toepassingsbeheerder of cloudtoepassingsbeheerder.

## <a name="prerequisites"></a>Vereisten

* Uw organisatie moet momenteel AD FS gebruiken om toegang te krijgen tot toepassingen.
* Azure AD Connect-status moet zijn ingeschakeld in uw Azure AD-tenant.
   * [Meer informatie over Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)
   * [Aan de slag met het instellen van Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>Ontdek AD FS-toepassingen die kunnen worden gemigreerd 

Het ad FS-toepassingsactiviteitenrapport is beschikbaar in de Azure-portal onder Azure **AD-gebruik& statistiekenrapportage.** Het activiteitsrapport ad FS-toepassingen analyseert elke AD FS-toepassing om te bepalen of deze kan worden gemigreerd als het is of dat aanvullende beoordeling nodig is. 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een beheerdersrol die toegang heeft tot gegevens over AD FS-toepassingsactiviteit (globale beheerder, rapportlezer, beveiligingslezer, toepassingsbeheerder of beheerder van cloudtoepassingen).

2. Selecteer **Azure Active Directory**en selecteer vervolgens **Enterprise-toepassingen**.

3. Selecteer **onder Activiteit**de optie **Gebruik& Insights (Voorbeeld)** en selecteer **vervolgens AD FS-toepassingsactiviteit** om een lijst met alle AD FS-toepassingen in uw organisatie te openen.

   ![ACTIVITEIT AD FS-toepassingen](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. Bekijk voor elke toepassing in de lijst met AD FS-toepassingsactiviteiten de **migratiestatus:**

   * **Klaar om te migreren** betekent dat de CONFIGURATIE van de AD FS-toepassings volledig wordt ondersteund in Azure AD en kan worden gemigreerd zoals het is.

   * **Als u moet controleren,** kunnen sommige instellingen van de toepassing worden gemigreerd naar Azure AD, maar moet u de instellingen controleren die niet kunnen worden gemigreerd.

   * **Extra stappen vereist** betekent dat Azure AD sommige instellingen van de toepassing niet ondersteunt, zodat de toepassing niet kan worden gemigreerd in de huidige status.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>De gereedheid van een aanvraag voor migratie evalueren 

1. Klik in de lijst met AD FS-toepassingsactiviteiten op de status in de kolom **Migratiestatus** om migratiegegevens te openen. U ziet een overzicht van de configuratietests die zijn geslaagd, samen met eventuele migratieproblemen.

   ![Details van de migratie](media/migrate-adfs-application-activity/migration-details.png)

2. Klik op een bericht om aanvullende details van de migratieregel te openen. Zie de tabel [Ad FS-toepassingsconfiguratietests](#ad-fs-application-configuration-tests) hieronder voor een volledige lijst met de geteste eigenschappen.

   ![Details migratieregel](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>Configuratietests voor AD FS-toepassingen

In de volgende tabel worden alle configuratietests weergegeven die worden uitgevoerd op AD FS-toepassingen.

|Resultaat  |Pass/waarschuwing/mislukt  |Beschrijving  |
|---------|---------|---------|
|Test-ADFSRPAdditionalAuthenticationRules <br> Er is ten minste één niet-migrerende regel gedetecteerd voor AdditionalAuthentication.       | Pas/waarschuwing          | De relying party heeft regels om te vragen voor multi-factor authenticatie (MFA). Als u naar Azure AD wilt gaan, vertaalt u deze regels naar beleid voor voorwaardelijke toegang. Als u een on-premises MFA gebruikt, raden we u aan naar Azure MFA te gaan. [Meer informatie over voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).        |
|Test-ADFSRPAdditionalWSFedEndpoint <br> Relying party heeft AdditionalWSFedEndpoint ingesteld op true.       | Geslaagd/mislukt          | De relying party in AD FS maakt meerdere WS-Fed bewering eindpunten.Momenteel ondersteunt Azure AD er slechts één.Als u een scenario hebt waarin dit resultaat de migratie blokkeert, [laat het ons dan weten.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints)     |
|Test-ADFSRPAllowedAuthenticationClassReferences <br> Relying Party heeft AllowedAuthenticationClassReferences ingesteld.       | Geslaagd/mislukt          | Met deze instelling in AD FS u opgeven of de toepassing is geconfigureerd om alleen bepaalde verificatietypen toe te staan. We raden u aan voorwaardelijke toegang te gebruiken om deze mogelijkheid te bereiken. Als u een scenario hebt waarin dit resultaat de migratie blokkeert, [laat het ons dan weten.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication)  [Meer informatie over voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).          |
|Test-ADFSRPAlwaysRequireAuthentication <br> AlwaysRequireAuthenticationCheckResult      | Geslaagd/mislukt          | Met deze instelling in AD FS u opgeven of de toepassing is geconfigureerd om SSO-cookies te negeren en **Altijd om verificatie te vragen.** In Azure AD u de verificatiesessie beheren met behulp van beleid voor voorwaardelijke toegang om vergelijkbaar gedrag te bereiken. [Meer informatie over het configureren van verificatiesessiebeheer met voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).          |
|Test-ADFSRPAutoUpdateIngeschakeld <br> Relying Party heeft AutoUpdateEnabled ingesteld op true       | Pas/waarschuwing          | Met deze instelling in AD FS u opgeven of AD FS is geconfigureerd om de toepassing automatisch bij te werken op basis van wijzigingen binnen de federatiemetagegevens. Azure AD ondersteunt dit vandaag niet, maar mag de migratie van de toepassing naar Azure AD niet blokkeren.           |
|Naam van Test-ADFSRPClaimsProvider <br> Relying Party heeft meerdere ClaimProviders ingeschakeld       | Geslaagd/mislukt          | Deze instelling in AD FS roept de identiteitsproviders op waarvan de relying party claims accepteert. In Azure AD u externe samenwerking inschakelen met Azure AD B2B. [Meer informatie over Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b).          |
|Regels voor autorisatievan test-ADFSRP-delegatie      | Geslaagd/mislukt          | De toepassing heeft aangepaste delegatieautorisatieregels gedefinieerd. Dit is een WS-Trust-concept dat Azure AD ondersteunt met behulp van moderne verificatieprotocollen, zoals OpenID Connect en OAuth 2.0. [Meer informatie over het Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc).          |
|Regels voor test-ADFSRPImpersonationAuthorization       | Pas/waarschuwing          | De toepassing heeft aangepaste imitatieautorisatieregels gedefinieerd.Dit is een WS-Trust-concept dat Azure AD ondersteunt met behulp van moderne verificatieprotocollen, zoals OpenID Connect en OAuth 2.0. [Meer informatie over het Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc).          |
|Regels voor afgifte van test-ADFSRP-emissies <br> Er is ten minste één niet-migrerende regel gedetecteerd voor UitgifteAutorisatie.       | Pas/waarschuwing          | De toepassing heeft aangepaste uitgifteautorisatieregels die zijn gedefinieerd in AD FS.Azure AD ondersteunt deze functionaliteit met Voorwaardelijke toegang tot Azure AD. [Meer informatie over voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/overview). <br> U ook de toegang tot een toepassing beperken door gebruikers of groepen die aan de toepassing zijn toegewezen. [Meer informatie over het toewijzen van gebruikers en groepen aan toegang tot toepassingen](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups).            |
|Transformatieregels voor test-ADFSRP-uitgiften <br> Er is ten minste één niet-migrerende regel gedetecteerd voor IssuanceTransform.       | Pas/waarschuwing          | De toepassing heeft aangepaste uitgiftetransformatieregels die zijn gedefinieerd in AD FS. Azure AD ondersteunt het aanpassen van de claims die in het token zijn uitgegeven. Zie [Claims aanpassen in het SAML-token voor bedrijfstoepassingen voor](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)meer informatie.           |
|Test-ADFSRPMonitoringIngeschakeld <br> Relying Party heeft MonitoringEnabled ingesteld op true.       | Pas/waarschuwing          | Met deze instelling in AD FS u opgeven of AD FS is geconfigureerd om de toepassing automatisch bij te werken op basis van wijzigingen binnen de federatiemetagegevens. Azure AD ondersteunt dit vandaag niet, maar mag de migratie van de toepassing naar Azure AD niet blokkeren.           |
|Test-ADFSRPNotBeforeSkew <br> NotBeforeSkewCheckResult NotBeforeSkewCheckResult      | Pas/waarschuwing          | AD FS staat een tijdsverschil toe op basis van de notbefore- en notonorna-tijden in het SAML-token. Azure AD verwerkt dit automatisch standaard.          |
|Test-ADFSRPRequestMFAFromClaimsProviders <br> Relying Party heeft RequestMFAFromClaimsProviders ingesteld op true.       | Pas/waarschuwing          | Deze instelling in AD FS bepaalt het gedrag voor MFA wanneer de gebruiker afkomstig is van een andere claimprovider. In Azure AD u externe samenwerking inschakelen met Azure AD B2B. Vervolgens u beleid voor voorwaardelijke toegang toepassen om gasttoegang te beschermen. Meer informatie over [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) en [Voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).          |
|Test-ADFSRPSignedSamlRequestsVereist <br> Relying Party heeft OndertekendSamlRequestsRequired ingesteld op true       | Geslaagd/mislukt          | De toepassing is geconfigureerd in AD FS om de handtekening in het SAML-verzoek te verifiëren. Azure AD accepteert een ondertekende SAML-aanvraag; Het zal de handtekening echter niet verifiëren. Azure AD heeft verschillende methoden om te beschermen tegen kwaadwillende oproepen. Azure AD gebruikt bijvoorbeeld de antwoord-URL's die in de toepassing zijn geconfigureerd om de SAML-aanvraag te valideren. Azure AD stuurt alleen een token om URL's te beantwoorden die voor de toepassing zijn geconfigureerd. Als u een scenario hebt waarin dit resultaat de migratie blokkeert, [laat het ons dan weten.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature)          |
|Levensduur van test-ADFSRPToken <br> TokenLifetimeCheckResultaat        | Pas/waarschuwing         | De toepassing is geconfigureerd voor een aangepaste tokenlevensduur. De AD FS-standaard is een uur.Azure AD ondersteunt deze functionaliteit met voorwaardelijke toegang. Zie [Verificatiesessiebeheer configureren met voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)voor meer informatie.          |
|Relying Party is ingesteld om claims te versleutelen. Dit wordt ondersteund door Azure AD       | Pass          | Met Azure AD u het token versleutelen dat naar de toepassing is verzonden. Zie [Azure AD SAML-tokenversleuteling configureren](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)voor meer informatie.          |
|VersleuteldenaamIdRequiredCheckResult      | Geslaagd/mislukt          | De toepassing is geconfigureerd om de nameID-claim in het SAML-token te versleutelen.Met Azure AD u het volledige token versleutelen dat naar de toepassing is verzonden.Versleuteling van specifieke claims wordt nog niet ondersteund. Zie [Azure AD SAML-tokenversleuteling configureren](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)voor meer informatie.         |

## <a name="check-the-results-of-claim-rule-tests"></a>De resultaten van claimregeltests controleren

Als u een claimregel hebt geconfigureerd voor de toepassing in AD FS, biedt de ervaring een gedetailleerde analyse voor alle claimregels. U ziet welke claimregels kunnen worden verplaatst naar Azure AD en welke regels verder moeten worden gecontroleerd.

1. Klik in de lijst met AD FS-toepassingsactiviteiten op de status in de kolom **Migratiestatus** om migratiegegevens te openen. U ziet een overzicht van de configuratietests die zijn geslaagd, samen met eventuele migratieproblemen.

2. Vouw op de pagina Details van de **migratieregel** de resultaten uit om details over mogelijke migratieproblemen weer te geven en om aanvullende richtlijnen te krijgen. Zie de tabel De resultaten van de tabel [claimregeltests](#check-the-results-of-claim-rule-tests) hieronder voor een gedetailleerde lijst met alle geteste claimregels.

   In het onderstaande voorbeeld worden migratieregeldetails voor de regel Uitgiftetransformatie weergegeven. Het bevat de specifieke delen van de claim die moeten worden gecontroleerd en aangepakt voordat u de toepassing naar Azure AD migreren.

   ![Migratieregel bevat aanvullende richtlijnen](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>Claimregeltests

In de volgende tabel worden alle claimregeltests weergegeven die worden uitgevoerd op AD FS-toepassingen.

|Eigenschap  |Beschrijving  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | De voorwaardeinstructie gebruikt Reguliere expressies om te evalueren of de claim overeenkomt met een bepaald patroon.Om een vergelijkbare functionaliteit in Azure AD te bereiken, u onder andere vooraf gedefinieerde transformatie gebruiken, zoals IfEmpty(), StartWith(), Contains(). Zie [Claims die zijn uitgegeven in het SAML-token voor bedrijfstoepassingen aanpassen voor](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)meer informatie.          |
|UNSUPPORTED_CONDITION_CLASS      | De voorwaardeinstructie heeft meerdere voorwaarden die moeten worden geëvalueerd voordat de uitgifteinstructie wordt uitgevoerd.Azure AD kan deze functionaliteit ondersteunen met de transformatiefuncties van de claim, waar u meerdere claimwaarden evalueren.Zie [Claims die zijn uitgegeven in het SAML-token voor bedrijfstoepassingen aanpassen voor](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)meer informatie.          |
|UNSUPPORTED_RULE_TYPE      | De claimregel kon niet worden herkend. Zie [Claims aanpassen die zijn uitgegeven in het SAML-token voor bedrijfstoepassingen voor](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)meer informatie over het configureren van claims in Azure AD.          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | De voorwaardeinstructie maakt gebruik van een uitgever die niet wordt ondersteund in Azure AD.Op dit moment sourcet Azure AD geen claims van winkels die anders zijn dan Active Directory of Azure AD. Als u hiermee geen toepassingen meer migreren naar Azure AD, [laat het ons dan weten.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)         |
|UNSUPPORTED_CONDITION_FUNCTION      | De voorwaardeinstructie gebruikt een geaggregeerde functie om één claim uit te geven of toe te voegen, ongeacht het aantal overeenkomsten.In Azure AD u het kenmerk van een gebruiker evalueren om te bepalen welke waarde voor de claim moet worden gebruikt met functies zoals IfEmpty(), StartWith(), Contains(), onder andere.Zie [Claims die zijn uitgegeven in het SAML-token voor bedrijfstoepassingen aanpassen voor](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)meer informatie.          |
|RESTRICTED_CLAIM_ISSUED      | De voorwaardeinstructie maakt gebruik van een claim die is beperkt in Azure AD. U mogelijk een beperkte claim indienen, maar u de bron niet wijzigen of een transformatie toepassen. Zie [Claims die worden uitgezonden in tokens voor een specifieke app in Azure AD voor](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)meer informatie.          |
|EXTERNAL_ATTRIBUTE_STORE      | De uitgifte-instructie maakt gebruik van een ander kenmerkarchief dan Active Directory. Op dit moment sourcet Azure AD geen claims van winkels die anders zijn dan Active Directory of Azure AD. Als dit resultaat ervoor leidt dat u geen toepassingen meer migreert naar Azure AD, [laat het ons dan weten.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)          |
|UNSUPPORTED_ISSUANCE_CLASS      | De uitgifte-instructie gebruikt ADD om claims toe te voegen aan de binnenkomende claimset. In Azure AD kan dit worden geconfigureerd als meerdere claimtransformaties.Zie [Claims die zijn uitgegeven in het SAML-token voor bedrijfstoepassingen aanpassen voor](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)meer informatie.         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | De uitgifte-instructie gebruikt Reguliere expressies om de waarde van de uit te geven claim te transformeren.Om vergelijkbare functionaliteit in Azure AD te bereiken, u vooraf gedefinieerde transformatie gebruiken, zoals Extract(), Trim(), ToLower, onder andere. Zie [Claims die zijn uitgegeven in het SAML-token voor bedrijfstoepassingen aanpassen voor](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)meer informatie.          |


## <a name="next-steps"></a>Volgende stappen

- [Video: Het activiteitenrapport van AD FS gebruiken om een toepassing te migreren](https://www.youtube.com/watch?v=OThlTA239lU)
- [Toepassingen beheren met Azure Active Directory](what-is-application-management.md)
- [Toegang tot apps beheren](what-is-access-management.md)
- [Azure AD Connect-federatie](../hybrid/how-to-connect-fed-whatis.md)
