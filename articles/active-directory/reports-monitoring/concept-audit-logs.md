---
title: Controleactiviteitenrapporten in Azure Active Directory Portal | Microsoft Docs
description: Ontdek de controleactiviteitenrapporten in de Azure Active Directory Portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad399fc24b2cdfbdc51e7feccba2c05786216b19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79253232"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Controleactiviteitenrapporten in Azure Active Directory Portal 

Met Azure Active Directory-rapporten (Azure AD) kunt u de gegevens ophalen die u nodig hebt om te bepalen hoe uw omgeving wordt uitgevoerd.

De rapportage architectuur bestaat uit de volgende onderdelen:

- **Activiteit** 
    - **Aanmeldingen** : het [rapport met aanmeldingen](concept-sign-ins.md) bevat informatie over het gebruik van beheerde toepassingen en aanmeldings activiteiten voor gebruikers.
    - **Auditlogboeken**: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voor beelden van audit logboeken zijn wijzigingen die zijn aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleid.
- **Beveiliging** 
    - **Risk ante aanmeldingen** : een [Risk ante aanmelding](concept-risky-sign-ins.md) is een indicator voor een aanmeldings poging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikers account is. 
    - **Gebruikers die zijn gemarkeerd voor risico** : een [Risk ante gebruiker](concept-user-at-risk.md) is een indicator voor een gebruikers account dat mogelijk is aangetast.

In dit artikel vindt u een overzicht van het controle rapport.
 
## <a name="who-can-access-the-data"></a>Wie heeft er toegang tot de gegevens?

* Gebruikers in de **beveiligings beheerder**, **beveiligings lezer**, **rapport lezer** , **globale lezer** of **globale beheerders** rollen

## <a name="audit-logs"></a>Auditlogboeken

De Azure AD-controle logboeken bieden records van systeem activiteiten voor naleving. Om het controle rapport te openen, selecteert u **controle logboeken** in het gedeelte **bewaking** van **Azure Active Directory**. Houd er rekening mee dat audit Logboeken een latentie van Maxi maal een uur kunnen hebben, zodat het lang kan duren voordat de gegevens van de controle activiteit worden weer gegeven in de portal nadat u de taak hebt voltooid.



Een controlelogboek heeft een standaardlijstweergave die het volgende laat zien:

- de datum en tijd van de gebeurtenis
- de service die het exemplaar heeft geregistreerd
- de categorie en de naam van de activiteit (*wat*) 
- de status van de activiteit (geslaagd of mislukt)
- het doel
- de initiator/actor (wie) van een activiteit

![Auditlogboeken](./media/concept-audit-logs/listview.png "Auditlogboeken")

U kunt de lijstweergave aanpassen door te klikken op **Kolommen** op de werkbalk.

![Auditlogboeken](./media/concept-audit-logs/columns.png "Auditlogboeken")

Hiermee kunt u extra velden weergeven of velden verwijderen die al worden weergegeven.

![Auditlogboeken](./media/concept-audit-logs/columnselect.png "Auditlogboeken")

Selecteer een item in de lijst weergave voor meer gedetailleerde informatie.

![Auditlogboeken](./media/concept-audit-logs/details.png "Auditlogboeken")


## <a name="filtering-audit-logs"></a>Auditlogboeken filteren

U kunt de controle gegevens filteren op de volgende velden:

- Service
- Categorie
- Activiteit
- Status
- Doel
- Gestart door (actor)
- Datumbereik

![Auditlogboeken](./media/concept-audit-logs/filter.png "Auditlogboeken")

Met het **service** filter kunt u een keuze uit een vervolg keuzelijst van de volgende services selecteren:

- Alle
- AAD-beheer UX
- Toegangsbeoordelingen
- Account inrichten
- Toepassingsproxy
- Verificatiemethoden
- B2C
- Voorwaardelijke toegang
- Hoofddirectory
- Beheer rechten
- Hybride verificatie
- Identiteitsbeveiliging
- Uitgenodigde gebruikers
- MIM-service
- MyApps
- PIM
- Self-service voor groepsbeheer
- Self-service voor wachtwoordbeheer
- Gebruiksrechtovereenkomst

Met het filter **categorie** kunt u een van de volgende filters selecteren:

- Alle
- AdministrativeUnit
- ApplicationManagement
- Verificatie
- Autorisatie
- Contactpersoon
- Apparaat
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- KerberosDomain
- Beheer van handelingen
- Label
- Overige
- PermissionGrantPolicy
- Beleid
- ResourceManagement
- RoleManagement
- UserManagement

Het **activiteiten** filter is gebaseerd op de selectie van de categorie en het resource type voor de activiteit die u maakt. U kunt een specifieke activiteit of alle activiteiten selecteren. 

U kunt de lijst met alle controle activiteiten ophalen met behulp van de Graph API:`https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

Met het filter **status** kunt u filteren op basis van de status van een audit bewerking. De status kan een van de volgende zijn:

- Alle
- Geslaagd
- Fout

Met het **doel** filter kunt u zoeken naar een bepaald doel door de naam of het User Principal Name (UPN) te starten. De doel naam en UPN zijn hoofdletter gevoelig. 

Met het filter **gestart door** kunt u definiëren hoe de naam van een actor of een Universal Principal Name (UPN) begint met. De naam en UPN zijn hoofdletter gevoelig.

Met het filter **datum bereik** kunt u een tijds bestek voor de geretourneerde gegevens definiëren.  
Mogelijke waarden zijn:

- 7 dagen
- 24 uur
- Aangepast

Wanneer u een aangepast tijdsbestek selecteert, kunt u een begintijd en eindtijd configureren.

U kunt er ook voor kiezen om de gefilterde gegevens te downloaden, tot 250.000 records door de knop **downloaden** te selecteren. U kunt de logboeken downloaden in de CSV-of JSON-indeling. Het aantal records dat u kunt downloaden, is beperkt door het [Bewaar beleid](reference-reports-data-retention.md)voor de Azure Active Directory-rapport.

![Auditlogboeken](./media/concept-audit-logs/download.png "Auditlogboeken")

## <a name="audit-logs-shortcuts"></a>Snelkoppelingen naar auditlogboeken

Naast **Azure Active Directory** biedt de Azure Portal twee extra beginpunten voor gegevenscontrole:

- Gebruikers en groepen
- Bedrijfstoepassingen

### <a name="users-and-groups-audit-logs"></a>Auditlogboeken voor gebruikers en groepen

Met de controlerapporten op basis van gebruikers en groepen krijgt u antwoord op vragen zoals:

- Welke soorten updates zijn toegepast op de gebruikers?

- Hoeveel gebruikers zijn gewijzigd?

- Hoeveel wachtwoorden zijn gewijzigd?

- Wat heeft een beheerder in een directory gedaan?

- Welke groepen zijn toegevoegd?

- Zijn er groepen met wijzigingen in het lidmaatschap?

- Zijn de eigenaren van een groep gewijzigd?

- Welke licenties zijn toegewezen aan een groep of een gebruiker?

Als u alleen controle gegevens wilt bekijken die gerelateerd zijn aan gebruikers, kunt u een gefilterde weer gave vinden onder **controle logboeken** in het gedeelte **bewaking** van het tabblad **gebruikers** . Dit ingangs punt heeft **UserManagement** als voorgeselecteerde categorie.

![Auditlogboeken](./media/concept-audit-logs/users.png "Auditlogboeken")

Als u alleen controle gegevens wilt bekijken die aan groepen zijn gerelateerd, kunt u een gefilterde weer gave vinden onder **controle logboeken** in het gedeelte **bewaking** van het tabblad **groepen** . Dit ingangs punt heeft **GroupManagement** als voorgeselecteerde categorie.

![Auditlogboeken](./media/concept-audit-logs/groups.png "Auditlogboeken")

### <a name="enterprise-applications-audit-logs"></a>Auditlogboeken voor bedrijfstoepassingen

Met de controlerapporten op basis van toepassingen krijgt u antwoord op vragen zoals:

* Welke toepassingen zijn toegevoegd of bijgewerkt?
* Welke toepassingen zijn verwijderd?
* Is een service-principal voor een toepassing gewijzigd?
* Zijn de namen van toepassingen gewijzigd?
* Wie heeft toestemming gegeven voor een toepassing?

Als u controle gegevens wilt bekijken die betrekking hebben op uw toepassingen, kunt u een gefilterde weer gave vinden onder **controle logboeken** in het gedeelte **activiteit** van de Blade **bedrijfs toepassingen** . Voor dit ingangs punt zijn **bedrijfs toepassingen** voorgeselecteerd als het **toepassings type**.

![Auditlogboeken](./media/concept-audit-logs/enterpriseapplications.png "Auditlogboeken")

## <a name="office-365-activity-logs"></a>Office 365-activiteiten logboeken

U kunt activiteiten logboeken van Office 365 bekijken vanuit het [Microsoft 365-beheer centrum](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Hoewel Office 365-activiteiten en activiteiten logboeken van Azure AD een groot aantal Directory bronnen delen, is alleen het Microsoft 365-beheer centrum een volledig overzicht van de activiteiten logboeken van Office 365. 

U kunt de activiteiten logboeken van Office 365 ook programmatisch openen met behulp van de [office 365-beheer-api's](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Volgende stappen

- [Referentie voor auditactiviteiten van Azure AD](reference-audit-activities.md)
- [Referentie voor retentie van Azure AD-rapporten](reference-reports-data-retention.md)
- [Naslag informatie over latentie van Azure AD-logboeken](reference-reports-latencies.md)
