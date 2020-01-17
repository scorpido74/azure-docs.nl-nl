---
title: Zwakke wacht woorden in azure AD-Azure Active Directory verbieden
description: Zwakke wacht woorden van uw envirionment met Azure AD dynamisch verboden passwrords
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb47b9df51803c76662b5fb4ca1fe23740e7af9a
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155051"
---
# <a name="configuring-the-custom-banned-password-list"></a>De aangepaste lijst met uitgesloten wacht woorden configureren

Veel organisaties vinden hun gebruikers een wacht woord met behulp van gang bare lokale woorden, zoals een school, een sport team of een beroemde persoon, zodat ze gemakkelijk te raden zijn. Met de aangepaste lijst met geblokkeerde wacht woorden van micro soft kunnen organisaties, naast de globale lijst met geblokkeerde wacht woorden, teken reeksen toevoegen om te evalueren en blok keren, wanneer gebruikers en beheerders een wacht woord proberen te wijzigen of opnieuw in te stellen.

## <a name="add-to-the-custom-list"></a>Toevoegen aan de aangepaste lijst

Voor het configureren van de aangepaste lijst met geblokkeerde wacht woorden is een Azure Active Directory Premium P1 of P2-licentie vereist. Zie de [pagina met Azure Active Directory prijzen](https://azure.microsoft.com/pricing/details/active-directory/)voor meer gedetailleerde informatie over Azure Active Directory-licenties.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en blader **naar Azure Active Directory** > **beveiligings** > **verificatie methoden** > **wachtwoord beveiliging**.
1. Stel de optie **aangepaste lijst afdwingen**in op **Ja**.
1. Teken reeksen toevoegen aan de **lijst met aangepaste verboden wacht woorden**, één teken reeks per regel
   * De aangepaste lijst met verboden wacht woorden kan Maxi maal 1000 voor waarden bevatten.
   * De aangepaste lijst met verboden wacht woorden is hoofdletter gevoelig.
   * De aangepaste lijst met geblokkeerde wacht woorden beschouwt veelvoorkomende teken vervanging.
      * Voor beeld: "o" en "0" of "a" en "\@"
   * De minimale teken reeks lengte is vier tekens en het maximum aantal is 16 tekens.
1. Wanneer u alle teken reeksen hebt toegevoegd, klikt u op **Opslaan**.

> [!NOTE]
> Het kan enkele uren duren voordat updates voor de aangepaste lijst met geblokkeerde wacht woorden worden toegepast.

> [!NOTE]
> De aangepaste lijst met verboden wacht woorden is beperkt tot een maximum van 1000 voor waarden. Het is niet ontworpen voor het blok keren van extreem grote lijsten met wacht woorden. Als u de voor delen van de aangepaste lijst met geblokkeerde wacht woorden volledig wilt benutten, raadt micro soft u aan eerst het beoogde ontwerp en gebruik van de aangepaste lijst met geblokkeerde wacht woorden te bekijken (Zie [aangepaste lijst met geblokkeerde wacht](concept-password-ban-bad.md#custom-banned-password-list)woorden) en ook het algoritme voor wachtwoord evaluatie (Zie [hoe wacht woorden worden geëvalueerd](concept-password-ban-bad.md#how-are-passwords-evaluated)).

![Wijzig de aangepaste lijst met verboden wacht woorden onder verificatie methoden in de Azure Portal](./media/howto-password-ban-bad/authentication-methods-password-protection.png)

## <a name="how-it-works"></a>Het werkt als volgt

Telkens wanneer een gebruiker of beheerder een Azure AD-wacht woord opnieuw instelt of wijzigt, wordt de lijst met verboden wacht woorden door lopen om te bevestigen dat deze zich niet in een lijst bevindt. Deze controle is opgenomen in een wacht woord dat is ingesteld of gewijzigd met behulp van Azure AD.

## <a name="what-do-users-see"></a>Wat gebruikers zien

Wanneer een gebruiker probeert om een wacht woord opnieuw in te stellen op een waarde die verboden zou zijn, zien ze een van de volgende fout berichten:

* Uw wacht woord bevat helaas een woord, woord groep of patroon waarmee uw wacht woord gemakkelijk kan worden geraden. Probeer het opnieuw met een ander wacht woord.
* Helaas kunt u dit wacht woord niet gebruiken omdat het woorden of tekens bevat die door uw beheerder zijn geblokkeerd. Probeer het opnieuw met een ander wacht woord.

## <a name="next-steps"></a>Volgende stappen

[Conceptueel overzicht van de lijsten met verboden wacht woorden](concept-password-ban-bad.md)

[Conceptueel overzicht van Azure AD-wachtwoord beveiliging](concept-password-ban-bad-on-premises.md)

[On-premises integratie met de lijsten met verboden wacht woorden inschakelen](howto-password-ban-bad-on-premises.md)
