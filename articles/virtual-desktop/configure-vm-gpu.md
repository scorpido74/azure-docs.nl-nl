---
title: GPU configureren voor Windows Virtual Desktop - Azure
description: Gpu-versnelde rendering en codering inschakelen in Windows Virtual Desktop.
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: 8b675a78041b68210fa7583510582783c506c720
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767040"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>GPU-versnelling (graphics processing unit) configureren voor Windows Virtual Desktop

Windows Virtual Desktop ondersteunt GPU-versnelde rendering en codering voor verbeterde app-prestaties en schaalbaarheid. GPU-versnelling is vooral cruciaal voor grafisch intensieve apps.

Volg de instructies in dit artikel om een gpu-geoptimaliseerde Azure-virtuele machine te maken, deze toe te voegen aan uw hostpool en configureer deze om GPU-versnelling te gebruiken voor rendering en codering. In dit artikel wordt ervan uitgegaan dat u al een Windows Virtual Desktop-tenant hebt geconfigureerd.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Een GPU-geoptimaliseerde Azure-virtuele machineformaat selecteren

Azure biedt een aantal [GPU geoptimaliseerde virtuele machineformaten.](/azure/virtual-machines/windows/sizes-gpu) De juiste keuze voor uw hostpool is afhankelijk van een aantal factoren, waaronder uw specifieke app-workloads, de gewenste kwaliteit van de gebruikerservaring en de kosten. Over het algemeen bieden grotere en capabeler gpu's een betere gebruikerservaring bij een bepaalde gebruikersdichtheid.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Een hostgroep maken, uw virtuele machine inrichten en een app-groep configureren

Maak een nieuwe hostgroep met een VM van de geselecteerde grootte. Zie [Zelfstudie: Een hostgroep maken met Azure Marketplace](/azure/virtual-desktop/create-host-pools-azure-marketplace)voor instructies.

Windows Virtual Desktop ondersteunt GPU-versnelde rendering en codering in de volgende besturingssystemen:

* Windows 10 versie 1511 of nieuwer
* Windows Server 2016 of hoger

U moet ook een app-groep configureren of de standaardgroep voor bureaublad-apps (met de naam 'Bureaubladtoepassingsgroep') gebruiken die automatisch wordt gemaakt wanneer u een nieuwe hostgroep maakt. Zie [Zelfstudie: App-groepen voor Windows Virtueel bureaublad beheren voor](/azure/virtual-desktop/manage-app-groups)instructies.

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Ondersteunde grafische stuurprogramma's installeren in uw virtuele machine

Als u wilt profiteren van de GPU-mogelijkheden van Azure N-serie VM's in Windows Virtual Desktop, moet u de juiste grafische stuurprogramma's installeren. Volg de instructies bij [ondersteunde besturingssystemen en stuurprogramma's](/azure/virtual-machines/windows/sizes-gpu#supported-operating-systems-and-drivers) om stuurprogramma's van de juiste grafische leverancier te installeren, handmatig of met behulp van een Azure VM-extensie.

Alleen stuurprogramma's die door Azure worden gedistribueerd, worden ondersteund voor Windows Virtual Desktop. Bovendien worden voor Azure VM's met NVIDIA GPU's alleen [NVIDIA GRID-stuurprogramma's](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) ondersteund voor Windows Virtual Desktop.

Na de installatie van het stuurprogramma is een vm-herstart vereist. Gebruik de verificatiestappen in de bovenstaande instructies om te controleren of grafische stuurprogramma's zijn geïnstalleerd.

## <a name="configure-gpu-accelerated-app-rendering"></a>GPU-versnelde app-rendering configureren

Apps en desktops die in configuraties met meerdere sessies worden uitgevoerd, worden standaard weergegeven met de CPU en maken geen gebruik van beschikbare GPU's voor rendering. Groepsbeleid configureren voor de sessiehost om GPU-versnelde rendering in te schakelen:

1. Maak verbinding met het bureaublad van de VM met een account met lokale beheerdersbevoegdheden.
2. Open het menu Start en typ 'gpedit.msc' om de groepsbeleidseditor te openen.
3. Navigeer door de structuur naar **computerconfiguratiebeheersjablonen** > **Administrative Templates** > **Windows Components** > **Remote Desktop Services** > **Remote Desktop Session Host** > **Remote Session Environment**.
4. Selecteer beleid **Gebruik de standaardgrafische adapter voor de hardware voor alle sessies met Extern bureaublad-services** en stel dit beleid in **op Ingeschakeld** om GPU-rendering in de externe sessie in te schakelen.

## <a name="configure-gpu-accelerated-frame-encoding"></a>GPU-versnelde framecodering configureren

Extern bureaublad codeert alle afbeeldingen die worden weergegeven door apps en desktops (of deze nu worden weergegeven met GPU of met CPU) voor overdracht naar Extern bureaublad-clients. Extern bureaublad maakt standaard geen gebruik van beschikbare GPU's voor deze codering. Groepsbeleid configureren voor de sessiehost om GPU-versnelde framecodering in te schakelen. Voortzetting van de bovenstaande stappen:

1. Selecteer beleid **Geef prioriteit aan de H.264/AVC 444 Grafische modus voor verbindingen met extern bureaublad** en stel dit beleid in op **Ingeschakeld** om H.264/AVC 444-codec in de externe sessie te forceren.
2. Selecteer beleid **Configureer H.264/AVC-hardwarecodering voor extern bureaublad-verbindingen** en stel dit beleid in **op Ingeschakeld** om hardwarecodering voor AVC/H.264 in de externe sessie in te schakelen.

    >[!NOTE]
    >Stel in Windows Server 2016 de optie **AVC-hardwarecodering boven** **Altijd proberen in.**

3. Nu het groepsbeleid is bewerkt, dwingt u een groepsbeleidsupdate af. Open de opdrachtprompt en typ:

    ```batch
    gpupdate.exe /force
    ```

4. Meld u af voor de sessie Extern bureaublad.

## <a name="verify-gpu-accelerated-app-rendering"></a>Het renderen van GPU-versnelde apps verifiëren

Als u wilt controleren of apps de GPU gebruiken voor rendering, probeert u een van de volgende opties:

* Voor Azure VM's met een `nvidia-smi` NVIDIA GPU gebruikt u het hulpprogramma zoals beschreven in [De installatie van stuurprogramma's verifiëren](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) om te controleren op gpu-gebruik bij het uitvoeren van uw apps.
* Bij ondersteunde versies van het besturingssysteem u taakbeheer gebruiken om te controleren op gpu-gebruik. Selecteer de GPU op het tabblad Prestaties om te zien of apps de GPU gebruiken.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Controleren GPU-versnelde framecodering

Ga als volgt te werk om te controleren of Extern bureaublad GPU-versnelde codering gebruikt:

1. Maak verbinding met het bureaublad van de VM met windows Virtual Desktop-client.
2. Start de Logboeken en navigeer naar het volgende knooppunt: **Logboeken** > **van Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreCDV** > **operationeel**
3. Als u wilt bepalen of GPU-versnelde codering wordt gebruikt, zoekt u naar gebeurtenis-ID 170. Als u "AVC-hardwareencoder ingeschakeld: 1" ziet, wordt GPU-codering gebruikt.
4. Als u wilt bepalen of de AVC 444-modus wordt gebruikt, zoekt u naar gebeurtenis-id 162. Als u "AVC Beschikbaar: 1 eerste profiel: 2048" ziet, wordt AVC 444 gebruikt.

## <a name="next-steps"></a>Volgende stappen

Deze instructies moeten u aan de slag met GPU-versnelling op één sessiehost (één VM). Enkele aanvullende overwegingen voor het inschakelen van GPU-versnelling in een grotere hostpool:

* Overweeg een [VM-extensie](/azure/virtual-machines/extensions/overview) te gebruiken om de installatie en updates van stuurprogramma's in een aantal VM's te vereenvoudigen. Gebruik de [NVIDIA GPU Driver Extension](/azure/virtual-machines/extensions/hpccompute-gpu-windows) voor VM's met NVIDIA GPU's en gebruik de AMD GPU Driver Extension (binnenkort beschikbaar) voor VM's met AMD GPU's.
* Overweeg Active Directory-groepsbeleid te gebruiken om de configuratie van groepsbeleid voor een aantal VM's te vereenvoudigen. Zie [Werken met groepsbeleidsobjecten](https://go.microsoft.com/fwlink/p/?LinkId=620889)voor informatie over het implementeren van groepsbeleid in het Active Directory-domein.
