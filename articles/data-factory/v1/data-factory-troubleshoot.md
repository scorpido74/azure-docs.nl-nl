---
title: Problemen met Azure Data Factory oplossen
description: Meer informatie over het oplossen van problemen met het gebruik van Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.assetid: 38fd14c1-5bb7-4eef-a9f5-b289ff9a6942
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: anandsub
robots: noindex
ms.openlocfilehash: 81ae5c3c702108d854e4dfde93001d5c99875666
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931583"
---
# <a name="troubleshoot-data-factory-issues"></a>Problemen met Data Factory oplossen
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Azure Data Factory. 

In dit artikel vindt u tips voor problemen met problemen bij het gebruik van Azure Data Factory. In dit artikel worden niet alle mogelijke problemen vermeld bij het gebruik van de service, maar het bevat een aantal problemen en algemene tips voor het oplossen van problemen.   

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing
### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Fout: Het abonnement is niet geregistreerd voor gebruik van de naamruimte Microsoft.DataFactory
Als u dit foutbericht ontvangt, is de resourceprovider van Azure Data Factory niet geregistreerd op uw computer. Ga als volgt te werk:

1. Start Azure PowerShell.
2. Meld u aan bij uw Azure-account met de volgende opdracht.

    ```powershell
    Connect-AzAccount
    ```
3. Voer de volgende opdracht uit om de Azure Data Factory-provider te registreren.

    ```powershell        
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Probleem: Ongeautoriseerde fout bij het uitvoeren van een cmdlet gegevensfabriek
Waarschijnlijk gebruikt u een verkeerd Azure-account of -abonnement met de Azure PowerShell. Gebruik de volgende cmdlets om het juiste Azure-account en -abonnement te selecteren voor gebruik met de Azure PowerShell.

1. Connect-AzAccount - Gebruik de juiste gebruikersnaam en wachtwoord
2. Get-AzSubscription - Bekijk alle abonnementen voor het account.
3. Naam van &lt;het&gt; AzAbonnement-abonnement selecteren - Selecteer het juiste abonnement. Gebruik dezelfde fabriek die u gebruikt om een gegevensfabriek op de Azure-portal te maken.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Probleem: kan geen Instellingen voor Gegevensbeheer Gateway Express starten vanuit Azure-portal
Voor de snelle installatie voor Data Management Gateway is Internet Explorer vereist of een webbrowser die compatibel is met Microsoft ClickOnce. Als u de snelle installatie niet kunt starten, voert u een van de volgende handelingen uit:

* Gebruik Internet Explorer of een microsoft ClickOnce-compatibele webbrowser.

    Als u met Chrome werkt, gaat u naar de [Chrome Web Store](https://chrome.google.com/webstore/), zoekt u op het trefwoord 'ClickOnce', en kiest en installeert u een van de ClickOnce-extensies.

    Doe hetzelfde voor Firefox (installeer add-in). Klik op de knop Menu openen op de werkbalk (drie horizontale lijnen in de rechterbovenhoek). Klik vervolgens op Invoegtoepassingen, zoek op het trefwoord 'ClickOnce', kies een van de ClickOnce-extensies en installeer deze.
* Gebruik de koppeling **Handmatiginstellen** op hetzelfde blad in de portal. U gebruikt deze aanpak om het installatiebestand te downloaden en handmatig uit te voeren. Nadat de installatie is geslaagd, ziet u het dialoogvenster Gegevensbeheergatewayconfiguratie. Kopieer de **sleutel** uit het portalscherm en gebruik deze in Configuratiebeheer om de gateway handmatig bij de service te registreren.  

### <a name="problem-fail-to-connect-to-on-premises-sql-server"></a>Probleem: geen verbinding maken met on-premises SQL Server
Start **Data Management Gateway Configuration Manager** op de gatewaymachine en gebruik het tabblad **Probleemoplossing** om de verbinding met SQL Server vanaf de gatewaymachine te testen. Zie [Problemen met de gateway oplossen](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) voor tips over het oplossen van verbindings-/gatewaygerelateerde problemen.   

### <a name="problem-input-slices-are-in-waiting-state-for-ever"></a>Probleem: invoersegmenten staan voor altijd in de wachtstand
De plakjes kunnen worden in **wachten** staat als gevolg van verschillende redenen. Een van de gemeenschappelijke redenen is dat de **externe** eigenschap niet is ingesteld op **true**. Elke gegevensset die buiten het bereik van Azure Data Factory wordt geproduceerd, moet worden gemarkeerd met **externe** eigenschap. Deze eigenschap geeft aan dat de gegevens extern zijn en niet worden ondersteund door pijplijnen in de gegevensfabriek. De gegevenssegmenten worden gemarkeerd als **Gereed** wanneer de gegevens beschikbaar zijn in het desbetreffende archief.

Raadpleeg het volgende voorbeeld voor het gebruik van de eigenschap **external**. U optioneel **externeGegevens*** opgeven wanneer u extern ingesteld op true.

Raadpleeg het artikel [Datasets](data-factory-create-datasets.md) (Gegevenssets) voor meer informatie over deze eigenschap.

```json
{
  "name": "CustomerTable",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "MyLinkedService",
    "typeProperties": {
      "folderPath": "MyContainer/MySubFolder/",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      }
    }
  }
}
```

U kunt het probleem oplossen door de eigenschap **external** en de optionele sectie **externalData** toe te voegen aan de JSON-definitie van de invoertabel en de tabel vervolgens opnieuw te maken.

### <a name="problem-hybrid-copy-operation-fails"></a>Probleem: Hybride kopieerbewerking mislukt
Zie [Problemen met de gateway oplossen](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) voor stappen om problemen met kopiëren naar/vanuit een on-premises gegevensarchief op te lossen met behulp van de Data Management Gateway.

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Probleem: On-demand HDInsight provisioning mislukt
Wanneer u een gekoppelde service van het type HDInsightOnDemand gebruikt, moet u een linkedServiceName opgeven die naar een Azure-blobopslag verwijst. De Data Factory-service gebruikt deze opslag om logboeken en ondersteunende bestanden voor het HDInsight-cluster op aanvraag op te slaan.  Soms mislukt het inrichten van een HDInsight-cluster op aanvraag en wordt de volgende fout weergegeven:

```
Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.
```

Deze fout geeft meestal aan dat de locatie van het opslagaccount, opgegeven in de linkedServiceName, zich niet bevindt in hetzelfde datacenter waar de inrichting van HDInsight plaatsvindt. Voorbeeld: als uw gegevensfabriek zich in West-VS bevindt en de Azure-opslag zich in Oost-VS bevindt, mislukt de on-demandvoorziening in West-VS.

Daarnaast is er een tweede JSON-eigenschap, additionalLinkedServiceNames, waar extra opslagaccounts in HDInsight op aanvraag kunnen worden opgegeven. Deze extra gekoppelde opslagaccounts moeten zich op dezelfde locatie bevinden als het HDInsight-cluster, of het mislukt met dezelfde fout.

### <a name="problem-custom-net-activity-fails"></a>Probleem: Aangepaste .NET-activiteit mislukt
Zie [Een pijplijn met aangepaste activiteit debuggen](data-factory-use-custom-activities.md#troubleshoot-failures) voor gedetailleerde stappen.

## <a name="use-azure-portal-to-troubleshoot"></a>Azure-portal gebruiken om problemen op te lossen
### <a name="using-portal-blades"></a>Portalblades gebruiken
Zie [Pijplijn controleren](data-factory-monitor-manage-pipelines.md) voor stappen.

### <a name="using-monitor-and-manage-app"></a>App voor controle en beheer gebruiken
Zie [Pijplijnen voor gegevensfabrieken controleren en beheren met monitor en App beheren](data-factory-monitor-manage-app.md) voor meer informatie.

## <a name="use-azure-powershell-to-troubleshoot"></a>Azure PowerShell gebruiken om problemen op te lossen
### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Azure PowerShell gebruiken om een fout op te lossen
Zie [Pipelines voor gegevensfabrieken controleren met Azure PowerShell](data-factory-monitor-manage-pipelines.md) voor meer informatie.

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: https://go.microsoft.com/fwlink/?LinkId=516971

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
