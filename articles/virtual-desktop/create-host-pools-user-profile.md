---
title: Windows Virtual Desktop FSLogix-profielcontainershare - Azure
description: Een FSLogix-profielcontainer instellen voor een Windows Virtual Desktop-hostgroep met behulp van een bestandsshare op basis van virtuele machines.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 96b593f544aa4bbf126c06747a01902581f5ffb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250918"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>Een profielcontainer maken voor een hostpool met behulp van een bestandsshare

De Windows Virtual Desktop-service biedt FSLogix-profielcontainers als de aanbevolen gebruikersprofieloplossing. We raden het gebruik van de UPD-oplossing (User Profile Disk) af, die wordt afgeschaft in toekomstige versies van Windows Virtual Desktop.

In dit artikel vindt u hoe u een FSLogix-profielcontainershare instelt voor een hostgroep met behulp van een virtuele machinegebaseerde bestandsshare. Zie de [FSLogix-site](https://docs.fslogix.com/)voor meer FSLogix-documentatie.

>[!NOTE]
>Zie Opslagopties voor [FSLogix-profielcontainers](store-fslogix-profile.md)als u op zoek bent naar vergelijkingsmateriaal over de verschillende opslagopties voor FSLogix-profiel .

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Een nieuwe virtuele machine maken die fungeert als bestandsshare

Wanneer u de virtuele machine maakt, moet u deze op hetzelfde virtuele netwerk plaatsen als virtuele machines van de hostpool of op een virtueel netwerk dat verbinding heeft met virtuele machines voor de hostpool. U een virtuele machine op meerdere manieren maken:

- [Een virtuele machine maken op basis van een Azure Gallery-afbeelding](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Een virtuele machine maken op basis van een beheerde afbeelding](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Een virtuele machine maken op basis van een niet-beheerde afbeelding](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

Nadat u de virtuele machine hebt gemaakt, u deze aansluiten bij het domein door de volgende dingen te doen:

1. [Maak verbinding met de virtuele machine](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) met de referenties die u hebt opgegeven bij het maken van de virtuele machine.
2. Start op de virtuele machine het **Configuratiescherm** en selecteer **Systeem**.
3. Selecteer **Computernaam**, selecteer **Instellingen wijzigen**en selecteer **Wijzigen...**
4. Selecteer **Domein** en voer het Active Directory-domein in op het virtuele netwerk.
5. Verifieer met een domeinaccount met bevoegdheden voor machines die lid zijn van het domein.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>De virtuele machine voorbereiden om te fungeren als bestandsshare voor gebruikersprofielen

Hieronder volgen algemene instructies voor het voorbereiden van een virtuele machine om als bestandsshare voor gebruikersprofielen op te treden:

1. Voeg de Active Directory-gebruikers van Windows Virtual Desktop toe aan een [Active Directory-beveiligingsgroep](/windows/security/identity-protection/access-control/active-directory-security-groups/). Deze beveiligingsgroep wordt gebruikt om de Windows Virtual Desktop-gebruikers te verifiëren bij de virtuele machine voor bestandsshare die u zojuist hebt gemaakt.
2. [Maak verbinding met de virtuele machine voor bestandsshare](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine).
3. Maak op de virtuele machine voor het delen van bestanden een map op het **C-station** die wordt gebruikt als profielaandeel.
4. Klik met de rechtermuisknop op de nieuwe map, selecteer **Eigenschappen,** selecteer **Delen**en selecteer **Geavanceerd delen...**.
5. Selecteer **Deze map delen**, selecteer **Machtigingen...**, selecteer vervolgens **Toevoegen...**.
6. Zoek naar de beveiligingsgroep waaraan u de Windows Virtual Desktop-gebruikers hebt toegevoegd en zorg ervoor dat de groep **volledige controle**heeft.
7. Nadat u de beveiligingsgroep hebt toegevoegd, klikt u met de rechtermuisknop op de map, selecteert u **Eigenschappen**, selecteert **u Delen**en kopieert u het **netwerkpad** dat u later wilt gebruiken.

Zie de [FSLogix-documentatie voor](/fslogix/fslogix-storage-config-ht/)meer informatie over machtigingen.

## <a name="configure-the-fslogix-profile-container"></a>De profielcontainer FSLogix configureren

Ga als volgt te werk om de virtuele machines te configureren met de FSLogix-software:

1. [Maak verbinding met de virtuele machine](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) met de referenties die u hebt opgegeven bij het maken van de virtuele machine.
2. Start een internetbrowser en navigeer naar [deze link](https://go.microsoft.com/fwlink/?linkid=2084562) om de FSLogix-agent te downloaden.
3. Navigeer naar \\ \\Win32\\ \\ \\Release of\\X64 Release in het .zip-bestand en voer **FSLogixAppsSetup** uit om de FSLogix-agent te installeren.  Zie FSLogix downloaden en installeren voor meer informatie over het installeren van [FSLogix.](/fslogix/install-ht/)
4. Navigeer naar **Programmabestanden** > **FSLogix** > **Apps** om de installatie van de agent te bevestigen.
5. Voer **RegEdit** in het startmenu uit als beheerder. Navigeer naar **computerHKEY_LOCAL_MACHINE\\\\software\\FSLogix**.
6. Een sleutel met de naam **Profielen maken**.
7. Maak de volgende waarden voor de toets Profielen:

| Name                | Type               | Gegevens/waarde                        |
|---------------------|--------------------|-----------------------------------|
| Ingeschakeld             | DWORD              | 1                                 |
| VHDLocaties        | Waarde voor meerdere tekenreeksen | "Netwerkpad voor bestandsshare"     |

>[!IMPORTANT]
>Om uw Windows Virtual Desktop-omgeving in Azure te beveiligen, raden we u aan de binnenkomende poort 3389 niet op uw VM's te openen. Voor Windows Virtual Desktop is geen open inkomende poort 3389 vereist voor gebruikers om toegang te krijgen tot de VM's van de hostgroep. Als u poort 3389 moet openen voor probleemoplossingsdoeleinden, raden we u aan [just-in-time VM-toegang te](../security-center/security-center-just-in-time.md)gebruiken.
