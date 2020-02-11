---
title: Meer informatie over de werking van Azure AD Provisioning | Microsoft Docs
description: Meer informatie over de werking van Azure AD-inrichting
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c3c0aea6ecaccc972702a8c87e4d127c71c75d6
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121362"
---
# <a name="how-provisioning-works"></a>Hoe inrichting werkt

Automatische inrichting heeft betrekking op het maken van gebruikers-id's en-rollen in de Cloud toepassingen waartoe gebruikers toegang nodig hebben. Naast het maken van gebruikers identiteiten, omvat automatische inrichting het onderhoud en de verwijdering van gebruikers identiteiten als status of rollen worden gewijzigd. Voordat u begint met het implementeren van een implementatie, kunt u dit artikel raadplegen voor meer informatie over de werking van Azure AD en over het verkrijgen van configuratie aanbevelingen. 

De **Azure AD-inrichtings service voorziet** gebruikers van SaaS-apps en andere systemen door verbinding te maken met een systeem voor scim (Cross-Domain Identity Management) 2,0 gebruikers beheer API-eind punt van de leverancier van de toepassing. Met dit SCIM-eind punt kan Azure AD programmatisch gebruikers maken, bijwerken en verwijderen. Voor geselecteerde toepassingen kan de inrichtings service ook extra identiteits-gerelateerde objecten, zoals groepen en rollen, maken, bijwerken en verwijderen. Het kanaal dat wordt gebruikt voor het inrichten tussen Azure AD en de toepassing, wordt versleuteld met HTTPS SSL-versleuteling.


![Azure AD Provisioning Service](./media/how-provisioning-works/provisioning0.PNG)
*afbeelding 1: de Azure AD-inrichtings service*

![werk stroom voor uitgaand gebruikers inrichten](./media/how-provisioning-works/provisioning1.PNG)
*afbeelding 2: ' uitgaande ' werk stroom voor gebruikers inrichting van Azure AD naar populaire SaaS-toepassingen*

![werk stroom voor het inrichten van inkomend gebruikers](./media/how-provisioning-works/provisioning2.PNG)
*afbeelding 3: ' Inkomend ' werk stroom voor gebruikers inrichting van populaire HCM-toepassingen (Human Capital Management) tot Azure Active Directory en Windows Server Active Directory*

## <a name="provisioning-using-scim-20"></a>Inrichten met behulp van SCIM 2,0

De Azure AD-inrichtings service maakt gebruik van het [SCIM 2,0-protocol](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards) voor automatische inrichting. De service maakt verbinding met het SCIM-eind punt voor de toepassing en maakt gebruik van SCIM User object schema en REST Api's om het inrichten en het ongedaan maken van de inrichting van gebruikers en groepen te automatiseren. Voor de meeste toepassingen in de Azure AD-galerie wordt een SCIM-inrichtings connector verstrekt. Bij het bouwen van apps voor Azure AD kunnen ontwikkel aars de SCIM 2,0-gebruikers beheer-API gebruiken om een SCIM-eind punt te bouwen dat Azure AD integreert voor inrichting. Zie [een scim-eind punt bouwen en gebruikers inrichten configureren](../app-provisioning/use-scim-to-provision-users-and-groups.md)voor meer informatie.

Als u een automatische Azure AD-inrichtings connector wilt aanvragen voor een app die momenteel niet actief is, vult u een [Azure Active Directory toepassings aanvraag](https://aka.ms/aadapprequest)in.

## <a name="authorization"></a>Autorisatie

Er zijn referenties vereist voor Azure AD om verbinding te maken met de gebruikers beheer-API van de toepassing. Tijdens het configureren van het automatisch inrichten van gebruikers voor een toepassing, moet u geldige referenties invoeren. U kunt referentie typen en vereisten voor de toepassing vinden door te verwijzen naar de zelf studie over apps. In de Azure Portal kunt u de referenties testen door Azure AD te proberen verbinding te maken met de inrichtings-app van de app met behulp van de opgegeven referenties.

Als op SAML gebaseerde eenmalige aanmelding ook voor de toepassing is geconfigureerd, is de interne opslag limiet voor Azure AD per toepassing 1024 bytes. Deze limiet omvat alle certificaten, geheime tokens, referenties en gerelateerde configuratie gegevens die zijn gekoppeld aan één exemplaar van een toepassing (ook wel een Service-Principal record genoemd in azure AD). Wanneer op SAML gebaseerde eenmalige aanmelding is geconfigureerd, verbruikt het certificaat dat wordt gebruikt voor het ondertekenen van de SAML-tokens vaak meer dan 50% van de ruimte. Eventuele aanvullende items (geheime tokens, Uri's, e-mail adressen, gebruikers namen en wacht woorden) die u tijdens het instellen van de gebruikers inrichting invoert, kunnen de opslag limiet overschrijden. Zie voor meer informatie [probleem bij het opslaan van beheerders referenties tijdens het configureren van gebruikers inrichting](../manage-apps/application-provisioning-config-problem-storage-limit.md).

## <a name="mapping-attributes"></a>Kenmerken toewijzen

Wanneer u het inrichten van gebruikers inschakelt voor een SaaS-toepassing van derden, bepaalt de Azure Portal de kenmerk waarden via kenmerk toewijzingen. Toewijzingen bepalen de gebruikers kenmerken die stroomt tussen Azure AD en de doel toepassing wanneer gebruikers accounts worden ingericht of bijgewerkt.

Er is een vooraf geconfigureerde set met kenmerken en kenmerk toewijzingen tussen Azure AD-gebruikers objecten en de gebruikers objecten van de SaaS-app. Sommige apps beheren andere typen objecten samen met gebruikers, zoals groepen.

Bij het instellen van de inrichting is het belang rijk om de kenmerk toewijzingen en werk stromen te controleren en te configureren waarmee wordt gedefinieerd welke gebruikers-(of groeps) eigenschappen stromen van Azure AD naar de toepassing. Bekijk en configureer de overeenkomende eigenschap (**objecten matchen met dit kenmerk**) die worden gebruikt om gebruikers/groepen op unieke wijze te identificeren en te vergelijken tussen de twee systemen.

U kunt de standaard kenmerk toewijzingen aanpassen op basis van de behoeften van uw bedrijf. U kunt dus bestaande kenmerk toewijzingen wijzigen of verwijderen, of nieuwe kenmerk toewijzingen maken. Zie voor meer informatie [Gebruikers inrichten kenmerk toewijzingen voor SaaS-toepassingen aanpassen](../manage-apps/customize-application-attributes.md).

Bij het configureren van inrichting tot een SaaS-toepassing, is een van de typen kenmerktoewijzingen die u kunt opgeven een expressie-toewijzing. Voor deze toewijzingen moet u een script achtige expressie schrijven waarmee u de gegevens van uw gebruikers kunt transformeren naar indelingen die acceptabel zijn voor de SaaS-toepassing. Zie [expressies schrijven voor kenmerk toewijzingen](functions-for-customizing-application-data.md)voor meer informatie.

## <a name="scoping"></a>Bereik 
### <a name="assignment-based-scoping"></a>Op toewijzing gebaseerde Scope

Voor uitgaande levering vanuit Azure AD naar een SaaS-toepassing is afhankelijkheid van [gebruikers-of groeps toewijzingen](../manage-apps/assign-user-or-group-access-portal.md) de meest voorkomende manier om te bepalen welke gebruikers binnen het bereik van de inrichting vallen. Omdat gebruikers toewijzingen ook worden gebruikt voor het inschakelen van eenmalige aanmelding, kan dezelfde methode worden gebruikt voor het beheren van toegang en inrichting. Op toewijzing gebaseerde scopes zijn niet van toepassing op binnenkomende inrichtings scenario's zoals workday en Successfactors.

* **Groepen.** Met een Azure AD Premium-licentie abonnement kunt u groepen gebruiken om toegang te verlenen aan een SaaS-toepassing. Wanneer het inrichtings bereik is ingesteld op **alleen toegewezen gebruikers en groepen synchroniseren**, wordt door de Azure AD-inrichtings service gebruikers ingericht of ongedaan gemaakt op basis van het feit of ze lid zijn van een groep die is toegewezen aan de toepassing. Het groeps object zelf is alleen ingericht als de toepassing groeps objecten ondersteunt.

* **Dynamische groepen.** De Azure AD User Provisioning-Service kan gebruikers in [dynamische groepen](../users-groups-roles/groups-create-rule.md)lezen en inrichten. Houd deze aanvullende opmerkingen en aanbevelingen in acht:

  * Dynamische groepen kunnen invloed hebben op de prestaties van end-to-end-inrichting van Azure AD naar SaaS-toepassingen.

  * Hoe snel een gebruiker in een dynamische groep wordt ingericht of ongedaan gemaakt in een SaaS-toepassing, is afhankelijk van de snelheid waarmee de dynamische groep lidmaatschaps wijzigingen kan evalueren. Zie [verwerkings status controleren voor een lidmaatschaps regel](../users-groups-roles/groups-create-rule.md)voor meer informatie over het controleren van de verwerkings status van een dynamische groep.

  * Wanneer een gebruiker het lidmaatschap van de dynamische groep kwijtraakt, wordt dit beschouwd als een niet-ingerichte gebeurtenis. Houd rekening met dit scenario bij het maken van regels voor dynamische groepen.

* **Geneste groepen.** De Azure AD User Provisioning-Service kan geen gebruikers in geneste groepen lezen of inrichten. De service kan alleen gebruikers lezen en inrichten die onmiddellijk lid zijn van een expliciet toegewezen groep. Deze beperking van ' groeps toewijzingen voor toepassingen ' is ook van invloed op eenmalige aanmelding (Zie [een groep gebruiken voor het beheren van toegang tot SaaS-toepassingen](../users-groups-roles/groups-saasapps.md)). In plaats daarvan moet u de groepen die de gebruikers moeten inrichten, rechtstreeks toewijzen of een ander [bereik](define-conditional-rules-for-provisioning-user-accounts.md) geven.

### <a name="attribute-based-scoping"></a>Op kenmerken gebaseerde Scope 

U kunt bereik filters gebruiken om op kenmerken gebaseerde regels te definiëren die bepalen welke gebruikers worden ingericht voor een toepassing. Deze methode wordt doorgaans gebruikt voor binnenkomende Provisioning van HCM-toepassingen naar Azure AD en Active Directory. Bereik filters worden geconfigureerd als onderdeel van de kenmerk toewijzingen voor elke Azure AD-gebruikers inrichtings connector. Zie voor meer informatie over het configureren van op kenmerken gebaseerde bereik filters op [basis van kenmerken met bereik filters](define-conditional-rules-for-provisioning-user-accounts.md).

### <a name="b2b-guest-users"></a>B2B-gebruikers (gast)

Het is mogelijk de Azure AD User Provisioning Service te gebruiken om B2B-gebruikers (of gast) in te richten in azure AD naar SaaS-toepassingen. Om B2B-gebruikers zich echter aan te melden bij de SaaS-toepassing met behulp van Azure AD, moet voor de SaaS-toepassing de mogelijkheid voor eenmalige aanmelding op basis van SAML op een specifieke manier zijn geconfigureerd. Zie voor meer informatie over het configureren van SaaS-toepassingen voor het ondersteunen van aanmeldingen van B2B-gebruikers [SaaS-apps configureren voor B2B-samen werking](../b2b/configure-saas-apps.md).

Houd er rekening mee dat de userPrincipalName voor een gast gebruiker vaak wordt opgeslagen als "alias # EXT #@domain.com". Wanneer de userPrincipalName is opgenomen in uw kenmerk toewijzingen als bron kenmerk, wordt het #EXT # verwijderd uit de userPrincipalName. Als u wilt dat de #EXT # aanwezig is, vervangt u userPrincipalName door originalUserPrincipalName als bron kenmerk. 

## <a name="provisioning-cycles-initial-and-incremental"></a>Inrichtings cycli: begin en incrementeel

Wanneer Azure AD het bron systeem is, gebruikt de inrichtings service de [differentiële query functie van de Azure AD-Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) om gebruikers en groepen te bewaken. De inrichtings service voert een eerste cyclus uit op het bron systeem en het doel systeem, gevolgd door periodieke incrementele cycli.

### <a name="initial-cycle"></a>Eerste cyclus

Wanneer de inrichtings service wordt gestart, wordt de eerste cyclus:

1. Query's uitvoeren voor alle gebruikers en groepen van het bron systeem en alle kenmerken ophalen die zijn gedefinieerd in de [kenmerk toewijzingen](customize-application-attributes.md).

2. Filter de gebruikers en groepen die zijn geretourneerd met behulp van de geconfigureerde [toewijzingen](../manage-apps/assign-user-or-group-access-portal.md) of [bereik filters op basis van kenmerken](define-conditional-rules-for-provisioning-user-accounts.md).

3. Wanneer een gebruiker is toegewezen of in het bereik van het inrichten is, vraagt de service het doel systeem om een overeenkomende gebruiker met behulp van de opgegeven [overeenkomende kenmerken](customize-application-attributes.md#understanding-attribute-mapping-properties). Voor beeld: als de naam van de userPrincipal in het bron systeem het overeenkomende kenmerk is en toewijst aan de gebruikers naam in het doel systeem, voert de inrichtings service een query uit op het doel systeem voor gebruikers namen die overeenkomen met de userPrincipal naam waarden in het bron systeem.

4. Als er geen overeenkomende gebruiker wordt gevonden in het doel systeem, wordt deze gemaakt met behulp van de kenmerken die worden geretourneerd door het bron systeem. Nadat het gebruikers account is gemaakt, detecteert en de inrichtings service wordt de ID van het doel systeem voor de nieuwe gebruiker gedetecteerd en opgeslagen in de cache. Deze ID wordt gebruikt om alle toekomstige bewerkingen op die gebruiker uit te voeren.

5. Als er een overeenkomende gebruiker wordt gevonden, wordt deze bijgewerkt met behulp van de kenmerken van het bron systeem. Nadat het gebruikers account is gevonden, detecteert de inrichtings service de ID van het doel systeem voor de nieuwe gebruiker en slaat deze in de cache op. Deze ID wordt gebruikt om alle toekomstige bewerkingen op die gebruiker uit te voeren.

6. Als de kenmerk toewijzingen ' verwijzings kenmerken ' bevatten, biedt de service extra updates op het doel systeem om de objecten waarnaar wordt verwezen, te maken en te koppelen. Een gebruiker kan bijvoorbeeld een kenmerk ' Manager ' hebben in het doel systeem, dat is gekoppeld aan een andere gebruiker die is gemaakt in het doel systeem.

7. Een water merk aan het einde van de eerste cyclus persistent maken, waarmee het start punt voor de latere incrementele cycli wordt geleverd.

Sommige toepassingen, zoals ServiceNow, G suite en Box ondersteunen niet alleen het inrichten van gebruikers, maar ook voor het inrichten van groepen en hun leden. Als groeps inrichting is ingeschakeld in de [toewijzingen](customize-application-attributes.md), synchroniseert de inrichtings service de gebruikers en de groepen en worden de groepslid maatschappen vervolgens op een later tijdstip gesynchroniseerd.

### <a name="incremental-cycles"></a>Incrementele cycli

Na de eerste cyclus zullen alle andere cycli:

1. Zoek het bron systeem op voor alle gebruikers en groepen die zijn bijgewerkt sinds het laatste water merk is opgeslagen.

2. Filter de gebruikers en groepen die zijn geretourneerd met behulp van de geconfigureerde [toewijzingen](../manage-apps/assign-user-or-group-access-portal.md) of [bereik filters op basis van kenmerken](define-conditional-rules-for-provisioning-user-accounts.md).

3. Wanneer een gebruiker is toegewezen of in het bereik van het inrichten is, vraagt de service het doel systeem om een overeenkomende gebruiker met behulp van de opgegeven [overeenkomende kenmerken](customize-application-attributes.md#understanding-attribute-mapping-properties).

4. Als er geen overeenkomende gebruiker wordt gevonden in het doel systeem, wordt deze gemaakt met behulp van de kenmerken die worden geretourneerd door het bron systeem. Nadat het gebruikers account is gemaakt, detecteert en de inrichtings service wordt de ID van het doel systeem voor de nieuwe gebruiker gedetecteerd en opgeslagen in de cache. Deze ID wordt gebruikt om alle toekomstige bewerkingen op die gebruiker uit te voeren.

5. Als er een overeenkomende gebruiker wordt gevonden, wordt deze bijgewerkt met behulp van de kenmerken van het bron systeem. Als het een nieuw toegewezen account is dat overeenkomt, detecteert de inrichtings service de ID van het doel systeem voor de nieuwe gebruiker en slaat deze in de cache op. Deze ID wordt gebruikt om alle toekomstige bewerkingen op die gebruiker uit te voeren.

6. Als de kenmerk toewijzingen ' verwijzings kenmerken ' bevatten, biedt de service extra updates op het doel systeem om de objecten waarnaar wordt verwezen, te maken en te koppelen. Een gebruiker kan bijvoorbeeld een kenmerk ' Manager ' hebben in het doel systeem, dat is gekoppeld aan een andere gebruiker die is gemaakt in het doel systeem.

7. Als een gebruiker die eerder in het bereik is ingericht, uit het bereik is verwijderd, inclusief het ongedaan maken van de toewijzing, schakelt de service de gebruiker in het doel systeem uit via een update.

8. Als een gebruiker die voorheen in het bereik van de inrichting stond, is uitgeschakeld of zacht verwijderd in het bron systeem, wordt de gebruiker door de service in het doel systeem uitschakel via een update.

9. Als een gebruiker die voorheen bij het inrichten was, permanent wordt verwijderd in het bron systeem, wordt de gebruiker door de service in het doel systeem verwijderd. In azure AD worden gebruikers permanent verwijderd 30 dagen nadat ze zacht zijn verwijderd.

10. Een nieuw water merk aan het einde van de incrementele cyclus persistent maken, dat het start punt voor de latere incrementele cycli levert.

> [!NOTE]
> U kunt de bewerkingen **maken**, **bijwerken**of **verwijderen** eventueel uitschakelen met behulp van de selectie vakjes voor de acties van het **doel object** in de sectie [toewijzingen](customize-application-attributes.md) . De logica voor het uitschakelen van een gebruiker tijdens een update wordt ook bepaald via een kenmerk toewijzing van een veld zoals ' accountEnabled '.

De inrichtings service blijft een incrementele cyclus van back-to-back uitvoeren, op intervallen die zijn gedefinieerd in de [zelf studie specifiek voor elke toepassing](../saas-apps/tutorial-list.md). Incrementele cycli worden voortgezet totdat een van de volgende gebeurtenissen zich voordoet:

- De service wordt hand matig gestopt met de Azure Portal of met de juiste Graph API opdracht 
- Er wordt een nieuwe eerste cyclus geactiveerd met de optie **wissen en opnieuw starten** in het Azure Portal, of met behulp van de juiste Graph API opdracht. Met deze actie wordt een opgeslagen water merk gewist en worden alle bron objecten opnieuw geëvalueerd.
- Er wordt een nieuwe eerste cyclus geactiveerd als gevolg van een wijziging in kenmerk toewijzingen of filter bereik. Met deze actie wordt ook een opgeslagen water merk gewist en worden alle bron objecten opnieuw geëvalueerd.
- Het inrichtings proces gaat in quarantaine (zie hieronder) als gevolg van een hoge fout frequentie en blijft meer dan vier weken in quarantaine. In dit geval wordt de service automatisch uitgeschakeld.

### <a name="errors-and-retries"></a>Fouten en pogingen

Als een fout in het doel systeem verhindert dat een afzonderlijke gebruiker wordt toegevoegd, bijgewerkt of verwijderd in het doel systeem, wordt de bewerking tijdens de volgende synchronisatie cyclus opnieuw geprobeerd. Als de gebruiker niet meer kan worden uitgevoerd, worden de nieuwe pogingen op een lagere frequentie uitgevoerd en wordt de schaal geleidelijk teruggezet op slechts één poging per dag. Om het probleem op te lossen, moeten beheerders de [inrichtings logboeken](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) controleren om de hoofd oorzaak te bepalen en de juiste actie ondernemen. Veelvoorkomende fouten kunnen het volgende omvatten:

- Gebruikers die geen kenmerk hebben dat is ingevuld in het bron systeem dat vereist is in het doel systeem
- Gebruikers met een kenmerk waarde in het bron systeem waarvoor zich een unieke beperking in het doel systeem bevindt, en dezelfde waarde is aanwezig in een andere gebruikers record

Los deze fouten op door de kenmerk waarden voor de betrokken gebruiker in het bron systeem aan te passen of door de kenmerk toewijzingen zo aan te passen dat er geen conflicten ontstaan.

### <a name="quarantine"></a>Quarantine

Als de meeste of alle aanroepen die op het doel systeem worden uitgevoerd, consistent mislukken vanwege een fout (bijvoorbeeld ongeldige beheerders referenties), wordt de inrichtings taak de status ' quarantaine '. Deze status wordt aangegeven in het [overzichts rapport](../manage-apps/check-status-user-account-provisioning.md) van de inrichting en via e-mail als e-mail meldingen zijn geconfigureerd in de Azure Portal.

In quarantaine wordt de frequentie van incrementele cycli geleidelijk per dag gereduceerd.

De inrichtings taak verlaat quarantaine nadat alle foutieve fouten zijn opgelost en de volgende synchronisatie cyclus wordt gestart. Als de inrichtings taak langer dan vier weken in quarantaine blijft, wordt de inrichtings taak uitgeschakeld. Hier vindt u meer informatie over de [quarantaine status.](../manage-apps/application-provisioning-quarantine-status.md)

### <a name="how-long-provisioning-takes"></a>Hoelang inrichten duurt

De prestaties zijn afhankelijk van of uw inrichtings taak een initiële inrichtings cyclus of een incrementele cyclus uitvoert. Zie [de status van gebruikers inrichten controleren](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)voor meer informatie over hoe lang het inrichten duurt en het controleren van de status van de inrichtings service.

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>Hoe weet ik of gebruikers correct worden ingericht?

Alle bewerkingen die worden uitgevoerd door de User Provisioning Service worden vastgelegd in de Azure AD [-inrichtings Logboeken (preview)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). De logboeken bevatten alle Lees-en schrijf bewerkingen die zijn aangebracht op de bron-en doel systemen en de gebruikers gegevens die tijdens elke bewerking zijn gelezen of geschreven. Voor informatie over het lezen van de inrichtings Logboeken in de Azure Portal, raadpleegt u de [hand leiding](../manage-apps/check-status-user-account-provisioning.md)voor het inrichten van de rapportage.

## <a name="de-provisioning"></a>De inrichting ongedaan maken

De Azure AD-inrichtings service zorgt ervoor dat de bron-en doel systemen synchroon blijven door het inrichtings account te verwijderen wanneer gebruikers niet meer toegang mogen hebben. 

De Azure AD-inrichtings service verwijdert een gebruiker in een toepassing zacht wanneer de suupports wordt verwijderd (update aanvraag met Active = false) en een van de volgende gebeurtenissen zich voordoet:

* Het gebruikers account wordt verwijderd in azure AD
*   De gebruiker is niet toegewezen aan de toepassing
*   De gebruiker voldoet niet meer aan een bereik filter en valt buiten het bereik
    * De Azure AD-inrichtings service laadt standaard gebruikers die buiten het bereik vallen, of schakelt deze uit. Als u dit standaard gedrag wilt overschrijven, kunt u een markering instellen om [verwijderingen buiten het bereik over te slaan](../app-provisioning/skip-out-of-scope-deletions.md).
*   De eigenschap AccountEnabled is ingesteld op False

Als een van de bovenstaande vier gebeurtenissen optreedt en de doel toepassing geen tijdelijke verwijderingen ondersteunt, wordt door de inrichtings service een Verwijder aanvraag verzonden om de gebruiker definitief uit de app te verwijderen. 

30 dagen nadat een gebruiker is verwijderd in azure AD, worden deze permanent verwijderd uit de Tenant. Op dit moment wordt door de inrichtings service een Verwijder aanvraag verzonden om de gebruiker permanent te verwijderen uit de toepassing. Op elk gewenst moment in het 30-daagse venster kunt u [een gebruiker permanent hand matig verwijderen](../fundamentals/active-directory-users-restore.md), waardoor een aanvraag voor verwijderen naar de toepassing wordt verzonden.

Als u een kenmerk IsSoftDeleted in uw kenmerk toewijzingen ziet, wordt dit gebruikt om de status van de gebruiker te bepalen en of een update aanvraag met actief = False moet worden verzonden om de gebruiker te verwijderen. 

## <a name="next-steps"></a>Volgende stappen

[Een automatische implementatie van gebruikers inrichten plannen](../app-provisioning/plan-auto-user-provisioning.md)

[Inrichting configureren voor een galerie-app](../manage-apps/configure-automatic-user-provisioning-portal.md)

[Een SCIM-eind punt bouwen en inrichten configureren bij het maken van uw eigen app](../app-provisioning/use-scim-to-provision-users-and-groups.md)

[Problemen oplossen met het configureren en inrichten van gebruikers voor een toepassing](../manage-apps/application-provisioning-config-problem.md).
