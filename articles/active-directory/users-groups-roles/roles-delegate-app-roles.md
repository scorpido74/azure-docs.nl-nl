---
title: Permanenten van toepassingsbeheerbeheer delegeren - Azure AD | Microsoft Documenten
description: Machtigingen verlenen voor beheer van toepassingstoegang in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 251bc1c2277f9e43543f95c49d0b730a5a41c3d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253037"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>Machtigingen voor app-registratie delegeren in Azure Active Directory

In dit artikel wordt beschreven hoe u machtigingen gebruiken die zijn verleend door aangepaste rollen in Azure Active Directory (Azure AD) om tegemoet te komen aan de behoeften van uw toepassingsbeheer. In Azure AD u machtigingen voor het maken en beheren van toepassingen op de volgende manieren delegeren:

- [Beperken wie toepassingen kan maken](#restrict-who-can-create-applications) en de toepassingen die ze maken beheren. Standaard in Azure AD kunnen alle gebruikers toepassingsregistraties registreren en alle aspecten beheren van toepassingen die ze maken. Dit kan worden beperkt tot alleen toestaan dat geselecteerde personen die toestemming.
- [Een of meer eigenaren toewijzen aan een toepassing](#assign-application-owners). Dit is een eenvoudige manier om iemand de mogelijkheid te geven om alle aspecten van Azure AD-configuratie voor een specifieke toepassing te beheren.
- [Het toewijzen van een ingebouwde beheerrol](#assign-built-in-application-admin-roles) die toegang verleent tot het beheren van configuratie in Azure AD voor alle toepassingen. Dit is de aanbevolen manier om IT-experts toegang te verlenen voor het beheren van brede machtigingen voor toepassingsconfiguratie zonder toegang te verlenen tot het beheren van andere delen van Azure AD die niet gerelateerd zijn aan toepassingsconfiguratie.
- [Een aangepaste rol maken](#create-and-assign-a-custom-role-preview) die zeer specifieke machtigingen definieert en aan iemand toewijs aan iemand als een enkele toepassing als beperkte eigenaar, of bij de directoryscope (alle toepassingen) als beperkte beheerder.

Het is belangrijk om te overwegen om toegang te verlenen met behulp van een van de bovenstaande methoden om twee redenen. Ten eerste vermindert het delegeren van de mogelijkheid om administratieve taken uit te voeren de algemene beheerdersoverhead. Ten tweede, het gebruik van beperkte machtigingen verbetert uw beveiligingshouding en vermindert de kans op onbevoegde toegang. Delegatieproblemen en algemene richtlijnen worden besproken in [het beheer van gemachtigde in Azure Active Directory](roles-concept-delegation.md).

## <a name="restrict-who-can-create-applications"></a>Beperken wie toepassingen kan maken

Standaard in Azure AD kunnen alle gebruikers toepassingsregistraties registreren en alle aspecten beheren van toepassingen die ze maken. Iedereen heeft ook de mogelijkheid om namens hen toestemming te geven voor apps die toegang krijgen tot bedrijfsgegevens. U ervoor kiezen deze machtigingen selectief toe te kennen door de globale switches in te stellen op 'Nee' en de geselecteerde gebruikers toe te voegen aan de rol Toepassingsontwikkelaar.

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>De standaardmogelijkheid uitschakelen om toepassingsregistraties of toestemming voor toepassingen te maken

1. Meld u aan bij uw Azure AD-organisatie met een account dat in aanmerking komt voor de rol globale beheerder in uw Azure AD-organisatie.
1. Stel een of beide van de volgende opties in:

    - Stel op de [pagina Gebruikersinstellingen voor uw organisatie](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)de instelling Gebruikers in **op** Nee. Hierdoor wordt de standaardmogelijkheid voor gebruikers om toepassingsregistraties te maken uitgeschakeld.
    - Stel in bij de [gebruikersinstellingen voor bedrijfstoepassingen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)de **gebruikers kunnen instemmen met toepassingen die namens hen toegang krijgen tot bedrijfsgegevens** en instellen op Nr. Hierdoor wordt de standaardmogelijkheid voor gebruikers om namens hen toestemming te geven voor toepassingen die toegang krijgen tot bedrijfsgegevens uitgeschakeld.

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>Individuele machtigingen verlenen om toepassingen te maken en toestemming te geven wanneer de standaardmogelijkheid is uitgeschakeld

Wijs de rol Toepassingsontwikkelaars toe om toepassingsregistraties te maken wanneer de instelling **Toepassingen kunnen registreren** is ingesteld op Nee. Deze rol verleent ook toestemming om namens de gebruiker toestemming te geven wanneer de **gebruikers kunnen instemmen met apps die namens hen toegang hebben tot bedrijfsgegevens.** Als systeemgedrag wordt deze automatisch toegevoegd als de eerste eigenaar wanneer een gebruiker een nieuwe toepassingsregistratie maakt. Eigendomsmachtigingen geven de gebruiker de mogelijkheid om alle aspecten van een toepassingsregistratie of bedrijfstoepassing die hij of zij bezitten, te beheren.

## <a name="assign-application-owners"></a>Toepassingseigenaren toewijzen

Het toewijzen van eigenaren is een eenvoudige manier om alle aspecten van Azure AD-configuratie te beheren voor een specifieke toepassingsregistratie of bedrijfstoepassing. Als systeemgedrag worden deze automatisch toegevoegd als de eerste eigenaar wanneer een gebruiker een nieuwe toepassingsregistratie maakt. Eigendomsmachtigingen geven de gebruiker de mogelijkheid om alle aspecten van een toepassingsregistratie of bedrijfstoepassing die hij of zij bezitten, te beheren. De oorspronkelijke eigenaar kan worden verwijderd en extra eigenaren kunnen worden toegevoegd.

### <a name="enterprise-application-owners"></a>Eigenaren van bedrijfstoepassingen

Als eigenaar kan een gebruiker de organisatiespecifieke configuratie van de bedrijfstoepassing beheren, zoals de enkele aanmeldingsconfiguratie, inrichting en gebruikerstoewijzingen. Een eigenaar kan ook andere eigenaren toevoegen of verwijderen. In tegenstelling tot globale beheerders kunnen eigenaren alleen de bedrijfstoepassingen beheren die ze bezitten.

In sommige gevallen omvatten ondernemingstoepassingen die vanuit de toepassingsgalerie zijn gemaakt, zowel een bedrijfstoepassing als een toepassingsregistratie. Wanneer dit waar is, voegt het toevoegen van een eigenaar aan de bedrijfstoepassing automatisch de eigenaar toe aan de bijbehorende toepassingsregistratie als eigenaar.

### <a name="to-assign-an-owner-to-an-enterprise-application"></a>Een eigenaar toewijzen aan een ondernemingstoepassing

1. Meld u aan bij [uw Azure AD-organisatie](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) met een account dat in aanmerking komt voor de toepassingsbeheerder of cloudtoepassingsbeheerder voor de organisatie.
1. Selecteer op de  [pagina App-registraties](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/)voor de organisatie een app om de pagina Overzicht voor de app te openen.
1. Selecteer **Eigenaren** om de lijst met eigenaren voor de app te bekijken.
1. Selecteer **Toevoegen** om een of meer eigenaren te selecteren die aan de app moeten worden toegevoegd.

> [!IMPORTANT]
> Gebruikers en serviceprincipals kunnen eigenaren zijn van toepassingsregistraties. Alleen gebruikers kunnen eigenaar zijn van bedrijfstoepassingen. Groepen kunnen niet worden toegewezen als eigenaren van beide.
>
> Eigenaren kunnen referenties toevoegen aan een toepassing en deze referenties gebruiken om zich voor te doen als de identiteit van de toepassing. De toepassing kan meer machtigingen dan de eigenaar, en dus zou een verhoging van de bevoegdheid over wat de eigenaar heeft toegang tot als gebruiker of service principal. Een toepassingseigenaar kan mogelijk gebruikers of andere objecten maken of bijwerken terwijl hij zich voordoet als de toepassing, afhankelijk van de machtigingen van de toepassing.

## <a name="assign-built-in-application-admin-roles"></a>Ingebouwde toepassingsbeheerdersrollen toewijzen

Azure AD heeft een reeks ingebouwde beheerdersrollen voor het verlenen van toegang tot het beheren van configuratie in Azure AD voor alle toepassingen. Deze rollen zijn de aanbevolen manier om IT-experts toegang te verlenen voor het beheren van brede toepassingsconfiguratiemachtigingen zonder toegang te verlenen tot het beheren van andere delen van Azure AD die niet gerelateerd zijn aan toepassingsconfiguratie.

- Toepassingsbeheerder: gebruikers in deze rol kunnen alle aspecten van bedrijfstoepassingen, toepassingsregistraties en proxy-instellingen voor toepassingen maken en beheren. Deze rol biedt ook de mogelijkheid om in te stemmen met gedelegeerde machtigingen en toepassingsmachtigingen met uitzondering van Microsoft Graph. Gebruikers die aan deze rol zijn toegewezen, worden niet als eigenaars toegevoegd bij het maken van nieuwe toepassingsregistraties of bedrijfstoepassingen.
- Cloudapplicationbeheerder: gebruikers in deze rol hebben dezelfde machtigingen als de rol Toepassingsbeheerder, met uitzondering van de mogelijkheid om de proxy van toepassingen te beheren. Gebruikers die aan deze rol zijn toegewezen, worden niet als eigenaars toegevoegd bij het maken van nieuwe toepassingsregistraties of bedrijfstoepassingen.

Zie [Beschikbare rollen](directory-assign-admin-roles.md#available-roles)voor meer informatie en om de beschrijving voor deze rollen te bekijken.

Volg de instructies in de [rollen Toewijzen aan gebruikers met de](../fundamentals/active-directory-users-assign-role-azure-portal.md) handleiding voor Azure Active Directory om de rollen toepassingsbeheerder of cloudtoepassingsbeheerder toe te wijzen.

> [!IMPORTANT]
> Toepassingsbeheerders en Cloudtoepassingsbeheerders kunnen referenties toevoegen aan een toepassing en deze referenties gebruiken om zich voor te doen als de identiteit van de toepassing. De toepassing kan machtigingen hebben die een verhoging van de bevoegdheden zijn ten opzichte van de machtigingen van de beheerdersrol. Een beheerder in deze rol kan mogelijk gebruikers of andere objecten maken of bijwerken terwijl hij zich voordoet als de toepassing, afhankelijk van de machtigingen van de toepassing.
> Geen van beide rollen verleent de mogelijkheid om instellingen voor voorwaardelijke toegang te beheren.

## <a name="create-and-assign-a-custom-role-preview"></a>Een aangepaste rol maken en toewijzen (voorbeeld)

Aangepaste rollen maken en aangepaste rollen toewijzen zijn afzonderlijke stappen:

- [Maak een aangepaste *roldefinitie* ](roles-create-custom.md) en [voeg er machtigingen aan toe vanuit een vooraf ingestelde lijst](roles-custom-available-permissions.md). Dit zijn dezelfde machtigingen die worden gebruikt in de ingebouwde rollen.
- [Maak een *roltoewijzing* ](roles-assign-powershell.md) om de aangepaste rol toe te wijzen.

Met deze scheiding u één roldefinitie maken en deze vervolgens vele malen toewijzen op verschillende *scopes.* Een aangepaste rol kan worden toegewezen aan een bereik voor de hele organisatie of worden toegewezen aan het bereik als één Azure AD-object. Een voorbeeld van een objectbereik is één app-registratie. Met behulp van verschillende scopes kan dezelfde roldefinitie aan Sally worden toegewezen voor alle app-registraties in de organisatie en vervolgens aan Naveen over alleen de registratie van de Contoso Expense Reports-app.

Tips bij het maken en gebruiken van aangepaste rollen voor het delegeren van toepassingsbeheer:
- Aangepaste rollen verlenen alleen toegang in de meest recente app-registratieblades van de Azure AD-portal. Ze geven geen toegang in de verouderde app registraties bladen.
- Aangepaste rollen verlenen geen toegang tot de Azure AD-portal wanneer de gebruikersinstelling 'Toegang tot Azure AD-beheerportal beperken' is ingesteld op Ja.
- App-registraties waartoe de gebruiker toegang heeft met roltoewijzingen worden alleen weergegeven op het tabblad 'Alle toepassingen' op de registratiepagina van apps. Ze worden niet weergegeven op het tabblad 'Owned applications'.

Zie het overzicht van [aangepaste rollen,](roles-custom-overview.md)het overzicht van aangepaste rollen en het maken van [een aangepaste rol](roles-create-custom.md) en het toewijzen van [een rol](roles-assign-powershell.md)voor meer informatie over de basisprincipes van aangepaste rollen.

## <a name="next-steps"></a>Volgende stappen

- [Subtypen en machtigingen voor toepassingsregistratie](roles-custom-available-permissions.md)
- [Naslaginformatie over azure AD-ad-beheerdersrol](directory-assign-admin-roles.md)
