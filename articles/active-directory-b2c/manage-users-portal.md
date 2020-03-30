---
title: Azure AD B2C-gebruikersaccounts voor consumenten maken & verwijderen in de Azure-portal
description: Meer informatie over het gebruik van de Azure-portal om consumentengebruikers te maken en te verwijderen in uw Azure AD B2C-map.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c8fad8b5bd2dccba36c0c6888712fa806f3cbfc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187216"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>De Azure-portal gebruiken om consumentengebruikers te maken en te verwijderen in Azure AD B2C

Er zijn mogelijk scenario's waarin u handmatig consumentenaccounts wilt maken in uw Azure Active Directory B2C (Azure AD B2C). Hoewel consumentenaccounts in een Azure AD B2C-map het meest worden gemaakt wanneer gebruikers zich aanmelden om een van uw toepassingen te gebruiken, u ze programmatisch maken en met behulp van de Azure-portal. Dit artikel richt zich op de Azure-portalmethode voor het maken en verwijderen van gebruikers.

Als u gebruikers wilt toevoegen of verwijderen, moet aan uw account de functie *Gebruikersbeheerder* of *Globale beheerder* worden toegewezen.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="types-of-user-accounts"></a>Typen gebruikersaccounts

Zoals beschreven in [Overzicht van gebruikersaccounts in Azure AD B2C,](user-overview.md)zijn er drie typen gebruikersaccounts die kunnen worden gemaakt in een Azure AD B2C-map:

* Werk
* Gast
* Consument

Dit artikel richt zich op het werken met **consumentenaccounts** in de Azure-portal. Zie Gebruikers toevoegen of verwijderen met Azure [Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md)voor informatie over het maken en verwijderen van werk- en gastaccounts.

## <a name="create-a-consumer-user"></a>Een consument maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het **filter Directory + abonnement** in het bovenste menu en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Selecteer Azure AD **B2C**in het linkermenu . Selecteer Ook **Alle services** en zoek naar Azure AD **B2C**en selecteer deze .
1. Onder **Beheren**, selecteer **Gebruikers**.
1. Selecteer **Nieuwe gebruiker**.
1. Selecteer **Azure AD B2C-gebruiker maken**.
1. Kies een **aanmeldingsmethode** en voer een **e-mailadres** of een **gebruikersnaam** in voor de nieuwe gebruiker. De aanmeldingsmethode die u hier selecteert, moet overeenkomen met de instelling die u hebt opgegeven voor de *lokale accountidentiteitsprovider* van uw Azure AD B2C-tenant (zie**Identiteitsproviders** **beheren** > in uw Azure AD B2C-tenant).
1. Voer een **naam** in voor de gebruiker. Dit is meestal de volledige naam (gegeven en achternaam) van de gebruiker.
1. (Optioneel) Je **aanmelden blokkeren** als je de mogelijkheid voor de gebruiker om je aan te melden wilt uitstellen. U aanmelden later inschakelen door het **profiel** van de gebruiker in de Azure-portal te bewerken.
1. Kies **Wachtwoord automatisch genereren** of Laat mij een wachtwoord **maken.**
1. Geef de **voor-** en **achternaam**van de gebruiker op.
1. Selecteer **Maken**.

Tenzij u **Aanmelding blokkeren**hebt geselecteerd, kan de gebruiker zich nu aanmelden met de aanmeldingsmethode (e-mail of gebruikersnaam) die u hebt opgegeven.

## <a name="delete-a-consumer-user"></a>Een consument verwijderen

1. Selecteer in uw Azure AD B2C-map **Gebruikers**en selecteer vervolgens de gebruiker die u wilt verwijderen.
1. Selecteer **Verwijderen**en vervolgens **Ja** om de verwijdering te bevestigen.

Zie [Een onlangs verwijderde gebruiker](../active-directory/fundamentals/active-directory-users-restore.md)herstellen of verwijderen voor meer informatie over het herstellen van een gebruiker binnen de eerste 30 dagen na verwijdering of voor het permanent verwijderen van een gebruiker.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure AD B2C: Gebruikersmigratie](user-migration.md)voor geautomatiseerde gebruikersbeheer, bijvoorbeeld het migreren van gebruikers van een andere identiteitsprovider naar uw Azure AD B2C-map.
