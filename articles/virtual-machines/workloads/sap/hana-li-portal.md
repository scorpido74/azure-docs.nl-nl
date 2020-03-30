---
title: Azure HANA-beheer voor grote instanties via Azure-portal | Microsoft Documenten
description: Beschrijft hoe u Azure HANA Large Instances identificeren en ermee communiceren via portal
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c14ff9c4f6d2bc2b1a62d1874d01950d09491c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70099818"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Beheer van grote Azure HANA-exemplaren via de Azure-portal
Dit document gaat over de manier waarop [HANA Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) worden gepresenteerd in [Azure portal](https://portal.azure.com) en welke activiteiten kunnen worden uitgevoerd via Azure portal met HANA Large Instance units die voor u worden geïmplementeerd. Zichtbaarheid van HANA Large Instances in Azure-portal wordt geleverd via een Azure-bronprovider voor HANA Large Instances, die momenteel in openbare preview is

## <a name="register-hana-large-instance-resource-provider"></a>HANA-bronprovider voor grote instanties registreren
Meestal wordt uw Azure-abonnement dat u voor HANA Large Instance-implementaties gebruikte, geregistreerd voor de HANA Large Instance Resource Provider. Als u echter niet zien dat u HANA-eenheden voor grote instanties hebt geïmplementeerd, moet u de ResourceProvider registreren in uw Azure-abonnement. Er zijn twee manieren om de HANA Large Instance Resource provider te registreren

### <a name="register-through-cli-interface"></a>Registreren via cli-interface
U moet zijn aangemeld bij uw Azure-abonnement, dat wordt gebruikt voor de HANA Large Instance-implementatie via de Azure CLI-interface. U de HANA Large Instance Provider (opnieuw) registreren met deze opdracht:
    
    az provider register --namespace Microsoft.HanaOnAzure

Zie het artikel [Azure-bronproviders en -typen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-cli) voor meer informatie


### <a name="register-through-azure-portal"></a>Registreren via Azure-portal
U de HANA Large Instance Resource Provider (opnieuw)registreren via Azure portal. U moet uw abonnement aanbieden in azure portal en dubbelklikken op het abonnement, dat is gebruikt om uw HANA Large Instance-eenheid(s) te implementeren. U bevindt zich op de overzichtspagina van uw abonnement, selecteert u 'Resourceproviders' zoals hieronder wordt weergegeven en typt "HANA" in het zoekvenster. 

![HLI RP registreren via Azure-portal](./media/hana-li-portal/portal-register-hli-rp.png)

In de getoonde schermafbeelding was de resourceprovider al geregistreerd. Als de resourceprovider nog niet is geregistreerd, drukt u op "opnieuw registreren" of "registreren".

Zie het artikel [Azure-bronproviders en -typen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-powershell) voor meer informatie


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Weergave van HANA-eenheden voor grote instanties in de Azure-portal
Bij het indienen van een HANA Large Instance deployment request wordt u gevraagd om het Azure-abonnement op te geven dat u ook met de HANA Large Instances maakt. Het wordt aanbevolen om hetzelfde abonnement te gebruiken dat u gebruikt om de SAP-toepassingslaag te implementeren die werkt tegen de HANA Large Instance-eenheden.
Terwijl uw eerste HANA-grote instanties worden geïmplementeerd, wordt een nieuwe [Azure-brongroep](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) gemaakt in het Azure-abonnement dat u hebt ingediend in de implementatieaanvraag voor uw HANA-grote instantie(s).  De nieuwe resourcegroep geeft een lijst van al uw HANA-eenheden voor grote instanties die u in het specifieke abonnement hebt geïmplementeerd.

Als u de nieuwe Azure-brongroep wilt vinden, geeft u de brongroep in uw abonnement aan door door het linkernavigatiedeelvenster van de Azure-portal te navigeren

![Navigatiedeelvenster in Azure-portal](./media/hana-li-portal/portal-resource-group.png)

In de lijst met brongroepen wordt u mogelijk vermeld, u moet mogelijk filteren op het abonnement dat u hebt gebruikt om HANA Large Instances te laten implementeren

![Brongroepen filteren in Azure-portal](./media/hana-li-portal/portal-filtering-subscription.png)

Nadat u hebt gefilterd op het juiste abonnement, u nog steeds een lange lijst met brongroepen hebben. Kijk voor een met een post-fix van **-Txxx** waar "xxx" zijn drie cijfers, zoals **-T050**. 

Terwijl u de brongroep hebt gevonden, geeft u de details ervan aan. De lijst die u hebt ontvangen, kan er uitzien als:

![HLI-lijst in Azure-portal](./media/hana-li-portal/portal-hli-units-list.png)

Alle eenheden die worden vermeld, vertegenwoordigen één HANA-eenheid voor grote instanties die in uw abonnement zijn geïmplementeerd. In dit geval kijkt u naar acht verschillende HANA Large Instance-eenheden, die in uw abonnement zijn geïmplementeerd.

Als u meerdere HANA Large Instance-tenants onder hetzelfde Azure-abonnement hebt geïmplementeerd, vindt u meerdere Azure-brongroepen 


## <a name="look-at-attributes-of-single-hli-unit"></a>Kijk naar kenmerken van één HLI-eenheid
In de lijst van de HANA Large Instance-eenheden u op één eenheid klikken en de details van de eenheid HANA Large Instance bekijken. 

In het overzichtsscherm krijgt u na het klikken op 'Meer weergeven' een presentatie van het apparaat, dat eruit ziet als:

![Overzicht van een HLI-eenheid weergeven](./media/hana-li-portal/portal-show-overview.png)

Als we kijken naar de verschillende getoonde kenmerken, zien deze kenmerken er nauwelijks anders uit dan Azure VM-kenmerken. In de koptekst aan de linkerkant worden de resourcegroep, de Azure-regio, de naam van het abonnement en de id weergegeven, evenals enkele tags die u hebt toegevoegd. Standaard hebben de HANA Large Instance-eenheden geen tag toegewezen. Aan de rechterkant van de koptekst wordt de naam van de eenheid vermeld als toegewezen wanneer de implementatie werd uitgevoerd. Het besturingssysteem wordt weergegeven, evenals het IP-adres. Net als bij VM's wordt ook het TYPE HANA Large instance unit met het aantal CPU-threads en geheugen weergegeven. Meer details over de verschillende HANA Large Instance units, worden hier getoond:

- [Beschikbare SKU's voor HLI](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [SAP HANA (Large Instances) opslagarchitectuur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

De extra gegevens op de juiste lagere kant zijn de revisie van de stempel van hana grote instantie. Mogelijke waarden zijn:

- Revisie 3
- Revisie 4

Revisie 4 is de nieuwste architectuur die is uitgebracht van HANA Large Instances met belangrijke verbeteringen in netwerklatentie tussen Azure VM's en HANA Large instance units die zijn geïmplementeerd in Revision 4 stempels of rijen.
Een andere zeer belangrijke informatie is te vinden in de rechterbenedenhoek van het overzicht met de naam van de Azure Proximity Placement Group die automatisch wordt gemaakt voor elke geïmplementeerde HANA Large Instance-eenheid. Er moet naar deze plaatsingsgroep voor nabijheid worden verwezen wanneer u de Azure VM's implementeert die de SAP-toepassingslaag hosten. Met behulp van de [azure proximity placement-groep](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) die is gekoppeld aan de HANA Large Instance-eenheid, zorgt u ervoor dat de Azure VM's in de nabijheid van de eenheid HANA Large Instance worden geïmplementeerd. De manier waarop nabijheidsplaatsingsgroepen kunnen worden gebruikt om de SAP-toepassingslaag in hetzelfde Azure-datacenter te vinden als de gehoste HANA-eenheden voor grote instantie van Revisie 4, wordt beschreven in [Azure Proximity Placement Groups voor optimale netwerklatentie met SAP-toepassingen.](sap-proximity-placement-scenarios.md)

Een extra veld in de rechterkolom van de koptekst informeert over de energiestatus van de EENHEID GROTE HANA.

> [!NOTE]
> De energiestatus beschrijft of de hardware-eenheid is ingeschakeld of uitgeschakeld. Het geeft geen informatie over het besturingssysteem wordt up and running. Wanneer u een HANA-eenheid voor grote instanties opnieuw start, zult u een kleine tijd ervaren waarin de status van de eenheid verandert in **Beginnen** om te gaan naar de status van **Gestart.** In de staat van **Gestart** betekent dat het OS wordt opgestart of dat het BE volledig is opgestart. Als gevolg hiervan u na een herstart van het apparaat niet verwachten dat u onmiddellijk bij het apparaat inlogt zodra de status overschakelt naar **Gestart.**
> 

Als u op 'Meer zien' drukt, wordt aanvullende informatie weergegeven. Een extra informatie is het weergeven van de herziening van de HANA Large Instance stempel, de eenheid werd ingezet in. Zie het artikel [Wat is SAP HANA op Azure (Large Instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) voor de verschillende revisies van HANA Large Instance-stempels

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>Activiteiten van één HANA Large Instance-eenheid controleren 
Naast het geven van een overzicht van de HANA Large Instance units, u de activiteiten van de betreffende eenheid controleren. Een activiteitenlogboek kan er uitzien als:

![Navigatiedeelvenster in Azure-portal](./media/hana-li-portal/portal-activity-list.png)

Een van de belangrijkste geregistreerde activiteiten zijn het opnieuw opstarten van een eenheid. De vermelde gegevens omvatten de status van de activiteit, de tijdstempel waarop de activiteit is geactiveerd, de abonnements-id waaruit de activiteit is geactiveerd en de Azure-gebruiker die de activiteit heeft geactiveerd. 

Een andere activiteit die wordt geregistreerd, zijn wijzigingen in de eenheid in de Azure-metagegevens. Naast de gestarte herstart, u de activiteit van **Write HANAInstances**zien. Dit type activiteit voert geen wijzigingen uit op de HANA Large Instance-eenheid zelf, maar documenteert wijzigingen in de metagegevens van de eenheid in Azure. In het geval dat wordt vermeld, hebben we een tag toegevoegd en verwijderd (zie volgende sectie).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>Een Azure-tag toevoegen en verwijderen aan een HANA-eenheid voor grote instanties
Een andere mogelijkheid die je hebt is om een [tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) toe te voegen aan een HANA Large Instance-eenheid. De manier waarop tags worden toegewezen verschilt niet van het toewijzen van tags aan VM's. Net als bij VM's bestaan de tags in Azure metagegevens en hebben ze voor HANA Large Instances dezelfde beperkingen als tags voor VM's.

Tags verwijderen werkt op dezelfde manier als bij VM's. Beide activiteiten, het toepassen en verwijderen van een tag worden vermeld in het activiteitenlogboek van de specifieke HANA Large Instance-eenheid.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>Eigenschappen van een HANA-eenheid voor grote instanties controleren
De sectie **Eigenschappen** bevat belangrijke informatie die u krijgt wanneer de instanties aan u worden overhandigd. Het is een sectie waar u alle informatie krijgt die u nodig zou kunnen hebben in ondersteuningsaanvragen of die u nodig hebt bij het instellen van opslagmomentopnameconfiguratie. Als zodanig is deze sectie een verzameling gegevens rond uw instantie, de connectiviteit van de instantie met Azure en de backend voor opslag. De bovenkant van de sectie ziet eruit als volgt:


![bovenste deel van HLI-eigenschappen in Azure-portal](./media/hana-li-portal/portal-properties-top.png)

De eerste paar gegevensitems zag je al in het overzichtsscherm. Maar een belangrijk deel van de gegevens is de ExpressRoute Circuit ID, die je kreeg als de eerste ingezeteenheden werden overhandigd. In sommige ondersteuningsgevallen wordt u mogelijk om die gegevens gevraagd. Een belangrijke gegevensinvoer wordt getoond onder aan screenshot. De weergegeven gegevens zijn het IP-adres van de NFS-opslagkop die uw opslag isoleert naar uw **tenant** in de HANA Large Instance-stack. Dit IP-adres is ook nodig wanneer u het [configuratiebestand bewerkt voor back-ups van opslagmomentopnamen.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots) 

Terwijl u naar beneden scrolt in het eigenschappenvenster, krijgt u aanvullende gegevens, zoals een unieke bron-id voor uw HANA-eenheid voor grote instanties of de abonnements-ID die aan de implementatie is toegewezen.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Een HANA-eenheid voor grote instanties opnieuw starten via Azure-portal
Het starten van een herstart van het Linux-besturingssysteem, waren er verschillende situaties waarin het OS niet kon een herstart met succes af. Als u een herstart wilt forceren, moet u een serviceaanvraag openen om Microsoft-bewerkingen een herstart van de hana-eenheid voor grote instanties te laten uitvoeren. De functionaliteit van een power restart van een HANA Large Instance-eenheid is nu geïntegreerd in de Azure-portal. Als u zich in het overzichtsgedeelte van de eenheid HANA Large Instance bevindt, ziet u de knop voor opnieuw opstarten boven op de gegevenssectie

![Stap #1 opnieuw starten in Azure-portal](./media/hana-li-portal/portal-restart-first-step.png)

Terwijl u op de herstartknop drukt, wordt u gevraagd of u het apparaat echt opnieuw wilt starten. Zoals u bevestigt door op de knop "Ja" te drukken, wordt het apparaat opnieuw opgestart.

> [!NOTE]
> In het herstartproces ervaart u een kleine tijd waarin de status van de eenheid verandert in **Starten** om te gaan naar de status van **Gestart.** In de staat van **Gestart** betekent dat het OS wordt opgestart of dat het BE volledig is opgestart. Als gevolg hiervan u na een herstart van het apparaat niet verwachten dat u onmiddellijk bij het apparaat inlogt zodra de status overschakelt naar **Gestart.**

> [!IMPORTANT]
> Afhankelijk van de hoeveelheid geheugen in uw HANA Large Instance-eenheid kan het opstarten en opnieuw opstarten van de hardware en het besturingssysteem tot een uur duren


## <a name="open-a-support-request-for-hana-large-instances"></a>Een ondersteuningsaanvraag openen voor HANA grote exemplaren
Buiten de Azure-portalweergave van HANA Large Instance-eenheden u ook ondersteuningsaanvragen maken die specifiek voor een HANA-eenheid voor grote instance-eenheden zijn. Als u de link volgt **Nieuwe ondersteuningsaanvraag** 

![de stap van serviceaanvraag #1 in Azure-portal starten](./media/hana-li-portal/portal-initiate-support-request.png)

Om de service van SAP HANA Large Instances in het volgende scherm te krijgen, moet u mogelijk 'Alle services' selecteren, zoals hieronder wordt weergegeven

![Alle services selecteren in Azure-portal](./media/hana-li-portal/portal-create-service-request.png)

In de lijst met services vindt u de service **SAP HANA Large Instance.** Terwijl u die service kiest, u specifieke probleemtypen selecteren zoals weergegeven:


![Probleemklasse selecteren in Azure-portal](./media/hana-li-portal/portal-select-problem-class.png)

Onder elk van de verschillende probleemtypen krijgt u een selectie van probleemsubtypen die u moet selecteren om uw probleem verder te karakteriseren. Nadat u het subtype hebt geselecteerd, u het onderwerp nu een naam geven. Zodra u klaar bent met het selectieproces, u naar de volgende stap van de creatie gaan. In de sectie **Oplossingen** wordt u gewezen op documentatie rond HANA Large Instances, die een aanwijzer kunnen geven naar een oplossing van uw probleem. Als u geen oplossing voor uw probleem vinden in de voorgestelde documentatie, gaat u naar de volgende stap. In de volgende stap wordt u gevraagd of het probleem bij VM's of met HANA Large Instance-eenheden is. Deze informatie helpt om het ondersteuningsverzoek door te sturen naar de juiste specialisten. 

![Details van de ondersteuningsaanvraag in Azure-portal](./media/hana-li-portal/portal-support-request-details.png)

Terwijl u de vragen beantwoordde en aanvullende details hebt verstrekt, u de volgende stap zetten om het ondersteuningsverzoek en het indienen ervan te bekijken.

## <a name="next-steps"></a>Volgende stappen

- [SAP HANA (grote exemplaren) op Azure controleren](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [Bewaken en problemen oplossen vanaf de HANA-zijde](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)

