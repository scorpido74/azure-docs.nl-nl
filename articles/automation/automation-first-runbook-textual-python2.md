---
title: Mijn eerste Python-runbook in Azure Automation
description: Zelfstudie die u door het maken, testen en publiceren van een eenvoudig Python-runbook leidt.
services: automation
ms.subservice: process-automation
ms.date: 03/19/2019
ms.topic: conceptual
ms.openlocfilehash: b788e5bda65702305ed6f6b001b57c28939aa875
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405919"
---
# <a name="my-first-python-runbook"></a>Mijn eerste Python-runbook

> [!div class="op_single_selector"]
> - [Grafisch](automation-first-runbook-graphical.md)
> - [PowerShell](automation-first-runbook-textual-powershell.md)
> - [PowerShell-werkstroom](automation-first-runbook-textual.md)
> - [Python](automation-first-runbook-textual-python2.md)

In deze zelfstudie wordt u door het maken van een [Python-runbook](automation-runbook-types.md#python-runbooks) in Azure Automation geleid. Je begint met een eenvoudig runbook dat je test en publiceert. Vervolgens wijzigt u het runbook zodanig dat Azure-resources daadwerkelijk worden beheerd, in dit geval door een virtuele machine van Azure te starten. Ten slotte maakt u het runbook robuuster door runbook-parameters toe te voegen.

> [!NOTE]
> Het gebruik van een webhook om een Python-runbook te starten wordt niet ondersteund.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

- Azure-abonnement. Als je er nog geen hebt, kun je [je MSDN-abonneevoordeel activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of je aanmelden voor een [gratis account.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Automation-account](automation-offering-get-started.md) om het runbook te bevatten en te verifiëren voor Azure-resources. Dit account moet machtigingen hebben om de virtuele machine te starten en stoppen.
- Een virtuele machine van Azure. U stopt en start deze machine, dus het mag geen productiemachine zijn.

## <a name="create-a-new-runbook"></a>Een nieuw runbook maken

U begint met het maken van een eenvoudig runbook dat de tekst *Hello World*uitvoert.

1. Open uw Automation-account in Azure Portal.

    Op de Automation-accountpagina vindt u een beknopte weergave van de resources in dit account. U hebt als het goed is al enkele assets. De meeste van deze assets zijn de modules die automatisch zijn opgenomen in een nieuw Automation-account. Ook moet u de referentieasset hebben die wordt genoemd in de [vereisten](#prerequisites).<br>

1. Selecteer **Runbooks** onder **Procesautomatisering** om de lijst met runbooks te openen.
1. Selecteer **Een runbook toevoegen** om een nieuw runbook te maken.
1. Geef het runbook de naam **MyFirstRunbook-Python**.
1. Selecteer **Python 2** voor **runbook-type**.
1. Klik op **Maken** om het runbook te maken en de teksteditor te openen.

## <a name="add-code-to-the-runbook"></a>Code toevoegen aan het runbook

Nu voegt u een eenvoudige `Hello World`opdracht toe om de tekst af te drukken.

```python
print("Hello World!")
```

Klik **op Opslaan** om het runbook op te slaan.

## <a name="test-the-runbook"></a>Het runbook testen

Voordat u het runbook publiceert om het beschikbaar te maken in productie, wilt u het testen om er zeker van te zijn dat het goed werkt. Wanneer u een runbook test, voert u de conceptversie uit en geeft u de uitvoer interactief weer.

1. Klik op **Testvenster** om het testvenster te openen.
1. Klik op **Start** om de test te starten. Dit moet de enige ingeschakelde optie zijn.
1. Een [runbooktaak](automation-runbook-execution.md) wordt gemaakt en de status ervan wordt weergegeven.
   De taakstatus begint als Queued, wat aangeeft dat het wachten is op een runbook-werknemer in de cloud die beschikbaar is. Het wordt verplaatst naar Starten wanneer een werknemer de taak claimt en vervolgens Wordt uitgevoerd wanneer het runbook daadwerkelijk wordt uitgevoerd.
1. Wanneer de runbooktaak is voltooid, wordt de uitvoer ervan weergegeven. In dit geval moet `Hello World`je zien .
1. Sluit het testvenster om terug te gaan naar het papier.

## <a name="publish-and-start-the-runbook"></a>Het runbook publiceren en starten

Het runbook dat u hebt gemaakt, bevindt zich nog steeds in de modus Concept. U moet het publiceren voordat u het in productie uitvoeren.
Wanneer u een runbook publiceert, overschrijft u de bestaande gepubliceerde versie met de conceptversie.
In dit geval hebt u nog geen gepubliceerde versie omdat u zojuist het runbook hebt gemaakt.

1. Klik op **Publiceren** om het runbook te publiceren en klik vervolgens op **Ja** wanneer hierom wordt gevraagd.
1. Als u naar links scrolt om het runbook op de pagina **Runbooks weer te** geven, ziet u een status van **auteur** van **gepubliceerd .**
1. Scroll terug naar rechts om het deelvenster voor **MyFirstRunbook-Python**weer te geven.
   Met de opties aan de bovenkant u het runbook starten, het runbook bekijken of plannen om in de toekomst op een bepaald moment te beginnen.
2. Klik **op Start** en klik vervolgens op **OK** wanneer het blad Runbook start wordt geopend.
3. Er wordt een taakvenster geopend voor de runbook-taak die u hebt gemaakt. U dit deelvenster sluiten, maar laten we het openlaten, zodat u de voortgang van de taak bekijken.
1. De functiestatus wordt weergegeven in **Het overzicht van taken** en komt overeen met de statussen die u hebt gezien toen u het runbook testte.
2. Zodra de status van het runbook voltooid wordt weergegeven, klikt u op **Uitvoer**. Het deelvenster Uitvoer wordt geopend, `Hello World`waar u zien.
3. Sluit het deelvenster Uitvoer.
4. Klik op **Alle logboeken** om het deelvenster Streams voor de runbooktaak te openen. U moet `Hello World` alleen zien in de uitvoerstroom. In dit deelvenster kunnen echter andere streams voor een runbook-taak worden weergegeven, zoals Verbose en Error, als het runbook naar deze streams schrijft.
5. Sluit het deelvenster Streams en het deelvenster Taak om terug te keren naar het deelvenster MyFirstRunbook-Python.
6. Klik **op Vacatures** om de pagina Vacatures voor dit runbook te openen. Op deze pagina worden alle taken weergegeven die door dit runbook zijn gemaakt. U zou slechts één weergegeven taak moeten zien, aangezien de taak slechts eenmaal is uitgevoerd.
7. U kunt op deze taak klikken om hetzelfde taakvenster te openen dat u hebt bekeken toen u het runbook startte. Met dit deelvenster u teruggaan in de tijd en de details bekijken van een taak die is gemaakt voor een bepaalde runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Verificatie toevoegen om Azure-resources te beheren

U hebt het runbook getest en gepubliceerd, maar tot nu toe doet het nog niets nuttigs. U wilt dat er Azure-resources mee worden beheerd.
Hiervoor moet het script zich verifiëren met behulp van de referenties van uw Automatiseringsaccount. Om u te helpen, u het [hulpprogrammapakket azure automation](https://github.com/azureautomation/azure_automation_utility) gebruiken om het eenvoudiger te maken om Azure-bronnen te verifiëren en ermee te communiceren.

> [!NOTE]
> Het automatiseringsaccount moet zijn gemaakt met de hoofdfunctie service voor het uitvoeren van een run as-certificaat.
> Als uw Automatiseringsaccount niet is gemaakt met de serviceprincipal, u verifiëren zoals beschreven in [Authenticeren met de Azure Management Libraries for Python](/azure/python/python-sdk-azure-authenticate).

1. Open de tekstuele editor door te klikken op **Bewerken** in het deelvenster MyFirstRunbook-Python.

2. Voeg de volgende code toe om te verifiëren aan Azure:

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

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Code toevoegen om Python Compute-client te maken en de VM te starten

Als u met Azure VM's wilt werken, maakt u een instantie van de [Azure Compute-client voor Python.](https://docs.microsoft.com/python/api/azure-mgmt-compute/azure.mgmt.compute.computemanagementclient)

Gebruik de compute client om de VM te starten. Voeg de volgende code toe aan het runbook:

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

Waar `MyResourceGroup` is de naam van de resourcegroep `TestVM` die de VM bevat en is de naam van de VM die u wilt starten.

Test en voer het runbook opnieuw uit om te zien dat de VM wordt gestart.

## <a name="use-input-parameters"></a>Invoerparameters gebruiken

Het runbook gebruikt momenteel hardgecodeerde waarden voor de namen van de resourcegroep en de VM.
Laten we nu code toevoegen die deze waarden haalt uit invoerparameters.

U gebruikt `sys.argv` de variabele om de parameterwaarden te krijgen.
Voeg de volgende code toe aan `import` het runbook direct na de andere instructies:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Hiermee importeert u de `sys` module en worden twee variabelen gemaakt om de resourcegroep- en VM-namen vast te houden.
Merk op dat het element `sys.argv[0]`van de argumentlijst, is de naam van het script, en is niet ingevoerd door de gebruiker.

Nu u de laatste twee regels van de runbook wijzigen om de waarden van de invoerparameter te gebruiken in plaats van hardgecodeerde waarden te gebruiken:

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

Wanneer u een Python-runbook start (in het deelvenster Testen of als een gepubliceerd runbook), u de waarden voor parameters invoeren op de pagina Runbook starten onder **Parameters**.

Nadat u een waarde in het eerste vak hebt ingevoerd, verschijnt er een tweede, enzovoort, zodat u zoveel parameterwaarden invoeren als nodig is.

De waarden zijn beschikbaar voor `sys.argv` het script in de array zoals in de code die u zojuist hebt toegevoegd.

Voer de naam van uw resourcegroep in als de waarde voor de eerste parameter en de naam van de vm om te beginnen als de waarde van de tweede parameter.

![Parameterwaarden invoeren](media/automation-first-runbook-textual-python/runbook-python-params.png)

Klik op **OK** om het runbook te starten. Het runbook wordt uitgevoerd en start de door u opgegeven VM.

## <a name="error-handling-in-python"></a>Foutafhandeling in Python

U ook de volgende conventies gebruiken om verschillende streams uit uw Python-runbooks op te halen, waaronder WAARSCHUWING-, FOUT- en FOUTFOUTstreams.

```python
print("Hello World output") 
print("ERROR: - Hello world error")
print("WARNING: - Hello world warning")
print("DEBUG: - Hello world debug")
print("VERBOSE: - Hello world verbose")
```

In het volgende voorbeeld ziet `try...except` u deze conventie die in een blok wordt gebruikt.

```python
try:
    raise Exception('one', 'two')
except Exception as detail:
    print 'ERROR: Handling run-time error:', detail
```

> [!NOTE]
> Azure Automation biedt `sys.stderr`geen ondersteuning voor .

## <a name="next-steps"></a>Volgende stappen

- Zie [Mijn eerste PowerShell-runbook](automation-first-runbook-textual-powershell.md)om aan de slag te gaan met PowerShell-runbooks.
- Zie Mijn eerste grafische runbook om aan de slag te gaan met grafische [runbooks.](automation-first-runbook-graphical.md)
- Zie [Mijn eerste PowerShell-werkstroomrunboek](automation-first-runbook-textual.md)om aan de slag te gaan met PowerShell-werkstroom.
- Zie [Azure Automation runbook-typen](automation-runbook-types.md)voor meer informatie over runbook-typen, hun voordelen en beperkingen.
- Zie [Azure for Python-ontwikkelaars voor](/azure/python/)meer informatie over het ontwikkelen voor Azure met Python.
- Zie de [Azure Automation GitHub](https://github.com/azureautomation/runbooks/tree/master/Utility/Python)om voorbeeldvan Python 2-runbooks weer te geven.
