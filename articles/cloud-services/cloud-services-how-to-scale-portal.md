---
title: Een cloudservice automatisch schalen in de portal | Microsoft Documenten
description: Meer informatie over het gebruik van de portal om automatische schaalregels te configureren voor een webrol of werknemersrol voor cloudservices in Azure.
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 05/18/2017
ms.author: tagore
ms.openlocfilehash: 5880544137855a2ea5bcd6d6e4bada46563564ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360834"
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-portal"></a>Automatisch schalen configureren voor een Cloud Service in de portal

Er kunnen voorwaarden worden ingesteld voor een rol van een cloudservicemedewerker die een schaalin- of out-bewerking activeert. De voorwaarden voor de rol kunnen worden gebaseerd op de CPU, schijf of netwerkbelasting van de rol. U ook een voorwaarde instellen op basis van een berichtenwachtrij of de statistiek van een andere Azure-bron die is gekoppeld aan uw abonnement.

> [!NOTE]
> Dit artikel richt zich op cloudserviceweb- en werknemersrollen. Wanneer u een virtuele machine (klassiek) rechtstreeks maakt, wordt deze gehost in een cloudservice. U een standaard virtuele machine schalen door deze te koppelen aan een [beschikbaarheidsset](../virtual-machines/windows/classic/configure-availability-classic.md) en deze handmatig in- of uitschakelen.

## <a name="considerations"></a>Overwegingen
U moet de volgende informatie overwegen voordat u schalen voor uw toepassing configureert:

* Schalen wordt beïnvloed door kerngebruik.

    Grotere rolexemplaren gebruiken meer kernen. U een toepassing alleen schalen binnen de limiet van de cores voor uw abonnement. Stel dat uw abonnement een limiet van 20 cores heeft. Als u een toepassing uitvoert met twee middelgrote cloudservices (in totaal 4 cores), u alleen andere implementaties van cloudservices in uw abonnement opschalen met de resterende 16 cores. Zie [Cloud Service Sizes](cloud-services-sizes-specs.md)voor meer informatie over formaten.

* U schalen op basis van een drempelwaarde voor wachtrijberichten. Zie [Wachtrijopslagservice gebruiken](../storage/queues/storage-dotnet-how-to-use-queues.md)voor meer informatie over het gebruik van wachtrijen.

* U ook andere bronnen schalen die aan uw abonnement zijn gekoppeld.

* Als u een hoge beschikbaarheid van uw toepassing wilt inschakelen, moet u ervoor zorgen dat deze wordt geïmplementeerd met twee of meer rolexemplaren. Zie [ServiceLevel Agreements voor](https://azure.microsoft.com/support/legal/sla/)meer informatie.

* Automatisch schalen gebeurt alleen wanneer alle rollen in de status **Gereed** staan.  


## <a name="where-scale-is-located"></a>Waar de schaal zich bevindt
Nadat u uw cloudservice hebt geselecteerd, moet u het cloudserviceblad zichtbaar hebben.

1. Selecteer op het cloudserviceblad op de tegel **Rollen en instanties** de naam van de cloudservice.   
   **BELANGRIJK:** Zorg ervoor dat u op de rol van de cloudservice klikt, niet op de rolinstantie die zich onder de rol bevindt.

    ![](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Selecteer de **schaaltegel.**

    ![](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Automatische schaal
U schaalinstellingen voor een rol configureren met twee modi **handmatig** of **automatisch.** Handleiding is zoals je zou verwachten, stel je de absolute telling van exemplaren. Met automatisch u echter regels instellen die bepalen hoe en door hoeveel u moet schalen.

Stel de **optie Schalen in** op planning en **prestatieregels**.

![Cloudservices schaalinstellingen met profiel en regel](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. Een bestaand profiel.
2. Voeg een regel toe voor het bovenliggende profiel.
3. Voeg een ander profiel toe.

Selecteer **Profiel toevoegen**. Het profiel bepaalt welke modus u wilt gebruiken voor de schaal: **altijd**, **herhaling**, **vaste datum**.

Nadat u het profiel en de regels hebt geconfigureerd, selecteert u het pictogram **Opslaan** bovenaan.

#### <a name="profile"></a>Profiel
Het profiel stelt minimum- en maximumexemplaren in voor de schaal en ook wanneer dit schaalbereik actief is.

* **Altijd**

    Houd dit aantal exemplaren altijd beschikbaar.  

    ![Cloudservice die altijd schaalt](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Herhaling**

    Kies een aantal dagen van de week om te schalen.

    ![Cloudserviceschaal met een herhalingsschema](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Vaste datum**

    Een vast datumbereik om de rol te schalen.

    ![CLoud-serviceschaal met een vaste datum](./media/cloud-services-how-to-scale-portal/select-fixed.png)

Nadat u het profiel hebt geconfigureerd, selecteert u de knop **OK** onder aan het profielblad.

#### <a name="rule"></a>Regel
Regels worden toegevoegd aan een profiel en vertegenwoordigen een voorwaarde die de schaal activeert.

De regeltrigger is gebaseerd op een statistiek van de cloudservice (CPU-gebruik, schijfactiviteit of netwerkactiviteit) waaraan u een voorwaardelijke waarde toevoegen. Bovendien u de trigger laten gebruiken op basis van een berichtenwachtrij of de statistiek van een andere Azure-bron die is gekoppeld aan uw abonnement.

![](./media/cloud-services-how-to-scale-portal/rule-settings.png)

Nadat u de regel hebt geconfigureerd, selecteert u de knop **OK** onder aan het regelblad.

## <a name="back-to-manual-scale"></a>Terug naar handmatige schaal
Navigeer naar de [schaalinstellingen](#where-scale-is-located) en stel de optie **Schalen op** in **op een instantietelling die ik handmatig invul.**

![Cloudservices schaalinstellingen met profiel en regel](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Met deze instelling verwijdert u geautomatiseerdschalen uit de rol en u het aantal instance's direct instellen.

1. De optie schaal (handmatig of geautomatiseerd).
2. Een schuifregelaar voor rolinstanties om de instanties in te stellen op schalen.
3. Instanties van de rol om naar te schalen.

Nadat u de schaalinstellingen hebt geconfigureerd, selecteert u het pictogram **Opslaan** bovenaan.



