---
title: Stel een lab in om mobiele applicatieontwikkeling te leren met Android Studio
titleSuffix: Azure Lab Services
description: Meer informatie over het opzetten van een lab om de ontwikkelklasse voor datamobiele toepassingen te onderwijzen die Android Studio gebruikt.  Artikel zal ook bespreken aanpassingen te maken bij het gebruik van Android Studio op een virtuele machine in Azure.
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 0c257589a2e93ac4c15a639e7156d0c0944b033c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849784"
---
# <a name="set-up-a-lab-to-teach-data-mobile-application-development-with-android-studio"></a>Stel een lab in om de ontwikkeling van data mobiele applicaties te leren met Android Studio

In dit artikel ziet u hoe u een inleidende klasse voor mobiele applicatieontwikkeling instellen.  Deze klasse richt zich op Android mobiele applicaties die kunnen worden gepubliceerd naar de [Google Play Store](https://play.google.com/store/apps).  Studenten leren hoe ze [Android Studio](https://developer.android.com/studio) kunnen gebruiken om applicaties te bouwen.  [Visual Studio Emulator voor Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) wordt gebruikt om de toepassing lokaal te testen.

## <a name="lab-configuration"></a>Labconfiguratie

Om dit lab in te stellen, hebt u een Azure-abonnement en labaccount nodig om aan de slag te gaan. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint. Zodra u een Azure-abonnement hebt, u een nieuw labaccount maken in Azure Lab Services. Zie [Zelfstudie om een labaccount in te stellen voor](tutorial-setup-lab-account.md)meer informatie over het maken van een nieuw labaccount.  U ook een bestaand labaccount gebruiken.

Volg de [instelvoor klaslokalen](tutorial-setup-classroom-lab.md) om een nieuw lab te maken en pas vervolgens de volgende instellingen toe:

| Grootte van de virtuele machine | Installatiekopie |
| -------------------- | ----- |
| Medium (geneste virtualisatie) | Windows Server 2019 Datacenter |

## <a name="template-machine-configuration"></a>Sjabloonmachineconfiguratie

Wanneer de sjabloonmachine is gemaakt, [start u de machine en maakt u verbinding met de machine om](how-to-create-manage-template.md#update-a-template-vm) de volgende taken te voltooien:

1. Functie Hyper-V toevoegen
2. Java downloaden en installeren.  
3. Visual Studio Emulator voor Android downloaden en installeren.
4. Download en installeer Android Studio.
5. Visual Studio Emulator configureren voor Android Studio.

## <a name="add-hyper-v-role"></a>Functie Hyper-V toevoegen

Hyper-V moet ingeschakeld zijn voor de succesvolle installatie van Visual Studio Emulator voor Android.  Volg instructies in het [inschakelen van geneste virtualisatie in een sjabloonartikel](how-to-enable-nested-virtualization-template-vm.md) voor virtuele machines.

## <a name="install-java"></a>Java installeren

Android Studio vereist Java.  Volg de onderstaande stappen om de nieuwste versie van Java te downloaden.

1. Navigeer naar de [Java-downloadpagina.](https://www.java.com/download/) Klik op de **javadownloadknop.**
2. Klik op de 64-bits Windows voor Java-webpagina op de knop met het label **Akkoord en Gratis downloaden starten.**
3. Wanneer het installatieprogramma **Java Setup** wordt weergegeven, klikt u op **Installeren**.
4. Wacht tot de titel van de installatiefunctie verandert in **Java Setup – Voltooien**.  Klik **op Knop Sluiten.**

## <a name="install-visual-studio-emulator-for-android"></a>Visual Studio Emulator voor Android installeren

Om een Android-toepassing lokaal te testen, moet deze een gevirtualiseerde versie van een Android-apparaat gebruiken.  Er zijn een paar Android emulators beschikbaar die een ontwikkelaar in staat zal stellen om hun toepassing te testen vanaf hun machine.  We gebruiken Visual Studio Emulator voor Android omdat het een emulator is die geneste virtualisatie ondersteunt.  Omdat de Lab Service VM al een virtuele machine is, hebben we een emulator nodig die geneste virtualisatie ondersteunt.  De ingebouwde emulator voor Android Studio biedt geen ondersteuning voor geneste virtualisatie.  Zie [hardwareversnelling voor emulatorprestaties (Hyper-V & HAXM)](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/hardware-acceleration)om te zien welke emulators geneste virtualisatie ondersteunen.

Gebruik de volgende instructies om Visual Studio Emulator voor Android te downloaden en te installeren.

1. Navigeer naar de startpagina [van Visual Studio Emulator voor Android.](https://visualstudio.microsoft.com/vs/msft-android-emulator/)
2. Klik **op de emulatorknop downloaden.**
3. Wanneer vs_emulatorsetup.exe is gedownload, voert u de uitvoerbare uit.
4. Wanneer het dialoogvenster Opstelling van Visual Studio wordt weergegeven, klikt u op de knop **Installeren.**
5. Wacht tot de installatie is voltooid.  Klik **op** de knop Nu opnieuw starten om de computer opnieuw op te starten en de installatie af te ronden.

Start de emulator eerst voordat je je toepassing implementeert met Android Studio.  Zie [Visual Studio Emulator voor Android-documentatie voor](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-emulator-for-android)meer informatie over de Visual Studio Emulator voor Android.

## <a name="install-android-studio"></a>Android Studio installeren

Volg onderstaande instructies om [Android Studio](https://developer.android.com/studio)te downloaden en te installeren.

1. Navigeer naar [de downloadpagina van Android Studio](https://developer.android.com/studio#downloads).  
    > [!NOTE]
    > Internet Explorer wordt niet ondersteund door deze site.
2. Klik op het Windows(64-bits) uitvoerbare Android Studio-pakket.
3. Lees de juridische voorwaarden geschreven in de pop-up.  Wanneer u klaar bent om door te gaan, controleer **ik ik heb gelezen en ga akkoord met de bovenstaande algemene voorwaarden** selectievakje en klik op de **knop Android Studio downloaden voor Windows.**
4. Eenmaal op de Android Studio setup executable is gedownload, voer de uitvoerbare.
5. Klik op de pagina **Welkom bij Android Studio Setup** van het installatieprogramma Van Android Studio **Setup** op **Volgende**.
6. Klik op de pagina **Configuratie-instellingen** op **Volgende**.
7. Klik op de pagina **Menumap kiezen op** **Installeren**.
8. Wacht tot de installatie is voltooid.
9. Klik op de pagina **Installatievoltooid** op **Volgende**.
10. Ga naar de pagina **Android Studio-instellingen invullen.**  Klik op **Voltooien**.
11. Android Studio start automatisch nadat de installatie is voltooid.
12. Selecteer in het dialoogvenster **Android-instellingen importeren uit...** de optie **Instellingen niet importeren**. Klik op **OK**.
13. Klik op de **welkomstpagina** van de **wizard Setup van Android Studio**op **Volgende**.
14. Kies op de pagina **Type installeren** de optie **Standaard**. Klik op **Volgende**.
15. Selecteer op de pagina **UI-thema** selecteren het gewenste thema. Klik op **Volgende**.
16. Klik **op** de pagina Instellingen verifiëren op **Volgende**.
17. Wacht op de pagina **Componenten downloaden** totdat alle onderdelen zijn gedownload.  Klik op **Voltooien**.

    > [!IMPORTANT]
    > De verwachting is dat de HAXM-installatie uitvalt.  HAXM ondersteunt geen geneste virtualisatie, daarom hebben we Visual Studio Emulator voor Android eerder in dit artikel geïnstalleerd.

18. Het dialoogvenster **Welkom bij Android Studio** wordt weergegeven wanneer de wizard Setup is voltooid.

## <a name="configure-android-studio-and-visual-studio-emulator-for-android"></a>Android Studio en Visual Studio Emulator configureren voor Android

Android Studio is bijna klaar voor gebruik.  We moeten visual studio emulator voor Android nog steeds vertellen waar de Android SDK is geïnstalleerd.  Dit zorgt ervoor dat emulators die in Visual Studio voor Android worden uitgevoerd, worden weergegeven als implementatiedoelen voor het debuggen van Android Studio.

We moeten een specifieke registersleutel instellen om Visual Studio Emulator voor Android te vertellen waar de Android Sdk zich bevindt.  Voer het script hieronder uit om de benodigde registersleutel in te stellen.  Het PowerShell-script hieronder gaat uit van de standaardinstallatielocatie voor de Android Sdk.  Als u uw Android Sdk op een `$androidSdkPath` andere locatie hebt geïnstalleerd, wijzigt u de waarde voor voordat u het script uitvoert.

```powershell
$androidSdkPath = Resolve-Path $(Join-Path "$($env:APPDATA)" "../Local/Android/Sdk")

$registryKeyPath = "HKLM:Software\WOW6432NODE\Android Sdk Tools"
New-Item -Path $registryKeyPath
New-ItemProperty -Path $registryKeyPath -Name Path -PropertyType String -Value $androidSdkPath
```

> [!IMPORTANT]
> Start Visual Studio Emulator opnieuw op voor Android en Android Studio, zodat de nieuwe instelling wordt gebruikt.

Start de versie die je nodig hebt in de Visual Studio Emulator.  Het wordt weergegeven als een implementatiedoel voor je Android-app in Android-studio.  De minimale versie voor het Android Studio-project moet de versie ondersteunen die wordt uitgevoerd in de Visual Studio Emulator voor Android.  Nu bent u klaar om projecten te maken en te debuggen met Android Studio en Visual Studio Emulator voor Android.  Als je problemen hebt, zie Android emulator probleemoplossing.

## <a name="cost"></a>Kosten

Als u de kosten van dit lab wilt inschatten, u het onderstaande voorbeeld volgen.
Voor een klas van 25 studenten met 20 uur geplande lestijd en 10 uur quotum voor huiswerk of opdrachten, zou de prijs voor het lab  

25 \* studenten (20 geplande + 10 quota) uur * 55 Lab Units * 0,01 USD per uur = 412,5 USD

Meer informatie over prijzen, zie [Azure Lab Services Pricing](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Volgende stappen

De volgende stappen zijn gebruikelijk voor het opzetten van een lab.

- [Een sjabloon maken en beheren](how-to-create-manage-template.md)
- [Gebruikers toevoegen](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quotum instellen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Een schema instellen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Koppelingen naar e-mailregistratie naar studenten](how-to-configure-student-usage.md#send-invitations-to-users)
