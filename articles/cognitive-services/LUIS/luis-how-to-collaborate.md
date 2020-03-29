---
title: Samenwerken met anderen - LUIS
titleSuffix: Azure Cognitive Services
description: Een app-eigenaar kan bijdragers toevoegen aan de ontwerpbron. Deze bijdragers kunnen het model wijzigen, de app trainen en publiceren.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 913a2b26f67773d9fafbc0a8430d121fbabb97cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053444"
---
# <a name="add-contributors-to-your-app"></a>Bijdragers toevoegen aan uw app

Een app-eigenaar kan bijdragers toevoegen aan apps. Deze medewerkers kunnen het model wijzigen, de app trainen en publiceren. 

[!INCLUDE [Manage collaborators and contributors based on migrated or not-migrated apps](./includes/manage-contributor-collaborator-migration.md)]

## <a name="add-contributor-to-azure-authoring-resource"></a>Bijdrage toevoegen aan Azure-ontwerpbron

De volgende procedure is voor alle gebruikers die zijn **gemigreerd** om de Azure-ontwerpbron te gebruiken.

U bent gemigreerd als uw LUIS-ontwerpervaring is gekoppeld aan een ontwerpbron op de pagina **> Azure-bronnen beheren** in de LUIS-portal.

1. Zoek in de Azure-portal de bron voor het schrijven van taalbegrip (LUIS). Het heeft `LUIS.Authoring`het type.
1. Selecteer op de pagina **Toegangsbeheer (IAM)** van deze bron de optie **+Toevoegen** en selecteer **Roltoewijzing toevoegen**.

    ![Voeg in Azure-portal roltoewijzing toe aan het ontwerpen van resources.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. Selecteer in het venster **Roltoewijzing toevoegen** de **rol** van inzender. Selecteer **in** de optie Toegang tot toewijzen de optie **Azure AD-gebruiker, -groep of serviceprincipal**. Voer in de optie **Selecteren** het e-mailadres van de gebruiker in. Als de gebruiker bekend is met meer dan 1 e-mailadres voor hetzelfde domein, moet u ervoor zorgen dat het _primaire_ e-mailaccount wordt ingevoerd.

    ![E-mail van de gebruiker toevoegen aan de rol van bijdragers voor Azure AD](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    Wanneer de e-mail van de gebruiker wordt gevonden, selecteert u het account en selecteert **u Opslaan**. 

    Als u problemen ondervindt met deze roltoewijzing, controleert u [Azure-roltoewijzingen](../../role-based-access-control/role-assignments-portal.md) en [problemen met azure-toegangsbeheer.](../../role-based-access-control/troubleshooting.md#problems-with-azure-role-assignments)

## <a name="add-collaborator-to-luis-app"></a>Medewerker toevoegen aan LUIS-app

De volgende procedure is voor alle gebruikers die niet zijn **gemigreerd** om de Azure-ontwerpbron te gebruiken.

U bent niet gemigreerd als uw LUIS-ontwerpervaring niet is gekoppeld aan een ontwerpbron op de pagina **> Azure-bronnen beheren** in de LUIS-portal.

Een app heeft één auteur, de eigenaar, maar kan veel bijdragers hebben. Als u bijdragers wilt toestaan uw LUIS-app te bewerken, moet u de e-mail die ze gebruiken toevoegen om toegang te krijgen tot de LUIS-portal aan de lijst met bijdragers. Zodra ze zijn toegevoegd, de app wordt weergegeven in hun LUIS portal.

1. Selecteer **Beheren** in het menu rechtsboven en selecteer **Bijdragers** in het linkermenu.

1. Selecteer **Bijdragers toevoegen** op de werkbalk.

1. Voer het e-mailadres in dat de medewerker gebruikt om zich aan te melden bij de LUIS-portal.

    ![Het e-mailadres van de medewerker toevoegen](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)


### <a name="users-with-multiple-emails"></a>Gebruikers met meerdere e-mails 

Als u bijdragers/bijdragers toevoegt aan een LUIS-app, geeft u het exacte e-mailadres op. Hoewel met Azure Active Directory (Azure AD) één gebruiker meer dan één e-mailaccount door elkaar kan laten gebruiken, vereist LUIS dat de gebruiker zich aanmeldt met het e-mailadres dat is opgegeven bij het toevoegen van de bijdrager/medewerker.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Azure Active Directory-bronnen

Als u [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (Azure AD) in uw organisatie gebruikt, heeft Language Understanding (LUIS) toestemming nodig voor de informatie over de toegang van uw gebruikers wanneer ze LUIS willen gebruiken. De resources die LUIS nodig heeft, zijn minimaal. 

U ziet de gedetailleerde beschrijving wanneer u probeert zich aan te melden met een account waarvoor toestemming van de beheerder is vereist of geen toestemming van de beheerder vereist is, zoals toestemming van de beheerder:

* Hiermee u zich aanmelden bij de app met uw organisatieaccount en de app uw profiel laten lezen. Het staat app ook toe om basisbedrijfsinformatie te lezen. Dit geeft LUIS toestemming om basisprofielgegevens te lezen, zoals gebruikersnaam, e-mail, naam
* Hiermee kan de app uw gegevens zien en bijwerken, zelfs als u de app momenteel niet gebruikt. De toestemming is vereist om het toegangstoken van de gebruiker te vernieuwen.


### <a name="azure-active-directory-tenant-user"></a>Azure Active Directory-tenantgebruiker

LUIS maakt gebruik van de standaard azure active directory (Azure AD) toestemmingsstroom. 

De tenantbeheerder moet rechtstreeks samenwerken met de gebruiker die toegang nodig heeft om LUIS in het Azure AD te gebruiken. 

* Eerst meldt de gebruiker zich aan bij LUIS en ziet het pop-updialoogvenster dat beheerdersgoedkeuring nodig heeft. De gebruiker neemt contact op met de tenantbeheerder voordat hij verdergaat. 
* Ten tweede tekent de tenantbeheerder zich bij LUIS en ziet een pop-updialoogvenster van de toestemmingsstroom. Dit is het dialoogvenster dat de beheerder nodig heeft om toestemming te geven aan de gebruiker. Zodra de beheerder de toestemming accepteert, kan de gebruiker doorgaan met LUIS. Als de tenantbeheerder zich niet aanmeldt bij LUIS, heeft de beheerder toegang tot [toestemming](https://account.activedirectory.windowsazure.com/r#/applications) voor LUIS, weergegeven in de volgende schermafbeelding. Let op: de lijst wordt `LUIS`gefilterd op items die de naam bevatten.

![Azure active directory permission by app website Azure active directory permission by app website Azure active directory permission by app website Azure active](./media/luis-how-to-collaborate/tenant-permissions.png)

Als de tenantbeheerder alleen wil dat bepaalde gebruikers LUIS gebruiken, zijn er een paar mogelijke oplossingen:
* Het geven van de "admin toestemming" (toestemming aan alle gebruikers van de Azure AD), maar vervolgens ingesteld op "Ja" de "Gebruiker toewijzing vereist" onder Enterprise Application Eigenschappen, en ten slotte toewijzen / alleen de gewenste gebruikers toe te wijzen / toe te voegen aan de toepassing. Met deze methode verstrekt de beheerder nog steeds "beheerderstoestemming" voor de App, maar het is mogelijk om de gebruikers te bedienen die toegang hebben tot de app.
* Een tweede oplossing is het gebruik van de [Azure AD-api voor identiteits- en toegangsbeheer in Microsoft Graph](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) om toestemming te geven aan elke specifieke gebruiker. 

Meer informatie over Azure active directory users and consent: 
* [Uw app beperken](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) tot een set gebruikers

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het gebruik van versies](luis-how-to-manage-versions.md) om de levenscyclus van uw app te beheren.
* Begrijp de concepten, waaronder de [bron van het ontwerpen](luis-concept-keys.md#authoring-key) en [bijdragers](luis-concept-keys.md#contributions-from-other-authors) op die bron.
* Meer informatie over het maken van [ontwerp-](luis-how-to-azure-subscription.md) en runtime-bronnen
* Migreren naar de nieuwe [bron voor het ontwerpen](luis-migration-authoring.md) 
