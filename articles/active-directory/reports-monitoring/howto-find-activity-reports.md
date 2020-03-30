---
title: Rapporten over gebruikersactiviteit zoeken in Azure-portal | Microsoft Documenten
description: Meer informatie over de rapporten over azure Active Directory-gebruikersactiviteiten in de Azure-portal.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d356f8c10ae3770d9f4ade100ab0496ee58d772f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74008217"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Activiteitenrapporten zoeken in de Azure-portal

In dit artikel vindt u informatie over het zoeken naar azure active directory-rapporten (Azure AD) van gebruikersactiviteiten in de Azure-portal.

## <a name="audit-logs-report"></a>Rapport voor audittrails

Het rapport met controlelogboeken combineert verschillende rapporten rond toepassingsactiviteiten in één weergave voor contextgebaseerde rapportage. Ga als het gaat om toegang tot het rapport controlelogboeken:

1. Navigeer naar de [Azure-portal](https://portal.azure.com).
2. Selecteer uw map in de rechterbovenhoek en selecteer vervolgens het **Azure Active Directory-blad** in het linkernavigatiedeelvenster.
3. Selecteer **Controlelogboeken** in de sectie **Activiteit** van het Azure Active Directory-blad. 

    ![Auditlogboeken](./media/howto-find-activity-reports/482.png "Auditlogboeken")

In het rapport controlelogboeken worden de volgende rapporten geconsolideerd:

* Auditrapport
* Activiteit voor wachtwoord opnieuw instellen
* Registratieactiviteit voor het opnieuw instellen van wachtwoorden
* Activiteit selfservicegroepen
* Naamswijzigingen van Office365-groepsnamen
* Rekeningbepalingsactiviteit
* Status voor wachtwoordrollover
* Fouten bij het inrichten van een account

### <a name="filtering-on-audit-logs"></a>Filteren op controlelogboeken

U geavanceerde filtering in het controlerapport gebruiken om toegang te krijgen tot een specifieke categorie controlegegevens door deze op te geven in het **filter Categorie.** Als u bijvoorbeeld alle activiteiten met betrekking tot gebruikers wilt weergeven, selecteert u de categorie **UserManagement.** 

Categorieën zijn:

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
- Overige
- Beleid
- ResourceBeheer
- RoleManagement
- UserManagement

U ook filteren op een specifieke service met het vervolgkeuzefilter **Service.** Als u bijvoorbeeld alle controlegebeurtenissen met betrekking tot selfservicewachtwoordbeheer wilt bekijken, selecteert u het filter **Wachtwoordbeheer selfservice.**

Diensten omvatten:

- Alle
- Toegangsbeoordelingen
- Account inrichten 
- Toepassing SSO
- Verificatiemethoden
- B2C
- Voorwaardelijke toegang
- Hoofddirectory
- Beheer van rechten
- Identiteitsbeveiliging
- Uitgenodigde gebruikers
- PIM
- Self-service voor groepsbeheer
- Self-service voor wachtwoordbeheer
- Gebruiksrechtovereenkomst

## <a name="sign-ins-report"></a>Aanmeldingenrapport 

De weergave **Aanmeldingen** bevat alle aanmeldingen van gebruikers en het rapport **Toepassingsgebruik.** U ook informatie over toepassingsgebruik bekijken in het gedeelte **Beheren** van het overzicht **van Enterprise-toepassingen.**

Ga als bedoeld als toegang tot het aanmeldingsrapport:

1. Navigeer naar de [Azure-portal](https://portal.azure.com).
2. Selecteer uw map in de rechterbovenhoek en selecteer vervolgens het **Azure Active Directory-blad** in het linkernavigatiedeelvenster.
3. Selecteer **Aanmeldingen** in de sectie **Activiteit** van het Azure Active Directory-blad. 

    ![Weergave Aanmeldingen](./media/howto-find-activity-reports/483.png "Weergave Aanmeldingen")


### <a name="filtering-on-application-name"></a>Filteren op toepassingsnaam

U het aanmeldingsrapport gebruiken om details over het gebruik van toepassingen weer te geven door te filteren op gebruikersnaam of toepassingsnaam.

![Pagina Aanmeldingsgebeurtenissen filteren](./media/howto-find-activity-reports/07.png "Pagina Aanmeldingsgebeurtenissen filteren")

## <a name="security-reports"></a>Beveiligingsrapporten

### <a name="anomalous-activity-reports"></a>Afwijkende activiteitsrapporten

Afwijkende activiteitsrapporten bieden informatie over beveiligingsgerelateerde risicodetecties die Azure AD kan detecteren en rapporteren.

In de volgende tabel worden de afwijkende activiteitsbeveiligingsrapporten van Azure AD en de bijbehorende typen risicodetecties in de Azure-portal weergegeven. Zie [Azure Active Directory-risicodetecties](concept-risk-events.md)voor meer informatie .  


| Azure AD-afwijkend activiteitenrapport |  Type risicodetectie van identiteitsbescherming|
| :--- | :--- |
| Gebruikers van wie de referenties zijn gelekt | Gelekte referenties |
| Onregelmatige aanmeldingsactiviteiten | Onmogelijke reis naar ongewone locaties |
| Aanmeldingen vanaf mogelijk geïnfecteerde apparaten | Aanmeldingen vanaf geïnfecteerde apparaten|
| Aanmeldingen van onbekende bronnen | Aanmeldingen vanaf anonieme IP-adressen |
| Aanmeldingen van IP-adressen met verdachte activiteit | Aanmeldingen van IP-adressen met verdachte activiteit |
| - | Aanmeldingen vanaf onbekende locaties |

De volgende azure AD-afwijkende activiteitsbeveiligingsrapporten worden niet opgenomen als risicodetecties in de Azure-portal:

* Aanmeldingen na meerdere mislukte pogingen
* Aanmeldingen vanuit meerdere locaties


### <a name="detected-risk-detections"></a>Gedetecteerde risicodetecties

U hebt toegang tot rapporten over gedetecteerde risicodetecties in de sectie **Beveiliging** van het **Azure Active Directory-blad** in de [Azure-portal.](https://portal.azure.com) Gedetecteerde risicodetecties worden bijgehouden in de volgende rapporten:   

- [Gebruikers die risico lopen](concept-user-at-risk.md)
- [Riskante aanmeldingen](concept-risky-sign-ins.md)

    ![Beveiligingsrapporten](./media/howto-find-activity-reports/04.png "Beveiligingsrapporten")

## <a name="troubleshoot-issues-with-activity-reports"></a>Problemen met activiteitenrapporten oplossen

### <a name="missing-data-in-the-downloaded-activity-logs"></a>Ontbrekende gegevens in de gedownloade activiteitslogboeken

#### <a name="symptoms"></a>Symptomen 

Ik heb de activiteitenlogboeken (audit of aanmeldingen) gedownload en ik zie niet alle records voor de tijd die ik heb geselecteerd. Hoe komt dat? 

 ![Rapportage](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>Oorzaak

Wanneer u activiteitslogboeken downloadt in de Azure-portal, beperken we de schaal tot 250000 records, gesorteerd op de meest recente eerst. 

#### <a name="resolution"></a>Oplossing

U kunt gebruikmaken van [API's van Azure AD Reporting](concept-reporting-api.md) om maximaal een miljoen records op te halen.

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Ontbrekende controlegegevens voor recente acties in de Azure-portal

#### <a name="symptoms"></a>Symptomen

Ik heb enkele acties uitgevoerd in de Azure-portal en had verwacht de auditlogboeken voor deze acties te zien op de blade `Activity logs > Audit Logs`, maar ik kan ze niet vinden.

 ![Rapportage](./media/troubleshoot-missing-audit-data/01.png)
 
#### <a name="cause"></a>Oorzaak

Acties worden niet direct weergegeven in de activiteitenlogboeken. In de onderstaande tabel ziet u de latentiewaarden voor activiteitenlogboeken. 

| Rapport | &nbsp; | Latentie (P95) | Latentie (P99) |
|--------|--------|---------------|---------------|
| Directorycontrole | &nbsp; | 2 minuten | 5 minuten |
| Aanmeldingsactiviteit | &nbsp; | 2 minuten | 5 minuten | 

#### <a name="resolution"></a>Oplossing

Wacht 15 minuten tot twee uur en kijk of de acties nu wel worden vermeld in het logboek. Als u de vermeldingen na twee uur nog steeds niet ziet, [maakt u een ondersteuningsticket aan](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) en dan gaan we aan de slag met het probleem.

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>Ontbrekende logboeken voor recente aanmeldingen van gebruikers in het azure AD-aanmeldingsactiviteitslogboek

#### <a name="symptoms"></a>Symptomen

Ik heb me onlangs aangemeld bij de Azure-portal en dan zou ik hier eigenlijk vermeldingen voor moeten zien in de logboeken op de blade `Activity logs > Sign-ins`, maar ik kan ze niet vinden.

 ![Rapportage](./media/troubleshoot-missing-audit-data/02.png)
 
#### <a name="cause"></a>Oorzaak

Acties worden niet direct weergegeven in de activiteitenlogboeken. In de onderstaande tabel ziet u de latentiewaarden voor activiteitenlogboeken. 

| Rapport | &nbsp; | Latentie (P95) | Latentie (P99) |
|--------|--------|---------------|---------------|
| Directorycontrole | &nbsp; | 2 minuten | 5 minuten |
| Aanmeldingsactiviteit | &nbsp; | 2 minuten | 5 minuten | 

#### <a name="resolution"></a>Oplossing

Wacht 15 minuten tot twee uur en kijk of de acties nu wel worden vermeld in het logboek. Als u de vermeldingen na twee uur nog steeds niet ziet, [maakt u een ondersteuningsticket aan](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) en dan gaan we aan de slag met het probleem.

### <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Ik kan niet meer dan 30 dagen aan rapportgegevens bekijken in de Azure-portal

#### <a name="symptoms"></a>Symptomen

Ik kan niet meer dan 30 dagen aan aanmeldings- en controlegegevens bekijken in de Azure-portal. Hoe komt dat? 

 ![Rapportage](./media/troubleshoot-missing-audit-data/03.png)

#### <a name="cause"></a>Oorzaak

Afhankelijk van uw licentie bewaart Azure Active Directory Actions de activiteitenrapporten gedurende de volgende perioden:

| Rapport           | &nbsp; |  Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ----   |  ---           | ---                 | ---                 |
| Directorycontrole  | &nbsp; |   7 dagen     | 30 dagen             | 30 dagen             |
| Aanmeldingsactiviteit | &nbsp; | Niet beschikbaar. U kunt gedurende zeven dagen uw eigen aanmeldingsactiviteit bekijken op de blade met uw individuele gebruikersprofiel. | 30 dagen | 30 dagen             |

Zie [Bewaarbeleid Azure Active Directory-rapporten](reference-reports-data-retention.md) voor meer informatie.  

#### <a name="resolution"></a>Oplossing

U hebt twee opties om de gegevens langer dan 30 dagen te bewaren. U kunt de [API's van de rapportagefunctie van Azure AD](concept-reporting-api.md) gebruiken om de gegevens via programmacode op te halen en op te slaan in een database. Een alternatief is om auditlogboeken te integreren in een SIEM-systeem van derden, zoals Splunk of SumoLogic.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van controlelogboeken](concept-audit-logs.md)
* [Overzicht van aanmeldingen](concept-sign-ins.md)
* [Overzicht van risicovolle gebeurtenissen](concept-risk-events.md)
