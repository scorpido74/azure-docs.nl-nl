---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: include
ms.date: 08/09/2018
ms.author: vashan, cynthn, rajsqr
ms.custom: include file
ms.openlocfilehash: 57f557a812ec5e4eea75b76ca1394ca360a85d30
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "67175974"
---
Azure Virtual Machines (Vm's) passeren verschillende statussen die kunnen worden ingedeeld in *inrichting* en *energie* status. Het doel van dit artikel is om deze statussen te beschrijven en te markeren wanneer klanten worden gefactureerd voor het gebruik van een exemplaar. 

## <a name="power-states"></a>Energiestatussen

De energie status vertegenwoordigt de laatste bekende status van de virtuele machine.

![Energie status diagram van de VM](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
De volgende tabel bevat een beschrijving van elke instantie status en geeft aan of deze in rekening wordt gebracht voor het gebruik van exemplaren of niet.

<table>
<tr>
<th>
Status
</th>
<th>
Beschrijving
</th>
<th>
Facturering van exemplaar gebruik
</th>
</tr>
<tr>
<td>
<p><b>Ingang</b></p>
</td>
<td>
<p>De VM wordt opgestart.</p>
<code>"statuses": [<br>
   {<br>
      "code": "PowerState/starting",<br>
       "level": "Info",<br>
        "displayStatus": "VM starting"<br>
    }<br>
    ]</code><br>
</td>
<td>
<p><b>Niet gefactureerd</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Wordt uitgevoerd</b></p>
</td>
<td>
<p>Normale werk status voor een virtuele machine</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/running",<br>
 "level": "Info",<br>
 "displayStatus": "VM running"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Gefactureerd</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Stoppen</b></p>
</td>
<td>
<p>Dit is een overgangs status. Als deze functie is voltooid, wordt deze weer gegeven als **gestopt**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopping",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopping"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Gefactureerd</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Gestopt</b></p>
</td>
<td>
<p>De virtuele machine is afgesloten vanuit het gast besturingssysteem of met behulp van de uitgeschakeld-Api's.</p>
<p>Er wordt nog steeds hardware toegewezen aan de virtuele machine en deze blijft op de host. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopped",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopped"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Gefactureerde&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Vrijgeven</b></p>
</td>
<td>
<p>Overgangs status. Als de VM is voltooid, wordt deze weer gegeven als **opgeheven**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocating",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocating"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Niet-gefactureerde&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Toewijzing ongedaan gemaakt</b></p>
</td>
<td>
<p>De virtuele machine is gestopt en verwijderd van de host. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocated",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocated"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Niet gefactureerd</b></p>
</td>
</tr>
</tbody>
</table>


&#42;sommige Azure-resources, zoals schijven en netwerken, worden er kosten in rekening gebracht. Voor software licenties op het exemplaar worden geen kosten in rekening gebracht.

## <a name="provisioning-states"></a>Inrichtings status

Een inrichtings status is de status van een door de gebruiker geïnitieerde, besturings vlak bewerking op de virtuele machine. Deze statussen zijn gescheiden van de energie status van een virtuele machine.

- **Maken** : VM maken.

- **Update** : Hiermee wordt het model voor een bestaande virtuele machine bijgewerkt. Sommige niet-model wijzigingen voor de VM, zoals starten/opnieuw opstarten, vallen ook onder update.

- **Verwijderen** : VM verwijderen.

- **Toewijzing opheffen** : is de locatie waar een virtuele machine wordt gestopt en verwijderd van de host. Het ongedaan maken van de toewijzing van een virtuele machine wordt beschouwd als een update, zodat de inrichtings status van het bijwerken wordt weer gegeven.



Dit zijn de overgangs statussen nadat het platform een door de gebruiker gestarte actie heeft geaccepteerd:

<br>

<table>
<tbody>
<tr>
<td width="162">
<p><b>Staten</b></p>
</td>
<td width="366">
<p>Beschrijving</p>
</td>
</tr>
<tr>
<td width="162">
<p><b>Maken</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating",<br>
 "level": "Info",<br>
 "displayStatus": "Creating"<br>
 }</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Bijwerken</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/updating",<br>
 "level": "Info",<br>
 "displayStatus": "Updating"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Verwijder</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/deleting",<br>
 "level": "Info",<br>
 "displayStatus": "Deleting"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Inrichtings status van besturings systeem</b></p>
</td>
<td width="366">
<p>Als een virtuele machine wordt gemaakt met een installatie kopie van een besturings systeem en niet met een gespecialiseerde installatie kopie, kunnen de volgende Substatussen worden waargenomen:</p>
<p>1. <b>OSProvisioningInprogress</b> &ndash; de virtuele machine wordt uitgevoerd en de installatie van het gast besturingssysteem wordt uitgevoerd. <p /> 
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningInprogress",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning In progress"<br>
 }<br>
]</code><br>
<p>2. <b>OSProvisioningComplete</b> &ndash; korte status. De virtuele machine wordt snel overgezet naar **geslaagd** , tenzij er uitbrei dingen moeten worden geïnstalleerd. Het installeren van extensies kan enige tijd duren. <br />
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningComplete",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning Complete"<br>
 }<br>
]</code><br>
<p><b>Opmerking</b>: het inrichten van het besturings systeem kan worden overgezet naar **mislukt** als er een fout in het besturings systeem optreedt of als het besturings systeem niet op tijd wordt geïnstalleerd. Klanten worden gefactureerd voor de geïmplementeerde virtuele machine op de infra structuur.</p>
</td>
</tr>
</table>


Zodra de bewerking is voltooid, verandert de virtuele machine in een van de volgende statussen:

- **Geslaagd** : de door de gebruiker geïnitieerde acties zijn voltooid.

    ```
  "statuses": [ 
  {
     "code": "ProvisioningState/succeeded",
     "level": "Info",
     "displayStatus": "Provisioning succeeded",
     "time": "time"
  }
  ]
    ```

 

- **Mislukt** : geeft een mislukte bewerking aan. Raadpleeg de fout codes voor meer informatie en mogelijke oplossingen.

    ```
  "statuses": [
    {
      "code": "ProvisioningState/failed/InternalOperationError",
      "level": "Error",
      "displayStatus": "Provisioning failed",
      "message": "Operation abandoned due to internal error. Please try again later.",
      "time": "time"
    }
    ]
    ```



## <a name="vm-instance-view"></a>Weer gave van VM-instantie

De instantie weergave-API biedt informatie over de status van de virtuele machine. Zie de documentatie over de [virtual machines-instantie weergave-](https://docs.microsoft.com/rest/api/compute/virtualmachines/instanceview) API voor meer informatie.

Azure resource Explorer biedt een eenvoudige gebruikers interface voor het weer geven van de actieve status van de VM: [resource Explorer](https://resources.azure.com/).

De inrichtings statussen zijn zichtbaar in de VM-eigenschappen en de weer gave van exemplaren. Energie statussen zijn beschikbaar in de instantie weergave van de virtuele machine. 

