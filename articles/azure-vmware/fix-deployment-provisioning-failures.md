---
title: Hulp krijgen bij implementatie van Azure VMware-oplossing of het inrichten van fouten
description: U kunt de informatie die u nodig hebt, ophalen uit de privécloud van uw Azure VMware-oplossing om een service aanvraag te doen voor implementatie van Azure VMware-oplossingen of het inrichten van fouten.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 63d5440a9e2b15463e465e1d32762889508feca1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88752237"
---
# <a name="get-help-with-azure-vmware-solution-deployment-or-provisioning-failures"></a>Hulp krijgen bij implementatie van Azure VMware-oplossing of het inrichten van fouten

In dit artikel leert u hoe u hulp kunt krijgen bij het implementeren van de Azure VMware-oplossing of het inrichten van fouten in uw privécloud door een service aanvraag (SR) te openen in de Azure Portal. U moet eerst enkele belang rijke informatie verzamelen in de Azure Portal. In de meeste gevallen hebt u het volgende nodig:

- Correlatie-ID (van de mislukte implementatie)
- ExpressRoute-circuit-ID (bij het schalen of koppelen van een bestaande privécloud met het ExpressRoute-circuit privécloud en mislukt)

## <a name="collect-the-correlation-id"></a>De correlatie-ID verzamelen
 
We gaan eerst de correlatie-ID bekijken. Wanneer u een privécloud (of een resource in Azure) maakt, wordt er een bijbehorende correlatie-ID gegenereerd. Elke Azure Resource Manager-implementatie genereert ook een unieke correlatie-ID. Met deze ID kunt u sneller SR maken en oplossen. 
 
Hier volgt een voor beeld van de uitvoer van een mislukte implementatie van een privécloud, waarbij de correlatie-ID is gemarkeerd.

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="De implementatie van de privécloud met de correlatie-ID is mislukt.":::

Kopieer de correlatie-ID en sla deze op die moet worden opgenomen in de service aanvraag. Zie [uw ondersteunings aanvraag maken](#create-your-support-request) aan het einde van dit artikel voor meer informatie.

Als de fout optreedt in de voorafgaande validatie fasen voordat een privécloud wordt geïmplementeerd, wordt er geen correlatie-ID gegenereerd. In dit geval kunt u gewoon de gegevens opgeven die u hebt gebruikt bij het maken van de Azure VMware-oplossing privécloud, waaronder:

- Locatie
- Resourcegroep
- Resourcenaam
 
### <a name="collect-a-summary-of-errors"></a>Een samen vatting van fouten verzamelen

De details van fouten kunnen ook nuttig zijn bij het oplossen van uw probleem. Selecteer in het voor gaande scherm **op hier klikken voor details** (gemarkeerd) en een samen vatting van fouten wordt geopend, zoals wordt weer gegeven in de volgende scherm afbeelding.
 
 :::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="De implementatie van de privécloud met de correlatie-ID is mislukt.":::

Kopieer en sla deze samen vatting opnieuw op voor opname in de SR.
 
### <a name="retrieve-past-deployments"></a>Eerdere implementaties ophalen

U kunt eerdere implementaties, waaronder mislukte, ophalen door te zoeken in het activiteiten logboek van de implementatie waartoe u toegang wilt krijgen door het pictogram meldingen te selecteren.

:::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="De implementatie van de privécloud met de correlatie-ID is mislukt.":::

Selecteer in meldingen **meer gebeurtenissen in het activiteiten logboek**.

:::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="De implementatie van de privécloud met de correlatie-ID is mislukt.":::

Zoek vervolgens naar de naam van de resource of op een andere unieke informatie die u hebt gebruikt bij het maken van de resource om de mislukte implementatie en de correlatie-ID te vinden. In het volgende voor beeld worden Zoek resultaten weer gegeven voor een privécloud (pc03).
 
:::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="De implementatie van de privécloud met de correlatie-ID is mislukt.":::
 
Als u de bewerkings naam van de mislukte implementatie selecteert, wordt er een venster met details geopend. Selecteer het tabblad JSON en zoek naar correlationId. Kopieer en neem deze op in de SR. 
 
## <a name="collect-the-expressroute-id-uri"></a>De ExpressRoute-ID (URI) verzamelen
 
Mogelijk hebt u al een privécloud en is er een fout opgetreden bij het schalen van de Cloud of peer met het ExpressRoute-circuit van de privécloud. In dat geval kan de ExpressRoute-ID van de privécloud worden gebruikt om deze te identificeren wanneer u een SR maakt.

Wanneer u een privécloud in de portal bekijkt, selecteert u **connectiviteit > ExpressRoute** en kopieert u de **ExpressRoute-id** naar het klem bord.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="De implementatie van de privécloud met de correlatie-ID is mislukt."::: 
 
Plak de ExpressRoute-ID in het juiste veld in de nieuwe ondersteunings aanvraag. Voor meer informatie, zie de volgende sectie, [uw ondersteunings aanvraag maken](#create-your-support-request).
 
> [!NOTE]
> In sommige gevallen kunnen validatie controles vóór een implementatie mislukken en is de enige beschik bare informatie de fout-en/of fout berichten. Dit kan handig zijn in een aantal storingen, bijvoorbeeld bij problemen met betrekking tot quota. het is belang rijk dat u deze berichten opneemt in de ondersteunings aanvraag. Zie de vorige sectie [een samen vatting van fouten verzamelen](#collect-a-summary-of-errors)om deze te verzamelen.

## <a name="create-your-support-request"></a>Uw ondersteunings aanvraag maken

Zie [een Azure-ondersteunings aanvraag maken](../azure-portal/supportability/how-to-create-azure-support-request.md)voor algemene richt lijnen bij het maken van uw ondersteunings aanvraag. 

Hier vindt u aanvullende richt lijnen die specifiek zijn voor het maken van een SR voor implementatie van een Azure VMware-oplossing of het inrichten van fouten.

1. Selecteer het **Help** -pictogram en vervolgens **+ nieuwe ondersteunings aanvraag**.

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="De implementatie van de privécloud met de correlatie-ID is mislukt.":::

2. Vul alle vereiste velden in en op het tabblad **basis beginselen** :

    - Selecteer bij **probleem type** **configuratie-en installatie problemen**.

    - Selecteer **een Privécloud inrichten voor het** **subtype**van het probleem.

3. Op het tabblad **Details** :

    - Vul alle vereiste velden in.

    - Plak uw correlatie-ID of ExpressRoute-ID in de opgegeven specifieke velden. Als u geen specifiek veld voor deze velden ziet, kunt u ze in het tekstvak plakken onder **Details over het probleem opgeven.**

    - Plak alle fout details, inclusief de samen vatting van fouten die u hebt gekopieerd, in het tekstvak onder **Details over het probleem opgeven.**

4. Controleer en selecteer **maken** om uw SR te maken.
