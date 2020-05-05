---
title: 'Zelf studie: een privécloud schalen'
description: In deze zelf studie gebruikt u de Azure Portal voor het schalen van een Azure VMware-oplossing (AVS) voor beeld van een privécloud.
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 70d8c1c555badd6102f4b2547492bdea54e55783
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740288"
---
# <a name="tutorial-scale-an-azure-vmware-solution-avs-preview-private-cloud"></a>Zelf studie: een voor beeld van een Azure VMware-oplossing (AVS) schalen een privécloud

Om optimaal gebruik te kunnen gaan van uw AVS preview privécloud, schaalt u de clusters en hosts om te laten zien wat u nodig hebt voor geplande workloads. Omdat AVS geen ondersteuning biedt voor uw on-premises vCenter tijdens de preview, moet u de gegevens die u al hebt gemaakt via de Azure Portal gebruiken.

U kunt het aantal clusters en het aantal hosts in een privécloud, zoals vereist voor de workload van uw toepassing, schalen. De beperkingen voor de prestaties en de beschik baarheid van specifieke services moeten per geval worden behandeld in uw AVS-preview-cloud omgeving. De limieten voor het cluster en de host in een privécloud zijn opgenomen in [het concept artikel over de privécloud](concepts-private-clouds-clusters.md).

In deze zelf studie gebruikt u de Azure Portal voor het volgende:

> [!div class="checklist"]
> * Een cluster toevoegen aan een bestaande privécloud
> * Hosts toevoegen aan een bestaand cluster

## <a name="prerequisites"></a>Vereisten

U hebt een privécloud nodig om deze zelf studie te volt ooien. Als u nog geen privécloud hebt gemaakt, gebruikt u de [zelf studie een privécloud maken](tutorial-create-private-cloud.md) voor het maken van een privécloud en het configureren van netwerken voor uw VMware Privécloud in azure voor het instellen van het vereiste virtuele netwerk.

## <a name="add-a-new-cluster"></a>Een nieuw cluster toevoegen

Selecteer op de pagina overzicht van een bestaande privécloud onder **beheren**de optie **persoonlijke Cloud schalen**. Selecteer vervolgens **+ een cluster toevoegen**.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="Selecteer een cluster toevoegen" border="true":::

Gebruik op de pagina **cluster toevoegen** de schuif regelaar om het aantal hosts te selecteren. Selecteer **Opslaan**.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Een nieuw privécloud-cluster configureren" border="true":::

De implementatie van het nieuwe cluster wordt gestart.

## <a name="scale-a-cluster"></a>Een cluster schalen 

Selecteer op de pagina overzicht van een bestaande privécloud een **persoonlijke Cloud schalen** en selecteer het potlood pictogram om het cluster te bewerken.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Selecteer privécloud in overzicht schalen" border="true":::

Gebruik op de pagina **cluster bewerken** de schuif regelaar om het aantal hosts te selecteren. Selecteer **Opslaan**.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="Een nieuw privécloud-cluster configureren" border="true":::

De toevoeging van hosts aan het cluster wordt gestart.

## <a name="next-steps"></a>Volgende stappen

Als u een privécloud een andere nieuwe AVS wilt [maken, maakt u een nieuwe privécloud](tutorial-create-private-cloud.md)volgens dezelfde netwerk vereisten, cluster-en host limieten...

<!-- LINKS - external-->

<!-- LINKS - internal -->
