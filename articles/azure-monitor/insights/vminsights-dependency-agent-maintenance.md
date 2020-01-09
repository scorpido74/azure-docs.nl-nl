---
title: Een upgrade uitvoeren van de Azure Monitor voor VM's-afhankelijkheids agent | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de Azure Monitor voor VM's dependency agent bijwerkt met behulp van de opdracht regel, de installatie wizard en andere methoden.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/30/2019
ms.openlocfilehash: 548a578365b03162396fb8618718ab1e7ce5b081
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75400805"
---
# <a name="how-to-upgrade-the-azure-monitor-for-vms-dependency-agent"></a>De Azure Monitor voor VM's-afhankelijkheids agent bijwerken

Na de eerste implementatie van de Azure Monitor voor VM's-afhankelijkheids agent worden er updates uitgebracht met oplossingen voor problemen of ondersteuning van nieuwe functies of functionaliteit.  In dit artikel wordt uitgelegd hoe u de beschik bare methoden en de upgrade hand matig of via Automation kunt begrijpen.

## <a name="upgrade-options"></a>Upgrade opties 

De afhankelijkheids agent voor Windows en Linux kan hand matig worden bijgewerkt naar de meest recente versie of automatisch afhankelijk van het implementatie scenario en de omgeving waarin de computer wordt uitgevoerd. De volgende methoden kunnen worden gebruikt om de agent bij te werken.

|Omgeving |Installatie methode |Upgrade methode |
|------------|--------------------|---------------|
|Azure VM | VM-extensie van afhankelijkheids agent voor [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) en [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) | De agent wordt standaard automatisch geüpgraded tenzij u uw Azure Resource Manager-sjabloon hebt geconfigureerd om u af te melden door de eigenschap *autoUpgradeMinorVersion* in te stellen op **False**. De upgrade voor de secundaire versie waarbij automatische upgrade is uitgeschakeld en een primaire versie-upgrade dezelfde methode volgen: Verwijder de extensie en installeer deze opnieuw. |
| Aangepaste installatie kopieën van Azure VM | Hand matige installatie van de afhankelijkheids agent voor Windows/Linux | Het bijwerken van Vm's naar de nieuwste versie van de agent moet worden uitgevoerd vanaf de opdracht regel met het Windows Installer-pakket of Linux self-extracting en installeer bare shell script bundel.|
| Niet-Azure Vm's | Hand matige installatie van de afhankelijkheids agent voor Windows/Linux | Het bijwerken van Vm's naar de nieuwste versie van de agent moet worden uitgevoerd vanaf de opdracht regel met het Windows Installer-pakket of Linux self-extracting en installeer bare shell script bundel. |

## <a name="upgrade-windows-agent"></a>Windows-agent bijwerken 

Als u de agent op een virtuele Windows-machine wilt bijwerken naar de nieuwste versie die niet is geïnstalleerd met behulp van de VM-extensie van de afhankelijkheids agent, voert u uit vanaf de opdracht prompt, het script of andere Automation-oplossing of met de installatie wizard van InstallDependencyAgent-Windows. exe.  

U kunt [hier](https://aka.ms/dependencyagentwindows)de meest recente versie van de Windows-agent downloaden.

### <a name="using-the-setup-wizard"></a>Met de wizard Setup

1. Meld u aan bij de computer met een account met beheerders rechten.

2. Voer **InstallDependencyAgent-Windows. exe** uit om de installatie wizard te starten.

3. Klik in het dialoog venster **dependency agent 9.9.1-configuratie** op **Ik ga akkoord** om de gebruiksrecht overeenkomst te accepteren.

5. Klik in het dialoog venster **dependency agent 9.9.0 verwijderen** op **volgende**. Op de status pagina wordt de voortgang van het verwijderen van de vorige versie weer gegeven.

6. Klik in het dialoog venster **dependency agent 9.9.0 verwijderen** op **verwijderen** om door te gaan met de installatie van de vorige versie in het pad dat is opgegeven in het dialoog venster. 

7. In het dialoog venster **dependency agent 9.9.0 verwijderen** wordt de voortgang van het verwijderen weer gegeven en wanneer deze is voltooid, wordt de pagina **volt ooien dependency agent verwijderen** weer gegeven. Klik op **Voltooien**.

8. In het dialoog venster **dependency agent 9.9.1-installatie** wordt de voortgang van de installatie weer gegeven. Wanneer de pagina **volt ooien dependency agent verwijderen** wordt weer gegeven, klikt u op **volt ooien**. 

### <a name="from-the-command-line"></a>Vanaf de opdracht regel

1. Meld u aan bij de computer met een account met beheerders rechten.

2. Voer de volgende opdracht uit.

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    Met de para meter `/RebootMode=manual` wordt voor komen dat de computer automatisch opnieuw wordt opgestart als sommige processen gebruikmaken van bestanden uit de vorige versie en een vergren deling hebben. 

3. Om te bevestigen dat de upgrade is geslaagd, raadpleegt u de `install.log` voor gedetailleerde informatie over de installatie. De logboekmap is *%Programfiles%\Microsoft afhankelijkheid Agent\logs*.

## <a name="upgrade-linux-agent"></a>Linux-agent bijwerken 

Upgrade van eerdere versies van de afhankelijkheids agent op Linux wordt ondersteund en uitgevoerd na dezelfde opdracht als een nieuwe installatie.

U kunt [hier](https://aka.ms/dependencyagentlinux)de meest recente versie van de Windows-agent downloaden.

1. Meld u aan bij de computer met een account met beheerders rechten.

2. Voer de volgende opdracht uit als root`sh InstallDependencyAgent-Linux64.bin -s`. 

Als de agent voor afhankelijkheden niet kan worden gestart, controleert u de logboeken voor uitgebreide foutgegevens. Op Linux-agents, de logboekmap is */var/opt/microsoft/dependency-agent/log*. 

## <a name="next-steps"></a>Volgende stappen

Als u wilt stoppen met het bewaken van uw Vm's gedurende een bepaalde periode of Azure Monitor voor VM's volledig wilt verwijderen, raadpleegt u [de bewaking van uw vm's in azure monitor voor VM's uitschakelen](vminsights-optout.md).
