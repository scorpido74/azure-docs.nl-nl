---
title: Stel een lab in om de ontwikkeling van mobiele toepassingen te leren maken met Android Studio
titleSuffix: Azure Lab Services
description: Meer informatie over het instellen van een Lab voor het leren van de ontwikkelings klasse voor gegevens mobiele toepassingen die gebruikmaakt van Android Studio.  In dit artikel worden ook de aanpassingen besproken waarmee u Android Studio op een virtuele machine in azure kunt gebruiken.
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 0c257589a2e93ac4c15a639e7156d0c0944b033c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76849784"
---
# <a name="set-up-a-lab-to-teach-data-mobile-application-development-with-android-studio"></a>Stel een lab in om de ontwikkeling van data Mobile-toepassingen te leren met Android Studio

In dit artikel wordt uitgelegd hoe u een inleidende klasse voor de ontwikkeling van mobiele toepassingen kunt instellen.  Deze klasse richt zich op mobiele Android-toepassingen die op de [Google Play Store](https://play.google.com/store/apps)kunnen worden gepubliceerd.  Studenten leren hoe u [Android Studio](https://developer.android.com/studio) kunt gebruiken om toepassingen te bouwen.  [Visual Studio emulator voor Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) wordt gebruikt om de toepassing lokaal te testen.

## <a name="lab-configuration"></a>Lab-configuratie

Als u dit Lab wilt instellen, hebt u een Azure-abonnement en een Lab-account nodig om aan de slag te gaan. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint. Zodra u een Azure-abonnement hebt ontvangen, kunt u een nieuw Lab-account maken in Azure Lab Services. Zie [zelf studie voor het instellen van een Lab](tutorial-setup-lab-account.md)-account voor meer informatie over het maken van een nieuw Lab-account.  U kunt ook een bestaand Lab-account gebruiken.

Volg de [zelf studie klassikale Lab instellen](tutorial-setup-classroom-lab.md) om een nieuw lab te maken en pas vervolgens de volgende instellingen toe:

| Grootte van de virtuele machine | Installatiekopie |
| -------------------- | ----- |
| Gemiddeld (geneste virtualisatie) | Windows Server 2019 Datacenter |

## <a name="template-machine-configuration"></a>Configuratie van de sjabloon machine

Wanneer het maken van de sjabloon is voltooid, [start u de machine en maakt u verbinding](how-to-create-manage-template.md#update-a-template-vm) met de computer om de volgende taken uit te voeren:

1. Hyper-V-functie toevoegen
2. Down load en Installeer Java.  
3. Down load en Installeer Visual Studio emulator voor Android.
4. Down load en installeer Android Studio.
5. Visual Studio emulator configureren voor Android Studio.

## <a name="add-hyper-v-role"></a>Hyper-V-functie toevoegen

Hyper-V moet zijn ingeschakeld voor een geslaagde installatie van Visual Studio emulator voor Android.  Volg de instructies in het artikel [een virtuele machine van een sjabloon gebruiken om geneste virtualisatie in te scha kelen](how-to-enable-nested-virtualization-template-vm.md) .

## <a name="install-java"></a>Java installeren

Voor Android Studio is Java vereist.  Volg de onderstaande stappen om de meest recente versie van Java te downloaden.

1. Ga naar de [Java-download pagina](https://www.java.com/download/). Klik op de knop **java downloaden** .
2. Op de webpagina 64-bits Windows voor Java klikt u op de knop met het label **akkoord en start u gratis downloaden**.
3. Wanneer het installatie programma voor **Java** wordt weer gegeven, klikt u op **installeren**.
4. Wacht tot de titel van het installatie programma is gewijzigd in **Java Setup-voltooid**.  Klik op de knop **sluiten** .

## <a name="install-visual-studio-emulator-for-android"></a>Visual Studio emulator voor Android installeren

Als u een Android-toepassing lokaal wilt testen, moet deze een gevirtualiseerde versie van een Android-apparaat gebruiken.  Er zijn enkele Android-emulators beschikbaar waarmee een ontwikkelaar hun toepassing vanaf hun machine kan testen.  We gebruiken Visual Studio emulator voor Android omdat het een emulator is die geneste virtualisatie ondersteunt.  Omdat de VM van de test service al een virtuele machine is, hebt u een emulator nodig die geneste virtualisatie ondersteunt.  De ingebouwde emulator voor Android Studio biedt geen ondersteuning voor geneste virtualisatie.  Als u wilt zien welke emulators ondersteuning bieden voor geneste virtualisatie, raadpleegt u hardwareversnelling [voor emulator-prestaties (Hyper-V & HAXM)](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/hardware-acceleration).

Gebruik de volgende instructies om Visual Studio emulator voor Android te downloaden en te installeren.

1. Ga naar de start pagina [van de Visual Studio-emulator voor Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) .
2. Klik op de **emulator knop downloaden** .
3. Als vs_emulatorsetup. exe is gedownload, voert u het uitvoer bare bestand uit.
4. Wanneer het dialoog venster Visual Studio Setup wordt weer gegeven, klikt u op de knop **installeren** .
5. Wacht totdat het installatie programma is voltooid.  Klik op de knop **nu opnieuw opstarten** om de computer opnieuw op te starten en de installatie te volt ooien.

Start de emulator eerst voordat u uw toepassing implementeert met behulp van Android Studio.  Zie de [documentatie voor Visual Studio emulator voor Android](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-emulator-for-android)voor meer informatie over de Visual Studio-emulator voor Android.

## <a name="install-android-studio"></a>Android Studio installeren

Volg de onderstaande instructies om [Android Studio](https://developer.android.com/studio)te downloaden en te installeren.

1. Navigeer naar [Android studio download pagina](https://developer.android.com/studio#downloads).  
    > [!NOTE]
    > Internet Explorer wordt niet ondersteund door deze site.
2. Klik op het uitvoer bare Android Studio-pakket voor Windows (64-bits).
3. Lees de juridische voor waarden die in het pop-upvenster zijn geschreven.  Wanneer u klaar bent om door te gaan, controleert u het selectie vakje **Ik heb de bovenstaande voor waarden gelezen en ermee akkoord** en klikt u op de knop **Android Studio voor Windows downloaden** .
4. Als het uitvoer bare bestand van de Android Studio-installatie wordt gedownload, voert u het uitvoer bare bestand uit.
5. Klik op de pagina **Welkom bij Android Studio Setup** van het installatie programma van **Android Studio Setup** op **volgende**.
6. Klik op de pagina **configuratie-instellingen** op **volgende**.
7. Klik op de pagina **menu Start kiezen** op **installeren**.
8. Wacht totdat de installatie is voltooid.
9. Klik op **volgende**op de pagina **installatie voltooid** .
10. Op de pagina de **installatie van Android Studio volt ooien** .  Klik op **Voltooien**.
11. Android Studio wordt automatisch gestart nadat de installatie is voltooid.
12. Selecteer in het dialoog venster **Android-instellingen importeren uit...** de optie **geen instellingen importeren**. Klik op **OK**.
13. Klik op de pagina **Welkom** van de **wizard Android Studio Setup**op **volgende**.
14. Kies op de pagina **type installatie** de optie **standaard**. Klik op **Volgende**.
15. Selecteer op de pagina **gebruikers interface thema selecteren** het gewenste thema. Klik op **Volgende**.
16. Klik op de pagina **instellingen controleren** op **volgende**.
17. Wacht op de pagina **onderdelen downloaden** totdat alle onderdelen zijn gedownload.  Klik op **Voltooien**.

    > [!IMPORTANT]
    > Verwacht wordt dat de installatie van HAXM mislukt.  HAXM biedt geen ondersteuning voor geneste virtualisatie. Daarom is de Visual Studio-emulator voor Android eerder in dit artikel geïnstalleerd.

18. Het dialoog venster **Welkom bij Android Studio** wordt weer gegeven wanneer de installatie wizard is voltooid.

## <a name="configure-android-studio-and-visual-studio-emulator-for-android"></a>Android Studio en Visual Studio emulator configureren voor Android

Android Studio is bijna klaar voor gebruik.  We moeten nog steeds Visual Studio emulator voor Android laten weten waar de Android SDK is geïnstalleerd.  Hierdoor worden alle emulators uitgevoerd in Visual Studio voor Android als implementatie doelen voor Android Studio fout opsporing.

We moeten een specifieke register sleutel instellen om de Visual Studio emulator voor Android te laten weten waar de Android SDK zich bevindt.  Voer het onderstaande script uit om de benodigde register sleutel in te stellen.  In het onderstaande Power shell-script wordt aangenomen dat de standaard installatie locatie voor de Android-SDK.  Als u uw Android SDK op een andere locatie hebt geïnstalleerd, wijzigt u `$androidSdkPath` de waarde voor voordat u het script uitvoert.

```powershell
$androidSdkPath = Resolve-Path $(Join-Path "$($env:APPDATA)" "../Local/Android/Sdk")

$registryKeyPath = "HKLM:Software\WOW6432NODE\Android Sdk Tools"
New-Item -Path $registryKeyPath
New-ItemProperty -Path $registryKeyPath -Name Path -PropertyType String -Value $androidSdkPath
```

> [!IMPORTANT]
> Start Visual Studio emulator opnieuw voor Android en Android Studio zodat de nieuwe instelling wordt gebruikt.

Start de versie die u nodig hebt in de Visual Studio-emulator.  Het wordt weer gegeven als een implementatie doel voor uw Android-app in Android Studio.  De minimale versie voor het Android Studio project moet ondersteuning bieden voor de versie die wordt uitgevoerd in de Visual Studio-emulator voor Android.  U bent er nu klaar voor om projecten te maken en fouten op te lossen met behulp van Android Studio en Visual Studio emulator voor Android.  Zie problemen met Android-Emulator oplossen als u problemen ondervindt.

## <a name="cost"></a>Kosten

Als u de kosten van dit Lab wilt schatten, kunt u het onderstaande voor beeld volgen.
Voor een klasse van 25 studenten met 20 uur geplande en tien uur aan quota voor huis werk of-toewijzingen, wordt de prijs voor het lab  

25 studenten \* (20 geplande + 10 quota) uur * 55 Lab-eenheden * 0,01 USD per uur = 412,5 USD

Zie [Azure Lab Services prijzen](https://azure.microsoft.com/pricing/details/lab-services/)voor meer informatie over prijzen.

## <a name="next-steps"></a>Volgende stappen

De volgende stappen zijn gebruikelijk voor het instellen van elk lab.

- [Een sjabloon maken en beheren](how-to-create-manage-template.md)
- [Gebruikers toevoegen](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota instellen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Een planning instellen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail registratie koppelingen naar studenten](how-to-configure-student-usage.md#send-invitations-to-users)
