---
title: Toestemming voor toepassingen beheren en toestemmingsaanvragen evalueren - Azure AD
description: Meer informatie over het beheren van toestemmingsverzoeken wanneer de toestemming van de gebruiker is uitgeschakeld of beperkt, en hoe u een aanvraag voor toestemming voor beheerders voor een tenant voor een toepassing evalueren.
services: active-directory
author: psignoret
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/27/2019
ms.author: mimart
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0451fe18629a572c9b49f14924bfa50293f42a2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367844"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>Toestemming voor aanvragen beheren en toestemmingsverzoeken evalueren

Microsoft [raadt aan](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#restrict-user-consent-operations) om toestemming van eindgebruikers voor toepassingen uit te schakelen. Dit zal het besluitvormingsproces centraliseren met het beveiligings- en identiteitsbeheerdersteam van uw organisatie.

Nadat de toestemming van de eindgebruiker is uitgeschakeld of beperkt, zijn er verschillende belangrijke overwegingen om ervoor te zorgen dat uw organisatie veilig blijft, terwijl bedrijfskritieke toepassingen kunnen worden gebruikt. Deze stappen zijn van cruciaal belang om de impact op het ondersteuningsteam en de IT-beheerders van uw organisatie te minimaliseren en tegelijkertijd het gebruik van onbeheerde accounts in toepassingen van derden te voorkomen.

## <a name="process-changes-and-education"></a>Procesveranderingen en onderwijs

 1. Overweeg de [werkstroom voor beheerderstoestemming (voorbeeld)](configure-admin-consent-workflow.md) in te schakelen om gebruikers in staat te stellen beheerdersgoedkeuring rechtstreeks vanaf het toestemmingsscherm aan te vragen.

 2. Zorg ervoor dat alle beheerders inzicht krijgen in het [machtigingen- en toestemmingskader,](../develop/consent-framework.md)hoe de [toestemmingsprompt](../develop/application-consent-experience.md) werkt en hoe ze een aanvraag voor toestemming voor beheerders voor de hele tenant kunnen [evalueren.](#evaluating-a-request-for-tenant-wide-admin-consent)
 3. Controleer de bestaande processen van uw organisatie voor gebruikers om beheerdersgoedkeuring voor een toepassing aan te vragen en indien nodig updates uit te voeren. Als processen worden gewijzigd:
    * Werk de relevante documentatie, monitoring, automatisering, enzovoort.
    * Communiceer proceswijzigingen met alle betrokken gebruikers, ontwikkelaars, ondersteuningsteams en IT-beheerders.

## <a name="auditing-and-monitoring"></a>Controleren en bewaken

1. [Controleer apps en machtigingen in](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#audit-apps-and-consented-permissions) uw organisatie om ervoor te zorgen dat er eerder geen ongerechtvaardigde of verdachte toepassingen zijn verleend.

2. Bekijk de subsidies voor [illegale toestemming in Office 365](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) voor aanvullende best practices en beveiligingen tegen verdachte toepassingen die OAuth-toestemming vragen.

3. Als uw organisatie over de juiste licentie beschikt:

    * Gebruik extra [Functies voor het controleren van OAuth-toepassingen in Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth)
    * Gebruik [Azure Monitor Werkmappen om machtigingen en toestemmingsgerelateerde](../reports-monitoring/howto-use-azure-monitor-workbooks.md) activiteiten te controleren. De map *Consent Insights* biedt een weergave van apps op aantal mislukte toestemmingsverzoeken. Dit kan handig zijn om prioriteit te geven aan toepassingen voor beheerders om te controleren en te beslissen of ze toestemming van de beheerder moeten verlenen.

### <a name="additional-considerations-for-reducing-friction"></a>Aanvullende overwegingen voor het verminderen van frictie

Als u de impact op vertrouwde, bedrijfskritieke toepassingen die al in gebruik zijn, wilt minimaliseren, u overwegen om beheerdersproactief toestemming te verlenen voor toepassingen met een groot aantal subsidies voor toestemming van gebruikers:

1. Maak een inventaris van de apps die al met veel gebruik aan uw organisatie zijn toegevoegd, op basis van aanmeldingslogboeken of activiteiten voor toestemmingverlening. Een [PowerShell-script](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) kan worden gebruikt om toepassingen snel en eenvoudig te ontdekken met een groot aantal toestemmingssubsidies voor gebruikers.

2. Evalueer de belangrijkste toepassingen die nog geen toestemming van de beheerder hebben gekregen.

   > [!IMPORTANT]
   > Evalueer een aanvraag zorgvuldig voordat u toestemming voor beheerders voor de hele tenant verleent, zelfs als veel gebruikers in de organisatie al toestemming hebben gegeven voor zichzelf.

3. Geef voor elke toepassing die is goedgekeurd toestemming voor beheerders voor de hele tenant met behulp van een van de onderstaande methoden.

4. Overweeg voor elke goedgekeurde toepassing de [toegang van gebruikers te beperken.](configure-user-consent.md)

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>Een aanvraag voor toestemming voor beheerders voor de hele tenant evalueren

Het verlenen van toestemming voor beheerders voor de hele tenant is een gevoelige bewerking.  Machtigingen worden verleend namens de hele organisatie en kunnen machtigingen bevatten voor het uitvoeren van zeer bevoorrechte bewerkingen. Bijvoorbeeld rolbeheer, volledige toegang tot alle postvakken of alle sites en volledige gebruikersimitatie.

Voordat u toestemming voor beheerders voor de hele tenant verleent, moet u ervoor zorgen dat u de toepassing en de uitgever van de toepassing vertrouwt voor het toegangsniveau dat u verleent. Als u er niet zeker van bent dat u begrijpt wie de toepassing beheert en waarom de toepassing de machtigingen aanvraagt, *verleent u geen toestemming.*

In de volgende lijst vindt u een aantal aanbevelingen om rekening mee te houden bij de beoordeling van een verzoek om toestemming van de beheerder te verlenen.

* **Inzicht in het [machtigingen- en toestemmingskader](../develop/consent-framework.md) in het Microsoft-identiteitsplatform.**

* **Het verschil tussen [gedelegeerde machtigingen en toepassingsmachtigingen begrijpen](../develop/v2-permissions-and-consent.md#permission-types).**

   Toepassingsmachtigingen geven de toepassing toegang tot de gegevens voor de hele organisatie, zonder enige interactie van de gebruiker. Met gedelegeerde machtigingen kan de toepassing optreden namens een gebruiker die op een bepaald moment is aangemeld bij de toepassing.

* **Begrijp de machtigingen die worden aangevraagd.**

   De door de toepassing gevraagde machtigingen worden vermeld in de [toestemmingsprompt](../develop/application-consent-experience.md). Als u de machtigingstitel uitbreidt, wordt de beschrijving van de toestemming weergegeven. De beschrijving voor toepassingsmachtigingen eindigt over het algemeen in "zonder een aangemelde gebruiker". De beschrijving voor gedelegeerde machtigingen eindigt over het algemeen met 'namens de aangemelde gebruiker'. Machtigingen voor de Microsoft Graph API worden beschreven in [Microsoft Graph Permissions Reference]- raadpleeg de documentatie voor andere API's om de machtigingen te begrijpen die ze blootstellen.

   Als u niet begrijpt dat er toestemming wordt gevraagd, *verleent u geen toestemming.*

* **Begrijpen welke toepassing machtigingen aanvraagt en wie de toepassing heeft gepubliceerd.**

   Wees op uw hoede voor kwaadaardige toepassingen proberen te kijken als andere toepassingen.

   Als u twijfelt aan de legitimiteit van een aanvraag of de uitgever ervan, *geef dan geen toestemming.* Zoek in plaats daarvan aanvullende bevestiging (bijvoorbeeld rechtstreeks bij de uitgever van de toepassing).

* **Controleer of de gevraagde machtigingen zijn afgestemd op de functies die u van de toepassing verwacht.**

   Een toepassing die SharePoint-sitebeheer aanbiedt, heeft bijvoorbeeld gedelegeerde toegang nodig om alle siteverzamelingen te lezen, maar heeft niet noodzakelijkerwijs volledige toegang tot alle postvakken of volledige imitatierechten in de map nodig.

   Als u vermoedt dat de toepassing meer machtigingen aanvraagt dan nodig is, *geef dan geen toestemming.* Neem contact op met de uitgever van de toepassing voor meer informatie.

## <a name="granting-consent-as-an-administrator"></a>Toestemming verlenen als beheerder

### <a name="granting-tenant-wide-admin-consent"></a>Toestemming voor beheerders voor tenant

Zie Toestemming voor beheerders [voor tenant verlenen voor een toepassing](grant-admin-consent.md) voor stapsgewijze instructies voor het verlenen van beheerderstoestemming voor tenant vanuit de Azure-portal, met Azure AD PowerShell of vanuit de toestemmingsprompt zelf.

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>Toestemming verlenen namens een specifieke gebruiker

In plaats van toestemming te verlenen voor de hele organisatie, kan een beheerder ook de [Microsft Graph API](https://docs.microsoft.com/graph/use-the-api) gebruiken om toestemming te verlenen voor gedelegeerde machtigingen namens één gebruiker. Zie [Toegang namens een gebruiker krijgen voor](https://docs.microsoft.com/graph/auth-v2-user)meer informatie.

## <a name="limiting-user-access-to-applications"></a>Gebruikerstoegang tot toepassingen beperken

De toegang van gebruikers tot toepassingen kan nog steeds worden beperkt, zelfs wanneer toestemming voor beheerders voor de hele tenant is verleend. Zie methoden voor het toewijzen van gebruikers [en groepen voor](methods-for-assigning-users-and-groups.md)meer informatie over het vereisen van gebruikerstoewijzing aan een toepassing.

Zie [Azure AD gebruiken voor toepassingstoegangsbeheer voor](what-is-access-management.md)meer informatie over het afhandelen van extra complexe scenario's.

## <a name="next-steps"></a>Volgende stappen

[Vijf stappen om uw identiteitsinfrastructuur te beveiligen](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#before-you-begin-protect-privileged-accounts-with-mfa)

[De werkstroom voor beheerderstoestemming configureren](configure-admin-consent-workflow.md)

[Configureren hoe eindgebruikers toestemming geven voor toepassingen](configure-user-consent.md)

[Machtigingen en toestemming in het Microsoft-identiteitsplatform](../develop/active-directory-v2-scopes.md)

[Azure AD op StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)