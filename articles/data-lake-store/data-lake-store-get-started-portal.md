---
title: Aan de slag met Azure Data Lake Storage Gen1 - portal
description: Gebruik de Azure-portal om een Data Lake Storage Gen1-account te maken en basisbewerkingen in het account uit te voeren.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 1bfb9362aa0e893a8bf1a758e5a0069e85ab227b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265582"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-portal"></a>Aan de slag met Azure Data Lake Storage Gen1 met de Azure-portal

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [Powershell](data-lake-store-get-started-powershell.md)
> * [Azure-CLI](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Meer informatie over het gebruik van de Azure-portal om een Data Lake Storage Gen1-account te maken en basisbewerkingen uit te voeren, zoals mappen maken, gegevensbestanden uploaden en downloaden, uw account verwijderen, enz. Zie [Overzicht van Azure Data Lake Storage Gen1](data-lake-store-overview.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelfstudie begint, moet u beschikken over de volgende items:

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-storage-gen1-account"></a>Een Data Lake Storage Gen1-account maken

1. Meld u aan bij de nieuwe [Azure-portal](https://portal.azure.com).
2. Klik **op Een bron > Storage > Data Lake Storage Gen1 maken**.
3. Geef in het **nieuwe Blade van Data Lake Storage Gen1** de waarden op zoals in de volgende schermafbeelding wordt weergegeven:

    ![Een nieuw Data Lake Storage Gen1-account maken](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Een nieuw Data Lake Storage Gen1-account maken")

   * **Naam**. Voer een unieke naam in voor het Data Lake Storage Gen1-account.
   * **Abonnement**. Selecteer het abonnement waaronder u een nieuw Data Lake Storage Gen1-account wilt maken.
   * **Resourcegroep**. Selecteer een bestaande resourcegroep of selecteer de optie **Nieuwe maken** om er een te maken. Een resourcegroep is een container met verwante resources voor een toepassing. Zie [Resourcegroepen in Azure](../azure-resource-manager/management/overview.md#resource-groups) voor meer informatie.
   * **Locatie:** selecteer een locatie waar u het Data Lake Storage Gen1-account wilt maken.
   * **Versleutelingsinstellingen**. Er zijn drie opties:

     * **Geen versleuteling inschakelen**.
     * **Gebruik sleutels die worden beheerd door Data Lake Storage Gen1**, als u wilt dat Data Lake Storage Gen1 uw versleutelingssleutels beheert.
     * **Sleutels gebruiken uit uw eigen Key Vault**. U kunt een bestaande Azure Key Vault selecteren of een nieuwe Key Vault maken. Als u de sleutels uit een Sleutelkluis wilt gebruiken, moet u machtigingen toewijzen voor het Data Lake Storage Gen1-account om toegang te krijgen tot de Azure Key Vault. Zie [Machtigingen toewijzen aan Azure Key Vault](#assign-permissions-to-azure-key-vault) voor instructies.

        ![Versleuteling van Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Versleuteling van Data Lake Storage Gen1")

        Klik op **OK** in de blade **Versleutelingsinstellingen**.

        Zie [Versleuteling van gegevens in Azure Data Lake Storage Gen1](./data-lake-store-encryption.md)voor meer informatie.

4. Klik **op Maken**. Als u ervoor kiest om het account aan het dashboard vast te maken, wordt u teruggebracht naar het dashboard en u de voortgang van uw Data Lake Storage Gen1-accountbepaling zien. Zodra het Data Lake Storage Gen1-account is ingericht, verschijnt het accountblad.

## <a name="assign-permissions-to-azure-key-vault"></a><a name="assign-permissions-to-azure-key-vault"></a>Machtigingen toewijzen aan Azure Key Vault

Als u sleutels van een Azure Key Vault hebt gebruikt om versleuteling te configureren op het Data Lake Storage Gen1-account, moet u de toegang configureren tussen het Data Lake Storage Gen1-account en het Azure Key Vault-account. Volg hiervoor de volgende stappen.

1. Als u sleutels uit de Azure Key Vault hebt gebruikt, wordt bovenin een waarschuwing weergegeven voor het blade voor het Data Lake Storage Gen1-account. Klik op de waarschuwing om **Versleuteling** te openen.

    ![Versleuteling van Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Versleuteling van Data Lake Storage Gen1")
2. Op de blade ziet u twee opties om toegang te configureren.

    ![Versleuteling van Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/adls-encryption-4.png "Versleuteling van Data Lake Storage Gen1")

   * Klik in de eerste optie op **Machtigingen verlenen** om toegang te configureren. De eerste optie is alleen ingeschakeld wanneer de gebruiker die het Data Lake Storage Gen1-account heeft gemaakt, ook een beheerder is voor de Azure Key Vault.
   * De andere optie is om de PowerShell-cmdlet uit te voeren die op de blade wordt weergegeven. U moet de eigenaar van de Azure Key Vault zijn of zijn gemachtigd om machtigingen voor de Azure Key Vault te verlenen. Wanneer u de cmdlet hebt uitgevoerd, keert u terug naar de blade en klikt u op **Inschakelen** om toegang te configureren.

> [!NOTE]
> U ook een Data Lake Storage Gen1-account maken met Azure Resource Manager-sjablonen. Deze sjablonen zijn toegankelijk vanaf [Azure-snelstartsjablonen](https://azure.microsoft.com/resources/templates/?term=data+lake+store):
> * Zonder gegevensversleuteling: [Implementeer Azure Data Lake Storage Gen1-account zonder gegevensversleuteling.](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)
> * Met gegevensversleuteling met Data Lake Storage Gen1: [Deploy Data Lake Storage Gen1-account met versleuteling (Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/).
> * Met gegevensversleuteling met Azure Key Vault: [Implementeer Data Lake Storage Gen1-account met versleuteling (Key Vault).](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)
>
>

## <a name="create-folders"></a><a name="createfolder"></a>Mappen maken

U mappen maken onder uw Data Lake Storage Gen1-account om gegevens te beheren en op te slaan.

1. Open het Data Lake Storage Gen1-account dat u hebt gemaakt. Klik in het linkerdeelvenster op **Alle bronnen**en klik vervolgens in het blad **Alle bronnen** op de accountnaam waaronder u mappen wilt maken. Als u het account hebt vastgemaakt aan het startboard, klikt u op de tegel voor dat account.
2. Klik in het accountblad Data Lake Storage Gen1 op **Data Explorer**.

    ![Mappen maken in een Data Lake Storage Gen1-account](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Mappen maken in een Data Lake Storage Gen1-account")
3. Klik op de blade Data Explorer op **Nieuwe map**, voer een naam in voor de nieuwe map en klik op **OK**.

    ![Mappen maken in een Data Lake Storage Gen1-account](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Mappen maken in een Data Lake Storage Gen1-account")

    De zojuist gemaakte map wordt vermeld op de blade **Data Explorer**. U geneste mappen tot elk niveau maken.

    ![Mappen maken in een Data Lake-account](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Mappen maken in een Data Lake-account")

## <a name="upload-data"></a><a name="uploaddata"></a>Gegevens uploaden

U uw gegevens direct op basisniveau of naar een map die u in het account hebt gemaakt, uploaden naar een Data Lake Storage Gen1-account.

1. Klik op de blade **Data Explorer** op **Uploaden**.
2. Navigeer op de blade **Bestanden uploaden** naar de bestanden die u wilt uploaden en klik op **Geselecteerde bestanden toevoegen**. U kunt meer dan één bestand selecteren om te uploaden.

    ![Gegevens uploaden](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Gegevens uploaden")

Als u nog geen voorbeeldgegevens hebt om te uploaden, kunt u de map **Ambulance Data** uit de [Azure Data Lake Git-opslagplaats](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) gebruiken.

## <a name="actions-available-on-the-stored-data"></a><a name="properties"></a>Acties die beschikbaar zijn voor de opgeslagen gegevens

Klik op het beletselteken bij een bestand en klik in het pop-upmenu op de actie die u wilt uitvoeren met de gegevens.

![Eigenschappen van de gegevens](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Eigenschappen van de gegevens")

## <a name="secure-your-data"></a>Uw gegevens beveiligen

U de gegevens die zijn opgeslagen in uw Data Lake Storage Gen1-account beveiligen met Azure Active Directory en toegangscontrole (ACL's). Zie [Gegevens beveiligen in Azure Data Lake Storage Gen1](data-lake-store-secure-data.md)voor instructies over hoe u dit doen.

## <a name="delete-your-account"></a>Uw account verwijderen

Als u een Data Lake Storage Gen1-account wilt verwijderen, klikt u uit uw Data Lake Storage Gen1-blade op **Verwijderen**. Om de actie te bevestigen, wordt u gevraagd de naam in te voeren van het account dat u wilt verwijderen. Voer de naam in van het account en klik op **Verwijderen**.

![Gegevens Lake Storage Gen1-account verwijderen](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Data Lake-account verwijderen")

## <a name="next-steps"></a>Volgende stappen

* [Azure Data Lake Storage Gen1 gebruiken voor vereisten voor big data](data-lake-store-data-scenarios.md)
* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Azure Data Lake Analytics gebruiken met Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight gebruiken met Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
