---
title: Update-implementaties voor Azure Automation maken Updatebeheer
description: In dit artikel wordt beschreven hoe u update-implementaties plant en hoe u de status ervan controleert.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 2a81376b284e0d1df84a69b969335c0e63999a00
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450260"
---
# <a name="how-to-deploy-updates-and-review-results"></a>Updates implementeren en resultaten bekijken

In dit artikel wordt beschreven hoe u een update-implementatie plant en het proces controleert nadat de implementatie is voltooid.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com)

## <a name="schedule-an-update-deployment"></a>Een update-implementatie plannen

Bij het plannen van een update-implementatie wordt een [schema](../shared-resources/schedules.md)resource gemaakt die is gekoppeld aan het**patch-MicrosoftOMSComputers**-runbook dat de update-implementatie op de doelcomputers afhandelt. U moet een implementatie plannen die volgt op de release planning en het service venster om updates te installeren. U kunt de typen updates kiezen die moeten worden opgenomen in de implementatie. Zo kunt u belangrijke updates of beveiligingsupdates opnemen en updatepakketten uitsluiten.

>[!NOTE]
>Als u na het maken van de implementatie de planningsresource verwijdert via de Azure-portal of met behulp van PowerShell, wordt de geplande update-implementatie door de verwijdering verbroken en wordt er een foutmelding weergegeven wanneer u probeert de planningsresource opnieuw te configureren vanuit de portal. U kunt de planningsresource alleen verwijderen door het bijbehorende implementatieschema te verwijderen.  

Een nieuwe update-implementatie plannen:

1. Ga in uw Automation-account naar **Update beheer** onder **Update beheer**en selecteer vervolgens **Update-implementatie plannen**.

2. Voer onder **nieuwe update-implementatie**in het veld **naam** een unieke naam in voor uw implementatie.

3. Selecteer het besturings systeem dat u wilt instellen als doel voor de update-implementatie.

4. In de regio **groepen die moeten worden bijgewerkt (preview-versie)** definieert u een query die het abonnement, de resource groepen, locaties en Tags combineert om een dynamische groep van virtuele Azure-machines samen te stellen die in uw implementatie moeten worden meegenomen. Zie voor meer informatie [dynamische groepen gebruiken met updatebeheer](update-mgmt-groups.md).

5. Selecteer in de regio **computers die u wilt bijwerken** een opgeslagen zoek opdracht, een geïmporteerde groep of kies **machines** in het vervolg keuzemenu en selecteer afzonderlijke machines. Met deze optie kunt u de gereedheid van de Log Analytics-agent voor elke machine bekijken. Zie [Computergroepen in Azure Monitorlogboeken](../../azure-monitor/platform/computer-groups.md) voor meer informatie over de verschillende manieren waarop u computergroepen kunt maken in Azure Monitor-logboeken.

6. Gebruik de regio **Update classificaties** om [Update classificaties](update-mgmt-view-update-assessments.md#work-with-update-classifications) voor producten op te geven. Voor elk product deselecteert u alle ondersteunde update classificaties, maar ook voor de implementatie van de update.

7. Gebruik de regio **updates opnemen/uitsluiten** om specifieke updates voor implementatie te selecteren. Op de pagina opnemen/uitsluiten worden de updates weer gegeven op KB-artikel-ID-nummers die moeten worden opgenomen of uitgesloten.
    
   > [!IMPORTANT]
   > Houd er rekening mee dat uitsluitingen insluitingen opheffen. Als u bijvoorbeeld een uitsluitings regel van opgeeft `*` , updatebeheer sluit alle patches of pakketten van de installatie uit. Uitgesloten patches worden nog steeds weer gegeven als ontbrekend op de computers. Als u op Linux-computers een pakket opneemt dat een afhankelijk pakket bevat dat is uitgesloten, installeert Updatebeheer het hoofdpakket niet.

   > [!NOTE]
   > U kunt geen updates opgeven die in de update-implementatie zijn vervangen.

8. Selecteer **schema-instellingen**. De standaard begintijd is 30 minuten na de huidige tijd. U kunt de starttijd op elke gewenste tijd instellen, maar minstens 10 minuten na de huidige tijd.

9. Gebruik het veld **recurrence** om op te geven of de implementatie één keer wordt uitgevoerd of een terugkerend schema gebruikt en selecteer **OK**.

10. Selecteer in de regio **pre-scripts + post-scripts (preview)** de scripts die moeten worden uitgevoerd vóór en na de implementatie. Zie [pre-scripts en post scripts beheren](update-mgmt-pre-post-scripts.md)voor meer informatie.
    
11. Gebruik het veld **onderhouds venster (minuten)** om de hoeveelheid tijd op te geven die is toegestaan voor het installeren van updates. Houd rekening met de volgende details wanneer u een onderhoudsvenster opgeeft:

    * Onderhoudsvensters bepalen hoeveel updates worden geïnstalleerd.
    * Updatebeheer stopt niet met het installeren van nieuwe updates als het einde van een onderhoudsvenster nadert.
    * Updatebeheer wordt niet beëindigd als er updates worden uitgevoerd wanneer het onderhoudsvenster wordt overschreden.
    * Als het onderhoudsvenster op Windows wordt overschreden, komt dat vaak doordat het installeren van een servicepack-update lang duurt.

    > [!NOTE]
    > Om te voor komen dat updates buiten een onderhouds venster op Ubuntu worden toegepast, `Unattended-Upgrade` moet u het pakket opnieuw configureren om automatische updates uit te scha kelen. Zie het [onderwerp automatische updates in de Ubuntu-Server handleiding](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)voor informatie over het configureren van het pakket.

12. Gebruik het veld **Opties voor opnieuw opstarten** om op te geven hoe opnieuw opstarten tijdens de implementatie moet worden afgehandeld. De volgende opties zijn beschikbaar: 
    * Opnieuw opstarten indien nodig (standaard)
    * Altijd opnieuw opstarten
    * Nooit opnieuw opstarten
    * Alleen opnieuw opstarten; met deze optie worden geen updates geïnstalleerd

    > [!NOTE]
    > De registersleutels onder [Registry keys used to manage restart](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) (registersleutels voor het beheren van opnieuw opstarten) kunnen een gebeurtenis voor opnieuw opstarten veroorzaken als **Opties voor opnieuw opstarten** is ingesteld op **Nooit opnieuw opstarten**.

13. Wanneer u klaar bent met het configureren van de implementatie planning, selecteert u **maken**.

    ![Deelvenster Planningsinstellingen bijwerken](./media/update-mgmt-deploy-updates/manageupdates-schedule-win.png)

14. U keert nu terug naar het statusdashboard. Selecteer **geplande update-implementaties** om het implementatie schema weer te geven dat u hebt gemaakt.

## <a name="schedule-an-update-deployment-programmatically"></a>Een update-implementatie via een programma plannen

Zie [Software Update Configurations - Create](/rest/api/automation/softwareupdateconfigurations/create) (software-updateconfiguraties: maken) als u wilt leren hoe u een update-implementatie kunt maken met de REST-API.

U kunt een voor beeld-runbook gebruiken om een wekelijkse update-implementatie te maken. Zie voor meer informatie over dit runbook [Create a weekly update deployment for one or more VMs in a resource group](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1) (een wekelijkse update-implementatie maken voor een of meer VM's in een resourcegroep).

## <a name="check-deployment-status"></a>Implementatie status controleren

Nadat de geplande implementatie is gestart, kunt u de status ervan weer geven op het tabblad **Update-implementaties** onder **Update beheer**. Als de implementatie momenteel wordt uitgevoerd, is de status **Wordt uitgevoerd**. Wanneer de implementatie is voltooid, verandert de status in **geslaagd**. Als er fouten optreden met een of meer updates in de implementatie, verandert de status in **Gedeeltelijk mislukt**.

## <a name="view-results-of-a-completed-update-deployment"></a>Resultaten van een voltooide update-implementatie weer geven

Wanneer de implementatie is voltooid, kunt u deze selecteren om de resultaten weer te geven.

[![Het dash board voor de implementatie status bijwerken voor een specifieke implementatie](./media/update-mgmt-deploy-updates/manageupdates-view-results.png)](./media/update-mgmt-deploy-updates/manageupdates-view-results-expanded.png#lightbox)

Onder **Update resultaten**bevat een samen vatting het totale aantal updates en de implementatie resultaten op de doel-vm's. In de tabel aan de rechter kant ziet u een gedetailleerde uitsplitsing van de updates en de installatie resultaten voor elke.

De beschikbare waarden zijn:

* **Niet geprobeerd** : de update is niet geïnstalleerd omdat er onvoldoende tijd beschikbaar was op basis van de gedefinieerde duur van het onderhouds venster.
* **Niet geselecteerd** : de update is niet geselecteerd voor implementatie.
* **Geslaagd** : de update is voltooid.
* **Mislukt** : de update is mislukt.

Selecteer **Alle logboeken** als u alle logboekvermeldingen wilt zien die tijdens de implementatie zijn gemaakt.

Selecteer **uitvoer** om de taak stroom te bekijken van het runbook dat verantwoordelijk is voor het beheren van de update-implementatie op de doel-vm's.

Selecteer **Fouten** voor gedetailleerde informatie over fouten die zijn opgetreden tijdens de implementatie.

## <a name="next-steps"></a>Volgende stappen

Zie [waarschuwingen maken voor updatebeheer](update-mgmt-configure-alerts.md)voor meer informatie over het maken van waarschuwingen om u te waarschuwen over update-implementatie resultaten.