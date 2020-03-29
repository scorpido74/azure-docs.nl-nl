---
title: Azure Spring Cloud verifiëren met Key Vault in GitHub-acties
description: Sleutelkluis gebruiken met CI/CD-werkstroom voor Azure Spring Cloud met GitHub-acties
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/20/2019
ms.openlocfilehash: 78cd5945e394219be0551bbe97afef07f18b61f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945471"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>Azure Spring Cloud verifiëren met Key Vault in GitHub-acties
Key vault is een veilige plek om sleutels op te slaan. Enterprise-gebruikers moeten referenties opslaan voor CI/CD-omgevingen in het bereik dat ze beheren. De sleutel om referenties in de sleutelkluis te krijgen, moet worden beperkt tot het bereik van resources.  Het heeft alleen toegang tot de key vault scope, niet tot het hele Azure-bereik. Het is als een sleutel die alleen een sterke doos kan openen, niet een master key die alle deuren in een gebouw kan openen. Het is een manier om een sleutel te krijgen met een andere sleutel, die nuttig is in een CICD-workflow. 

## <a name="generate-credential"></a>Referenties genereren
Voer de opdracht hieronder uit op uw lokale machine om een sleutel te genereren om toegang te krijgen tot de sleutelkluis:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
Het bereik dat `--scopes` door de parameter is opgegeven, beperkt de toegang tot de sleutel tot de resource.  Het kan alleen toegang tot de sterke doos.

Met resultaten:
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Sla de resultaten vervolgens op in **GitHub-geheimen** zoals beschreven in [Het instellen van uw GitHub-repository en verifieer met Azure.](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate)

## <a name="add-access-policies-for-the-credential"></a>Toegangsbeleid voor de referentie toevoegen
De bovenstaande referenties kunnen alleen algemene informatie over de Key Vault krijgen, niet de inhoud die deze opslaat.  Als u geheimen wilt opslaan in de Key Vault, moet u toegangsbeleid instellen voor de referentie.

Ga naar het **Key Vault-dashboard** in Azure-portal, klik op het menu **Toegangsbeheer** en open het tabblad **Roltoewijzingen.** Selecteer **Apps** voor **Tekst** en `This resource` voor **bereik**.  U ziet de referentie die u in de vorige stap hebt gemaakt:

 ![Toegangsbeleid instellen](./media/github-actions/key-vault1.png)

Kopieer bijvoorbeeld de `azure-cli-2020-01-19-04-39-02`referentienaam. Open het menu **Toegangsbeleid** en klik op de koppeling **Toegangsbeleid toevoegen.**  Selecteer `Secret Management` voor **Sjabloon**en selecteer **Principal**. Plak de naam van de referenties in het invoervak **Principal**/**Select:**

 ![Selecteer](./media/github-actions/key-vault2.png)

 Klik **op** de knop Toevoegen in het dialoogvenster **Toegangsbeleid toevoegen** en klik vervolgens op **Opslaan**.

## <a name="generate-full-scope-azure-credential"></a>Azure-referentie met volledige scope genereren
Dit is de sleutel om alle deuren in het gebouw te openen. De procedure is vergelijkbaar met de vorige stap, maar hier wijzigen we het bereik om de hoofdsleutel te genereren:

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

Nogmaals, resultaten:
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Kopieer de hele JSON-tekenreeks.  Bo terug naar **Key Vault** dashboard. Open het menu **Geheimen** en klik op de knop **Genereren/importeren.** Voer de geheime naam `AZURE-CRENDENTIALS-FOR-SPRING`in, zoals . Plak de JSON-referentietekenreeks in het vak **Waarde.** Mogelijk merkt u dat het invoervak met waarde een tekstveld met één regel is, in plaats van een tekstgebied met meerdere regels.  U daar de volledige JSON-tekenreeks plakken.

 ![Volledige scopereferentie](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>Referenties combineren in GitHub-acties
Stel de referenties in die worden gebruikt wanneer de CICD-pijplijn wordt uitgevoerd:

```
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}           # Strong box key you generated in the first step
    - uses: Azure/get-keyvault-secrets@v1.0
      with:
        keyvault: "zlhe-test"
        secrets: "AZURE-CREDENTIALS-FOR-SPRING"           # Master key to open all doors in the building
      id: keyvaultaction
    - uses: azure/login@v1
      with:
        creds: ${{ steps.keyvaultaction.outputs.AZURE-CREDENTIALS-FOR-SPRING }}
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud             # Spring CLI commands from here
          az spring-cloud list

```

## <a name="next-steps"></a>Volgende stappen
* [Spring Cloud GitHub-acties](./spring-cloud-howto-github-actions.md)