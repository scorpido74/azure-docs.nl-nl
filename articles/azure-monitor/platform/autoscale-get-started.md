---
title: Aan de slag met autoscale in Azure
description: Meer informatie over het schalen van uw resource Web App, Cloud Service, Virtual Machine of Virtual Machine Scale set in Azure.
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 0a40496e4d496d0062c6ee7a6ab935c1ad9b35a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75396352"
---
# <a name="get-started-with-autoscale-in-azure"></a>Aan de slag met automatische schaalaanpassing in Azure
In dit artikel wordt beschreven hoe u uw instellingen voor automatische schaal instelt voor uw resource in de Microsoft Azure-portal.

Azure Monitor autoscale is alleen van toepassing op [Virtual Machine Scale Sets,](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [Cloud Services](https://azure.microsoft.com/services/cloud-services/), App Service - [Web Apps](https://azure.microsoft.com/services/app-service/web/)en API [Management services](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>De instellingen voor automatisch schalen in uw abonnement ontdekken
U alle bronnen ontdekken waarvoor Autoscale van toepassing is in Azure Monitor. Gebruik de volgende stappen voor een stapsgewijze walkthrough:

1. Open de [Azure-portal.][1]
1. Klik op het Azure Monitor-pictogram in het linkerdeelvenster.
  ![Azure-monitor openen][2]
1. Klik **op Autoscale** om alle resources weer te geven waarvoor Autoscale van toepassing is, samen met de huidige status Autoscale.
  ![Autoscale ontdekken in Azure Monitor][3]

U het filtervenster bovenaan gebruiken om de lijst te bereiken om resources in een specifieke resourcegroep, specifieke resourcetypen of een specifieke resource te selecteren.

Voor elke resource vindt u het huidige aantal instantieen de status Automatisch schalen. De status Autoscale kan zijn:

- **Niet geconfigureerd**: U hebt Autoscale nog niet ingeschakeld voor deze bron.
- **Ingeschakeld**: U hebt Autoscale ingeschakeld voor deze resource.
- **Uitgeschakeld:** U hebt Autoscale voor deze bron uitgeschakeld.

## <a name="create-your-first-autoscale-setting"></a>Uw eerste instelling voor automatisch schalen maken

Laten we nu een eenvoudige stapsgewijze walkthrough doorlopen om uw eerste autoscale-instelling te maken.

1. Open het **mes automatisch schalen** in Azure Monitor en selecteer een resource die u wilt schalen. (De volgende stappen gebruiken een App Service-abonnement dat is gekoppeld aan een web-app. U [uw eerste ASP.NET web-app in Azure in 5 minuten maken.][4])
1. Houd er rekening mee dat het huidige aantal instantie 1 is. Klik **op Automatisch schalen inschakelen**.
  ![Schaalinstelling voor nieuwe web-app][5]
1. Geef een naam op voor de schaalinstelling en klik op **Een regel toevoegen**. Let op de opties voor schaalregels die worden geopend als een contextvenster aan de rechterkant. Standaard stelt dit de optie in om het aantal instanceen met 1 te schalen als het CPU-percentage van de resource hoger is dan 70 procent. Laat het bij de standaardwaarden en klik op **Toevoegen**.
  ![Schaalinstelling maken voor een web-app][6]
1. Je hebt nu je eerste schaalregel gemaakt. Houd er rekening mee dat de UX aanbevolen best practices en stelt dat "Het wordt aanbevolen om ten minste een schaal in de regel." Dit doet u als volgt:

    a. Klik op **Een regel toevoegen**.

    b. **Operator** instellen op **Minder dan**.

    c. **Drempel instellen** op **20**.

    d. **Stel Bewerking** in om het aantal te **verlagen met**.

   U moet nu een schaalinstelling hebben die uit/schaalt op basis van CPU-gebruik.
   ![Schaal op basis van CPU][8]
1. Klik op **Opslaan**.

Gefeliciteerd! U hebt nu met succes uw eerste schaalinstelling gemaakt om uw web-app automatisch te schalen op basis van cpu-gebruik.

> [!NOTE]
> Dezelfde stappen zijn van toepassing om aan de slag te gaan met een virtuele machineschaalset of cloudservicerol.

## <a name="other-considerations"></a>Andere overwegingen
### <a name="scale-based-on-a-schedule"></a>Schalen op basis van een schema
Naast de schaal op basis van CPU, u uw schaal anders instellen voor specifieke dagen van de week.

1. Klik **op Een schaalvoorwaarde toevoegen**.
1. Het instellen van de schaalmodus en de regels is hetzelfde als de standaardvoorwaarde.
1. Selecteer **Specifieke dagen herhalen** voor het schema.
1. Selecteer de dagen en de begin-/eindtijd voor wanneer de schaalvoorwaarde moet worden toegepast.

![Schaalvoorwaarde op basis van planning][9]
### <a name="scale-differently-on-specific-dates"></a>Anders schalen op specifieke datums
Naast de schaal op basis van CPU, u uw schaal anders instellen voor specifieke datums.

1. Klik **op Een schaalvoorwaarde toevoegen**.
1. Het instellen van de schaalmodus en de regels is hetzelfde als de standaardvoorwaarde.
1. Selecteer **Begin-/einddatum opgeven** voor de planning.
1. Selecteer de begin-/einddatum en de begin-/eindtijd voor wanneer de schaalvoorwaarde moet worden toegepast.

![Schaalvoorwaarde op basis van datums][10]

### <a name="view-the-scale-history-of-your-resource"></a>De schaalgeschiedenis van uw resource weergeven
Wanneer uw resource wordt opgeschaald of omlaag wordt geschaald, wordt een gebeurtenis aangemeld in het activiteitenlogboek. U de schaalgeschiedenis van uw resource van de afgelopen 24 uur bekijken door over te schakelen naar het tabblad **Geschiedenis uitvoeren.**

![Uitvoer.gesch][11]

Als u de volledige schaalgeschiedenis (maximaal 90 dagen) wilt bekijken, selecteert u **Klik hier voor meer details.** Het activiteitenlogboek wordt geopend, waarbij Automatisch schalen vooraf zijn geselecteerd voor uw resource en categorie.

### <a name="view-the-scale-definition-of-your-resource"></a>De schaaldefinitie van uw resource weergeven
Autoscale is een Azure Resource Manager-bron. U de schaaldefinitie in JSON bekijken door over te schakelen naar het **tabblad JSON.**

![Schaaldefinitie][12]

U indien nodig direct wijzigingen aanbrengen in JSON. Deze wijzigingen worden weergegeven nadat u ze hebt opgeslagen.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Automatisch schalen uitschakelen en uw exemplaren handmatig schalen
Er kunnen momenten zijn waarop u de huidige schaalinstelling wilt uitschakelen en uw resource handmatig wilt schalen.

Klik bovenaan op de knop **Autoschaal uitschakelen.**
![Autoscale uitschakelen][13]

> [!NOTE]
> Met deze optie wordt uw configuratie uitgeschakeld. U er echter weer naar terug gaan nadat u Autoscale opnieuw hebt ingeschakeld.

U nu het aantal exemplaren instellen waarop u handmatig wilt schalen.

![Handmatige schaal instellen][14]

U altijd terugkeren naar Autoscale door op **Automatisch schalen inschakelen** te klikken en vervolgens op **Opslaan**.

## <a name="next-steps"></a>Volgende stappen
- [Een activiteitslogboekwaarschuwing maken om alle autoscale-enginebewerkingen op uw abonnement te controleren](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Een activiteitslogboekwaarschuwing maken om alle mislukte automatische schaal-in/scale-outbewerkingen op uw abonnement te controleren](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-dotnet
[5]: ./media/autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/autoscale-get-started/scale-in-recommendation.png
[8]: ./media/autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/autoscale-get-started/scale-condition-schedule.png
[10]: ./media/autoscale-get-started/scale-condition-dates.png
[11]: ./media/autoscale-get-started/scale-history.png
[12]: ./media/autoscale-get-started/scale-definition-json.png
[13]: ./media/autoscale-get-started/disable-autoscale.png
[14]: ./media/autoscale-get-started/set-manualscale.png

