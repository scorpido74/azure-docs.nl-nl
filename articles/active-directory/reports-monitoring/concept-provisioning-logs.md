---
title: Inrichtings Logboeken in de Azure Active Directory-Portal (preview) | Microsoft Docs
description: Inleiding tot het inrichtings activiteiten rapporten in de Azure Active Directory Portal
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
ms.date: 07/29/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74b2d80d21822758991ad8b3bc1d3bb44e2257f4
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129807"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Rapporten inrichten in de Azure Active Directory Portal (preview)

De rapportage architectuur in Azure Active Directory (Azure AD) bestaat uit de volgende onderdelen:

- **Activiteit** 
    - Aanmeldingen: informatie over het gebruik van beheerde toepassingen en aanmeldings activiteiten voor gebruikers.
    - **Audit logboeken** - [audit logboeken](concept-audit-logs.md) bevatten informatie over de systeem activiteit van gebruikers en groeps beheer, beheerde toepassingen en Directory-activiteiten.
    - Inrichtings logboeken: systeem activiteiten bieden over gebruikers, groepen en rollen die zijn ingericht door de Azure AD-inrichtings service. 

- **Beveiliging** 
    - **Risk ante** aanmeldingen: een [Risk ante aanmelding](concept-risky-sign-ins.md) is een indicator voor een aanmeldings poging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikers account is.
    - **Gebruikers die zijn gemarkeerd voor risico** : een [Risk ante gebruiker](concept-user-at-risk.md) is een indicator voor een gebruikers account dat mogelijk is aangetast.

In dit onderwerp vindt u een overzicht van het inrichtings rapport.

## <a name="prerequisites"></a>Vereisten

### <a name="who-can-access-the-data"></a>Wie heeft er toegang tot de gegevens?
* Gebruikers in de rollen beveiligings beheerder, beveiligings lezer, rapport lezer, toepassings beheerder en Cloud toepassings beheerder
* Globale beheerders


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>Welke Azure AD-licentie hebt u nodig om de inrichtings activiteiten te openen?

Aan uw Tenant moet een Azure AD Premium-licentie zijn gekoppeld om het rapport alle inrichtings activiteiten te bekijken. Zie [Aan de slag met Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) om uw versie van Azure Active Directory te upgraden. 

## <a name="provisioning-logs"></a>Inrichtings logboeken

De inrichtings logboeken bieden antwoorden op de volgende vragen:

* Welke groepen zijn met succes gemaakt in ServiceNow?
* Hoe rollen zijn geïmporteerd uit Amazon Web Services?
* Wat zijn de gebruikers die niet met succes zijn gemaakt in DropBox?

U kunt toegang krijgen tot de inrichtings logboeken door inrichtings logboeken te selecteren in de sectie **bewaking** van de Blade **Azure Active Directory** in de [Azure Portal](https://portal.azure.com). Het kan Maxi maal twee uur duren voordat bepaalde inrichtings records worden weer gegeven in de portal.

![] Inrichtings logboeken (./media/concept-provisioning-logs/access-provisioning-logs.png "") Inrichtings logboeken


Een inrichtings logboek heeft een standaard lijst weergave waarin het volgende wordt weer gegeven:

- De identiteit
- De actie
- Het bron systeem
- Het doel systeem
- De status
- De datum


![Standaard kolommen](./media/concept-provisioning-logs/default-columns.png "Standaard kolommen")

U kunt de lijstweergave aanpassen door te klikken op **Kolommen** op de werkbalk.

![Kolom kiezer](./media/concept-provisioning-logs/column-chooser.png "Kolom kiezer")

Hiermee kunt u extra velden weergeven of velden verwijderen die al worden weergegeven.

![Beschik bare kolommen](./media/concept-provisioning-logs/available-columns.png "Beschik bare kolommen")

Selecteer een item in de lijst weergave voor meer gedetailleerde informatie.

![Gedetailleerde informatie](./media/concept-provisioning-logs/detailed-information.png "Gedetailleerde informatie")


## <a name="filter-provisioning-activities"></a>Inrichtings activiteiten filteren

Als u de gerapporteerde gegevens wilt beperken tot een niveau dat geschikt is voor u, kunt u de inrichtings gegevens filteren met behulp van de volgende standaard velden. Houd er rekening mee dat de waarden in de filters dynamisch worden ingevuld op basis van uw Tenant. Als u bijvoorbeeld geen gebeurtenissen voor maken in uw Tenant hebt, is er geen filter optie om te maken.

- Identiteit
- Action
- Bron systeem
- Doel systeem
- Status
- Date


![Filter](./media/concept-provisioning-logs/filter.png "Filter")

Met het identiteits filter kunt u de naam of de identiteit opgeven die u bevalt. Deze identiteit kan een gebruiker, een groep, een rol of een ander object zijn. U kunt zoeken op de naam of ID van het object. De ID is afhankelijk van het scenario. Wanneer u bijvoorbeeld een object inricht vanuit Azure AD naar Sales Force, is de bron-ID de object-ID van de gebruiker in azure AD terwijl de TargetID de ID van de gebruiker in Sales Force is. Bij het inrichten van workday naar Active Directory, is de bron-ID de werk nemer-ID van de werkdag. Houd er rekening mee dat de naam van de gebruiker mogelijk niet altijd aanwezig is in de identiteits kolom. Er wordt altijd één ID weer. 

Met het filter **bron systeem** kunt u opgeven waar de identiteit van wordt opgehaald. Bij het inrichten van een object van Azure AD naar ServiceNow, is het bron systeem bijvoorbeeld Azure AD. 

Met het filter **doel systeem** kunt u opgeven waar de identiteit wordt ingericht. Bij het inrichten van een object van Azure AD naar ServiceNow, is het doel systeem bijvoorbeeld ServiceNow. 

Met het **status** filter kunt u het volgende selecteren:

- Alle
- Geslaagd
- Fout
- Overgeslagen

Met het **actie** filter kunt u het volgende filteren:

- Maken 
- Update
- Verwijderen
- Uitschakelen
- Overige

Met het filter **Datum** kunt u een tijdsbestek opgeven voor de geretourneerde gegevens.  
Mogelijke waarden zijn:

- 1 maand
- 7 dagen
- 30 dagen
- 24 uur
- Aangepaste tijdinterval

Wanneer u een aangepast tijds bestek selecteert, kunt u een begin-en eind datum configureren.


Naast de standaard velden, wanneer deze zijn geselecteerd, kunt u ook de volgende velden in het filter toevoegen:

- **Taak-id** : een unieke taak-id is gekoppeld aan elke toepassing waarvoor u het inrichten hebt ingeschakeld.   

- **Cyclus-id** : Hiermee wordt de inrichtings cyclus uniek geïdentificeerd. U kunt deze ID delen ter ondersteuning van het opzoeken van de cyclus waarin deze gebeurtenis plaatsvond.

- **Wijzig de id** -unieke id voor de inrichtings gebeurtenis. U kunt deze ID delen ter ondersteuning van het opzoeken van de inrichtings gebeurtenis.   



  

## <a name="provisioning-details"></a>Inrichtings gegevens 

Wanneer u een item in de inrichtings lijst weergave selecteert, krijgt u meer informatie over dit item.
De details worden gegroepeerd op basis van de volgende categorieën:

- Stappen

- Problemen oplossen en aanbevelingen

- Gewijzigde eigenschappen

- Samenvatting


![Filter](./media/concept-provisioning-logs/provisioning-tabs.png "Tabbladen")



### <a name="steps"></a>Stappen

Het tabblad **stappen** bevat een overzicht van de stappen voor het inrichten van een object. Het inrichten van een object kan bestaan uit vier stappen: 

- Object importeren
- Bepalen of het object binnen bereik is
- Object tussen bron en doel matchen
- Inrichtings object (actie ondernemen: dit kan een maken, bijwerken, verwijderen of uitschakelen zijn)



![Filter](./media/concept-provisioning-logs/steps.png "Filter")


### <a name="troubleshoot-and-recommendations"></a>Problemen oplossen en aanbevelingen


Op het tabblad **probleem oplossing en aanbevelingen** vindt u de fout code en de reden. De fout gegevens zijn alleen beschikbaar in het geval van een fout. 


### <a name="modified-properties"></a>Gewijzigde eigenschappen

De **gewijzigde eigenschappen** bevat de oude waarde en nieuwe waarde. In gevallen waarin er geen oude waarde is, is de kolom oude waarde leeg. 


### <a name="summary"></a>Samenvatting

Op het tabblad **samen vatting** vindt u een overzicht van wat er is gebeurd en de id's voor het object in het bron-en doel systeem. 

## <a name="what-you-should-know"></a>Wat u moet weten

- In de Azure Portal worden de gemelde inrichtings gegevens 30 dagen opgeslagen als u een Premium-editie en 7 dagen hebt als u een gratis editie hebt.

- U kunt het kenmerk ID wijzigen als unieke id gebruiken. Dit is bijvoorbeeld handig bij interactie met product ondersteuning.

- Er is momenteel geen optie om inrichtings gegevens te downloaden.

- Er is momenteel geen ondersteuning voor log Analytics.

- Wanneer u de inrichtings logboeken vanuit de context van een app opent, worden de gebeurtenissen niet automatisch gefilterd op de specifieke app, zoals bij de controle Logboeken.

## <a name="next-steps"></a>Volgende stappen

* [De status van het inrichten van gebruikers controleren](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [Probleem bij het configureren van de gebruikers inrichting voor een Azure AD Gallery-toepassing](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)


