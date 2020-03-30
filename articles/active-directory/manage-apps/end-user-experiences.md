---
title: Ervaringen van eindgebruikers voor toepassingen - Azure Active Directory
description: Azure Active Directory (Azure AD) biedt verschillende aanpasbare manieren om toepassingen te implementeren voor eindgebruikers in uw organisatie.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72b3e37a423442194b81e3d10ecc7157201ff8be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266622"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Ervaringen van eindgebruikers voor toepassingen in Azure Active Directory

Azure Active Directory (Azure AD) biedt verschillende aanpasbare manieren om toepassingen te implementeren voor eindgebruikers in uw organisatie:

* Azure AD-toegangspaneel
* Startpictogram voor Office 365-toepassingen
* Directe aanmelding bij federatieve apps
* Dieptekoppelingen naar federatieve apps, op basis van wachtwoorden, of bestaande apps

Welke methode(s) u kiest om te implementeren in uw organisatie is uw discretie.

## <a name="azure-ad-access-panel"></a>Azure AD-toegangspaneel

Het Access https://myapps.microsoft.com Panel op is een web-based portal waarmee een eindgebruiker met een organisatie-account in Azure Active Directory om te bekijken en te starten toepassingen waarvoor ze toegang hebben gekregen door de Azure AD-beheerder. Als u een eindgebruiker bent met [Azure Active Directory Premium,](https://azure.microsoft.com/pricing/details/active-directory/)u ook gebruikmaken van selfservicegroepsbeheermogelijkheden via het Access-paneel.

![Schermafbeelding van de Azure AD Access Panel-portal](media/what-is-single-sign-on/azure-ad-access-panel.png)

Standaard worden alle toepassingen samen op één pagina weergegeven. Maar u verzamelingen gebruiken om gerelateerde toepassingen samen te groeperen en op een apart tabblad te presenteren, waardoor ze gemakkelijker te vinden zijn. U bijvoorbeeld verzamelingen gebruiken om logische groeperingen van toepassingen te maken voor specifieke taken, taken, projecten, enzovoort. Zie Mijn [apps-verzamelingen gebruiken om gebruikerstoegangspanelen aan te passen](access-panel-collections.md)voor meer informatie. 

Het Access-paneel staat los van de Azure-portal en vereist niet dat gebruikers een Azure-abonnement of Office 365-abonnement hebben.

Zie de [inleiding tot het toegangspaneel voor](../user-help/active-directory-saas-access-panel-introduction.md)meer informatie in het access-paneel van Azure.

## <a name="office-365-application-launcher"></a>Startpictogram voor Office 365-toepassingen

Voor organisaties die Office 365 hebben geïmplementeerd, worden toepassingen die via Azure AD aan [https://portal.office.com/myapps](https://portal.office.com/myapps)gebruikers zijn toegewezen, ook weergegeven in de Office 365-portal op . Dit maakt het gemakkelijk en handig voor gebruikers in een organisatie om hun apps te starten zonder een tweede portal te hoeven gebruiken, en is de aanbevolen oplossing voor het starten van apps voor organisaties die Office 365 gebruiken.

![Schermafbeelding van de Office 365-portal](./media/end-user-experiences/microsoft-365-portal-office-com.png)

Zie [Uw app weergeven in het startprogramma voor Office 365-apps](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher)voor meer informatie over het startprogramma voor Office 365-toepassingen.

## <a name="direct-sign-on-to-federated-apps"></a>Directe aanmelding bij federatieve apps

De meeste federatieve toepassingen die SAML 2.0, WS-Federation of OpenID Connect ondersteunen, ondersteunen ook de mogelijkheid voor gebruikers om bij de toepassing te beginnen en vervolgens aangemeld te worden via Azure AD door automatische omleiding of door op een koppeling te klikken om in te loggen. Dit staat bekend als serviceprovider -geïnitieerde aanmelding en de meeste federatieve toepassingen in de Azure AD-toepassingsgalerie ondersteunen dit (zie de documentatie die is gekoppeld aan de wizard single sign-on configuratie van de app in de Azure-portal voor meer informatie).

![Voorbeeld van een aanmeldingspagina voor mobiele apps](./media/end-user-experiences/workdaymobile.png)

## <a name="direct-sign-on-links"></a>Directe aanmeldingskoppelingen

Azure AD ondersteunt ook directe single sign-on links naar afzonderlijke toepassingen die ondersteuning bieden voor eenmalige aanmelding op basis van wachtwoorden, gekoppelde eenmalige aanmelding en elke vorm van gefedereerde enkele aanmelding.

Deze koppelingen zijn speciaal vervaardigde URL's die een gebruiker via het Azure AD-aanmeldingsproces voor een specifieke toepassing verzenden zonder dat de gebruiker deze moet starten vanuit het Azure AD-toegangspaneel of Office 365. Deze **URL's voor gebruikerstoegang** zijn te vinden onder de eigenschappen van beschikbare bedrijfstoepassingen. Selecteer Azure Active Directory > **Enterprise-toepassingen**in de **Azure-portal**. Selecteer de toepassing en selecteer **Eigenschappen**.

![Voorbeeld van de URL van gebruikerstoegang in Twitter-eigenschappen](media/end-user-experiences/direct-sign-on-link.png)

Deze koppelingen kunnen overal worden gekopieerd en geplakt waar u een aanmeldingskoppeling naar de geselecteerde toepassing wilt geven. Dit kan in een e-mail zijn, of in een aangepaste webportal die u hebt ingesteld voor toegang tot gebruikerstoepassingen. Hier is een voorbeeld van een Azure AD direct single sign-on URL voor Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Net als bij organisatiespecifieke URL's voor het toegangspaneel u deze URL verder aanpassen door een van de actieve of geverifieerde domeinen voor uw map toe te voegen na het myapps.microsoft.com-domein. Dit zorgt ervoor dat elke organisatiebranding onmiddellijk op de aanmeldingspagina wordt geladen zonder dat de gebruiker eerst zijn gebruikersnaam hoeft in te voeren:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Wanneer een geautoriseerde gebruiker op een van deze toepassingsspecifieke koppelingen klikt, ziet hij eerst de aanmeldingspagina voor de organisatie (ervan uitgaande dat deze nog niet zijn aangemeld) en worden ze na aanmelding eerst doorgestuurd naar zijn of haar app zonder te stoppen bij het toegangspaneel. Als de gebruiker de vereisten mist om toegang te krijgen tot de toepassing, zoals de op een wachtwoord gebaseerde browserextensie met één teken, wordt de koppeling door de gebruiker gevraagd de ontbrekende extensie te installeren. De link-URL blijft ook constant als de enkele aanmeldingsconfiguratie voor de toepassing verandert.

Deze koppelingen gebruiken dezelfde toegangscontrolemechanismen als het toegangspaneel en Office 365, en alleen gebruikers of groepen die zijn toegewezen aan de toepassing in de Azure-portal, kunnen zich met succes verifiëren. Elke gebruiker die onbevoegd is, ziet echter een bericht waarin wordt uitgelegd dat hij geen toegang heeft gekregen en krijgt een link om het toegangspaneel te laden om beschikbare toepassingen te bekijken waarvoor hij wel toegang heeft.

## <a name="next-steps"></a>Volgende stappen

Zie Azure [Active Directory-implementatieplannen](../fundamentals/active-directory-deployment-plans.md) voor implementatieplannen voor implementatieplannen voor implementatieplannen voor implementatie
