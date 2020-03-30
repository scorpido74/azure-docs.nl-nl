---
title: Ingebouwde Azure Stack-virtuele machines naar Azure Security Center
description: Met deze snelle start ziet u hoe u de azure-monitor-, update- en configuratiebeheer-extensie indient op een azure stack-virtuele machines.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73686525"
---
# <a name="quickstart-onboard-your-azure-stack-virtual-machines-to-security-center"></a>Snelstart: uw virtual machines van Azure Stack aan boord van beveiligingscentrum
Nadat u uw Azure-abonnement hebt ingeschakeld, u Beveiligingscentrum inschakelen om uw virtuele machines die op Azure Stack worden uitgevoerd, te beschermen door de **azure-extensie voor beeldschermen, bijwerken en configuratiebeheer** toe te voegen aan de Azure Stack-marktplaats.

In deze quickstart ziet u hoe u de azure **monitor-, update- en configuratiebeheer-extensie** toevoegt op een virtuele machine (Linux en Windows worden beide ondersteund) die worden uitgevoerd op Azure Stack.

## <a name="prerequisites"></a>Vereisten
U moet over een abonnement op Microsoft Azure beschikken om met Security Center aan de slag te gaan. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/pricing/free-trial/).

U moet een Azure-abonnement hebben op de standaardlaag van het Beveiligingscentrum voordat u deze quickstart start. Zie [Onboard your Azure subscription to Security Center Standard](security-center-get-started.md) (Uw Azure-abonnement registreren voor Security Center Standard) voor upgrade-instructies. U de standaardlaag van het Beveiligingscentrum 30 dagen gratis proberen. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie.

## <a name="select-your-workspace-in-azure-security-center"></a>Selecteer uw werkruimte in Azure Security Center

1. Meld u aan bij de [Azure-portal](https://azure.microsoft.com/features/azure-portal/).
2. Selecteer In het menu **Microsoft Azure** de optie **Beveiligingscentrum**. **Security Center - Overzicht** wordt geopend. 

   ![Overzicht van Security Center][2]

3. Selecteer in het hoofdmenu van Security Center de optie **Aan de slag**.
4. Selecteer het tabblad **Aan de slag**.

   ![Aan de slag][3]

5. Klik op **Configureren** onder **Nieuwe niet-Azure-computers toevoegen**. Een lijst met uw Log Analytics-werkruimten wordt weergegeven. De lijst bevat, indien van toepassing, de standaardwerkruimte die is gemaakt door Security Center toen automatisch inrichten werd ingeschakeld. Selecteer deze werkruimte of een andere werkruimte waaraan u de Azure Stack VM wilt koppelen om beveiligingsgegevens te rapporteren.

    ![Niet-Azure-computer toevoegen](./media/quick-onboard-windows-computer/non-azure.png)

   Het **Direct Agent-blad** wordt geopend met een koppeling voor het downloaden van de agent en sleutels die uw werkruimte-id kan gebruiken bij het configureren van de agent.

   >[!NOTE]
   > U hoeft de agent niet handmatig te downloaden. De agent wordt in de onderstaande stappen geïnstalleerd als VM-extensie.

6. Selecteer rechts van **Werkruimte-id** het kopieerpictogram en plak de id in Kladblok.

7. Selecteer rechts van **Primaire sleutel** het kopieerpictogram en plak de sleutel in Kladblok.

## <a name="add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines"></a>De extensie voor virtuele machines toevoegen aan uw bestaande virtuele Azure Stack-machines
U moet nu de azure **monitor-, update- en configuratiebeheer-extensie** toevoegen aan de virtuele machines die op uw Azure Stack worden uitgevoerd.

1. Log in een nieuw browsertabblad in op uw **Azure Stack-portal.**
2. Ga naar de pagina **Virtuele machines** en selecteer de virtuele machine die u wilt beveiligen met Security Center. Zie [deze quickstart voor virtuele Windows-machines](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) of [deze quickstart voor virtuele Linux-machines](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal)voor informatie over het maken van een virtuele machine op Azure Stack.
3. Selecteer **Extensies**. De lijst van virtuele machine-extensies geïnstalleerd op deze virtuele machine wordt weergegeven.
4. Klik op het tabblad **Toevoegen.** Het **menublad New Resource** wordt geopend en toont de lijst met beschikbare virtuele machine-extensies. 
5. Selecteer de **azure-extensie voor monitor-, update- en configuratiebeheer** en klik op **Maken**. Het configuratieblad voor de configuratie **van de installatieextensie** wordt geopend.

>[!NOTE]
> Als u de azure **monitor-, update- en configuratiebeheerextensie** niet in uw marktplaats ziet staan, neem dan contact op met uw Azure Stack-operator om deze beschikbaar te maken.

6. Plak in het configuratieblad **voor extensie installeren** de **werkruimte-id** en **werkruimtesleutel (primaire sleutel)** die u in de vorige procedure naar kladblok hebt gekopieerd.
7. Wanneer u klaar bent met het verstrekken van de benodigde configuratie-instellingen, klikt u op **OK**.
8. Zodra de uitbreidingsinstallatie is voltooid, wordt de status weergegeven als **Provisioning Succeeded**. Het kan tot een uur duren voordat de virtuele machine wordt weergegeven in het portal van het Beveiligingscentrum.

Zie [Windows-computers](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard)verbinden voor meer informatie over het installeren en configureren van de agent voor Windows.

Zie Azure Log Analytics [Linux Agent oplossen](../azure-monitor/platform/agent-linux-troubleshoot.md)voor Linux-probleemproblemen.

U kunt nu uw Azure-VM's en niet-Azure-computers op één plek bewaken. In de Portal security center op Azure, onder **Compute,** hebt u een overzicht van alle VM's en computers, samen met hun aanbevelingen. Security Center maakt ook alle detectie voor deze computers weer in beveiligingswaarschuwingen.

  ![Blade Compute][6]

Er worden twee soorten pictogrammen weergegeven op de blade **Compute**:

![pictogram1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Niet-Azure-computer 

![pictogram2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Azure VM (Azure Stack VM's worden weergegeven in deze groep)

## <a name="clean-up-resources"></a>Resources opschonen
Wanneer dit niet meer nodig is, u de extensie uit de virtuele machine verwijderen via de Azure Stack-portal.

Ga als u de extensie verwijderen:

1. Open de **Azure Stack Portal**.
2. Ga naar de pagina **Virtuele machines** en selecteer de virtuele machine waaruit u de extensie wilt verwijderen.
3. Selecteer **Extensies**, selecteer de extensie **Microsoft.EnterpriseCloud.Monitoring**.
4. Klik op **Verwijderen**en bevestig de selectie door op **Ja**te klikken.

## <a name="next-steps"></a>Volgende stappen
In deze quickstart hebt u de Azure Monitor-, Update- en Configuration Management-extensie ingericht op een virtuele machine die op Azure Stack wordt uitgevoerd. Voor meer informatie over het gebruik van Security Center gaat u verder met de zelfstudie voor het configureren van een beveiligingsbeleid en het beoordelen van de beveiliging van uw resources.

> [!div class="nextstepaction"]
> [Zelfstudie: Beveiligingsbeleidsregels opstellen en beoordelen](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
