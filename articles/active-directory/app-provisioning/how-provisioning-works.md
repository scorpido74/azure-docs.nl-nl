---
title: Begrijpen hoe Azure AD-inrichting werkt | Microsoft Documenten
description: Begrijpen hoe Azure AD-inrichting werkt
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 555fb39836054be05102f4c28167d72016805639
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481497"
---
# <a name="how-provisioning-works"></a>Hoe inrichting werkt

Automatische inrichting verwijst naar het maken van gebruikersidentiteiten en rollen in de cloudtoepassingen waartoe gebruikers toegang nodig hebben. Naast het maken van gebruikersidentiteiten, omvat automatische inrichting het onderhoud en verwijderen van gebruikersidentiteiten als status of rollen veranderen. Voordat u een implementatie start, u dit artikel bekijken om te leren hoe Azure AD-voorziening werkt en configuratieaanbevelingen te krijgen. 

De **Azure AD Provisioning Service** voorziet gebruikers van SaaS-apps en andere systemen door verbinding te maken met een Systeem voor Cross-Domain Identity Management (SCIM) 2.0-api-eindpunt voor gebruikersbeheer dat wordt geleverd door de leverancier van de toepassing. Met dit SCIM-eindpunt kan Azure AD gebruikers programmatisch maken, bijwerken en verwijderen. Voor geselecteerde toepassingen kan de inrichtingsservice ook extra identiteitsgerelateerde objecten maken, bijwerken en verwijderen, zoals groepen en rollen. Het kanaal dat wordt gebruikt voor het inrichten tussen Azure AD en de toepassing, wordt versleuteld met HTTPS TLS-versleuteling.


![Azure](./media/how-provisioning-works/provisioning0.PNG)
AD-servicevoorzieningsservice*figuur 1: de Azure AD-inrichtingsservice*

![Uitgaande](./media/how-provisioning-works/provisioning1.PNG)
gebruikersinrichtingswerkstroom*Figuur 2: 'Uitgaande' gebruikersinrichtingswerkstroom van Azure AD naar populaire SaaS-toepassingen*

![Binnenkomende](./media/how-provisioning-works/provisioning2.PNG)
gebruikersinrichtingswerkstroom*Figuur 3: 'Binnenkomende' gebruikersinrichtingsworkflow van populaire HCM-toepassingen (Human Capital Management) naar Azure Active Directory en Active Directory van Windows Server*

## <a name="provisioning-using-scim-20"></a>Inrichting met SCIM 2.0

De Azure AD-inrichtingsservice gebruikt het [SCIM 2.0-protocol](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards) voor automatische inrichting. De service maakt verbinding met het SCIM-eindpunt voor de toepassing en maakt gebruik van SCIM-gebruikersobjectschema en REST API's om de inrichting en devoorziening van gebruikers en groepen te automatiseren. Voor de meeste toepassingen in de Azure AD-galerie is een scim-gebaseerde inrichtingsconnector beschikbaar. Bij het bouwen van apps voor Azure AD kunnen ontwikkelaars de SCIM 2.0-gebruikersbeheer-API gebruiken om een SCIM-eindpunt te bouwen dat Azure AD integreert voor inrichting. Zie Een [SCIM-eindpunt bouwen en gebruikersinrichting configureren](../app-provisioning/use-scim-to-provision-users-and-groups.md)voor meer informatie.

Als u een automatische Azure AD-inrichtingsconnector wilt aanvragen voor een app die er momenteel geen heeft, vult u een [Azure Active Directory-toepassingsaanvraag](https://aka.ms/aadapprequest)in.

## <a name="authorization"></a>Autorisatie

Referenties zijn vereist voor Azure AD om verbinding te maken met de gebruikersbeheer-API van de toepassing. Terwijl u automatische gebruikersinrichting voor een toepassing configureert, moet u geldige referenties invoeren. U referentietypen en vereisten voor de toepassing vinden door te verwijzen naar de zelfstudie van de app. In de Azure-portal u de referenties testen door Azure AD te laten proberen verbinding te maken met de inrichtingsapp van de app met behulp van de meegeleverde referenties.

Als saml-gebaseerde enkele aanmelding ook is geconfigureerd voor de toepassing, is de interne opslaglimiet per toepassing per toepassing 1024 bytes. Deze limiet omvat alle certificaten, geheime tokens, referenties en gerelateerde configuratiegegevens die zijn gekoppeld aan één exemplaar van een toepassing (ook wel serviceprincipalrecord in Azure AD genoemd). Wanneer SAML-gebaseerde enkele aanmelding is geconfigureerd, verbruikt het certificaat dat wordt gebruikt om de SAML-tokens te ondertekenen vaak meer dan 50% procent van de ruimte. Eventuele extra items (geheime tokens, URI's, e-mailadressen van meldingen, gebruikersnamen en wachtwoorden) die u invoert tijdens de installatie van de gebruikersinrichting, kunnen de opslaglimiet overschrijden. Zie [Beheerdersreferenties probleem opslaan tijdens het configureren van gebruikersinrichting](../manage-apps/application-provisioning-config-problem-storage-limit.md)voor meer informatie.

## <a name="mapping-attributes"></a>Toewijzingskenmerken

Wanneer u gebruikersvoorzieningen inschakelt voor een SaaS-toepassing van derden, bepaalt de Azure-portal de kenmerkenswaarden via kenmerktoewijzingen. Toewijzingen bepalen de gebruikerskenmerken die tussen Azure AD en de doeltoepassing stromen wanneer gebruikersaccounts worden ingericht of bijgewerkt.

Er is een vooraf geconfigureerde set kenmerken en kenmerktoewijzingen tussen Azure AD-gebruikersobjecten en de gebruikersobjecten van elke SaaS-app. Sommige apps beheren andere typen objecten samen met gebruikers, zoals Groepen.

Bij het instellen van inrichting is het belangrijk om de kenmerktoewijzingen en -werkstromen te controleren en te configureren die bepalen welke gebruikerseigenschappen (of groep) van Azure AD naar de toepassing stromen. De overeenkomende eigenschap **(Objecten overeenkomen met dit kenmerk)** controleren en configureren die wordt gebruikt om gebruikers/groepen tussen de twee systemen op unieke wijze te identificeren en matchen.

U de standaardtoewijzingen voor kenmerken aanpassen aan uw bedrijfsbehoeften. U dus bestaande kenmerktoewijzingen wijzigen of verwijderen of nieuwe kenmerktoewijzingen maken. Zie Voor meer informatie [het aanpassen van kenmerktoewijzingen voor saas-toepassingen aanpassen.](../manage-apps/customize-application-attributes.md)

Wanneer u de inrichting configureert voor een SaaS-toepassing, is een van de typen kenmerktoewijzingen die u opgeven een expressietoewijzing. Voor deze toewijzingen moet u een scriptachtige expressie schrijven waarmee u de gegevens van uw gebruikers omzetten in indelingen die acceptabeler zijn voor de SaaS-toepassing. Zie [Expressies schrijven voor kenmerktoewijzingen voor](functions-for-customizing-application-data.md)meer informatie.

## <a name="scoping"></a>Scoping 
### <a name="assignment-based-scoping"></a>Op opdracht gebaseerde scoping

Voor uitgaande provisioning van Azure AD naar een SaaS-toepassing is vertrouwen op [gebruikers- of groepstoewijzingen](../manage-apps/assign-user-or-group-access-portal.md) de meest voorkomende manier om te bepalen welke gebruikers in het bereik van inrichting zijn. Omdat gebruikerstoewijzingen ook worden gebruikt voor het inschakelen van één aanmelding, kan dezelfde methode worden gebruikt voor het beheren van zowel toegang als inrichting. Op opdrachten gebaseerde scoping is niet van toepassing op binnenkomende provisioningscenario's zoals Workday en Successfactors.

* **Groepen.** Met een Azure AD Premium-licentieplan u groepen gebruiken om toegang toe te wijzen aan een SaaS-toepassing. Wanneer het inrichtingsbereik is ingesteld op **Alleen toegewezen gebruikers en groepen synchroniseren,** wordt de Azure AD-inrichtingsservice vervolgens gebruikers voorzien of de-provisioneren op basis van de vraag of ze lid zijn van een groep die aan de toepassing is toegewezen. Het groepsobject zelf is niet ingericht, tenzij de toepassing groepsobjecten ondersteunt.

* **Dynamische groepen.** De Azure AD-service voor het inrichten van gebruikers kan gebruikers in [dynamische groepen](../users-groups-roles/groups-create-rule.md)lezen en inrichten. Houd deze kanttekeningen en aanbevelingen in gedachten:

  * Dynamische groepen kunnen van invloed zijn op de prestaties van end-to-end provisioning van Azure AD naar SaaS-toepassingen.

  * Hoe snel een gebruiker in een dynamische groep is ingericht of gede-provisioned in een SaaS-toepassing, hangt af van hoe snel de dynamische groep lidmaatschapswijzigingen kan evalueren. Zie [Verwerkingsstatus controleren voor een lidmaatschapsregel voor](../users-groups-roles/groups-create-rule.md)informatie over het controleren van de verwerkingsstatus van een dynamische groep.

  * Wanneer een gebruiker het lidmaatschap van de dynamische groep verliest, wordt dit beschouwd als een de-provisioning-gebeurtenis. Houd rekening met dit scenario bij het maken van regels voor dynamische groepen.

* **Geneste groepen.** De Azure AD-service voor het inrichten van gebruikers kan gebruikers niet lezen of inrichten in geneste groepen. De service kan alleen gebruikers lezen en inrichten die direct lid zijn van een expliciet toegewezen groep. Deze beperking van "groepsgebaseerde toewijzingen aan toepassingen" heeft ook gevolgen voor eenmalige aanmelding (zie [Een groep gebruiken om de toegang tot SaaS-toepassingen te beheren).](../users-groups-roles/groups-saasapps.md) Wijs in plaats daarvan rechtstreeks bereik toe of [anderszins in](define-conditional-rules-for-provisioning-user-accounts.md) de groepen die de gebruikers bevatten die moeten worden ingericht.

### <a name="attribute-based-scoping"></a>Scoping op basis van kenmerken 

U scopingfilters gebruiken om op kenmerken gebaseerde regels te definiëren die bepalen welke gebruikers zijn ingericht voor een toepassing. Deze methode wordt vaak gebruikt voor inkomende inrichting van HCM-toepassingen naar Azure AD en Active Directory. Scopingfilters zijn geconfigureerd als onderdeel van de kenmerktoewijzingen voor elke Azure AD-gebruikersinrichtingsconnector. Zie [Op kenmerken gebaseerde toepassingsinrichting](define-conditional-rules-for-provisioning-user-accounts.md)op basis van kenmerken voor meer informatie over het configureren van op kenmerken gebaseerde scopingfilters.

### <a name="b2b-guest-users"></a>B2B (gast)gebruikers

Het is mogelijk om de Azure AD-gebruikersinrichtingsservice te gebruiken voor het inrichten van B2B-gebruikers (of gastgebruikers) in Azure AD naar SaaS-toepassingen. Als B2B-gebruikers zich echter aanmelden bij de SaaS-toepassing met Azure AD, moet de SaaS-toepassing zijn SAML-gebaseerde single sign-on-mogelijkheid op een specifieke manier hebben geconfigureerd. Zie [SaaS-apps configureren voor B2B-samenwerking voor](../b2b/configure-saas-apps.md)meer informatie over het configureren van SaaS-toepassingen om aanmeldingen van B2B-gebruikers te ondersteunen.

Houd er rekening mee dat de userPrincipalName voor een@domain.comgastgebruiker vaak wordt opgeslagen als 'alias#EXT# '. wanneer de userPrincipalName is opgenomen in uw kenmerktoewijzingen als bronkenmerk, wordt de #EXT# verwijderd van de userPrincipalName. Als u wilt dat de #EXT# aanwezig is, vervangt u userPrincipalName door originalUserPrincipalName als bronkenmerk. 

## <a name="provisioning-cycles-initial-and-incremental"></a>Inrichtingscycli: Initiële en incrementele

Wanneer Azure AD het bronsysteem is, gebruikt de inrichtingsservice de [deltaquery gebruiken om wijzigingen in Microsoft Graph-gegevens bij te houden](https://docs.microsoft.com/graph/delta-query-overview) om gebruikers en groepen te controleren. De inrichtingsservice voert een eerste cyclus uit ten opzichte van het bronsysteem en het doelsysteem, gevolgd door periodieke incrementele cycli.

### <a name="initial-cycle"></a>Initiële cyclus

Wanneer de inrichtingsservice wordt gestart, zal de eerste cyclus:

1. Vraag alle gebruikers en groepen uit het bronsysteem op en haal alle kenmerken op die zijn gedefinieerd in de [kenmerktoewijzingen.](customize-application-attributes.md)

2. Filter de geretourneerde gebruikers en groepen met behulp van geconfigureerde [toewijzingen](../manage-apps/assign-user-or-group-access-portal.md) of [op kenmerken gebaseerde scopingfilters](define-conditional-rules-for-provisioning-user-accounts.md).

3. Wanneer een gebruiker is toegewezen of in het bereik van de inrichting is, vraagt de service het doelsysteem voor een overeenkomende gebruiker op met behulp van de opgegeven [overeenkomende kenmerken.](customize-application-attributes.md#understanding-attribute-mapping-properties) Voorbeeld: Als de naam userPrincipal in het bronsysteem het overeenkomende kenmerk en de kaarten aan userName in het doelsysteem is, stelt de inrichtingsservice het doelsysteem op voor gebruikersNamen die overeenkomen met de naamwaarden van userPrincipal in het bronsysteem.

4. Als een overeenkomende gebruiker niet wordt gevonden in het doelsysteem, wordt deze gemaakt met behulp van de kenmerken die zijn geretourneerd uit het bronsysteem. Nadat het gebruikersaccount is gemaakt, detecteert en cachet de inrichtingsservice de id van het doelsysteem voor de nieuwe gebruiker. Deze id wordt gebruikt om alle toekomstige bewerkingen op die gebruiker uit te voeren.

5. Als een overeenkomende gebruiker wordt gevonden, wordt deze bijgewerkt met behulp van de kenmerken die door het bronsysteem worden geleverd. Nadat het gebruikersaccount is gekoppeld, detecteert en cachet de inrichtingsservice de id van het doelsysteem voor de nieuwe gebruiker. Deze id wordt gebruikt om alle toekomstige bewerkingen op die gebruiker uit te voeren.

6. Als de kenmerktoewijzingen 'referentiekenmerken' bevatten, werkt de service extra updates uit op het doelsysteem om de objecten waarnaar wordt verwezen te maken en te koppelen. Een gebruiker kan bijvoorbeeld een kenmerk 'Manager' in het doelsysteem hebben, dat is gekoppeld aan een andere gebruiker die in het doelsysteem is gemaakt.

7. Ga door tot een watermerk aan het einde van de eerste cyclus, wat het uitgangspunt is voor de latere incrementele cycli.

Sommige toepassingen zoals ServiceNow, G Suite en Box ondersteunen niet alleen het inrichten van gebruikers, maar ook het inrichten van groepen en hun leden. In die gevallen, als groepsinrichting is ingeschakeld in de [toewijzingen,](customize-application-attributes.md)synchroniseert de inrichtingsservice de gebruikers en de groepen en synchroniseert de groeplidmaatschappen later.

### <a name="incremental-cycles"></a>Incrementele cycli

Na de eerste cyclus zullen alle andere cycli:

1. Vraag het bronsysteem op voor alle gebruikers en groepen die zijn bijgewerkt sinds het laatste watermerk is opgeslagen.

2. Filter de geretourneerde gebruikers en groepen met behulp van geconfigureerde [toewijzingen](../manage-apps/assign-user-or-group-access-portal.md) of [op kenmerken gebaseerde scopingfilters](define-conditional-rules-for-provisioning-user-accounts.md).

3. Wanneer een gebruiker is toegewezen of in het bereik van de inrichting is, vraagt de service het doelsysteem voor een overeenkomende gebruiker op met behulp van de opgegeven [overeenkomende kenmerken.](customize-application-attributes.md#understanding-attribute-mapping-properties)

4. Als een overeenkomende gebruiker niet wordt gevonden in het doelsysteem, wordt deze gemaakt met behulp van de kenmerken die zijn geretourneerd uit het bronsysteem. Nadat het gebruikersaccount is gemaakt, detecteert en cachet de inrichtingsservice de id van het doelsysteem voor de nieuwe gebruiker. Deze id wordt gebruikt om alle toekomstige bewerkingen op die gebruiker uit te voeren.

5. Als een overeenkomende gebruiker wordt gevonden, wordt deze bijgewerkt met behulp van de kenmerken die door het bronsysteem worden geleverd. Als het een nieuw toegewezen account is dat is gekoppeld, detecteert en cachet de inrichtingsservice de id van het doelsysteem voor de nieuwe gebruiker. Deze id wordt gebruikt om alle toekomstige bewerkingen op die gebruiker uit te voeren.

6. Als de kenmerktoewijzingen 'referentiekenmerken' bevatten, werkt de service extra updates uit op het doelsysteem om de objecten waarnaar wordt verwezen te maken en te koppelen. Een gebruiker kan bijvoorbeeld een kenmerk 'Manager' in het doelsysteem hebben, dat is gekoppeld aan een andere gebruiker die in het doelsysteem is gemaakt.

7. Als een gebruiker die voorheen in het bereik van de inrichting stond, uit het bereik wordt verwijderd, inclusief niet-toegewezen, schakelt de service de gebruiker in het doelsysteem uit via een update.

8. Als een gebruiker die voorheen in het bereik van de inrichting was uitgeschakeld of soft-verwijderd in het bronsysteem, schakelt de service de gebruiker in het doelsysteem uit via een update.

9. Als een gebruiker die voorheen in het bereik van de inrichting was, hard wordt verwijderd in het bronsysteem, verwijdert de service de gebruiker in het doelsysteem. In Azure AD worden gebruikers 30 dagen nadat ze zacht zijn verwijderd, verwijderd.

10. Ga door tot een nieuw watermerk aan het einde van de incrementele cyclus, die het uitgangspunt vormt voor de latere incrementele cycli.

> [!NOTE]
> U de **bewerkingen Maken,** **Bijwerken**of **Verwijderen** optioneel uitschakelen met de selectievakjes **Doelobjectacties** in de sectie [Toewijzingen.](customize-application-attributes.md) De logica om een gebruiker uit te schakelen tijdens een update wordt ook beheerd via een kenmerktoewijzing vanuit een veld zoals 'accountEnabled'.

De inrichtingsservice blijft back-to-back incrementele cycli voor onbepaalde tijd uitvoeren, met intervallen die zijn gedefinieerd in de [zelfstudie die specifiek is voor elke toepassing](../saas-apps/tutorial-list.md). Incrementele cycli gaan door totdat een van de volgende gebeurtenissen optreedt:

- De service wordt handmatig gestopt met het gebruik van de Azure-portal of met de juiste Microsoft Graph API-opdracht.
- Een nieuwe eerste cyclus wordt geactiveerd met de optie **Status wissen en opnieuw starten** in de Azure-portal of met de juiste Microsoft Graph API-opdracht. Met deze actie wordt elk opgeslagen watermerk gewist en worden alle bronobjecten opnieuw geëvalueerd.
- Een nieuwe eerste cyclus wordt geactiveerd vanwege een wijziging in kenmerktoewijzingen of scopingfilters. Met deze actie wordt ook elk opgeslagen watermerk gewist en worden alle bronobjecten opnieuw geëvalueerd.
- Het inrichtingsproces gaat in quarantaine (zie hieronder) vanwege een hoog foutenpercentage en blijft langer dan vier weken in quarantaine. In dit geval wordt de service automatisch uitgeschakeld.

### <a name="errors-and-retries"></a>Fouten en nieuwe pogingen

Als een fout in het doelsysteem voorkomt dat een individuele gebruiker wordt toegevoegd, bijgewerkt of verwijderd in het doelsysteem, wordt de bewerking opnieuw geprobeerd in de volgende synchronisatiecyclus. Als de gebruiker blijft falen, beginnen de nieuwe pogingen op een verminderde frequentie te gebeuren en worden ze geleidelijk teruggeschroefd naar slechts één poging per dag. Om de fout op te lossen, moeten beheerders de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) controleren om de hoofdoorzaak te bepalen en de juiste actie te ondernemen. Veelvoorkomende fouten kunnen zijn:

- Gebruikers die geen kenmerk hebben dat is ingevuld in het bronsysteem dat vereist is in het doelsysteem
- Gebruikers met een kenmerkwaarde in het bronsysteem waarvoor een unieke beperking in het doelsysteem is en dezelfde waarde aanwezig is in een andere gebruikersrecord

Los deze fouten op door de kenmerkwaarden voor de betrokken gebruiker in het bronsysteem aan te passen of door de kenmerktoewijzingen aan te passen zodat ze geen conflicten veroorzaken.

### <a name="quarantine"></a>Quarantaine

Als de meeste of alle oproepen die tegen het doelsysteem worden uitgevoerd, consistent mislukken vanwege een fout (bijvoorbeeld ongeldige beheerdersreferenties), gaat de inrichtingstaak in een quarantainestatus. Deze status wordt aangegeven in [het rapportagerapport](../manage-apps/check-status-user-account-provisioning.md) en via e-mail als e-mailmeldingen zijn geconfigureerd in de Azure-portal.

Wanneer in quarantaine, de frequentie van incrementele cycli wordt geleidelijk teruggebracht tot eenmaal per dag.

De inrichtingstaak verlaat quarantaine nadat alle aanstootgevende fouten zijn opgelost en de volgende synchronisatiecyclus wordt gestart. Als de inrichtingstaak langer dan vier weken in quarantaine blijft, is de inrichtingstaak uitgeschakeld. Lees hier meer over de [quarantainestatus.](../manage-apps/application-provisioning-quarantine-status.md)

### <a name="how-long-provisioning-takes"></a>Hoelang inrichten duurt

De prestaties zijn afhankelijk van de vraag of uw inrichtingstaak een eerste inrichtingscyclus of een incrementele cyclus uitvoert. Zie [De status van de invoorzieningen van](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)de gebruiker controleren voor meer informatie over hoe de inrichtingsservice duurt en hoe u de status van de inrichting van de inrichting controleren.

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>Hoe weet u of gebruikers goed worden ingericht

Alle bewerkingen die door de gebruikersinrichtingsservice worden uitgevoerd, worden geregistreerd in de Azure AD [Provisioning-logboeken (voorbeeld).](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) De logboeken bevatten alle lees- en schrijfbewerkingen die zijn uitgevoerd naar de bron- en doelsystemen en de gebruikersgegevens die tijdens elke bewerking zijn gelezen of geschreven. Zie de [rapportagehandleiding voor het inrichten](../manage-apps/check-status-user-account-provisioning.md)van de inloglogboeken in de Azure-portal voor informatie over het lezen van de inrichtingslogboeken in de Azure-portal.

## <a name="de-provisioning"></a>De-provisioning

De Azure AD-inrichtingsservice houdt bron- en doelsystemen gesynchroniseerd door accounts te de-provisioneren wanneer gebruikers geen toegang meer zouden moeten hebben. 

De Azure AD-inrichtingsservice verwijdert een gebruiker in een toepassing wanneer de toepassing zachte verwijderingen suupports (updateaanvraag met actief = false) en een van de volgende gebeurtenissen optreedt:

* Het gebruikersaccount wordt verwijderd in Azure AD
*   De gebruiker is niet toegewezen aan de toepassing
*   De gebruiker voldoet niet meer aan een scopingfilter en valt buiten het bereik
    * Standaard verwijdert of schakelt de Azure AD-inrichtingsservice gebruikers die buiten het bereik vallen, uit. Als u dit standaardgedrag wilt overschrijven, u instellen dat een vlag [verwijderingen buiten het bereik overslaat.](../app-provisioning/skip-out-of-scope-deletions.md)
*   De eigenschap AccountEnabled is ingesteld op Onwaar

Als een van de bovenstaande vier gebeurtenissen optreedt en de doeltoepassing geen zachte verwijderingen ondersteunt, stuurt de provisioning-service een delete-verzoek om de gebruiker permanent uit de app te verwijderen. 

30 dagen nadat een gebruiker is verwijderd in Azure AD, worden deze definitief uit de tenant verwijderd. Op dit moment stuurt de provisioning service een DELETE-verzoek om de gebruiker in de toepassing permanent te verwijderen. Op elk moment tijdens het 30-dagenvenster u [een gebruiker handmatig permanent verwijderen,](../fundamentals/active-directory-users-restore.md)waardoor een verwijderverzoek naar de toepassing wordt verstuurt.

Als u een kenmerk IsSoftDeleted ziet in uw kenmerktoewijzingen, wordt het gebruikt om de status van de gebruiker te bepalen en of u een updateverzoek met actief = false wilt verzenden om de gebruiker te verwijderen. 

## <a name="next-steps"></a>Volgende stappen

[Implementatie van een automatische gebruikersinrichting plannen](../app-provisioning/plan-auto-user-provisioning.md)

[Inrichting configureren voor een galerie-app](../manage-apps/configure-automatic-user-provisioning-portal.md)

[Een SCIM-eindpunt bouwen en provisioning configureren bij het maken van uw eigen app](../app-provisioning/use-scim-to-provision-users-and-groups.md)

[Problemen met het configureren en inrichten van gebruikers oplossen voor een toepassing](../manage-apps/application-provisioning-config-problem.md).
