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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253232"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Controleactiviteitenrapporten in Azure Active Directory Portal 

Met Azure AD-rapporten (Azure Directory) u de informatie krijgen die u nodig hebt om te bepalen hoe het met uw omgeving gaat.

De rapportagearchitectuur bestaat uit de volgende componenten:

- **Activiteit** 
    - **Aanmeldingsrapport** : het [aanmeldingsrapport](concept-sign-ins.md) bevat informatie over het gebruik van beheerde toepassingen en aanmeldingsactiviteiten voor gebruikers.
    - **Auditlogboeken**: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voorbeelden van controlelogboeken zijn wijzigingen die zijn aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleidsregels.
- **Beveiliging** 
    - **Riskante aanmeldingen** - Een [riskante aanmelding](concept-risky-sign-ins.md) is een indicator voor een aanmeldingspoging die mogelijk is uitgevoerd door iemand die niet de rechtmatige eigenaar van een gebruikersaccount is. 
    - **Gebruikers gemarkeerd voor risico** - Een [riskante gebruiker](concept-user-at-risk.md) is een indicator voor een gebruikersaccount die mogelijk is gecompromitteerd.

Dit artikel geeft u een overzicht van het auditrapport.
 
## <a name="who-can-access-the-data"></a>Wie heeft er toegang tot de gegevens?

* Gebruikers in de rollen **Beveiligingsbeheerder**, **Beveiligingslezer,** **Rapportlezer,** **Global Reader** of **Global Administrator**

## <a name="audit-logs"></a>Auditlogboeken

De Azure AD-controlelogboeken bieden records van systeemactiviteiten voor naleving. Als u het controlerapport wilt openen, selecteert u **Controlelogboeken** in de sectie **Controle** van **Azure Active Directory**. Houd er rekening mee dat controlelogboeken een latentie van maximaal een uur kunnen hebben, dus het kan zo lang duren voordat controleactiviteitsgegevens in de portal worden weergegeven nadat u de taak hebt voltooid.



Een controlelogboek heeft een standaardlijstweergave die het volgende laat zien:

- de datum en tijd van de gebeurtenis
- de service die de gebeurtenis heeft geregistreerd
- de categorie en de naam van de activiteit *(wat)* 
- de status van de activiteit (succes of mislukking)
- het doel
- de initiator/actor (wie) van een activiteit

![Auditlogboeken](./media/concept-audit-logs/listview.png "Auditlogboeken")

U kunt de lijstweergave aanpassen door te klikken op **Kolommen** op de werkbalk.

![Auditlogboeken](./media/concept-audit-logs/columns.png "Auditlogboeken")

Hiermee kunt u extra velden weergeven of velden verwijderen die al worden weergegeven.

![Auditlogboeken](./media/concept-audit-logs/columnselect.png "Auditlogboeken")

Selecteer een item in de lijstweergave voor meer gedetailleerde informatie.

![Auditlogboeken](./media/concept-audit-logs/details.png "Auditlogboeken")


## <a name="filtering-audit-logs"></a>Auditlogboeken filteren

U de controlegegevens filteren op de volgende velden:

- Service
- Categorie
- Activiteit
- Status
- Doel
- Gestart door (actor)
- Datumbereik

![Auditlogboeken](./media/concept-audit-logs/filter.png "Auditlogboeken")

Met het filter **Service** u kiezen uit een vervolgkeuzelijst van de volgende services:

- Alle
- AAD Management UX
- Toegangsbeoordelingen
- Account inrichten
- Toepassingsproxy
- Verificatiemethoden
- B2C
- Voorwaardelijke toegang
- Hoofddirectory
- Beheer van rechten
- Hybride verificatie
- Identiteitsbeveiliging
- Uitgenodigde gebruikers
- MIM-service
- MijnApps
- PIM
- Self-service voor groepsbeheer
- Self-service voor wachtwoordbeheer
- Gebruiksrechtovereenkomst

Met het filter **Categorie** u een van de volgende filters selecteren:

- Alle
- AdministrativeUnit
- ApplicationManagement
- Authentication
- Autorisatie
- Contactpersoon
- Apparaat
- DeviceConfiguration
- DirectoryBeheer
- EntitlementManagement
- GroupManagement
- KerberosDomein KerberosDomein
- KeyManagement
- Label
- Overige
- Beleid voor verlening van machtigingen
- Beleid
- ResourceBeheer
- RoleManagement
- UserManagement

Het filter **Activiteit** is gebaseerd op de selectie van de categorie en het type activiteit die u maakt. U kunt een specifieke activiteit of alle activiteiten selecteren. 

U de lijst met alle controleactiviteiten krijgen met behulp van de Graph API:`https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

Met het filter **Status** u filteren op basis van de status van een auditbewerking. De status kan een van de volgende:

- Alle
- Geslaagd
- Fout

Met het filter **Doel** u naar een bepaald doel zoeken aan de begin van de naam of gebruikersnaam (UPN). De doelnaam en UPN zijn hoofdlettergevoelig. 

Met **het filter Geïnitieerd** u bepalen wat de naam van een actor of een universele hoofdnaam (UPN) begint. De naam en UPN zijn casegevoelig.

Met het filter **Datumbereik** u een tijdsbestek voor de geretourneerde gegevens definiëren.  
Mogelijke waarden zijn:

- 7 dagen
- 24 uur
- Aangepast telefoonnummer

Wanneer u een aangepast tijdsbestek selecteert, kunt u een begintijd en eindtijd configureren.

U er ook voor kiezen om de gefilterde gegevens, tot 250.000 records, te downloaden door de knop **Downloaden te** selecteren. U de logboeken downloaden in de CSV- of JSON-indeling. Het aantal records dat u downloaden, wordt beperkt door het [azure Active Directory-rapportbehoudbeleid](reference-reports-data-retention.md).

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

Als u alleen controlegegevens wilt controleren die gerelateerd zijn aan gebruikers, u een gefilterde weergave vinden onder **Controlelogboeken** in de sectie **Controle** van het tabblad **Gebruikers.** Dit toegangspunt heeft **UserManagement** als vooraf geselecteerde categorie.

![Auditlogboeken](./media/concept-audit-logs/users.png "Auditlogboeken")

Als u alleen controlegegevens wilt controleren die gerelateerd zijn aan groepen, u een gefilterde weergave vinden onder **Controlelogboeken** in de sectie **Controle** van het tabblad **Groepen.** Dit toegangspunt heeft **GroupManagement** als vooraf geselecteerde categorie.

![Auditlogboeken](./media/concept-audit-logs/groups.png "Auditlogboeken")

### <a name="enterprise-applications-audit-logs"></a>Auditlogboeken voor bedrijfstoepassingen

Met de controlerapporten op basis van toepassingen krijgt u antwoord op vragen zoals:

* Welke toepassingen zijn toegevoegd of bijgewerkt?
* Welke toepassingen zijn verwijderd?
* Is een serviceprincipal voor een toepassing gewijzigd?
* Zijn de namen van toepassingen gewijzigd?
* Wie heeft toestemming gegeven voor een toepassing?

Als u controlegegevens met betrekking tot uw toepassingen wilt controleren, u een gefilterde weergave vinden onder **Controlelogboeken** in de sectie **Activiteit** van het **bedrijfsmes.** Met dit invoerpunt zijn **Enterprise-toepassingen** vooraf geselecteerd als **toepassingstype**.

![Auditlogboeken](./media/concept-audit-logs/enterpriseapplications.png "Auditlogboeken")

## <a name="office-365-activity-logs"></a>Office 365-activiteitslogboeken

U Office 365-activiteitslogboeken bekijken vanuit het [Microsoft 365-beheercentrum](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Hoewel office 365-activiteits- en Azure-ad-activiteitslogboeken veel directorybronnen delen, biedt alleen het Microsoft 365-beheercentrum een volledige weergave van de activiteitslogboeken van Office 365. 

U de Office 365-activiteitslogboeken ook programmatisch openen met behulp van de [OFFICE 365 Management API's.](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview)

## <a name="next-steps"></a>Volgende stappen

- [Referentie voor auditactiviteiten van Azure AD](reference-audit-activities.md)
- [Naslaginformatie over het bewaren van Azure AD-rapporten](reference-reports-data-retention.md)
- [Verwijzing naar latenlaten van Azure AD-logboeken](reference-reports-latencies.md)
