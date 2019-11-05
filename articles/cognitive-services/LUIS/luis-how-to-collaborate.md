---
title: Samen werken met anderen-LUIS
titleSuffix: Azure Cognitive Services
description: Een eigenaar van een app kan mede werkers toevoegen aan de ontwerp bron. Deze inzenders kunnen het model, de training en de publicatie van de App wijzigen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 15c7c6664b0c17d02f1f4e9120c9509fda21e0f4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467526"
---
# <a name="add-contributors-to-your-app"></a>Inzenders toevoegen aan uw app

Een app-eigenaar kan mede werkers toevoegen aan apps. Deze mede werkers kunnen het model, de training en de publicatie van de app, wijzigen. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

[!INCLUDE [Manage collaborators and contributors based on migrated or not-migrated apps](./includes/manage-contributor-collaborator-migration.md)]

## <a name="add-contributor-to-azure-authoring-resource"></a>Inzender toevoegen aan de Azure-ontwerp bron

De volgende procedure geldt voor alle gebruikers die zijn **gemigreerd** om de Azure-ontwerp bron te gebruiken.

U hebt gemigreerd als uw LUIS-ontwerp ervaring is gekoppeld aan een bewerkings resource op de pagina **Manage-> Azure-resources** in de Luis-Portal.

1. Zoek de Language Understanding (LUIS) authoring resource in het Azure Portal. Dit is het type `LUIS.Authoring`.
1. Selecteer op de pagina Access Control van de resource **(IAM)** **+ toevoegen** en selecteer vervolgens **functie toewijzing toevoegen**.

    ![Voeg in Azure Portal roltoewijzing toe voor het ontwerpen van resources.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. Selecteer in het venster **roltoewijzing toevoegen** de **rol** van Inzender. In de optie **toegang toewijzen aan** selecteert u **Azure AD-gebruiker,-groep of Service-Principal**. Voer in de optie **selecteren** het e-mail adres van de gebruiker in. Als de gebruiker bekend is met meer dan één e-mail adres voor hetzelfde domein, moet u ervoor zorgen dat u het _primaire_ e-mail account invoert.

    ![E-mail adres van gebruiker toevoegen aan de rol Inzender voor Azure AD](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    Wanneer het e-mail adres van de gebruiker wordt gevonden, selecteert u het account en selecteert u **Opslaan**. 

    Als u problemen ondervindt met deze roltoewijzing, raadpleegt u [Azure-roltoewijzingen](../../role-based-access-control/role-assignments-portal.md) en [probleem oplossing voor Azure-toegangs beheer](../../role-based-access-control/troubleshooting.md#problems-with-rbac-role-assignments).

## <a name="add-collaborator-to-luis-app"></a>Een samen werker toevoegen aan de LUIS-app

De volgende procedure geldt voor alle gebruikers die **niet zijn gemigreerd** om de Azure-ontwerp bron te gebruiken.

U bent niet gemigreerd als uw LUIS-ontwerp ervaring niet is gekoppeld aan een bewerkings resource op de pagina **Azure-resources beheren** in de Luis >-Portal.

Een app heeft één auteur, de eigenaar, maar kan veel deel nemers hebben. Als u samen werkers wilt toestaan om uw LUIS-app te bewerken, moet u de e-mail toevoegen die wordt gebruikt voor toegang tot de LUIS-Portal naar de lijst met mede werkers. Zodra ze zijn toegevoegd, wordt de app weer gegeven in de LUIS-Portal.

1. Selecteer **beheren** in het menu rechtsboven en selecteer vervolgens deel **nemers** in het linkermenu.

1. Selecteer **samen werker toevoegen** op de werk balk.

    [![Samen werker toevoegen](./media/luis-how-to-collaborate/add-collaborator.png "Samen werker toevoegen")](./media/luis-how-to-collaborate/add-collaborator.png#lightbox)

1. Voer het e-mail adres in dat de samen werker gebruikt om zich aan te melden bij de LUIS-Portal.

    ![E-mail adres van de mede werker toevoegen](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)


### <a name="users-with-multiple-emails"></a>Gebruikers met meerdere e-mail berichten 

Als u inzenders/mede werkers aan een LUIS-app toevoegt, geeft u precies het e-mail adres op. Terwijl Azure Active Directory (Azure AD) toestaat dat één gebruiker meer dan één e-mail account heeft gebruikt, moet de gebruiker zich aanmelden met het e-mail adres dat is opgegeven bij het toevoegen van de mede werker/samen werker.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Azure Active Directory resources

Als u [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (Azure AD) in uw organisatie gebruikt, moet language UNDERSTANDING (Luis) toestemming hebben voor de informatie over de toegang van gebruikers wanneer ze Luis willen gebruiken. De resources die LUIS nodig hebben, zijn mini maal. 

De gedetailleerde beschrijving wordt weer gegeven wanneer u zich probeert aan te melden met een account met beheerders toestemming of geen beheerder toestemming vereist, zoals toestemming van de beheerder:

* Met kunt u zich aanmelden bij de app met uw organisatie account en de app uw profiel laten lezen. Ook kan de app basis informatie over het bedrijf lezen. Dit geeft LUIS-machtiging voor het lezen van basis profiel gegevens, zoals gebruikers-ID, e-mail, naam
* Hiermee kunnen uw gegevens worden weer gegeven en bijgewerkt met de app, zelfs wanneer u de app momenteel niet gebruikt. De machtiging is vereist voor het vernieuwen van het toegangs token van de gebruiker.


### <a name="azure-active-directory-tenant-user"></a>Azure Active Directory Tenant gebruiker

LUIS maakt gebruik van de toestemmings stroom van de standaard Azure Active Directory (Azure AD). 

De Tenant beheerder moet direct samen werken met de gebruiker die toegang nodig heeft om LUIS te gebruiken in azure AD. 

* Eerst meldt de gebruiker zich aan bij LUIS en ziet u het pop-updialoogvenster dat door de beheerder moet worden goedgekeurd. De gebruiker neemt contact op met de Tenant beheerder voordat u doorgaat. 
* Ten tweede meldt de Tenant beheerder zich aan bij LUIS en ziet hij een pop-upvenster met goedkeurings stroom. Dit is het dialoog venster dat de beheerder moet machtigen voor de gebruiker. Zodra de beheerder de machtiging heeft geaccepteerd, kan de gebruiker door gaan met LUIS. Als de Tenant beheerder zich niet bij LUIS aanmeldt, heeft de beheerder toegang tot [toestemming](https://account.activedirectory.windowsazure.com/r#/applications) voor Luis, zoals weer gegeven in de volgende scherm afbeelding. U ziet dat de lijst is gefilterd op items die de naam `LUIS`bevatten.

![Toestemming van Azure Active Directory per app-website](./media/luis-how-to-collaborate/tenant-permissions.png)

Als de Tenant beheerder alleen bepaalde gebruikers in staat wilt stellen om LUIS te gebruiken, zijn er enkele mogelijke oplossingen:
* De "toestemming van de beheerder" (toestemming geven aan alle gebruikers van de Azure AD), maar vervolgens ingesteld op "ja" de "gebruikers toewijzing vereist" onder eigenschappen van bedrijfs toepassing en uiteindelijk alleen de gewenste gebruikers aan de toepassing toewijzen/toevoegen. Met deze methode wordt de beheerder nog steeds ' toestemming van de beheerder ' gegeven aan de app, maar het is mogelijk om de gebruikers te beheren die er toegang toe hebben.
* Een tweede oplossing is het gebruik van [Azure AD Graph API](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) om toestemming te geven aan elke specifieke gebruiker. 

Meer informatie over Azure Active Directory-gebruikers en-toestemming: 
* [Uw app beperken](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) tot een set gebruikers

## <a name="next-steps"></a>Volgende stappen

* Meer informatie [over het gebruik van versies](luis-how-to-manage-versions.md) om de levens cyclus van uw app te beheren.
* Inzicht in de concepten, waaronder de [ontwerp resource](luis-concept-keys.md#authoring-key) en [inzenders](luis-concept-keys.md#contributions-from-other-authors) voor die bron.
* Meer informatie [over het maken](luis-how-to-azure-subscription.md) van ontwerp-en runtime-resources
* Migreren naar de nieuwe [ontwerp bron](luis-migration-authoring.md) 
