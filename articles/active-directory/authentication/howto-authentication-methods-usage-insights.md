---
title: Gebruiksmethoden & inzichten - Azure Active Directory
description: Rapportage over azure AD self-service password reset en Multi-Factor Authentication authentication method usage
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 053b052abf6f23c385dc7447639aa40b6c2c58a1
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680191"
---
# <a name="authentication-methods-usage--insights-preview"></a>Gebruiksmethoden voor verificatiemethoden & inzichten (voorbeeld)

Met &-inzichten u begrijpen hoe verificatiemethoden voor functies zoals Azure Multi-Factor Authentication en selfservicewachtwoordreset werken in uw organisatie. Deze rapportagemogelijkheid biedt uw organisatie de middelen om te begrijpen welke methoden worden geregistreerd en hoe ze worden gebruikt.

## <a name="permissions-and-licenses"></a>Machtigingen en licenties

De volgende rollen hebben toegang tot gebruik en inzichten:

- Globale beheerder
- Beveiligingslezer
- Beveiligingsbeheer
- Rapporten Reader

Er is geen extra licentie vereist om toegang te krijgen tot gebruik en inzichten. Azure Multi-Factor Authentication en self-service password reset (SSPR) licentiegegevens zijn te vinden op de [Azure Active Directory prijssite.](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="how-it-works"></a>Hoe werkt het?

Ga als u toegang krijgen tot het gebruik en de inzichten van verificatiemethoden:

1. Blader naar [Azure Portal](https://portal.azure.com).
1. Blader naar **Azure Active Directory** > **Password reset** > **Usage & insights.**
1. In de overzichten **Registratie** of **Gebruik** u ervoor kiezen om de vooraf gefilterde rapporten te openen om te filteren op basis van uw behoeften.

![Overzicht van gebruik& inzichten](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

Ga & naar . [https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) Via deze link vind je het inschrijfoverzicht.

De tegels die zijn geregistreerd, gebruikers zijn ingeschakeld en tegels met gebruikers die geschikt zijn voor uw gebruikers:

- Geregistreerd: een gebruiker wordt als geregistreerd beschouwd als deze (of een beheerder) voldoende verificatiemethoden heeft geregistreerd om te voldoen aan het SSPR- of Multifactorauthenticatiebeleid van uw organisatie.
- Ingeschakeld: een gebruiker wordt als ingeschakeld beschouwd als deze geschikt is voor het SSPR-beleid. Als SSPR is ingeschakeld voor een groep, wordt de gebruiker als ingeschakeld beschouwd als deze zich in die groep bevindt. Als SSPR is ingeschakeld voor alle gebruikers, worden alle gebruikers in de tenant (met uitzondering van gasten) als ingeschakeld beschouwd.
- Staat: Een gebruiker wordt geschikt geacht als ze zowel geregistreerd als ingeschakeld zijn. Deze status betekent dat ze sspr op elk gewenst moment kunnen uitvoeren.

Als u op een van deze tegels klikt of op de inzichten die erin worden weergegeven, u een vooraf gefilterde lijst met registratiegegevens krijgen.

In de grafiek **Registraties** op het tabblad **Registratie** wordt het aantal registraties van de geslaagde en mislukte verificatiemethode per verificatiemethode weergegeven. In het diagram **Opnieuw instellingen** op het tabblad **Gebruik** wordt het aantal geslaagde en mislukte verificaties weergegeven tijdens de wachtwoordresetstroom per verificatiemethode.

Als u op een van de grafieken klikt, wordt u naar een vooraf gefilterde lijst met registratie- of resetgebeurtenissen gebracht.

Met het besturingselement in de bovenste, rechterhoek u het datumbereik voor de controlegegevens in de grafieken Registraties en Reseten wijzigen tot 24 uur, 7 dagen of 30 dagen.

### <a name="registration-details"></a>Registratiegegevens

Als u klikt op de **geregistreerde gebruikers,** **gebruikers ingeschakeld**of tegels of inzichten die geschikt zijn **voor gebruikers,** brengt u naar de registratiegegevens.

In het rapport registratiegegevens worden de volgende gegevens voor elke gebruiker weergegeven:

- Naam
- Gebruikersnaam
- Registratiestatus (Alles, Geregistreerd, Niet geregistreerd)
- Ingeschakelde status (Alles, Ingeschakeld, Niet ingeschakeld)
- Capabele status (All, Capable, Not capable)
- Methoden (app-melding, app-code, telefoongesprek, sms, e-mail, beveiligingsvragen)

Met behulp van de besturingselementen boven aan de lijst u zoeken naar een gebruiker en de lijst met gebruikers filteren op basis van de getoonde kolommen.

### <a name="reset-details"></a>Details opnieuw instellen

Als u op de grafieken Registraties of Resets klikt, wordt u bij de details van de reset gegevens gebracht.

In het rapport resetdetails worden registratie- en resetgebeurtenissen van de afgelopen 30 dagen weergegeven, waaronder:

- Naam
- Gebruikersnaam
- Functie (Alles, Registratie, Resetten)
- Verificatiemethode (app-melding, app-code, telefoongesprek, Office-oproep, sms, e-mail, beveiligingsvragen)
- Status (Alles, Succes, Mislukking)

Met behulp van de besturingselementen boven aan de lijst u zoeken naar een gebruiker en de lijst met gebruikers filteren op basis van de getoonde kolommen.

## <a name="limitations"></a>Beperkingen

De gegevens in deze rapporten worden met maximaal 60 minuten vertraagd. Er bestaat een veld 'Laatste vernieuwd' in de Azure-portal om te bepalen hoe recent uw gegevens zijn.

Gegevens over gebruiken en inzichten zijn geen vervanging voor de activiteitsrapporten of -gegevens van Azure Multi-Factor Authentication in het ad-aanmeldingsrapport van Azure.

Rapport kan momenteel niet worden gefilterd om externe gebruikers uit te sluiten.

## <a name="next-steps"></a>Volgende stappen

- [Werken met de API voor gebruiksrapport verificatiemethoden](https://docs.microsoft.com/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Verificatiemethoden voor uw organisatie kiezen](concept-authentication-methods.md)
- [Gecombineerde registratie-ervaring](concept-registration-mfa-sspr-combined.md)
