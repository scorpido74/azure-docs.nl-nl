---
title: Een Scriptinglab voor Linux-shell instellen met Azure Lab Services | Microsoft Documenten
description: Meer informatie over het opzetten van een lab om shell scripting op Linux te leren.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 100a485588c77f6977001dae984b30ebcb1de557
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443547"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>Een lab opzetten om shell scripting op Linux te leren
Dit artikel laat zien hoe je een lab op te zetten om shell scripting te leren op Linux. Scripting is een handig onderdeel van systeembeheer waarmee beheerders repetitieve taken kunnen vermijden. In dit voorbeeldscenario omvat de klasse traditionele bashscripts en verbeterde scripts. Verbeterde scripts zijn scripts die bash-commando's en Ruby combineren. Deze aanpak stelt Ruby in staat om gegevens door te geven en commando's te bashen om met de shell te communiceren. 

Studenten die deze scripting lessen krijgen een Linux virtuele machine om de basisprincipes van Linux te leren, en ook vertrouwd te raken met de bash shell scripting. De Linux virtuele machine wordt geleverd met remote desktop toegang ingeschakeld en met [gedit](https://help.gnome.org/users/gedit/stable/) en [Visual Studio Code](https://code.visualstudio.com/) tekst editors geïnstalleerd.

## <a name="lab-configuration"></a>Labconfiguratie
Om dit lab in te stellen, hebt u een Azure-abonnement nodig om aan de slag te gaan. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint. Zodra u een Azure-abonnement hebt, u een nieuw labaccount maken in Azure Lab Services of een bestaand labaccount gebruiken. Zie de volgende zelfstudie voor het maken van een nieuw labaccount: [Zelfstudie om een Lab-account op te zetten](tutorial-setup-lab-account.md).

Nadat u het labaccount hebt gemaakt, schakelt u de volgende instellingen in het labaccount in: 

| Lab-accountinstelling | Instructies |
| ----------- | ------------ |  
| Marketplace-afbeeldingen | Schakel de [Ubuntu Server 18.04 LTS-afbeelding](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) in voor gebruik binnen uw labaccount. Zie [Marketplace-afbeeldingen opgeven die beschikbaar zijn voor makers van het lab voor](specify-marketplace-images.md)meer informatie. | 

Volg [deze zelfstudie](tutorial-setup-classroom-lab.md) om een nieuw lab te maken en de volgende instellingen toe te passen:

| Lab-instellingen | Waarde/instructies | 
| ------------ | ------------------ |
| Virtuele machine (VM) grootte | Klein  |
| VM-afbeelding | [Ubuntu Server 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) |
| Verbinding met extern bureaublad inschakelen | Inschakelen. <p>Als u deze instelling inschakelt, kunnen docenten en studenten verbinding maken met hun VM's via het externe bureaublad (RDP). Zie [Extern bureaublad inschakelen voor virtuele Linux-machines in een lab in Azure Lab Services voor](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm)meer informatie. </p>|


## <a name="install-desktop-and-xrdp"></a>Desktop en xrdp installeren
De [Ubuntu Server 18.04 LTS-afbeelding](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) heeft niet standaard een externe desktopserver geïnstalleerd. Volg instructies in het [Installatie en configureer Extern bureaublad om verbinding te maken met een Linux VM in Azure-artikel](../../virtual-machines/linux/use-remote-desktop.md) om de pakketten te installeren die nodig zijn op de sjabloonmachine om verbinding te maken via het remote desktop protocol.

## <a name="install-ruby"></a>Ruby installeren
Ruby is een open-source dynamische taal die kan worden gecombineerd met bash scripts. In dit gedeelte `apt-get` ziet u hoe u de nieuwste versie van [Ruby kunt](https://www.ruby-lang.org/)installeren.

1. Installeer updates door de volgende opdrachten uit te voeren:

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  Installeer [Ruby](https://www.ruby-lang.org/).  Ruby is een open-source dynamische taal die kan worden gecombineerd met bash scripts. 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>Ontwikkeltools installeren
In deze sectie ziet u hoe u een paar teksteditors installeert. Gedit is de standaard teksteditor voor de gnome-desktopomgeving. Het is ontworpen als een teksteditor voor algemene doeleinden. Visual Studio Code is een teksteditor met ondersteuning voor foutopsporing en integratie van bronbeheer.

> [!NOTE]
> Er zijn verschillende teksteditors beschikbaar. Visual Studio Code en gedit zijn slechts twee voorbeelden.

1. Installeer [gedit](https://help.gnome.org/users/gedit/stable/).

    ```bash
    sudo apt-get install gedit
    ```
1. Installeer [Visual Studio Code](https://code.visualstudio.com/).  Visual Studio-code kan worden geïnstalleerd met de Snap Store.  Zie Visual Studio [Code alternatieve downloads](https://code.visualstudio.com/#alt-downloads)voor alternatieve installatieopties voor alternatieve installatieopties.

    ```bash
    sudo snap install vscode --classic 
    ```

    De sjabloon is nu bijgewerkt en heeft zowel de programmeertaal als de ontwikkelingstools die nodig zijn om het lab te voltooien. De sjabloonafbeelding kan nu worden gepubliceerd in het lab. Selecteer de knop **Publiceren** op de sjabloonpagina om de sjabloon naar het lab te publiceren.  

## <a name="cost"></a>Kosten 
Als u de kosten van dit lab wilt schatten, u het volgende voorbeeld gebruiken:
 
Voor een klas van 25 studenten met 20 uur geplande lestijd en 10 uur quotum voor huiswerk of opdrachten, zou de prijs voor het lab zijn: 

25 studenten * (20 + 10) uur * 20 Lab Units * 0,01 USD per uur = 150 USD

Voor meer informatie over de prijzen vindt u in het volgende document: [Azure Lab Services Pricing](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusie
Dit artikel heeft je door de stappen gehaald om een lab voor scriptklassen te maken. Hoewel dit artikel zich richtte op het instellen van Ruby scripting tools op Linux machine, kan dezelfde setup worden gebruikt voor andere scripting klassen zoals Python op Linux.

## <a name="next-steps"></a>Volgende stappen
De volgende stappen zijn gebruikelijk voor het instellen van een lab:

- [Gebruikers toevoegen](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quotum instellen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Een schema instellen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Links naar registratie via e-mail naar studenten](how-to-configure-student-usage.md#send-invitations-to-users). 





