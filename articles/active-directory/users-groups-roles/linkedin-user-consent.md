---
title: LinkedIn-gegevensdelen en -toestemming - Azure Active Directory | Microsoft Documenten
description: Legt uit hoe LinkedIn-integratie gegevens deelt via Microsoft-apps in Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a21c986ccfe96bae5d341e0ba2ee6622d998d076
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025398"
---
# <a name="linkedin-account-connections-data-sharing-and-consent"></a>LinkedIn-accountverbindingen het delen en toestemming van gegevens

U gebruikers in uw Active Directory-organisatie (Azure AD) toestemming geven om hun Microsoft-werk- of schoolaccount te koppelen aan hun LinkedIn-account. Nadat een gebruiker zijn accounts met elkaar verbindt, zijn informatie en hoogtepunten van LinkedIn beschikbaar in sommige Microsoft-apps en -services. Gebruikers kunnen ook verwachten dat hun netwerkervaring op LinkedIn wordt verbeterd en verrijkt met informatie van Microsoft.

Als u LinkedIn-informatie wilt zien in Microsoft-apps en -services, moeten gebruikers toestemming geven om hun eigen Microsoft- en LinkedIn-accounts met elkaar te verbinden. Gebruikers wordt gevraagd hun accounts te verbinden wanneer ze voor het eerst klikken om iemands LinkedIn-informatie op een profielkaart in Outlook, OneDrive of SharePoint Online te zien. LinkedIn-accountverbindingen zijn niet volledig ingeschakeld voor uw gebruikers totdat ze instemmen met de ervaring en om hun accounts te verbinden.

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>Voordelen van het delen van LinkedIn-informatie

Toegang tot LinkedIn-informatie binnen Microsoft-apps en -services maakt het voor uw gebruikers gemakkelijker om verbinding te maken, te engageren en professionele relaties op te bouwen met collega's, klanten en partners binnen en buiten uw organisatie. Nieuwe gebruikers kunnen sneller aan de slag gaan door contact te maken met collega's, er meer over te leren en gemakkelijk toegang te krijgen tot meer informatie. Hier is een voorbeeld van hoe LinkedIn-informatie wordt weergegeven op de profielkaart in Microsoft-apps:

![LinkedIn-integratie in uw organisatie mogelijk maken](./media/linkedin-user-consent/display-example.png)

## <a name="enable-and-announce-linkedin-integration"></a>LinkedIn-integratie inschakelen en aankondigen

U moet een Azure Active Directory-beheerder zijn om de instelling voor uw organisatie te beheren. U het inschakelen voor alle gebruikers, of voor een specifieke set gebruikers.

1. Als u de integratie wilt inschakelen of uitschakelen, voert u de stappen uit in [De integratie van Toestemming voor LinkedIn voor uw Azure AD-organisatie.](linkedin-integration.md)
2. Wanneer u de LinkedIn-integratie in uw organisatie aankondigt, wijst u uw gebruikers naar de veelgestelde vragen over [LinkedIn-informatie in Microsoft-apps en -services.](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381) Het artikel bevat informatie over waar LinkedIn-informatie wordt weergegeven, hoe accounts kunnen worden gekoppeld en meer.

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>Toestemming van gebruikers voor toegang tot gegevens in Microsoft en LinkedIn

Gegevens die vanaf LinkedIn worden geopend, worden niet permanent opgeslagen in Microsoft-services. Gegevens die vanuit Microsoft worden geopend, worden niet permanent opgeslagen met LinkedIn, behalve contactpersonen. Microsoft-contactpersonen worden opgeslagen op LinkedIn totdat gebruikers ze verwijderen, zoals beschreven in [het verwijderen van geïmporteerde contactpersonen van LinkedIn.](https://www.linkedin.com/help/linkedin/answer/43377)

Wanneer gebruikers hun accounts verbinden, zijn informatie en inzichten van LinkedIn beschikbaar in sommige Microsoft-apps, zoals de profielkaart. Gebruikers kunnen ook verwachten dat hun netwerkervaring op LinkedIn wordt verbeterd en verrijkt met informatie van Microsoft.
Wanneer gebruikers in uw organisatie hun LinkedIn- en Microsoft-werk- of schoolaccounts met elkaar verbinden, hebben ze twee opties:

* Geef toestemming voor toegang tot gegevens vanuit beide accounts. Dit betekent dat ze toestemming geven voor hun Microsoft- of werkaccount om toegang te krijgen tot gegevens van hun LinkedIn-account en dat [hun LinkedIn-account toegang heeft tot gegevens van hun Microsoft-werk- of schoolaccount.](https://www.linkedin.com/help/linkedin/answer/84077)
* Geef toestemming om alleen de LinkedIn-gegevens te openen via hun Microsoft-werk- en schoolaccount.

Gebruikers kunnen accounts op elk gewenst moment loskoppelen en machtigingen voor gegevenstoegang verwijderen en [gebruikers kunnen bepalen hoe hun eigen LinkedIn-profiel wordt bekeken,](https://www.linkedin.com/help/linkedin/answer/83)inclusief of hun profiel kan worden bekeken in Microsoft-apps.

### <a name="linkedin-account-data"></a>LinkedIn-accountgegevens

Wanneer u uw Microsoft- en LinkedIn-accounts verbindt, staat u LinkedIn toe de volgende gegevens aan Microsoft te verstrekken:

* Profielgegevens - bevat LinkedIn-identiteit, contactgegevens en de informatie die u met anderen deelt in uw [LinkedIn-profiel](https://www.linkedin.com/help/linkedin/answer/15493).
* Interessesgegevens - omvat interesses op LinkedIn, zoals mensen en onderwerpen die u volgt, cursussengroepen en inhoud die u leuk vindt en deelt.
* Abonnementsgegevens - omvat abonnementen op LinkedIn-toepassingen en -services, samen met bijbehorende gegevens. 
* Verbindingsgegevens - omvat uw [LinkedIn-netwerk,](https://www.linkedin.com/help/linkedin/answer/110) inclusief profielen en contactgegevens van uw 1e graads verbindingen.

Gegevens die vanaf LinkedIn worden geopend, worden niet permanent opgeslagen in Microsoft-services. Zie de [Privacyverklaring](https://privacy.microsoft.com/privacystatement/)van Microsoft voor meer informatie over het gebruik van persoonlijke gegevens door Microsoft.

### <a name="microsoft-work-or-school-account-data"></a>Microsoft-werk- of schoolaccountgegevens

Wanneer u uw Microsoft- en LinkedIn-accounts verbindt, staat u Microsoft toe de volgende gegevens aan LinkedIn te verstrekken:

* Profielgegevens - bevat informatie zoals uw voornaam, achternaam, profielfoto, e-mailadres, manager en mensen die u beheert.
* Agendagegevens - omvat vergaderingen in uw agenda's, hun tijden, locaties en contactgegevens van deelnemers. Informatie over de vergadering, zoals agenda, inhoud of vergadertitel, wordt niet opgenomen in de agendagegevens.
* Interessesgegevens - omvat de interesses die aan uw account zijn gekoppeld, op basis van uw gebruik van Microsoft-services, zoals Cortana en Bing for Business.
* Abonnementsgegevens - omvat abonnementen die door uw organisatie worden aangeboden voor Microsoft-apps en -services, zoals Office 365.
* Contactpersonengegevens - bevat contactlijsten in Outlook, Skype en andere Microsoft-accountservices, inclusief de contactgegevens voor mensen die u vaak communiceert of waarmee u samenwerkt. Contactpersonen worden periodiek geïmporteerd, opgeslagen en gebruikt door LinkedIn, bijvoorbeeld om verbindingen voor te stellen, contacten te helpen organiseren en updates over contactpersonen weer te geven.

Gegevens die vanuit Microsoft worden geopend, worden niet permanent opgeslagen met LinkedIn, behalve contactpersonen. Microsoft-contactpersonen worden opgeslagen op LinkedIn totdat gebruikers ze verwijderen. Meer informatie over [het verwijderen van geïmporteerde contactpersonen van LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Zie het Privacybeleid van [LinkedIn](https://www.linkedin.com/legal/privacy-policy)voor meer informatie over het gebruik van persoonsgegevens door LinkedIn. Voor LinkedIn-services, gegevensoverdracht en -opslag kunnen gegevens van de Europese Unie naar de Verenigde Staten en terug stromen en wordt uw privacy beschermd zoals beschreven in [gegevensoverdrachten van de Europese Unie.](https://www.linkedin.com/help/linkedin/answer/62533)

## <a name="next-steps"></a>Volgende stappen

* [LinkedIn in Microsoft-toepassingen met uw werk- of schoolaccount](https://www.linkedin.com/help/linkedin/answer/84077)
