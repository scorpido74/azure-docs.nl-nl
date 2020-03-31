---
title: Problemen met VM- en omgevingsfouten Azure DevTest Labs oplossen
description: Meer informatie over het oplossen van fouten in virtuele machines (VM) en het maken van omgevingen in Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 155a359608cf6d846578306545f5ce0b4003949c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166347"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Vm- en fouten bij het maken van omgevingen oplossen in Azure DevTest Labs
DevTest Labs geeft u waarschuwingen als een machinenaam ongeldig is of als u op het punt staat een labbeleid te schenden. Soms ziet u `X` rood naast uw lab-VM- of omgevingstatus die u informeert dat er iets mis is gegaan.  Dit artikel biedt een paar trucs die u gebruiken om het onderliggende probleem te vinden en, hopelijk, het probleem in de toekomst te voorkomen.

## <a name="portal-notifications"></a>Portalmeldingen
Als u de Azure-portal gebruikt, u eerst naar het **deelvenster Meldingen**kijken.  In het deelvenster Meldingen, dat beschikbaar is op de opdrachtbalk door op het **belpictogram**te klikken, wordt u weergegeven of de lab-vm of het maken van de omgeving is geslaagd of niet.  Als er een fout is opgetreden, ziet u het foutbericht dat is gekoppeld aan de creatiefout. De details geven vaak meer informatie om u te helpen het probleem op te lossen. In het volgende voorbeeld is het maken van virtuele machines mislukt omdat de kernen opraken. In het gedetailleerde bericht wordt uitgelegd hoe u het probleem oplossen en een verhoging van het kernquotum aanvragen.

![Azure-portalmelding](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>VM in corruptiestaat
Als u de status van uw vm in het lab als **beschadigd**ziet, is de onderliggende virtuele machine mogelijk verwijderd van de pagina **Virtuele machine** waarnaar de gebruiker kan navigeren vanaf de pagina **Virtuele machines** (niet vanaf de pagina DevTest Labs). Ruim je lab op in DevTest Labs door de VM uit het lab te verwijderen. Maak vervolgens uw VM opnieuw in het lab. 

![VM in beschadigde staat](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>Activiteitenlogboeken
Bekijk activiteitslogboeken als u een fout onderzoekt ergens na een poging tot het maken van uw VM of omgeving. In deze sectie ziet u hoe u logboeken voor VM's en omgevingen vinden.

## <a name="activity-logs-for-virtual-machines"></a>Activiteitslogboeken voor virtuele machines

1. Selecteer op de startpagina van uw lab de VM om de **pagina Virtuele machine** te starten.
2. Selecteer op de pagina **Virtuele machine** in de sectie **CONTROLE van** het linkermenu het **logboek Activiteit** om alle logboeken te bekijken die aan de virtuele machine zijn gekoppeld.
3. Selecteer in de items van het activiteitenlogboek de bewerking die is mislukt. De mislukte bewerking wordt `Write Virtualmachines`meestal genoemd .
4. Ga in het rechterdeelvenster naar het tabblad JSON. U ziet de details in de JSON-weergave van het logboek.

    ![Activiteitenlogboek voor een vm](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Kijk door de JSON log `statusMessage` tot u de woning vindt. Het geeft u de belangrijkste foutmelding en verdere details informatie, indien van toepassing. De volgende JSON is een voorbeeld voor de kern geciteerd overschreden fout eerder in dit artikel.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>Activiteitenlogboek voor een omgeving

Voer de volgende stappen uit om het activiteitenlogboek voor het maken van een omgeving te bekijken:

1. Selecteer op de startpagina van uw lab **Configuratie en beleid** in het linkermenu.
2. Selecteer **activiteitslogboeken** op de pagina **Configuratie en beleid** in het menu.
3. Zoek naar de fout in de activiteitenlijst in het logboek en selecteer deze.
4. Ga in het rechterdeelvenster naar het tabblad JSON en zoek naar de **statusMessage**.

    ![Milieuactiviteitslogboek](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Implementatielogboeken voor resourcebeheer-sjablonen
Als uw omgeving of virtuele machine is gemaakt door middel van automatisering, is er nog een laatste plek om foutinformatie te zoeken. Dat is het azure resource manager-sjabloonimplementatielogboek. Wanneer een labbron wordt gemaakt door middel van automatisering, wordt dit vaak gedaan via een Azure Resource Manager-sjabloonimplementatie. Zie[https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) voor voorbeeld Azure Resource Manager-sjablonen waarmee DevTest Labs-bronnen zijn gemaakt.

Voer de volgende stappen uit om de implementatielogboeken van de labsjabloon te bekijken:

1. Start de pagina voor de resourcegroep waarin het lab bestaat.
2. Selecteer **Implementaties** in het linkermenu onder **Instellingen**.
3. Zoek naar implementaties met een mislukte status en selecteer deze.
4. Selecteer **op** de pagina Implementatie de koppeling **Bewerkingsdetails** voor de bewerking die is mislukt.
5. U ziet details over de bewerking die is mislukt in het venster Details van de **Bewerking.**

## <a name="next-steps"></a>Volgende stappen
Zie [Artefact-fouten oplossen](devtest-lab-troubleshoot-artifact-failure.md)
