---
title: Een persoonlijk Microsoft-account toevoegen aan de Microsoft Authenticator-app - Azure AD
description: Voeg persoonlijke Microsoft-accounts toe, zoals voor Outlook.com of Xbox LIVE aan de Microsoft Authenticator-app om uw identiteit te verifiëren tijdens het gebruik van tweestapsverificatie.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: cef4f83881aed67c46477110de530bbf191ee39f
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984737"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Persoonlijke Microsoft-accounts toevoegen aan de Microsoft Authenticator-app

Voeg uw persoonlijke Microsoft-accounts, zoals voor Outlook.com- en Xbox LIVE-accounts, toe aan de Microsoft Authenticator-app voor zowel het standaard verificatieproces met twee stappen als het aanmelden voor een wachtwoordloze telefoon.

- **Standaard tweefactorverificatiemethode.** Typ uw gebruikersnaam en wachtwoord op het apparaat waarop u zich aanmeldt en kies vervolgens of de Microsoft Authenticator-app een melding verzendt of dat u de bijbehorende verificatiecode liever kopieert vanuit het scherm **Accounts** van de Microsoft Authenticator-app.

- **Wachtwoordloze aanmeldingsmethode.** Typ uw gebruikersnaam op het apparaat waarop u zich aanmeldt met uw persoonlijke Microsoft-account en gebruik vervolgens uw mobiele apparaat om te controleren of u uw vingerafdruk, gezicht of pincode gebruikt. Voor deze methode hoeft u niet uw wachtwoord op te geven.

>[!Important]
>Voordat u uw account toevoegen, moet u de Microsoft Authenticator-app downloaden en installeren. Als u dat nog niet hebt gedaan, volgt u de stappen in het [artikel Downloaden en installeren.](user-help-auth-app-download-install.md)

U uw persoonlijke Microsoft-account toevoegen door eerst tweestapsverificatie in te schakelen en vervolgens het account aan de app toe te voegen. U hoeft geen tweestapsverificatie in te schakelen om alleen aanmelding met wachtwoordloze telefoon voor uw account te gebruiken, maar we raden u ten zeerste aan om tweestapsverificatie in te schakelen voor extra accountbeveiliging.

## <a name="turn-on-two-factor-verification"></a>Tweestapsverificatie inschakelen

1. Ga op uw computer naar de [basispagina Beveiliging](https://account.microsoft.com/security) en meld u aan met uw persoonlijke Microsoft-account. Bijvoorbeeld alain@outlook.com.

2. Kies onder aan de **basispagina Beveiliging** de koppeling **meer beveiligingsopties.**

    ![Basispagina beveiliging met de koppeling 'meer beveiligingsopties' gemarkeerd](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. Ga naar de **sectie Verificatie in twee stappen** en kies ervoor om de functie **in**te schakelen . Je het hier ook uitschakelen als je het niet meer wilt gebruiken met je persoonlijke account.

## <a name="add-your-microsoft-account-to-the-app"></a>Uw Microsoft-account toevoegen aan de app

1. Open de Microsoft Authenticator-app op uw mobiele apparaat.

1. Selecteer op Android **account toevoegen** in het pictogram Aanpassen **en beheren** rechtsboven.

    ![Selectiepagina's voor Android-account](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

    Selecteer in iOS het pluspictogram rechtsboven.

    ![iOS-versie van de accountselectie-ervaring](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon-ios.png)

1. Kies **Op** de pagina Account toevoegen de optie **Persoonlijk account**.

1. Selecteer **Aanmelden bij Microsoft** om uw account toe te voegen. Een QR-code kan worden gebruikt wanneer beschikbaar, maar u uw account altijd toevoegen door u aan te melden met uw gebruikersnaam en wachtwoord.

    ![Selecteer een Microsoft-account of scan een QR-code indien beschikbaar](media/user-help-auth-app-add-personal-ms-account/add-account-android.png)

1. Meld u aan bij uw persoonlijke account, alain@outlook.comgebruik het juiste e-mailadres (zoals) en selecteer **Volgende**.

    >[!Note]
    >Als u geen persoonlijk Microsoft-account hebt, [kunt u er een maken.](https://account.microsoft.com/account/Account?refd=www.bing.com&ru=https%3A%2F%2Faccount.microsoft.com%2F%3Frefd%3Dwww.bing.com&destrt=home-index)

1. Voer uw wachtwoord in en selecteer **Aanmelden**. Uw persoonlijke account wordt toegevoegd aan de Microsoft Authenticator-app.

## <a name="next-steps"></a>Volgende stappen

- Nadat u uw accounts aan de app hebt toegevoegd, u zich aanmelden met de Authenticator-app op uw apparaat. Zie [Aanmelden met de app](user-help-auth-app-sign-in.md)voor meer informatie.

- Zie de sectie Problemen met **verificatiecode oplossen** van de beveiligingsgegevens van het [Microsoft-account & verificatiecodes](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) als u problemen ondervindt met het verkrijgen van uw verificatiecode voor uw persoonlijke Microsoft-account.

- Voor apparaten met iOS u ook een back-up maken van uw accountreferenties en gerelateerde app-instellingen, zoals de volgorde van uw accounts, naar de cloud. Zie [Back-upmaken en herstellen met de Microsoft Authenticator-app voor](user-help-auth-app-backup-recovery.md)meer informatie.
