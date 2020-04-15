---
title: Gebruikerservaring via sms voor telefoonnummer (voorbeeld) - Azure AD
description: Meer informatie over de gebruikerservaring van sms-aanmeldingvoor nieuwe of bestaande telefoonnummers
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: e5369247fc1e31bafb158e83a54bf8badf721d0d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378832"
---
# <a name="use-your-phone-number-as-a-user-name-preview"></a>Uw telefoonnummer gebruiken als gebruikersnaam (voorbeeld)

Het registreren van een apparaat geeft uw telefoon toegang tot de services van uw organisatie en geeft uw organisatie geen toegang tot uw telefoon. Als u een beheerder bent, u meer informatie vinden in [Gebruikers configureren en inschakelen voor verificatie via sms.](../authentication/howto-authentication-sms-signin.md)

Als uw organisatie geen sms-aanmelding beschikbaar heeft gemaakt, ziet u geen optie voor het account wanneer u een telefoon registreert met uw account.  

## <a name="when-you-have-a-new-phone-number"></a>Wanneer u een nieuw telefoonnummer hebt

Als u een nieuwe telefoon of nieuw nummer krijgt en u registreert deze bij een organisatie waarvoor sms-aanmelding beschikbaar is, ervaart u het normale registratieproces voor telefoons:

1. Selecteer **Methode toevoegen**.
1. Selecteer **Telefoon**.
1. Voer telefoonnummer in en selecteer **Tekst mij een code**.
1. Nadat u de code hebt ingevoerd, selecteert u **Volgende**.
1. U ziet een prompt die zegt: "SMS geverifieerd. Uw telefoon is met succes geregistreerd."

> [!Important]
> Vanwege een bekend probleem in de preview, voor een korte tijd het toevoegen van telefoonnummer zal niet registreren het nummer voor SMS-aanmelding. Je moet je aanmelden met het toegevoegde nummer en vervolgens de aanwijzingen volgen om het nummer voor sms-aanmelding te registreren.

### <a name="when-the-phone-number-is-in-use"></a>Wanneer het telefoonnummer in gebruik is

Als u een telefoonnummer probeert te gebruiken dat iemand anders in uw organisatie gebruikt, ziet u het volgende bericht:

![Foutbericht wanneer uw telefoonnummer al is gebruikt](media/sms-sign-in-explainer/sms-sign-in-error.png)

Neem contact op met uw beheerder om het probleem op te lossen.

## <a name="when-you-have-an-existing-number"></a>Wanneer u een bestaand nummer hebt

Als u al een telefoonnummer bij een organisatie gebruikt en uw telefoonnummer als gebruikersnaam gebruikt, u zich via de volgende stappen aanmelden.

1. Wanneer aanmelden via sms beschikbaar is, wordt een banner weergegeven met de vraag of u het telefoonnummer voor sms-aanmelding wilt inschakelen:

    [![](media/sms-sign-in-explainer/sms-sign-in-banner.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-banner.png#lightbox)

1. Er wordt ook een knop **Inschakelen** weergegeven als u de caret selecteert op de tegel telefoonmethode:

    [![](media/sms-sign-in-explainer/sms-sign-in-phone-method.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-phone-method.png#lightbox)

1. Als u de methode wilt inschakelen, selecteert u **Inschakelen**. U wordt gevraagd de actie te bevestigen:

    ![Bevestigingsdialoogvenster om aanmelden via sms in te schakelen voor een telefoonnummer](media/sms-sign-in-explainer/sms-sign-in-confirmation.png)

1. Selecteer **Inschakelen**.

## <a name="when-you-remove-your-phone-number"></a>Wanneer u uw telefoonnummer verwijdert

1. Als u het telefoonnummer wilt verwijderen, selecteert u de knop Verwijderen op de tegel van de sms-aanmeldingsmethode.

    [![](media/sms-sign-in-explainer/sms-sign-in-delete-method.png "Banner to delete SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-delete-method.png#lightbox)

2. Wanneer u wordt gevraagd de actie te bevestigen, selecteert u **OK**.

U een telefoonnummer dat wordt gebruikt als standaardaanmeldingsmethode niet verwijderen. Als u het nummer wilt verwijderen, moet u de standaardaanmeldingsmethode wijzigen en vervolgens het telefoonnummer opnieuw verwijderen.
