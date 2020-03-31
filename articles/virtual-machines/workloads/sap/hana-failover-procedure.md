---
title: HANA-failoverprocedure naar een rampsite voor SAP HANA op Azure (grote exemplaren) | Microsoft Documenten
description: Failover uitvoeren naar een noodherstelsite voor SAP HANA op Azure (Grote exemplaren)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3fe3ee79318ab9fdc9f2c0e9585051439b76b5cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617143"
---
# <a name="disaster-recovery-failover-procedure"></a>Procedure bij failover van het noodherstel


>[!IMPORTANT]
>Dit artikel is geen vervanging voor de SAP HANA-beheerdocumentatie of SAP Notes. Wij verwachten dat u een goed begrip en expertise hebt in SAP HANA administratie en operaties, met name voor back-up, herstel, hoge beschikbaarheid en disaster recovery (DR). In dit artikel worden screenshots van SAP HANA Studio getoond. Inhoud, structuur en de aard van de schermen van SAP-beheertools en de tools zelf kunnen veranderen van SAP HANA-release naar release.

Er zijn twee gevallen om rekening mee te houden wanneer u niet naar een DR-site gaat:

- U hebt de SAP HANA-database nodig om terug te gaan naar de nieuwste status van gegevens. In dit geval is er een selfservicescript waarmee u de failover uitvoeren zonder dat u contact hoeft op te nemen met Microsoft. Voor de failback moet u met Microsoft werken.
- U wilt deze herstellen naar een opslagmomentopname die niet de nieuwste gerepliceerde momentopname is. In dit geval moet u met Microsoft werken. 

>[!NOTE]
>De volgende stappen moeten worden uitgevoerd op de HANA Large Instance-eenheid, die de DR-eenheid vertegenwoordigt. 
 
Volg de stappen in 'Volledige DR-failover - azure_hana_dr_failover' uitvoeren in [Microsoft-snapshottools voor SAP HANA op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf)om te herstellen naar de nieuwste momentopnamen voor gerepliceerde opslag. 

Als u meerdere SAP HANA-exemplaren wilt hebben die zijn mislukt, voert u de opdracht azure_hana_dr_failover meerdere keren uit. Voer desgevraagd de SAP HANA SID in die u wilt mislukken en herstelt. 


U de DR-failover ook testen zonder dat dit gevolgen heeft voor de werkelijke replicatierelatie. Als u een testfailover wilt uitvoeren, voert u de stappen uit in 'Een test DR-failover uitvoeren - azure_hana_test_dr_failover' in [Microsoft-momentopnamehulpprogramma's voor SAP HANA op Azure.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf) 

>[!IMPORTANT]
>Voer *geen* productietransacties uit op de instantie die u in de DR-site hebt gemaakt tijdens het testen van **een failover.** De opdracht azure_hana_test_dr_failover maakt een reeks volumes die geen relatie hebben met de primaire site. Hierdoor is synchronisatie terug naar de primaire site *niet* mogelijk. 

Als u meerdere SAP HANA-exemplaren wilt testen, voert u het script meerdere keren uit. Voer desgevraagd de SAP HANA SID in van de instantie die u wilt testen op failover. 

>[!NOTE]
>Als u niet naar de DR-site moet gaan om bepaalde gegevens te redden die uren geleden zijn verwijderd en de DR-volumes moeten worden ingesteld op een eerdere momentopname, is deze procedure van toepassing. 

1. Schakel het niet-productieexemplaar van HANA uit op de noodhersteleenheid van HANA Large Instances die u uitvoert. Een slapende HANA-productie-instantie is vooraf geïnstalleerd.
1. Zorg ervoor dat er geen SAP HANA-processen worden uitgevoerd. Gebruik de volgende opdracht voor deze controle:

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      De uitvoer moet u het **hdbdaemon-proces** in een gestopte toestand laten zien en geen andere HANA-processen in een lopende of gestarte status.
1. Bepaal welke momentopnamenaam of SAP HANA-back-up-id u wilt hebben om de noodherstelsite te herstellen. In echte gevallen van noodherstel is deze momentopname meestal de nieuwste momentopname. Als u verloren gegevens wilt herstellen, kiest u een eerdere momentopname.
1. Neem contact op met Azure Support via een ondersteuningsaanvraag met hoge prioriteit. Vraag om het herstellen van die momentopname met de naam en datum van de momentopname of de HANA-back-up-id op de DR-site. De standaardinstelling is dat de bewerkingszijde alleen het /hana/gegevensvolume herstelt. Als u wilt dat de / hana / logbackups volumes ook hebben, moet u specifiek staat dat. *Herstel het /hana/gedeelde volume niet.* Kies in plaats daarvan specifieke bestanden zoals global.ini uit de **.snapshot-map** en de submappen ervan nadat u het /hana/gedeelde volume opnieuw hebt gemonteerd voor PRD. 

   Aan de bewerkingenzijde vinden de volgende stappen plaats:

   a. De replicatie van momentopnamen van het productievolume naar de noodherstelvolumes wordt gestopt. Deze storing kan al zijn gebeurd als een storing op de productielocatie de reden is dat u de noodherstelprocedure moet uitvoeren.
   
   b. De naam van de opslagmomentopname of momentopname met de door u gekozen back-up-id wordt hersteld op de noodherstelvolumes.
   
   c. Na het herstel zijn de noodherstelvolumes beschikbaar om te worden gemonteerd op de HANA Large Instance-eenheden in het gebied van noodherstel.
      
1. Monteer de noodherstelvolumes naar de HANA Large Instance-eenheid op de rampherstellocatie. 
1. Start de slapende SAP HANA-productieinstantie.
1. Als u ervoor kiest om back-uplogboeken voor transactielogboeken te kopiëren om de RPO-tijd te verkorten, voegt u de back-ups van het transactielogboek samen in de nieuw gemonteerde DR/hana/logback-ups. Overschrijf bestaande back-ups niet. Kopieer nieuwere back-ups die niet zijn gerepliceerd met de nieuwste replicatie van een opslagmomentopname.
1. U ook afzonderlijke bestanden herstellen uit de momentopnamen die niet zijn gerepliceerd naar het /hana/shared/PRD-volume in de DR Azure-regio.

In de volgende stappen wordt uitgelegd hoe u de SAP HANA-productie-instantie herstellen op basis van de momentopname van de herstelde opslag en de beschikbare back-ups van het transactielogboek.

1. Wijzig de back-uplocatie in **/hana/logbackups** met SAP HANA Studio.

   ![De back-uplocatie voor DR-herstel wijzigen](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA scant de back-upbestandslocaties en stelt de meest recente back-up van het transactielogboek voor om te herstellen. De scan kan enkele minuten duren voordat een scherm als het volgende wordt weergegeven:

   ![Lijst met back-ups van transactielogboeken voor DR-herstel](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Enkele standaardinstellingen aanpassen:

      - **Deltaback-ups wissen.**
      - Selecteer **Loggebied initialiseren**.

   ![Het loggebied initialiseren instellen](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Selecteer **Finish**.

   ![Het DR-herstel voltooien](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Er moet een voortgangsvenster verschijnen, zoals hier wordt weergegeven. Houd er rekening mee dat het voorbeeld is van een herstel herstel na noodgevallen van een sap HANA-configuratie met drie nodes.

![Voortgang herstellen](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Als het herstel niet meer reageert op het scherm **Voltooien** en het voortgangsscherm niet wordt weergegeven, bevestigt u of alle SAP HANA-exemplaren op de werknemersknooppunten worden uitgevoerd. Start indien nodig de SAP HANA-exemplaren handmatig.


## <a name="failback-from-a-dr-to-a-production-site"></a>Failback van een DR naar een productielocatie
U niet terug van een DR naar een productiesite. Laten we eens kijken naar een scenario waarin de failover naar de rampherstelsite werd veroorzaakt door problemen in de productie-Azure-regio en niet door uw behoefte om verloren gegevens te herstellen. 

U voert uw SAP-productieworkload al een tijdje uit op de noodherstelsite. Als de problemen in de productiesite zijn opgelost, wilt u niet terug naar uw productiesite. Omdat u geen gegevens verliezen, omvat de stap terug naar de productiesite verschillende stappen en nauwe samenwerking met het SAP HANA-team voor Azure-operationsteam. Het is aan u om het operationele team te activeren om te beginnen met het synchroniseren terug naar de productiesite nadat de problemen zijn opgelost.

Volg deze stappen:

1. Het SAP HANA-operationele team voor Azure-bewerkingen krijgt de trigger om de productieopslagvolumes te synchroniseren vanaf de opslagvolumes voor noodherstel, die nu de productiestatus vertegenwoordigen. In deze toestand wordt de HANA Large Instance-eenheid in de productielocatie afgesloten.
1. Het SAP HANA op Azure operations-team controleert de replicatie en zorgt ervoor dat deze wordt ingehaald voordat ze u hiervan op de hoogte stellen.
1. U sluit de toepassingen af die de productie HANA Instance gebruiken in de ramprecoverysite. U voert vervolgens een BACK-up van het HANA-transactielogboek uit. Vervolgens stopt u het HANA-exemplaar dat wordt uitgevoerd op de HANA Large Instance-eenheden in de rampherstelsite.
1. Nadat het HANA-exemplaar dat wordt uitgevoerd in de HANA Large Instance-eenheid op de rampherstelsite is uitgeschakeld, synchroniseert het operationele team de schijfvolumes handmatig opnieuw.
1. Het SAP HANA-team voor Azure-bewerkingen start de HANA Large Instance-eenheid opnieuw in de productiesite. Ze geven het aan jou. U zorgt ervoor dat de SAP HANA-instantie zich in een afsluitstatus bevindt op het opstartmoment van de EENHEID HANA Large Instance.
1. U voert dezelfde stappen voor het herstellen van de database uit als toen u eerder niet naar de herstelsite voor noodgevallen ging.

## <a name="monitor-disaster-recovery-replication"></a>Herstelreplicatie na noodgevallen controleren

Voer het script `azure_hana_replication_status`uit om de status van de voortgang van de opslagreplicatie te controleren. Deze opdracht moet worden uitgevoerd vanaf een eenheid die wordt uitgevoerd in de locatie voor noodherstel om te functioneren zoals verwacht. De opdracht werkt, ongeacht of replicatie actief is. De opdracht kan worden uitgevoerd voor elke HANA Large Instance-eenheid van uw tenant op de locatie voor noodherstel. Het kan niet worden gebruikt om details over het opstartvolume te verkrijgen. 

Zie 'Dr-replicatiestatus - azure_hana_replication_status' in [Microsoft-snapshottools voor SAP HANA op Azure voor](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf)meer informatie over de opdracht en de uitvoer.


## <a name="next-steps"></a>Volgende stappen
- Zie [Monitor en problemen van HANA-kant](hana-monitor-troubleshoot.md).
