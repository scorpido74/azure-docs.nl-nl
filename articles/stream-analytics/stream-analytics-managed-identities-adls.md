---
title: Azure Stream Analytics verifiëren voor Azure Data Lake Storage Gen1
description: In dit artikel wordt beschreven hoe u beheerde identiteiten gebruiken voor het verifiëren van uw Azure Stream Analytics-taak aan Azure Data Lake Storage Gen1 uitvoer.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: seodec18
ms.openlocfilehash: 01741ea56b9e6f55c1393e88fc7991d410c33119
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78379191"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities"></a>Stream Analytics verifiëren voor het Azure Data Lake Storage Gen1 met behulp van beheerde identiteiten

Azure Stream Analytics biedt ondersteuning voor verificatie met Azure Data Lake Storage (ADLS) Gen1 uitvoer beheerde identiteit. De identiteit is een beheerde toepassing in Azure Active Directory die staat voor een bepaalde Stream Analytics-taak geregistreerd en kan worden gebruikt om een specifieke resource te verifiëren. Beheerde identiteiten elimineert u de beperkingen van methoden voor verificatie op basis van een gebruiker, zoals u dat u hoeft te verifiëren vanwege wachtwoord te wijzigen of token verlopen voor voor een gebruiker die zich na negentig dagen voordoen. Daarnaast beheerde identiteiten helpen bij de automatisering van Stream Analytics-taak implementaties die worden uitgevoerd naar Azure Data Lake Storage Gen1.

In dit artikel ziet u drie manieren om beheerde identiteit in te scha kelen voor een Azure Stream Analytics-taak die naar een Azure Data Lake Storage Gen1 wordt uitgevoerd via de Azure Portal, Azure Resource Manager sjabloon implementatie en Azure Stream Analytics hulpprogram ma's voor Visual Studio.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>Azure-portal

1. Begin met het maken van een nieuwe Stream Analytics-taak of door het openen van een bestaande taak in Azure portal. Selecteer in de menu balk aan de linkerkant van het scherm **beheerde identiteit** onder **configureren**.

   ![Stream Analytics beheerde identiteit configureren](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. Selecteer door het **systeem toegewezen beheerde identiteit gebruiken** in het venster dat aan de rechter kant wordt weer gegeven. Klik op **Opslaan** in een service-principal voor de identiteit van de stream Analytics taak in azure Active Directory. De levenscyclus van de identiteit van de zojuist gemaakte worden beheerd door Azure. Wanneer de Stream Analytics-taak wordt verwijderd, wordt de identiteit van de bijbehorende (dat wil zeggen, de service-principal) wordt automatisch verwijderd door Azure.

   Wanneer de configuratie is opgeslagen, wordt de Object-ID (OID) van de service-principal wordt vermeld als de Principal-ID, zoals hieronder weergegeven:

   ![Stream Analytics-service principal-ID](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   De service-principal heeft dezelfde naam als de Stream Analytics-taak. Als de naam van uw taak bijvoorbeeld **MyASAJob**is, is de naam van de service-principal die is gemaakt ook **MyASAJob**.

3. Klik in het venster uitvoer eigenschappen van de ADLS Gen1 uitvoer Sink op de vervolg keuzelijst verificatie modus en selecteer * * beheerde identiteit * *.

4. Vul de rest van de eigenschappen. Zie [een Data Lake Store-uitvoer maken met Stream Analytics](../data-lake-store/data-lake-store-stream-analytics.md)voor meer informatie over het maken van een ADLS-uitvoer. Wanneer u klaar bent, klikt u op **Opslaan**.

   ![Azure Data Lake Storage configureren](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Ga naar de pagina overzicht van de ADLS Gen1 en klik op **Data Explorer**.

   ![Overzicht van Data Lake-opslag configureren](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. Selecteer in het deel venster Data Explorer de optie **toegang** en klik op **toevoegen** in het deel venster toegang.

   ![Data Lake Storage Access configureren](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. Typ de naam van de Service-Principal in het tekstvak in het deel venster **gebruiker of groep selecteren** . Houd er rekening mee dat de naam van de service-principal ook de naam van de bijbehorende Stream Analytics-taak is. Als u te typen van de principal-naam begint, wordt deze weergegeven onder het tekstvak. Kies de gewenste Service Principal Name en klik op **selecteren**.

   ![Selecteer de naam van een service-principal](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. Controleer in het deel venster **machtigingen** de machtigingen **schrijven** en **uitvoeren** en wijs deze toe aan **deze map en alle onderliggende items**. Klik vervolgens op **OK**.

   ![Selecteer schrijven en uitvoeren van machtigingen](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. De service-principal wordt vermeld onder **toegewezen machtigingen** in het deel venster **toegang** , zoals hieronder wordt weer gegeven. U kunt nu teruggaan en uw Stream Analytics-taak starten.

   ![Stream Analytics toegang krijgen tot de lijst in de portal](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Zie [Access Control in azure data Lake Storage gen1](../data-lake-store/data-lake-store-access-control.md)voor meer informatie over de machtigingen voor het bestands systeem van data Lake Storage gen1.

## <a name="stream-analytics-tools-for-visual-studio"></a>Stream Analytics-hulpprogram ma's voor Visual Studio

1. Stel in JobConfig. json de instelling **systeem toegewezen identiteit** in op **waar**.

   ![Beheerde identiteiten voor taak configuratie Stream Analytics](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. Klik in het venster uitvoer eigenschappen van de ADLS Gen1 uitvoer Sink op de vervolg keuzelijst verificatie modus en selecteer * * beheerde identiteit * *.

   ![Beheerde identiteiten voor ADLS-uitvoer](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. Vul de overige eigenschappen in en klik op **Opslaan**.

4. Klik in de query-editor op **verzenden naar Azure** .

   Wanneer u de taak verzendt, voert de hulpprogram ma's twee dingen uit:

   * Maakt automatisch een service-principal voor de identiteit van de Stream Analytics taak in Azure Active Directory. De levenscyclus van de identiteit van de zojuist gemaakte worden beheerd door Azure. Wanneer de Stream Analytics-taak wordt verwijderd, wordt de identiteit van de bijbehorende (dat wil zeggen, de service-principal) wordt automatisch verwijderd door Azure.

   * Stel de machtigingen **schrijven** en **uitvoeren** automatisch in voor het pad ADLS gen1 voorvoegsel dat in de taak wordt gebruikt en wijs dit toe aan deze map en alle onderliggende items.

5. U kunt de Resource Manager-sjablonen met de volgende eigenschap genereren met behulp van [Stream Analytics CI. CD Nuget package](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) versie 1.5.0 of hoger op een build-machine (buiten Visual Studio). Volg de implementatie stappen voor de Resource Manager-sjabloon in de volgende sectie om de Service-Principal op te halen en toegang te verlenen tot de Service-Principal via Power shell.

## <a name="resource-manager-template-deployment"></a>Sjabloonimplementatie van Resource Manager

1. U kunt een *micro soft. StreamAnalytics/streamingjobs-* resource maken met een beheerde identiteit door de volgende eigenschap op te nemen in de resource sectie van uw Resource Manager-sjabloon:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Deze eigenschap vertelt ons Azure Resource Manager maken en beheren van de identiteit voor uw Azure Stream Analytics-taak.

   **Voorbeeld taak**
   
   ```json
   {
     "Name": "AsaJobWithIdentity",
     "Type": "Microsoft.StreamAnalytics/streamingjobs",
     "Location": "West US",
     "Identity": {
       "Type": "SystemAssigned",
     },
     "properties": {
       "sku": {
         "name": "standard"
       },
       "outputs": [
         {
           "name": "string",
           "properties":{
             "datasource": {
               "type": "Microsoft.DataLake/Accounts",
               "properties": {
                 "accountName": "myDataLakeAccountName",
                 "filePathPrefix": "cluster1/logs/{date}/{time}",
                 "dateFormat": "YYYY/MM/DD",
                 "timeFormat": "HH",
                 "authenticationMode": "Msi"
             }
           }
         }
       }
     }
   }
   ```
  
   **Voorbeeld taak respons**

   ```json
   {
    "Name": "mySAJob",
    "Type": "Microsoft.StreamAnalytics/streamingjobs",
    "Location": "West US",
    "Identity": {
      "Type": "SystemAssigned",
        "principalId": "GUID",
        "tenantId": "GUID",
      },
      "properties": {
        "sku": {
          "name": "standard"
        },
     }
   }
   ```

   Noteer de Principal-ID uit het antwoord van de taak toegang verlenen tot de vereiste ADLS-resource.

   De **Tenant-id** is de id van de Azure Active Directory Tenant waar de Service-Principal is gemaakt. De service-principal is gemaakt in de Azure-tenant die wordt vertrouwd door het abonnement.

   Het **type** geeft het type beheerde identiteit aan zoals wordt uitgelegd in typen beheerde identiteiten. Alleen de door het systeem toegewezen type wordt ondersteund.

2. Biedt toegang tot de service-principal met behulp van PowerShell. Als u wilt toegang verleent tot de service-principal via PowerShell, voert u de volgende opdracht uit:

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   De **PrincipalId** is de object-id van de Service-Principal en wordt weer gegeven op het portal scherm wanneer de Service-Principal is gemaakt. Als u de taak met behulp van de sjabloonimplementatie van een Resource Manager-hebt gemaakt, wordt de Object-ID wordt weergegeven in de identiteitseigenschap van de reactie van de taak.

   **Voorbeeld**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   Raadpleeg de documentatie van [set-AzDataLakeStoreItemAclEntry](/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry) voor meer informatie over de bovenstaande Power shell-opdracht.

## <a name="limitations"></a>Beperkingen
Deze functie biedt geen ondersteuning voor het volgende:

1. **Multi tenant-toegang**: de service-principal die is gemaakt voor een bepaalde stream Analytics taak bevindt zich op de Azure Active Directory Tenant waarop de taak is gemaakt en kan niet worden gebruikt voor een resource die zich op een andere Azure Active Directory Tenant bevindt. Daarom kunt u alleen MSI gebruiken op ADLS gen 1-resources die zich in dezelfde Azure Active Directory Tenant bevinden als uw Azure Stream Analytics-taak. 

2. Door **[gebruiker toegewezen identiteit](../active-directory/managed-identities-azure-resources/overview.md)** : wordt niet ondersteund. Dit betekent dat de gebruiker niet in staat is om hun eigen Service-Principal in te voeren om te worden gebruikt door hun Stream Analytics-taak. De service-principal wordt gegenereerd door Azure Stream Analytics.

## <a name="next-steps"></a>Volgende stappen

* [Een Data Lake Store-uitvoer maken met Stream Analytics](../data-lake-store/data-lake-store-stream-analytics.md)
* [Stream Analytics query's lokaal testen met Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Live-gegevens lokaal testen met Azure Stream Analytics-hulpprogram ma's voor Visual Studio](stream-analytics-live-data-local-testing.md) 
