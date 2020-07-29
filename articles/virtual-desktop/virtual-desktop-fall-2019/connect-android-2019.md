---
title: Verbinding maken met het virtuele bureau blad van Windows (klassiek) van Android-Azure
description: Verbinding maken met het virtuele bureau blad van Windows (klassiek) met behulp van de Android-client.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fd319c7a6b356052f91e779e2f2a67eff4c4b6de
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87270750"
---
# <a name="connect-to-windows-virtual-desktop-classic-with-the-android-client"></a>Verbinding maken met het virtuele bureau blad van Windows (klassiek) met de Android-client

> Van toepassing op: Android 4,1 en hoger, Chromebooks met ChromeOS 53 of hoger.

>[!IMPORTANT]
>Deze inhoud is van toepassing op het virtuele bureau blad van Windows (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager virtueel-bureaublad objecten van Windows. Zie [dit artikel](../connect-android.md)als u probeert Azure Resource Manager virtueel-bureaublad objecten van Windows te beheren.

>[!NOTE]
> De mogelijkheid om toegang te krijgen tot virtuele bureau blad-resources van Windows vanaf de Android-client, is momenteel beschikbaar als preview-versie.

U kunt toegang krijgen tot virtuele bureau blad-resources van Windows vanaf uw Android-apparaat met onze Download bare client. U kunt de Android-client ook gebruiken op Chromebook-apparaten die ondersteuning bieden voor de Google Play Store. In deze hand leiding wordt uitgelegd hoe u de Android-client kunt instellen.

## <a name="install-the-android-client"></a>De Android-client installeren

[Down load](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) en installeer de-client op uw Android-apparaat om aan de slag te gaan.

## <a name="subscribe-to-a-feed"></a>Abonneren op een feed

Abonneer u op de feed van uw beheerder om de lijst met beheerde resources weer te geven die u kunt openen op uw Android-apparaat.

Abonneren op een feed:

1. Tik in het verbindings centrum **+** en tik vervolgens op **externe bron invoer**.
2. Voer de URL van de feed in het veld URL van de **feed** in. De feed-URL kan een URL of een e-mail adres zijn.
   - Als u een URL gebruikt, gebruikt u de beheerder die u normaal gesp roken hebt gekregen <https://rdweb.wvd.microsoft.com> .
   - Als u e-mail wilt gebruiken, voert u uw e-mail adres in. De client zoekt naar een URL die is gekoppeld aan uw e-mail adres als uw beheerder de server op die manier heeft geconfigureerd.
3. Tik op **VOLGENDE**.
4. Geef uw referenties op wanneer u hierom wordt gevraagd.
   - Geef voor de **gebruikers naam**de gebruikers naam op met machtigingen voor toegang tot resources.
   - Geef bij **wacht woord**het wacht woord op dat is gekoppeld aan de gebruikers naam.
   - U wordt mogelijk ook gevraagd extra factoren op te geven als uw beheerder de verificatie op die manier heeft geconfigureerd.

Nadat u zich hebt geabonneerd, moeten de externe bronnen worden weer gegeven in het verbindings centrum.

Zodra u bent geabonneerd op een feed, wordt de inhoud van de feed regel matig automatisch bijgewerkt. Resources kunnen worden toegevoegd, gewijzigd of verwijderd op basis van wijzigingen die zijn aangebracht door de beheerder.

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over het gebruik van de Android-client naar aan [de slag met de Android-client](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/).