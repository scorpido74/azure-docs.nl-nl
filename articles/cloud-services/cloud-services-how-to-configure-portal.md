---
title: Een cloudservice configureren (portal) | Microsoft Documenten
description: Meer informatie over het configureren van cloudservices in Azure. Meer informatie over het bijwerken van de configuratie van de cloudservice en het configureren van externe toegang tot rolexemplaren. In deze voorbeelden wordt de Azure-portal gebruikt.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 12/07/2016
ms.author: tagore
ms.openlocfilehash: 554d3e465b42ca889ba03565e87193f80e89ed1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75361004"
---
# <a name="how-to-configure-cloud-services"></a>Cloud Services configureren

U de meest gebruikte instellingen voor een cloudservice configureren in de Azure-portal. Als u uw configuratiebestanden rechtstreeks wilt bijwerken, downloadt u een serviceconfiguratiebestand om bij te werken en uploadt u het bijgewerkte bestand en werkt u de cloudservice bij met de configuratiewijzigingen. Hoe dan ook, de configuratie-updates worden naar alle rolinstanties gepusht.

U ook de exemplaren van uw cloudservicerollen of extern bureaublad erin beheren.

Azure kan alleen zorgen voor 99,95 procent servicebeschikbaarheid tijdens de configuratie-updates als u ten minste twee rolexemplaren voor elke rol hebt. Dat stelt de ene virtuele machine in staat om clientaanvragen te verwerken terwijl de andere wordt bijgewerkt. Zie [ServiceLevel Agreements voor](https://azure.microsoft.com/support/legal/sla/)meer informatie.

## <a name="change-a-cloud-service"></a>Een cloudservice wijzigen

Nadat u de [Azure-portal hebt](https://portal.azure.com/)geopend, navigeert u naar uw cloudservice. Vanaf hier beheer je veel aspecten ervan.

![Pagina Instellingen](./media/cloud-services-how-to-configure-portal/cloud-service.png)

De koppelingen **Instellingen** of **Alle instellingen** openen **instellingen** waar u de **eigenschappen**wijzigen, de **configuratie**wijzigen, de **certificaten**beheren, **waarschuwingsregels**instellen en de **gebruikers** beheren die toegang hebben tot deze cloudservice.

![Azure cloudservice-instellingen](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Gastbesturingssysteem-versie beheren

Azure werkt uw gastbesturingssysteem standaard periodiek bij aan de nieuwste ondersteunde afbeelding in de besturingssysteemfamilie die u hebt opgegeven in uw serviceconfiguratie (.cscfg), zoals Windows Server 2016.

Als u een specifieke versie van het besturingssysteem moet targeten, u deze instellen in **Configuratie**.

![Be-versie instellen](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> Als u een specifieke os-versie kiest, worden automatische OS-updates uitgeschakeld en wordt het patchen van uw verantwoordelijkheid. U moet ervoor zorgen dat uw rolexemplaren updates ontvangen of dat u uw toepassing blootstellen aan beveiligingsproblemen.

## <a name="monitoring"></a>Bewaking

U waarschuwingen toevoegen aan uw cloudservice. Klik op **Instellingen** > **waarschuwingsregels** > **Waarschuwing toevoegen**.

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

Vanaf hier u een waarschuwing instellen. Met de vervolgkeuzelijst **Metrische** u een waarschuwing instellen voor de volgende typen gegevens.

* Schijf lezen
* Schijfschrijven
* Netwerk in
* Netwerk uit
* CPU-percentage

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Controle configureren vanaf een metrische tegel

In plaats van > **instellingenwaarschuwingsregels te**gebruiken, u op een van de metrische tegels in de sectie **Settings** **Controle** van de cloudservice klikken.

![CloudServicebewaking](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Vanaf hier u de grafiek aanpassen die met de tegel wordt gebruikt of een waarschuwingsregel toevoegen.

## <a name="reboot-reimage-or-remote-desktop"></a>Opnieuw opstarten, opnieuw beelden of extern bureaublad

U extern bureaublad instellen via de [Azure-portal (extern bureaublad instellen),](cloud-services-role-enable-remote-desktop-new-portal.md) [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)of via [Visual Studio.](cloud-services-role-enable-remote-desktop-visual-studio.md)

Als u opnieuw wilt opstarten, opnieuw wilt afbeelding of afstandsbediening wilt in een Cloud Service, selecteert u de instantie van de cloudservice.

![Cloudservice-instantie](./media/cloud-services-how-to-configure-portal/cs-instance.png)

U vervolgens een verbinding met extern bureaublad starten, de instantie op afstand opnieuw opstarten of de instantie op afstand opnieuw weer aanmaken (beginnen met een nieuwe afbeelding).

![Knoppen voor cloudservice-instantie](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Uw .cscfg opnieuw configureren

Mogelijk moet u uw cloudservice opnieuw configureren via het [service config -bestand (cscfg).](cloud-services-model-and-package.md#cscfg) Eerst moet je je .cscfg-bestand downloaden, wijzigen en vervolgens uploaden.

1. Klik op het pictogram **Instellingen** of de koppeling **Alle instellingen** om **Instellingen**te openen.

    ![Pagina Instellingen](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Klik op het **configuratie-item.**

    ![Configuratieblad](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Klik op de knop **Downloaden**.

    ![Download](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. Nadat u het serviceconfiguratiebestand hebt bijgewerkt, uploadt en past u de configuratie-updates uit:

    ![Uploaden](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Selecteer het bestand .cscfg en klik op **OK**.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [implementeren van een cloudservice](cloud-services-how-to-create-deploy-portal.md).
* Een [aangepaste domeinnaam configureren](cloud-services-custom-domain-name-portal.md).
* [Beheer uw cloudservice.](cloud-services-how-to-manage-portal.md)
* [Ssl-certificaten](cloud-services-configure-ssl-certificate-portal.md)configureren .



