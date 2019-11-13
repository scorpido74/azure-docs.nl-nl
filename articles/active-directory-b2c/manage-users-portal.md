---
title: Azure AD B2C gebruikers accounts voor consumenten maken & verwijderen in de Azure Portal
description: Meer informatie over het gebruik van de Azure Portal voor het maken en verwijderen van consumenten gebruikers in uw Azure AD B2C Directory.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 259ce2b1881c31c2558539fed34513575d193a48
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961986"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>Gebruik de Azure Portal om consumenten gebruikers te maken en te verwijderen in Azure AD B2C

Er zijn mogelijk scenario's waarin u hand matig consumenten accounts wilt maken in de map Azure Active Directory B2C (Azure AD B2C). Hoewel consumenten accounts in een Azure AD B2C Directory meestal worden gemaakt wanneer gebruikers zich aanmelden om een van uw toepassingen te gebruiken, kunt u ze via een programma maken en met behulp van de Azure Portal. Dit artikel is gericht op de Azure Portal methode voor het maken en verwijderen van gebruikers.

Als u gebruikers wilt toevoegen of verwijderen, moet aan uw account de rol *gebruikers beheerder* of *globale beheerder* zijn toegewezen.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="types-of-user-accounts"></a>Typen gebruikers accounts

Zoals beschreven in [overzicht van gebruikers accounts in azure AD B2C](user-overview.md), zijn er drie typen gebruikers accounts die kunnen worden gemaakt in een Azure AD B2C map:

* Overwerk
* Gast
* Consument

In dit artikel wordt aandacht besteed aan het werken met **consumenten accounts** in de Azure Portal. Zie [gebruikers toevoegen of verwijderen met Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md)voor meer informatie over het maken en verwijderen van accounts voor werk en gast.

## <a name="create-a-consumer-user"></a>Een consument gebruiker maken

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Selecteer het filter **Directory + abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C Tenant bevat.
1. Selecteer in het linkermenu **Azure AD B2C**. U kunt ook **alle services** selecteren en **Azure AD B2C**zoeken en selecteren.
1. Onder **Beheren**, selecteer **Gebruikers**.
1. Selecteer **Nieuwe gebruiker**.
1. Selecteer **Azure AD B2C gebruiker maken**.
1. Kies een **aanmeldings methode** en geef een **e-mail** adres of een **gebruikers naam** op voor de nieuwe gebruiker. De aanmeldings methode die u hier selecteert, moet overeenkomen met de instelling die u hebt opgegeven voor de provider van de *lokale account* -id van uw Azure AD B2C-Tenant (Zie > - **id-providers** **beheren** in uw Azure AD B2C-Tenant).
1. Voer een **naam** in voor de gebruiker. Dit is normaal gesp roken de volledige naam (vermeldings-en achtername) van de gebruiker.
1. Beschrijving U kunt **Aanmelden blok keren** als u de mogelijkheid wilt vertragen dat de gebruiker zich aanmeldt. U kunt aanmelden later inschakelen door het **profiel** van de gebruiker te bewerken in de Azure Portal.
1. Kies **wacht woord automatisch genereren** of **laat mij wacht woord maken**.
1. De **voor naam** en **Achternaam**van de gebruiker opgeven.
1. Selecteer **Maken**.

Tenzij u **Aanmelden blok keren**hebt geselecteerd, kan de gebruiker zich nu aanmelden met de aanmeldings methode (e-mail adres of gebruikers naam) die u hebt opgegeven.

## <a name="delete-a-consumer-user"></a>Een consument gebruiker verwijderen

1. Selecteer in uw Azure AD B2C Directory de optie **gebruikers**en selecteer vervolgens de gebruiker die u wilt verwijderen.
1. Selecteer **verwijderen**en klik vervolgens op **Ja** om de verwijdering te bevestigen.

Zie [een onlangs verwijderde gebruiker herstellen of verwijderen met Azure Active Directory](../active-directory/fundamentals/active-directory-users-restore.md)voor meer informatie over het herstellen van een gebruiker binnen de eerste 30 dagen na het verwijderen of voor het permanent verwijderen van een gebruiker.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure AD B2C: gebruikers migratie](active-directory-b2c-user-migration.md)voor geautomatiseerde scenario's voor gebruikers beheer, bijvoorbeeld om gebruikers te migreren van een andere ID-provider naar uw Azure AD B2C Directory.
