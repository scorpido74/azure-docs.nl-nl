---
title: Uw app testen in Azure | Microsoft Documenten
description: Leer hoe u een bestandsshare maakt in een lab en deze op uw lokale machine en een virtuele machine in het lab monteert en vervolgens bureaublad-/webtoepassingen implementeert in het delen van bestanden en deze te testen.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: spelluru
ms.openlocfilehash: f8c57b9e1fabbd04a7d9c92484b0f52f074c2577
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65872420"
---
# <a name="test-your-app-in-azure"></a>Uw app in Azure testen 
In dit artikel vindt u stappen voor het testen van uw toepassing in Azure met Behulp van DevTest Labs. Eerst stelt u een bestandsshare in een lab in en monteer t u deze als een schijf op uw lokale ontwikkelingsmachine en een VM in een lab. Vervolgens gebruikt u Visual Studio 2019 om uw app te implementeren in het bestandsshare, zodat u de app op de VM in het lab uitvoeren.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten 
1. [Maak een Azure-abonnement](https://azure.microsoft.com/free/) als u er nog geen hebt en meld u aan bij [Azure portal.](https://portal.azure.com)
2. Volg instructies in [dit artikel](devtest-lab-create-lab.md) om een lab te maken met Azure DevTest Labs. Maak het lab vast aan uw dashboard, zodat u het de volgende keer dat u zich aanmeldt gemakkelijk vinden. Azure DevTest Labs stelt u in staat om snel resources binnen Azure te maken door verspilling te minimaliseren en de kosten te beheersen. Zie [overzicht](devtest-lab-overview.md)voor meer informatie over DevTest Labs. 
3. Maak een Azure Storage-account in de brongroep van het lab door instructies te volgen in het artikel [Een opslagaccount maken.](../storage/common/storage-create-storage-account.md) Selecteer op de pagina **Opslagaccount maken** de optie **Bestaand gebruiken** voor **resourcegroep**en selecteer de **resourcegroep van het lab**. 
4. Maak een bestandsshare in uw Azure-opslag door instructies te volgen in het artikel [Een bestandsshare maken in Azure Files.](../storage/files/storage-how-to-create-file-share.md) 

## <a name="mount-the-file-share-on-your-local-machine"></a>Het bestandsaandeel op uw lokale machine zetten
1. Gebruik op uw lokale machine het script van [Persistenting Azure-bestandsshare-referenties in windows,](../storage/files/storage-how-to-use-files-windows.md#persisting-azure-file-share-credentials-in-windows) sectie van [Gebruik een Azure-bestandsshare met](../storage/files/storage-how-to-use-files-windows.md) Windows-artikel. 
2. Gebruik vervolgens `net use` de opdracht om de bestandsshare op uw machine te monteren. Hier volgt de voorbeeldopdracht: Geef uw Azure-opslagnaam en bestandsnaam op voordat u de opdracht uitvoert. 

    `net use Z: \\<YOUR AZURE STORAGE NAME>.file.core.windows.net\<YOUR FILE SHARE NAME> /persistent:yes`

## <a name="create-a-vm-in-the-lab"></a>Een virtuele machine maken in het lab
1. Selecteer op de pagina **Bestandsdelen** de **resourcegroep** in het broodkruimelmenu bovenaan. U ziet de pagina **Resourcegroep.** 
    
    ![Resourcegroep selecteren in het menu broodkruimel](media/test-app-in-azure/select-resource-group-bread-crump.png)
2. Selecteer op de pagina **Resourcegroep** het **lab dat** u hebt gemaakt in DevTest Labs.

    ![Het lab selecteren](media/test-app-in-azure/select-devtest-lab-in-resource-group.png)
3. Selecteer op de pagina **DevTest Lab** voor uw lab de optie **+ Toevoegen** op de werkbalk. 

    ![Knop Toevoegen voor het lab](media/test-app-in-azure/add-button-in-lab.png)
4. Zoek op de **basispagina Kiezen** naar een **kleine schijf**en selecteer **[smalldisk] Windows Server 2016 Data Center**. 

    ![Windows-server met kleine schijf kiezen](media/test-app-in-azure/choose-small-disk-windows-server.png)
5. Geef **op** de pagina Virtuele machine de naam van **de virtuele machine**op, **gebruikersnaam,** **wachtwoord**en selecteer **Maken**.    
    
    ![Virtuele machinepagina maken](media/test-app-in-azure/create-virtual-machine-page.png)    

## <a name="mount-the-file-share-on-your-vm"></a>Het bestandsaandeel op uw vm monteren
1. Nadat de virtuele machine is gemaakt, selecteert u de **virtuele machine** in de lijst.    

    ![Selecteer de lab-vm](media/test-app-in-azure/select-lab-vm.png)
2. Selecteer **Verbinding maken** op de werkbalk om verbinding te maken met de virtuele machine. 
3. [Azure PowerShell installeren](/powershell/azure/install-az-ps).
4. Volg de instructies in de sectie Het delen van bestanden op. 

## <a name="publish-your-app-from-visual-studio"></a>Uw app publiceren vanuit Visual Studio
In deze sectie publiceert u uw app van Visual Studio naar een test-VM in de cloud.

1. Maak een desktop-/webapplicatie met Visual Studio 2019.
2. Bouw uw app.
3. Als u uw app wilt publiceren, klikt u met de rechtermuisknop op uw project in de **Oplossingsverkenner**en selecteert u **Publiceren**. 
4. Voer in de **wizard Publiceren**het **station** in dat is toegewezen aan uw bestandsshare.

    **Bureaublad-app:**

    ![Desktop-app](media/test-app-in-azure/desktop-app.png)

    **Web-app:**

    ![Web-app](media/test-app-in-azure/web-app.png)

1. Selecteer **Volgende** om de publicatiewerkstroom te voltooien en selecteer **Voltooien**. Wanneer u de wizardstappen hebt voltooid, bouwt Visual Studio uw toepassing en publiceert deze in uw bestandsshare. 


## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>Test de app op uw test-VM in het lab

1. Navigeer naar de virtuele machinepagina voor uw vm in het lab. 
2. Selecteer **Start** op de werkbalk om de VM te starten als deze is gestopt. U beleid voor automatisch starten en automatisch afsluiten instellen voor uw vm om te voorkomen dat u elke keer start en stopt. 
3. Selecteer **Verbinden**.

    ![Virtuele machinepagina](media/test-app-in-azure/virtual-machine-page.png)
4. Start In de virtuele machine **De**verkenner en selecteer **Deze pc** om uw bestandsshare te vinden.

    ![Aandeel zoeken op VM](media/test-app-in-azure/find-share-on-vm.png)

    > [!NOTE]
    > Als u uw bestandsshare niet vinden op uw virtuele machine of op uw lokale `net use` machine, u deze opnieuw monteren door de opdracht uit te voeren. U `net use` de opdracht vinden in de wizard **Verbinding** van uw **bestandsshare** in de Azure-portal.
1. Open de bestandsshare en bevestig dat u de app ziet die u vanuit Visual Studio hebt geïmplementeerd. 

    ![Aandeel openen op VM](media/test-app-in-azure/open-file-share.png)

    U uw app nu openen en testen in de test-VM die u in Azure hebt gemaakt.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen om te leren hoe vm's in een lab te gebruiken. 

- [Een virtuele machine aan een lab toevoegen](devtest-lab-add-vm.md)
- [Een lab-vm opnieuw starten](devtest-lab-restart-vm.md)
- [Het formaat van een lab-vm wijzigen](devtest-lab-resize-vm.md)
