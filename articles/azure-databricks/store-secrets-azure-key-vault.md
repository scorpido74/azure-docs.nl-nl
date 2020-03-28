---
title: Zelfstudie - Toegang blob-opslag met behulp van sleutelkluis met Azure Databricks
description: In deze zelfstudie wordt beschreven hoe u azure blob-opslag vanuit Azure Databricks openen met geheimen die zijn opgeslagen in een sleutelkluis.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 07/19/2019
ms.openlocfilehash: 15399d5a00c13141877dcf44640df2c1f9b9ba5c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75889059"
---
# <a name="tutorial-access-azure-blob-storage-from-azure-databricks-using-azure-key-vault"></a>Zelfstudie: Toegang krijgen tot Azure Blob Storage vanuit Azure Databricks met Azure Key Vault

In deze zelfstudie wordt beschreven hoe u azure blob-opslag vanuit Azure Databricks openen met geheimen die zijn opgeslagen in een sleutelkluis.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een opslagaccount en blobcontainer maken
> * Een Azure Key Vault maken en een geheim toevoegen
> * Een Azure Databricks-werkruimte maken en een geheim bereik toevoegen
> * Toegang tot uw blobcontainer vanuit Azure Databricks

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/).

> [!Note]
> Deze zelfstudie kan niet worden uitgevoerd met **azure free trial subscription**.
> Als je een gratis account hebt, ga je naar je profiel en wijzig je je abonnement naar **betalen per gebruik.** Zie [Gratis Azure-account](https://azure.microsoft.com/free/) voor meer informatie. Verwijder vervolgens [de bestedingslimiet](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)en [vraag een quotumverhoging aan](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) voor vCPU's in uw regio. Wanneer u uw Azure Databricks-werkruimte maakt, u de prijscategorie **Proefkeuzeprijzen (Premium - 14-dagen gratis DU's)** selecteren om de werkruimte gedurende 14 dagen toegang te geven tot gratis Premium Azure Databricks DU's.

## <a name="create-a-storage-account-and-blob-container"></a>Een opslagaccount en blobcontainer maken

1. Selecteer in de Azure-portal de optie **Een bronopslag** > **maken**. Selecteer vervolgens **Opslagaccount**.

   ![Azure-opslagaccountbron zoeken](./media/store-secrets-azure-key-vault/create-storage-account-resource.png)

2. Selecteer uw abonnements- en resourcegroep of maak een nieuwe resourcegroep. Voer vervolgens een naam van een opslagaccount in en kies een locatie. Selecteer **Controleren + maken**.

   ![Opslagaccounteigenschappen instellen](./media/store-secrets-azure-key-vault/create-storage-account.png)

3. Als de validatie mislukt, u de problemen oplossen en het opnieuw proberen. Als de validatie is geslaagd, selecteert u **Maken** en wachten tot het opslagaccount is gemaakt.

4. Navigeer naar uw nieuw gemaakte opslagaccount en selecteer **Blobs** onder **Services** op de pagina **Overzicht.** Selecteer **+ Container** en voer een containernaam in. Selecteer **OK**.

   ![Nieuwe container maken](./media/store-secrets-azure-key-vault/create-blob-storage-container.png)

5. Zoek een bestand dat u wilt uploaden naar uw blob-opslagcontainer. Als u geen bestand hebt, gebruikt u een teksteditor om een nieuw tekstbestand met bepaalde informatie te maken. In dit voorbeeld bevat een bestand met de naam **hw.txt** de tekst 'hallo wereld'. Sla uw tekstbestand lokaal op en upload het naar uw blob-opslagcontainer.

   ![Bestand uploaden naar container](./media/store-secrets-azure-key-vault/upload-txt-file.png)

6. Ga terug naar uw opslagaccount en selecteer **Toegangssleutels** onder **Instellingen**. Kopieer **de naam van het Opslagaccount** en sleutel **1** naar een teksteditor voor later gebruik in deze zelfstudie.

   ![Toegangssleutels voor opslagaccount zoeken](./media/store-secrets-azure-key-vault/storage-access-keys.png)

## <a name="create-an-azure-key-vault-and-add-a-secret"></a>Een Azure Key Vault maken en een geheim toevoegen

1. Selecteer in de Azure-portal **Een resource maken** en voer **Sleutelkluis** in het zoekvak in.

   ![Een zoekvak voor Azure-bronnen maken](./media/store-secrets-azure-key-vault/find-key-vault-resource.png)

2. De Key Vault-bron wordt automatisch geselecteerd. Selecteer **Maken**.

   ![Een Key Vault-bron maken](./media/store-secrets-azure-key-vault/create-key-vault-resource.png)

3. Voer op de pagina **Sleutelkluis maken** de volgende gegevens in en bewaar de standaardwaarden voor de overige velden:

   |Eigenschap|Beschrijving|
   |--------|-----------|
   |Name|Een unieke naam voor uw sleutelkluis.|
   |Abonnement|Kies een abonnement.|
   |Resourcegroep|Kies een resourcegroep of maak een nieuwe groep.|
   |Locatie|Kies een locatie.|

   ![Eigenschappen van Azure-sleutelkluis](./media/store-secrets-azure-key-vault/create-key-vault-properties.png)

3. Selecteer na het opgeven van de bovenstaande gegevens **Maken**. 

4. Navigeer naar uw nieuw gemaakte sleutelkluis in de Azure-portal en selecteer **Geheimen**. Selecteer vervolgens **+ Genereren/importeren**. 

   ![Nieuwe sleutelkluisgeheim genereren](./media/store-secrets-azure-key-vault/generate-import-secrets.png)

5. Geef **op de pagina Een geheim maken** de volgende informatie op en bewaar de standaardwaarden voor de resterende velden:

   |Eigenschap|Waarde|
   |--------|-----------|
   |Uploadopties|Handmatig|
   |Name|Vriendelijke naam voor uw opslagaccountsleutel.|
   |Waarde|toets1 van uw opslagaccount.|

   ![Eigenschappen voor nieuw sleutelkluisgeheim](./media/store-secrets-azure-key-vault/create-storage-secret.png)

6. Sla de sleutelnaam op in een teksteditor voor gebruik later in deze zelfstudie en selecteer **Maken**. Navigeer vervolgens naar het menu **Eigenschappen.** Kopieer de **DNS-naam** en **bron-id** naar een teksteditor voor gebruik later in de zelfstudie.

   ![DNS-naam en resource-id van Azure Key Vault kopiëren](./media/store-secrets-azure-key-vault/copy-dns-resource.png)

## <a name="create-an-azure-databricks-workspace-and-add-a-secret-scope"></a>Een Azure Databricks-werkruimte maken en een geheim bereik toevoegen

1. Selecteer in de Azure-portal de optie **Een resource** > **Analytics** > **Azure Databricks maken**.

    ![Databricks op Azure-portal](./media/store-secrets-azure-key-vault/azure-databricks-on-portal.png)

2. Geef onder **Azure Databricks Service**de volgende waarden om een Databricks-werkruimte te maken.

   |Eigenschap  |Beschrijving  |
   |---------|---------|
   |Naam van de werkruimte     | Geef een naam op voor uw Databricks-werkruimte.        |
   |Abonnement     | Selecteer uw Azure-abonnement in de vervolgkeuzelijst.        |
   |Resourcegroep     | Selecteer dezelfde brongroep die uw sleutelkluis bevat. |
   |Locatie     | Selecteer dezelfde locatie als uw Azure Key Vault. Zie [Azure-services die beschikbaar zijn per regio voor](https://azure.microsoft.com/regions/services/)alle beschikbare regio's.        |
   |Prijscategorie     |  U kunt kiezen tussen **Standard** en **Premium**. Bekijk de pagina [Prijzen voor Databricks](https://azure.microsoft.com/pricing/details/databricks/) voor meer informatie over deze categorieën.       |

   ![Eigenschappen van Databricks-werkruimte](./media/store-secrets-azure-key-vault/create-databricks-service.png)

   Selecteer **Maken**.

3. Navigeer naar uw nieuw gemaakte Azure Databricks-bron in de Azure-portal en selecteer **Werkruimte starten.**

   ![Azure Databricks-werkruimte starten](./media/store-secrets-azure-key-vault/launch-databricks-workspace.png)

4. Zodra uw Azure Databricks-werkruimte in een apart venster is geopend, u **#secrets/createScope** toevoegen aan de URL. De URL moet de volgende indeling hebben: 

   **https://<\locatie>.azuredatabricks.net/?o=<\orgID>#secrets/createScope**.
   

5. Voer een scopenaam in en voer de DNS-naam en resource-id van Azure Key Vault in die u eerder hebt opgeslagen. Sla de naam van het bereik op in een teksteditor voor gebruik later in deze zelfstudie. Ten slotte selecteert u **Create**.

   ![Geheime scope maken in de Azure Databricks-werkruimte](./media/store-secrets-azure-key-vault/create-secret-scope.png)

## <a name="access-your-blob-container-from-azure-databricks"></a>Toegang tot uw blobcontainer vanuit Azure Databricks

1. Selecteer op de startpagina van uw Azure Databricks-werkruimte **Nieuw cluster** onder **Algemene taken**.

   ![Een nieuw Azure Databricks-notitieblok maken](./media/store-secrets-azure-key-vault/create-new-cluster.png)

2. Voer een clusternaam in en selecteer **Cluster maken**. Het maken van het cluster duurt enkele minuten.

3. Zodra het cluster is gemaakt, navigeert u naar de startpagina van uw Azure Databricks-werkruimte en selecteert **u Nieuw notitieblok** onder Algemene **taken**.

   ![Een nieuw Azure Databricks-notitieblok maken](./media/store-secrets-azure-key-vault/create-new-notebook.png)

4. Voer een notitiebloknaam in en stel de taal in op Python. Stel het cluster in op de naam van het cluster dat u in de vorige stap hebt gemaakt.

5. Voer de volgende opdracht uit om de blobopslagcontainer te monteren. Vergeet niet de waarden voor de volgende eigenschappen te wijzigen:

   * de naam van uw container
   * uw naam van uw opslag-account
   * mount-naam
   * config-toets
   * scope-naam
   * sleutelnaam

   ```python
   dbutils.fs.mount(
   source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
   mount_point = "/mnt/<mount-name>",
   extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})
   ```

   * **mount-name** is een DBFS-pad dat aangeeft waar de Blob Storage-container of een map in de container (opgegeven in bron) worden gemonteerd.
   * **conf-key** kan `fs.azure.account.key.<\your-storage-account-name>.blob.core.windows.net` een van beide of`fs.azure.sas.<\your-container-name>.<\your-storage-account-name>.blob.core.windows.net`
   * **scope-name** is de naam van het geheime bereik dat u in de vorige sectie hebt gemaakt. 
   * **sleutelnaam** is de naam van het geheim dat u hebt gemaakt voor de opslagaccountsleutel in uw sleutelkluis.

   ![Blob-opslagbevestiging maken in notitieblok](./media/store-secrets-azure-key-vault/command1.png)

6. Voer de volgende opdracht uit om het tekstbestand in de blobopslagcontainer in een gegevensframe te lezen. Wijzig de waarden in de opdracht die overeenkomen met de naam en bestandsnaam van de houder.

   ```python
   df = spark.read.text("mnt/<mount-name>/<file-name>")
   ```

   ![Bestand lezen naar gegevensframe](./media/store-secrets-azure-key-vault/command2.png)

7. Gebruik de volgende opdracht om de inhoud van het bestand weer te geven.

   ```python
   df.show()
   ```
   ![Gegevensframe weergeven](./media/store-secrets-azure-key-vault/command3.png)

8. Voer de volgende opdracht uit om de blobopslag los te maken:

   ```python
   dbutils.fs.unmount("/mnt/<mount-name>")
   ```

   ![Opslagaccount demonteren](./media/store-secrets-azure-key-vault/command4.png)

9. Merk op dat zodra de mount is losgekoppeld, je niet meer lezen van je blob-opslagaccount.

   ![Fout opslagaccount ongedaan maken](./media/store-secrets-azure-key-vault/command5.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet blijft gebruiken, verwijdert u de volgende stappen:

1. Selecteer **resourcegroepen** in het linkermenu in de Azure-portal en navigeer naar uw brongroep.

2. Selecteer **Brongroep verwijderen** en typ de naam van uw resourcegroep. Selecteer vervolgens **Verwijderen**. 

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel om te leren hoe u een VNet-geïnjecteerde Databricks-omgeving implementeert met een Service Endpoint ingeschakeld voor Cosmos DB.
> [!div class="nextstepaction"]
> [Zelfstudie: Azure Databricks implementeren met een Cosmos DB-eindpunt](service-endpoint-cosmosdb.md)
