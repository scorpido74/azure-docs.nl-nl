---
title: Windows-beheercentrum integreren met Azure Security Center | Microsoft Documenten
description: In dit artikel wordt uitgelegd hoe u Azure Security Center integreren met windows-beheercentrum
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 5467794bf246fab4ff7ded9c445dbeee0c4093b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139620"
---
# <a name="integrate-azure-security-center-with-windows-admin-center"></a>Azure Security Center integreren met Windows-beheercentrum

Windows Admin Center is een beheertool voor uw Windows-servers. Het is een enkele locatie voor systeembeheerders om toegang te krijgen tot de meerderheid van de meest gebruikte beheertools. Vanuit Windows Admin Center u uw on-prem-servers rechtstreeks aan boord nemen in Azure Security Center. Vervolgens u een overzicht van uw beveiligingsaanbevelingen en waarschuwingen rechtstreeks bekijken in de Windows Admin Center-ervaring.

> [!NOTE]
> Uw Azure-abonnement en de bijbehorende Werkruimte Log Analytics moeten beide de standaardlaag van het Beveiligingscentrum hebben ingeschakeld om de integratie van het Windows-beheercentrum in te schakelen.
> De standaardlaag is de eerste 30 dagen gratis als u deze nog niet eerder hebt gebruikt op het abonnement en de werkruimte. Zie voor meer informatie [de pagina prijsinformatie](security-center-pricing.md).
>

Wanneer u een server hebt aangesloten van Windows Admin Center naar Azure Security Center, u het als:

* Beveiligingswaarschuwingen en aanbevelingen weergeven in de extensie Beveiligingscentrum in Windows-beheercentrum
* Bekijk de beveiligingshouding en haal aanvullende gedetailleerde informatie op van de beheerde servers van uw Windows Admin Center in beveiligingscentrum binnen de Azure-portal (of via een API)

Door deze twee hulpprogramma's te combineren, wordt Security Center uw enige ruit om al uw beveiligingsgegevens weer te geven, ongeacht de bron: het beschermen van uw Windows Admin Center beheerde on-prem-servers, uw VM's en eventuele extra PaaS-workloads.

## <a name="onboarding-windows-admin-center-managed-servers-into-security-center"></a>Beheerde servers van Windows Admin Center inhet beheerde beveiligingscentrum in- of instappen

1. Selecteer in het Windows-beheercentrum een van uw servers en selecteer in het deelvenster **Extra** de extensie Azure Security Center:

    ![Azure Security Center-extensie in Windows-beheercentrum](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > Als de server al is aangesloten bij Security Center, wordt het venster niet weergegeven.

1. Klik **op Aanmelden bij Azure en stel in**.
    ![Uitbreiding Windows-beheercentrum inwerken voor Azure Security Center](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. Volg de instructies om uw server aan te sluiten op Security Center. Nadat u de benodigde details hebt ingevoerd en bevestigd, voert Security Center de nodige configuratiewijzigingen aan om ervoor te zorgen dat alle volgende wijzigingen waar zijn:
    * Een Azure Gateway is geregistreerd.
    * De server heeft een werkruimte om aan te rapporteren en een bijbehorend abonnement.
    * De standaardloganalyseoplossing van het beveiligingscentrum is ingeschakeld op de werkruimte. Deze oplossing biedt de standaardlaagfuncties van Security Center voor *alle* servers en virtuele machines die naar deze werkruimte worden verzonden.
    * De standaardtariefprijzen voor Virtual Machine van Het Beveiligingscentrum zijn ingeschakeld voor het abonnement.
    * De Microsoft Monitoring Agent (MMA) is geïnstalleerd op de server en geconfigureerd om te rapporteren aan de geselecteerde werkruimte. Als de server al rapporteert aan een andere werkruimte, is deze geconfigureerd om ook te rapporteren aan de nieuw geselecteerde werkruimte.

    > [!NOTE]
    > Het kan enige tijd duren na het instappen voor aanbevelingen te verschijnen. Afhankelijk van uw serveractiviteit ontvangt u mogelijk *geen* waarschuwingen. Als u testwaarschuwingen wilt genereren om te testen dat uw waarschuwingen correct werken, volgt u de instructies in [de waarschuwingsvalidatieprocedure](security-center-alert-validation.md).


## <a name="viewing-security-recommendations-and-alerts-in-windows-admin-center"></a>Beveiligingsaanbevelingen en waarschuwingen weergeven in Windows-beheercentrum

Eenmaal aan boord u uw waarschuwingen en aanbevelingen rechtstreeks bekijken in het Azure Security Center-gebied van Windows Admin Center. Klik op een aanbeveling of een waarschuwing om deze weer te geven in de Azure-portal. Daar krijgt u aanvullende informatie en leert u hoe u problemen oplossen.

[![Aanbevelingen en waarschuwingen van het Beveiligingscentrum zoals te zien in Windows Admin Center](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="viewing-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>Beveiligingsaanbevelingen en waarschuwingen voor beheerde servers van het Windows-beheercentrum weergeven in beveiligingscentrum
Vanuit Azure Security Center:

* Als u beveiligingsaanbevelingen voor al uw Windows-beheercentrumservers wilt weergeven, opent u **Compute & Apps** en klikt u op het tabblad **VM's en computers.** Filter de lijst op bron 'Server' zoals hier wordt weergegeven:

    [![Beveiligingsaanbevelingen voor beheerde servers van het Windows-beheercentrum weergeven](media/windows-admin-center-integration/viewing-recommendations-wac.png)](media/windows-admin-center-integration/viewing-recommendations-wac.png#lightbox)

* Als u beveiligingswaarschuwingen voor al uw Windows Admin Center-servers wilt weergeven, opent u **Beveiligingswaarschuwingen.** Klik **op Filteren** en zorg ervoor dat **alleen** 'Niet-Azure' is geselecteerd:

    ![Beveiligingswaarschuwingen filteren voor beheerde servers van het Windows-beheercentrum](./media/windows-admin-center-integration/filtering-alerts-to-non-azure.png)

    [![Beveiligingswaarschuwingen voor beheerde servers van het Windows-beheercentrum weergeven](media/windows-admin-center-integration/viewing-alerts-wac.png)](media/windows-admin-center-integration/viewing-alerts-wac.png#lightbox)