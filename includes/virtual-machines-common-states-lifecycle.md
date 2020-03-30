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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175974"
---
Azure Virtual Machines (VM's) gaan door verschillende statussen die kunnen worden gecategoriseerd in *inrichtings-* en energiestatussen. *power* Het doel van dit artikel is om deze toestanden te beschrijven en specifiek te benadrukken wanneer klanten worden gefactureerd bijvoorbeeld gebruik. 

## <a name="power-states"></a>Energiestatussen

De energiestatus vertegenwoordigt de laatst bekende status van de VM.

![VM-powerstatediagram](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
In de volgende tabel vindt u een beschrijving van elke instantiestatus en wordt aangegeven of deze bijvoorbeeld wordt gefactureerd.

<table>
<tr>
<th>
Status
</th>
<th>
Beschrijving
</th>
<th>
Facturering van instantiegebruik
</th>
</tr>
<tr>
<td>
<p><b>Beginnen</b></p>
</td>
<td>
<p>VM wordt opgestart.</p>
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
<p>Normale werkstatus voor een virtuele machine</p>
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
<p>Dit is een overgangsstaat. Wanneer voltooid, wordt het weergegeven als **Gestopt**.</p>
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
<p>De VM is uitgeschakeld vanuit het besturingssysteem van de gast of met behulp van de PowerOff API's.</p>
<p>Hardware wordt nog steeds toegewezen aan de VM en het blijft op de host. </p>
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
<p>Overgangsstaat. Wanneer de VM is voltooid, wordt deze weergegeven als **Deallocated**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocating",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocating"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Niet gefactureerd&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Toewijzing ongedaan gemaakt</b></p>
</td>
<td>
<p>De VM is gestopt en uit de host verwijderd. </p>
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


&#42;Sommige Azure-bronnen, zoals schijven en netwerken, brengen kosten in rekening. Softwarelicenties op de instantie brengen geen kosten met zich mee.

## <a name="provisioning-states"></a>Voorzieningsstaten

Een inrichtingsstatus is de status van een door de gebruiker geïnitieerde, besturingsvlakbewerking op de VM. Deze statussen staan los van de energiestatus van een vm.

- **Maken** – VM-creatie.

- **Update** – werkt het model voor een bestaande VM bij. Sommige niet-modelwijzigingen in VM, zoals Start/Opnieuw starten, vallen ook onder de update.

- **Verwijderen** – VM verwijderen.

- **Deallocate** – is waar een VM wordt gestopt en verwijderd uit de host. Het deallocating van een VM wordt beschouwd als een update, dus het zal provisioning staten met betrekking tot bijwerken weer te geven.



Hier volgen de overgangsbewerkingsstaten nadat het platform een door de gebruiker geïnitieerde actie heeft geaccepteerd:

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
<p><b>Verwijderen</b></p>
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
<p><b>OS-provisioningstaten</b></p>
</td>
<td width="366">
<p>Als een VM is gemaakt met een OS-afbeelding en niet met een gespecialiseerde afbeelding, kunnen volgende substaten worden waargenomen:</p>
<p>1. <b>OSProvisioningProgress</b> &ndash; De VM wordt uitgevoerd en de installatie van gast-OS is in volle gang. <p /> 
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningInprogress",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning In progress"<br>
 }<br>
]</code><br>
<p>2. <b>OSProvisioningComplete</b> &ndash; Kortstondige staat. De VM schakelt snel over naar **Succes,** tenzij er extensies moeten worden geïnstalleerd. Het installeren van extensies kan tijd kosten. <br />
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningComplete",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning Complete"<br>
 }<br>
]</code><br>
<p><b>Opmerking:</b>OS Provisioning kan **overgaan** naar Mislukt als er een OS-fout optreedt of als het besturingssysteem niet op tijd wordt geïnstalleerd. Klanten worden gefactureerd voor de geïmplementeerde VM op de infrastructuur.</p>
</td>
</tr>
</table>


Zodra de bewerking is voltooid, wordt de VM overgezet naar een van de volgende statussen:

- **Geslaagd** – de door de gebruiker geïnitieerde acties zijn voltooid.

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

 

- **Mislukt** – vertegenwoordigt een mislukte bewerking. Raadpleeg de foutcodes voor meer informatie en mogelijke oplossingen.

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



## <a name="vm-instance-view"></a>VM-instantieweergave

De API voor instantieweergave biedt vm-statusgegevens. Zie de [API-documentatie voor virtuele machines - Instantieweergave](https://docs.microsoft.com/rest/api/compute/virtualmachines/instanceview) voor meer informatie.

Azure Resources explorer biedt een eenvoudige gebruikersinterface voor het weergeven van de vm-status: [Resource Explorer](https://resources.azure.com/).

Inprovisioning-statussen zijn zichtbaar op VM-eigenschappen en instantieweergave. Power states zijn beschikbaar in de voorbeeldweergave van VM. 

