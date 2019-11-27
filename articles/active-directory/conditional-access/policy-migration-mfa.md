---
title: Beleid voor voorwaardelijke toegang migreren-Azure Active Directory
description: In dit artikel wordt beschreven hoe u een klassiek beleid migreert waarvoor multi-factor Authentication is vereist in de Azure Portal.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: tutorial
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca00ae62ba114aecef48117fd8a54b7f2e962dfd
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380327"
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>Migreer een klassiek beleid waarvoor multi-factor Authentication is vereist in de Azure Portal

In deze zelf studie ziet u hoe u een klassiek beleid migreert waarvoor **multi-factor Authentication** voor een Cloud-app is vereist. Hoewel het geen vereiste is, raden we u aan [het klassieke beleid te migreren in de Azure Portal](policy-migration.md) voordat u begint met het migreren van uw klassieke beleids regels.

## <a name="overview"></a>Overzicht

In het scenario in dit artikel ziet u hoe u een klassiek beleid migreert waarvoor **multi-factor Authentication** is vereist voor een Cloud-app.

![Azure Active Directory](./media/policy-migration/33.png)

Het migratie proces bestaat uit de volgende stappen:

1. [Open het klassieke beleid](#open-a-classic-policy) om de configuratie-instellingen op te halen.
1. Maak een nieuw beleid voor voorwaardelijke toegang voor Azure AD om uw klassieke beleid te vervangen. 
1. Schakel het klassieke beleid uit.

## <a name="open-a-classic-policy"></a>Een klassiek beleid openen

1. Klik in het [Azure Portal](https://portal.azure.com)op de linkernavigatiebalk op **Azure Active Directory**.

   ![Azure Active Directory](./media/policy-migration-mfa/01.png)

1. Klik op de pagina **Azure Active Directory** in de sectie **beheren** op **voorwaardelijke toegang**.

   ![Voorwaardelijke toegang](./media/policy-migration-mfa/02.png)

1. Klik in de sectie **beheren** op **klassiek beleid (preview-versie)** .

   ![Klassiek beleid](./media/policy-migration-mfa/12.png)

1. Klik in de lijst met klassiek beleid op het beleid waarvoor **multi-factor Authentication** is vereist voor een Cloud-app.

   ![Klassiek beleid](./media/policy-migration-mfa/13.png)

## <a name="create-a-new-conditional-access-policy"></a>Een nieuw beleid voor voorwaardelijke toegang maken

1. Klik in het [Azure Portal](https://portal.azure.com)op de linkernavigatiebalk op **Azure Active Directory**.

   ![Azure Active Directory](./media/policy-migration/01.png)

1. Klik op de pagina **Azure Active Directory** in de sectie **beheren** op **voorwaardelijke toegang**.

   ![Voorwaardelijke toegang](./media/policy-migration/02.png)

1. Klik op de pagina **voorwaardelijke toegang** op **toevoegen**in de werk balk aan de bovenkant om de pagina **Nieuw** weer te geven.

   ![Voorwaardelijke toegang](./media/policy-migration/03.png)

1. Typ op de pagina **Nieuw** in het tekstvak **naam** een naam voor uw beleid.

   ![Voorwaardelijke toegang](./media/policy-migration/29.png)

1. Klik in de sectie **toewijzingen** op **gebruikers en groepen**.

   ![Voorwaardelijke toegang](./media/policy-migration/05.png)

   1. Als u alle gebruikers hebt geselecteerd in het klassieke beleid, klikt u op **alle gebruikers**. 

      ![Voorwaardelijke toegang](./media/policy-migration/35.png)

   1. Als u groepen hebt geselecteerd in het klassieke beleid, klikt u op **gebruikers en groepen selecteren**en selecteert u vervolgens de vereiste gebruikers en groepen.

      ![Voorwaardelijke toegang](./media/policy-migration/36.png)

   1. Als u de uitgesloten groepen hebt, klikt u op het tabblad **uitsluiten** en selecteert u vervolgens de vereiste gebruikers en groepen. 

      ![Voorwaardelijke toegang](./media/policy-migration/37.png)

1. Klik op de pagina **Nieuw** op **Cloud-apps**in het gedeelte **toewijzing** om de pagina **Cloud-apps** te openen.
1. Voer de volgende stappen uit op de pagina **Cloud-apps** :
   1. Klik op **apps selecteren**.
   1. Klik op **Selecteren**.
   1. Selecteer uw Cloud-app op de pagina **selecteren** en klik vervolgens op **selecteren**.
   1. Klik op de pagina **Cloud-apps** op **gereed**.
1. Als u **multi-factor Authentication vereisen** hebt ingeschakeld:

   ![Voorwaardelijke toegang](./media/policy-migration/26.png)

   1. Klik in de sectie **toegangs beheer** op **verlenen**.

      ![Voorwaardelijke toegang](./media/policy-migration/27.png)

   1. Klik op de pagina **Grant** op **toegang verlenen**en klik vervolgens op **multi-factor Authentication vereisen**.
   1. Klik op **Selecteren**.
1. Klik op om uw **beleid in te** scha kelen.

   ![Voorwaardelijke toegang](./media/policy-migration/30.png)

## <a name="disable-the-classic-policy"></a>Het klassieke beleid uitschakelen

Als u het klassieke beleid wilt uitschakelen, klikt u op **uitschakelen** in de weer gave **Details** .

![Klassiek beleid](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>Volgende stappen

- Zie [klassiek beleid migreren in de Azure Portal](policy-migration.md)voor meer informatie over de klassieke beleids migratie.
- Als u wilt weten hoe u een beleid voor voorwaardelijke toegang kunt configureren, raadpleegt u [MFA vereisen voor specifieke apps met voorwaardelijke toegang Azure Active Directory](app-based-mfa.md).
- Als u klaar bent voor het configureren van beleid voor voorwaardelijke toegang voor uw omgeving, raadpleegt u de [Aanbevolen procedures voor voorwaardelijke toegang in azure Active Directory](best-practices.md).
