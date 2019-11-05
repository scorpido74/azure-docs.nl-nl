---
title: Acceptatie pakket voorwaardelijke toegang-Azure Active Directory
description: Voorwaardelijke toegang van Azure AD voor toegang tot resources
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: daaced0aae595e839f2b2476562fc5caffb63640
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473937"
---
#  <a name="adopting-azure-ad-conditional-access"></a>Voorwaardelijke toegang voor Azure AD

In een mobiele, wereld wijde Cloud hebben gebruikers vanaf elke locatie toegang tot de resources van uw organisatie via verschillende soorten apparaten en apps. Als gevolg hiervan is het niet langer genoeg om te richten op wie toegang heeft tot een resource. U kunt bepalen wie er toegang heeft en waar de gebruiker zich bevindt en welk apparaat wordt gebruikt en nog veel meer.

Als u dit besturings element wilt opgeven, kunt u met de **voorwaardelijke toegang van Azure Active Directory (AD)** de voor waarden opgeven waaraan gebruikers moeten voldoen om toegang te krijgen tot een toepassing, zoals multi-factor Authentication (MFA). Met behulp van beleid voor voorwaardelijke toegang bepaalt u hoe geautoriseerde gebruikers (gebruikers die toegang hebben tot een Cloud-app) toegang hebben tot Cloud-apps onder specifieke voor waarden. Raadpleeg [Wat is voorwaardelijke toegang in azure Active Directory](overview.md) voor meer informatie.

## <a name="key-benefits"></a>Belangrijkste voordelen

De belangrijkste voor delen van het gebruik van voorwaardelijke toegang voor Azure AD zijn:

* **Verhoog de productiviteit:** Met beleid voor voorwaardelijke toegang (CA) kunt u het punt instellen waarop gebruikers wordt gevraagd MFA te gebruiken, toegang hebben geblokkeerd of verplicht zijn om een vertrouwd apparaat te gebruiken. Zo kunt u bijvoorbeeld beleids regels instellen, zoals het vereisen dat gebruikers alleen voor MFA in een toepassing worden ingesteld wanneer het bedrijfs netwerk wordt uitgeschakeld. Als u MFA-aanvragen reduceert, blijven gebruikers productiever dan wanneer ze zich aanmelden bij een MFA. Daarnaast kunt u met voorwaardelijke toegang van Azure AD beleids regels per gebruiker opgeven en ook app-specifieke beleids regels maken.
* **Risico beheren:** Het inschakelen van beleids regels voor voorwaardelijke toegang biedt u de mogelijkheid om identiteits beveiliging in de cloud te baseren, Toegangs beheer mogelijkheden op basis van Risico's en systeem eigen ondersteuning voor multi-factor Authentication. Als u voorwaardelijke toegang met identiteits beveiliging koppelt, kunt u definiëren wanneer de toegang tot een toepassing wordt geblokkeerd of gegatedd.
* **Naleving en beheer van adressen:** Controleer de toegangs aanvragen en-goed keuringen voor de toepassing en meer informatie over het gebruik van de volledige toepassing met Azure AD omdat deze systeem eigen controle logboeken ondersteunt voor elke aanvraag voor toegang tot toepassingen. Auditing omvat de identiteit van de aanvrager, de aangevraagde datum, de zakelijke reden, de goedkeurings status en de identiteit van de goed keurder. Deze gegevens zijn ook beschikbaar vanuit een API, waardoor het mogelijk is om deze gegevens in een SIEM-systeem (Security Incident and Event monitoring) te importeren.
* **Kosten beheren:** Door toegangs beleid naar Azure AD te verplaatsen, vermindert u de afhankelijkheid van aangepaste of on-premises oplossingen, zoals Active Directory Federation Services (ADFS) voor voorwaardelijke toegang, waardoor de kosten voor de infra structuur worden verminderd.

## <a name="customer-case-studies"></a>Casestudy's van klanten

Ontdek hoe de meeste organisaties gebruikmaken van voorwaardelijke toegang van Azure AD om geautomatiseerde beslissingen over toegangs beheer te definiëren en te implementeren voor toegang tot Cloud-apps op basis van voor waarden. De volgende verhalen laten zien hoe deze klant aan deze behoeften voldoet.

* [**Wipro** verstuurt mobiele productiviteit met micro soft Cloud security tools om klant afspraken te verbeteren.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) Het beleid voor voorwaardelijke toegang in azure AD heeft het bedrijf in staat gesteld om documenten, resources en toepassingen te delen met behulp van vertrouwde externe entiteiten---die hun eigen referenties kunnen gebruiken---met behoud van de controle over de eigen bedrijfs gegevens.
* [**Accenture** beveiligt de overstap naar de cloud met Microsoft Cloud app-beveiliging](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security) Accenture de [App-beheer voor voorwaardelijke toegang](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad) -functie van Cloud app Security evalueert, die gebruikmaakt van Azure Active Directory voorwaardelijke toegang tot de poort toegang tot toepassingen op basis van bepaalde voor waarden. LePenske geeft aan dat deze functie nuttig kan zijn voor het inschakelen van alleen-lezen toegang tot bestanden terwijl het downloaden wordt verboden.
* [ **Aramex** Delivery Limited-wereld wijd logistiek en transport bedrijf maakt met de Cloud verbonden Office met identiteits-en toegangs beheer oplossing](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en). Het garanderen van beveiligde toegang is met name lastig bij de externe werk nemers van Aramex. Het bedrijf past nu voorwaardelijke toegang toe om deze externe werk nemers toegang te geven tot hun SaaS-toepassingen van buiten het netwerk. De regel voor voorwaardelijke toegang bepaalt of Multi-Factor Authentication moet worden afgedwongen, zodat alleen de juiste personen de juiste toegang geven.

Ga voor meer informatie over de ervaringen van klanten en partners in voorwaardelijke toegang van Azure AD naar [de fantastische dingen die mensen doen met Azure](https://azure.microsoft.com/case-studies/?service=active-directory).

## <a name="announcements"></a>Aankondigingen

Azure AD ontvangt voortdurend verbeteringen. Als u op de hoogte wilt blijven van de meest recente ontwikkelingen, raadpleegt u [Wat is er nieuw in azure Active Directory?](../fundamentals/whats-new.md)

Recente blogs van de technische community en micro soft Identity-divisie:

* 24 september 2018, [voorwaardelijke toegang Azure Active Directory in azure Databricks](https://azure.microsoft.com/updates/azure-active-directory-conditional-access-in-azure-databricks/)
* 21 september 2018, [aangepaste besturings elementen voor voorwaardelijke toegang van Azure AD zijn beschikbaar als open bare preview](https://azure.microsoft.com/updates/azure-ad-conditional-access-custom-controls-are-in-public-preview/)
* 21 september 2018, [ondersteuning voor voorwaardelijke toegang van Azure AD voor beperkte toegang met Microsoft Cloud app Security is nu beschikbaar](https://azure.microsoft.com/updates/azure-ad-conditional-access-support-for-limited-access-with-microsoft-cloud-app-security-is-now-available/)
* 21 september 2018, [voorwaardelijke toegang van Azure AD: beheerde browser ondersteuning voor IOS/Android-platforms nu als preview-versie](https://azure.microsoft.com/updates/azure-ad-conditional-access-managed-browser-support-for-ios-android-platforms-now-in-preview/) beschikbaar
* 21 september 2018, [voorwaardelijke toegang tot Azure AD voor land codes is in open bare preview](https://azure.microsoft.com/updates/azure-ad-conditional-access-for-country-codes-is-in-public-preview/)
* 21 september 2018, [Azure AD-gebruiks voorwaarden zijn nu beschikbaar](https://azure.microsoft.com/updates/azure-ad-terms-of-use-now-available/)

## <a name="learning-resources"></a>Trainings materiaal

Volg de onderstaande koppelingen voor een overzicht van de werking van de functies voor voorwaardelijke toegang van Azure AD.

* Meer informatie over '[Wat is voorwaardelijke toegang in azure Active Directory?](overview.md)'
* Weet '[Wat zijn de voor waarden in azure Active Directory voorwaardelijke toegang?](conditions.md)'
* Weet '[Wat is de voor waarde van de locatie in azure Active Directory voorwaardelijke toegang?](location-condition.md)'
* Weet '[Wat zijn toegangs beheer in azure Active Directory voorwaardelijke toegang?](controls.md)'
* Zoek '[Wat is het hulp programma wat als? in azure Active Directory voorwaardelijke toegang? '](what-if-tool.md)
* Volg de [Aanbevolen procedures voor voorwaardelijke toegang in azure Active Directory](best-practices.md)

Raadpleeg ook de volgende koppelingen voor meer informatie over het beveiligen van de toegang tot alle services die zijn geïntegreerd met Azure Active Directory.

* [Configuraties voor identiteits-en toegangs apparaten](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations). Hierin wordt beschreven hoe u beveiligde toegang tot Cloud Services configureert via Enterprise Mobility + Security-producten door een aanbevolen omgeving en configuratie te implementeren, met inbegrip van een vereiste set beleids regels voor voorwaardelijke toegang en gerelateerde mogelijkheden.
* [Azure Active Directory verwijzing naar de instellingen voor voorwaardelijke toegang](technical-reference.md). Ontdekken
   * Welke apps gebruiken voorwaardelijke toegang?
   * Welke services worden ingeschakeld met voorwaardelijke toegang?
* [Schakel Azure Active Directory voorwaardelijke toegang in voor toegang tot beveiligde gebruikers](https://www.youtube.com/watch?v=eLAYBwjCGoA). Bekijk deze video om te zien hoe voorwaardelijke toegang een rol speelt in andere werk belastingen van de Enter prise-en mobiliteits Suite.

### <a name="training-videos"></a>Trainingsvideo's

**Voorwaardelijke toegang in Enterprise Mobility + Security**
   > [!VIDEO https://www.youtube.com/embed/A7IrxAH87wc]

**Voorwaardelijke toegang op basis van een apparaat**
   > [!VIDEO https://www.youtube.com/embed/AdM0zYB-3WQ]

**Azure Active Directory voor voorwaardelijke toegang inschakelen voor toegang tot beveiligde gebruikers**
   > [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

### <a name="online-courses"></a>Onlinecursussen

Raadpleeg de volgende cursussen voor voorwaardelijke toegang en meer op [Pluralsight.com](https://www.pluralsight.com/):

* Pluralsight.com: [ontwerp Identity Management in Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
   * "Deze cursus leidt u door de belangrijkste items die u moet kennen om uw oplossing voor identiteits beheer te ontwerpen met Azure AD." Voorwaardelijke toegang van Azure AD wordt behandeld in de module ' rollen en Access Control met Azure AD gebruiken '.

* Pluralsight.com: [ontwerp verificatie voor Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
   * "In deze cursus wordt uitgelegd hoe u Azure AD gebruikt om al uw vereisten voor Cloud verificatie op te lossen." Voorwaardelijke toegang van Azure AD wordt behandeld in de module verificatie vereisten voor verschillende Scenario's.

* Pluralsight.com: [autorisatie ontwerpen voor Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)
   * "Deze cursus beschik bare autorisatie opties voor Azure en Azure AD." Voorwaardelijke toegang van Azure AD wordt behandeld in de module autorisatie met Azure Resource Manager en Azure AD.

### <a name="books"></a>Boeken

* O'Reilly-de [implementatie van Azure Solutions-Second Edition.](https://www.oreilly.com/library/view/implementing-azure-solutions/9781789343045/b7ead3db-eb1c-4ace-897e-86ee25ea86be.xhtml)
   * "Ga aan de slag met Azure-Services en leer hoe u deze implementeert in uw organisatie. Voorwaardelijke toegang van Azure AD is opgenomen in het hoofd stuk [Deploying and synchronizing Azure Active Directory](https://learning.oreilly.com/library/view/implementing-azure-solutions/9781789343045/02ca8bba-08cf-4691-a7d0-1b96e286e7ea.xhtml).

* Wiley- [Microsoft Azure-infrastructuur services voor architecten: Cloud oplossingen ontwerpen](https://www.wiley.com/Microsoft+Azure+Infrastructure+Services+for+Architects%3A+Designing+Cloud+Solutions-p-9781119596547)
   * "Dit is alles wat u nodig hebt om omgevingen te begrijpen, te evalueren, te implementeren en te onderhouden die gebruikmaken van Microsoft Azure."

## <a name="white-papers"></a>Technische documenten

* Gepubliceerd 18 december 2018, [een flexibele toegangs beheer strategie maken met Azure Active Directory](../authentication/concept-resilient-controls.md)
   * Dit document bevat richt lijnen voor strategieën die een organisatie kan aannemen om flexibiliteit te bieden om het risico van vergren deling tijdens onvoorziene onderbrekingen te verminderen.

* Gepubliceerd 18 september 2018, [resources voor het migreren van toepassingen naar Azure Active Directory](../manage-apps/migration-resources.md)
   * Dit Witboek bevat een lijst met resources waarmee u de toegang tot en verificatie van toepassingen kunt migreren naar Azure Active Directory (Azure AD).

* Gepubliceerd op 12 juli 2018 [Azure-blauwdruk voor beveiliging en naleving: Paas voor het hosten van webtoepassingen voor UK-officiële workloads](../../security/blueprints/ukofficial-paaswa-overview.md)
   * Azure-blauw drukken bestaat uit richt lijnen voor documenten en Automation-sjablonen die Cloud architecturen implementeren om oplossingen te bieden voor scenario's met betrekking tot accreditatie-of nalevings vereisten.

## <a name="guidance-for-it-administrators"></a>Richt lijnen voor IT-beheerders

Meld u aan bij de [Azure Portal](https://portal.azure.com/) als globale beheerder, beveiligings beheerder of beheerder van de voorwaardelijke toegang. Raadpleeg de [machtigingen voor beheerdersrol in azure Active Directory.](../users-groups-roles/directory-assign-admin-roles.md)

Als IT-beheerder kunt u met behulp van [voorwaardelijke toegang van Azure AD](overview.md) vereisen dat gebruikers zich verifiëren met Azure multi-factor Authentication, zich aanmelden vanaf een vertrouwd netwerk of een vertrouwd apparaat.

Hier vindt u nuttige koppelingen om aan de slag te gaan:

* [Aanbevolen procedures voor voorwaardelijke toegang in Azure Active Directory](best-practices.md)
* [Azure AD-toegangs beoordelingen gebruiken voor het beheren van gebruikers die zijn uitgesloten van het beleid voor voorwaardelijke toegang](../governance/conditional-access-exclusion.md)
* [Procedure: de implementatie van voorwaardelijke toegang plannen in Azure Active Directory](plan-conditional-access.md)
* [Quick Start: MFA vereisen voor specifieke apps met voorwaardelijke toegang Azure Active Directory](app-based-mfa.md)
* [Quick Start: vereisen dat gebruiks voorwaarden worden geaccepteerd voor toegang tot Cloud-apps](require-tou.md)
* [Quick Start: toegang blok keren wanneer er een sessie risico wordt gedetecteerd met Azure Active Directory voorwaardelijke toegang](app-sign-in-risk.md)
* [Veelgestelde vragen over voorwaardelijke toegang voor Azure AD](faqs.md)
   * Voor aanvullende vragen kunt u ook het MSDN- [forum](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&sort=relevancedesc&brandIgnore=True&searchTerm=password+reset+azure)bekijken.
   * Als u het antwoord op een probleem niet kunt vinden, zijn onze ondersteunings teams altijd beschikbaar om u verder te helpen. [Neem contact op met micro soft ondersteuning](../authentication/active-directory-passwords-troubleshoot.md#contact-microsoft-support).

### <a name="tutorials"></a>Zelfstudies

* [**Quick Start: MFA vereisen voor specifieke apps met voorwaardelijke toegang Azure Active Directory**](app-based-mfa.md)
   * In deze Quick start ziet u hoe u een beleid voor voorwaardelijke toegang voor Azure AD configureert waarvoor multi-factor Authentication is vereist voor een geselecteerde Cloud-app in uw omgeving.

* [**Quick Start: vereisen dat gebruiks voorwaarden worden geaccepteerd voor toegang tot Cloud-apps**](require-tou.md)
   * In deze Quick start ziet u hoe u een beleid voor voorwaardelijke toegang voor Azure AD configureert waarvoor een gebruiks voorwaarden moeten worden geaccepteerd voor een geselecteerde Cloud-app in uw omgeving.

* [**Quick Start: toegang blok keren wanneer er een sessie risico wordt gedetecteerd met Azure Active Directory voorwaardelijke toegang**](app-sign-in-risk.md)
   * In deze Quick start ziet u hoe u een beleid voor voorwaardelijke toegang kunt configureren dat een aanmelding blokkeert wanneer een geconfigureerd risico niveau voor aanmelden is gedetecteerd.

* [Zelf studie: **een klassiek beleid migreren waarvoor multi-factor Authentication is vereist in de Azure Portal**](policy-migration-mfa.md)
   * In deze zelf studie ziet u hoe u een klassiek beleid migreert waarvoor multi-factor Authentication (MFA) voor een Cloud-app is vereist.

## <a name="end-user-readiness-and-communication"></a>Gereedheid en communicatie voor eind gebruikers

Voorwaardelijke toegang maakt gebruik van andere Azure AD-mogelijkheden die van invloed kunnen zijn op de ervaring van de eind gebruiker. U kunt bijvoorbeeld Azure multi-factor Authentication gebruiken om sterke verificatie voor gebruikers in te scha kelen. In dat geval gebruikt u de sjablonen voor eind gebruikers van Azure MFA.

## <a name="next-steps"></a>Volgende stappen

* Start uw implementatie met de documentatie voor de planning van de [voorwaardelijke toegang](plan-conditional-access.md).
