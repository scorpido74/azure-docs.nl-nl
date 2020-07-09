---
title: Automatische update van de Mobility-service in Azure Site Recovery
description: Overzicht van de automatische update van de Mobility-service bij het repliceren van virtuele Azure-machines met behulp van Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/02/2020
ms.author: rajanaki
ms.openlocfilehash: b57ce89979225015dc87bbfb17f9603897ef6d6b
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135845"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Automatische update van de Mobility-service in azure-naar-Azure-replicatie

Azure Site Recovery maakt gebruik van een maandelijkse release-uitgebracht om eventuele problemen op te lossen en bestaande functies te verbeteren of nieuwe onderdelen toe te voegen. Als u actueel wilt blijven met de service, moet u elke maand een patch implementatie plannen. Als u wilt voor komen dat de overhead aan elke upgrade is gekoppeld, kunt u Site Recovery toestaan onderdeel updates te beheren.

Zoals vermeld in [Azure-to-Azure-nood herstel architectuur](azure-to-azure-architecture.md), wordt de Mobility-service geïnstalleerd op alle Azure virtual machines (vm's) waarvoor replicatie van de ene Azure-regio naar de andere is ingeschakeld. Wanneer u automatische updates gebruikt, werkt elke nieuwe release de Mobility service-extensie bij.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Hoe werkt automatische updates?

Wanneer u Site Recovery gebruikt voor het beheren van updates, implementeert het een globaal runbook (gebruikt door Azure-Services) via een Automation-account dat is gemaakt in hetzelfde abonnement als de kluis. Elke kluis gebruikt één Automation-account. Voor elke VM in een kluis controleert het runbook of er actieve automatische updates zijn. Als er een nieuwere versie van de Mobility service-extensie beschikbaar is, wordt de update geïnstalleerd.

Het standaard runbook-schema wordt dagelijks om 12:00 uur in de tijd zone van de Geografie van de gerepliceerde VM weer gegeven. U kunt het runbook-schema ook wijzigen via het Automation-account.

> [!NOTE]
> Vanaf [Update pakket 35](site-recovery-whats-new.md#updates-march-2019)kunt u een bestaand Automation-account kiezen dat u wilt gebruiken voor updates. Vóór update pakket 35 heeft Site Recovery standaard het Automation-account gemaakt. U kunt deze optie alleen selecteren wanneer u replicatie voor een virtuele machine inschakelt. Het is niet beschikbaar voor een virtuele machine waarop replicatie al is ingeschakeld. De instelling die u selecteert, is van toepassing op alle virtuele Azure-machines die in dezelfde kluis worden beveiligd.

Bij het inschakelen van automatische updates is het niet nodig om uw virtuele Azure-machines opnieuw op te starten of de actieve replicatie te beïnvloeden.

De facturering van taken in het Automation-account is gebaseerd op het aantal taak runtime minuten dat in een maand is gebruikt. Het uitvoeren van de taak duurt een paar seconden tot ongeveer een minuut en wordt gedekt als gratis eenheden. Standaard worden 500 minuten opgenomen als gratis eenheden voor een Automation-account, zoals wordt weer gegeven in de volgende tabel:

| Gratis eenheden inbegrepen (elke maand) | Prijs |
|---|---|
| Taak runtime 500 minuten | ₹ 0.14/minuut

## <a name="enable-automatic-updates"></a>Automatische updates inschakelen

Er zijn verschillende manieren waarop Site Recovery de extensie-updates kunt beheren:

- [Beheren als onderdeel van de stap replicatie inschakelen](#manage-as-part-of-the-enable-replication-step)
- [De instellingen voor het bijwerken van de extensie in de kluis in-/uitschakelen](#toggle-the-extension-update-settings-inside-the-vault)
- [Updates hand matig beheren](#manage-updates-manually)

### <a name="manage-as-part-of-the-enable-replication-step"></a>Beheren als onderdeel van de stap replicatie inschakelen

Wanneer u replicatie voor een virtuele machine inschakelt [vanuit de VM-weer gave](azure-to-azure-quickstart.md) of [vanuit de Recovery Services-kluis](azure-to-azure-how-to-enable-replication.md), kunt u toestaan dat Site Recovery updates voor de site Recovery-extensie beheert of hand matig beheert.

:::image type="content" source="./media/azure-to-azure-autoupdate/enable-rep.png" alt-text="Extensie-instellingen":::

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>De instellingen voor het bijwerken van de extensie in de kluis in-/uitschakelen

1. Ga vanuit de Recovery Services kluis naar **Manage**  >  **site Recovery-infra structuur**beheren.
1. Onder voor de update-instellingen **van Azure virtual machines**  >  -**extensie**  >  **kunt u site Recovery beheren**selecteren. **On**

   Als u de extensie hand matig wilt beheren, selecteert u **uit**.

1. Selecteer **Opslaan**.

:::image type="content" source="./media/azure-to-azure-autoupdate/vault-toggle.png" alt-text="Instellingen voor extensie-updates":::

> [!IMPORTANT]
> Wanneer u **site Recovery voor beheer toestaan**kiest, wordt de instelling toegepast op alle virtuele machines in de kluis.

> [!NOTE]
> Een van beide opties waarschuwt u voor het Automation-account dat wordt gebruikt voor het beheren van updates. Als u deze functie voor de eerste keer gebruikt in een kluis, wordt standaard een nieuw Automation-account gemaakt. U kunt de instelling ook aanpassen en een bestaand Automation-account kiezen. Alle volgende keer dat de replicatie in dezelfde kluis wordt ingeschakeld, wordt het eerder gemaakte Automation-account gebruikt. Op dit moment worden in de vervolg keuzelijst alleen Automation-accounts weer geven die zich in dezelfde resource groep bevinden als de kluis.

### <a name="manage-updates-manually"></a>Updates hand matig beheren

1. Als er nieuwe updates zijn voor de Mobility-service die is geïnstalleerd op uw Vm's, ziet u de volgende melding: er **is een nieuwe site Recovery update voor de replicatie agent beschikbaar. Klik om te installeren.**

   :::image type="content" source="./media/vmware-azure-install-mobility-service/replicated-item-notif.png" alt-text="Venster gerepliceerde items":::

1. Selecteer de melding om de pagina VM selecteren te openen.
1. Kies de Vm's die u wilt bijwerken en selecteer vervolgens **OK**. De update Mobility-service wordt gestart voor elke geselecteerde VM.

   :::image type="content" source="./media/vmware-azure-install-mobility-service/update-okpng.png" alt-text="VM-lijst van gerepliceerde items":::

## <a name="common-issues-and-troubleshooting"></a>Veelvoorkomende problemen en probleem oplossing

Als er een probleem is met de automatische updates, wordt er een fout melding weer geven onder **configuratie problemen** in het kluis dashboard.

Als u automatische updates niet kunt inschakelen, raadpleegt u de volgende veelvoorkomende fouten en aanbevolen acties:

- **Fout**: u hebt geen machtigingen om een uitvoeren als-account voor Azure (Service-Principal) te maken en de rol Inzender toe te kennen aan de Service-Principal.

  **Aanbevolen actie**: Zorg ervoor dat het aangemelde account is toegewezen als Inzender en probeer het opnieuw. Voor meer informatie over het toewijzen van machtigingen raadpleegt u de sectie vereiste machtigingen [: gebruik de portal om een Azure AD-toepassing en Service-Principal te maken die toegang hebben tot resources](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

  Als u de meeste problemen wilt verhelpen nadat u automatische updates hebt ingeschakeld, selecteert u **herstellen**. Als de knop herstellen niet beschikbaar is, raadpleegt u het fout bericht dat wordt weer gegeven in het deel venster instellingen voor het bijwerken van de extensie.

  :::image type="content" source="./media/azure-to-azure-autoupdate/repair.png" alt-text="Knop Site Recovery service herstellen in instellingen voor extensie-updates":::

- **Fout**: het run as-account heeft geen machtiging voor toegang tot de Recovery Services-resource.

  **Aanbevolen actie**: Verwijder [het uitvoeren als-account en maak het vervolgens opnieuw](../automation/manage-runas-account.md). Of zorg ervoor dat de Azure Active Directory toepassing van het Automation uitvoeren als-account toegang heeft tot de Recovery Services-resource.

- **Fout**: het run as-account is niet gevonden. Een van deze is verwijderd of niet gemaakt: Azure Active Directory toepassing, Service-Principal, rol, Automation-certificaat Asset, Automation-verbindings element, of de vinger afdruk is niet identiek aan het certificaat en de verbinding.

  **Aanbevolen actie**: Verwijder [het uitvoeren als-account en maak het vervolgens opnieuw](../automation/manage-runas-account.md).

- **Fout**: het uitvoeren als-certificaat van Azure dat wordt gebruikt door het Automation-account is bijna verlopen.

  Het zelfondertekende certificaat dat voor het run as-account is gemaakt, verloopt één jaar na de aanmaak datum. U kunt het certificaat op elk gewenst moment vernieuwen voordat het verloopt. Als u zich hebt geregistreerd voor e-mail meldingen, ontvangt u ook e-mails wanneer een actie is vereist aan uw zijde. Deze fout wordt weer gegeven twee maanden vóór de verval datum en wordt gewijzigd in een kritieke fout als het certificaat is verlopen. Zodra het certificaat is verlopen, werkt automatisch bijwerken pas nadat u dit hebt vernieuwd.

  **Aanbevolen actie**: Selecteer **herstellen** en vervolgens **certificaat vernieuwen**om dit probleem op te lossen.

  :::image type="content" source="./media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG" alt-text="renew-CERT":::

  > [!NOTE]
  > Nadat u het certificaat hebt vernieuwd, vernieuwt u de pagina om de huidige status weer te geven.
