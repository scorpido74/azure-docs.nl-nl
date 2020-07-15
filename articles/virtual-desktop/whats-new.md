---
title: Nieuwe functies in Windows Virtual Desktop - Azure
description: Nieuwe functies en productupdates voor Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 06/15/2020
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.openlocfilehash: 7052e9203532320a7de5197e983d40cb0a34b50d
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85374777"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Nieuwe functies in Windows Virtual Desktop

Windows Virtual Desktop wordt regelmatig bijgewerkt. In dit artikel vindt u meer informatie over:

- De meest recente updates
- Nieuwe functies
- Verbeteringen in bestaande functies
- Opgeloste fouten

Dit artikel wordt maandelijks bijgewerkt. Controleer hier regelmatig of er nieuwe updates beschikbaar zijn.

## <a name="june-2020"></a>Juni 2020

De afgelopen maand is de Windows Virtual Desktop Lente 2020-update geïntroduceerd in de preview-versie. Deze update bevat veel geweldige nieuwe functies waar we graag meer over vertellen. Hier ziet u welke nieuwe functies de Lente 2020-update bevat.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager-preview"></a>Windows Virtual Desktop is nu geïntegreerd met Azure Resource Manager (preview)

Windows Virtual Desktop is nu geïntegreerd in Azure Resource Manager. In de meest recente update zijn alle Windows Virtual Desktop-objecten omgezet naar Azure Resource Manager-resources. Deze update is ook geïntegreerd met op rollen gebaseerd toegangsbeheer (RBAC) van Azure. Raadpleeg [Wat is Azure Resource Manager?](../azure-resource-manager/management/overview.md) voor meer informatie.

Dit is wat deze wijziging betekent voor u:

- Windows Virtual Desktop is nu geïntegreerd met de Azure-portal. Dit betekent dat u alles rechtstreeks kunt beheren in de portal. U hebt hiervoor geen PowerShell, web-apps of hulpprogramma's van derden nodig. Bekijk onze zelfstudie op [Een hostpool maken met behulp van de Azure-portal](create-host-pools-azure-marketplace.md) om aan de slag te gaan.

- Vóór de Lente 2020-update kon u RemoteApps en Desktops alleen publiceren voor individuele gebruikers. Met Azure Resource Manager kunt u resources nu publiceren in Azure Active Directory-groepen.

- De eerdere versie van Windows Virtual Desktop beschikte over vier ingebouwde beheerdersrollen die u kon toewijzen aan een tenant of hostpool. Deze rollen vallen in Azure nu onder [op rollen gebaseerd toegangsbeheer](../role-based-access-control/overview.md). U kunt deze rollen toepassen op elk Azure Resource Manager-object in Windows Virtual Desktop, waardoor u beschikt over een compleet en uitgebreid delegatiemodel.

- In de Lente 2020-update hoeft u Azure Marketplace of de GitHub-sjabloon niet meer herhaaldelijk uit te voeren om een hostpool uit te breiden. Het enige wat u hoeft te doen om een hostpool uit te breiden, is naar de hostpool gaan in de Azure-portal en **+ Toevoegen** selecteren om extra sessiehosts te implementeren.

- Implementatie van hostpools is nu volledig geïntegreerd met de [Gedeelde installatiekopiegalerie van Azure](../virtual-machines/windows/shared-image-galleries.md). De Gedeelde installatiekopiegalerie is een afzonderlijke Azure-service waarin installatiekopiedefinities van VM’s (virtuele machines) worden opgeslagen, waaronder versiebeheer voor installatiekopieën. U kunt ook globale replicatie gebruiken om uw installatiekopieën te kopiëren en te verzenden naar andere Azure-regio’s voor lokale implementatie.

- Bewakingsfuncties die voorheen werden uitgevoerd via PowerShell of met de web-app voor de Diagnostische service, zijn nu verplaatst naar Log Analytics in de Azure-portal. U hebt nu ook twee opties om uw rapporten te visualiseren. U kunt Kusto-query's uitvoeren en werkmappen gebruiken om visuele rapporten te maken.

- U hebt geen Azure AD-toestemming (Azure Active Directory) meer nodig om Windows Virtual Desktop te gebruiken. In de Lente 2020-update worden met de Azure AD-tenant in uw Azure-abonnement uw gebruikers geverifieerd en wordt RBAC geboden voor uw beheerders.


### <a name="powershell-support"></a>PowerShell-ondersteuning

In de Lente 2020-update zijn nieuwe AzWvd-cmdlets toegevoegd aan de Azure PowerShell AZ-module. Deze nieuwe module wordt ondersteund in PowerShell Core, wat wordt uitgevoerd in .NET Core.

Volg de instructies in [De PowerShell-module instellen voor Windows Virtual Desktop](powershell-module.md) om de module te installeren.

U kunt ook een lijst met beschikbare opdrachten bekijken in de [AzWvd PowerShell-referentie](/powershell/module/az.desktopvirtualization/?view=azps-4.2.0#desktopvirtualization).

Lees [onze blogpost](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245) voor meer informatie over de nieuwe functies. 

### <a name="additional-gateways"></a>Aanvullende gateways

We hebben een nieuw gatewaycluster toegevoegd in Zuid-Afrika om de latentie van de verbinding te verminderen.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Microsoft Teams in Windows Virtual Desktop (preview)

We hebben enkele verbeteringen aangebracht in Microsoft Teams voor Windows Virtual Desktop. Het belangrijkste is dat Windows Virtual Desktop nu ondersteuning biedt voor de omleiding van audio en visualisatie bij oproepen. Omleiding verbetert de latentie door directe paden te maken tussen gebruikers wanneer ze audio of video gebruiken om te bellen. Minder afstand betekent ook minder hops, waardoor oproepen een helderder beeld en beter geluid hebben.

Lees [onze blogpost](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Lees meer over toekomstige plannen in de [roadmap voor Microsoft 365 Windows Virtual Desktop](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop).

Bekijk deze artikelen voor meer informatie over updates voor onze clients voor Windows Virtual Desktop en Extern bureaublad-services:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [MacOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)
