---
title: Ervaringen van eind gebruikers voor toepassingen-Azure Active Directory
description: Azure Active Directory (Azure AD) biedt verschillende aanpas bare manieren om toepassingen te implementeren voor eind gebruikers in uw organisatie.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: kenwith
ms.reviewer: arvindh
ms.openlocfilehash: d3b7dbbb9aab29e083795025ad5bf7381a4fa0ee
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89231211"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Ervaringen van eind gebruikers voor toepassingen in Azure Active Directory

Azure Active Directory (Azure AD) biedt verschillende aanpas bare manieren om toepassingen te implementeren voor eind gebruikers in uw organisatie:

* Mijn apps in azure AD
* Office 365-start programma voor toepassingen
* Directe aanmelding bij federatieve apps
* Dieptekoppelingen naar federatieve apps, op basis van wachtwoorden, of bestaande apps

Welke methode (n) u wilt implementeren in uw organisatie is uw keuze.

## <a name="azure-ad-my-apps"></a>Mijn apps in azure AD

Mijn apps op https://myapps.microsoft.com is een webgebaseerde Portal waarmee een eind gebruiker met een organisatie account in azure Active Directory toepassingen kunnen weer geven en starten waartoe ze toegang hebben gekregen door de Azure AD-beheerder. Als u een eind gebruiker bent met [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), kunt u ook gebruikmaken van self-service voor groeps beheer via mijn apps.

Standaard worden alle toepassingen op één pagina weer gegeven. U kunt echter verzamelingen gebruiken om gerelateerde toepassingen te groeperen en deze op een afzonderlijk tabblad te presen teren, zodat u ze eenvoudiger kunt vinden. U kunt bijvoorbeeld verzamelingen gebruiken om logische groeperingen van toepassingen te maken voor specifieke taak rollen, taken, projecten, enzovoort. Zie [verzamelingen maken in de portal mijn apps](access-panel-collections.md)voor meer informatie. 

Mijn apps staat los van de Azure Portal en vereist niet dat gebruikers een Azure-abonnement of een Office 365-abonnement hebben.

Zie [Inleiding tot mijn apps](../user-help/active-directory-saas-access-panel-introduction.md)voor meer informatie over Azure AD-apps.

## <a name="office-365-application-launcher"></a>Office 365-start programma voor toepassingen

Voor organisaties die Office 365 hebben geïmplementeerd, worden toepassingen die zijn toegewezen aan gebruikers via Azure AD ook weer gegeven in de Office 365-Portal op [https://portal.office.com/myapps](https://portal.office.com/myapps) . Dit maakt het eenvoudig en handig voor gebruikers in een organisatie om hun apps te starten zonder dat ze een tweede Portal hoeven te gebruiken en is de aanbevolen oplossing voor het starten van apps voor organisaties die Office 365 gebruiken.

Zie [uw app weer geven in het start programma voor apps van office 365](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher)voor meer informatie over het start programma voor Office 365-toepassingen.

## <a name="direct-sign-on-to-federated-apps"></a>Directe aanmelding bij federatieve apps

De meeste federatieve toepassingen die ondersteuning bieden voor SAML 2,0, WS-Federation of OpenID Connect Connect bieden ook ondersteuning voor de mogelijkheid van gebruikers om te beginnen bij de toepassing. vervolgens wordt u via automatische omleiding aangemeld via Azure AD of door op een koppeling te klikken om u aan te melden. Dit wordt aangeduid als een door de service provider geïnitieerde aanmelding en de meeste federatieve toepassingen in de Azure AD-toepassings galerie ondersteunen dit (Zie de documentatie die is gekoppeld aan de configuratie wizard voor eenmalige aanmelding van de app in de Azure Portal voor meer informatie).

## <a name="direct-sign-on-links"></a>Directe aanmeldings koppelingen

Azure AD biedt ook ondersteuning voor directe eenmalige aanmelding voor afzonderlijke toepassingen die ondersteuning bieden voor eenmalige aanmelding op basis van wacht woorden, een gekoppelde eenmalige aanmelding en een vorm van federatieve eenmalige aanmelding.

Deze koppelingen zijn speciaal ontworpen Url's die een gebruiker verzenden via het aanmeldings proces van Azure AD voor een specifieke toepassing, zonder dat de gebruiker ze hoeft te starten vanuit Azure AD mijn apps of Office 365. Deze **gebruikers toegang url's** zijn te vinden onder de eigenschappen van beschik bare zakelijke toepassingen. Selecteer **Azure Active Directory** > **Bedrijfstoepassingen** in de Azure Portal. Selecteer de toepassing en selecteer vervolgens **Eigenschappen**.

![Voor beeld van de URL voor gebruikers toegang in Twitter-eigenschappen](media/end-user-experiences/direct-sign-on-link.png)

Deze koppelingen kunnen worden gekopieerd en geplakt, waar u een aanmeldings koppeling naar de geselecteerde toepassing wilt opgeven. Dit kan een e-mail bericht zijn of in een aangepaste portal op het web die u hebt ingesteld voor toegang tot gebruikers toepassingen. Hier volgt een voor beeld van een Azure AD direct-URL voor eenmalige aanmelding voor Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Net als voor de organisatie-specifieke Url's voor mijn apps kunt u deze URL verder aanpassen door een van de actieve of geverifieerde domeinen voor uw map toe te voegen na het *MyApps.Microsoft.com* -domein. Dit zorgt ervoor dat de huis stijl van de organisatie direct op de aanmeldings pagina wordt geladen zonder dat de gebruiker eerst de gebruikers-ID moet invoeren:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Wanneer een geautoriseerde gebruiker op een van deze toepassingsspecifieke koppelingen klikt, zien ze eerst hun organisatorische aanmeldings pagina (ervan uitgaande dat ze nog niet zijn aangemeld), en nadat de aanmelding is omgeleid naar de app zonder eerst op mijn apps te stoppen. Als aan de gebruiker vereisten voor toegang tot de toepassing ontbreken, zoals de browser extensie voor eenmalige aanmelding op basis van een wacht woord, wordt de gebruiker door de koppeling gevraagd de ontbrekende extensie te installeren. De URL van de koppeling blijft ook constant als de configuratie voor eenmalige aanmelding voor de toepassing wordt gewijzigd.

Deze koppelingen gebruiken dezelfde toegangs beheer mechanismen als mijn apps en Office 365, en alleen gebruikers of groepen die zijn toegewezen aan de toepassing in de Azure Portal, kunnen worden geverifieerd. Een gebruiker die niet is gemachtigd, ziet echter een bericht waarin wordt uitgelegd dat ze geen toegang hebben gekregen en een koppeling krijgen om mijn apps te laden om beschik bare toepassingen weer te geven waarvoor ze toegang hebben.

## <a name="next-steps"></a>Volgende stappen

* [Quickstartreeks over toepassingsbeheer](view-applications-portal.md)
* [Wat is eenmalige aanmelding?](what-is-single-sign-on.md)
* [Hand leiding voor het integreren van Azure Active Directory met toepassingen](plan-an-application-integration.md)
