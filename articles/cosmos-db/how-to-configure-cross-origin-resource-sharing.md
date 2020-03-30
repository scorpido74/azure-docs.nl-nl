---
title: Cross-Origin Resource Sharing (CORS) in Azure Cosmos DB
description: In dit artikel wordt beschreven hoe u CORS (Cross-Origin Resource Sharing) configureert in Azure Cosmos DB met azure portal- en Azure Resource Manager-sjablonen.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: dech
ms.openlocfilehash: 7a487cb10965a379a0a418efaa061be88c5d10dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77082991"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>Cross-Origin Resource Sharing (CORS) configureren

Cross-Origin Resource Sharing (CORS) is een HTTP-functie waarmee een webtoepassing die onder het ene domein wordt uitgevoerd, toegang heeft tot bronnen in een ander domein. Webbrowsers implementeren een beveiligingsbeperking die bekend staat als beleid van dezelfde oorsprong, waardoor een webpagina geen API's in een ander domein kan aanroepen. CORS biedt echter een veilige manier om het oorsprongsdomein api's in een ander domein te laten aanroepen. De Core (SQL) API in Azure Cosmos DB ondersteunt nu Cross-Origin Resource Sharing (CORS) met behulp van de header 'allowedOrigins'. Nadat u de CORS-ondersteuning voor uw Azure Cosmos-account hebt ingeschakeld, worden alleen geverifieerde aanvragen geëvalueerd om te bepalen of ze zijn toegestaan volgens de regels die u hebt opgegeven.

U de CORS-instelling (Cross-origin resource sharing) configureren vanuit de Azure-portal of vanuit een Azure Resource Manager-sjabloon. Voor Cosmos-accounts die de Core (SQL)-API gebruiken, ondersteunt Azure Cosmos DB een JavaScript-bibliotheek die werkt in zowel Node.js- als browseromgevingen. Deze bibliotheek kan nu profiteren van CORS-ondersteuning bij gebruik van de Gateway-modus. Er is geen configuratie aan de clientzijde nodig om deze functie te gebruiken. Met CORS-ondersteuning kunnen bronnen uit een browser rechtstreeks toegang krijgen tot Azure Cosmos DB via de [JavaScript-bibliotheek](https://www.npmjs.com/package/@azure/cosmos) of rechtstreeks vanuit de [REST API](https://docs.microsoft.com/rest/api/cosmos-db/) voor eenvoudige bewerkingen.

> [!NOTE]
> CORS-ondersteuning is alleen van toepassing en wordt ondersteund voor de Azure Cosmos DB Core (SQL) API. Het is niet van toepassing op de Azure Cosmos DB API's voor Cassandra, Gremlin of MongoDB, omdat deze protocollen http niet gebruiken voor client-servercommunicatie.

## <a name="enable-cors-support-from-azure-portal"></a>CORS-ondersteuning inschakelen vanuit Azure-portal

Gebruik de volgende stappen om Cross-Origin Resource Sharing in te schakelen met Azure Portal:

1. Navigeer naar uw Azure cosmos DB-account. Open **CORS** het CORS-mes.

2. Geef een door komma's gescheiden lijst met oorsprong op die cross-origine gesprekken kan voeren naar uw Azure Cosmos DB-account. Bijvoorbeeld , `https://www.mydomain.com` `https://mydomain.com`, `https://api.mydomain.com`. U ook een\*wildcard gebruiken om alle oorsprong toe te staan en **Verzenden te selecteren.** 

   > [!NOTE]
   > Momenteel u geen jokertekens gebruiken als onderdeel van de domeinnaam. Bijvoorbeeld `https://*.mydomain.net` formaat wordt nog niet ondersteund. 

   ![Cross origin resource sharing inschakelen met Azure Portal](./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png)

## <a name="enable-cors-support-from-resource-manager-template"></a>CORS-ondersteuning inschakelen vanuit resourcemanagersjabloon

Als u CORS wilt inschakelen met behulp van een resourcemanagersjabloon, voegt u de sectie 'cors' met de eigenschap 'toegestaanOorsprong' toe aan een bestaande sjabloon. De volgende JSON is een voorbeeld van een sjabloon waarmee een nieuw Azure Cosmos-account wordt gemaakt met CORS ingeschakeld.

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "cors": [
      {
        "allowedOrigins": "*"
      }
    ]
  }
}
```

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>De JavaScript-bibliotheek azure cosmos DB gebruiken vanuit een browser

Vandaag de dag heeft de Azure Cosmos DB JavaScript-bibliotheek alleen de CommonJS-versie van de bibliotheek die wordt geleverd met het pakket. Als u deze bibliotheek vanuit de browser wilt gebruiken, moet u een hulpprogramma zoals Rollup of Webpack gebruiken om een browsercompatibele bibliotheek te maken. Bepaalde Node.js-bibliotheken moeten browsermocks voor hen hebben. Het volgende is een voorbeeld van een webpack config-bestand dat de nodige mock-instellingen heeft.

```javascript
const path = require("path");

module.exports = {
  entry: "./src/index.ts",
  devtool: "inline-source-map",
  node: {
    net: "mock",
    tls: "mock"
  },
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist")
  }
};
```
 
Hier is een [codevoorbeeld](https://github.com/christopheranderson/cosmos-browser-sample) dat TypeScript en Webpack gebruikt met de Azure Cosmos DB JavaScript SDK-bibliotheek om een Todo-app te bouwen die realtime updates verzendt wanneer nieuwe items worden gemaakt.
Gebruik als aanbevolen praktijk de primaire sleutel niet om vanuit de browser met Azure Cosmos DB te communiceren. Gebruik in plaats daarvan resourcetokens om te communiceren. Zie [Toegang tot het](secure-access-to-data.md#resource-tokens) DB-artikel beveiligen voor meer informatie over brontokens.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over andere manieren om uw Azure Cosmos-account te beveiligen:

* [Een firewall configureren voor het Azure Cosmos DB-artikel.](how-to-configure-firewall.md)

* [Virtuele netwerk- en subnetgebaseerde toegang configureren voor uw Azure Cosmos DB-account](how-to-configure-vnet-service-endpoint.md)
