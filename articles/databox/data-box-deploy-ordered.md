---
title: Zelfstudie voor Azure Data Box bestellen | Microsoft Docs
description: Ontdek wat de implementatievereisten zijn en hoe u een Azure Data Box kunt bestellen
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: cfb95f2fb02544197f9b2796a705844e33eca201
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392473"
---
# <a name="tutorial-order-azure-data-box"></a>Zelfstudie: Azure Data Box bestellen

Azure Data Box is een hybride oplossing waarmee u uw on-premises gegevens snel, gemakkelijk en betrouwbaar in Azure kunt importeren. U zet uw gegevens op een opslagapparaat van 80 TB (bruikbare capaciteit) dat Microsoft naar u heeft verstuurd, en u stuurt het apparaat vervolgens terug. Deze gegevens worden vervolgens geüpload in Azure.

In deze zelfstudie wordt beschreven hoe u een Azure Data Box bestelt. In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
>
> * Vereisten om Data Box te implementeren
> * Een Data Box bestellen
> * De bestelling volgen
> * De bestelling annuleren

## <a name="prerequisites"></a>Vereisten

# <a name="portal"></a>[Portal](#tab/portal)

Zorg dat u aan de volgende configuratievereisten voor de Data Box-service en het apparaat voldoet voordat u het apparaat implementeert:

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

U kunt u aanmelden bij Azure en Azure CLI-opdrachten uitvoeren. Dit kan op twee manieren:

* U kunt de CLI installeren en CLI-opdrachten lokaal uitvoeren.
* U kunt CLI-opdrachten uitvoeren vanuit Azure Portal, in Azure Cloud Shell.

Voor de zelfstudie gebruiken we Azure CLI via Windows PowerShell, maar het staat u vrij om een van beide opties te kiezen.

### <a name="install-the-cli-locally"></a>De CLI lokaal installeren

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (versie 2.0.67 of hoger) installeren. U kunt ook [installeren met behulp van MSI](https://aka.ms/installazurecliwindows).

#### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Open een Windows PowerShell-opdrachtvenster en meld u aan bij Azure met de opdracht [az login](/cli/azure/reference-index#az-login):

```azurecli
PS C:\Windows> az login
```

Dit is de uitvoer bij een geslaagde aanmelding:

```output
You have logged in. Now let us find all the subscriptions to which you have access.
[
   {
      "cloudName": "AzureCloud",
      "homeTenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "isDefault": true,
      "managedByTenants": [],
      "name": "My Subscription",
      "state": "Enabled",
      "tenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "user": {
          "name": "gusp@contoso.com",
          "type": "user"
      }
   }
]
```

#### <a name="install-the-azure-data-box-cli-extension"></a>De Azure Data Box CLI-extensie installeren

Voordat u de Azure Data Box CLI-opdrachten kunt gebruiken, moet u de extensie installeren. Azure CLI-extensies geven u toegang tot experimentele opdrachten en opdrachten in een evaluatieversie die nog niet zijn verzonden als onderdeel van de kern-CLI. Zie [Extensies gebruiken met Azure CLI](/cli/azure/azure-cli-extensions-overview) voor meer informatie over extensies.

Voer de volgende opdracht uit om de extensie voor Azure Data Box te installeren: `az extension add --name databox`:

```azurecli

    PS C:\Windows> az extension add --name databox
```

Als de extensie is geïnstalleerd, ziet u de volgende uitvoer:

```output
    The installed extension 'databox' is experimental and not covered by customer support. Please use with discretion.
    PS C:\Windows>

    # az databox help

    PS C:\Windows> az databox -h

    Group
        az databox

    Subgroups:
        job [Experimental] : Commands to manage databox job.

    For more specific examples, use: az find "az databox"

        Please let us know how we are doing: https://aka.ms/clihats
```

### <a name="use-azure-cloud-shell"></a>Azure Cloud Shell gebruiken

U kunt [Azure Cloud Shell](https://shell.azure.com/), een door Azure gehoste interactieve shell-omgeving, gebruiken via uw browser om CLI-opdrachten uit te voeren. Azure Cloud Shell biedt ondersteuning voor Bash of Windows PowerShell met Azure-services. De Azure CLI is vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Klik op de knop Cloud Shell in het menu in het gedeelte rechtsboven in Azure Portal:

![Cloud Shell](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

Met de knop start u een interactieve shell waarmee u alle stappen in dit instructieartikel kunt uitvoeren.

<!-- To start Azure Cloud Shell:

| Option | Example/Link |
|-----------------------------------------------|---|
| Select **Try It** in the upper-right corner of a code block. Selecting **Try It** doesn't automatically copy the code to Cloud Shell. | ![Example of Try It for Azure Cloud Shell](../../includes/media/cloud-shell-try-it/hdi-azure-cli-try-it.png) |
| Go to [https://shell.azure.com](https://shell.azure.com), or select the **Launch Cloud Shell** button to open Cloud Shell in your browser. | [![Launch Cloud Shell in a new window](../../includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com) |
| Select the **Cloud Shell** button on the menu bar at the upper right in the [Azure portal](https://portal.azure.com). | ![Cloud Shell button in the Azure portal](../../includes/media/cloud-shell-try-it/hdi-cloud-shell-menu.png) |

To run the code in this article in Azure Cloud Shell:

1. Start Cloud Shell.

2. Select the **Copy** button on a code block to copy the code.

3. Paste the code into the Cloud Shell session by selecting **Ctrl**+**Shift**+**V** on Windows and Linux or by selecting **Cmd**+**Shift**+**V** on macOS.

4. Select **Enter** to run the code.

For this tutorial, we use Windows PowerShell command prompt to run Azure CLI commands. -->

<!-- This goes away, we'll show this later when we show how to order a Data Box. -->
<!-- ## Change the output format type

All Azure CLI commands will use json as the output format by default unless you change it. You can change the output format by using the global parameter `--output <output-format>`. -->

<!-- ```azurecli

az databox job <command> --output <output-format>

```

Azure Data Box CLI commands support the following output formats:

* json (default setting)
* jsonc
* table
* tsv
* yaml
* yamlc
* none

You can use the parameter `--output` with all Azure Data Box CLI commands. -->

<!-- To set the output format to yaml: -->

<!-- ```azurecli
PS C:\Windows>az databox job show --resource-group "myresourcegroup" --name "mydataboxorder" --output "yaml"

``` -->
<!-- 
To set the out format to tabular form (easier to read):

```azurecli
PS C:\Windows>az databox job show --resource-group "myresourcegroup" --name "mydataboxorder" --output "table"

``` -->

<!-- Here's the example output of `az databox job show` after changing the output format to table:

```azurecli
PS C:\WINDOWS\system32> az databox job show --resource-group "GDPTest" --name "mydataboxtest3" --output "table"
Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.

DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
--------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
NonScheduled    True             True                       False          True                         westus      mydataboxorder  myresourcegroup          2020-06-11T22:05:49.436622+00:00  DeviceOrdered

``` -->

---

## <a name="order-data-box"></a>Data Box bestellen

# <a name="portal"></a>[Portal](#tab/portal)

Voer de volgende stappen uit in Azure Portal om een apparaat te bestellen.

1. Gebruik uw Microsoft Azure-referenties om u aan te melden op deze URL: [https://portal.azure.com](https://portal.azure.com).
2. Klik op **+ Een resource maken** en zoek naar *Azure Data Box*. Klik op **Azure Data Box**.

   [![Zoek naar Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Klik op **Create**.

4. Controleer of de Data Box-service beschikbaar is in uw regio. Voer de volgende gegevens in of selecteer deze en selecteer **Toepassen**.

    |Instelling  |Waarde  |
    |---------|---------|
    |Abonnement     | Selecteer een EA-, CSP- of Azure Sponsorship-abonnement voor de Data Box-service. <br> Het abonnement is gekoppeld aan uw factureringsrekening.       |
    |Type overdracht     | Selecteer **Importeren in Azure**.        |
    |Bronland/-regio    |    Selecteer het land/de regio waar uw gegevens zich momenteel bevinden.         |
    |Doel-Azure-regio     |     Selecteer de Azure-regio waarnaar u uw gegevens wilt overdragen.        |

5. Selecteer **Data Box**. De maximale bruikbare capaciteit voor één bestelling is 80 TB. U kunt meerdere bestellingen doen voor grotere gegevensgrootten.

      [![Selecteer Data Box optie 1](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

6. Voer in **Bestellen** de **Orderdetails** in. Voer de volgende gegevens in of selecteer deze en selecteer **Volgende**.

    |Instelling  |Waarde  |
    |---------|---------|
    |Naam     |  Geef een beschrijvende naam op om de bestelling te volgen. <br> De naam kan tussen 3 en 24 tekens bevatten (letters, cijfers en afbreekstreepjes). <br> De naam moet beginnen en eindigen met een letter of cijfer.      |
    |Resourcegroep     |    Gebruik een bestaande of maak een nieuwe. <br> Een resourcegroep is een logische container voor resources die samen kunnen worden beheerd of geïmplementeerd.         |
    |Doel-Azure-regio     | Selecteer een regio voor uw opslagaccount. <br> Ga naar [Beschikbaarheid in de regio](data-box-overview.md#region-availability) voor meer informatie.        |
    |Opslaglocatie     | Kies een opslagaccount, beheerde schijven of beide. <br> Selecteer een of meer opslagaccounts in de gefilterde lijst van een bestaand opslagaccount, gebaseerd op de opgegeven Azure-regio. Data Box kan worden gekoppeld aan maximaal 10 opslagaccounts. <br> U kunt ook een nieuw account van het type **Algemeen gebruik v1**, **Algemeen gebruik v2** of **Blob-opslag** maken. <br>Opslagaccounts met virtuele netwerken worden ondersteund. Als u wilt dat de Data Box-service kan werken met beveiligde opslagaccounts, schakelt u in de firewallinstellingen van het opslagaccount de vertrouwde services in. Zie [Azure Data Box toevoegen als een vertrouwde service](../storage/common/storage-network-security.md#exceptions) voor meer informatie.|

    Als u een opslagaccount selecteert als de opslaglocatie, ziet u het volgende scherm:

    ![Data Box-bestelling voor opslagaccount](media/data-box-deploy-ordered/order-storage-account.png)

    Als u Data Box gebruikt om beheerde schijven te maken op basis van de on-premises VHD's (virtuele harde schijven), moet u ook de volgende informatie opgeven:

    |Instelling  |Waarde  |
    |---------|---------|
    |Resourcegroepen     | Maak nieuwe resourcegroepen als u beheerde schijven wilt maken van on-premises virtuele harde schijven. U kunt alleen een bestaande resourcegroep gebruiken als de resourcegroep eerder is gemaakt en beschikbaar was op het moment van het plaatsen van een Data Box-bestelling voor een beheerde schijf door de Data Box-service. <br> U kunt meerdere resourcegroepen opgeven door de namen te scheiden met een puntkomma. Er worden maximaal tien resourcegroepen ondersteund.|

    ![Data Box-bestelling voor beheerde schijf](media/data-box-deploy-ordered/order-managed-disks.png)

    Het opslagaccount dat is opgegeven voor beheerde schijven wordt gebruikt als een opslagaccount waarin de gegevens worden klaargezet. De Data Box-service uploadt de virtuele harde schijven als pagina-blobs naar dit opslagaccount waarna de schijven worden omgezet in beheerde schijven en naar de resourcegroepen worden verplaatst. Zie [Uploaden van gegevens naar Azure controleren](data-box-deploy-picked-up.md#verify-data-upload-to-azure) voor meer informatie.

7. Bij **Verzendadres** geeft u uw voor- en achternaam, de naam en het postadres van het bedrijf en een geldig telefoonnummer op. Klik op **Adres valideren**. De service controleert of de service beschikbaar is voor de regio van het verzendadres. Als de service beschikbaar is voor het opgegeven verzendadres, ontvangt u daarover een melding.

   Als u zelfbeheerde verzending hebt geselecteerd, ontvangt u een e-mailmelding nadat de bestelling is geplaatst. Zie [Zelfbeheerde verzending gebruiken](data-box-portal-customer-managed-shipping.md) voor meer informatie over zelfbeheerde verzendingen.

8. Klik op **Volgende** zodra de verzendgegevens zijn gevalideerd.

9. In de **Meldingsdetails** geeft u een e-mailadres op. De service stuurt e-mailmeldingen naar het opgegeven e-mailadres over updates van de bestelstatus.

    We raden u aan een e-mailadres van een groep te gebruiken, zodat u meldingen blijft ontvangen als een beheerder de groep verlaat.

10. Bekijk de gegevens met betrekking tot de bestelling, het contact, de meldingen en de privacyvoorwaarden in **Samenvatting**. Vink het selectievakje aan waarmee u akkoord gaat met de privacyvoorwaarden.

11. Klik op **Bestellen**. Het duurt een paar minuten voordat de bestelling is gemaakt.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Voer de volgende stappen uit met behulp van Azure CLI om een apparaat te bestellen:

1. Noteer uw instellingen voor uw Data Box-bestelling. Deze instellingen omvatten uw persoonlijke/zakelijke gegevens, abonnementsnaam, apparaatgegevens en verzendgegevens. U moet deze instellingen als parameters gebruiken bij het uitvoeren van de CLI-opdracht om de Data Box-bestelling te maken. In de volgende tabel ziet u de parameterinstellingen die worden gebruikt voor `az databox job create`:

   | Instelling (parameter) | Beschrijving |  Voorbeeldwaarde |
   |---|---|---|
   |resource-group| Gebruik een bestaande of maak een nieuwe. Een resourcegroep is een logische container voor resources die samen kunnen worden beheerd of geïmplementeerd. | "myresourcegroup"|
   |naam| De naam van de bestelling die u maakt. | "mydataboxorder"|
   |contact-name| De naam die is gekoppeld aan het verzendadres. | "Gus Poland"|
   |telefoon| Het telefoonnummer van de persoon die of het bedrijf dat de bestelling zal ontvangen.| "14255551234"
   |location| De dichtstbijzijnde Azure-regio voor u waaruit uw apparaat wordt verzonden.| "US - west"|
   |sku| Het specifieke Data Box-apparaat dat u bestelt. Geldige waarden zijn: "DataBox", "DataBoxDisk" en "DataBoxHeavy"| "DataBox" |
   |email-list| De e-mailadressen die zijn gekoppeld aan de bestelling.| "gusp@contoso.com" |
   |street-address1| Het adres waarnaar de bestelling wordt verzonden. | "15700 NE 39th St" |
   |street-address2| De secundaire adresgegevens, zoals het appartementnummer of het gebouwnummer. | "Bld 123" |
   |city| De plaats waarnaar het apparaat wordt verzonden. | "Redmond" |
   |state-or-province| De staat/provincie waarnaar het apparaat wordt verzonden.| "WA" |
   |country| Het land waarnaar het apparaat wordt verzonden. | "United States" |
   |postal-code| De postcode die is gekoppeld aan het verzendadres.| "98052"|
   |company-name| De naam van het bedrijf waarvoor u werkt.| "Contoso, LTD" |
   |opslagaccount| Het Azure Storage account waaruit u gegevens wilt importeren.| "mystorageaccount"|
   |debug| Informatie over foutopsporing opnemen in uitgebreide logboekregistratie  | --debug |
   |help| Help-informatie weergeven voor deze opdracht. | --help -h |
   |only-show-errors| Alleen fouten weergeven, waarschuwingen onderdrukken. | --only-show-errors |
   |output -o| Hiermee stelt u de uitvoerindeling in.  Toegestane waarden: json, jsonc, none, table, tsv, yaml, yamlc. De standaardwaarde is json. | --output "json" |
   |query| De JMESPath-querytekenreeks. Zie [JMESPath](http://jmespath.org/) voor meer informatie. | --query <string>|
   |verbose| Uitgebreide logboekregistratie opnemen. | --verbose |

2. Gebruik in de opdrachtprompt van uw keuze of terminal de opdracht [az databox job create](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-create) om uw Azure Data Box-bestelling te maken.

   ```azurecli
   az databox job create --resource-group <resource-group> --name <order-name> --location <azure-location> --sku <databox-device-type> --contact-name <contact-name> --phone <phone-number> --email-list <email-list> --street-address1 <street-address-1> --street-address2 <street-address-2> --city "contact-city" --state-or-province <state-province> --country <country> --postal-code <postal-code> --company-name <company-name> --storage-account "storage-account"
   ```

   Hier volgt een voorbeeld van het gebruik van de opdracht:

   ```azurecli
   az databox job create --resource-group "myresourcegroup" \
                         --name "mydataboxtest3" \
                         --location "westus" \
                         --sku "DataBox" \
                         --contact-name "Gus Poland" \
                         --phone "14255551234" \
                         --email-list "gusp@contoso.com" \
                         --street-address1 "15700 NE 39th St" \
                         --street-address2 "Bld 25" \
                         --city "Redmond" \
                         --state-or-province "WA" \
                         --country "US" \
                         --postal-code "98052" \
                         --company-name "Contoso" \
                         --storage-account mystorageaccount
   ```

   Dit is de uitvoer van het uitvoeren van de opdracht:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   {
     "cancellationReason": null,
     "deliveryInfo": {
        "scheduledDateTime": "0001-01-01T00:00:00+00:00"
   },
   "deliveryType": "NonScheduled",
   "details": null,
   "error": null,
   "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.DataBox/jobs/mydataboxtest3",
   "identity": {
     "type": "None"
   },
   "isCancellable": true,
   "isCancellableWithoutFee": true,
   "isDeletable": false,
   "isShippingAddressEditable": true,
   "location": "westus",
   "name": "mydataboxtest3",
   "resourceGroup": "myresourcegroup",
   "sku": {
     "displayName": null,
     "family": null,
     "name": "DataBox"
   },
   "startTime": "2020-06-10T23:28:27.354241+00:00",
   "status": "DeviceOrdered",
   "tags": {},
   "type": "Microsoft.DataBox/jobs"

   }
   PS C:\Windows>

   ```

3. Alle Azure CLI-opdrachten gebruiken standaard json als de uitvoerindeling, tenzij u deze wijzigt. U kunt de uitvoerindeling wijzigen met behulp van de algemene parameter `--output <output-format>`. Als u de indeling wijzigt in 'tabel', is de uitvoer beter leesbaar.

   Dit is dezelfde opdracht die we zojuist hebben uitgevoerd met een kleine aanpassing om de opmaak te wijzigen:

    ```azurecli
    az databox job create --resource-group "myresourcegroup" --name "mydataboxtest4" --location "westus" --sku "DataBox" --contact-name "Gus Poland" --phone "14255551234" --email-list "gusp@contoso.com" --street-address1 "15700 NE 39th St" --street-address2 "Bld 25" --city "Redmond" --state-or-province "WA" --country "US" --postal-code "98052" --company-name "Contoso" --storage-account mystorageaccount --output "table"
   ```

   Dit is de uitvoer van het uitvoeren van de opdracht:

   ```output

    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered

    ```

---

## <a name="track-the-order"></a>De bestelling volgen

# <a name="portal"></a>[Portal](#tab/portal)

Nadat u uw bestelling hebt geplaatst, kunt u de status van de bestelling volgen via de Azure-portal. Ga naar uw Data Box-bestelling en vervolgens naar **Overzicht** om de status te bekijken. In de portal ziet u dat de bestelling de status **Besteld** heeft.

Als het apparaat niet beschikbaar is, ontvangt u een melding. Als het apparaat wel beschikbaar is, identificeert Microsoft het apparaat dat moet worden verzonden en bereidt Microsoft de verzending voor. Tijdens de voorbereiding van het apparaat vinden de volgende acties plaats:

* Voor elk opslagaccount dat aan het apparaat is gekoppeld, worden SMB-shares gemaakt.
* Voor elke share worden toegangsreferenties zoals een gebruikersnaam en wachtwoord gegenereerd.
* Er wordt ook een apparaatwachtwoord gegenereerd om het apparaat mee te ontgrendelen.
* De Data Box wordt vergrendeld om onbevoegde toegang tot het apparaat te voorkomen.

Wanneer de apparaatvoorbereiding is voltooid, wordt de bestelling in de portal weergegeven met de status **Verwerkt**.

![Data Box-bestelling verwerkt](media/data-box-overview/data-box-order-status-processed.png)

Microsoft bereidt de verzending vervolgens voor en verzendt het apparaat met een regionale vervoerder. U ontvangt uw volgnummer zodra het apparaat is verzonden. In de portal wordt bestelling weergegeven met de status **Verzonden**.

![Data Box-bestelling verzonden](media/data-box-overview/data-box-order-status-dispatched.png)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

### <a name="track-a-single-order"></a>Eén bestelling volgen

Als u informatie wilt ophalen over een enkele, bestaande Azure Data Box-bestelling, voert u [az databox job show](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-show) uit. De opdracht geeft informatie weer over de bestelling, zoals, maar niet beperkt tot: naam, resourcegroep, traceringsinformatie, abonnements-ID, contactgegevens, type verzending en apparaat-SKU.

   ```azurecli
   az databox job show --resource-group <resource-group> --name <order-name>
   ```

   In de volgende tabel ziet u de parameterinformatie voor `az databox job show`:

   | Parameter | Beschrijving |  Voorbeeldwaarde |
   |---|---|---|
   |resource-group [vereist]| De naam van de resourcegroep die is gekoppeld aan de bestelling. Een resourcegroep is een logische container voor resources die samen kunnen worden beheerd of geïmplementeerd. | "myresourcegroup"|
   |name [vereist]| De naam van de bestelling die moet worden weergegeven. | "mydataboxorder"|
   |debug| Informatie over foutopsporing opnemen in uitgebreide logboekregistratie | --debug |
   |help| Help-informatie weergeven voor deze opdracht. | --help -h |
   |only-show-errors| Alleen fouten weergeven, waarschuwingen onderdrukken. | --only-show-errors |
   |output -o| Hiermee stelt u de uitvoerindeling in.  Toegestane waarden: json, jsonc, none, table, tsv, yaml, yamlc. De standaardwaarde is json. | --output "json" |
   |query| De JMESPath-querytekenreeks. Zie [JMESPath](http://jmespath.org/) voor meer informatie. | --query <string>|
   |verbose| Uitgebreide logboekregistratie opnemen. | --verbose |

   Hier volgt een voorbeeld van de opdracht met de uitvoerindeling ingesteld op 'tabel':

   ```azurecli
    PS C:\WINDOWS\system32> az databox job show --resource-group "myresourcegroup" \
                                                --name "mydataboxtest4" \
                                                --output "table"
   ```

   Dit is de uitvoer van het uitvoeren van de opdracht:

   ```output
    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   ```

> [!NOTE]
> Weergeven van de bestelling kan worden ondersteund op abonnementsniveau en dat maakt de resourcegroep tot een optionele parameter (in plaats van een vereiste parameter).

### <a name="list-all-orders"></a>Alle bestellingen weergeven

Als u meerdere apparaten hebt besteld, kunt u [az databox job list](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-list) uitvoeren om al uw Azure Data Box-bestellingen weer te geven. Met de opdracht wordt een lijst weergegeven met alle bestellingen die deel uitmaken van een specifieke resourcegroep. Het volgende wordt ook weergegeven in de uitvoer: naam van bestelling, status van verzending, Azure-regio, leveringstype, status van bestelling. Geannuleerde orders worden ook opgenomen in de lijst.
De opdracht geeft ook tijdstempels van elke bestelling weer.

```azurecli
az databox job list --resource-group <resource-group>
```

In de volgende tabel ziet u de parameterinformatie voor `az databox job list`:

   | Parameter | Beschrijving |  Voorbeeldwaarde |
   |---|---|---|
   |resource-group [vereist]| De naam van de resourcegroep die de bestellingen bevat. Een resourcegroep is een logische container voor resources die samen kunnen worden beheerd of geïmplementeerd. | "myresourcegroup"|
   |debug| Informatie over foutopsporing opnemen in uitgebreide logboekregistratie | --debug |
   |help| Help-informatie weergeven voor deze opdracht. | --help -h |
   |only-show-errors| Alleen fouten weergeven, waarschuwingen onderdrukken. | --only-show-errors |
   |output -o| Hiermee stelt u de uitvoerindeling in.  Toegestane waarden: json, jsonc, none, table, tsv, yaml, yamlc. De standaardwaarde is json. | --output "json" |
   |query| De JMESPath-querytekenreeks. Zie [JMESPath](http://jmespath.org/) voor meer informatie. | --query <string>|
   |verbose| Uitgebreide logboekregistratie opnemen. | --verbose |

   Hier volgt een voorbeeld van de opdracht met de uitvoerindeling ingesteld op 'tabel':

   ```azurecli
    PS C:\WINDOWS\system32> az databox job list --resource-group "GDPTest" --output "table"
   ```

   Dit is de uitvoer van het uitvoeren van de opdracht:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   CancellationReason                                               DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name                 ResourceGroup    StartTime                         Status
   ---------------------- ----------------------------------------  --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  -------------------  ---------------  --------------------------------  -------------
   OtherReason This was a test order for documentation purposes.    NonScheduled    False            False                      True           False                        westus      gdpImportTest        GDPTest          2020-05-26T23:20:57.464075+00:00  Cancelled
   NoLongerNeeded This order was created for documentation purposes.NonScheduled    False            False                      True           False                        westus      mydataboxExportTest  GDPTest          2020-05-27T00:04:16.640397+00:00  Cancelled
   IncorrectOrder                                                   NonScheduled    False            False                      True           False                        westus      mydataboxtest2       GDPTest          2020-06-10T16:54:23.509181+00:00  Cancelled
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest3       GDPTest          2020-06-11T22:05:49.436622+00:00  DeviceOrdered
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest4       GDPTest          2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   PS C:\WINDOWS\system32>
   ```

---

## <a name="cancel-the-order"></a>De bestelling annuleren

# <a name="portal"></a>[Portal](#tab/portal)

Als u deze bestelling wilt annuleren, gaat u in de Azure-portal naar **Overzicht** en selecteert u **Annuleren** in de opdrachtbalk.

Nadat u een bestelling hebt geplaatst, kunt u deze op elk moment annuleren voordat de status op Verwerkt is ingesteld.

Als u een geannuleerde bestelling wilt verwijderen, gaat u naar **Overzicht** en selecteert u **Verwijderen** in de opdrachtbalk.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

### <a name="cancel-an-order"></a>Een order annuleren

Als u een Azure Data Box-bestelling wilt annuleren, voert u [az databox job cancel](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-cancel) uit. U moet een reden opgeven voor het annuleren van de bestelling.

   ```azurecli
   az databox job cancel --resource-group <resource-group> --name <order-name> --reason <cancel-description>
   ```

   In de volgende tabel ziet u de parameterinformatie voor `az databox job cancel`:

   | Parameter | Beschrijving |  Voorbeeldwaarde |
   |---|---|---|
   |resource-group [vereist]| De naam van de resourcegroep die is gekoppeld aan de bestelling die moet worden verwijderd. Een resourcegroep is een logische container voor resources die samen kunnen worden beheerd of geïmplementeerd. | "myresourcegroup"|
   |name [vereist]| De naam van de bestelling die moet worden verwijderd. | "mydataboxorder"|
   |reason [vereist]| De reden voor het annuleren van de bestelling. | "Ik heb onjuiste informatie ingevoerd en wil de bestelling annuleren." |
   |ja| Niet vragen om bevestiging. | --yes (-y)| --yes -y |
   |debug| Informatie over foutopsporing opnemen in uitgebreide logboekregistratie | --debug |
   |help| Help-informatie weergeven voor deze opdracht. | --help -h |
   |only-show-errors| Alleen fouten weergeven, waarschuwingen onderdrukken. | --only-show-errors |
   |output -o| Hiermee stelt u de uitvoerindeling in.  Toegestane waarden: json, jsonc, none, table, tsv, yaml, yamlc. De standaardwaarde is json. | --output "json" |
   |query| De JMESPath-querytekenreeks. Zie [JMESPath](http://jmespath.org/) voor meer informatie. | --query <string>|
   |verbose| Uitgebreide logboekregistratie opnemen. | --verbose |

   Hier volgt een voorbeeld van de opdracht met uitvoer:

   ```azurecli
   PS C:\Windows> az databox job cancel --resource-group "myresourcegroup" --name "mydataboxtest3" --reason "Our budget was slashed due to **redacted** and we can no longer afford this device."
   ```

   Dit is de uitvoer van het uitvoeren van de opdracht:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   Are you sure you want to perform this operation? (y/n): y
   PS C:\Windows>
   ```

### <a name="delete-an-order"></a>Een bestelling verwijderen

Als u een Azure Data Box-bestelling hebt geannuleerd, kunt u [az databox job delete](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-delete) uitvoeren om de bestelling te verwijderen.

   ```azurecli
   az databox job delete --name [-n] <order-name> --resource-group <resource-group> [--yes] [--verbose]
   ```

   In de volgende tabel ziet u de parameterinformatie voor `az databox job delete`:

   | Parameter | Beschrijving |  Voorbeeldwaarde |
   |---|---|---|
   |resource-group [vereist]| De naam van de resourcegroep die is gekoppeld aan de bestelling die moet worden verwijderd. Een resourcegroep is een logische container voor resources die samen kunnen worden beheerd of geïmplementeerd. | "myresourcegroup"|
   |name [vereist]| De naam van de bestelling die moet worden verwijderd. | "mydataboxorder"|
   |abonnement| De naam of ID (GUID) van uw Azure-abonnement. | "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" |
   |ja| Niet vragen om bevestiging. | --yes (-y)| --yes -y |
   |debug| Informatie over foutopsporing opnemen in uitgebreide logboekregistratie | --debug |
   |help| Help-informatie weergeven voor deze opdracht. | --help -h |
   |only-show-errors| Alleen fouten weergeven, waarschuwingen onderdrukken. | --only-show-errors |
   |output -o| Hiermee stelt u de uitvoerindeling in.  Toegestane waarden: json, jsonc, none, table, tsv, yaml, yamlc. De standaardwaarde is json. | --output "json" |
   |query| De JMESPath-querytekenreeks. Zie [JMESPath](http://jmespath.org/) voor meer informatie. | --query <string>|
   |verbose| Uitgebreide logboekregistratie opnemen. | --verbose |

Hier volgt een voorbeeld van de opdracht met uitvoer:

   ```azurecli
   PS C:\Windows> az databox job delete --resource-group "myresourcegroup" --name "mydataboxtest3" --yes --verbose
   ```

   Dit is de uitvoer van het uitvoeren van de opdracht:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   command ran in 1.142 seconds.
   PS C:\Windows>
   ```

---

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende artikelen met betrekking tot Azure Data Box, zoals:

> [!div class="checklist"]
>
> * Vereisten om Data Box te implementeren
> * Data Box bestellen
> * De bestelling volgen
> * De bestelling annuleren

Ga naar de volgende zelfstudie om te lezen hoe u uw Data Box instelt.

> [!div class="nextstepaction"]
> [Uw Azure Data Box instellen](./data-box-deploy-set-up.md)
