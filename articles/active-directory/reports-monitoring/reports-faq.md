---
title: Veelgestelde vragen over Azure Active Directory Reports | Microsoft Documenten
description: Veelgestelde vragen rond Azure Active Directory-rapporten.
services: active-directory
documentationcenter: ''
author: cawrites
manager: MarkusVi
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 273fdb80475defb0576bcd29d1944c5f6c595cfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266505"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Veelgestelde vragen rond Azure Active Directory-rapporten

Dit artikel bevat antwoorden op veelgestelde vragen over Azure Active Directory (Azure AD)-rapportage. Zie [Azure Active Directory-rapportage](overview-reports.md) voor meer informatie. 

## <a name="getting-started"></a>Aan de slag 

**V: Ik gebruik `https://graph.windows.net/<tenant-name>/reports/` momenteel de eindpunt-API's om azure AD-audit en geïntegreerde rapporten over toepassingsgebruik programmatisch in onze rapportagesystemen te plaatsen. Waar moet ik op overschakelen?**

**A:** Zoek de [API-verwijzing](https://developer.microsoft.com/graph/) op om te zien hoe u [de API's gebruiken om toegang te krijgen tot activiteitenrapporten.](concept-reporting-api.md) Dit eindpunt heeft twee rapporten (**Audit** en **Sign-ins)** die alle gegevens die je hebt in het oude API-eindpunt bieden. Dit nieuwe eindpunt bevat ook een aanmeldingsrapport met de Azure AD Premium-licentie die u gebruiken om app-gebruik, apparaatgebruik en aanmeldingsgegevens van gebruikers op te halen.

---

**V: Ik gebruik `https://graph.windows.net/<tenant-name>/reports/` momenteel de endpoint API's om Azure AD-beveiligingsrapporten (specifieke soorten detecties, zoals gelekte referenties of aanmeldingen van anonieme IP-adressen) programmatisch naar onze rapportagesystemen te halen. Waar moet ik op overschakelen?**

**A:** U de  [API voor risicodetecties van identiteitsbeveiliging](../identity-protection/graph-get-started.md)gebruiken om toegang te krijgen tot beveiligingsdetecties via Microsoft Graph. Deze nieuwe indeling biedt meer flexibiliteit in hoe u gegevens opvragen, met geavanceerde filtering, veldselectie en meer, en standaardiseert risicodetecties tot één type voor eenvoudigere integratie in SVM's en andere hulpprogramma's voor het verzamelen van gegevens. Omdat de gegevens een andere indeling hebben, u een nieuwe query niet vervangen door uw oude query's. De [nieuwe API maakt](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent)echter gebruik van Microsoft Graph , de Microsoft-standaard voor API's als O365 of Azure AD. Het benodigde werk kan dus uw huidige Microsoft Graph-investeringen uitbreiden of u helpen uw overgang naar dit nieuwe standaardplatform te beginnen.

---

**V: Hoe krijg ik een premium licentie?**

**A:** Zie [Aan de slag met Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) om uw Azure Active Directory-editie te upgraden.

---

**V: Hoe snel moet ik activiteitengegevens zien nadat ik een premiumlicentie heb verkregen?**

**A:** Als u al activiteitengegevens hebt als een gratis licentie, dan u deze onmiddellijk zien. Als u geen gegevens hebt, duurt het een of twee dagen voordat de gegevens in de rapporten worden weergegeven.

---

**V: Kan ik de gegevens van vorige maand zien nadat ik een Azure AD PREMIUM-licentie heb verkregen?**

**A:** Als u onlangs bent overgestapt op een Premium-versie (inclusief een proefversie), u gegevens in eerste instantie tot 7 dagen zien. Wanneer gegevens zich ophopen, u gegevens van de afgelopen 30 dagen zien.

---

**V: Moet ik een globale beheerder zijn om de aanmeldingen voor activiteiten te zien bij de Azure-portal of om gegevens via de API te krijgen?**

**A:** Nee, u hebt ook toegang tot de rapportagegegevens via de portal of via de API als u een **beveiligingslezer** of **beveiligingsbeheerder** voor de tenant bent. Natuurlijk hebben **globale beheerders** ook toegang tot deze gegevens.

---


## <a name="activity-logs"></a>Activiteitenlogboeken


**V: Wat is het bewaren van gegevens voor activiteitslogboeken (Controle en Aanmelding) in de Azure-portal?** 

**A:** In de volgende tabel worden de bewaarperiode voor gegevens voor activiteitslogboeken weergegeven. Zie beleid [voor het bewaren van gegevens voor Azure AD-rapporten voor](reference-reports-data-retention.md)meer informatie.

| Rapport                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Auditlogboeken             | 7 dagen        | 30 dagen             | 30 dagen             |
| Aanmeldingen               | N.v.t.           | 30 dagen             | 30 dagen             |
| Azure MFA-gebruik        | 30 dagen       | 30 dagen             | 30 dagen             |

---

**V: Hoe lang duurt het voordat ik de activiteitsgegevens kan zien nadat ik mijn taak heb voltooid?**

**A:** Audit logs hebben een latentie variërend van 15 minuten tot een uur. Logboeken voor aanmeldingsactiviteiten kunnen voor sommige records 15 minuten tot maximaal 2 uur duren.

---

**V: Kan ik informatie over office 365-activiteitenlogboeken via de Azure-portal krijgen?**

**A:** Hoewel office 365-activiteits- en Azure-ad-activiteitslogboeken veel directorybronnen delen, wilt u een volledige weergave van de office 365-activiteitslogboeken, moet u naar het [Microsoft 365-beheercentrum](https://admin.microsoft.com) gaan om informatie over office 365-activiteitslogboeken op te halen.

---

**V: Welke API's gebruik ik om informatie te krijgen over Office 365-activiteitslogboeken?**

**A:** Gebruik de [API's voor Office 365-beheer](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview) om toegang te krijgen tot de Office 365-activiteitslogboeken via een API.

---

**V: Hoeveel records kan ik downloaden van azure portal?**

**A:** U maximaal 5000 records downloaden van de Azure-portal. De records worden gesorteerd op *de meest recente* en standaard, krijg je de meest recente 5000 records.

---

## <a name="risky-sign-ins"></a>Riskante aanmeldingen

**V: Er is een risicodetectie in Identiteitsbescherming, maar ik zie geen bijbehorende aanmelding in het aanmeldingsrapport. Wordt dit verwacht?**

**A:** Ja, Identiteitsbescherming evalueert het risico voor alle verificatiestromen, interactief of niet-interactief. In alle aanmeldingen worden echter alleen de interactieve aanmeldingen weergegeven.

---

**V: Hoe weet ik waarom een aanmelding of gebruiker riskant is gemarkeerd in de Azure-portal?**

**A:** Als u een **Azure AD Premium-abonnement** hebt, u meer informatie vinden over de onderliggende risicodetecties door de gebruiker te selecteren in **Gebruikers die zijn gemarkeerd voor risico's** of door een record te selecteren in het rapport **Riskante aanmeldingen.** Als u een **gratis** of **Basic-abonnement hebt,** u de gebruikers met risico- en riskante aanmeldingsrapporten bekijken, maar u de onderliggende informatie over risicodetectie niet zien.

---

**V: Hoe worden IP-adressen berekend in het aanmeldingen en risicovolle aanmeldingsrapport?**

**A:** IP-adressen worden zodanig uitgegeven dat er geen definitief verband bestaat tussen een IP-adres en dat de computer met dat adres zich fysiek bevindt. Het in kaart brengen van IP-adressen wordt verder bemoeilijkt door factoren zoals mobiele providers en VPN's die IP-adressen uitgeven uit centrale pools vaak ver van de plaats waar het clientapparaat daadwerkelijk wordt gebruikt. Momenteel in Azure AD-rapporten is het converteren van IP-adres naar een fysieke locatie een beste inspanning op basis van traces, registergegevens, reverse look ups en andere informatie. 

---

**V: Wat betekent de risicodetectie "Sign-in with additional risk tected"?**

**A:** Als u u inzicht wilt geven in alle riskante aanmeldingen in uw omgeving, fungeert u 'Aanmelden met extra risico's gedetecteerd' als tijdelijke aanduiding voor aanmeldingen voor detecties die exclusief zijn voor azure AD-identiteitsbeveiliging-abonnees.

---

## <a name="conditional-access"></a>Voorwaardelijke toegang

**V: Wat is er nieuw met deze functie?**

**A:** Klanten kunnen nu probleemvoorwaardenbeleid oplossen via alle aanmeldingen. Klanten kunnen de status Voorwaardelijke toegang bekijken en ingaan op de details van het beleid dat van toepassing is op de aanmelding en het resultaat voor elk beleid.

**V: Hoe ga ik aan de slag?**

**A:** Om aan de slag te gaan:

* Navigeer naar het aanmeldingsrapport in de [Azure-portal.](https://portal.azure.com)
* Klik op de aanmelding die u wilt oplossen.
* Navigeer naar het tabblad **Voorwaardelijke toegang.** Hier u alle beleidsregels bekijken die van invloed zijn geweest op de aanmelding en het resultaat voor elk beleid. 
    
**V: Wat zijn alle mogelijke waarden voor de status Voorwaardelijke toegang?**

**A:** De status Voorwaardelijke toegang kan de volgende waarden hebben:

* **Niet toegepast:** dit betekent dat er geen CA-beleid was met de gebruiker en app in het bereik. 
* **Succes**: Dit betekent dat er een CA-beleid was met de gebruiker en app in het bereik en dat het CA-beleid met succes is voldaan. 
* **Fout:** dit betekent dat er een CA-beleid was met de gebruiker en de app in het bereik en dat er niet is voldaan aan het CA-beleid. 
    
**V: Wat zijn alle mogelijke waarden voor het resultaat van het beleid voor voorwaardelijke toegang?**

**A:** Een beleid voor voorwaardelijke toegang kan de volgende resultaten hebben:

* **Succes**: Het beleid is met succes bevredigd.
* **Falen**: Het beleid was niet voldaan.
* **Niet toegepast**: Dit kan zijn omdat de polisvoorwaarden niet voldeden.
* **Niet ingeschakeld**: dit is te wijten aan het beleid in uitgeschakelde staat. 
    
**V: De beleidsnaam in het alle aanmeldingsrapport komt niet overeen met de beleidsnaam in CA. Waarom?**

**A:** De beleidsnaam in het alle aanmeldingsrapport is gebaseerd op de naam van het CA-beleid op het moment van de aanmelding. Dit kan in strijd zijn met de beleidsnaam in CA als u de beleidsnaam later hebt bijgewerkt, dat wil zeggen na de aanmelding.

**V: Mijn aanmelding is geblokkeerd vanwege een beleid voor voorwaardelijke toegang, maar uit het rapport met aanmeldingsactiviteiten blijkt dat de aanmelding is geslaagd. Waarom?**

**A:** Op dit moment worden in het aanmeldingsrapport mogelijk geen nauwkeurige resultaten weergegeven voor Exchange ActiveSync-scenario's wanneer voorwaardelijke toegang wordt toegepast. Er kunnen gevallen zijn waarin het aanmeldingsresultaat in het rapport een geslaagde aanmelding weergeeft, maar de aanmelding is eigenlijk mislukt vanwege een beleid voor voorwaardelijke toegang. 
