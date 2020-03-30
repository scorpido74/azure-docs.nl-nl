---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: tanmaygore
ms.service: virtual-machines
ms.topic: include
ms.date: 02/06/2020
ms.author: tagore
ms.custom: include file
ms.openlocfilehash: 57469bef7014010164234638f3d059ac96b125cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78383909"
---
## <a name="what-is-the-time-required-for-migration"></a>Wat is de tijd die nodig is voor migratie?

De planning en uitvoering van migratie hangt sterk af van de complexiteit van de architectuur en kan enkele maanden duren.  

## <a name="what-is-the-definition-of-a-new-customer-on-iaas-vms-classic"></a>Wat is de definitie van een nieuwe klant op IaaS VM's (klassiek)?

Klanten die in de maand februari 2020 (een maand voor de afschaffing) geen IaaS VM's (klassiek) in hun abonnement hadden, worden als nieuwe klanten beschouwd. 

## <a name="what-is-the-definition-of-an-existing-customer-on-iaas-virtual-machines-classic"></a>Wat is de definitie van een bestaande klant op IaaS Virtual Machines (klassiek)?

Klanten die in de maand februari 2020 IaaS VM's (Classic) actief of gestopt hadden, maar deze in de maand februari 2020 in hun abonnementen hadden toegewezen, worden als bestaande klant beschouwd. Alleen deze klanten krijgen tot 1 maart 2023 de tijd om hun VM's te migreren van Azure Service Manager naar Azure Resource Manager. 

## <a name="why-am-i-getting-an-error-stating-newclassicvmcreationnotallowedforsubscription"></a>Waarom krijg ik een foutmelding met de vermelding "NewClassicVMCreationNotAllowedForSubscription"?

Als onderdeel van het pensioenproces is IaaS VM (klassiek) niet langer beschikbaar voor nieuwe klanten. Wij hebben u geïdentificeerd als nieuwe klanten en daarom was uw operatie niet geautoriseerd. We raden ten zeerste aan azure virtual machines te gebruiken [met ARM.](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell) Als u Azure VM's niet gebruiken met ARM, neemt u contact op met ondersteuning voor whitelisting van abonnementen.

## <a name="does-this-migration-plan-affect-any-of-my-existing-services-or-applications-that-run-on-azure-virtual-machines"></a>Is dit migratieplan van invloed op mijn bestaande services en toepassingen die worden uitgevoerd op virtuele Azure-machines? 

Pas op 1 maart 2023 voor IaaS VM's (klassiek). De IaaS VM's (klassiek) zijn volledig ondersteunde diensten in algemene beschikbaarheid. U kunt deze resources blijven gebruiken om uw footprint in Microsoft Azure te vergroten. Op 1 maart 2023 worden deze VM's volledig buiten gebruik gesteld en worden alle actieve of toegewezen VM's stopgezet & dealtoegewezen. Andere klassieke bronnen zoals Cloud Services (Classic), Storage Accounts (Classic), etc. hebben geen invloed op andere klassieke bronnen.   

## <a name="what-happens-to-my-vms-if-i-dont-plan-on-migrating-in-the-near-future"></a>Wat gebeurt er met mijn virtuele machines als ik niet van plan ben om in de nabije toekomst te migreren? 

Op 1 maart 2023 worden de IaaS VM's (Classic) volledig buiten gebruik gesteld en worden alle actieve of toegewezen VM's & deal-toegewezen. Om bedrijfsimpact te voorkomen, geven we het goed aan om vandaag te beginnen met het plannen van uw migratie en deze voor 1 maart 2023 te voltooien. We zijn niet deprecating de bestaande klassieke API's, Cloud Services en resource model. Het is de bedoeling om migreren eenvoudig te maken omdat er veel geavanceerde functies beschikbaar zijn in het Resource Manager-implementatiemodel. We raden u aan deze resources te migreren naar Azure Resource Manager. 

## <a name="what-does-this-migration-plan-mean-for-my-existing-tooling"></a>Wat betekent dit migratieplan voor mijn bestaande tooling? 

Het bijwerken van uw tooling voor het Resource Manager-implementatiemodel is een van de belangrijkste veranderingen die moet worden doorgevoerd voor uw migratieplannen.

## <a name="how-long-will-the-management-plane-downtime-be"></a>Hoe lang duurt de downtime voor het management? 

Dit is afhankelijk van het aantal resources dat wordt gemigreerd. Bij kleinere implementaties (enkele tientallen virtuele machines) zou de volledige migratie minder dan een uur moeten duren. Bij grootschalige implementaties (honderden virtuele machines) kan de migratie enkele uren duren.

## <a name="can-i-roll-back-after-my-migrating-resources-are-committed-in-resource-manager"></a>Kan ik het migreren van mijn resources ongedaan maken nadat de resources zijn doorgevoerd in Resource Manager? 

U kunt de migratie afbreken wanneer de resources zich nog in de staat Voorbereid bevinden. U kunt het migreren niet meer ongedaan maken wanneer de resources eenmaal zijn doorgevoerd.

## <a name="can-i-roll-back-my-migration-if-the-commit-operation-fails"></a>Kan ik de migratie ongedaan maken wanneer de doorvoerbewerking is mislukt? 

U kunt de migratie niet afbreken als wanneer de doorvoerbewerking is mislukt. Alle migratiebewerkingen, met inbegrip van de doorvoerbewerking, zijn idempotent. Daarom is het raadzaam om de bewerking na een korte tijd opnieuw uit te voeren. Als u nog steeds met een foutmelding te maken hebt, maakt u een ondersteuningsticket.

## <a name="do-i-have-to-buy-another-express-route-circuit-if-i-have-to-use-iaas-under-resource-manager"></a>Moet ik nog een ExpressRoute-circuit kopen als ik IaaS via Resource Manager wil gebruiken? 

Nee. Recent is het [verplaatsen van ExpressRoute-circuits van het klassieke naar het Resource Manager-implementatiemodel](../articles/expressroute/expressroute-move.md) ingeschakeld. U hoeft geen nieuw ExpressRoute-circuit te kopen als u er al een hebt.

## <a name="what-if-i-had-configured-role-based-access-control-policies-for-my-classic-iaas-resources"></a>Wat gebeurt er als ik op rollen gebaseerd toegangsbeheerbeleid heb geconfigureerd voor mijn klassieke IaaS-resources? 

Tijdens de migratie worden de klassieke resources Resource Manager-resources. Daarom raden we u aan de RBAC-beleidsupdates te plannen die na de migratie moeten plaatsvinden.

## <a name="i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>Ik heb mijn klassieke VM's in een kluis. Kan ik mijn virtuele machines migreren van de klassieke modus naar de Resource Manager-modus en ze beschermen in een Recovery Services-kluis?

Wanneer u een VM verplaatst van de klassieke modus naar resourcebeheer, worden back-ups die vóór de migratie zijn gemaakt, niet gemigreerd naar de nieuw gemigreerde VM resourcebeheer. Als u echter uw back-ups van klassieke VM's wilt behouden, volgt u deze stappen vóór de migratie. 

1. Ga in de kluis Herstelservices naar het tabblad **Beveiligde items** en selecteer de VM. 
2. Klik op Beveiliging stoppen. Laat de optie *Gekoppelde back-upgegevens verwijderen***uitgeschakeld**.

> [!NOTE]
> Er worden kosten in rekening gebracht voor back-ups totdat u gegevens bewaart. Back-upkopieën worden gesnoeid per bewaarbereik. De laatste back-upkopie wordt echter altijd bewaard totdat u back-upgegevens expliciet verwijdert. Het wordt aangeraden om uw bewaarbereik van de virtuele machine te controleren en "Back-upgegevens verwijderen" op het beveiligde item in de kluis te activeren zodra het bewaarbereik voorbij is. 
>
>

Als u de virtuele machine wilt migreren naar de modus Resourcebeheer, 

1. Verwijder de back-up-/momentopname-extensie uit de VM.
2. Migreer de virtuele machines van de klassieke modus naar de Resource Manager-modus. Zorg ervoor dat de opslagruimte en de netwerkgegevens die corresponderen met de virtuele machine, ook naar de Resource Manager-modus worden gemigreerd.

Als u een back-up wilt maken van de gemigreerde VM, gaat u bovendien naar het beheerblad van Virtual Machine om [back-ups in](../articles/backup/quick-backup-vm-portal.md#enable-backup-on-a-vm)te schakelen.

## <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-migration"></a>Kan ik mijn abonnement of resources valideren om te ontdekken of ze geschikt zijn voor migratie? 

Ja. Bij de door het platform ondersteunde migratieoptie is de eerste stap van het voorbereiden op de migratie het controleren of de resources geschikt zijn voor migratie. Als de validatiebewerking mislukt, ontvangt u berichten over alle redenen waarom de migratie niet kan worden voltooid.

## <a name="what-happens-if-i-run-into-a-quota-error-while-preparing-the-iaas-resources-for-migration"></a>Wat gebeurt er als er een quotumfout optreedt bij het voorbereiden van de IaaS-resources op migratie? 

Het wordt aanbevolen om de migratie af te breken en daarna een ondersteuningsaanvraag in te dienen voor het verhogen van de quota in de regio waarin u de VM's wilt migreren. Wanneer het quotaverzoek is goedgekeurd, kunt u de migratiestappen opnieuw uitvoeren.

## <a name="how-do-i-report-an-issue"></a>Hoe meld ik een probleem? 

Plaats berichten over uw problemen met migratie en vragen over migratie op ons [VM-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows), met het trefwoord ClassicIaaSMigration. Het wordt aanbevolen om al uw vragen op dit forum te plaatsen. Als u een ondersteuningscontract hebt, kunt u ook een ondersteuningsticket aanmaken.

## <a name="what-if-i-dont-like-the-names-of-the-resources-that-the-platform-chose-during-migration"></a>Wat kan ik doen als ik de resourcenamen niet leuk vind die het platform heeft gekozen tijdens de migratie? 

Alle resources waarvoor u expliciet namen opgeeft in het klassieke implementatiemodel, behouden die naam tijdens de migratie. In sommige gevallen worden er nieuwe resources gemaakt. Er wordt dan bijvoorbeeld een netwerkinterface gemaakt voor elke VM. Momenteel wordt de mogelijkheid niet ondersteund om invloed uit te oefenen op de namen van de nieuwe resources die tijdens de migratie worden gemaakt. Geef het via het [Azure-feedbackforum](https://feedback.azure.com) aan als u deze mogelijkheid graag zou willen laten toevoegen.

## <a name="can-i-migrate-expressroute-circuits-used-across-subscriptions-with-authorization-links"></a>Kan ik ExpressRoute-circuits migreren die voor verschillende abonnementen met autorisatielinks worden gebruikt? 

ExpressRoute-circuits met abonnementsoverstijgende autorisatielinks kunnen niet automatisch worden gemigreerd zonder downtime. Er is informatie beschikbaar over het uitvoeren van handmatige migratie. Zie [ExpressRoute-circuits en de bijbehorende virtuele netwerken van het klassieke naar het Resource Manager-implementatiemodel migreren](../articles/expressroute/expressroute-migration-classic-resource-manager.md) voor stappen en meer informatie.

## <a name="i-got-the-message-vm-is-reporting-the-overall-agent-status-as-not-ready-hence-the-vm-cannot-be-migrated-ensure-that-the-vm-agent-is-reporting-overall-agent-status-as-ready-or-vm-contains-extension-whose-status-is-not-being-reported-from-the-vm-hence-this-vm-cannot-be-migrated"></a>Ik kreeg het bericht *"VM is de rapportage van de algemene agent status als Not Ready. Daarom kan de VM niet worden gemigreerd. Controleer of de VM-agent de algemene agentstatus als Gereed rapporteert"* of *'VM bevat extensie waarvan de status niet wordt gerapporteerd vanuit de VM. Vandaar dat deze VM niet kan worden gemigreerd."*

Dit bericht wordt weergegeven wanneer de VM geen uitgaande verbinding heeft met internet. De VM-agent maakt gebruik van een uitgaande verbinding om het Azure-opslagaccount te bereiken. Zo kan de agentstatus elke vijf minuten worden bijgewerkt.
