---
title: Een nood herstel van virtuele machines volt ooien
description: In dit artikel wordt beschreven hoe u een nood herstel van virtuele machines kunt uitvoeren met behulp van de Azure VMware-oplossing
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 2ccb6546b9b01255e4a28aed79fd0d3ccbc4516c
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91579860"
---
# <a name="complete-a-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>Een nood herstel van virtuele machines met Azure VMware-oplossing volt ooien

Dit artikel bevat het proces voor het volt ooien van herstel na nood gevallen van uw virtuele machines (Vm's) met VMware HCX-oplossing en het gebruik van een persoonlijke cloud van Azure VMware-oplossing als herstel-of doel site.

VMware HCX biedt verschillende bewerkingen die nauw keurig en nauw keuriger kunnen worden beheerd in het replicatie beleid. Beschik bare bewerkingen zijn onder andere:

- **Omgekeerd** : nadat een nood geval is opgetreden. Reverse helpt bij het maken van site B de bron site en site waar de beveiligde VM nu woont.

- **Pause** : onderbreek het huidige replicatie beleid dat is gekoppeld aan de geselecteerde VM.

- **Hervatten** : Hiermee wordt het huidige replicatie beleid dat is gekoppeld aan de geselecteerde VM, onderbroken.

- **Verwijder** -Verwijder het huidige replicatie beleid dat is gekoppeld aan de geselecteerde VM.

- **Nu synchroniseren** : buiten de gebonden synchronisatie bron-VM naar de beveiligde virtuele machine.

In deze hand leiding worden de volgende replicatie scenario's behandeld:

- Een virtuele machine of een groep virtuele machines beveiligen.

- Een test herstel van een virtuele machine of een groep virtuele machines volt ooien.

- Een virtuele machine of een groep virtuele machines herstellen.

- Omgekeerde beveiliging van een virtuele machine of een groep virtuele machines.

## <a name="protect-vms"></a>Virtuele machines beschermen

1. Meld u aan bij de **vSphere-client** op de bron site en de toegang tot **HCX-invoeg toepassing**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/hcx-vsphere.png" alt-text="De optie HCX in vSphere" border="true":::

1. Voer het gebied voor **nood herstel** in en selecteer **vm's beveiligen**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png" alt-text="De optie HCX in vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png":::

1. Selecteer de bron en de externe sites. De externe site in dit geval moet de privécloud van Azure VMware-oplossing zijn.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machines.png" alt-text="De optie HCX in vSphere" border="true":::

1. Selecteer, indien nodig, de standaard opties voor **replicatie** :

   - **Compressie inschakelen:** Aanbevolen voor scenario's met lage door voer.

   - **Quiescence inschakelen:** Pauzeert de virtuele machine om ervoor te zorgen dat een consistente kopie wordt gesynchroniseerd met de externe site.

   - **Doel opslag:** Externe gegevens opslag voor de beveiligde Vm's, en in een privécloud van Azure VMware-oplossing, moet dit de vSAN-gegevens opslag zijn.

   - **Compute-container:** Extern vSphere-cluster of-resource groep.

   - **Doelmap:** Externe doelmap, die optioneel is, en als er geen map is geselecteerd, worden de Vm's direct onder het geselecteerde cluster geplaatst.

   - **RPO:** Synchronisatie-interval tussen de bron-VM en de beveiligde virtuele machine en kan vijf minuten tot 24 uur duren.

   - **Interval voor moment opnamen:** Interval tussen moment opnamen.

   - **Aantal moment opnamen:** Het totale aantal moment opnamen binnen het geconfigureerde interval voor moment opnamen.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png" alt-text="De optie HCX in vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png":::

1. Selecteer een of meer virtuele machines in de lijst en configureer de replicatie opties naar wens.

   De virtuele machines nemen standaard het beleid voor globale instellingen over dat is geconfigureerd in de standaard-replicatie opties. Configureer voor elke netwerk interface in de geselecteerde virtuele machine de externe **netwerk poort groep** en selecteer **volt ooien** om het beveiligings proces te starten.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/network-interface-options.png" alt-text="De optie HCX in vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/network-interface-options.png":::

1. Bewaak het proces voor elk van de geselecteerde Vm's in hetzelfde nood herstel gebied.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png" alt-text="De optie HCX in vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png":::

1. Nadat de virtuele machine is beveiligd, kunt u de verschillende moment opnamen weer geven op het tabblad **moment opnamen** .

   :::image type="content" source="./media/disaster-recovery-virtual-machines/list-of-snapshots.png" alt-text="De optie HCX in vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/list-of-snapshots.png":::

   De gele drie hoek betekent dat de moment opnamen en de virtuele schijf niet zijn getest in een test herstel bewerking.

   Er zijn belang rijke verschillen tussen een uitgeschakelde virtuele machine en een VM die is ingeschakeld. De afbeelding toont het synchronisatie proces voor een ingeschakelde VM. Het synchronisatie proces wordt gestart totdat de eerste moment opname is voltooid, een volledige kopie van de virtuele machine en de volgende stappen in het geconfigureerde interval. Voor een uitgeschakelde VM wordt een kopie gesynchroniseerd, waarna de VM wordt weer gegeven als inactief en de beveiligings bewerking wordt weer gegeven als voltooid.  Wanneer de virtuele machine is ingeschakeld, wordt het synchronisatie proces gestart voor de externe site.

## <a name="complete-a-test-recover-of-vms"></a>Een test herstel van Vm's volt ooien

1. Meld u aan bij de **vSphere-client** op de externe site. Dit is de privécloud van de Azure VMware-oplossing. 
1. Selecteer in de **HCX-invoeg toepassing**in het gebied nood herstel de verticale ellipsen op een wille keurige virtuele machine om het menu bewerkingen weer te geven en selecteer vervolgens **herstel-VM testen**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/test-recover-virtual-machine.png" alt-text="De optie HCX in vSphere" border="true":::

1. Selecteer de opties voor de test en de moment opname die u wilt gebruiken om verschillende statussen van de virtuele machine te testen.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/choose-snapshot.png" alt-text="De optie HCX in vSphere" border="true":::

1. Nadat de **test**is geselecteerd, wordt de herstel bewerking gestart.

1. Als u klaar bent, kunt u de nieuwe VM controleren in de Azure VMware-oplossing Private Cloud vCenter.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/verify-test-recovery.png" alt-text="De optie HCX in vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/verify-test-recovery.png":::

1. Nadat de tests zijn uitgevoerd op de virtuele machine of een toepassing die erop wordt uitgevoerd, voert u een schone bewerking uit om het test exemplaar te verwijderen.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png" alt-text="De optie HCX in vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png":::

## <a name="recover-vms"></a>Vm's herstellen

1. Meld u aan bij de **vSphere-client** op de externe site, de Azure VMware-oplossing privécloud en open de **HCX-invoeg toepassing**.

   Voor het herstel scenario wordt een groep virtuele machines die worden gebruikt voor dit voor beeld.

1. Selecteer de virtuele machine die u wilt herstellen in de lijst, open het menu **acties** en selecteer **vm's herstellen**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines.png" alt-text="De optie HCX in vSphere" border="true":::

1. Configureer de herstel opties voor elk exemplaar en selecteer **herstellen** om de herstel bewerking te starten.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines-confirm.png" alt-text="De optie HCX in vSphere" border="true":::

1. Nadat de herstel bewerking is voltooid, worden de nieuwe virtuele machines weer gegeven in de inventarisatie van de externe vCenter Server.

## <a name="complete-a-reverse-replication-on-vms"></a>Een omgekeerde replicatie op Vm's volt ooien

1. Meld u aan bij de **vSphere-client** op uw Azure VMware-oplossing privécloud en open de **HCX-invoeg toepassing**.
   
   >[!NOTE]
   >Zorg ervoor dat de oorspronkelijke Vm's op de bron site zijn uitgeschakeld voordat u de omgekeerde replicatie start. De bewerking mislukt als de virtuele machines niet zijn uitgeschakeld.

1. Selecteer in de lijst de virtuele machines die u wilt repliceren naar de bron site, open het menu **acties** en selecteer **omgekeerd**. 
1. Selecteer **omgekeerde** om de replicatie te starten.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/reverse-operation-virtual-machines.png" alt-text="De optie HCX in vSphere" border="true":::

1. Controleer de sectie Details van elke virtuele machine.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/review-reverse-operation.png" alt-text="De optie HCX in vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/review-reverse-operation.png":::

## <a name="disaster-recovery-plan-automation"></a>Automation plan voor herstel na nood gevallen

VMware HCX heeft momenteel geen ingebouwd mechanisme voor het maken en automatiseren van een plan voor herstel na nood gevallen. VMware HCX biedt echter een set REST-Api's, waaronder Api's voor de bewerking voor herstel na nood geval. De API-specificatie kan worden geopend in VMware HCX manager in de URL.

De volgende bewerkingen in nood herstel worden gedekt door deze Api's.

- Beveiligen

- Herstellen

- Herstellen testen

- Geplande herstel bewerking

- Reverse

- Query’s uitvoeren

- Opschonen testen

- Onderbreken

- Hervatten

- Beveiliging verwijderen

- Opnieuw configureren

Hieronder ziet u een voor beeld van een nettolading voor herstel bewerkingen in JSON.

```json
[

    {

        "replicationId": "string",

        "needPowerOn": true,

        "instanceId": "string",

        "source": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "destination": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "placement": [

            {

                "containerType": "string",

                "containerId": "string"

            }

        ],

        "resourceId": "string",

        "forcePowerOff": true,

        "isTest": true,

        "forcePowerOffAfterTimeout": true,

        "isPlanned": true

    }

]
```

Met behulp van deze Api's kan een klant een aangepast mechanisme bouwen om het maken en uitvoeren van een nood herstel plan te automatiseren.
