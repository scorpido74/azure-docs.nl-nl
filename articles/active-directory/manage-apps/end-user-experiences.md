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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c2ec85af2c954c2189f0df1b407a2c40967f096
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763394"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Ervaringen van eind gebruikers voor toepassingen in Azure Active Directory

Azure Active Directory (Azure AD) biedt verschillende aanpas bare manieren om toepassingen te implementeren voor eind gebruikers in uw organisatie:

* Azure AD-toegangs venster
* Office 365-start programma voor toepassingen
* Directe aanmelding bij federatieve apps
* Dieptekoppelingen naar federatieve apps, op basis van wachtwoorden, of bestaande apps

Welke methode (n) u wilt implementeren in uw organisatie is uw keuze.

## <a name="azure-ad-access-panel"></a>Azure AD-toegangs venster

Het toegangs venster op https://myapps.microsoft.com is een webgebaseerde Portal waarmee een eind gebruiker met een organisatie account in azure Active Directory toepassingen kunnen weer geven en starten waartoe ze toegang hebben gekregen door de Azure AD-beheerder. Als u een eind gebruiker bent met [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), kunt u ook gebruikmaken van self-service voor groeps beheer via het toegangs venster.

![Scherm afbeelding toont de Azure AD-toegangs venster Portal](media/what-is-single-sign-on/azure-ad-access-panel.png)

Standaard worden alle toepassingen op één pagina weer gegeven. U kunt echter verzamelingen gebruiken om gerelateerde toepassingen te groeperen en deze op een afzonderlijk tabblad te presen teren, zodat u ze eenvoudiger kunt vinden. U kunt bijvoorbeeld verzamelingen gebruiken om logische groeperingen van toepassingen te maken voor specifieke taak rollen, taken, projecten, enzovoort. Zie [mijn app-verzamelingen gebruiken om deel Vensters voor gebruikers toegang te wijzigen](access-panel-collections.md)voor meer informatie. 

Het toegangs venster is gescheiden van de Azure Portal en vereist niet dat gebruikers een Azure-abonnement of een Office 365-abonnement hebben.

Zie [Inleiding tot het toegangs venster](../user-help/active-directory-saas-access-panel-introduction.md)voor meer informatie over het Azure AD-toegangs venster.

## <a name="office-365-application-launcher"></a>Office 365-start programma voor toepassingen

Voor organisaties die Office 365 hebben geïmplementeerd, worden toepassingen die zijn toegewezen aan gebruikers via Azure AD ook weer gegeven in de Office 365-Portal op [https://portal.office.com/myapps](https://portal.office.com/myapps) . Dit maakt het eenvoudig en handig voor gebruikers in een organisatie om hun apps te starten zonder dat ze een tweede Portal hoeven te gebruiken en is de aanbevolen oplossing voor het starten van apps voor organisaties die Office 365 gebruiken.

![Scherm opname toont de Office 365-Portal](./media/end-user-experiences/microsoft-365-portal-office-com.png)

Zie [uw app weer geven in het start programma voor apps van office 365](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher)voor meer informatie over het start programma voor Office 365-toepassingen.

## <a name="direct-sign-on-to-federated-apps"></a>Directe aanmelding bij federatieve apps

De meeste federatieve toepassingen die ondersteuning bieden voor SAML 2,0, WS-Federation of OpenID Connect Connect bieden ook ondersteuning voor de mogelijkheid van gebruikers om te beginnen bij de toepassing. vervolgens wordt u via automatische omleiding aangemeld via Azure AD of door op een koppeling te klikken om u aan te melden. Dit wordt aangeduid als een door de service provider geïnitieerde aanmelding en de meeste federatieve toepassingen in de Azure AD-toepassings galerie ondersteunen dit (Zie de documentatie die is gekoppeld aan de configuratie wizard voor eenmalige aanmelding van de app in de Azure Portal voor meer informatie).

![Voor beeld van een aanmeldings pagina voor mobiele apps](./media/end-user-experiences/workdaymobile.png)

## <a name="direct-sign-on-links"></a>Directe aanmeldings koppelingen

Azure AD biedt ook ondersteuning voor directe eenmalige aanmelding voor afzonderlijke toepassingen die ondersteuning bieden voor eenmalige aanmelding op basis van wacht woorden, een gekoppelde eenmalige aanmelding en een vorm van federatieve eenmalige aanmelding.

Deze koppelingen zijn speciaal ontworpen Url's die een gebruiker verzenden via het aanmeldings proces van Azure AD voor een specifieke toepassing, zonder dat de gebruiker ze hoeft te starten vanuit het Azure AD-toegangs venster of Office 365. Deze **gebruikers toegang url's** zijn te vinden onder de eigenschappen van beschik bare zakelijke toepassingen. Selecteer **Azure Active Directory** > **Bedrijfstoepassingen** in de Azure Portal. Selecteer de toepassing en selecteer vervolgens **Eigenschappen**.

![Voor beeld van de URL voor gebruikers toegang in Twitter-eigenschappen](media/end-user-experiences/direct-sign-on-link.png)

Deze koppelingen kunnen worden gekopieerd en geplakt, waar u een aanmeldings koppeling naar de geselecteerde toepassing wilt opgeven. Dit kan een e-mail bericht zijn of in een aangepaste portal op het web die u hebt ingesteld voor toegang tot gebruikers toepassingen. Hier volgt een voor beeld van een Azure AD direct-URL voor eenmalige aanmelding voor Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Net als voor specifieke Url's voor het toegangs venster kunt u deze URL verder aanpassen door een van de actieve of geverifieerde domeinen voor uw map toe te voegen na het myapps.microsoft.com-domein. Dit zorgt ervoor dat de huis stijl van de organisatie direct op de aanmeldings pagina wordt geladen zonder dat de gebruiker eerst de gebruikers-ID moet invoeren:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Wanneer een geautoriseerde gebruiker op een van deze toepassingsspecifieke koppelingen klikt, zien ze eerst hun organisatorische aanmeldings pagina (ervan uitgaande dat ze nog niet zijn aangemeld), en nadat de aanmelding is omgeleid naar de app zonder eerst op het toegangs paneel te stoppen. Als aan de gebruiker vereisten voor toegang tot de toepassing ontbreken, zoals de browser extensie voor eenmalige aanmelding op basis van een wacht woord, wordt de gebruiker door de koppeling gevraagd de ontbrekende extensie te installeren. De URL van de koppeling blijft ook constant als de configuratie voor eenmalige aanmelding voor de toepassing wordt gewijzigd.

Deze koppelingen gebruiken dezelfde toegangs beheer mechanismen als het toegangs venster en Office 365, en alleen de gebruikers of groepen die zijn toegewezen aan de toepassing in de Azure Portal, kunnen worden geverifieerd. Een gebruiker die niet is gemachtigd, ziet echter een bericht waarin wordt uitgelegd dat ze geen toegang hebben gekregen en een koppeling krijgen om het toegangs venster te laden om beschik bare toepassingen weer te geven waarvoor ze toegang hebben.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Active Directory implementatie plannen](../fundamentals/active-directory-deployment-plans.md) voor implementatie plannen
