---
title: Een werk- of schoolaccount toevoegen aan de Microsoft Authenticator-app - Azure AD
description: Voeg uw werk- of schoolaccount toe aan de Microsoft Authenticator-app om uw identiteit te verifiëren terwijl u tweestapsverificatie gebruikt.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: f0cc14a53f7ead7f0a496728d477d7d30857a0fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063914"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>Uw werk- of schoolaccount toevoegen aan de Microsoft Authenticator-app

Als uw organisatie tweestapsverificatie gebruikt, u uw werk- of schoolaccount instellen om de Microsoft Authenticator-app te gebruiken als een van de verificatiemethoden.

>[!Important]
>Voordat u uw account toevoegen, moet u de Microsoft Authenticator-app downloaden en installeren. Als u dat nog niet hebt gedaan, volgt u de stappen in het [artikel Downloaden en installeren.](user-help-auth-app-download-install.md)

## <a name="add-your-work-or-school-account"></a>Uw werk- of schoolaccount toevoegen

1. Ga op uw computer naar de pagina [Extra beveiligingsverificatie.](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1)

    >[!Note]
    >Als u de pagina **Extra beveiligingsverificatie** niet ziet, is het mogelijk dat uw beheerder de beveiligingsgegevens (preview)-ervaring heeft ingeschakeld. Als dat het geval is, moet u de instructies in de [beveiligingsgegevens instellen volgen om een authenticator-app-sectie te gebruiken.](security-info-setup-auth-app.md) Als dat niet het geval is, moet u contact opnemen met de helpdesk van uw organisatie voor hulp. Zie [Overzicht van beveiligingsgegevens (voorbeeld).](user-help-security-info-overview.md)

2. Schakel het selectievakje naast **de Authenticator-app**in en selecteer **Configureren**.

    De pagina **Mobiele app configureren** wordt weergegeven.

    ![Scherm met de QR-code](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. Open de Microsoft Authenticator-app, selecteer **Account toevoegen** in het pictogram **Aanpassen en besturingselement** rechtsboven en selecteer **Werk- of schoolaccount**.

    >[!Note]
    >Als dit de eerste keer is dat u de Microsoft Authenticator-app instelt, ontvangt u mogelijk een prompt met de vraag of u de app toegang wilt geven tot uw camera (iOS) of dat de app foto's kan maken en video (Android) opnemen. U moet **Toestaan** selecteren, zodat de authenticator-app toegang heeft tot uw camera om in de volgende stap een foto van de QR-code te maken. Als u de camera niet toestaat, u de authenticator-app nog steeds instellen, maar u moet de codegegevens handmatig toevoegen. Zie Handmatig een account toevoegen aan [de app voor](user-help-auth-app-add-account-manual.md)informatie over het handmatig toevoegen van de code.

4. Gebruik de camera van uw apparaat om de QR-code te scannen vanuit het scherm **Mobiele app configureren** op uw computer en kies **Gereed**.

    >[!Note]
    >Als uw camera de QR-code niet kan vastleggen, u uw accountgegevens handmatig toevoegen aan de Microsoft Authenticator-app voor tweestapsverificatie. Zie [Uw account handmatig toevoegen](user-help-auth-app-add-account-manual.md)voor meer informatie en hoe u dit doen.

5. Bekijk het **scherm Accounts** van de app op uw apparaat om te controleren of uw account goed is en of er een bijbehorende verificatiecode van zes cijfers is. Voor extra beveiliging wordt de verificatiecode elke 30 seconden gewijzigd waardoor iemand een code meerdere keren kan gebruiken.

    ![Scherm Accounts](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>Volgende stappen

- Nadat u uw accounts aan de app hebt toegevoegd, u zich aanmelden met de Authenticator-app op uw apparaat. Zie [Aanmelden met de app](user-help-auth-app-sign-in.md)voor meer informatie.

- Voor apparaten met iOS u ook een back-up maken van uw accountreferenties en gerelateerde app-instellingen, zoals de volgorde van uw accounts, naar de cloud. Zie [Back-upmaken en herstellen met de Microsoft Authenticator-app voor](user-help-auth-app-backup-recovery.md)meer informatie.
