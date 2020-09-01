---
title: Azure Resource Manager wacht woord instellen op uw Azure Stack Edge GPU-apparaat
description: Hierin wordt beschreven hoe u verbinding maakt met de Azure Resource Manager die wordt uitgevoerd op uw Azure Stack Edge GPU met behulp van Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: d64db4ab8da1f5f0f4c2dc69b2bc6d4da66c8e91
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146280"
---
# <a name="set-azure-resource-manager-password"></a>Azure Resource Manager wacht woord instellen

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

In dit artikel wordt beschreven hoe u uw Azure Resource Manager wacht woord instelt. U moet dit wacht woord instellen wanneer u verbinding maakt met de lokale Api's van het apparaat via de Azure Resource Manager.

De procedure voor het instellen van het wacht woord kan verschillen, afhankelijk van of u de Azure Portal of de Power shell-cmdlets gebruikt. Elk van deze procedures wordt beschreven in de volgende secties.


## <a name="reset-password-via-the-azure-portal"></a>Wacht woord opnieuw instellen via de Azure Portal

1. Ga in het Azure Portal naar de resource Azure Stack Edge die u hebt gemaakt om uw apparaat te beheren. Ga naar **Edge-rekenproces > Aan de slag**.

2. Selecteer in het rechterdeel venster, vanaf de opdracht balk, het **wacht woord voor de Edge-arm opnieuw instellen**. 

    ![EdgeARM gebruikers wachtwoord 1 opnieuw instellen](media/azure-stack-edge-j-series-set-azure-resource-manager-password/set-edgearm-password-1.png)

3. Geef op de Blade **EdgeArm gebruikers wachtwoord opnieuw instellen** een wacht woord op om verbinding te maken met de lokale api's van uw apparaat via de Azure Resource Manager. Bevestig het wacht woord en selecteer **opnieuw instellen**.

    ![EdgeARM gebruikers wachtwoord 2 opnieuw instellen](media/azure-stack-edge-j-series-set-azure-resource-manager-password/set-edgearm-password-2.png)



## <a name="reset-password-via-powershell"></a>Wacht woord opnieuw instellen via Power shell

1. Ga in azure Portal naar de Azure Stack Edge-resource die u hebt gemaakt om uw apparaat te beheren. Noteer de volgende para meters op de pagina **overzicht** .

    - Resource naam Azure Stack rand
    - Abonnements-id

2. Ga naar **instellingen > eigenschappen**. Noteer de volgende para meters op de pagina **Eigenschappen** .

    - Resourcegroep
    - CIK-versleutelings sleutel: Selecteer weer gave en kopieer vervolgens de **versleutelings sleutel**.

    ![CIK-coderings sleutel ophalen](media/azure-stack-edge-j-series-set-azure-resource-manager-password/get-cik-portal.png)
 
3. Geef een wacht woord op dat u gaat gebruiken om verbinding te maken met Azure Resource Manager.

4. Start de Cloud shell. Selecteer op het pictogram in de rechter bovenhoek:

    ![Cloud shell starten](media/azure-stack-edge-j-series-set-azure-resource-manager-password/start-cloud-shell.png) 

    Als de Cloud shell is gestart, moet u mogelijk overschakelen naar Power shell.

    ![Cloud Shell](media/azure-stack-edge-j-series-set-azure-resource-manager-password/cloud-shell.png)   


5. Context instellen. Type:

    `Set-AzContext -SubscriptionId <Subscription ID>`

    Hier volgt een voorbeeld van uitvoer:

    
    ```azurepowershell
    PS Azure:\> Set-AzContext -SubscriptionId 8eb87630-972c-4c36-a270-f330e6c063df
    
        Name        Account   SubscriptionName   Environment  TenantId
        ----       -------    ----------------   -----------  --------
        DataBox_Edge_Test (8eb87630-972c-4c36-a… MSI@50342 DataBox_Edge_Tes AzureCloud           72f988bf-86f1-41af-91ab-2d7…
    
        PS Azure:/
    ```
    
5.  Als u oude PS-modules hebt, moet u deze installeren.

    `Remove-Module  Az.DataBoxEdge -force`

    Hier volgt een voorbeeld uitvoer. In dit voor beeld waren er geen oude modules om te worden geïnstalleerd.

    
    ```azurepowershell
        PS Azure:\> Remove-Module  Az.DataBoxEdge -force
        Remove-Module : No modules were removed. Verify that the specification of modules to remove is correct and those modules exist in the runspace.
        At line:1 char:1
        + Remove-Module  Az.DataBoxEdge -force
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ResourceUnavailable: (:) [Remove-Module], InvalidOperationException
        + FullyQualifiedErrorId : Modules_NoModulesRemoved,Microsoft.PowerShell.Commands.RemoveModuleCommand
    
        PS Azure:\
    ```

6. Met de volgende reeks opdrachten wordt een script gedownload en uitgevoerd om Power shell-modules te installeren.
    
    ```azurepowershell
        cd ~/clouddrive
        wget https://aka.ms/dbe-cmdlet-beta -O Az.DataBoxEdge.zip
        unzip ./Az.DataBoxEdge.zip
        Import-Module ~/clouddrive/Az.DataBoxEdge/Az.DataBoxEdge.psd1 -Force
    ```

7. In de volgende reeks opdrachten moet u de resource naam, de naam van de resource groep, de versleutelings sleutel en het wacht woord opgeven dat u in de vorige stap hebt opgegeven.

    ```azurepowershell
    $devicename = "<Azure Stack Edge resource name>"
    $resourceGroup = "<Resource group name>"
    $cik = "<Encryption key>"
    $password = "<Password>"
    ```
    De para meters voor wacht woord-en versleutelings sleutel moeten worden door gegeven als beveiligde teken reeksen. Gebruik de volgende cmdlets om het wacht woord en de versleutelings sleutel te converteren naar beveiligde teken reeksen.

    ```azurepowershell
    $pass = ConvertTo-SecureString $password -AsPlainText -Force
    $key = ConvertTo-SecureString $cik -AsPlainText -Force
    ```
    Gebruik de hierboven gegenereerde beveiligde teken reeksen als para meters in de cmdlet Set-AzDataBoxEdgeUser om het wacht woord opnieuw in te stellen. Gebruik dezelfde resource groep die u hebt gebruikt bij het maken van de Azure Stack EDGE/Data Box Gateway resource.

    ```azurepowershell
    Set-AzDataBoxEdgeUser -ResourceGroupName $resourceGroup -DeviceName $devicename -Name EdgeARMUser  -Password $pass -EncryptionKey $key
    ```
    Hier volgt een voor beeld van de uitvoer.
    
    ```azurepowershell
    PS /home/aseuser/clouddrive> $devicename = "myaseresource"
    PS /home/aseuser/clouddrive> $resourceGroup = "myaserg"
    PS /home/aseuser/clouddrive> $cik = "54a7450fd7b3c506e10efea4e0c88a9390f37e299fbf43e01fb5dfe483ac036b6d0f85a6246e1926e297f98c0ff84c20a57348c689eff179ce31571fc787ac0a"
    PS /home/aseuser/clouddrive> $password = "Password2"
    PS /home/aseuser/clouddrive> $pass = ConvertTo-SecureString $password -AsPlainText -Force
    PS /home/aseuser/clouddrive> $key = ConvertTo-SecureString $cik -AsPlainText -Force
    PS /home/aseuser/clouddrive> Set-AzDataBoxEdgeUser -ResourceGroupName $resourceGroup -DeviceName $devicename -Name EdgeARMUser  -Password $pass -EncryptionKey $key
    
        User name   Type ResourceGroupName DeviceName
    ---------   ---- ----------------- ----------
        EdgeARMUser ARM  myaserg        myaseresource
    
        PS /home/aseuser/clouddrive>
    ```
Gebruik het nieuwe wacht woord om verbinding te maken met Azure Resource Manager.

## <a name="next-steps"></a>Volgende stappen

[Verbinding maken met Azure Resource Manager](azure-stack-edge-j-series-connect-resource-manager.md)
