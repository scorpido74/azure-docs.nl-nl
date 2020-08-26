---
title: 'Zelfstudie: Een privécloud schalen'
description: In deze zelfstudie gebruikt u de Azure-portal voor het schalen van een privécloud van Azure VMware Solution Preview.
ms.topic: tutorial
ms.date: 08/21/2020
ms.openlocfilehash: dddfbddd57f3ad6b541d11c360aeadea4383044a
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750428"
---
# <a name="tutorial-scale-an-azure-vmware-solution-preview-private-cloud"></a>Zelfstudie: Een privécloud van Azure VMware Solution Preview schalen

Als u optimaal wilt profiteren van uw privécloud van Azure VMware Solution Preview, kunt u de clusters en hosts schalen aan de hand van wat u nodig hebt voor geplande workloads. Aangezien Azure VMware Solution in de preview-versie uw on-premises vCenter niet ondersteunt, moet u gebruiken wat u al hebt gemaakt via de Azure-portal.

U kunt het aantal clusters en het aantal hosts in een privécloud schalen naar de vereisten voor de workload van uw toepassing. De beperkingen voor de prestaties en de beschikbaarheid van specifieke services moeten per geval worden behandeld in de preview-cloudomgeving van Azure VMware Solution. De limieten voor het cluster en de host in een privécloud zijn uiteengezet in [het conceptartikel over privéclouds](concepts-private-clouds-clusters.md).

In deze zelfstudie hebt u de Azure-portal gebruikt voor het volgende:

> [!div class="checklist"]
> * Een cluster toevoegen aan een bestaande privécloud
> * Hosts toevoegen aan een bestaand cluster

## <a name="prerequisites"></a>Vereisten

U hebt een privécloud nodig om deze zelfstudie te voltooien. Als u nog geen privécloud hebt gemaakt, gebruikt u de [zelfstudie over het maken van een privécloud](tutorial-create-private-cloud.md) om een privécloud te maken en netwerkopties te configureren voor uw VMware-privécloud in Azure om het vereiste virtuele netwerk in te stellen.

## <a name="add-a-new-cluster"></a>Een nieuw cluster toevoegen

1. Selecteer op de overzichtspagina van een bestaande privécloud onder **Beheren** de optie **Privécloud schalen**. Selecteer vervolgens **+ Een cluster toevoegen**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="een cluster toevoegen selecteren" border="true":::

1. Gebruik op de pagina **Cluster toevoegen** de schuifregelaar om het aantal hosts te selecteren. Selecteer **Opslaan**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Gebruik op de pagina Cluster toevoegen de schuifregelaar om het aantal hosts te selecteren. Selecteer Opslaan." border="true":::

   De implementatie van het nieuwe cluster wordt gestart.

## <a name="scale-a-cluster"></a>Een cluster schalen 

1. Selecteer op de overzichtspagina van een bestaande privécloud de optie **Privécloud schalen** en selecteer het potloodpictogram om het cluster te bewerken.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Privécloud schalen selecteren in Overzicht" border="true":::

1. Gebruik op de pagina **Cluster bewerken** de schuifregelaar om het aantal hosts te selecteren. Selecteer **Opslaan**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="Gebruik op de pagina Cluster bewerken de schuifregelaar om het aantal hosts te selecteren. Selecteer Opslaan." border="true":::

   Het toevoegen van hosts aan het cluster wordt gestart.

## <a name="next-steps"></a>Volgende stappen

Als u nog een privécloud van Azure VMware Solution nodig hebt, kunt u [nog een privécloud maken](tutorial-create-private-cloud.md) met dezelfde netwerkvereisten en cluster- en hostlimieten.

<!-- LINKS - external-->

<!-- LINKS - internal -->
