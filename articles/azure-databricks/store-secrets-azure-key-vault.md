---
title: Zelfstudie - Toegang tot Blob Storage met behulp van een sleutelkluis vanuit Azure Databricks
description: In deze zelfstudie wordt beschreven hoe u toegang tot Azure Blob Storage krijgt vanuit Azure Databricks met behulp van geheimen die in een sleutelkluis zijn opgeslagen.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 07/19/2019
ms.openlocfilehash: 026165c7c2052992e8ab485f9ab81c8964f38235
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647961"
---
# <a name="tutorial-access-azure-blob-storage-from-azure-databricks-using-azure-key-vault"></a>Zelfstudie: Toegang tot Azure Blob Storage vanuit Azure Databricks met behulp van Azure Key Vault

In deze zelfstudie wordt beschreven hoe u toegang tot Azure Blob Storage krijgt vanuit Azure Databricks met behulp van geheimen die in een sleutelkluis zijn opgeslagen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een opslagaccount en blobcontainer maken
> * Een Azure Key Vault maken en een geheim toevoegen
> * Een Azure Databricks-werkruimte maken en een geheim bereik toevoegen
> * Toegang tot uw blobcontainer vanuit Azure Databricks

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

> [!Note]
> Deze zelfstudie kan niet worden uitgevoerd met behulp van een **gratis Azure-proefabonnement**.
> Als u een gratis account hebt, gaat u naar uw profiel en wijzigt u uw abonnement in **Betalen per gebruik**. Zie [Gratis Azure-account](https://azure.microsoft.com/free/) voor meer informatie. Vervolgens [verwijdert u de bestedingslimiet](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit) en [vraagt u een quotumverhoging aan](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) voor vCPU’s in uw regio. Wanneer u uw Azure Databricks-werkruimte maakt, kunt u de prijscategorie **Proefversie (Premium - 14 dagen gratis DBU’s)** selecteren om de werkruimte 14 dagen lang toegang te geven tot gratis Premium Azure Databricks DBU’s.

## <a name="create-a-storage-account-and-blob-container"></a>Een opslagaccount en blobcontainer maken

1. Selecteer **Een resource maken** > **Opslag** in de Azure-portal. Selecteer vervolgens **Opslagaccount**.

   ![Resource voor Azure-opslagaccount zoeken](./media/store-secrets-azure-key-vault/create-storage-account-resource.png)

2. Selecteer uw abonnement en resourcegroep of maak een nieuwe resourcegroep. Voer vervolgens een opslagaccountnaam in en kies een locatie. Selecteer **Controleren + maken**.

   ![Eigenschappen van opslagaccount instellen](./media/store-secrets-azure-key-vault/create-storage-account.png)

3. Als de validatie mislukt, lost u de problemen op en probeert u het opnieuw. Als de validatie slaagt, selecteert u **Maken** en wacht u tot het opslagaccount is gemaakt.

4. Navigeer naar uw zojuist gemaakte opslagaccount en selecteer **Blobs** onder **Services** op de pagina **Overzicht**. Selecteer vervolgens **+ Container** en voer een containernaam in. Selecteer **OK**.

   ![Nieuwe container maken](./media/store-secrets-azure-key-vault/create-blob-storage-container.png)

5. Zoek een bestand dat u wilt uploaden naar uw Blob Storage-container. Als u geen bestand hebt, gebruikt u een teksteditor om een nieuw tekstbestand met wat informatie te maken. In dit voorbeeld bevat een bestand genaamd **hw.txt** de tekst ‘hallo wereld’. Sla uw tekstbestand lokaal op en upload het naar uw Blob Storage-container.

   ![Bestand uploaden naar container](./media/store-secrets-azure-key-vault/upload-txt-file.png)

6. Ga terug naar uw opslagaccount en selecteer **Toegangssleutels** onder **Instellingen**. Kopieer **Naam van opslagaccount** en **key1** naar een teksteditor voor later gebruik in deze zelfstudie.

   ![Toegangssleutels voor opslagaccount zoeken](./media/store-secrets-azure-key-vault/storage-access-keys.png)

## <a name="create-an-azure-key-vault-and-add-a-secret"></a>Een Azure Key Vault maken en een geheim toevoegen

1. Selecteer in de Azure-portal de optie **Een resource maken** en voer **Key Vault** in het zoekvak in.

   ![Een zoekvak voor Azure-resources maken](./media/store-secrets-azure-key-vault/find-key-vault-resource.png)

2. De Key Vault-resource wordt automatisch geselecteerd. Selecteer **Maken**.

   ![Een Key Vault-resource maken](./media/store-secrets-azure-key-vault/create-key-vault-resource.png)

3. Voer op de pagina **Sleutelkluis maken** de volgende informatie in en laat voor de resterende velden de standaardwaarden staan:

   |Eigenschap|Beschrijving|
   |--------|-----------|
   |Naam|Een unieke naam voor uw sleutelkluis.|
   |Abonnement|Kies een abonnement.|
   |Resourcegroep|Kies een resourcegroep of maak een nieuwe.|
   |Locatie|Kies een locatie.|

   ![Azure Key Vault-eigenschappen](./media/store-secrets-azure-key-vault/create-key-vault-properties.png)

3. Selecteer na het opgeven van de bovenstaande gegevens **Maken**. 

4. Navigeer naar uw zojuist gemaakte sleutelkluis in de Azure-portal en selecteer **Geheimen**. Selecteer vervolgens **+ Genereren/importeren**. 

   ![Nieuw sleutelkluisgeheim genereren](./media/store-secrets-azure-key-vault/generate-import-secrets.png)

5. Geef op de pagina **Een geheim maken** de volgende informatie op en laat voor de resterende velden de standaardwaarden staan:

   |Eigenschap|Waarde|
   |--------|-----------|
   |Uploadopties|Handmatig|
   |Naam|Beschrijvende naam voor uw opslagaccountsleutel.|
   |Waarde|key1 uit uw opslagaccount.|

   ![Eigenschappen voor nieuw sleutelkluisgeheim](./media/store-secrets-azure-key-vault/create-storage-secret.png)

6. Sla de sleutelnaam op in een teksteditor voor later gebruik in deze zelfstudie, en selecteer **Maken**. Navigeer vervolgens naar het menu **Eigenschappen**. Kopieer de **DNS-naam** en **Resource-id** naar een teksteditor voor later gebruik in de zelfstudie.

   ![DNS-naam en resource-id van Azure Key Vault kopiëren](./media/store-secrets-azure-key-vault/copy-dns-resource.png)

## <a name="create-an-azure-databricks-workspace-and-add-a-secret-scope"></a>Een Azure Databricks-werkruimte maken en een geheim bereik toevoegen

1. Selecteer in Azure Portal **Een resource maken** > **Analyse** > **Azure Databricks**.

    ![Databricks in Azure-portal](./media/store-secrets-azure-key-vault/azure-databricks-on-portal.png)

2. Geef onder **Azure Databricks Service** de volgende waarden op voor het maken van een Databricks-werkruimte.

   |Eigenschap  |Beschrijving  |
   |---------|---------|
   |Werkruimtenaam     | Geef een naam op voor uw Databricks-werkruimte.        |
   |Abonnement     | Selecteer uw Azure-abonnement in de vervolgkeuzelijst.        |
   |Resourcegroep     | Selecteer dezelfde resourcegroep die uw sleutelkluis bevat. |
   |Locatie     | Selecteer dezelfde locatie als uw Azure Key Vault. Zie [Azure-services beschikbaar per regio](https://azure.microsoft.com/regions/services/) voor alle beschikbare regio's.        |
   |Prijscategorie     |  U kunt kiezen tussen **Standard** en **Premium**. Bekijk de pagina [Prijzen voor Databricks](https://azure.microsoft.com/pricing/details/databricks/) voor meer informatie over deze categorieën.       |

   ![Eigenschappen van Databricks-werkruimte](./media/store-secrets-azure-key-vault/create-databricks-service.png)

   Selecteer **Maken**.

3. Navigeer naar uw zojuist gemaakte Azure Databricks-resource in de Azure-portal en selecteer **Werkruimte starten**.

   ![Azure Databricks-werkruimte starten](./media/store-secrets-azure-key-vault/launch-databricks-workspace.png)

4. Zodra uw Azure Databricks-werkruimte openstaat in een apart venster, voegt u **#secrets/createScope** aan de URL toe. De URL moet de volgende indeling hebben: 

   **https://<\locatie>.azuredatabricks.net/?o=<\orgID>#secrets/createScope**.
   

5. Voer een bereiknaam in, en voer de DNS-naam en resource-id van Azure Key Vault in die u eerder hebt opgeslagen. Sla de bereiknaam op in een teksteditor voor later gebruik in deze zelfstudie. Ten slotte selecteert u **Create**.

   ![Een geheim bereik maken in de Azure Databricks-werkruimte](./media/store-secrets-azure-key-vault/create-secret-scope.png)

## <a name="access-your-blob-container-from-azure-databricks"></a>Toegang tot uw blobcontainer vanuit Azure Databricks

1. Selecteer op de startpagina van uw Azure Databricks-werkruimte de optie **Nieuw cluster** onder **Algemene taken**.

   ![Een nieuw Azure Databricks-notitieblok maken](./media/store-secrets-azure-key-vault/create-new-cluster.png)

2. Voer een clusternaam in en selecteer **Cluster maken**. Het duurt enkele minuten voordat het cluster is gemaakt.

3. Zodra het cluster is gemaakt, navigeert u naar de startpagina van uw Azure Databricks-werkruimte en selecteert u **Nieuw notitieblok** onder **Algemene taken**.

   ![Een nieuw Azure Databricks-notitieblok maken](./media/store-secrets-azure-key-vault/create-new-notebook.png)

4. Voer een notitiebloknaam in en stel de taal in op Python. Stel het cluster in op de naam van het cluster dat u in de vorige stap hebt gemaakt.

5. Voer de volgende opdracht uit om uw Blob Storage-container te koppelen. Vergeet niet de waarden voor de volgende eigenschappen te wijzigen:

   * uw-containernaam
   * uw-opslagaccountnaam
   * koppelingsnaam
   * configuratiesleutel
   * bereiknaam
   * sleutelnaam

   ```python
   dbutils.fs.mount(
   source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
   mount_point = "/mnt/<mount-name>",
   extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})
   ```

   * **koppelingsnaam** is een DBFS-pad dat aangeeft waar de Blob Storage-container of een map in de container (opgegeven in bron) wordt gekoppeld.
   * **configuratiesleutel** kan `fs.azure.account.key.<\your-storage-account-name>.blob.core.windows.net` of `fs.azure.sas.<\your-container-name>.<\your-storage-account-name>.blob.core.windows.net` zijn.
   * **bereiknaam** is de naam van het geheime bereik dat u in de vorige sectie hebt gemaakt. 
   * **sleutelnaam** is de naam van het geheim dat u hebt gemaakt voor de opslagaccountsleutel in uw sleutelkluis.

   ![Blob Storage-koppeling maken in notitieblok](./media/store-secrets-azure-key-vault/command1.png)

6. Voer de volgende opdracht uit om het tekstbestand in uw Blob Storage-container te lezen naar een gegevensframe. Wijzig de waarden in de opdracht om uw koppelingsnaam en bestandsnaam overeen te laten komen.

   ```python
   df = spark.read.text("/mnt/<mount-name>/<file-name>")
   ```

   ![Bestand lezen naar gegevensframe](./media/store-secrets-azure-key-vault/command2.png)

7. Gebruik de volgende opdracht om de inhoud van uw bestand weer te geven.

   ```python
   df.show()
   ```
   ![Gegevensframe weergeven](./media/store-secrets-azure-key-vault/command3.png)

8. Voer de volgende opdracht uit om uw Blob Storage te ontkoppelen:

   ```python
   dbutils.fs.unmount("/mnt/<mount-name>")
   ```

   ![Opslagaccount ontkoppelen](./media/store-secrets-azure-key-vault/command4.png)

9. Merk op dat zodra het account is ontkoppeld, u niet langer kunt lezen vanuit uw Blob Storage-account.

   ![Fout bij ontkoppelen van opslagaccount](./media/store-secrets-azure-key-vault/command5.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, verwijder dan uw hele resourcegroep met de volgende stappen:

1. Selecteer in het linkermenu in de Azure-portal de optie **Resourcegroepen** en navigeer naar uw resourcegroep.

2. Selecteer **Resourcegroep verwijderen** en typ uw resourcegroepnaam. Selecteer vervolgens **Verwijderen**. 

## <a name="next-steps"></a>Volgende stappen

Ga verder naar het volgende artikel voor meer informatie over het implementeren van een in VNet opgenomen Databricks-omgeving met een service-eindpunt ingeschakeld voor Cosmos DB.
> [!div class="nextstepaction"]
> [Zelfstudie: Azure Databricks implementeren met een Cosmos DB-eindpunt](service-endpoint-cosmosdb.md)
