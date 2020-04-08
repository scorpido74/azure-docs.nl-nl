---
title: Algemene taken voor beheer van cloudservices | Microsoft Documenten
description: Meer informatie over het beheren van Cloud Services in de Azure-portal. In deze voorbeelden wordt de Azure-portal gebruikt.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: 80481bc11933b0404079221f23b5054024f00acb
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811363"
---
# <a name="manage-cloud-services-in-the-azure-portal"></a>Cloudservices beheren in de Azure-portal
In het **gebied Cloud Services** van de Azure-portal u het als:

* Een servicerol of een implementatie bijwerken.
* Een gefaseerde implementatie naar productie bevorderen.
* Koppel resources aan uw cloudservice, zodat u de resourceafhankelijkheden zien en de resources samen schalen.
* Een cloudservice of een implementatie verwijderen.

Zie Automatisch schalen configureren voor een [cloudservice in de portal voor](cloud-services-how-to-scale-portal.md)meer informatie over het schalen van uw cloudservice.

## <a name="update-a-cloud-service-role-or-deployment"></a>Een cloudservicerol of -implementatie bijwerken
Als u de toepassingscode voor uw cloudservice moet bijwerken, gebruikt u **Bijwerken** op het cloudserviceblad. U één rol of alle rollen bijwerken. Als u wilt bijwerken, u een nieuw servicepakket of serviceconfiguratiebestand uploaden.

1. Selecteer in de [Azure-portal][Azure portal]de cloudservice die u wilt bijwerken. Met deze stap wordt het cloudservice-instantieblad geopend.

2. Selecteer **Bijwerken**op het blad .

    ![Knop Bijwerken](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Werk de implementatie bij met een nieuw servicepakketbestand (.cspkg) en serviceconfiguratiebestand (.cscfg).

    ![Implementatie bijwerken](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. Werkt optioneel het opslagaccount en het implementatielabel bij.

5. Als rollen slechts één rolinstantie hebben, schakelt u het selectievakje **Implementeren in, zelfs als een of meer rollen één instantie bevatten,** zodat de upgrade kan worden voortgezet.

    Azure kan slechts 99,95 procent servicebeschikbaarheid garanderen tijdens een update van een cloudservice als elke rol ten minste twee rolexemplaren (virtuele machines) heeft. Met twee rolexemplaren verwerkt de ene virtuele machine clientaanvragen, terwijl de andere wordt bijgewerkt.

6. Schakel het selectievakje **Implementatie starten** in om de update toe te passen nadat de upload van het pakket is voltooid.

7. Selecteer **OK** om te beginnen met het bijwerken van de service.

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>Implementaties omwisselen om een gefaseerde implementatie naar productie te bevorderen
Wanneer u besluit een nieuwe versie van een cloudservice te implementeren, begeeft u uw nieuwe release en test u deze in uw cloudservice-faseringsomgeving. Gebruik **Swap** om de URL's te schakelen waarmee de twee implementaties worden aangepakt en een nieuwe release naar productie te promoten.

U implementaties wisselen via de pagina **Cloud Services** of het dashboard.

1. Selecteer in de [Azure-portal][Azure portal]de cloudservice die u wilt bijwerken. Met deze stap wordt het cloudservice-instantieblad geopend.

2. Selecteer **Swap**op het blad .

    ![Knop Cloud Services-swap](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. De volgende bevestigingsprompt wordt geopend:

    ![CloudServices-swap](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Nadat u de implementatiegegevens hebt geverifieerd, selecteert u **OK** om de implementaties te verwisselen.

    De implementatieswap gebeurt snel omdat het enige dat verandert, de virtuele IP-adressen (VIP's) voor de implementaties zijn.

    Als u rekenkosten wilt besparen, u de implementatie van tijdelijke bestanden verwijderen nadat u hebt geverifieerd dat uw productie-implementatie werkt zoals verwacht.

### <a name="common-questions-about-swapping-deployments"></a>Veelvoorkomende vragen over het verwisselen van implementaties

**Wat zijn de voorwaarden voor het verwisselen van implementaties?**

Er zijn twee belangrijke vereisten voor een succesvolle implementatieswap:

- Als u een statisch IP-adres wilt gebruiken voor uw productiesleuf, moet u er ook een reserveren voor uw faseringssleuf. Anders mislukt de swap.

- Alle exemplaren van uw rollen moeten worden uitgevoerd voordat u de swap uitvoeren. U de status van uw exemplaren controleren op het **overzichtsblad** van de Azure-portal. U ook de opdracht [Get-AzureRole](/powershell/module/servicemanagement/azure/get-azurerole?view=azuresmps-3.7.0) gebruiken in Windows PowerShell.

Houd er rekening mee dat updates van gast-besturingssysteem en servicehealingbewerkingen ook kunnen leiden tot het mislukken van implementatieswaps. Zie [Problemen met de implementatie van cloudservices oplossen](cloud-services-troubleshoot-deployment-problems.md)voor meer informatie.

**Loopt een swap downtime voor mijn toepassing? Hoe moet ik ermee omgaan?**

Zoals beschreven in de vorige sectie, is een implementatieswap meestal snel, omdat het slechts een configuratiewijziging is in de Azure-load balancer. In sommige gevallen kan het 10 of meer seconden duren en resulteren in tijdelijke verbindingsfouten. Als u de impact op uw klanten wilt beperken, u overwegen [de logica voor het opnieuw proberen van de klant te](../best-practices-retry-general.md)implementeren.

## <a name="delete-deployments-and-a-cloud-service"></a>Implementaties en een cloudservice verwijderen
Voordat u een cloudservice verwijderen, moet u elke bestaande implementatie verwijderen.

Als u rekenkosten wilt besparen, u de implementatie van tijdelijke bestanden verwijderen nadat u hebt geverifieerd dat uw productie-implementatie werkt zoals verwacht. Er worden rekenkosten in rekening gebracht voor geïmplementeerde rolexemplaren die zijn gestopt.

Gebruik de volgende procedure om een implementatie of uw cloudservice te verwijderen.

1. Selecteer in de [Azure-portal][Azure portal]de cloudservice die u wilt verwijderen. Met deze stap wordt het cloudservice-instantieblad geopend.

2. Selecteer **Verwijderen**op het blad .

    ![Knop Verwijderen van cloudservices](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Als u de volledige cloudservice wilt verwijderen, schakelt u het selectievakje **Cloudservice en implementaties in.** U ook het selectievakje **Productie-implementatie** of het selectievakje **Implementatie fasering** kiezen.

    ![Cloudservices verwijderen](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Selecteer **Onderaan verwijderen.**

5. Als u de cloudservice wilt verwijderen, selecteert u **Cloudservice verwijderen**. Selecteer vervolgens bij de bevestigingsprompt **Ja**.

> [!NOTE]
> Wanneer een cloudservice wordt verwijderd en de bewaking van verbose is geconfigureerd, moet u de gegevens handmatig verwijderen uit uw opslagaccount. Zie [Inleiding tot cloudservicebewaking](cloud-services-how-to-monitor.md)voor informatie over waar u de statistiekentabellen vinden.


## <a name="find-more-information-about-failed-deployments"></a>Meer informatie over mislukte implementaties
Het **overzichtsblad** heeft bovenaan een statusbalk. Wanneer u de balk selecteert, wordt een nieuw blad geopend en worden foutgegevens weergegeven. Als de implementatie geen fouten bevat, is het informatieblad leeg.

![Overzicht van Cloud Services](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Volgende stappen
* [Algemene configuratie van uw cloudservice.](cloud-services-how-to-configure-portal.md)
* Meer informatie over het [implementeren van een cloudservice](cloud-services-how-to-create-deploy-portal.md).
* Een [aangepaste domeinnaam configureren](cloud-services-custom-domain-name-portal.md).
* [TLS/SSL-certificaten](cloud-services-configure-ssl-certificate-portal.md)configureren.



