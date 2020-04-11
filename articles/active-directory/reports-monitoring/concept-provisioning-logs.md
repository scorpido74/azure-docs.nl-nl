---
title: Logboeken inrichten in de Azure Active Directory-portal (voorbeeld) | Microsoft Documenten
description: Inleiding tot rapportages over het inrichten van activiteiten in de Azure Active Directory-portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/04/2019
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30cc8be6ad9ebffcad58c5b2412ae15ff3f26fa5
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113323"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Rapporten inrichten in de Azure Active Directory-portal (voorbeeld)

De rapportagearchitectuur in Azure Active Directory (Azure AD) bestaat uit de volgende onderdelen:

- **Activiteit** 
    - **Aanmeldingsprogramma's** : informatie over het gebruik van beheerde toepassingen en aanmeldingsactiviteiten voor gebruikers.
    - **Controlelogboeken** - [Auditlogs](concept-audit-logs.md) bieden systeemactiviteitsinformatie over gebruikers en groepsbeheer, beheerde toepassingen en directoryactiviteiten.
    - **Logboeken inrichten** - Systeemactiviteit bieden over gebruikers, groepen en rollen die zijn ingericht door de Azure AD-inrichtingsservice. 

- **Beveiliging** 
    - **Riskante aanmeldingen** - Een [riskante aanmelding](concept-risky-sign-ins.md) is een indicator voor een aanmeldingspoging die mogelijk is uitgevoerd door iemand die niet de rechtmatige eigenaar van een gebruikersaccount is.
    - **Gebruikers gemarkeerd voor risico** - Een [riskante gebruiker](concept-user-at-risk.md) is een indicator voor een gebruikersaccount die mogelijk is gecompromitteerd.

Dit onderwerp geeft u een overzicht van het inrichtingsrapport.

## <a name="prerequisites"></a>Vereisten

### <a name="who-can-access-the-data"></a>Wie heeft er toegang tot de gegevens?
* Gebruikers in de rollen Beveiligingsbeheerder, Beveiligingslezer, Rapportlezer, Toepassingsbeheerder en CloudApplication Administrator
* Globale beheerders


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>Welke Azure AD-licentie hebt u nodig om toegang te krijgen tot inrichtingsactiviteiten?

Uw tenant moet een Azure AD Premium-licentie hebben om het rapport all up provisioning activity te bekijken. Zie [Aan de slag met Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) om uw versie van Azure Active Directory te upgraden. 

## <a name="provisioning-logs"></a>Inrichtingslogboeken

De inrichtingslogboeken geven antwoorden op de volgende vragen:

* Welke groepen zijn gemaakt in ServiceNow?
* Hoe rollen zijn geïmporteerd uit Amazon Web Services?
* Welke gebruikers zijn zonder succes gemaakt in DropBox?

U hebt toegang tot de inrichtingslogboeken door **Logboeken interichten** te selecteren in de sectie **Controle** van het **Azure Active Directory-blad** in de [Azure-portal.](https://portal.azure.com) Het kan tot twee uur duren voordat sommige inprovisioningrecords in het portaal worden weergegeven.

![Inrichtingslogboeken](./media/concept-provisioning-logs/access-provisioning-logs.png "Inrichtingslogboeken")


Een inrichtingslogboek heeft een standaardlijstweergave die wordt weergegeven:

- De identiteit
- De actie
- Het bronsysteem
- Het doelsysteem
- De status
- De datum


![Standaardkolommen](./media/concept-provisioning-logs/default-columns.png "Standaardkolommen")

U kunt de lijstweergave aanpassen door te klikken op **Kolommen** op de werkbalk.

![Kolomkiezer](./media/concept-provisioning-logs/column-chooser.png "Kolomkiezer")

Hiermee kunt u extra velden weergeven of velden verwijderen die al worden weergegeven.

![Beschikbare kolommen](./media/concept-provisioning-logs/available-columns.png "Beschikbare kolommen")

Selecteer een item in de lijstweergave voor meer gedetailleerde informatie.

![Gedetailleerde informatie](./media/concept-provisioning-logs/steps.png "Filteren")


## <a name="filter-provisioning-activities"></a>Filterinrichtingsactiviteiten

U uw inrichtingsgegevens filteren. Sommige filterwaarden worden dynamisch ingevuld op basis van uw tenant. Als u bijvoorbeeld geen afspraken maakt in uw tenant, is er geen filteroptie voor maken.
In de standaardweergave u de volgende filters selecteren:

- Identiteit
- Date
- Status
- Actie


![Filter](./media/concept-provisioning-logs/default-filter.png "Filteren")

Met het filter **Identiteit** u de naam of de identiteit opgeven waar u om geeft. Deze identiteit kan een gebruiker, groep, rol of ander object zijn. U zoeken op de naam of id van het object. De ID verschilt per scenario. Wanneer u bijvoorbeeld een object instelt van Azure AD naar SalesForce, is de bron-id de object-id van de gebruiker in Azure AD, terwijl de TargetID de id van de gebruiker in Salesforce is. Bij het inrichten van Workday naar Active Directory is de bron-id de werknemer-id van de werknemer Workday.When provisioning from Workday to Active Directory, the Source ID is the Workday worker worker ID. Houd er rekening mee dat de naam van de gebruiker mogelijk niet altijd aanwezig is in de kolom Identiteit. Er zal altijd één ID zijn. 


Met het filter **Datum** kunt u een tijdsbestek opgeven voor de geretourneerde gegevens.  
Mogelijke waarden zijn:

- 1 maand
- 7 dagen
- 30 dagen
- 24 uur
- Aangepast tijdsinterval

Wanneer u een aangepast tijdsbestek selecteert, u een begindatum en een einddatum configureren.


Met het filter **Status** u het:

- Alle
- Geslaagd
- Fout
- Overgeslagen



Met het filter **Actie** u het:

- Maken 
- Bijwerken
- Verwijderen
- Uitschakelen
- Overige

Bovendien u bij de filters van de standaardweergave ook de volgende filters instellen:

- Taak-ID
- Cyclus-id
- ID wijzigen
- Bron-id
- Doel-ID
- Toepassing


![Een veld kiezen](./media/concept-provisioning-logs/add-filter.png "Een veld kiezen")


- **Functie-ID** - Een unieke functie-ID is gekoppeld aan elke toepassing waarvoor u provisioning hebt ingeschakeld.   

- **Cycle ID** - Identificeert op unieke wijze de inrichtingscyclus. U deze ID delen om de cyclus op te zoeken waarin deze gebeurtenis heeft plaatsgevonden.

- **ID wijzigen** - Unieke id voor de inrichtingsgebeurtenis. U deze id delen om de inrichtingsgebeurtenis op te zoeken.   


- **Bronsysteem** - Hiermee u opgeven waar de identiteit vandaan komt. Wanneer u bijvoorbeeld een object instelt van Azure AD naar ServiceNow, is het bronsysteem Azure AD. 

- **Doelsysteem** - Hiermee u opgeven waar de identiteit wordt ingericht. Wanneer u bijvoorbeeld een object instelt van Azure AD naar ServiceNow, is het doelsysteem ServiceNow. 

- **Toepassing** - Hiermee u alleen records van toepassingen weergeven met een weergavenaam die een specifieke tekenreeks bevat.

 

## <a name="provisioning-details"></a>Nadere bijzonderheden 

Wanneer u een item selecteert in de lijstweergave voor inrichten, krijgt u meer details over dit item.
De gegevens zijn gegroepeerd op basis van de volgende categorieën:

- Stappen

- Problemen en aanbevelingen oplossen

- Gewijzigde eigenschappen

- Samenvatting


![Filter](./media/concept-provisioning-logs/provisioning-tabs.png "Tabbladen")



### <a name="steps"></a>Stappen

Het tabblad **Stappen** geeft een overzicht van de stappen die zijn genomen om een object in te richten. Het inrichten van een object kan uit vier stappen bestaan: 

- Object importeren
- Bepalen of het object zich in het bereik bevindt
- Object overeenkomen tussen bron en doel
- Object voorziening (actie ondernemen - dit kan een maken, bijwerken, verwijderen of uitschakelen zijn)



![Filter](./media/concept-provisioning-logs/steps.png "Filteren")


### <a name="troubleshoot-and-recommendations"></a>Problemen en aanbevelingen oplossen


Het tabblad **Problemen en aanbevelingen** bevat de foutcode en reden. De foutinformatie is alleen beschikbaar in het geval van een storing. 


### <a name="modified-properties"></a>Gewijzigde eigenschappen

De **gewijzigde eigenschappen** tonen de oude waarde en de nieuwe waarde. In gevallen waarin er geen oude waarde is, is de oude waardekolom leeg. 


### <a name="summary"></a>Samenvatting

Het **tabblad overzicht** geeft een overzicht van wat er is gebeurd en id's voor het object in het bron- en doelsysteem. 

## <a name="what-you-should-know"></a>Wat u moet weten

- De Azure-portal slaat gerapporteerde provisioninggegevens op voor 30 dagen als u een premium-editie hebt en 7 dagen als u een gratis editie hebt..

- U het kenmerk Wijzigings-id gebruiken als unieke id. Dit is bijvoorbeeld handig bij interactie met productondersteuning.

- Er is momenteel geen optie om provisioning gegevens te downloaden.

- Er is momenteel geen ondersteuning voor log-analyses.

- Wanneer u de inrichtingslogboeken opent vanuit de context van een app, worden gebeurtenissen niet automatisch gefilterd naar de specifieke app op de manier waarop controlelogboeken dit doen.

## <a name="error-codes"></a>Foutcodes

Gebruik de onderstaande tabel om beter te begrijpen hoe u fouten oplossen die u vinden in de inrichtingslogboeken. Geef feedback via de link onder aan deze pagina voor foutcodes die ontbreken. 

|Foutcode|Beschrijving|
|---|---|
|Conflict, EntryConflict|Corrigeer de conflicterende kenmerkwaarden in Azure AD of de toepassing of controleer de overeenkomende kenmerkconfiguratie als het conflicterende gebruikersaccount zou moeten worden geëvenaard en overgenomen. Bekijk de volgende [documentatie](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) voor meer informatie over het configureren van overeenkomende kenmerken.|
|TooManyRequests|De doel-app heeft deze poging om de gebruiker bij te werken afgewezen omdat deze overbelast is en te veel aanvragen ontvangt. Er is niets te doen. Deze poging wordt automatisch in gebruik genomen. Microsoft is ook op de hoogte gebracht van dit probleem.|
|InternalServerError |De doel-app heeft een onverwachte fout gegenereerd. Er kan een serviceprobleem zijn met de doeltoepassing waardoor dit niet werkt. Deze poging wordt automatisch binnen 40 minuten uitgeschakeld.|
|OnvoldoendeRechten, MethodNotAtoegestaan, Niet Toegestaan, Onbevoegd| Azure AD kon verifiëren met de doeltoepassing, maar is niet gemachtigd om de update uit te voeren. Controleer alle instructies die door de doeltoepassing evenals de respectieve [toepassingszelfstudie worden verstrekt.](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)|
|Onverwerkbare entiteit|De doeltoepassing heeft een onverwachte reactie geretourneerd. De configuratie van de doeltoepassing is mogelijk niet correct of er kan een serviceprobleem zijn met de doeltoepassing waardoor dit niet werkt.|
|WebExceptionProtocolD |Er is een HTTP-protocolfout opgetreden tijdens het maken van verbinding met de doeltoepassing. Er is niets te doen. Deze poging wordt automatisch binnen 40 minuten uitgeschakeld.|
|Ongeldig Anker|Een gebruiker die eerder is gemaakt of gematcht door de inrichtingsservice, bestaat niet meer. Controleer of de gebruiker bestaat. Als u een re-match van alle gebruikers wilt forceren, gebruikt u de MS Graph API om taak opnieuw op te [starten.](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http) Houd er rekening mee dat het opnieuw opstarten van provisioning een eerste cyclus zal activeren, wat tijd kan kosten om te voltooien. Het verwijdert ook de cache die de inrichtingsservice gebruikt om te werken, wat betekent dat alle gebruikers en groepen in de tenant opnieuw moeten worden geëvalueerd en bepaalde provisioning-gebeurtenissen kunnen worden verwijderd.|
|Niet geïmplementeerd | De doel-app heeft een onverwachte reactie gekregen. De configuratie van de app is mogelijk niet correct of er kan een serviceprobleem zijn met de doel-app waardoor dit niet werkt. Controleer alle instructies die door de doeltoepassing evenals de respectieve [toepassingszelfstudie worden verstrekt.](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) |
|MandatoryFieldsMissing, MissingValues |De gebruiker kan niet worden gemaakt omdat vereiste waarden ontbreken. Corrigeer de ontbrekende kenmerkwaarden in de bronrecord of controleer de overeenkomende kenmerkconfiguratie om te controleren of de vereiste velden niet worden weggelaten. [Meer informatie](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) over het configureren van overeenkomende kenmerken.|
|Schemaattribuut niet gevonden |Kan de bewerking niet uitvoeren omdat er een kenmerk is opgegeven dat niet bestaat in de doeltoepassing. Bekijk de [documentatie](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) over het aanpassen van kenmerken en zorg ervoor dat uw configuratie correct is.|
|InternalError |Er is een interne servicefout opgetreden binnen de Azure AD-inrichtingsservice. Er is niets te doen. Deze poging wordt automatisch opnieuw geprobeerd in 40 minuten.|
|OngeldigDomein |De bewerking kan niet worden uitgevoerd vanwege een kenmerkwaarde die een ongeldige domeinnaam bevat. Werk de domeinnaam op de gebruiker bij of voeg deze toe aan de toegestane lijst in de doeltoepassing. |
|Time-out |De bewerking kan niet worden voltooid omdat de doeltoepassing te lang duurde om te reageren. Er is niets te doen. Deze poging wordt automatisch opnieuw geprobeerd in 40 minuten.|
|Licentielimiet overschreden|De gebruiker kan niet worden gemaakt in de doeltoepassing omdat er geen beschikbare licenties voor deze gebruiker zijn. U extra licenties aanschaffen voor de doeltoepassing of uw gebruikerstoewijzingen en toewijzingsconfiguratie controleren om ervoor te zorgen dat de juiste gebruikers de juiste kenmerken hebben toegewezen.|
|Dubbele doelvermeldingen  |De bewerking kan niet worden voltooid omdat er meer dan één gebruiker in de doeltoepassing is gevonden met de geconfigureerde overeenkomende kenmerken. Verwijder de dubbele gebruiker uit de doeltoepassing of configureer de toewijzingen van het kenmerk zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)beschreven.|
|DuplicateSourceEntries | De bewerking kan niet worden voltooid omdat er meer dan één gebruiker is gevonden met de geconfigureerde overeenkomende kenmerken. Verwijder de dubbele gebruiker of configureer de toewijzingen van kenmerken zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)beschreven.|

## <a name="next-steps"></a>Volgende stappen

* [Controleer de status van de gebruikersinrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [Probleem met het configureren van gebruikersinrichting voor een Azure AD Gallery-toepassing](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)


