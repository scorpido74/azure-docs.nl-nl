---
title: Een Ethical Hacking-lab opzetten met Azure Lab Services | Microsoft Documenten
description: Meer informatie over het opzetten van een lab met Azure Lab Services om ethisch hacken aan te leren.
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
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 2b600edc4c360a2b2990be34e44bb8fbd1c8f721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133173"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>Een lab opzetten om ethische hackingklasse te onderwijzen 
Dit artikel laat zien hoe je het opzetten van een klasse die zich richt op forensische kant van ethische hacking. Penetratie testen, een praktijk die wordt gebruikt door de ethische hacking gemeenschap, treedt op wanneer iemand probeert om toegang te krijgen tot het systeem of netwerk om kwetsbaarheden die een kwaadwillende aanvaller kan exploiteren aan te tonen. 

In een ethische hacking klasse, studenten kunnen leren moderne technieken voor de verdediging tegen kwetsbaarheden. Elke student krijgt een Windows Server host virtuele machine die twee geneste virtuele machines heeft - een virtuele machine met [Metasploitable3](https://github.com/rapid7/metasploitable3) beeld en een andere machine met [Kali Linux-afbeelding.](https://www.kali.org/) De Metasploitable virtuele machine wordt gebruikt voor het exploiteren van doeleinden en Kali virtuele machine biedt toegang tot de tools die nodig zijn om forensische taken uit te voeren.

Dit artikel bevat twee hoofdsecties. Het eerste deel gaat over het maken van het klaslab. Het tweede deel gaat over het maken van de sjabloonmachine met geneste virtualisatie ingeschakeld en met de benodigde gereedschappen en afbeeldingen. In dit geval, een Metasploitable beeld en een Kali Linux-afbeelding op een machine die Hyper-V ingeschakeld om de beelden te hosten heeft.

## <a name="lab-configuration"></a>Labconfiguratie
Om dit lab in te stellen, hebt u een Azure-abonnement nodig om aan de slag te gaan. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint. Zodra u een Azure-abonnement hebt, u een nieuw labaccount maken in Azure Lab Services of een bestaand account gebruiken. Zie de volgende zelfstudie voor het maken van een nieuw labaccount: [Zelfstudie om een labaccount in te stellen.](tutorial-setup-lab-account.md)

Volg [deze zelfstudie](tutorial-setup-classroom-lab.md) om een nieuw lab te maken en pas vervolgens de volgende instellingen toe:

| Grootte van de virtuele machine | Installatiekopie |
| -------------------- | ----- | 
| Medium (geneste virtualisatie) | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>Sjabloonmachine 

Nadat de sjabloonmachine is gemaakt, start u de machine en sluit u deze aan om de volgende drie belangrijke taken te voltooien. 
 
1. Stel de machine in voor geneste virtualisatie. Het maakt alle geschikte windows-functies, zoals Hyper-V, en stelt het netwerk voor de Hyper-V beelden in staat om te kunnen communiceren met elkaar en het internet.
2. Stel de [Kali](https://www.kali.org/) Linux-afbeelding in. Kali is een Linux-distributie die tools bevat voor penetratietesten en beveiligingscontrole.
3. Stel de Metasploitable afbeelding in. Voor dit voorbeeld wordt de [Metasploitable3-afbeelding](https://github.com/rapid7/metasploitable3) gebruikt. Deze afbeelding is gemaakt om opzettelijk beveiligingsproblemen te hebben.

Een script dat de hierboven beschreven taken automatiseert is beschikbaar bij [Lab Services Ethical Hacking Scripts.](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking)

### <a name="prepare-template-machine-for-nested-virtualization"></a>Sjabloonmachine voorbereiden op geneste virtualisatie
Volg instructies in [dit artikel](how-to-enable-nested-virtualization-template-vm.md) om uw sjabloon virtuele machine voor te bereiden op geneste virtualisatie. 

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Een geneste virtuele machine instellen met Kali Linux Image
Kali is een Linux-distributie die tools bevat voor penetratietesten en beveiligingscontrole.

1. Afbeelding downloaden [https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/)van .  
    1. Download de **Kali Linux Hyper-V 64 Bit** voor Hyper-V.
    1. Haal het .7z-bestand eruit.  Als je nog geen 7 zip hebt, download het dan van [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html). Onthoud de locatie van de uitgepakte map zoals u deze later nodig hebt.
2. Open **Hyper-V Manager** van Administratieve Tools.
1. Selecteer **Actie**en selecteer **Virtuele machine importeren**. 
1. Kies op de pagina **Map zoeken van** de wizard Virtuele machine **importeren** de locatie van de uitgepakte map met de Kali Linux-afbeelding.

    ![Dialoogvenster Map zoeken](../media/class-type-ethical-hacking/locate-folder.png)
1. Selecteer op de pagina **Virtuele machine selecteren** de Kali Linux-afbeelding.  In dit geval is het beeld **kali-linux-2019.3-hyperv**.

    ![Kali-afbeelding selecteren](../media/class-type-ethical-hacking/select-kali-image.png)
1.  Selecteer op de pagina **Type importeren kiezen** de optie De virtuele machine kopiëren **(een nieuwe unieke ID maken).**

    ![Importtype kiezen](../media/class-type-ethical-hacking/choose-import-type.png)
1. Accepteer de standaardinstellingen voor **Mappen kiezen voor virtuele machinebestanden** en Mappen kiezen om virtuele harde schijven-pagina's **op te slaan** en selecteer **Volgende**.
1. Kies op de pagina **Netwerk verbinding** de optie **LabServicesSwitch** die eerder is gemaakt in de sectie **Sjabloon voorbereiden voor geneste virtualisatie** van dit artikel en selecteer **Volgende**.

    ![Netwerkpagina verbinden](../media/class-type-ethical-hacking/connect-network.png)
1. Selecteer **Voltooien** op de **overzichtspagina.** Wacht tot kopieer- en importbewerkingen zijn voltooid. De Kali Linux virtuele machine zal nu beschikbaar zijn in Hyper-V.
1. Kies **vanuit Hyper-V Manager** **Action** -> **Start**en kies **Action** -> **Connect** om verbinding te maken met de virtuele machine.  
12. De standaardgebruikersnaam `root` is en `toor`het wachtwoord is . 

    > [!NOTE]
    > Als u de afbeelding wilt ontgrendelen, drukt u op de Ctrl-toets en sleept u de muis naar boven.

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>Een geneste vm instellen met metasploitable image  
De Rapid7 Metasploitable afbeelding is een afbeelding die met opzet is geconfigureerd met beveiligingsproblemen. U gebruikt deze afbeelding om problemen te testen en te vinden. In de volgende instructies ziet u hoe u een vooraf gemaakte Metasploitable-afbeelding gebruiken. Als er echter een nieuwere versie van de Metasploitable-afbeelding nodig is, zie [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3).

1. Navigeer [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html)naar . Vul het formulier in om de afbeelding te downloaden en selecteer de knop **Verzenden.**
1. Selecteer de knop **Nu downloaden metasploitable.**
1. Wanneer het zip-bestand wordt gedownload, haalt u het zip-bestand uit en onthoudt u de locatie.
1. Converteer het uitgepakte vmdk-bestand naar een vhdx-bestand, zodat u gebruiken met Hyper-V. Open Hiervoor PowerShell met beheerdersbevoegdheden en navigeer je naar de map waar het vmdk-bestand zich bevindt en volg je de volgende instructies:
    1. Download de [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497)en voer mvmc_setup.msi-bestand uit wanneer u daarom wordt gevraagd.
    1. Importeer de PowerShell-module.  De standaardlocatie waar de module is geïnstalleerd, is C:\Program Files\Microsoft Virtual Machine Converter\

        ```powershell
        Import-Module 'C:\Program Files\Microsoft Virtual Machine Converter\MvmcCmdlet.psd1'
        ```
    1. Converteer de vmdk naar een vhd-bestand dat kan worden gebruikt door Hyper-V. Deze bewerking kan enkele minuten duren.
    
        ```powershell
        ConvertTo-MvmcVirtualHardDisk -SourceLiteralPath .\Metasploitable.vmdk -DestinationLiteralPath .\Metasploitable.vhdx -VhdType DynamicHardDisk -VhdFormat vhdx
        ```
    1. Kopieer de nieuw gemaakte metasploitable.vhdx naar C:\Users\Public\Documents\Hyper-V\Virtual Hard Disks\. 
1. Maak een nieuwe Hyper-V virtuele machine.
    1. Open **Hyper-V Manager**.
    1. Kies **Action** -> **New** -> Virtual**Machine**.
    1. Klik **op** de pagina Voor het begin van de wizard Nieuwe **virtuele machine**op **Volgende**.
    1. Voer op de pagina **Naam en locatie opgeven** in **Metasploitable** voor de **naam**in en selecteer **Volgende**.

        ![Wizard Nieuwe vm-afbeelding](../media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. Accepteer **op** de pagina Generatie opgeven de standaardinstellingen en selecteer **Volgende**.
    1. Voer **op** de pagina Geheugen toewijzen **512 MB** in voor het **opstartgeheugen**en selecteer **Volgende**. 

        ![Geheugenpagina toewijzen](../media/class-type-ethical-hacking/assign-memory-page.png)
    1. Laat op de pagina **Netwerk configureren** de verbinding als Niet **verbonden .** U stelt de netwerkadapter later in.
    1. Selecteer op de pagina **Virtuele harde schijf verbinden** de optie Een bestaande virtuele harde schijf **gebruiken**. Blader naar de locatie voor het **metasploitable.vhdx-bestand** dat in de vorige stap is gemaakt en selecteer **Volgende**. 

        ![Pagina virtuele netwerkschijf verbinden](../media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. Selecteer op de wizard **Nieuwe virtuele machine voltooien** en selecteer **Voltooien**.
    1. Zodra de virtuele machine is gemaakt, selecteert u deze in de Hyper-V Manager. Zet de machine nog niet aan.  
    1. Kies -> **Actie-instellingen**. **Action**
    1. Selecteer **Hardware toevoegen**in het dialoogvenster Instellingen **voor metasploitable** . 
    1. Selecteer **Verouderde netwerkadapter**en selecteer **Toevoegen**.

        ![Pagina netwerkadapter](../media/class-type-ethical-hacking/network-adapter-page.png)
    1. Selecteer op de pagina **Verouderde netwerkadapter** **labservicesswitch** voor de instelling **Virtuele switch** en selecteer **OK**. LabServicesSwitch is gemaakt bij de voorbereiding van de sjabloonmachine voor Hyper-V in de sectie **Sjabloon voorbereiden voor geneste virtualisatie.**

        ![Pagina Verouderde netwerkadapter](../media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. De Metasploitable afbeelding is nu klaar voor gebruik. Kies **vanuit Hyper-V Manager** **Action** -> **Start**en kies **Action** -> **Connect** om verbinding te maken met de virtuele machine.  De standaardgebruikersnaam is **msfadmin** en het wachtwoord is **msfadmin**. 


De sjabloon is nu bijgewerkt en heeft beelden die nodig zijn voor een ethische hacking penetratie testen klasse, een afbeelding met tools om de penetratie testen en een ander beeld met beveiligingsproblemen te ontdekken. De sjabloonafbeelding kan nu worden gepubliceerd in de klasse. Selecteer de knop **Publiceren** op de sjabloonpagina om de sjabloon naar het lab te publiceren.
  

## <a name="cost"></a>Kosten  
Als u de kosten van dit lab wilt schatten, u het volgende voorbeeld gebruiken: 
 
Voor een klas van 25 studenten met 20 uur geplande lestijd en 10 uur quotum voor huiswerk of opdrachten, zou de prijs voor het lab zijn: 

25 studenten * (20 + 10) uur * 55 Lab Units * 0,01 USD per uur = 412,50 USD. 

Zie [Azure Lab Services-prijzen](https://azure.microsoft.com/pricing/details/lab-services/)voor meer informatie over prijzen.

## <a name="conclusion"></a>Conclusie
Dit artikel liep je door de stappen om een lab voor ethische hacking klasse te creëren. Het bevat stappen voor het instellen van geneste virtualisatie voor het maken van twee virtuele machines in de virtuele machine van de host voor het penetreren van tests.

## <a name="next-steps"></a>Volgende stappen
De volgende stappen zijn gebruikelijk voor het instellen van een lab:

- [Gebruikers toevoegen](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quotum instellen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Een schema instellen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Links naar registratie via e-mail naar studenten](how-to-configure-student-usage.md#send-invitations-to-users). 

