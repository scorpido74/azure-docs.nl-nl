---
title: Azure Stream Analytics verifiëren voor Azure Data Lake Storage Gen1
description: In dit artikel wordt beschreven hoe u beheerde identiteiten gebruikt om uw Azure Stream Analytics-taak te verifiëren voor Azure Data Lake Storage Gen1-uitvoer.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: seodec18
ms.openlocfilehash: 01741ea56b9e6f55c1393e88fc7991d410c33119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254376"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities"></a>Stream Analytics verifiëren naar Azure Data Lake Storage Gen1 met beheerde identiteiten

Azure Stream Analytics ondersteunt beheerde identiteitsverificatie met Azure Data Lake Storage (ADLS) Gen1-uitvoer. De identiteit is een beheerde toepassing die is geregistreerd in Azure Active Directory en die een bepaalde Stream Analytics-taak vertegenwoordigt en kan worden gebruikt om te verifiëren naar een gerichte bron. Beheerde identiteiten elimineren de beperkingen van op gebruikers gebaseerde verificatiemethoden, zoals het opnieuw verifiëren vanwege wachtwoordwijzigingen of het verlopen van gebruikerstokens die elke 90 dagen plaatsvinden. Daarnaast helpen beheerde identiteiten bij de automatisering van Stream Analytics-taakimplementaties die worden uitgevoerd naar Azure Data Lake Storage Gen1.

In dit artikel vindt u drie manieren om beheerde identiteit in te schakelen voor een Azure Stream Analytics-taak die wordt uitgevoerd naar een Azure Data Lake Storage Gen1 via de Azure-portal, Azure Resource Manager-sjabloonimplementatie en Azure Stream Analytics-hulpprogramma's voor Visual Studio.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>Azure Portal

1. Begin met het maken van een nieuwe Stream Analytics-taak of door een bestaande taak in Azure-portal te openen. Selecteer **Beheerde identiteit** onder **Configureren**in de menubalk aan de linkerkant van het scherm.

   ![Beheerde identiteit van Stream Analytics configureren](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. Selecteer **Beheerde identiteit met systeem gebruiken** in het venster dat aan de rechterkant wordt weergegeven. Klik **op Opslaan** in een serviceprincipal voor de identiteit van de functie Stream Analytics in Azure Active Directory. De levenscyclus van de nieuw gemaakte identiteit wordt beheerd door Azure. Wanneer de taak Stream Analytics wordt verwijderd, wordt de bijbehorende identiteit (dat wil zeggen de serviceprincipal) automatisch verwijderd door Azure.

   Wanneer de configuratie wordt opgeslagen, wordt de Object ID (OID) van de serviceprincipal vermeld als de hoofd-id zoals hieronder weergegeven:

   ![Hoofd-id van de Stream Analytics-service](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   De serviceprincipal heeft dezelfde naam als de functie Stream Analytics. Als de naam van uw taak bijvoorbeeld **MyASAJob**is, is de naam van de serviceprincipal die is gemaakt ook **MyASAJob.**

3. Klik in het venster uitvoereigenschappen van de uitvoersink van ADLS Gen1 op de vervolgkeuzelijst Verificatiemodus en selecteer **Beheerde identiteit **.

4. Vul de rest van de eigenschappen in. Zie [Een Data lake Store-uitvoer maken met streamanalytics](../data-lake-store/data-lake-store-stream-analytics.md)voor meer informatie over het maken van een ADLS-uitvoer. Klik op **Opslaan**als u klaar bent.

   ![Azure Data Lake Storage configureren](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Navigeer naar de overzichtspagina van uw ADLS Gen1 en klik op **Data explorer.**

   ![Overzicht van Gegevensmeeropslag configureren](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. Selecteer **Access** in het deelvenster Gegevensverkenner en klik op **Toevoegen** in het deelvenster Access.

   ![Toegang tot gegevensopslag configureren](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. Typ in het tekstvak in het **deelvenster Gebruiker of groeps selecteren** de naam van de serviceprincipal. Vergeet niet dat de naam van de serviceprincipal ook de naam is van de bijbehorende Stream Analytics-taak. Als u begint met het typen van de hoofdnaam, wordt deze onder het tekstvak weergegeven. Kies de gewenste servicenaam en klik op **Selecteren**.

   ![Een hoofdnaam van de service selecteren](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. Controleer **in** het deelvenster Machtigingen de machtigingen **voor schrijven** en **uitvoeren** en wijs deze toe aan Deze map en **alle kinderen**. Klik vervolgens op **Ok.**

   ![Schrijf- en uitvoermachtigingen selecteren](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. De serviceprincipal wordt vermeld onder **Toegewezen machtigingen** in het **deelvenster Access,** zoals hieronder wordt weergegeven. Je nu teruggaan en je Stream Analytics-taak starten.

   ![Stream Analytics-toegangslijst in portal](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Zie [Toegangsbeheer in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md)voor meer informatie over machtigingen voor Gegevenslake Storage Gen1-bestandssysteem.

## <a name="stream-analytics-tools-for-visual-studio"></a>Tools voor Stream Analytics voor Visual Studio

1. Stel in JobConfig.json **de identiteit gebruiken in** op **True**.

   ![Stream Analytics-taakconfig beheerde identiteiten](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. Klik in het venster uitvoereigenschappen van de uitvoersink van ADLS Gen1 op de vervolgkeuzelijst Verificatiemodus en selecteer **Beheerde identiteit **.

   ![Beheerde identiteiten van ADLS-uitvoer](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. Vul de rest van de eigenschappen in en klik op **Opslaan**.

4. Klik **op Verzenden naar Azure** in de queryeditor.

   Wanneer u de taak indient, doen de tools twee dingen:

   * Hiermee wordt automatisch een serviceprincipal gemaakt voor de identiteit van de functie Stream Analytics in Azure Active Directory. De levenscyclus van de nieuw gemaakte identiteit wordt beheerd door Azure. Wanneer de taak Stream Analytics wordt verwijderd, wordt de bijbehorende identiteit (dat wil zeggen de serviceprincipal) automatisch verwijderd door Azure.

   * Stel automatisch **Schrijf-** en **uitvoermachtigingen** in voor het ADLS Gen1-voorvoegselpad dat in de taak wordt gebruikt en wijs deze toe aan deze map en alle kinderen.

5. U de Resource Manager-sjablonen genereren met de volgende eigenschap met [Behulp van Stream Analytics CI. CD Nuget pakket](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) versie 1.5.0 of hoger op een build machine (buiten Visual Studio). Volg de implementatiestappen van resourcebeheersjablonen in het volgende gedeelte om de serviceprincipal te krijgen en toegang te verlenen tot de serviceprincipal via PowerShell.

## <a name="resource-manager-template-deployment"></a>Implementatie van resourcebeheer-sjabloon

1. U een *Microsoft.StreamAnalytics/streamingjobs-bron* met een beheerde identiteit maken door de volgende eigenschap op te nemen in het resourcegedeelte van uw resourcemanagersjabloon:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Met deze eigenschap moet Azure Resource Manager de identiteit voor uw Azure Stream Analytics-taak maken en beheren.

   **Voorbeeldtaak**
   
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
  
   **Voorbeeldtaakreactie**

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

   Neem nota van de hoofd-ID van de job reactie om toegang te verlenen tot de vereiste ADLS bron.

   De **tenant-id** is de id van de Azure Active Directory-tenant waar de serviceprincipal wordt gemaakt. De serviceprincipal wordt gemaakt in de Azure-tenant die wordt vertrouwd door het abonnement.

   Het **type beheerde** identiteit geeft het type beheerde identiteit aan zoals uitgelegd in typen beheerde identiteiten. Alleen het type Systeemtoegewezen wordt ondersteund.

2. Geef toegang tot de serviceprincipal met PowerShell. Voer de volgende opdracht uit om toegang te geven tot de serviceprincipal via PowerShell:

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   De **PrincipalId** is de object-id van de serviceprincipal en wordt weergegeven op het portalscherm zodra de serviceprincipal is gemaakt. Als u de taak hebt gemaakt met behulp van een resourcemanager-sjabloonimplementatie, wordt de object-id weergegeven in de eigenschap Identiteit van de taakrespons.

   **Voorbeeld**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   Raadpleeg de documentatie [Set-AzDataLakeStoreItemAclEntry](/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry) voor meer informatie over de bovenstaande PowerShell-opdracht.

## <a name="limitations"></a>Beperkingen
Deze functie ondersteunt het volgende niet:

1. Toegang met meerdere tenants: de serviceprincipal die is gemaakt voor een bepaalde Stream Analytics-taak, bevindt zich op de Azure Active **Directory-tenant**waarop de taak is gemaakt en kan niet worden gebruikt tegen een bron die zich op een andere Azure Active Directory-tenant bevindt. Daarom u MSI alleen gebruiken op ADLS Gen 1-resources die zich binnen dezelfde Azure Active Directory-tenant bevinden als uw Azure Stream Analytics-taak. 

2. **[User Assigned Identity](../active-directory/managed-identities-azure-resources/overview.md)**: wordt niet ondersteund. Dit betekent dat de gebruiker niet in staat is om zijn eigen serviceprincipal in te voeren die wordt gebruikt door zijn Stream Analytics-taak. De serviceprincipal wordt gegenereerd door Azure Stream Analytics.

## <a name="next-steps"></a>Volgende stappen

* [Een Data lake Store-uitvoer maken met stream-analyses](../data-lake-store/data-lake-store-stream-analytics.md)
* [Query's van Stream Analytics lokaal testen met Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Live gegevens lokaal testen met Azure Stream Analytics-hulpprogramma's voor Visual Studio](stream-analytics-live-data-local-testing.md) 
