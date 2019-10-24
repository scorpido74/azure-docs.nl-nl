---
title: Key Vault gebruiken om Azure Cosmos DB sleutels op te slaan en te openen
description: Gebruik Azure Key Vault om Azure Cosmos DB connection string, sleutels, eind punten op te slaan en te openen.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 55e6bbc338c1ac6f9ef935b4a3a05c32f2b5e9f5
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755215"
---
# <a name="secure-azure-cosmos-keys-using-azure-key-vault"></a>Azure Cosmos-sleutels beveiligen met Azure Key Vault 

Wanneer u Azure Cosmos DB gebruikt voor uw toepassingen, kunt u toegang krijgen tot de data base, verzamelingen en documenten met behulp van het eind punt en de sleutel binnen het configuratie bestand van de app.  Het is echter niet veilig om sleutels en URL rechtstreeks in de toepassings code te plaatsen, omdat ze beschikbaar zijn in de Lees bare tekst indeling voor alle gebruikers. U wilt er zeker van zijn dat het eind punt en de sleutels beschikbaar zijn, maar ook via een beveiligd mechanisme. In Azure Key Vault kunt u toepassings geheimen veilig opslaan en beheren.

De volgende stappen zijn vereist om Azure Cosmos DB toegangs sleutels op te slaan en te lezen vanuit Key Vault:

* Een sleutelkluis maken  
* Azure Cosmos DB toegangs sleutels toevoegen aan de Key Vault  
* Een Azure-webtoepassing maken  
* De toepassing registreren & machtigingen verlenen om de Key Vault te lezen  


## <a name="create-a-key-vault"></a>Een sleutelkluis maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).  
2. Selecteer **een resource maken > beveiligings > Key Vault**.  
3. Geef in de sectie **Sleutelkluis maken** de volgende gegevens op:  
   * **Naam:** Geef een unieke naam op voor de Key Vault.  
   * **Abonnement:** Kies het abonnement dat u gaat gebruiken.  
   * Kies **Nieuw** bij **Resourcegroep** en voer de naam van een resourcegroep in.  
   * Kies een locatie in de vervolg keuzelijst locatie.  
   * Wijzig de standaard instellingen van de andere opties.  
4. Selecteer na het opgeven van de bovenstaande gegevens **Maken**.  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>Azure Cosmos DB toegangs sleutels toevoegen aan de Key Vault.
1. Ga naar het Key Vault dat u in de vorige stap hebt gemaakt en open het tabblad **geheimen** .  
2. Selecteer **+ genereren/importeren**, 

   * Selecteer **hand matig** voor **Upload opties**.
   * Geef een **naam** op voor uw geheim
   * Geef de connection string van uw Cosmos DB-account op in het veld **waarde** . En selecteer vervolgens **maken**.

   ![Een geheim maken](./media/access-secrets-from-keyvault/create-a-secret.png)

4. Nadat het geheim is gemaakt, opent u het en kopieert u de geheime id * * in de volgende indeling. U gebruikt deze id in de volgende sectie. 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Een Azure-webtoepassing maken

1. Een Azure-webtoepassing maken of u kunt de app downloaden vanuit de [github-opslag plaats](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/Demo/keyvaultdemo). Het is een eenvoudige MVC-toepassing.  

2. Pak de gedownloade toepassing uit en open het **HomeController.cs** -bestand. Werk de geheime ID in op de volgende regel:

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. **Sla** het bestand op en **bouw** de oplossing.  
4. Implementeer de toepassing vervolgens naar Azure. Klik met de rechter muisknop op project en kies **publiceren**. Maak een nieuw app service-profiel (u kunt de app WebAppKeyVault1 noemen) en selecteer **publiceren**.   

5. Zodra de toepassing is geïmplementeerd. Ga vanuit het Azure Portal naar de web-app die u hebt geïmplementeerd en schakel de **beheerde service-identiteit** van deze toepassing in.  

   ![Beheerde service-identiteit](./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png)

Als u de toepassing nu wilt uitvoeren, wordt de volgende fout weer gegeven, omdat u geen machtiging hebt gekregen voor deze toepassing in Key Vault.

![App geïmplementeerd zonder toegang](./media/access-secrets-from-keyvault/app-deployed-without-access.png)

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>De toepassing registreren & machtigingen verlenen om de Key Vault te lezen

In deze sectie registreert u de toepassing met Azure Active Directory en geeft u machtigingen voor de toepassing om de Key Vault te lezen. 

1. Ga naar het Azure Portal en open de **Key Vault** die u hebt gemaakt in de vorige sectie.  

2. Open **toegangs beleid**, selecteer **+ nieuwe** zoeken de web-app die u hebt geïmplementeerd, selecteer machtigingen en selecteer **OK**.  

   ![Toegangs beleid toevoegen](./media/access-secrets-from-keyvault/add-access-policy.png)

Als u de toepassing nu uitvoert, kunt u het geheim lezen van Key Vault.

![App geïmplementeerd met geheim](./media/access-secrets-from-keyvault/app-deployed-with-access.png)
 
Op dezelfde manier kunt u een gebruiker toevoegen voor toegang tot de sleutel kluis. U moet uzelf toevoegen aan de Key Vault door **toegangs beleid** te selecteren en vervolgens alle machtigingen te verlenen die u nodig hebt om de toepassing uit te voeren vanuit Visual Studio. Wanneer deze toepassing wordt uitgevoerd vanaf uw bureau blad, neemt u uw identiteit in beslag.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg het artikel over [firewall ondersteuning](firewall-support.md) om een firewall te configureren voor Azure Cosmos db.
* Zie voor meer informatie over het configureren van het service-eind punt van de virtuele netwerk, [beveiligde toegang via het VNet-service-eindpunt](vnet-service-endpoint.md) artikel
