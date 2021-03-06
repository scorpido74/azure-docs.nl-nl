---
title: Windows-beheer centrum-servers beveiligen met Azure Security Center
description: In dit artikel wordt uitgelegd hoe u Azure Security Center integreert met Windows-beheer centrum
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: memildin
ms.openlocfilehash: 36f519ce41ccfbfb48ca696ed2a61c6131a75998
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906326"
---
# <a name="protect-windows-admin-center-resources-with-security-center"></a>Windows-beheer centrum-resources beveiligen met Security Center

Windows-beheer centrum is een beheer programma voor uw Windows-servers. Het is één locatie voor systeem beheerders om toegang te krijgen tot het meren deel van de meest gebruikte beheer hulpprogramma's. Vanuit het Windows-beheer centrum kunt u uw on-premises servers rechtstreeks op de Azure Security Center. U kunt vervolgens een samen vatting van uw beveiligings aanbevelingen en waarschuwingen rechtstreeks bekijken in de Windows-beheer centrum-ervaring.

> [!NOTE]
> Voor uw Azure-abonnement en de bijbehorende Log Analytics-werk ruimte moet Azure Defender zijn ingeschakeld om de integratie van het Windows-beheer centrum mogelijk te maken.
> Azure Defender is de eerste 30 dagen gratis als u deze nog niet eerder hebt gebruikt in het abonnement en de werk ruimte. Zie [de pagina met prijs informatie](security-center-pricing.md)voor meer informatie.
>

Wanneer u een server uit het Windows-beheer centrum hebt geboardd naar Azure Security Center, kunt u het volgende doen:

* Beveiligings waarschuwingen en aanbevelingen weer geven binnen de Security Center-extensie in het Windows-beheer centrum
* Bekijk de beveiligings postuur en haal meer gedetailleerde informatie op over uw door Windows-beheer centrum beheerde servers in Security Center binnen de Azure Portal (of via een API)

Door deze twee hulpprogram ma's te combi neren, wordt Security Center uw enige glas venster om al uw beveiligings gegevens weer te geven, ongeacht de bron: uw Windows-beheer centrum beheert on-premises servers, uw Vm's en eventuele extra PaaS-workloads.

## <a name="onboard-windows-admin-center-managed-servers-into-security-center"></a>Onboarding van door Windows-beheer centrum beheerde servers in Security Center

1. Selecteer een van uw servers in het Windows-beheer centrum en selecteer in het deel venster **extra** de extensie Azure Security Center:

    ![Azure Security Center-extensie in het Windows-beheer centrum](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > Als de server al is onboarded naar Security Center, wordt het configuratie venster niet weer gegeven.

1. Klik op **Aanmelden bij Azure en stel in**.
    ![Onboarding van Windows-beheer centrum uitbrei ding naar Azure Security Center](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. Volg de instructies om uw server te verbinden met Security Center. Nadat u de benodigde gegevens hebt ingevoerd en bevestigd, Security Center de benodigde configuratie wijzigingen aanbrengen om ervoor te zorgen dat alle volgende voor waarden van toepassing zijn:
    * Er is een Azure-gateway geregistreerd.
    * De server heeft een werk ruimte om aan te melden en een gekoppeld abonnement.
    * De Log Analytics oplossing van Security Center is ingeschakeld op de werk ruimte. Deze oplossing biedt functies van Azure Defender voor *alle* servers en virtuele machines die aan deze werk ruimte rapporteren.
    * Azure Defender voor servers is ingeschakeld voor het abonnement.
    * De Log Analytics-agent is geïnstalleerd op de server en geconfigureerd om te rapporteren aan de geselecteerde werk ruimte. Als de server al aan een andere werk ruimte rapporteert, wordt deze ook geconfigureerd om te rapporteren aan de zojuist geselecteerde werk ruimte.

    > [!NOTE]
    > Het kan enige tijd duren voordat de aanbevelingen worden weer gegeven. Afhankelijk van uw server activiteit *worden er mogelijk geen waarschuwingen weer* gegeven. Als u test waarschuwingen wilt genereren om te testen of uw waarschuwingen correct werken, volgt u de instructies in [de procedure voor waarschuwings validatie](security-center-alert-validation.md).


## <a name="view-security-recommendations-and-alerts-in-windows-admin-center"></a>Aanbevelingen en waarschuwingen voor beveiliging in het Windows-beheer centrum weer geven

Als u dit hebt gedaan, kunt u uw waarschuwingen en aanbevelingen rechtstreeks bekijken in het gedeelte Azure Security Center van het Windows-beheer centrum. Klik op een aanbeveling of een waarschuwing om deze weer te geven in de Azure Portal. Daar vindt u aanvullende informatie en leert u hoe u problemen kunt oplossen.

[![Aanbevelingen en waarschuwingen Security Center zoals weer gegeven in het Windows-beheer centrum](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="view-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>Aanbevelingen en waarschuwingen voor beveiliging voor door Windows-beheer centrum beheerde servers in Security Center weer geven
Van Azure Security Center:

* Als u beveiligings aanbevelingen voor al uw Windows-beheer centrum servers wilt weer geven, opent u [inventarisatie van assets](asset-inventory.md) en filtert u het type machine dat u wilt onderzoeken. Selecteer het tabblad **vm's en computers** .

* Als u beveiligings waarschuwingen voor alle Windows-beheer centrum servers wilt weer geven, opent u **beveiligings waarschuwingen**. Klik op **filter** en zorg ervoor dat u **alleen** ' niet-Azure ' selecteert:

    :::image type="content" source="./media/windows-admin-center-integration/filtering-alerts-by-environment.png" alt-text="Beveiligings waarschuwingen voor door Windows-beheer centrum beheerde servers filteren" lightbox="./media/windows-admin-center-integration/filtering-alerts-by-environment.png":::