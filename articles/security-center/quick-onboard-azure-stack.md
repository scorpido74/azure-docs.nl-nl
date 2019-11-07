---
title: Azure Stack virtuele machines onboarden naar Azure Security Center
description: In deze Quick start ziet u hoe u de Azure Monitor-, update-en configuratie beheer-extensie van de virtuele machine kunt inrichten op een Azure Stack virtuele machines.
services: security-center
documentationcenter: na
author: pipposera
manager: dsavage
ms.assetid: 8982348a-0624-40c7-8a1e-642a523c7f6b
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: fiseraci
ms.openlocfilehash: c186dcb2d7d7d423f5c001bdb4d3f3503beebd9c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686525"
---
# <a name="quickstart-onboard-your-azure-stack-virtual-machines-to-security-center"></a>Quick Start: uw Azure Stack virtuele machines onboarden naar Security Center
Nadat u uw Azure-abonnement hebt voor bereid, kunt u Security Center voor het beveiligen van uw virtuele machines die worden uitgevoerd op Azure Stack door de extensie van de virtuele machine voor **Azure monitor, update en Configuration Management** toe te voegen vanuit de Azure stack Commerce.

In deze Quick start ziet u hoe u de **Azure monitor-, update-en configuratie beheer-** extensie voor virtuele machines op een virtuele machine kunt toevoegen (Linux en Windows worden beide ondersteund) die worden uitgevoerd op Azure stack.

## <a name="prerequisites"></a>Vereisten
U moet over een abonnement op Microsoft Azure beschikken om met Security Center aan de slag te gaan. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/pricing/free-trial/).

U moet een Azure-abonnement hebben op de Standard-laag van Security Center voordat u deze Snelstartgids start. Zie [Onboard your Azure subscription to Security Center Standard](security-center-get-started.md) (Uw Azure-abonnement registreren voor Security Center Standard) voor upgrade-instructies. U kunt Security Center gratis laag voor 30 dagen proberen. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie.

## <a name="select-your-workspace-in-azure-security-center"></a>Selecteer uw werk ruimte in Azure Security Center

1. Meld u aan bij de [Azure Portal](https://azure.microsoft.com/features/azure-portal/).
2. Ga naar het **Microsoft Azure**-menu en selecteer **Security Center**. **Security Center - Overzicht** wordt geopend. 

   ![Security Center-overzicht][2]

3. Selecteer in het hoofdmenu van Security Center de optie **Aan de slag**.
4. Selecteer het tabblad **Aan de slag**.

   ![Aan de slag][3]

5. Klik op **Configureren** onder **Nieuwe niet-Azure-computers toevoegen**. Een lijst met uw Log Analytics-werkruimten wordt weergegeven. De lijst bevat, indien van toepassing, de standaardwerkruimte die is gemaakt door Security Center toen automatisch inrichten werd ingeschakeld. Selecteer deze werk ruimte of een andere werk ruimte waarvan u wilt dat de Azure Stack VM beveiligings gegevens rapporteert aan.

    ![Niet-Azure-computer toevoegen](./media/quick-onboard-windows-computer/non-azure.png)

   De Blade **direct agent** wordt geopend met een koppeling voor het downloaden van de agent en de sleutels voor de werk ruimte-id die moet worden gebruikt bij het configureren van de agent.

   >[!NOTE]
   > U hoeft de agent niet hand matig te downloaden. De agent wordt als een VM-extensie geïnstalleerd in de volgende stappen.

6. Selecteer rechts van **Werkruimte-id** het kopieerpictogram en plak de id in Kladblok.

7. Selecteer rechts van **Primaire sleutel** het kopieerpictogram en plak de sleutel in Kladblok.

## <a name="add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines"></a>De extensie van de virtuele machine toevoegen aan uw bestaande Azure Stack virtuele machines
U moet nu de extensie van de virtuele machine **Azure monitor, update en configuratie beheer** toevoegen aan de virtuele machines die worden uitgevoerd op uw Azure stack.

1. Meld u in een nieuw browser tabblad aan bij uw **Azure stack** -Portal.
2. Ga naar de pagina **virtuele machines** en selecteer de virtuele machine die u met Security Center wilt beveiligen. Voor informatie over het maken van een virtuele machine op Azure Stack raadpleegt u [deze Snelstartgids voor virtuele Windows-machines](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) of [deze Snelstartgids voor virtuele Linux-machines](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Selecteer **Extensies**. De lijst met virtuele-machine uitbreidingen die op deze virtuele machine is geïnstalleerd, wordt weer gegeven.
4. Klik op het tabblad **toevoegen** . De Blade **Nieuw resource** menu wordt geopend en toont de lijst met beschik bare extensies van virtuele machines. 
5. Selecteer de **Azure monitor-, update-en configuratie beheer** uitbreiding en klik op **maken**. De Blade **installatie van extensie** configuratie wordt geopend.

>[!NOTE]
> Als u de **Azure monitor-, update-en configuratie beheer** extensie die wordt vermeld in uw Marketplace niet ziet, kunt u contact opnemen met uw Azure stack-operator om deze beschikbaar te maken.

6. Plak de **werk ruimte-id** en **werkruimte sleutel (primaire sleutel)** die u in het klad blok hebt gekopieerd in de vorige procedure op de Blade **extensie configuratie installeren** .
7. Klik op **OK**wanneer u klaar bent met het opgeven van de benodigde configuratie-instellingen.
8. Zodra de installatie van de extensie is voltooid, wordt de status weer gegeven als **inrichting geslaagd**. Het kan een uur duren voordat de virtuele machine wordt weer gegeven in de Security Center Portal.

Zie [Windows-computers verbinden](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard)voor meer informatie over het installeren en configureren van de agent voor Windows.

Zie [problemen met Azure log Analytics Linux-agent oplossen](../azure-monitor/platform/agent-linux-troubleshoot.md)voor Linux problemen oplossen met agents.

U kunt nu uw Azure-VM's en niet-Azure-computers op één plek bewaken. In de Security Center Portal op Azure, onder **Compute**, hebt u een overzicht van alle vm's en computers samen met hun aanbevelingen. Security Center wordt ook de detectie van deze computers in beveiligings waarschuwingen geoppereerd.

  ![Blade Compute][6]

Er worden twee soorten pictogrammen weergegeven op de blade **Compute**:

![pictogram1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Niet-Azure-computer 

![pictogram2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Azure VM (Azure Stack-Vm's worden weer gegeven in deze groep)

## <a name="clean-up-resources"></a>Resources opschonen
Wanneer u deze niet meer nodig hebt, kunt u de extensie uit de virtuele machine verwijderen via de Azure Stack Portal.

De uitbrei ding verwijderen:

1. Open de **Azure stack Portal**.
2. Ga naar de pagina **virtuele machines** en selecteer de virtuele machine waarvan u de extensie wilt verwijderen.
3. Selecteer **uitbrei dingen**, selecteer de extensie **micro soft. EnterpriseCloud. monitoring**.
4. Klik op **installatie ongedaan maken**en bevestig de selectie door op **Ja**te klikken.

## <a name="next-steps"></a>Volgende stappen
In deze Quick Start hebt u de Azure Monitor-, update-en configuratie beheer uitbreiding ingericht op een virtuele machine die wordt uitgevoerd op Azure Stack. Voor meer informatie over het gebruik van Security Center gaat u verder met de zelfstudie voor het configureren van een beveiligingsbeleid en het beoordelen van de beveiliging van uw resources.

> [!div class="nextstepaction"]
> [Zelfstudie: Beveiligingsbeleidsregels opstellen en beoordelen](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
