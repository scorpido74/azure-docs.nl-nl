---
title: Uw werkapparaat aansluiten bij het netwerk van uw organisatie - AD
description: Meer informatie over het aansluiten van uw werkapparaat op het netwerk van uw organisatie.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: curtand
ms.reviewer: jairoc
ms.openlocfilehash: 0ff8b85a15d94ded2d702e0df247f9ebc4d3f923
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266310"
---
# <a name="join-your-work-device-to-your-organizations-network"></a>Sluit uw werkapparaat aan bij het netwerk van uw organisatie
Sluit uw Windows 10-apparaat in handen van uw bedrijf aan het netwerk van uw organisatie, zodat u toegang hebt tot mogelijk beperkte bronnen.

## <a name="what-happens-when-you-join-your-device"></a>Wat gebeurt er wanneer u lid wordt van uw apparaat
Terwijl u uw Windows 10-apparaat aansluit bij het netwerk van uw organisatie, worden de volgende acties uitgevoerd:

- Windows registreert uw apparaat bij het netwerk van uw organisatie, zodat u toegang krijgt tot uw bronnen met uw persoonlijke account. Nadat uw apparaat is geregistreerd, voegt Windows uw apparaat toe aan het netwerk, zodat u de gebruikersnaam en het wachtwoord van uw organisatie gebruiken om u aan te melden en toegang te krijgen tot beperkte bronnen.

- Optioneel wordt u op basis van de keuzes van uw organisatie gevraagd om verificatie in twee stappen in te stellen via [multi-factorverificatie](multi-factor-authentication-end-user-first-time.md) of [beveiligingsgegevens.](user-help-security-info-overview.md)

- Optioneel wordt u op basis van de keuzes van uw organisatie automatisch ingeschreven voor het beheer van mobiele apparaten, zoals Microsoft Intune. Zie Uw apparaat inschrijven in Intune voor meer informatie over het inschrijven in Microsoft [Intune.](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all)

- Je gaat door het aanmeldingsproces en gebruikt automatisch aanmelden met je organisatieaccount.

## <a name="to-join-a-brand-new-windows-10-device"></a>Deelnemen aan een gloednieuw Windows 10-apparaat
Als uw apparaat gloednieuw is en nog niet is ingesteld, u het Oobe-proces (Windows Out of Box Experience) doorlopen om uw apparaat bij het netwerk aan te sluiten.

1. Start uw nieuwe apparaat op en start het OOBE-proces.

2. Typ **in** het scherm Aanmelden met Microsoft uw werk- of schoole-mailadres.

    ![Inlogscherm met e-mailadres](./media/user-help-join-device-on-network/join-device-oobe-signin.png)

3. Typ uw wachtwoord in het scherm **Uw wachtwoord invoeren.**

    ![Voer het wachtwoordscherm in](./media/user-help-join-device-on-network/join-device-oobe-password.png)

4. Keur uw apparaat goed op uw mobiele apparaat, zodat het toegang heeft tot uw account. 

    ![Mobiel meldingsscherm](./media/user-help-join-device-on-network/join-device-oobe-mobile.png)

5. Voltooi het OOBE-proces, inclusief het instellen van uw privacy-instellingen en het instellen van Windows Hello (indien nodig).

    Uw apparaat is nu verbonden met het netwerk van uw organisatie.

## <a name="to-make-sure-youre-joined"></a>Om er zeker van te zijn dat je lid bent
Je ervoor zorgen dat je lid wordt door naar je instellingen te kijken.

1. Open **Instellingen**en selecteer **Accounts**.

    ![Accounts op het scherm Instellingen](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Selecteer **Werk of school openen**en zorg ervoor dat u tekst ziet die iets zegt **als, Verbonden * \<* met your_organization>Azure AD**.

    ![Toegang tot werk- of schoolscherm met verbonden contoso-account](./media/user-help-join-device-on-network/join-device-oobe-verify.png)


## <a name="to-join-an-already-configured-windows-10-device"></a>Deelnemen aan een al geconfigureerd Windows 10-apparaat
Als u uw apparaat al een tijdje hebt en het apparaat al is ingesteld, u deze stappen volgen om uw apparaat bij het netwerk aan te sluiten.

1. Open **Instellingen**en selecteer **Accounts**.

2. Selecteer **Werk of school openen**en selecteer Verbinding **maken**.

    ![Toegang tot koppelingen naar werk of school en Verbinding](./media/user-help-join-device-on-network/join-device-access-work-school-connect.png)

3. Selecteer Op het scherm **Een werk- of schoolaccount instellen** de optie **Dit apparaat aansluiten bij Azure Active Directory**.

    ![Een werk- of schoolaccountscherm instellen](./media/user-help-join-device-on-network/join-device-setup-join-aad.png)

4. Typ in het scherm **Laten we u aanmelden,** typ alain@contoso.comuw e-mailadres (bijvoorbeeld) en selecteer **Volgende**.

    ![Je hebt je aangemeld in het scherm](./media/user-help-join-device-on-network/join-device-setup-get-signed-in.png)

5. Typ uw wachtwoord in het **scherm Wachtwoord invoeren** en selecteer **Aanmelden**.

    ![Wachtwoord invoeren](./media/user-help-join-device-on-network/join-device-setup-password.png)

6. Keur uw apparaat goed op uw mobiele apparaat, zodat het toegang heeft tot uw account. 

    ![Mobiel meldingsscherm](./media/user-help-join-device-on-network/join-device-setup-mobile.png)

7. Bekijk de informatie om te controleren of deze juist is en selecteer **Join**in het scherm Zorg ervoor dat dit het scherm **van uw organisatie is.**

    ![Zorg ervoor dat dit het verificatiescherm van uw organisatie is](./media/user-help-join-device-on-network/join-device-setup-confirm.png)

8. Klik op het scherm **U bent helemaal ingesteld** op **Gereed**.

    ![U bent helemaal klaar scherm](./media/user-help-join-device-on-network/join-device-setup-finish.png)

## <a name="to-make-sure-youre-joined"></a>Om er zeker van te zijn dat je lid bent
Je ervoor zorgen dat je lid wordt door naar je instellingen te kijken.

1. Open **Instellingen**en selecteer **Accounts**.

    ![Accounts op het scherm Instellingen](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Selecteer **Werk of school openen**en zorg ervoor dat u tekst ziet die iets zegt **als, Verbonden * \<* met your_organization>Azure AD**.

    ![Toegang tot werk- of schoolscherm met verbonden contoso-account](./media/user-help-join-device-on-network/join-device-setup-verify.png)

## <a name="next-steps"></a>Volgende stappen
Nadat u lid bent geworden van uw apparaat in het netwerk van uw organisatie, moet u toegang hebben tot al uw bronnen met behulp van uw werk- of schoolaccountgegevens.

- Zie [Uw persoonlijke apparaat registreren op het netwerk van uw organisatie](user-help-register-device-on-network.md)als u uw persoonlijke apparaat wilt registreren, zoals uw telefoon.

- Zie de Help-inhoud van Intune van [Intune](https://docs.microsoft.com/intune-user-help/use-managed-devices-to-get-work-done)als uw organisatie wordt beheerd met Microsoft Intune en u vragen hebt over inschrijving, aanmelding of een ander Intune-gerelateerd probleem.