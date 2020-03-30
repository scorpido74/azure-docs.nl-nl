---
title: De Azure-monitor voor de afhankelijkheidsagent van VM's upgraden
description: In dit artikel wordt beschreven hoe u de Azure Monitor voor VMs Dependency-agent upgraden met behulp van opdrachtregel, installatiewizard en andere methoden.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: c55bee9880c4134f2e304a7fc5176225477fe5f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480756"
---
# <a name="how-to-upgrade-the-azure-monitor-for-vms-dependency-agent"></a>De Azure-monitor voor de afhankelijkheidsagent van VM's upgraden

Na de eerste implementatie van de Azure Monitor for VMs Dependency agent worden updates uitgebracht die bugfixes of ondersteuning van nieuwe functies of functionaliteit bevatten.  In dit artikel u inzicht krijgen in de beschikbare methoden en hoe u de upgrade handmatig of via automatisering uitvoeren.

## <a name="upgrade-options"></a>Upgradeopties 

De afhankelijkheidsagent voor Windows en Linux kan handmatig of automatisch worden geüpgraded naar de nieuwste release, afhankelijk van het implementatiescenario en de omgeving waarin de machine wordt uitgevoerd. De volgende methoden kunnen worden gebruikt om de agent te upgraden.

|Omgeving |Installatiemethode |Upgrademethode |
|------------|--------------------|---------------|
|Azure VM | Afhankelijkheidsagent VM-extensie voor [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) en [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) | Agent wordt standaard automatisch geüpgraded, tenzij u uw Azure Resource Manager-sjabloon hebt geconfigureerd om zich af te melden door de *eigenschap autoUpgradeMinorVersion* in te stellen op **false**. De upgrade voor de secundaire versie waarbij de automatische upgrade is uitgeschakeld en een grote versie-upgrade dezelfde methode volgen - verwijder en installeer de extensie opnieuw. |
| Aangepaste Azure VM-afbeeldingen | Handmatige installatie van afhankelijkheidsagent voor Windows/Linux | Het bijwerken van VM's naar de nieuwste versie van de agent moet worden uitgevoerd vanaf de opdrachtregel met het Windows-installatiepakket of linux-zelf-extraherende en installeerbare shellscriptbundel.|
| Niet-Azure VM's | Handmatige installatie van afhankelijkheidsagent voor Windows/Linux | Het bijwerken van VM's naar de nieuwste versie van de agent moet worden uitgevoerd vanaf de opdrachtregel met het Windows-installatiepakket of linux-zelf-extraherende en installeerbare shellscriptbundel. |

## <a name="upgrade-windows-agent"></a>Windows-agent bijwerken 

Als u de agent op een Windows-vm wilt bijwerken naar de nieuwste versie die niet is geïnstalleerd met de VM-extensie afhankelijkheidsagent, voert u de opdrachtprompt, het script of een andere automatiseringsoplossing uit of gebruikt u de wizard InstallatieAfhankelijkheidsagent-Windows.exe.  

U de nieuwste versie van de Windows-agent downloaden vanaf [hier.](https://aka.ms/dependencyagentwindows)

### <a name="using-the-setup-wizard"></a>De wizard Setup gebruiken

1. Meld u aan bij de computer met een account met beheerdersrechten.

2. Executeer **InstallDependencyAgent-Windows.exe** om de wizard Setup te starten.
   
3. Volg de wizard **Setup van afhankelijkheidsagent** om de vorige versie van de afhankelijkheidsagent te verwijderen en installeer vervolgens de nieuwste versie.


### <a name="from-the-command-line"></a>Vanaf de opdrachtregel

1. Meld u aan bij de computer met een account met beheerdersrechten.

2. Voer de volgende opdracht uit.

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    De `/RebootMode=manual` parameter voorkomt dat de upgrade de machine automatisch opnieuw opstart als sommige processen bestanden van de vorige versie gebruiken en er een vergrendeling op hebben. 

3. Als u wilt bevestigen dat `install.log` de upgrade is geslaagd, controleert u de informatie voor de installatie. De logboekmap is *%Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="upgrade-linux-agent"></a>Linux-agent upgraden 

Een upgrade van eerdere versies van de afhankelijkheidsagent op Linux wordt ondersteund en uitgevoerd volgens dezelfde opdracht als een nieuwe installatie.

U de nieuwste versie van de Windows-agent downloaden vanaf [hier.](https://aka.ms/dependencyagentlinux)

1. Meld u aan bij de computer met een account met beheerdersrechten.

2. Voer de volgende`sh InstallDependencyAgent-Linux64.bin -s`opdracht uit als hoofd . 

Als de afhankelijkheidsagent niet wordt gestart, controleert u de logboeken op gedetailleerde foutgegevens. Op Linux-agents is de logmap */var/opt/microsoft/dependency-agent/log.* 

## <a name="next-steps"></a>Volgende stappen

Zie [Bewaking van uw VM's uitschakelen in Azure Monitor voor VM's](vminsights-optout.md)als u de bewaking van uw VM's voor een bepaalde tijd wilt stoppen of Azure Monitor voor VM's volledig wilt verwijderen.
