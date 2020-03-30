---
title: Virtuele machineschaalset implementeren met Visual Studio
description: Virtuele machineschaalsets implementeren met Visual Studio en een resourcemanagersjabloon
ms.custom: vs-azure, H1Hack27Feb2017
ms.workload: azure-vs
author: mayanknayar
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: manayar
ms.openlocfilehash: c49b4f42bc726c68880bdd4d6f58956936e83177
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066965"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Een virtuele machineschaalset maken met Visual Studio

In dit artikel ziet u hoe u een Azure Virtual Machine Scale Set implementeert met behulp van een implementatie van Visual Studio Resource Group.

[Azure Virtual Machine Scale Sets](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) is een Azure Compute-bron voor het implementeren en beheren van een verzameling vergelijkbare virtuele machines met automatische schaal en taakverdeling. U virtuele machineschaalsets inrichten en implementeren met [Azure Resource Manager-sjablonen](https://github.com/Azure/azure-quickstart-templates). Azure Resource Manager-sjablonen kunnen worden geïmplementeerd met Azure CLI, PowerShell, REST en ook rechtstreeks vanuit Visual Studio. Visual Studio biedt een reeks voorbeeldsjablonen die u implementeren als onderdeel van een azure resourcegroepimplementatieproject.

Azure Resource Group-implementaties zijn een manier om een set gerelateerde Azure-resources te groeperen en te publiceren in één implementatiebewerking. Zie [Azure-brongroepen maken en implementeren via Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Als u aan de slag wilt met het implementeren van virtuele machineschaalsets in Visual Studio, hebt u de volgende vereisten nodig:

* Visual Studio 2013 of hoger
* Azure SDK 2.7, 2.8 of 2.9

>[!NOTE]
>Dit artikel maakt gebruik van Visual Studio 2019 met [Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="create-a-project"></a>Een project maken<a name="creating-a-project"></a> 

1. Open Visual Studio en selecteer **Een nieuw project maken**.

1. Kies in **Een nieuw project maken**de optie Azure Resource **Group** voor C# en selecteer **Volgende**.

1. Voer in **Uw nieuwe project configureren**een naam in en selecteer **Maken**.

    ![Uw project een naam geven en maken](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. Kies in de lijst met sjablonen de sjabloon **Windows Virtual Machine Scale Set** of Linux Virtual Machine Scale **Set.** Selecteer **OK**.

   ![Een sjabloon voor virtuele machines selecteren](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

Nadat u uw project hebt gemaakt, bevat **Solution Explorer** een PowerShell-implementatiescript, een Azure Resource Manager-sjabloon en een parameterbestand voor de virtuele machineschaalset.

## <a name="customize-your-project"></a>Uw project aanpassen

Nu u de sjabloon bewerken om deze aan te passen aan de behoeften van uw toepassing. U eigenschappen voor virtuele machine-extensie toevoegen of regels voor het balanceren van de lastenbewerken bewerken. Standaard zijn de sjablonen voor virtuele machineschaalset geconfigureerd om de **AzureDiagnostics-extensie** te implementeren, waardoor het eenvoudig is om regels voor automatische waarden toe te voegen. De sjablonen implementeren ook een load balancer met een openbaar IP-adres, geconfigureerd met binnenkomende NAT-regels.

Met de load balancer u verbinding maken met de virtuele machine-exemplaren met SSH (Linux) of RDP (Windows). Het front-end poortbereik begint bij 50000. Voor Linux, als u SSH naar poort 50000, load balancing routes u naar poort 22 van de eerste virtuele machine in de Scale Set. Aansluiten op poort 50001 wordt doorgestuurd naar poort 22 van de tweede virtuele machine, enzovoort.

 Een goede manier om uw sjablonen te bewerken met Visual Studio is door de **JSON Outline**te gebruiken. U de parameters, variabelen en resources ordenen. Met inzicht in het schema kan Visual Studio fouten in uw sjabloon aanwijzen voordat u het implementeert.

![JSON Explorer](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>Het project implementeren

Implementeer de sjabloon Azure Resource Manager om de bron van de virtuele machineschaalset te maken:

1. Klik in **Solution Explorer**met de rechtermuisknop op het project en kies**Nieuw** **implementeren** > .

    ![Uw project implementeren](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. Kies **in Deploy naar Resource Group**welk abonnement u wilt gebruiken en selecteer een resourcegroep. U indien nodig een resourcegroep maken.

1. Selecteer vervolgens **Parameters bewerken** om parameters in te voeren die aan uw sjabloon worden doorgegeven.

   ![Abonnement- en resourcegroep invoeren](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. Geef de gebruikersnaam en het wachtwoord voor het besturingssysteem op. Deze waarden zijn vereist om de implementatie te maken. Als PowerShell-hulpprogramma's voor Visual Studio niet zijn geïnstalleerd, selecteert u **Wachtwoorden opslaan** om een verborgen PowerShell-opdrachtprompt te voorkomen of gebruikt u [Key Vault-ondersteuning.](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/) Selecteer **Opslaan** om door te gaan.

    ![Implementatieparameters bewerken](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. Selecteer Implementeren **in Deploy naar Resource Group** **.** Met de actie wordt het script **Deploy-AzureResourceGroup.ps1** uitgevoerd. In het venster **Uitvoer** wordt de voortgang van de implementatie weergegeven.

   ![Uitvoer toont resultaten](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## <a name="explore-your-virtual-machine-scale-set"></a>Ontdek uw virtuele machineschaalset<a name="exploring-your-virtual-machine-scale-set"></a>

Selecteer Cloud Explorer **weergeven** > **Cloud Explorer** om de nieuwe virtuele machineschaalset weer te geven. Gebruik **Alles vernieuwen,** indien nodig.

![Cloud Explorer](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

**Met Cloud Explorer** u Azure-resources beheren in Visual Studio tijdens het ontwikkelen van toepassingen. U uw virtuele machineschaalset ook bekijken in de [Azure-portal](https://portal.azure.com) en [Azure Resource Explorer.](https://resources.azure.com/)

 De portal biedt de beste manier om uw Azure-infrastructuur te beheren met een webbrowser. Azure Resource Explorer biedt een eenvoudige manier om Azure-bronnen te verkennen en te debuggen. Azure Resource Explorer biedt de instantieweergave en toont ook PowerShell-opdrachten voor de resources die u bekijkt.

## <a name="next-steps"></a>Volgende stappen

Zodra u virtuele machineschaalsets met succes hebt geïmplementeerd via Visual Studio, u uw project verder aanpassen aan uw toepassingsvereisten. Configureer bijvoorbeeld automatisch schalen door een **Insights-bron** toe te voegen. U infrastructuur toevoegen aan uw sjabloon, zoals zelfstandige virtuele machines, of toepassingen implementeren met behulp van de aangepaste scriptextensie. Goede voorbeeldsjablonen zijn te vinden in de GitHub-opslagplaats [Azure Quickstart Templates.](https://github.com/Azure/azure-quickstart-templates) Zoeken naar `vmss`.
