---
title: Just-in-time-toegang aanvragen
description: Beschrijft hoe uitgevers van Azure Managed Applications just-in-time toegang tot een beheerde toepassing aanvragen.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 7f475774828bcaecd471e13de994b156041323ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651382"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Just-in-time toegang inschakelen en aanvragen voor Azure Managed Applications

Consumenten van uw beheerde toepassing kunnen terughoudend zijn om u permanente toegang te verlenen tot de beheerde resourcegroep. Als uitgever van een managertoepassing hebt u er misschien op aan dat consumenten precies weten wanneer u toegang nodig hebt tot de beheerde bronnen. Om consumenten meer controle te geven over het verlenen van toegang tot beheerde resources, biedt Azure Managed Applications een functie die just-in-time (JIT)-toegang wordt genoemd, die momenteel in preview is.

Met JIT-toegang u verhoogde toegang tot de bronnen van een beheerde toepassing aanvragen voor probleemoplossing of onderhoud. U hebt altijd alleen-lezen toegang tot de bronnen, maar voor een bepaalde periode u meer toegang hebben.

De werkstroom voor het verlenen van toegang is:

1. U voegt een beheerde toepassing toe aan de marktplaats en geeft op dat JIT-toegang beschikbaar is.

1. Tijdens de implementatie biedt de consument JIT-toegang voor dat exemplaar van de beheerde toepassing.

1. Na de implementatie kan de consument de instellingen voor JIT-toegang wijzigen.

1. U verzendt een verzoek om toegang wanneer u de beheerde resources moet oplossen of bijwerken.

1. De consument keurt uw verzoek goed.

Dit artikel richt zich op de acties die uitgevers ondernemen om JIT-toegang in te schakelen en aanvragen in te dienen. Zie [Just-in-time-toegang goedkeuren in Azure Managed Applications](approve-just-in-time-access.md)voor meer informatie over het goedkeuren van JIT-toegangsaanvragen.

## <a name="add-jit-access-step-to-ui"></a>De stap Voor JIT-toegang toevoegen aan de gebruikersinterface

Uw CreateUiDefinition.json-bestand is precies hetzelfde als het ui-bestand dat u maakt voor permanente toegang, behalve dat u een stap moet bevatten waarmee consumenten JIT-toegang kunnen inschakelen. Zie [Azure Managed Applications in de Marketplace](publish-marketplace-app.md)voor meer informatie over het publiceren van uw eerste beheerde toepassingsaanbieding in de Azure Marketplace.

Als u de JIT-mogelijkheid voor uw aanbieding wilt ondersteunen, voegt u de volgende inhoud toe aan het bestand CreateUiDefinition.json:

In "stappen":

```json
{
    "name": "jitConfiguration",
    "label": "JIT Configuration",
    "subLabel": {
        "preValidation": "Configure JIT settings for your application",
        "postValidation": "Done"
    },
    "bladeTitle": "JIT Configuration",
    "elements": [
        {
          "name": "jitConfigurationControl",
          "type": "Microsoft.Solutions.JitConfigurator",
          "label": "JIT Configuration"
        }
    ]
}
```
 
In "uitgangen":

```json
"jitAccessPolicy": "[steps('jitConfiguration').jitConfigurationControl]"
```

> [!NOTE]
> JIT-toegang is in preview. Het schema voor JIT-configuratie kan in toekomstige iteraties worden gewijzigd.

## <a name="enable-jit-access"></a>JIT-toegang inschakelen

Wanneer u uw aanbod op de markt definieert, moet u jit-toegang inschakelen.

1. Meld u aan bij de [publicatieportal van Cloud Partner](https://cloudpartner.azure.com).

1. Geef waarden op om uw beheerde toepassing in de marketplace te publiceren. Selecteer **Ja** voor **JIT Access inschakelen?**

   ![Just-in-time toegang inschakelen](./media/request-just-in-time-access/marketplace-enable.png)

U hebt een JIT-configuratiestap toegevoegd aan uw gebruikersinterface en hebt JIT-toegang in het marketplace-aanbod ingeschakeld. Wanneer consumenten uw beheerde toepassing implementeren, kunnen ze [JIT-toegang inschakelen voor hun voorbeeld.](approve-just-in-time-access.md#enable-during-deployment)

## <a name="request-access"></a>Toegang aanvragen

Wanneer u toegang nodig hebt tot de beheerde resources van de consument, stuurt u een aanvraag voor een specifieke rol, tijd en duur. De consument moet het verzoek dan goedkeuren.

Ga als lid van het JIT-toegangsverzoek:

1. Selecteer **JIT Access** voor de beheerde toepassing die u moet openen.

1. Selecteer **In aanmerking komende rollen**en selecteer **Activeren** in de kolom ACTIE voor de gewenste rol.

   ![Verzoek om toegang activeren](./media/request-just-in-time-access/send-request.png)

1. Selecteer in het formulier **Rol activeren** een begintijd en -duur om uw rol actief te laten zijn. Selecteer **Activeren** om het verzoek te verzenden.

   ![Toegang activeren](./media/request-just-in-time-access/activate-access.png) 

1. Bekijk de meldingen om te zien dat het nieuwe JIT-verzoek met succes naar de consument wordt verzonden.

   ![Melding](./media/request-just-in-time-access/in-progress.png)

   Nu moet u wachten tot de consument [uw verzoek goedkeurt.](approve-just-in-time-access.md#approve-requests)

1. Als u de status van alle JIT-aanvragen voor een beheerde toepassing wilt weergeven, selecteert u **JIT-toegangs-** en **aanvraaggeschiedenis**.

   ![Status weergeven](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Bekende problemen

De hoofd-ID van het account waarin JIT-toegang wordt aangevraagd, moet expliciet worden opgenomen in de definitie van beheerde toepassingen. Het account kan niet alleen worden opgenomen via een groep die is opgegeven in het pakket. Deze beperking zal worden vastgesteld in een toekomstige release.

## <a name="next-steps"></a>Volgende stappen

Zie [Just-in-time toegang goedkeuren in Azure Managed Applications](approve-just-in-time-access.md)voor meer informatie over het goedkeuren van aanvragen voor JIT-toegang.
