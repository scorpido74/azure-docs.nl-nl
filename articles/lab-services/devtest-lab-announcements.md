---
title: Een aankondiging plaatsen in een lab in Azure DevTest Labs | Microsoft Documenten
description: Meer informatie over het toevoegen van een aankondiging aan een lab in Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 67a09946-4584-425e-a94c-abe57c9cbb82
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 84120b07de3a03a049493eb973b6dc46f8668387
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75976449"
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Een aankondiging plaatsen in een lab in Azure DevTest Labs

Als labbeheerder u een aangepaste aankondiging plaatsen in een bestaand lab om gebruikers op de hoogte te stellen van recente wijzigingen of toevoegingen aan het lab. U gebruikers bijvoorbeeld informeren over:

- Nieuwe VM-formaten die beschikbaar zijn
- Afbeeldingen die momenteel onbruikbaar zijn
- Updates van het labbeleid

Eenmaal geplaatst, wordt de aankondiging weergegeven op de overzichtspagina van het lab en kan de gebruiker deze selecteren voor meer details.

De aankondigingsfunctie is bedoeld om te worden gebruikt voor tijdelijke meldingen.  U een aankondiging eenvoudig uitschakelen nadat deze niet meer nodig is.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>Stappen om een aankondiging te plaatsen in een bestaand lab

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecteer indien nodig **Alle services**en selecteer **Vervolgens DevTest Labs** in de lijst. (Uw lab wordt mogelijk al weergegeven op het dashboard onder **Alle bronnen**).
1. Selecteer in de lijst met labs het lab waarin u een aankondiging wilt plaatsen.
1. Selecteer **Configuratie en beleid**in het gebied **Overzicht** van het lab.

    ![Knop Configuratie en beleid](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. Selecteer aan de linkerkant onder **INSTELLINGEN**de optie **Lab-aankondiging**.

    ![De aankondigingsknop van het laboratorium](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. Als u een bericht wilt maken voor de gebruikers in dit lab, stelt u **Ingeschakeld in** **op Ja**.

1. U een **vervaldatum** invoeren om een datum en tijd op te geven waarna de aankondiging niet meer aan gebruikers wordt weergegeven. Als u geen vervaldatum invoert, blijft de aankondiging bestaan totdat u deze uitschakelt.

   > [!NOTE]
   > Nadat de aankondiging is verlopen, wordt deze niet meer weergegeven aan gebruikers, maar bestaat het nog steeds in het **aankondigingsvenster van het lab.** U er bewerkingen aan aanbrengen en deze opnieuw inschakelen om het opnieuw actief te maken.
   >
   >

1. Voer een **aankondigingstitel** en de **aankondigingstekst in**.

   De titel kan maximaal 100 tekens zijn en wordt aan de gebruiker getoond op de overzichtspagina van het lab. Als de gebruiker de titel selecteert, wordt de aankondigingstekst weergegeven.

   De aankondigingstekst accepteert afwaardering. Als u de aankondigingstekst invoert, u het bericht onder aan het scherm bekijken in het gebied Voorbeeld.

    ![Het aankondigingsscherm van het lab om het bericht te maken.](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. Selecteer **Opslaan** zodra je aankondiging klaar is om te posten.

Wanneer u deze aankondiging niet langer wilt weergeven aan gebruikers in het lab, gaat u terug naar de **aankondigingspagina van het lab** en stelt **u Ingeschakeld** in op **Nee.** Als u een vervaldatum hebt opgegeven, wordt de aankondiging op die datum en tijd automatisch uitgeschakeld.

## <a name="steps-for-users-to-view-an-announcement"></a>Stappen voor gebruikers om een aankondiging te bekijken

1. Selecteer in de [Azure-portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)een lab.

1. Als het lab er een aankondiging voor heeft geplaatst, wordt een informatiebericht weergegeven boven aan de overzichtspagina van het lab. Dit informatiebericht is de aankondigingstitel die is opgegeven toen de aankondiging werd gemaakt.

    ![Lab aankondiging op overzicht pagina](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. De gebruiker kan het bericht selecteren om de volledige aankondiging te bekijken.

    ![Meer informatie voor de aankondiging van het lab](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon
U een aankondiging opgeven als onderdeel van een Azure Resource Manager-sjabloon zoals weergegeven in het volgende voorbeeld:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string",
            "defaultValue": "devtestlabfromarm"
        },
        "regionId": {
            "type": "string",
            "defaultValue": "eastus"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-04-26-preview",
            "name": "[parameters('name')]",
            "type": "Microsoft.DevTestLab/labs",
            "location": "[parameters('regionId')]",
            "tags": {},
            "properties": {
                "labStorageType": "Premium",
                "announcement":
                {
                    "title": "Important! Three images are currently inaccessible. Click for more information.",
                    "markdown":"# Images are inaccessible\n\nThe following 3 images are currently not available for use: \n\n- image1\n- image2\n- image3\n\nI am working to fix the problem ASAP.",
                    "enabled": "Enabled",
                    "expirationDate":"2018-12-31T06:00:00+00:00",
                    "expired": "false"
                },
                "support": {
                    "markdown": "",
                    "enabled": "Enabled"
                }
            },
            "resources": [
                {
                    "apiVersion": "2017-04-26-preview",
                    "name": "LabVmsShutdown",
                    "location": "[parameters('regionId')]",
                    "type": "schedules",
                    "dependsOn": [
                        "[resourceId('Microsoft.DevTestLab/labs', parameters('name'))]"
                    ],
                    "properties": {
                        "status": "Enabled",
                        "timeZoneId": "Eastern Standard Time",
                        "dailyRecurrence": {
                            "time": "1900"
                        },
                        "taskType": "LabVmsShutdownTask",
                        "notificationSettings": {
                            "status": "Disabled",
                            "timeInMinutes": 30
                        }
                    }
                },
                {
                    "apiVersion": "2017-04-26-preview",
                    "name": "[concat('Dtl', parameters('name'))]",
                    "type": "virtualNetworks",
                    "location": "[parameters('regionId')]",
                    "dependsOn": [
                        "[resourceId('Microsoft.DevTestLab/labs', parameters('name'))]"
                    ]
                }
            ]
        }
    ]
}
```

U een Azure Resource Manager-sjabloon implementeren met een van de volgende manieren:

- [Azure-portal](../azure-resource-manager/templates/deploy-portal.md)
- [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Azure-CLI](../azure-resource-manager/templates/deploy-cli.md)
- [REST-API](../azure-resource-manager/templates/deploy-rest.md)

## <a name="next-steps"></a>Volgende stappen
* Als u een labbeleid wijzigt of instelt, u een aankondiging plaatsen om gebruikers te informeren. [Stel beleidsregels en schema's](devtest-lab-set-lab-policy.md) in met informatie over het toepassen van beperkingen en conventies in uw abonnement met behulp van aangepast beleid.
* Bekijk [de sjabloongalerie voor Azure Resource Manager QuickStart van DevTest Labs.](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
