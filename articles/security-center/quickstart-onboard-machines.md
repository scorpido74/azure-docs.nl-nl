---
title: Uw niet-Azure-machines verbinden met Azure Security Center
description: Leer hoe u uw niet-Azure-machines kunt verbinden met Security Center
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 6f2889c298f525e1babf80f86d4ae140ef2ce96f
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448946"
---
#  <a name="connect-your-non-azure-machines-to-security-center"></a>Uw niet-Azure-machines verbinden met Security Center

Security Center kan de beveiligingsstatus van uw niet-Azure-computers controleren, maar u moet deze resources dan eerst onboarden. U kunt niet-Azure-computers toevoegen vanaf de pagina **Aan de slag** of vanuit **Voorraad**, zoals hieronder wordt beschreven.

## <a name="add-non-azure-computers"></a>Niet-Azure-computers toevoegen 

1. Open in het menu van Security Center de pagina **Aan de slag**.
1. Selecteer het tabblad **Aan de slag**.

    :::image type="content" source="./media/security-center-onboarding/onboarding-get-started-tab.png" alt-text="Tabblad Aan de slag op de pagina Aan de slag" lightbox="./media/security-center-onboarding/onboarding-get-started-tab.png":::

1. Selecteer onder **Niet-Azure-servers toevoegen** de optie **Configureren**.

    > [!TIP]
    > U kunt Apparaten toevoegen ook openen met de knop **Niet-Azure-servers toevoegen** op de pagina **Voorraad**.

    Een lijst met uw Log Analytics-werkruimten wordt weergegeven. De lijst bevat, indien van toepassing, de standaardwerkruimte die is gemaakt door Security Center toen automatisch inrichten werd ingeschakeld. Selecteer deze werkruimte of een andere werkruimte die u wilt gebruiken.

    U kunt computers aan een bestaande werkruimte toevoegen of een nieuwe werkruimte maken. 

1. Selecteer eventueel  **Nieuwe werkruimte maken** om een nieuwe werkruimte te maken.

1. Selecteer in de lijst met werkruimten **Servers toevoegen** voor de relevante werkruimte.
    De pagina **Agentbeheer** wordt weergegeven.

    Kies hier de relevante procedure, afhankelijk van het type machines dat u aan het onboarden bent:

    - [Uw Azure Stack-VM's onboarden](#onboard-your-azure-stack-vms)
    - [Uw Linux-machines onboarden](#onboard-your-linux-machines)
    - [Uw Windows-machines onboarden](#onboard-your-windows-machines)


### <a name="onboard-your-azure-stack-vms"></a>Uw Azure Stack-VM's onboarden
Om Azure Stack-VM's toe te kunnen voegen, hebt u de gegevens op de pagina **Agentbeheer** nodig en moet u de VM-extensie **Azure Monitor, update- en configuratiebeheer** configureren op de virtuele machines die op uw Azure Stack draaien.
1. Kopieer op de pagina **Agentbeheer** de **werkruimte-id** en **primaire sleutel** in Kladblok.
1. Meld u aan bij uw **Azure Stack**-portal en open de pagina **Virtuele machines**.
1. Selecteer de virtuele machine die u met Security Center wilt beveiligen.
    >[!TIP]
    > Zie [deze quickstart voor virtuele Windows-machines](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) of [deze quickstart voor virtuele Linux-machines](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal) voor meer informatie over het maken van een virtuele machine op Azure Stack.
1. Selecteer **Extensies**. De lijst met virtuele machine-extensies die op deze virtuele machine is geïnstalleerd, wordt weergegeven.
1. Selecteer het tabblad **Toevoegen**. Het menu **Nieuwe resource** toont de lijst met beschikbare extensies voor virtuele machines.
1. Selecteer achtereenvolgens de extensie **Azure Monitor, update- en configuratiebeheer** en **Maken**. De configuratiepagina **Extensie installeren** wordt geopend.
    >[!NOTE]
    > Als u de extensie **Azure Monitor, update-en configuratiebeheer** die wordt vermeld in uw Marketplace niet ziet, kunt u contact opnemen met uw Azure Stack-operator om deze beschikbaar te maken.
1. Op de configuratiepagina **Extensie installeren** plakt u de **werkruimte-id** en **werkruimtesleutel (primaire sleutel)** die u in de vorige stap naar Kladblok hebt gekopieerd.
1. Selecteer **OK** nadat u de configuratie hebt afgerond. De status van de extensie wordt weergegeven als **Inrichten geslaagd**. Het kan een uur duren voordat de virtuele machine wordt weergegeven in Security Center.


### <a name="onboard-your-linux-machines"></a>Uw Linux-machines onboarden
Om Linux-machines toe te voegen, hebt u de WGET-opdracht op de pagina **Agentbeheer** nodig.
1. Kopieer op de pagina **Agentbeheer** de opdracht **WGET** in Kladblok. Sla dit bestand op een locatie op die toegankelijk is vanaf uw Linux-computer.
1. Open op uw Linux-computer het bestand met de opdracht WGET. Selecteer de volledige inhoud en kopieer en plak deze in een terminalconsole.
1. Zodra de installatie is voltooid, kunt u controleren of de *omsagent* is geïnstalleerd door de opdracht *pgrep* uit te voeren. De opdracht retourneert de PID (proces-id) *omsagent*.
    De logboeken voor de agent zijn te vinden op: */var/opt/microsoft/omsagent/\<workspace id>/log/* Het kan tot 30 minuten duren voordat de nieuwe Linux-machine in Security Center verschijnt.


### <a name="onboard-your-windows-machines"></a>Uw Windows-machines onboarden
Om Windows-computers toe te voegen, hebt u de gegevens op de pagina **Agentbeheer** nodig en moet u het juiste agentbestand (32-/64-bits) downloaden.
1. Selecteer de koppeling **Windows-agent downloaden** die van toepassing is op het processortype van uw computer om het installatiebestand te downloaden.
1. Kopieer op de pagina **Agentbeheer** de **werkruimte-id** en **primaire sleutel** in Kladblok.
1. Kopieer het gedownloade installatiebestand naar de doelcomputer en voer het uit.
1. Volg de installatiewizard (**Volgende** **Ik ga akkoord**, **Volgende**, **Volgende**).
    1. Op de pagina **Azure Log Analytics** plakt u de **werkruimte-id** en **werkruimtesleutel (primaire sleutel)** die u in Kladblok hebt gekopieerd.
    1. Als u de computer wilt laten rapporteren bij een Log Analytics-werkruimte in de Azure Government-cloud, selecteert u **Azure US Government** in de vervolgkeuzelijst **Azure Cloud**.
    1. Als de computer met de Log Analytics-service moet communiceren via een proxyserver, selecteert u **Geavanceerd** en geeft u de URL en het poortnummer van de proxyserver op.
    1. Nadat u alle configuratie-instellingen hebt ingevoerd, selecteert u **Volgende**.
    1. Controleer op de pagina **Gereed om te installeren** de instellingen die moeten worden toegepast en selecteer **Installeren**.
    1. Selecteer op de pagina **Configuratie voltooid** de optie **Voltooien**.

Als u klaar bent wordt de **Log Analytics-agent** in het **Configuratiescherm** weergegeven. U kunt hier de configuratie controleren en verifiëren of de agent is verbonden.

Zie [Windows-computers verbinden](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard) voor meer informatie over het installeren en configureren van de agent.


## <a name="verifying"></a>Controleren
Gefeliciteerd. Nu worden uw Azure- en niet-Azure-machines op één plek weergegeven. Open de pagina [Assetvoorraad](asset-inventory.md) en filter de relevante resourcetypen. Deze twee pictogrammen onderscheiden de typen:

  ![pictogram1](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Niet-Azure-machine

  ![pictogram2](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Azure VM


## <a name="next-steps"></a>Volgende stappen

Op deze pagina werd uitgelegd hoe u uw niet-Azure-machines kunt toevoegen aan Azure Security Center. Om de status van deze niet-Azure-machines te controleren, gebruikt u de voorraad-hulpprogramma's zoals beschreven op de volgende pagina:

- [Verken en beheer uw resources met hulpprogramma's voor assetvoorraad en -beheer](asset-inventory.md)