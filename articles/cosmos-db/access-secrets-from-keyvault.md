---
title: Key Vault gebruiken om Azure Cosmos DB-sleutels op te slaan en te openen
description: Gebruik Azure Key Vault om Azure Cosmos DB-verbindingstekenreeksen, sleutels, eindpunten op te slaan en te openen.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 55e6bbc338c1ac6f9ef935b4a3a05c32f2b5e9f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72755215"
---
# <a name="secure-azure-cosmos-keys-using-azure-key-vault"></a>Azure Cosmos-sleutels beveiligen met Azure Key Vault 

Wanneer u Azure Cosmos DB voor uw toepassingen gebruikt, hebt u toegang tot de database, verzamelingen, documenten met behulp van het eindpunt en de sleutel in het configuratiebestand van de app.  Het is echter niet veilig om sleutels en URL rechtstreeks in de toepassingscode te plaatsen, omdat ze beschikbaar zijn in duidelijke tekstindeling voor alle gebruikers. Zorg ervoor dat het eindpunt en de sleutels wel beschikbaar zijn, maar via een beveiligd mechanisme. Azure Key Vault kan u helpen bij het veilig opslaan en beheren van toepassingsgeheimen.

De volgende stappen zijn vereist om Azure Cosmos DB-toegangssleutels op te slaan en te lezen vanuit Key Vault:

* Een sleutelkluis maken  
* Azure Cosmos DB-toegangssleutels toevoegen aan de Key Vault  
* Een Azure-webtoepassing maken  
* De aanvraag registreren & machtigingen verlenen om de Sleutelkluis te lezen  


## <a name="create-a-key-vault"></a>Een sleutelkluis maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).  
2. Selecteer **Een bron maken > Beveiliging > Sleutelkluis**.  
3. Geef in de sectie **Sleutelkluis maken** de volgende gegevens op:  
   * **Naam:** Geef een unieke naam op voor uw Key Vault.  
   * **Abonnement:** Kies het abonnement dat u gebruikt.  
   * Kies **Nieuw** bij **Resourcegroep** en voer de naam van een resourcegroep in.  
   * Kies een locatie in de vervolgkeuzelijst Locatie.  
   * Laat andere opties over aan hun standaardinstellingen.  
4. Selecteer na het opgeven van de bovenstaande gegevens **Maken**.  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>Voeg Azure Cosmos DB-toegangssleutels toe aan de Key Vault.
1. Ga naar de sleutelkluis die u in de vorige stap hebt gemaakt en open het tabblad **Geheimen.**  
2. Selecteer **+Genereren/importeren**, 

   * Selecteer **Handleiding** voor **uploadopties**.
   * Geef een **naam** op voor je geheim
   * Geef de verbindingstekenreeks van uw Cosmos DB-account op in het veld **Waarde.** En selecteer vervolgens **Maken**.

   ![Een geheim maken](./media/access-secrets-from-keyvault/create-a-secret.png)

4. Nadat het geheim is gemaakt, opent u het en kopieert u de **Secret Identifier die zich in de volgende indeling bevindt. U gebruikt deze id in de volgende sectie. 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Een Azure-webtoepassing maken

1. Maak een Azure-webtoepassing of u de app downloaden vanuit de [GitHub-repository.](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/Demo/keyvaultdemo) Het is een eenvoudige MVC applicatie.  

2. Rits de gedownloade toepassing uit en open het **HomeController.cs** bestand. Werk de geheime id in de volgende regel bij:

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. Sla het bestand **op,** **bouw** de oplossing.  
4. Implementeer vervolgens de toepassing naar Azure. Klik met de rechtermuisknop op het project en kies **publiceren**. Maak een nieuw app-serviceprofiel (u de app WebAppKeyVault1 een naam geven) en selecteer **Publiceren**.   

5. Zodra de toepassing is geïmplementeerd. Navigeer vanuit de Azure-portal naar de web-app die u hebt geïmplementeerd en schakel de **beheerde service-identiteit** van deze toepassing in.  

   ![Beheerde service-identiteit](./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png)

Als u de toepassing nu uitvoert, ziet u de volgende fout, omdat u geen toestemming hebt gegeven voor deze toepassing in Key Vault.

![App geïmplementeerd zonder toegang](./media/access-secrets-from-keyvault/app-deployed-without-access.png)

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>De aanvraag registreren & machtigingen verlenen om de Sleutelkluis te lezen

In deze sectie registreert u de toepassing bij Azure Active Directory en geeft u machtigingen voor de toepassing om de Sleutelkluis te lezen. 

1. Navigeer naar de Azure-portal en open de **Sleutelkluis** die u in de vorige sectie hebt gemaakt.  

2. Open **Access-beleid**, selecteer **+Nieuw toevoegen** zoek de web-app die u hebt geïmplementeerd, selecteer machtigingen en selecteer **OK**.  

   ![Toegangsbeleid toevoegen](./media/access-secrets-from-keyvault/add-access-policy.png)

Als je de applicatie uitvoert, kun je het geheim van Key Vault lezen.

![App geïmplementeerd met geheim](./media/access-secrets-from-keyvault/app-deployed-with-access.png)
 
Op dezelfde manier u een gebruiker toevoegen om toegang te krijgen tot de sleutel Vault. Je moet jezelf toevoegen aan de Key Vault door **Toegangsbeleid** te selecteren en vervolgens alle machtigingen te verlenen die je nodig hebt om de toepassing uit Visual studio uit te voeren. Wanneer deze toepassing wordt uitgevoerd vanaf uw bureaublad, neemt u uw identiteit.

## <a name="next-steps"></a>Volgende stappen

* Als u een firewall voor Azure Cosmos DB wilt configureren, raadpleegt u het ondersteuningsartikel [voor firewallondersteuning.](firewall-support.md)
* Zie [Beveiligde toegang met het eindpuntartikel van vNet-service](vnet-service-endpoint.md) als u eindpuntvan de virtuele netwerkservice wilt configureren.
