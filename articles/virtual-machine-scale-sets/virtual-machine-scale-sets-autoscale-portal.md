---
title: Virtuele machineschaalsets automatisch schalen in de Azure-portal
description: Regels voor automatische schaal maken voor virtuele machineschaalsets in de Azure-portal
author: ju-shim
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: jushiman
ms.openlocfilehash: 1915b144aec5a5447504c70d18dbf420d255a08e
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010287"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Automatisch een virtuele machineschaalschalen schalen in de Azure-portal
Wanneer u een schaalset maakt, definieert u het aantal VM-exemplaren dat u wilt uitvoeren. Wanneer de vraag van de toepassing verandert, kunt u het aantal VM-exemplaren automatisch vergroten of verkleinen. De mogelijkheid van automatisch schalen stelt u in staat om altijd te voldoen aan de vraag van klanten houden of om gedurende de levenscyclus van uw app te reageren op wijzigingen in de prestaties van de toepassing.

In dit artikel ziet u hoe u regels voor automatische schaal maakt in de Azure-portal die de prestaties van de VM-exemplaren in uw schaalset controleren. Deze regels voor automatische schaal verhogen of verminderen het aantal VM-exemplaren als reactie op deze prestatiestatistieken. U deze stappen ook uitvoeren met [Azure PowerShell](tutorial-autoscale-powershell.md) of azure [CLI.](tutorial-autoscale-cli.md)


## <a name="prerequisites"></a>Vereisten
Als u regels voor automatische schaal wilt maken, hebt u een bestaande virtuele machineschaalset nodig. U een schaalset maken met de [Azure-portal,](quick-create-portal.md) [Azure PowerShell](quick-create-powershell.md)of [Azure CLI](quick-create-cli.md).


## <a name="create-a-rule-to-automatically-scale-out"></a>Een regel maken om automatisch uit te schalen
Als de vraag van uw toepassing toeneemt, neemt de belasting van de VM-exemplaren in de schaalset ook toe. Als deze toegenomen belasting consistent is, en geen piekbelasting is, kunt u regels voor automatisch schalen configureren om het aantal VM-exemplaren in de schaalset te verhogen. Wanneer deze VM-exemplaren worden gemaakt en uw toepassingen worden geïmplementeerd, zorgt de schaalset ervoor dat er via de load balancer verkeer wordt gedistribueerd naar de exemplaren. U bepaalt welke meetwaarden gegevens moeten worden bewaakt, zoals CPU of schijf, hoe lang de belasting van de toepassing overeen moet komen met een bepaalde drempelwaarde en hoeveel VM-exemplaren er moeten worden toegevoegd aan de schaalset.

1. Open de Azure-portal en selecteer **Resourcegroepen** in het menu aan de linkerkant van het dashboard.
2. Selecteer de resourcegroep die uw schaalset bevat en kies vervolgens uw schaalset in de lijst met bronnen.
3. Kies **Schalen** in het menu aan de linkerkant van het venster met de schaalset. Selecteer de knop om automatisch schalen in te **schakelen:**

    ![Automatisch schalen inschakelen in de Azure-portal](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Voer een naam in voor uw instellingen, zoals *automatisch schalen,* en selecteer de optie Om een regel toe te **voegen.**

5. Laten we een regel maken die het aantal VM-exemplaren in een schaalset verhoogt wanneer de gemiddelde CPU-belasting meer dan 70% bedraagt over een periode van 10 minuten. Wanneer de regel wordt geactiveerd, wordt het aantal VM-exemplaren met 20% verhoogd. In schaalsets met een klein aantal VM-exemplaren u de **bewerking** instellen om *het aantal te verhogen* en vervolgens *1* of *2* opgeven voor het *aantal exemplaren*. In schaalsets met een groot aantal VM-exemplaren kan een toename van 10% of 20% VM-exemplaren geschikter zijn.

    Geef de volgende instellingen voor uw regel op:
    
    | Parameter              | Uitleg                                                                                                         | Waarde          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Tijdsaggregatie*     | Hiermee definieert u hoe de verzamelde meetwaarden moeten worden samengevoegd voor analyse.                                                | Average        |
    | *Metrische naam*          | De prestatiemeetwaarde die u wilt bewaken en waarvoor u acties wilt toepassen op de schaalset.                                                   | Percentage CPU |
    | *Tijdsintervalstatistieken* | Hiermee bepaalt u hoe de verzamelde statistieken in elke keer dat korrel moet worden samengevoegd voor analyse.                             | Average        |
    | *Operator*             | De operator die wordt gebruikt voor het vergelijken van de meetwaarden met de drempelwaarde.                                                     | Groter dan   |
    | *Drempel*            | Het percentage dat ervoor zorgt dat de regel voor automatisch schalen een actie activeert.                                                 | 70             |
    | *Duur*             | De hoeveelheid tijd waarna de meetwaarde en drempelwaarde met elkaar worden vergeleken.                                   | 10 minuten     |
    | *Bewerking*            | Hiermee bepaalt u of de schaalset omhoog of omlaag moet worden schaalt wanneer de regel van toepassing is en met welke verhoging                        | Verhoog het percentage met |
    | *Instantietelling*       | Het percentage VM-exemplaren dat moet worden gewijzigd wanneer de regel wordt geactiveerd.                                            | 20             |
    | *Afkoelen (minuten)*  | De tijd die moet worden gewacht voordat de regel opnieuw wordt toegepast, zodat de acties voor automatisch schalen voldoende tijd hebben om effectief te zijn. | 5 minuten      |

    In de volgende voorbeelden wordt een regel weergegeven die is gemaakt in de Azure-portal die overeenkomt met deze instellingen:

    ![Een regel voor automatisch schalen maken om het aantal VM-exemplaren te verhogen](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. Als u de regel wilt maken, selecteert u **Toevoegen**


## <a name="create-a-rule-to-automatically-scale-in"></a>Een regel maken om automatisch in te schalen
In het weekend of 's avonds kan de vraag voor uw toepassing afnemen. Als deze afgenomen belasting consistent is gedurende een bepaalde periode, kunt u regels voor automatisch schalen configureren om het aantal VM-exemplaren in de schaalset te verlagen. Deze inschaalactie reduceert de kosten voor het uitvoeren van uw schaalset, aangezien u alleen het aantal exemplaren uitvoert dat vereist is om te voldoen aan de actuele vraag.

1. Kies ervoor om opnieuw een regel toe te **voegen.**
2. Maak een regel die het aantal VM-exemplaren in een schaalset vermindert wanneer de gemiddelde CPU-belasting vervolgens onder de 30% daalt over een periode van 10 minuten. Wanneer de regel wordt geactiveerd, wordt het aantal VM-exemplaren met 20% verminderd.

    Gebruik dezelfde aanpak als bij de vorige regel. Pas de volgende instellingen voor uw regel aan:
    
    | Parameter              | Uitleg                                                                                                          | Waarde          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operator*             | De operator die wordt gebruikt voor het vergelijken van de meetwaarden met de drempelwaarde.                                                      | Kleiner dan   |
    | *Drempel*            | Het percentage dat ervoor zorgt dat de regel voor automatisch schalen een actie activeert.                                                 | 30             |
    | *Bewerking*            | Hiermee bepaalt u of de schaalset omhoog of omlaag moet worden schaalt wanneer de regel van toepassing is en met welke verhoging                         | Percentage verlagen door |
    | *Instantietelling*       | Het percentage VM-exemplaren dat moet worden gewijzigd wanneer de regel wordt geactiveerd.                                             | 20             |

3. Als u de regel wilt maken, selecteert u **Toevoegen**


## <a name="define-autoscale-instance-limits"></a>Instantielimieten voor automatisch schalen definiëren
In uw autoscale-profiel moet een minimum-, maximum- en standaardaantal VM-exemplaren worden gedefinieerd. Wanneer uw regels voor automatische schaal worden toegepast, zorgen deze instantielimieten ervoor dat u niet verder dan het maximum aantal exemplaren uitschaalt of niet verder schaalt dan het minimum aantal exemplaren.

1. Stel de volgende instantielimieten in:

    | Minimum | Maximum | Standaard|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Als u de regels en instantieslimieten voor automatisch schalen wilt toepassen, selecteert u **Opslaan**.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Aantal exemplaren in een schaalset controleren
Als u het aantal en de status van VM-exemplaren wilt zien, selecteert u **Instanties** in het menu aan de linkerkant van het venster met de schaalset. De status geeft aan of de VM-instantie *Maakt* terwijl de schaalset automatisch wordt geschaald of wordt verwijderd terwijl de schaal automatisch wordt *geschaald.*

![Een lijst met vm-exemplaren met schaalset weergeven](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Autoschaal op basis van een planning
In de vorige voorbeelden werd automatisch een schaal in- of uitgeschaald met basishoststatistieken zoals CPU-gebruik. U ook regels voor autoschaal maken op basis van schema's. Met deze regels op basis van schema's u het aantal VM-exemplaren automatisch uitschalen voordat de verwachte toename van de toepassingsvraag, zoals de kernwerkuren, wordt uitgevoerd en vervolgens automatisch schalen naar het aantal exemplaren op een tijdstip dat u verwacht dat er minder vraag is, zoals het weekend.

1. Kies **Schalen** in het menu aan de linkerkant van het venster met de schaalset. Als u de bestaande regels voor automatisch schalen wilt verwijderen die in de vorige voorbeelden zijn gemaakt, kiest u het pictogram prullenbak.

    ![De bestaande regels voor automatisch schalen verwijderen](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Kies voor **Een schaalvoorwaarde toevoegen**. Selecteer het potloodpictogram naast de regelnaam en geef een naam op zoals *Uitschalen tijdens elke werkdag*.

    ![De naam van de standaardregel voor automatisch schalen wijzigen](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Selecteer de keuzerondje om te **schalen naar een specifiek aantal instanties**.
4. Als u het aantal exemplaren wilt opschalen, voert u *10* in als het aantal exemplaren.
5. Kies **Specifieke dagen herhalen** voor het type **Planning.**
6. Selecteer alle werkdagen, van maandag tot en met vrijdag.
7. Kies de juiste tijdzone en geef vervolgens een **begintijd** van *09:00 op.*
8. Kies ervoor om opnieuw **een schaalvoorwaarde toe** te voegen. Herhaal het proces om een schema te maken met de naam *Schaal in de avond* die wordt geschaald tot *3* instanties, elke weekdag wordt herhaald en om *18:00*uur begint.
9. Als u de regels voor automatische schaal op basis van planning wilt toepassen, selecteert u **Opslaan**.

    ![Autoscaleregels maken die volgens een schema schalen](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Als u wilt zien hoe uw regels voor automatische schaal worden toegepast, selecteert u **Geschiedenis uitvoeren** boven aan het **venster Schalen.** De grafiek- en gebeurtenissenlijst geeft aan wanneer de regels voor automatische schaal worden geactiveerd en het aantal VM-exemplaren in uw schaalset toeneemt of afneemt.


## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u regels voor automatische schaal gebruiken om horizontaal te schalen en het *aantal* VM-exemplaren in uw schaalset te verhogen of te verminderen. U ook verticaal schalen om de *grootte*van de VM-instantie te vergroten of te verkleinen. Zie [Verticale automatische schaal met virtuele machineschaalsets](virtual-machine-scale-sets-vertical-scale-reprovision.md)voor meer informatie.

Zie [Virtuele machineschaalsets beheren met Azure PowerShell](virtual-machine-scale-sets-windows-manage.md)voor informatie over het beheren van uw VM-exemplaren.

Zie Acties voor automatische schaal gebruiken om [e-mail- en webhook-waarschuwingsmeldingen te verzenden in Azure Monitor](../azure-monitor/platform/autoscale-webhook-email.md)voor meer informatie over het genereren van waarschuwingen wanneer uw regels voor automatische schaal worden geactiveerd. U ook [controlelogboeken gebruiken om e-mail- en webhook-waarschuwingsmeldingen te verzenden in Azure Monitor.](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)
