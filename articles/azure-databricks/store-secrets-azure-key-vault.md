---
title: Toegang tot Blob-opslag met sleutel kluis-Azure Databricks
description: In deze zelf studie wordt beschreven hoe u toegang krijgt tot Azure Blob Storage van Azure Databricks met behulp van geheimen die zijn opgeslagen in een sleutel kluis.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 07/19/2019
ms.openlocfilehash: 16339590c3cfb11235ef4763c2e32c71b521c709
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539072"
---
# <a name="tutorial-access-azure-blob-storage-from-azure-databricks-using-azure-key-vault"></a>Zelf studie: toegang tot Azure Blob Storage vanuit Azure Databricks met behulp van Azure Key Vault

In deze zelf studie wordt beschreven hoe u toegang krijgt tot Azure Blob Storage van Azure Databricks met behulp van geheimen die zijn opgeslagen in een sleutel kluis.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een opslag account en BLOB-container maken
> * Een Azure Key Vault maken en een geheim toevoegen
> * Een Azure Databricks-werk ruimte maken en een geheim bereik toevoegen
> * Toegang tot uw BLOB-container vanuit Azure Databricks

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/).

> [!Note]
> Deze zelf studie kan niet worden uitgevoerd met een **gratis proef abonnement van Azure**.
> Als u een gratis account hebt, gaat u naar uw profiel en wijzigt u uw abonnement in **betalen per gebruik**. Zie [Gratis Azure-account](https://azure.microsoft.com/free/) voor meer informatie. Vervolgens [verwijdert u de bestedings limiet](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)en [vraagt u een quotum toename](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) aan voor vcpu's in uw regio. Wanneer u uw Azure Databricks-werk ruimte maakt, kunt u de prijs categorie **Trial (Premium-14-dagen gratis dbu's)** selecteren om de werk ruimte gedurende 14 dagen toegang te geven tot gratis premium Azure Databricks dbu's.

## <a name="create-a-storage-account-and-blob-container"></a>Een opslag account en BLOB-container maken

1. Selecteer in de Azure Portal **een resource maken** > **opslag**. Selecteer vervolgens **opslag account**.

   ![Azure Storage-account resource zoeken](./media/store-secrets-azure-key-vault/create-storage-account-resource.png)

2. Selecteer uw abonnement en resource groep of maak een nieuwe resource groep. Voer vervolgens een naam voor het opslag account in en kies een locatie. Selecteer **Controleren + maken**.

   ![Eigenschappen van opslag account instellen](./media/store-secrets-azure-key-vault/create-storage-account.png)

3. Als de validatie mislukt, verhelpt u de problemen en probeert u het opnieuw. Als de validatie is geslaagd, selecteert u **maken** en wacht u totdat het opslag account is gemaakt.

4. Navigeer naar het zojuist gemaakte opslag account en selecteer **blobs** onder **Services** op de pagina **overzicht** . Selecteer vervolgens **+ container** en voer een container naam in. Selecteer **OK**.

   ![Nieuwe container maken](./media/store-secrets-azure-key-vault/create-blob-storage-container.png)

5. Zoek een bestand dat u wilt uploaden naar de BLOB storage-container. Als u geen bestand hebt, kunt u een tekst editor gebruiken om een nieuw tekst bestand met informatie te maken. In dit voor beeld bevat een bestand met de naam **hw. txt** de tekst "Hallo wereld". Sla het tekst bestand lokaal op en upload het naar uw Blob Storage-container.

   ![Bestand uploaden naar container](./media/store-secrets-azure-key-vault/upload-txt-file.png)

6. Ga terug naar uw opslag account en selecteer **toegangs sleutels** onder **instellingen**. Kopieer de naam van het **opslag account** en de **sleutel 1** naar een tekst editor voor later gebruik in deze zelf studie.

   ![Toegangs sleutels voor opslag accounts zoeken](./media/store-secrets-azure-key-vault/storage-access-keys.png)

## <a name="create-an-azure-key-vault-and-add-a-secret"></a>Een Azure Key Vault maken en een geheim toevoegen

1. Selecteer in het Azure Portal de optie **een resource maken** en geef **Key Vault** op in het zoekvak.

   ![Een zoekvak voor een Azure-resource maken](./media/store-secrets-azure-key-vault/find-key-vault-resource.png)

2. De Key Vault resource wordt automatisch geselecteerd. Selecteer **Maken**.

   ![Een Key Vault resource maken](./media/store-secrets-azure-key-vault/create-key-vault-resource.png)

3. Voer op de pagina **sleutel kluis maken** de volgende informatie in en behoud de standaard waarden voor de resterende velden:

   |Eigenschap|Beschrijving|
   |--------|-----------|
   |Naam|Een unieke naam voor uw sleutel kluis.|
   |Abonnement|Kies een abonnement.|
   |Resourcegroep|Kies een resource groep of maak een nieuwe.|
   |Locatie|Kies een locatie.|

   ![Eigenschappen van Azure sleutel kluis](./media/store-secrets-azure-key-vault/create-key-vault-properties.png)

3. Selecteer na het opgeven van de bovenstaande gegevens **Maken**. 

4. Navigeer naar de zojuist gemaakte sleutel kluis in de Azure Portal en selecteer **geheimen**. Selecteer vervolgens **+ genereren/importeren**. 

   ![Nieuw sleutel kluis geheim genereren](./media/store-secrets-azure-key-vault/generate-import-secrets.png)

5. Geef op de pagina **een geheim maken** de volgende informatie op en behoud de standaard waarden voor de resterende velden:

   |Eigenschap|Waarde|
   |--------|-----------|
   |Upload opties|Handmatig|
   |Naam|Beschrijvende naam voor de sleutel van uw opslag account.|
   |Waarde|Key1 van uw opslag account.|

   ![Eigenschappen voor nieuw sleutel kluis geheim](./media/store-secrets-azure-key-vault/create-storage-secret.png)

6. Sla de naam van de sleutel op in een tekst editor, zodat u deze later in deze zelf studie kunt gebruiken en selecteer **maken**. Ga vervolgens naar het menu **Eigenschappen** . Kopieer de **DNS-naam** en **resource-id** naar een tekst editor, zodat u deze later in de zelf studie kunt gebruiken.

   ![Azure Key Vault DNS-naam en Resource-ID kopiëren](./media/store-secrets-azure-key-vault/copy-dns-resource.png)

## <a name="create-an-azure-databricks-workspace-and-add-a-secret-scope"></a>Een Azure Databricks-werk ruimte maken en een geheim bereik toevoegen

1. Selecteer in Azure Portal **Een resource maken** > **Analyse** > **Azure Databricks**.

    ![Databricks op Azure Portal](./media/store-secrets-azure-key-vault/azure-databricks-on-portal.png)

2. Geef onder **Azure Databricks service**de volgende waarden op om een Databricks-werk ruimte te maken.

   |Eigenschap  |Beschrijving  |
   |---------|---------|
   |Werkruimte naam     | Geef een naam op voor uw Databricks-werkruimte.        |
   |Abonnement     | Selecteer uw Azure-abonnement in de vervolgkeuzelijst.        |
   |Resourcegroep     | Selecteer dezelfde resource groep die de sleutel kluis bevat. |
   |Locatie     | Selecteer dezelfde locatie als uw Azure Key Vault. Zie [Azure-Services beschikbaar per regio](https://azure.microsoft.com/regions/services/)voor alle beschik bare regio's.        |
   |Prijscategorie     |  U kunt kiezen tussen **Standard** en **Premium**. Bekijk de pagina [Prijzen voor Databricks](https://azure.microsoft.com/pricing/details/databricks/) voor meer informatie over deze categorieën.       |

   ![Eigenschappen van Databricks-werk ruimte](./media/store-secrets-azure-key-vault/create-databricks-service.png)

   Selecteer **Maken**.

3. Navigeer naar uw nieuw gemaakte Azure Databricks-resource in de Azure Portal en selecteer **werk ruimte starten**.

   ![Azure Databricks-werk ruimte starten](./media/store-secrets-azure-key-vault/launch-databricks-workspace.png)

4. Nadat uw Azure Databricks-werk ruimte in een afzonderlijk venster is geopend, voegt u **#secrets/createscope** toe aan de URL. De URL moet de volgende indeling hebben: 

   **https://< \location >. azuredatabricks. net/#secrets/createscope**.

5. Voer een scope naam in en voer de Azure Key Vault DNS-naam en de resource-ID in die u eerder hebt opgeslagen. Sla de naam van het bereik op in een tekst editor, zodat u deze later in deze zelf studie kunt gebruiken. Ten slotte selecteert u **Create**.

   ![Een geheim bereik maken in de Azure Databricks-werk ruimte](./media/store-secrets-azure-key-vault/create-secret-scope.png)

## <a name="access-your-blob-container-from-azure-databricks"></a>Toegang tot uw BLOB-container vanuit Azure Databricks

1. Op de start pagina van uw Azure Databricks-werk ruimte selecteert u **Nieuw cluster** onder **algemene taken**.

   ![Een nieuwe Azure Databricks notitie blok maken](./media/store-secrets-azure-key-vault/create-new-cluster.png)

2. Voer een cluster naam in en selecteer **cluster maken**. Het kan een paar minuten duren voordat het cluster is gemaakt.

3. Zodra het cluster is gemaakt, gaat u naar de start pagina van uw Azure Databricks-werk ruimte en selecteert u **Nieuw notitie blok** onder **algemene taken**.

   ![Een nieuwe Azure Databricks notitie blok maken](./media/store-secrets-azure-key-vault/create-new-notebook.png)

4. Voer de naam van een notitie blok in en stel de taal in op python. Stel het cluster in op de naam van het cluster dat u in de vorige stap hebt gemaakt.

5. Voer de volgende opdracht uit om de BLOB storage-container te koppelen. Vergeet niet om de waarden voor de volgende eigenschappen te wijzigen:

   * uw-container naam
   * uw-opslag account-naam
   * koppelings naam
   * configuratie sleutel
   * bereik-naam
   * sleutel naam

   ```python
   dbutils.fs.mount(
   source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
   mount_point = "/mnt/<mount-name>",
   extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})
   ```

   * **mount-name** is een DBFS-pad dat aangeeft waar de BLOB storage container of een map in de container (opgegeven in de bron) wordt gekoppeld.
   * **conf-sleutel** kan `fs.azure.account.key.<\your-storage-account-name>.blob.core.windows.net` of `fs.azure.sas.<\your-container-name>.<\your-storage-account-name>.blob.core.windows.net`
   * **bereik: naam** is de naam van het geheime bereik dat u in de vorige sectie hebt gemaakt. 
   * **sleutel naam** is de naam van het geheim dat u hebt gemaakt voor de sleutel van het opslag account in uw sleutel kluis.

   ![Blob-opslag koppeling in notitie blok maken](./media/store-secrets-azure-key-vault/command1.png)

6. Voer de volgende opdracht uit om het tekst bestand in uw Blob Storage-container te lezen in een data frame. Wijzig de waarden in de opdracht zodat deze overeenkomen met de naam en bestands naam van de koppeling.

   ```python
   df = spark.read.text("mnt/<mount-name>/<file-name>")
   ```

   ![Bestand lezen in data frame](./media/store-secrets-azure-key-vault/command2.png)

7. Gebruik de volgende opdracht om de inhoud van het bestand weer te geven.

   ```python
   df.show()
   ```
   ![Data frame weer geven](./media/store-secrets-azure-key-vault/command3.png)

8. Voer de volgende opdracht uit om de Blob-opslag te ontkoppelen:

   ```python
   dbutils.fs.unmount("/mnt/<mount-name>")
   ```

   ![Opslag account ontkoppelen](./media/store-secrets-azure-key-vault/command4.png)

9. Als de koppeling is ontkoppeld, kunt u niet meer lezen uit uw Blob Storage-account.

   ![Fout bij ontkoppelen van opslag account](./media/store-secrets-azure-key-vault/command5.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet wilt blijven gebruiken, verwijdert u de hele resource groep door de volgende stappen uit te voeren:

1. Selecteer **resource groepen** in het menu aan de linkerkant in azure Portal en navigeer naar uw resource groep.

2. Selecteer **resource groep verwijderen** en typ de naam van de resource groep. Selecteer vervolgens **Verwijderen**. 

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over het implementeren van een door VNet geïnjecteerde Databricks-omgeving met een service-eind punt dat is ingeschakeld voor Cosmos DB.
> [!div class="nextstepaction"]
> [Zelf studie: Azure Databricks implementeren met een Cosmos DB-eind punt](service-endpoint-cosmosdb.md)
