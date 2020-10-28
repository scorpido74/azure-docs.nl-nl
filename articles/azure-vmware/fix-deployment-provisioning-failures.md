---
title: Hulp krijgen bij implementatie van Azure VMware-oplossing of het inrichten van fouten
description: U kunt de informatie die u nodig hebt, ophalen uit de privécloud van uw Azure VMware-oplossing om een service aanvraag te doen voor implementatie van Azure VMware-oplossingen of het inrichten van fouten.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 1f46dde895db417fd2b488a6203d5482e73d3c5e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779489"
---
# <a name="get-help-with-azure-vmware-solution-deployment-or-provisioning-failures"></a>Hulp krijgen bij implementatie van Azure VMware-oplossing of het inrichten van fouten

Dit artikel helpt u bij het implementeren en inrichtings fouten van Azure VMware-oplossingen. Wanneer er fouten zijn opgetreden in uw privécloud, moet u een [ondersteunings aanvraag](https://rc.portal.azure.com/#create/Microsoft.Support) (SR) openen in de Azure Portal. 

Eerst moet u echter enkele belang rijke informatie verzamelen in de Azure Portal:

- Correlatie-id
- ExpressRoute-circuit-ID

## <a name="collect-the-correlation-id"></a>De correlatie-ID verzamelen
 
Er wordt een correlatie-ID gegenereerd wanneer u een privécloud of een resource in azure maakt. Elke Azure Resource Manager-implementatie genereert ook een correlatie-ID. Met deze ID kunt u sneller SR maken en oplossen. 
 
Hier volgt een voor beeld van de uitvoer van een mislukte implementatie van een privécloud, waarbij de correlatie-ID is gemarkeerd.

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="De implementatie van de privécloud met de correlatie-ID is mislukt.":::

Kopieer de correlatie-ID en sla deze op die moet worden opgenomen in de service aanvraag. Zie [uw ondersteunings aanvraag maken](#create-your-support-request) aan het einde van dit artikel voor meer informatie.

Als de fout optreedt in de voorafgaande validatie fase, wordt er geen correlatie-ID gegenereerd. In dit geval kunt u de gegevens opgeven die u hebt gebruikt bij het maken van de Azure VMware-oplossing privécloud, waaronder:

- Locatie
- Resourcegroep
- Resourcenaam
 
### <a name="collect-a-summary-of-errors"></a>Een samen vatting van fouten verzamelen

De details van eventuele fouten kunnen u helpen om uw probleem op te lossen. Selecteer in het voor gaande scherm het waarschuwings bericht om een samen vatting van fouten weer te geven.
 
 :::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="De implementatie van de privécloud met de correlatie-ID is mislukt.":::

Kopieer en sla deze samen vatting opnieuw op voor opname in de SR.
 
### <a name="retrieve-past-deployments"></a>Eerdere implementaties ophalen

U kunt eerdere implementaties, waaronder mislukte, ophalen door te zoeken in het activiteiten logboek van de implementatie waartoe u toegang wilt krijgen door het pictogram meldingen te selecteren.

:::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="De implementatie van de privécloud met de correlatie-ID is mislukt.":::

Selecteer in meldingen **meer gebeurtenissen in het activiteiten logboek** .

:::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="De implementatie van de privécloud met de correlatie-ID is mislukt.":::

Zoek vervolgens naar de naam van de resource of andere informatie die wordt gebruikt om de resource te maken om de mislukte implementatie en de correlatie-ID ervan te vinden. In het volgende voor beeld worden Zoek resultaten weer gegeven voor een privécloud (pc03).
 
:::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="De implementatie van de privécloud met de correlatie-ID is mislukt.":::
 
Als u de bewerkings naam van de mislukte implementatie selecteert, wordt er een venster met details geopend. Selecteer het tabblad JSON en zoek naar correlationId. Kopieer en neem deze op in de SR. 
 
## <a name="collect-the-expressroute-id-uri"></a>De ExpressRoute-ID (URI) verzamelen
 
Misschien probeert u een bestaande privécloud te schalen of een peer te koppelen met het ExpressRoute-circuit van de privécloud. In dat geval hebt u de ExpressRoute-ID nodig. 

Selecteer in de Azure Portal **connectiviteit > ExpressRoute** en kopieer de **ExpressRoute-id** naar het klem bord.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="De implementatie van de privécloud met de correlatie-ID is mislukt."::: 
 
> [!NOTE]
> In sommige gevallen kan controles vóór validatie mislukken voordat de implementatie wordt uitgevoerd. de enige beschik bare informatie is de fout-en fout berichten. Dit kan handig zijn in veel gevallen, bijvoorbeeld aan quota gerelateerde problemen, en het is belang rijk dat u deze berichten opneemt in de ondersteunings aanvraag. Zie de vorige sectie [een samen vatting van fouten verzamelen](#collect-a-summary-of-errors)om deze te verzamelen.

## <a name="create-your-support-request"></a>Uw ondersteunings aanvraag maken

Zie [een Azure-ondersteunings aanvraag maken](../azure-portal/supportability/how-to-create-azure-support-request.md)voor algemene richt lijnen bij het maken van uw ondersteunings aanvraag. 

Hier vindt u specifieke richt lijnen voor het maken van een implementatie of het inrichten van een SR voor Azure VMware-oplossing.

1. Selecteer het **Help** -pictogram en vervolgens **+ nieuwe ondersteunings aanvraag** .

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="De implementatie van de privécloud met de correlatie-ID is mislukt.":::

2. Vul alle vereiste velden in en op het tabblad **basis beginselen** :

    - Selecteer bij **probleem type** **configuratie-en installatie problemen** .

    - Selecteer **een Privécloud inrichten voor het** **subtype** van het probleem.

3. Op het tabblad **Details** :

    - Vul alle vereiste velden in.

    - Plak uw correlatie-ID of ExpressRoute-ID in de opgegeven specifieke velden. Als u een specifiek veld niet ziet, kunt u deze plakken in het tekstvak onder **Details over het probleem opgeven.**

    - Plak alle fout details, inclusief de samen vatting van fouten die u hebt gekopieerd, in het tekstvak onder **Details over het probleem opgeven.**

4. Controleer en selecteer **maken** om uw SR te maken.
