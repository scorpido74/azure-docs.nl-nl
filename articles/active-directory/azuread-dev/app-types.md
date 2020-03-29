---
title: Toepassingstypen in v1.0 | Azure
description: Beschrijft de typen apps en scenario's die worden ondersteund door het Azure Active Directory v2.0-eindpunt.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: c290cbf36fd53d5afb5fd805cda896fb6879bb4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154947"
---
# <a name="application-types-in-v10"></a>Toepassingstypen in v1.0

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) ondersteunt verificatie voor verschillende moderne app-architecturen, allemaal op basis van industriestandaardprotocollen OAuth 2.0 of OpenID Connect.

In het volgende diagram worden de scenario's en toepassingstypen geïllustreerd en hoe verschillende componenten kunnen worden toegevoegd:

![App-soorten en scenario’s](./media/authentication-scenarios/application-types-scenarios.png)

Dit zijn de vijf primaire toepassingsscenario's die worden ondersteund door Azure AD:

- **[Spa(SPA)](single-page-application.md)**: een gebruiker moet zich aanmelden bij een toepassing van één pagina die is beveiligd door Azure AD.
- **[Webbrowser naar webtoepassing:](web-app.md)** een gebruiker moet zich aanmelden bij een webtoepassing die is beveiligd door Azure AD.
- **[Native application to web API:](native-app.md)** A native application that runs on a phone, tablet, or PC needs to authenticate a user to get resources from a web API that is secured by Azure AD.
- **[Webtoepassing naar web-API:](web-api.md)** een webtoepassing moet resources ophalen uit een web-API die is beveiligd door Azure AD.
- **[Daemon of servertoepassing naar web-API:](service-to-service.md)** een daemon-toepassing of een servertoepassing zonder webgebruikersinterface moet resources krijgen van een web-API die is beveiligd door Azure AD.

Volg de koppelingen voor meer informatie over elk type app en begrijp de scenario's op hoog niveau voordat u met de code gaat werken. U ook meer te weten komen over de verschillen die u moet weten bij het schrijven van een bepaalde app die werkt met het v1.0-eindpunt of v2.0-eindpunt.

> [!NOTE]
> Het v2.0-eindpunt biedt geen ondersteuning voor alle Azure AD-scenario's en -functies. Als u wilt bepalen of u het v2.0-eindpunt moet gebruiken, leest u over [v2.0-beperkingen](../develop/active-directory-v2-limitations.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

U een van de apps en scenario's ontwikkelen die hier worden beschreven met behulp van verschillende talen en platforms. Ze worden allemaal ondersteund door volledige code monsters beschikbaar in de code monsters gids: [v1.0 code monsters per scenario](sample-v1-code.md) en [v2.0 code monsters per scenario](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json). U de codevoorbeelden ook rechtstreeks downloaden uit de bijbehorende [GitHub-monsterrepositories.](https://github.com/Azure-Samples?q=active-directory)

Bovendien, als uw toepassing een specifiek stuk of segment van een end-to-end scenario nodig heeft, kan die functionaliteit in de meeste gevallen onafhankelijk van elkaar worden toegevoegd. Als u bijvoorbeeld een native toepassing hebt die een web-API aanroept, u eenvoudig een webtoepassing toevoegen die ook de web-API aanroept.

## <a name="app-registration"></a>App-registratie

### <a name="registering-an-app-that-uses-the-azure-ad-v10-endpoint"></a>Een app registreren die het Azure AD v1.0-eindpunt gebruikt

Elke toepassing die verificatie uitbesteedt aan Azure AD, moet worden geregistreerd in een map. Deze stap omvat het vertellen van Azure AD over uw toepassing, inclusief de URL waar deze zich bevindt, de URL om antwoorden na verificatie te verzenden, de URI om uw toepassing te identificeren en meer. Deze informatie is vereist om een aantal belangrijke redenen:

* Azure AD moet met de toepassing communiceren bij het verwerken van aanmelding of het uitwisselen van tokens. De informatie die wordt doorgegeven tussen Azure AD en de toepassing omvat het volgende:
  
  * **Toepassings-ID URI** - De id voor een toepassing. Deze waarde wordt tijdens verificatie naar Azure AD verzonden om aan te geven voor welke toepassing de beller een token wil. Bovendien is deze waarde opgenomen in het token, zodat de toepassing weet dat het het beoogde doel was.
  * **URL van antwoord** beantwoorden en **URI omleiden** - Voor een web-API of webtoepassing is de URL van het antwoord de locatie waar Azure AD het verificatieantwoord verzendt, inclusief een token als de verificatie is geslaagd. Voor een native toepassing is de OMLEIDINGsURI een unieke id waarop Azure AD de gebruikersagent doorverwijst in een OAuth 2.0-aanvraag.
  * **Toepassings-id** - de id voor een toepassing, die wordt gegenereerd door Azure AD wanneer de toepassing wordt geregistreerd. Wanneer u een autorisatiecode of token aanvraagt, worden de toepassings-id en -sleutel tijdens verificatie naar Azure AD verzonden.
  * **Sleutel** : de sleutel die samen met een toepassings-id wordt verzonden wanneer deze wordt geauthenticerd naar Azure AD om een web-API aan te roepen.
* Azure AD moet ervoor zorgen dat de toepassing over de vereiste machtigingen beschikt om toegang te krijgen tot uw directorygegevens, andere toepassingen in uw organisatie, enzovoort.

Voor meer informatie, meer informatie over het [registreren van een app](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="single-tenant-and-multi-tenant-apps"></a>Apps met één tenant en meerdere tenant's

Inrichten wordt duidelijker wanneer u begrijpt dat er twee categorieën toepassingen zijn die kunnen worden ontwikkeld en geïntegreerd met Azure AD:

* **Toepassing met één tenant** - Een enkele tenanttoepassing is bedoeld voor gebruik in één organisatie. Dit zijn meestal line-of-business (LoB) toepassingen geschreven door een enterprise developer. Een enkele tenanttoepassing hoeft slechts toegankelijk te zijn voor gebruikers in één map en dientengevolge hoeft deze slechts in één map te worden ingericht. Deze toepassingen worden meestal geregistreerd door een ontwikkelaar in de organisatie.
* **Multi-tenant applicatie** - Een multi-tenant applicatie is bedoeld voor gebruik in veel organisaties, niet slechts één organisatie. Dit zijn normaliter Software-as-a-Service-toepassingen (SaaS), geschreven door een onafhankelijke softwareleverancier (ISV). Multi-tenant toepassingen moeten worden ingericht in elke directory waar ze zullen worden gebruikt, waarvoor toestemming van de gebruiker of beheerder nodig is om ze te registreren. Het toestemmingsproces begint wanneer een toepassing in de map is geregistreerd en toegang verkrijgt tot de Graph-API of wellicht zelfs een andere web-API. Wanneer een gebruiker of beheerder van een andere organisatie zich aanmeldt om de toepassing te gebruiken, wordt deze weergegeven met een dialoogvenster met de machtigingen die de toepassing vereist. De gebruiker of beheerder kan dan instemmen met de toepassing, die de toepassing toegang geeft tot de vermelde gegevens, en uiteindelijk registreert de toepassing in hun directory. Zie [Overzicht van het Consent Framework](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)voor meer informatie.

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>Aanvullende overwegingen bij het ontwikkelen van apps met één tenant of meerdere tenant

Sommige aanvullende overwegingen doen zich voor bij het ontwikkelen van een multi-tenant toepassing in plaats van een enkele tenant toepassing. Als u uw toepassing bijvoorbeeld beschikbaar stelt aan gebruikers in meerdere mappen, hebt u een mechanisme nodig om te bepalen in welke tenant ze zich bevinden. Eén tenanttoepassing hoeft alleen in een eigen map naar een gebruiker te zoeken, terwijl een toepassing met meerdere tenants een specifieke gebruiker moet identificeren uit alle mappen in Azure AD. Om deze taak uit te voeren, biedt Azure AD een gemeenschappelijk verificatieeindpunt waar elke multi-tenanttoepassing aanmeldingsaanvragen kan leiden in plaats van een tenantspecifiek eindpunt. Dit eindpunt `https://login.microsoftonline.com/common` is voor alle mappen in Azure AD, terwijl `https://login.microsoftonline.com/contoso.onmicrosoft.com`een tenantspecifiek eindpunt mogelijk is . Het algemene eindpunt is vooral belangrijk om rekening mee te houden bij het ontwikkelen van uw toepassing, omdat u de nodige logica nodig hebt om meerdere tenants te verwerken tijdens aanmeldings-, afmeldings- en tokenvalidatie.

Als u momenteel één tenanttoepassing ontwikkelt, maar deze beschikbaar wilt maken voor veel organisaties, u eenvoudig wijzigingen aanbrengen in de toepassing en de configuratie ervan in Azure AD om deze multitenant geschikt te maken. Bovendien gebruikt Azure AD dezelfde ondertekeningssleutel voor alle tokens in alle mappen, of u nu verificatie verstrekt in één tenant of multi-tenanttoepassing.

Elk scenario in dit document bevat een onderafdeling waarin de inrichtingsvereisten worden beschreven. Zie [Toepassingen integreren met Azure Active Directory](../develop/single-and-multi-tenant-apps.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) voor meer informatie over het inrichten van een toepassing in Azure AD en de verschillen tussen toepassingen met één en meerdere tenanttoepassingen voor meer informatie. Lees verder om de algemene toepassingsscenario's in Azure AD te begrijpen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over andere [basisprincipes](v1-authentication-scenarios.md) voor Azure AD-verificatie
