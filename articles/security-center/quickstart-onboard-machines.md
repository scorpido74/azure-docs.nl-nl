---
title: Uw niet-Azure-machines verbinden met Azure Security Center
description: Meer informatie over hoe u uw niet-Azure-machines verbindt met Security Center
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 2602df2e8a2699914ee32138a8aeba31d7f58cdb
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935552"
---
#  <a name="connect-your-non-azure-machines-to-security-center"></a>Uw niet-Azure-machines verbinden met Security Center

Security Center kan de beveiligingsstatus van uw niet-Azure-computers controleren, maar u moet deze resources dan eerst onboarden. U kunt niet-Azure-computers toevoegen vanaf de pagina **aan** de slag of op basis van de **inventaris** , zoals hieronder wordt beschreven.

## <a name="add-non-azure-computers"></a>Niet-Azure-computers toevoegen 

1. Open in het menu van Security Center de pagina **aan** de slag.
1. Selecteer het tabblad **Aan de slag**.

    :::image type="content" source="./media/security-center-onboarding/onboarding-get-started-tab.png" alt-text="Tabblad aan de slag op de pagina aan de slag" lightbox="./media/security-center-onboarding/onboarding-get-started-tab.png":::

1. Selecteer onder **niet-Azure-servers toevoegen**de optie **configureren** .

    > [!TIP]
    > U kunt ook de knop machines toevoegen op **inventory** de pagina inventaris **toevoegen van niet-Azure-servers** openen.

    Een lijst met uw Log Analytics-werkruimten wordt weergegeven. De lijst bevat, indien van toepassing, de standaardwerkruimte die is gemaakt door Security Center toen automatisch inrichten werd ingeschakeld. Selecteer deze werkruimte of een andere werkruimte die u wilt gebruiken.

    U kunt computers toevoegen aan een bestaande werk ruimte of een nieuwe werk ruimte maken. 

1. Selecteer eventueel  **nieuwe werk ruimte maken**om een nieuwe werk ruimte te maken.

1. Selecteer in de lijst met werk ruimten de optie **servers toevoegen** voor de relevante werk ruimte.
    De pagina **agents beheren** wordt weer gegeven.

    Kies hier de relevante procedure, afhankelijk van het type machines dat u wilt voorbereiden:

    - [Onboarding van uw Azure Stack-Vm's](#onboard-your-azure-stack-vms)
    - [Onboarding van uw Linux-machines](#onboard-your-linux-machines)
    - [Onboarding van uw Windows-computers](#onboard-your-windows-machines)


### <a name="onboard-your-azure-stack-vms"></a>Onboarding van uw Azure Stack-Vm's
Om Azure Stack Vm's toe te voegen, moet u de informatie op de pagina **agents beheren** en de uitbrei ding van de virtuele machine voor het **Azure monitor, bijwerken en configuratie beheer** configureren op de virtuele machines die worden uitgevoerd op uw Azure stack.
1. Kopieer op de pagina **agents beheren** de **werk ruimte-id** en **primaire sleutel** naar Klad blok.
1. Meld u aan bij uw **Azure stack** -Portal en open de pagina **virtuele machines** .
1. Selecteer de virtuele machine die u met Security Center wilt beveiligen.
    >[!TIP]
    > Zie [deze quickstart voor virtuele Windows-machines](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) of [deze quickstart voor virtuele Linux-machines](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal) voor meer informatie over het maken van een virtuele machine op Azure Stack.
1. Selecteer **Extensies**. De lijst met virtuele machine-extensies die op deze virtuele machine is geïnstalleerd, wordt weergegeven.
1. Selecteer het tabblad **toevoegen** . In het menu **nieuwe resource** wordt de lijst met beschik bare extensies voor virtuele machines weer gegeven.
1. Selecteer de **Azure monitor-, update-en configuratie beheer** uitbreiding en selecteer **maken**. De configuratie pagina **voor de installatie** van de extensie wordt geopend.
    >[!NOTE]
    > Als u de extensie **Azure Monitor, update-en configuratiebeheer** die wordt vermeld in uw Marketplace niet ziet, kunt u contact opnemen met uw Azure Stack-operator om deze beschikbaar te maken.
1. Plak op de pagina **extensie configuratie installeren** de **werk ruimte-id** en **werkruimte sleutel (primaire sleutel)** die u in de vorige stap hebt gekopieerd naar Klad blok.
1. Wanneer u de configuratie hebt voltooid, selecteert u **OK**. De status van de uitbrei ding wordt weer gegeven als **inrichting geslaagd**. Het kan een uur duren voordat de virtuele machine in Security Center wordt weer gegeven.


### <a name="onboard-your-linux-machines"></a>Onboarding van uw Linux-machines
Om Linux-machines toe te voegen, hebt u de WGET-opdracht van de **agent beheer** pagina nodig.
1. Kopieer op de pagina **agents beheren** de opdracht **wget** naar Klad blok. Sla dit bestand op een locatie op die toegankelijk is vanaf uw Linux-computer.
1. Open het bestand op uw Linux-computer met de opdracht WGET. Selecteer de hele inhoud en kopieer deze en plak deze in een Terminal-console.
1. Wanneer de installatie is voltooid, kunt u controleren of de *omsagent* is geïnstalleerd door de opdracht *pgrep* uit te voeren. Met de opdracht wordt de *omsagent* -PID geretourneerd.
    De logboeken voor de agent vindt u op: */var/opt/Microsoft/omsagent/ \<workspace id> /log/* het kan tot 30 minuten duren voordat de nieuwe Linux-machine wordt weer gegeven in Security Center.


### <a name="onboard-your-windows-machines"></a>Onboarding van uw Windows-computers
Om Windows-computers toe te voegen, moet u de informatie op de pagina **agents beheren** en het juiste agent bestand (32/64 bits) downloaden.
1. Selecteer de koppeling **Windows-agent downloaden** die van toepassing is op het processortype van uw computer om het installatiebestand te downloaden.
1. Kopieer op de pagina **agents beheren** de **werk ruimte-id** en **primaire sleutel** naar Klad blok.
1. Kopieer het gedownloade installatie bestand naar de doel computer en voer dit uit.
1. Volg de installatie wizard (**volgende**, **Ik ga akkoord** **, volgende volgende)**. **Next**
    1. Plak op de pagina **Azure log Analytics** de **werk ruimte-id** en **werkruimte sleutel (primaire sleutel)** die u in Klad blok hebt gekopieerd.
    1. Als de computer moet rapporteren aan een Log Analytics-werk ruimte in Azure Government Cloud, selecteert u **Azure US Government** in de vervolg keuzelijst van de **Azure-Cloud** .
    1. Als de computer met de Log Analytics-service moet communiceren via een proxyserver, selecteert u **Geavanceerd** en geeft u de URL en het poortnummer van de proxyserver op.
    1. Wanneer u alle configuratie-instellingen hebt ingevoerd, selecteert u **volgende**.
    1. Controleer op de pagina **gereed voor installatie** de instellingen die moeten worden toegepast en selecteer **installeren**.
    1. Selecteer op de pagina **Configuratie voltooid** de optie **Voltooien**.

Als u klaar bent wordt de **Log Analytics-agent** in het **Configuratiescherm** weergegeven. U kunt hier de configuratie controleren en verifiëren of de agent is verbonden.

Zie [Windows-computers verbinden](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard)voor meer informatie over het installeren en configureren van de agent.


## <a name="verifying"></a>Controleren
Gefeliciteerd Nu kunt u uw Azure-en niet-Azure-machines op één plek weer geven. Open de [pagina Asset Inventory](asset-inventory.md) en filter naar de relevante resource typen. Deze twee pictogrammen onderscheiden de typen:

  ![pictogram1](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Niet-Azure-computer

  ![pictogram2](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Azure VM


## <a name="next-steps"></a>Volgende stappen

Op deze pagina wordt uitgelegd hoe u uw niet-Azure-machines kunt toevoegen aan Azure Security Center. Als u de status wilt controleren, gebruikt u de inventarisatie hulpprogramma's zoals beschreven op de volgende pagina:

- [Uw resources verkennen en beheren met Asset Inventory and management tools](asset-inventory.md)