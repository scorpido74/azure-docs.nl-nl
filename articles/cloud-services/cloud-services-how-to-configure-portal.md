---
title: Een Cloud service (Portal) configureren | Microsoft Docs
description: Meer informatie over het configureren van Cloud Services in Azure. Meer informatie over het bijwerken van de configuratie van de Cloud service en het configureren van externe toegang tot rolinstanties. In deze voor beelden wordt gebruikgemaakt van de Azure Portal.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 12/07/2016
ms.author: tagore
ms.openlocfilehash: e862818a4fe2471af574d153d43f0096af7847b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80811412"
---
# <a name="how-to-configure-cloud-services"></a>Cloud Services configureren

U kunt de meestgebruikte instellingen voor een Cloud service configureren in de Azure Portal. Als u uw configuratie bestanden direct wilt bijwerken, downloadt u een service configuratie bestand om bij te werken en uploadt u vervolgens het bijgewerkte bestand en werkt u de Cloud service bij met de configuratie wijzigingen. In beide gevallen worden de configuratie-updates naar alle rolinstanties gepusht.

U kunt ook de exemplaren van uw Cloud service rollen, of extern bureau blad, beheren.

Azure kan alleen de service beschikbaarheid van 99,95% garanderen tijdens de configuratie-updates als u ten minste twee rolinstanties voor elke rol hebt. Hiermee kan één virtuele machine client aanvragen verwerken terwijl de andere wordt bijgewerkt. Zie [Service overeenkomst](https://azure.microsoft.com/support/legal/sla/)voor meer informatie.

## <a name="change-a-cloud-service"></a>Een Cloud service wijzigen

Ga na het openen van de [Azure Portal](https://portal.azure.com/)naar uw Cloud service. Hier beheert u veel aspecten hiervan.

![Pagina instellingen](./media/cloud-services-how-to-configure-portal/cloud-service.png)

Met de koppelingen **instellingen** of **alle instellingen** worden **instellingen** geopend, waar u de **Eigenschappen**kunt wijzigen, de **configuratie**wijzigen, de **certificaten**beheert, **waarschuwings regels**instelt en de **gebruikers** beheert die toegang hebben tot deze Cloud service.

![Instellingen van de Azure-Cloud service](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>De versie van het gast besturingssysteem beheren

Standaard werkt Azure uw gast besturingssysteem regel matig bij naar de meest recente ondersteunde installatie kopie in de besturingssysteem familie die u hebt opgegeven in uw service configuratie (. cscfg), zoals Windows Server 2016.

Als u een specifieke versie van het besturings systeem nodig hebt, kunt u deze instellen in de **configuratie**.

![Versie van besturings systeem instellen](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> Als u een specifieke versie van het besturings systeem kiest, worden automatisch updates van het besturings systeem uitgeschakeld en wordt er patch voor uw verantwoordelijkheid gemaakt. U moet ervoor zorgen dat uw rolinstanties updates ontvangen of u kunt uw toepassing bloot stellen aan beveiligings problemen.

## <a name="monitoring"></a>Bewaking

U kunt waarschuwingen toevoegen aan uw Cloud service. Klik op **instellingen** > **waarschuwings regels** > **waarschuwing toevoegen**.

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

Hier kunt u een waarschuwing instellen. In de vervolg keuzelijst **metriek** kunt u een waarschuwing instellen voor de volgende typen gegevens.

* Schijf lezen
* Schijf schrijven
* Netwerk - inkomend
* Netwerk - uitgaand
* CPU-percentage

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Bewaking configureren op basis van een metrische tegel

In plaats van **instellingen** > voor**waarschuwings regels**te gebruiken, kunt u klikken op een van de metrische tegels in de sectie **bewaking** van de Cloud service.

![Cloud service bewaken](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Hier kunt u de grafiek die wordt gebruikt met de tegel aanpassen of een waarschuwings regel toevoegen.

## <a name="reboot-reimage-or-remote-desktop"></a>Opnieuw opstarten, installatie kopie terugzetten of extern bureau blad

U kunt extern bureau blad instellen via de [Azure Portal (extern bureau blad instellen)](cloud-services-role-enable-remote-desktop-new-portal.md), [Power shell](cloud-services-role-enable-remote-desktop-powershell.md)of via [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).

Als u opnieuw wilt opstarten, een installatie kopie of een externe server wilt omzetten in een Cloud service, selecteert u het Cloud service-exemplaar.

![Cloud service-exemplaar](./media/cloud-services-how-to-configure-portal/cs-instance.png)

U kunt vervolgens een verbinding met een extern bureau blad initiëren, het exemplaar extern opnieuw opstarten of een installatie kopie op afstand (beginnen met een nieuwe installatie kopie) van het exemplaar.

![Knoppen voor het Cloud service-exemplaar](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Uw. cscfg opnieuw configureren

Mogelijk moet u de Cloud service opnieuw configureren via het [cscfg-bestand (service config)](cloud-services-model-and-package.md#cscfg) . Eerst moet u uw cscfg-bestand downloaden, wijzigen en vervolgens uploaden.

1. Klik op het **instellingen** pictogram of de koppeling **alle instellingen** om **instellingen**te openen.

    ![Pagina instellingen](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Klik op het **configuratie** -item.

    ![Blade configuratie](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Klik op de knop **Downloaden**.

    ![Download](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. Nadat u het service configuratie bestand hebt bijgewerkt, uploadt u de configuratie-updates en past u deze toe:

    ![Uploaden](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Selecteer het. cscfg-bestand en klik op **OK**.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [implementeren van een Cloud service](cloud-services-how-to-create-deploy-portal.md).
* Een [aangepaste domein naam](cloud-services-custom-domain-name-portal.md)configureren.
* [Uw Cloud service beheren](cloud-services-how-to-manage-portal.md).
* [TLS/SSL-certificaten](cloud-services-configure-ssl-certificate-portal.md)configureren.



