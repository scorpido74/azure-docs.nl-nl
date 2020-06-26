---
title: Onboarding van Azure Stack virtuele machines naar Azure Security Center
description: In deze quickstart ziet u hoe u de virtuele machine-extensie van Azure Monitor, update- en configuratiebeheer op een virtuele Azure Stack-machines inricht.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8982348a-0624-40c7-8a1e-642a523c7f6b
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: memildin
ms.openlocfilehash: 1f0a11616fe996f02d6464d23724156906176041
ms.sourcegitcommit: 99d016949595c818fdee920754618d22ffa1cd49
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/15/2020
ms.locfileid: "84771152"
---
# <a name="quickstart-onboard-your-azure-stack-virtual-machines-to-security-center"></a>Quickstart: Onboarding van uw Azure Stack virtuele machines naar Security Center
Nadat u uw Azure-abonnement hebt ingesloten, kunt u Security Center inschakelen om uw virtuele machines die op Azure Stack worden uitgevoerd te beschermen door de virtuele machine-extensie **Azure Monitor, update- en configuratiebeheer** toe te voegen van de Azure Stack Marketplace.

In deze quickstart ziet u hoe u de virtuele machine-uitbreiding van **Azure Monitor, update- en configuratiebeheer** op een virtuele Azure Stack-machine (Linux en Windows worden beide ondersteund) die worden uitgevoerd op Azure Stack, kunt toevoegen.

## <a name="prerequisites"></a>Vereisten
U moet over een abonnement op Microsoft Azure beschikken om met Security Center aan de slag te gaan. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/pricing/free-trial/).

U moet een Azure-abonnement hebben voor de Standard-categorie van Security Center voordat u deze quickstart start. Zie [Onboard your Azure subscription to Security Center Standard](security-center-get-started.md) (Uw Azure-abonnement registreren voor Security Center Standard) voor upgrade-instructies. U kunt Security Center Standard 30 dagen kosteloos proberen. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie.

## <a name="select-your-workspace-in-azure-security-center"></a>Selecteer uw werkruimte in Azure Security Center

1. Meld u aan bij de [Azure Portal](https://azure.microsoft.com/features/azure-portal/).
2. Ga naar het **Microsoft Azure**-menu en selecteer **Security Center**. **Security Center - Overzicht** wordt geopend. 

   ![Overzicht van Security Center][2]

3. Selecteer in het hoofdmenu van Security Center de optie **Aan de slag**.
4. Selecteer het tabblad **Aan de slag**.

   ![Aan de slag][3]

5. Klik op **Configureren** onder **Nieuwe niet-Azure-computers toevoegen**. Een lijst met uw Log Analytics-werkruimten wordt weergegeven. De lijst bevat, indien van toepassing, de standaardwerkruimte die is gemaakt door Security Center toen automatisch inrichten werd ingeschakeld. Selecteer deze werkruimte of een andere werkruimte waarvan u wilt dat de Azure Stack VM beveiligingsgegevens aan moet rapporteren.

    ![Niet-Azure-computer toevoegen](./media/quick-onboard-windows-computer/non-azure.png)

   De blade **Agent toewijzen** wordt geopend, met een koppeling om een agent en sleutels voor uw werkruimte-id te downloaden, zodat u de agent kunt configureren.

   >[!NOTE]
   > U hoeft de agent niet handmatig te downloaden. De agent wordt als een VM-extensie geïnstalleerd in de volgende stappen.

6. Selecteer rechts van **Werkruimte-id** het kopieerpictogram en plak de id in Kladblok.

7. Selecteer rechts van **Primaire sleutel** het kopieerpictogram en plak de sleutel in Kladblok.

## <a name="add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines"></a>De extensie van de virtuele machine toevoegen aan uw bestaande Azure Stack virtuele machines
U moet nu de extensie van de virtuele machine **Azure Monitor, update- en configuratiebeheer** toevoegen aan de virtuele machines die op uw Azure Stack worden uitgevoerd.

1. Meld u in een nieuw browsertabblad aan bij uw **Azure Stack**-portal.
2. Ga naar de pagina **Virtuele machines** en selecteer de virtuele machine die u met Security Center wilt beveiligen. Zie [deze quickstart voor virtuele Windows-machines](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) of [deze quickstart voor virtuele Linux-machines](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal) voor meer informatie over het maken van een virtuele machine op Azure Stack.
3. Selecteer **Extensies**. De lijst met virtuele machine-extensies die op deze virtuele machine is geïnstalleerd, wordt weergegeven.
4. Klik op de knop **Add**. De menublade **Nieuwe resource** wordt geopend en toont de lijst met beschikbare extensies van virtuele machines. 
5. Selecteer de extensie **Azure Monitor, update-en configuratie beheer** en klik op **maken**. De configuratieblade **Extensie installeren** wordt geopend.

>[!NOTE]
> Als u de extensie **Azure Monitor, update-en configuratiebeheer** die wordt vermeld in uw Marketplace niet ziet, kunt u contact opnemen met uw Azure Stack-operator om deze beschikbaar te maken.

6. Op de configuratieblade **Extensie installeren** plakt u de **werkruimte-id** en **werkruimtesleutel (primaire sleutel)** die u in de vorige stap in kladblok hebt gekopieerd.
7. Wanneer u klaar bent met het opgeven van de benodigde configuratie-instellingen, klikt u op **OK**.
8. Zodra de installatie van de extensie is voltooid, wordt de status weergegeven als **inrichting geslaagd**. Het kan een uur duren voordat de virtuele machine wordt weergegeven in de Security Center Portal.

Zie [Windows-computers verbinden](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard) voor meer informatie over het installeren en configureren van de agent voor Windows.

Zie [Probleemoplossing voor Linux-agent voor Log Analytics](../azure-monitor/platform/agent-linux-troubleshoot.md) voor meer informatie over het oplossen van problemen met agents in Linux.

U kunt nu uw Azure-VM's en niet-Azure-computers op één plek bewaken. Onder **Compute** vindt u een overzicht van alle VM's en computers, plus aanbevelingen. Security Center geeft ook detecties voor deze computers in beveiligingswaarschuwingen weer.

  ![Blade Compute][6]

Er worden twee soorten pictogrammen weergegeven op de blade **Compute**:

![pictogram1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Niet-Azure-computer 

![pictogram2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Azure-VM (Azure Stack-VM's worden weergegeven in deze groep)

## <a name="clean-up-resources"></a>Resources opschonen
Wanneer u deze niet meer nodig hebt, kunt u de extensie uit de virtuele machine verwijderen via de Azure Stack-portal.

De extensie verwijderen:

1. Open de **Azure Stack-portal**.
2. Ga naar de pagina **Virtuele machines**, selecteer de virtuele machine waarvan u de extensie wilt verwijderen.
3. Selecteer **Extensies**, de extensie **Microsoft.EnterpriseCloud.Monitoring**.
4. Klik op **Verwijderen** en bevestig de selectie door op **ja**te klikken.

## <a name="next-steps"></a>Volgende stappen
In deze quickstart hebt u de virtuele machine-extensie van Azure Monitor, update- en configuratiebeheer op een virtuele Azure Stack-machine ingericht. Voor meer informatie over het gebruik van Security Center gaat u verder met de zelfstudie voor het configureren van een beveiligingsbeleid en het beoordelen van de beveiliging van uw resources.

> [!div class="nextstepaction"]
> [Zelfstudie: Beveiligingsbeleid opstellen en beoordelen](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
