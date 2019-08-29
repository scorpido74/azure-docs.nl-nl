---
title: Mijn eerste python-runbook in Azure Automation
description: Zelf studie die u helpt bij het maken, testen en publiceren van een eenvoudig python-runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/19/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6808ad101a828708ff4fe77bb4b8a55cf8c5fec7
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135432"
---
# <a name="my-first-python-runbook"></a>Mijn eerste python-runbook

> [!div class="op_single_selector"]
> - [Grafisch](automation-first-runbook-graphical.md)
> - [PowerShell](automation-first-runbook-textual-powershell.md)
> - [PowerShell-werkstroom](automation-first-runbook-textual.md)
> - [Python](automation-first-runbook-textual-python2.md)

In deze zelf studie wordt u begeleid bij het maken van een [python-runbook](automation-runbook-types.md#python-runbooks) in azure Automation. U begint met een eenvoudig runbook dat u test en publiceert. Vervolgens wijzigt u het runbook zodanig dat Azure-resources daadwerkelijk worden beheerd, in dit geval door een virtuele machine van Azure te starten. Ten slotte maakt u het runbook betrouwbaarder door runbook-para meters toe te voegen.

> [!NOTE]
> Het gebruik van een webhook voor het starten van een python-runbook wordt niet ondersteund.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

- Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Automation-account](automation-offering-get-started.md) om het runbook te bevatten en te verifiëren voor Azure-resources. Dit account moet machtigingen hebben om de virtuele machine te starten en stoppen.
- Een virtuele machine van Azure. U stopt en start deze machine, dus het mag geen productiemachine zijn.

## <a name="create-a-new-runbook"></a>Een nieuw runbook maken

U begint met het maken van een eenvoudig runbook waarmee de tekst *Hallo wereld*worden uitgevoerd.

1. Open uw Automation-account in Azure Portal.

    Op de Automation-accountpagina vindt u een beknopte weergave van de resources in dit account. U hebt als het goed is al enkele assets. De meeste van deze assets zijn de modules die automatisch zijn opgenomen in een nieuw Automation-account. Ook moet u de referentieasset hebben die wordt genoemd in de [vereisten](#prerequisites).<br>

1. Selecteer **Runbooks** onder **Procesbeheer** om de lijst van runbooks te openen.
1. Selecteer **+ een Runbook toevoegen** om een nieuw runbook te maken.
1. Geef het runbook de naam *MyFirstRunbook-python*.
1. In dit geval gaat u een [python-runbook](automation-runbook-types.md#python-runbooks) maken, dus Selecteer **python 2** voor het **type runbook**.
1. Klik op **Maken** om het runbook te maken en de teksteditor te openen.

## <a name="add-code-to-the-runbook"></a>Code toevoegen aan het runbook

Nu voegt u een eenvoudige opdracht toe om de tekst "Hallo wereld" af te drukken:

```python
print("Hello World!")
```

Klik op **Opslaan** om het runbook op te slaan.

## <a name="test-the-runbook"></a>Het runbook testen

Voordat u het runbook publiceert om het beschikbaar te maken in productie, wilt u het testen om er zeker van te zijn dat het goed werkt. Wanneer u een runbook test, voert u de **concept**versie uit en geeft u de uitvoer interactief weer.

1. Klik op **Testvenster** om het testvenster te openen.
1. Klik op **Start** om de test te starten. Dit moet de enige ingeschakelde optie zijn.
1. Een [runbooktaak](automation-runbook-execution.md) wordt gemaakt en de status ervan wordt weergegeven.
   In eerste instantie is de taakstatus *In de wachtrij geplaatst*. Hiermee wordt aangegeven dat er wordt gewacht tot er in de cloud een runbook worker beschikbaar is. Het wordt verplaatst naar het *begin* wanneer een werk nemer de taak claimt en vervolgens *wordt uitgevoerd* wanneer het runbook daad werkelijk wordt uitgevoerd.
1. Wanneer de runbooktaak is voltooid, wordt de uitvoer ervan weergegeven. In dit geval ziet u *Hallo wereld*.
1. Sluit het testvenster om terug te gaan naar het papier.

## <a name="publish-and-start-the-runbook"></a>Het runbook publiceren en starten

Het runbook dat u hebt gemaakt, bevindt zich nog in de concept modus. U moet deze publiceren voordat u deze in productie kunt uitvoeren.
Wanneer u een runbook publiceert, overschrijft u de bestaande gepubliceerde versie met de concept versie.
In dit geval hebt u nog geen gepubliceerde versie omdat u het runbook zojuist hebt gemaakt.

1. Klik op **Publiceren** om het runbook te publiceren en klik vervolgens op **Ja** wanneer hierom wordt gevraagd.
1. Als u naar links schuift om het runbook nu weer te geven in het deel venster **Runbooks** , wordt de **ontwerp status** **gepubliceerd**weer gegeven.
1. Schuif terug naar rechts om het deel venster voor **MyFirstRunbook-python**weer te geven.
   Met de opties bovenaan kunnen we het runbook starten, het runbook weer geven of plannen dat het op een bepaald moment in de toekomst wordt gestart.
2. U wilt het runbook starten, dus klik op **Start** en klik vervolgens op **OK** wanneer de Blade runbook starten wordt geopend.
3. Er wordt een taak venster geopend voor de runbook-taak die u hebt gemaakt. U kunt dit deel venster sluiten, maar in dit geval laat u het geopend, zodat u de voortgang van de taak kunt bekijken.
1. De taak status wordt weer gegeven in **taak samenvatting** en komt overeen met de statussen die u hebt gezien tijdens het testen van het runbook.
2. Zodra voor het runbook de status *Voltooid* wordt weergegeven, klikt u op **Uitvoer**. Het deel venster uitvoer wordt geopend en u kunt uw *Hallo wereld*zien.
3. Sluit het deelvenster Uitvoer.
4. Klik op **Alle logboeken** om het deelvenster Streams voor de runbooktaak te openen. U zou alleen *Hallo wereld* moeten zien in de uitvoerstroom, maar er kunnen ook andere stromen voor een runbooktaak worden weergegeven, zoals Uitgebreid en Fout als hiernaar wordt geschreven met het runbook.
5. Sluit het deel venster streams en het taak venster om terug te gaan naar het deel venster MyFirstRunbook-python.
6. Klik op **Taken** om het deelvenster Taken voor dit runbook te openen. Hiermee worden alle taken weergegeven die met dit runbook zijn gemaakt. U zou slechts één weergegeven taak moeten zien, aangezien de taak slechts eenmaal is uitgevoerd.
7. U kunt op deze taak klikken om hetzelfde taakvenster te openen dat u hebt bekeken toen u het runbook startte. Hiermee kunt u teruggaan in de tijd en de details bekijken van elke taak die voor een bepaald runbook is gemaakt.

## <a name="add-authentication-to-manage-azure-resources"></a>Verificatie toevoegen voor het beheren van Azure-resources

U hebt het runbook getest en gepubliceerd, maar tot nu toe doet het nog niets nuttigs. U wilt dat er Azure-resources mee worden beheerd.
Om Azure-resources te beheren, moet het script worden geverifieerd met behulp van de referenties van uw Automation-account. Om u te helpen, kunt u het [Azure Automation Utility-pakket](https://github.com/azureautomation/azure_automation_utility) gebruiken om Azure-resources gemakkelijker te verifiëren en ermee te werken.

> [!NOTE]
> Het Automation-account moet zijn gemaakt met de Service Principal-functie om er een uit te voeren als certificaat.
> Als uw Automation-account niet is gemaakt met de Service-Principal, kunt u verifiëren met behulp van de methode die wordt beschreven bij [verificatie met de Azure-beheer bibliotheken voor python](/azure/python/python-sdk-azure-authenticate).

1. Open de tekst editor door te klikken op **bewerken** in het deel venster MyFirstRunbook-python.

2. Voeg de volgende code toe om te verifiëren bij Azure:

   ```python
   import os
   from azure.mgmt.compute import ComputeManagementClient
   import azure.mgmt.resource
   import automationassets

   def get_automation_runas_credential(runas_connection):
       from OpenSSL import crypto
       import binascii
       from msrestazure import azure_active_directory
       import adal

       # Get the Azure Automation RunAs service principal certificate
       cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
       pks12_cert = crypto.load_pkcs12(cert)
       pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())

       # Get run as connection information for the Azure Automation service principal
       application_id = runas_connection["ApplicationId"]
       thumbprint = runas_connection["CertificateThumbprint"]
       tenant_id = runas_connection["TenantId"]

       # Authenticate with service principal certificate
       resource ="https://management.core.windows.net/"
       authority_url = ("https://login.microsoftonline.com/"+tenant_id)
       context = adal.AuthenticationContext(authority_url)
       return azure_active_directory.AdalAuthentication(
       lambda: context.acquire_token_with_client_certificate(
               resource,
               application_id,
               pem_pkey,
               thumbprint)
       )

   # Authenticate to Azure using the Azure Automation RunAs service principal
   runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
   azure_credential = get_automation_runas_credential(runas_connection)
   ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Code toevoegen voor het maken van python Compute client en het starten van de VM

Als u met Azure-Vm's wilt werken, maakt u een exemplaar van de [Azure Compute-client voor python](https://docs.microsoft.com/python/api/azure.mgmt.compute.computemanagementclient?view=azure-python).

Gebruik de compute-client om de virtuele machine te starten. Voeg de volgende code toe aan het runbook:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start(
    "MyResourceGroup", "TestVM")
async_vm_start.wait()
```

Waarbij _MyResourceGroup_ de naam is van de resource groep die de virtuele machine bevat en _TestVM_ de naam is van de virtuele machine die u wilt starten.

Test het runbook en voer het opnieuw uit om te zien dat de virtuele machine wordt gestart.

## <a name="use-input-parameters"></a>Invoer parameters gebruiken

Het runbook maakt momenteel gebruik van hardcoded waarden voor de namen van de resource groep en de VM.
We gaan nu code toevoegen waarmee deze waarden worden opgehaald uit de invoer parameters.

U gebruikt de `sys.argv` variabele om de parameter waarden op te halen.
Voeg de volgende code toe aan het runbook direct na de `import` andere-instructies:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Hiermee wordt de `sys` module geïmporteerd en worden twee variabelen gemaakt voor het opslaan van de resource groep en VM-namen.
U ziet dat het element van de argumenten lijst `sys.argv[0]`,, de naam van het script en niet wordt ingevoerd door de gebruiker.

Nu kunt u de laatste twee regels van het runbook wijzigen om de invoer parameter waarden te gebruiken in plaats van hardcoded waarden te gebruiken:

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

Wanneer u een python-runbook start (op de **test** pagina of als een gepubliceerd runbook), kunt u de waarden voor para meters opgeven op de pagina **Runbook starten** onder **para meters**.

Wanneer u begint met het invoeren van een waarde in het eerste vak, wordt er een tweede weer gegeven, enzovoort, zodat u zo veel parameter waarden kunt invoeren als nodig is.

De waarden zijn beschikbaar voor het script als de `sys.argv` matrix in de code die u zojuist hebt toegevoegd.

Voer de naam van uw resource groep in als de waarde voor de eerste para meter en de naam van de virtuele machine die moet worden gestart als de waarde van de tweede para meter.

![Parameter waarden invoeren](media/automation-first-runbook-textual-python/runbook-python-params.png)

Klik op **OK** om het runbook te starten. Het runbook wordt uitgevoerd en start de virtuele machine die u hebt opgegeven.

## <a name="next-steps"></a>Volgende stappen

- Zie [Mijn eerste PowerShell-runbook](automation-first-runbook-textual-powershell.md) om aan de slag te gaan met PowerShell-runbooks
- Zie [Mijn eerste grafische runbook](automation-first-runbook-graphical.md) om aan de slag te gaan met grafische runbooks
- Zie [Mijn eerste PowerShell Workflow-runbook](automation-first-runbook-textual.md) om aan de slag te gaan met PowerShell Workflow-runbooks
- Zie [Azure Automation-runbooktypen](automation-runbook-types.md) voor meer informatie over runbooktypen, hun voordelen en beperkingen
- Zie [Azure voor python-ontwikkel aars](/azure/python/) voor meer informatie over het ontwikkelen van Azure met python.
- Als u voor beelden van python 2-runbooks wilt bekijken, raadpleegt u de [Azure Automation github](https://github.com/azureautomation/runbooks/tree/master/Utility/Python)
