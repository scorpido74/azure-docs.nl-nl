---
title: Een werk-of school account toevoegen aan de Microsoft Authenticator-app-Azure AD
description: Voeg uw werk-of school account toe aan de app Microsoft Authenticator om uw identiteit te verifiëren met twee ledige verificatie methoden.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1d781ee16cd1c78f03b58288bf75e036e38cf00
ms.sourcegitcommit: a100e3d8b0697768e15cbec11242e3f4b0e156d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2020
ms.locfileid: "75681222"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>Voeg uw werk-of school account toe aan de app Microsoft Authenticator

Als uw organisatie twee ledige verificatie gebruikt, kunt u uw werk-of school account instellen om de Microsoft Authenticator-app als een van de verificatie methoden te gebruiken.

>[!Important]
>Voordat u uw account kunt toevoegen, moet u de app Microsoft Authenticator downloaden en installeren. Als u dat nog niet hebt gedaan, volgt u de stappen in het artikel [app downloaden en installeren](user-help-auth-app-download-install.md) .

## <a name="add-your-work-or-school-account"></a>Uw werk-of school account toevoegen

1. Ga op uw computer naar de pagina [aanvullende beveiligings verificatie](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1) .

    >[!Note]
    >Als de pagina **aanvullende beveiligings verificatie** niet wordt weer gegeven, is het mogelijk dat de beheerder de ervaring voor beveiligings gegevens (preview) heeft ingeschakeld. Als dat het geval is, volgt u de instructies in de sectie [beveiligings gegevens instellen voor het gebruik van een verificator-app](security-info-setup-auth-app.md) . Als dat niet het geval is, moet u contact opnemen met de Help Desk van uw organisatie voor hulp. Zie voor meer informatie over beveiligings informatie [overzicht van beveiligings gegevens (preview)](user-help-security-info-overview.md).

2. Schakel het selectie vakje naast de **verificator-app**in en selecteer **configureren**.

    De pagina **mobiele app configureren** wordt weer gegeven.

    ![Scherm met de QR-code](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. Open de Microsoft Authenticator-app, selecteer **account toevoegen** uit het pictogram **aanpassen en beheer** in de rechter bovenhoek en selecteer vervolgens **werk-of school account**.

    >[!Note]
    >Als dit de eerste keer is dat u de Microsoft Authenticator-app instelt, wordt u mogelijk gevraagd of u de app toegang wilt geven tot uw camera (iOS) of dat de app afbeeldingen en record video (Android) mag maken. U moet **toestaan** inschakelen zodat de verificator-app toegang heeft tot uw camera om een foto van de QR-code in de volgende stap te maken. Als u de camera niet toestaat, kunt u nog steeds de verificator-app instellen, maar u moet de code gegevens hand matig toevoegen. Zie [hand matig een account toevoegen aan de app](user-help-auth-app-add-account-manual.md)voor meer informatie over het hand matig toevoegen van de code.

4. Gebruik de camera van uw apparaat om de QR-code te scannen vanuit het scherm **mobiele app configureren** op uw computer en kies vervolgens **gereed**.

    >[!Note]
    >Als uw camera de QR-code niet kan vastleggen, kunt u uw account gegevens hand matig toevoegen aan de Microsoft Authenticator-app voor twee ledige verificatie. Zie [hand matig uw account toevoegen](user-help-auth-app-add-account-manual.md)voor meer informatie en hoe u dit doet.

5. Bekijk het scherm **accounts** van de app op uw apparaat om te controleren of uw account juist is en of er een verificatie code van zes cijfers is. Voor extra beveiliging verandert de verificatie code elke 30 seconden om te voor komen dat iemand meerdere keren een code gebruikt.

    ![Scherm accounts](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>Volgende stappen

- Nadat u uw accounts aan de app hebt toegevoegd, kunt u zich aanmelden met behulp van de verificator-app op uw apparaat. Zie [Aanmelden met de app](user-help-auth-app-sign-in.md)voor meer informatie.

- Voor apparaten met iOS kunt u ook een back-up maken van uw account referenties en de bijbehorende app-instellingen, zoals de volg orde van uw accounts, naar de Cloud. Zie [back-up en herstellen met Microsoft Authenticator-app](user-help-auth-app-backup-recovery.md)voor meer informatie.
