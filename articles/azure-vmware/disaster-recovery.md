---
title: Een nood herstel van virtuele machines volt ooien
description: In dit artikel wordt beschreven hoe u een herstel na nood geval voor virtuele machines kunt uitvoeren met behulp van AVS
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 5fdfc252486d94fcc22ebba9705fa5e259539921
ms.sourcegitcommit: 2721b8d1ffe203226829958bee5c52699e1d2116
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/28/2020
ms.locfileid: "84148154"
---
# <a name="complete-a-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>Een nood herstel van virtuele machines met Azure VMware-oplossing volt ooien

Dit artikel bevat het proces voor het volt ooien van herstel na nood gevallen van uw virtuele machines met de VMware Hybrid Cloud extension (HCX)-oplossing en het gebruik van een persoonlijke cloud van Azure VMware-oplossing als herstel-of doel site.

VMware HCX biedt verschillende bewerkingen die nauw keurig en nauw keuriger kunnen worden beheerd in het replicatie beleid. Beschik bare bewerkingen zijn onder andere:

- Omgekeerd: nadat een nood geval is opgetreden. Reverse helpt bij het maken van site B de bron site en site waar de beveiligde VM nu woont.

- Onderbreken: onderbreek het huidige replicatie beleid dat is gekoppeld aan de geselecteerde virtuele machine.

- Hervatten: Hiermee wordt het huidige replicatie beleid dat is gekoppeld aan de geselecteerde virtuele machine, onderbroken.

- Verwijder-Verwijder het huidige replicatie beleid dat is gekoppeld aan de geselecteerde virtuele machine.

- Nu synchroniseren: de virtuele machine van de gebonden synchronisatie bron op de beveiligde VM.

In deze hand leiding worden de volgende replicatie scenario's behandeld:

- Een virtuele machine of een groep virtuele machines beveiligen.

- Een test herstel van een virtuele machine of een groep virtuele machines volt ooien.

- Een virtuele machine of een groep virtuele machines herstellen.

- Omgekeerde beveiliging van een virtuele machine of een groep virtuele machines.

## <a name="protect-virtual-machines"></a>Virtuele machines beveiligen

Meld u aan bij de **vSphere-client** op de bron site en de toegang tot **HCX-invoeg toepassing**.

:::image type="content" source="./media/disaster-recovery/hcx-vsphere.png" alt-text="De optie HCX in vSphere" border="true":::

Voer het gebied voor **nood herstel** in en klik op **virtuele machines beveiligen**.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine.png" alt-text="Selecteer vm's beveiligen" border="true":::

Selecteer in het venster dat wordt geopend de bron en de externe sites, de externe site in dit geval moet de privécloud zijn.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machines.png" alt-text="venster Vm's beveiligen" border="true":::

Selecteer, indien nodig, de standaard opties voor replicatie:

- **Compressie inschakelen:** Aanbevolen voor scenario's met lage door voer.

- **Quiescence inschakelen:** Pauzeert de virtuele machine om ervoor te zorgen dat een consistente kopie wordt gesynchroniseerd met de externe site.

- **Doel opslag:** Selecteer de externe gegevens opslag voor de beveiligde virtuele machine (s). In een privécloud in de Cloud moet deze selectie het VSAN-gegevens opslag zijn.

- **Compute-container:** Het externe vSphere-cluster of de resource groep.

- **Doelmap:** De externe doelmap, deze instelling is optioneel en als er geen map is geselecteerd, worden de VM ('s) direct onder het geselecteerde cluster getempod.

- **RPO:** Deze waarde is het synchronisatie-interval tussen de virtuele bron machine en de beveiligde virtuele machine en kan vijf minuten tot 24 uur duren.

- **Interval voor moment opnamen:** Interval tussen moment opnamen.

- **Aantal moment opnamen:** Het totale aantal moment opnamen binnen het geconfigureerde interval voor moment opnamen.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine-options.png" alt-text="opties voor virtuele machines beveiligen" border="true":::

Selecteer een of meer virtuele machines in de lijst en configureer de virtuele machine als nodig de replicatie opties.

De virtuele machines nemen standaard het beleid voor globale instellingen over dat is geconfigureerd in de standaard-replicatie opties. Configureer voor elke netwerk interface in de geselecteerde virtuele machine de externe **netwerk poort groep** en selecteer **volt ooien** om het beveiligings proces te starten.

:::image type="content" source="./media/disaster-recovery/network-interface-options.png" alt-text="opties voor netwerk interface" border="true":::

Zoals in de volgende afbeelding wordt weer gegeven, kunt u het proces voor elk van de geselecteerde virtuele machines in hetzelfde nood herstel gebied bewaken.

:::image type="content" source="./media/disaster-recovery/protect-monitor-progress.png" alt-text="de voortgang van de beveiliging bewaken" border="true":::

Nadat de virtuele machine is beveiligd, kunnen de verschillende moment opnamen worden weer gegeven op het tabblad **moment opnamen** .

:::image type="content" source="./media/disaster-recovery/list-of-snapshots.png" alt-text="lijst met moment opnamen" border="true":::

De gele drie hoek betekent dat de moment opnamen en de virtuele schijf niet zijn getest in een test herstel bewerking.

Er zijn belang rijke verschillen tussen een uitgeschakelde virtuele machine en een VM die is ingeschakeld.
In de vorige scherm afbeelding ziet u het synchronisatie proces voor een ingeschakelde virtuele machine. Het synchronisatie proces is gestart totdat de eerste moment opname is voltooid, een volledige kopie van de virtuele machine en de volgende stappen in het geconfigureerde interval.

Voor een uitgeschakelde VM wordt een kopie gesynchroniseerd, waarna de VM wordt weer gegeven als inactieve en de beveiligings bewerking wordt weer gegeven als voltooid.

Wanneer de virtuele machine is ingeschakeld, wordt het synchronisatie proces voor de externe site gestart.

## <a name="complete-a-test-recover-of-virtual-machines"></a>Een test herstel van virtuele machines volt ooien

Meld u aan bij de **vSphere-client** op de externe site. Dit is de privécloud. Selecteer in de **HCX-invoeg toepassing**in het gebied nood herstel de verticale ellipsen op een wille keurige virtuele machine om het menu bewerkingen weer te geven. Selecteer **VM herstellen testen**.

:::image type="content" source="./media/disaster-recovery/test-recover-virtual-machine.png" alt-text="Selecteer VM herstellen testen" border="true":::

Selecteer in het nieuwe venster de opties voor de test. Selecteer de moment opname die u wilt gebruiken om verschillende statussen van de virtuele machine te testen.

:::image type="content" source="./media/disaster-recovery/choose-snapshot.png" alt-text="Kies een moment opname en klik op testen" border="true":::

Nadat u op **testen**hebt geklikt, wordt de herstel bewerking gestart.

Wanneer de bewerking voor het testen van de test is voltooid, kan de nieuwe virtuele machine worden gecontroleerd in de automatische AVS-Cloud-vCenter.

:::image type="content" source="./media/disaster-recovery/verify-test-recovery.png" alt-text="herstel bewerking controleren" border="true":::

Tot slot nadat het testen is uitgevoerd op de virtuele machine of een toepassing die erop wordt uitgevoerd, wordt het exemplaar van de test verwijderd.

:::image type="content" source="./media/disaster-recovery/cleanup-test-instance.png" alt-text="test exemplaar opschonen" border="true":::

## <a name="recover-virtual-machines"></a>Virtuele machines herstellen

Meld u aan bij de **vSphere-client** op de externe site, die de privécloud is en open de **HCX-invoeg toepassing**.

Voor het herstel scenario is dit een groep virtuele machines die worden gebruikt voor dit voor beeld.

Selecteer de virtuele machine die u wilt herstellen in de lijst, open het menu **acties** en selecteer **vm's herstellen**.

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines.png" alt-text="virtuele machines herstellen" border="true":::

Configureer de herstel opties voor elk exemplaar en klik op **herstellen** om de herstel bewerking te starten.

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines-confirm.png" alt-text="bevestiging van virtuele machines herstellen" border="true":::

Nadat de herstel bewerking is voltooid, worden de nieuwe virtuele machines weer gegeven in de inventarisatie van externe vCenter Server.

## <a name="complete-a-reverse-replication-on-virtual-machines"></a>Een omgekeerde replicatie voor virtuele machines volt ooien

Meld u aan bij de **vSphere-client** op uw AVS-privécloud en de toegang tot **HCX-invoeg toepassing**.
Het is vereist dat de oorspronkelijke virtuele machines op de bron site worden uitgeschakeld voordat u de omgekeerde replicatie start. De bewerking mislukt als de virtuele machines niet zijn uitgeschakeld.

Selecteer de virtuele machines die u wilt repliceren naar de bron site uit de lijst, open het menu **acties** en selecteer **omkeren**. Klik in het pop-upvenster op **terug** om de replicatie te starten.

:::image type="content" source="./media/disaster-recovery/reverse-operation-virtual-machines.png" alt-text="Selecteer omgekeerde actie onder beveiligde bewerkingen" border="true":::

De replicatie kan worden bewaakt in de sectie Details van elke virtuele machine.

:::image type="content" source="./media/disaster-recovery/review-reverse-operation.png" alt-text="de resultaten van omgekeerde actie controleren" border="true":::

## <a name="disaster-recovery-plan-automation"></a>Automation plan voor herstel na nood gevallen

VMware HCX heeft momenteel geen ingebouwd mechanisme voor het maken en automatiseren van een plan voor herstel na nood gevallen. Deze functie bestaat niet in HCX. Het biedt echter een set REST-Api's, inclusief Api's voor de bewerking voor herstel na nood geval.

De API-specificatie kan worden geopend in HCX manager in de URL.

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
