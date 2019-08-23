---
title: Een account maken in de Azure-portal - Azure Batch| Microsoft Docs
description: Informatie over het maken van een Azure Batch-account in Azure Portal voor het uitvoeren van grootschalige parallelle workloads in de cloud
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d4bf7b894a23f0054485ddb6dbb91c4df2122222
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907330"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Een Batch-account maken met behulp van Azure Portal

Meer informatie over het maken van een Azure Batch-account in de [Azure Portal][azure_portal]en het kiezen van de account eigenschappen die passen bij uw reken scenario. Lees waar u belangrijke accounteigenschappen, zoals toegangssleutels en account-URL's, kunt vinden.

Zie [Overzicht van de functies](batch-api-basics.md) voor achtergrondinformatie over Batch-accounts en scenario's.

## <a name="create-a-batch-account"></a>Batch-account maken

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Meld u aan bij [Azure Portal][azure_portal].

1. Selecteer **Resource maken** > **Compute** > **Batch-service**.

    ![Batch in de Marketplace][marketplace_portal]

1. Voer instellingen in voor **Nieuw Batch-account**. Zie de volgende details.

    ![Batch-account maken][account_portal]

    a. **Abonnement**: Het abonnement waarin het Batch-account moet worden gemaakt. Als u slechts één abonnement hebt, is het standaard geselecteerd.

    b. **Resourcegroep**: Een bestaande resourcegroep voor het nieuwe Batch-account. U kunt ook optioneel een nieuwe resourcegroep maken.

    c. **Accountnaam**: De naam die u kiest, moet uniek zijn in de Azure-regio waarin het account wordt gemaakt (zie **Locatie** hieronder). De accountnaam mag alleen kleine letters of cijfers bevatten en moet 3 tot 24 tekens lang zijn.

    d. **Locatie**: De Azure-regio om het Batch-account in te maken. Alleen de regio's die door uw abonnement en resourcegroep worden ondersteund, worden als opties weergegeven.

    e. **Opslagaccount**: Een optioneel Azure Storage-account voor algemeen gebruik dat u koppelt aan het Batch-account. Een v2-opslagaccount voor algemeen gebruik wordt aanbevolen voor de beste prestaties. Zie het [Overzicht van Batch-functies](batch-api-basics.md#azure-storage-account) voor alle opslagaccountopties in Batch. Selecteer in de portal een bestaand opslagaccount of maak een nieuw opslagaccount.

      ![Create a storage account][storage_account]

    f. **Pooltoewijzingsmodus**: Op het tabblad met **Geavanceerde** instellingen kunt u de groepstoewijzingsmodus **Batch-service** of **Gebruikersabonnement** opgeven. Voor de meeste scenario's kunt u de standaardinstelling **Batch-service** accepteren.

      ![Groepstoewijzingsmodus van Batch][pool_allocation]

1. Selecteer **Maken** om het account te maken.

## <a name="view-batch-account-properties"></a>Eigenschappen van Batch-account weergeven

Zodra het account is gemaakt, selecteert u het account om naar de instellingen en eigenschappen te gaan. U hebt toegang tot alle eigenschappen en accountinstellingen via het linkermenu.

![Pagina Batch-account in Azure Portal][account_blade]

* **Naam, URL en sleutels voor het Batch-account**: Wanneer u een toepassing ontwikkelt met de [Batch-API's](batch-apis-tools.md#azure-accounts-for-batch-development), hebt u een account-URL en -sleutel nodig voor toegang tot de Batch-resources. (Batch ondersteunt ook Azure Active Directory-verificatie.)

    Selecteer **Sleutels** om de toegangsgegevens voor het Batch-account te bekijken.

    ![Batch-accountsleutels in Azure Portal][account_keys]

* Als u de naam en sleutels van het opslagaccount wilt zien dat is gekoppeld aan uw Batch-account, selecteert u **Opslagaccount**.

* Als u wilt zien welke resourcequota van toepassing zijn op het Batch-account, selecteert u **Quota**. Zie [Quota en limieten voor Batch-service](batch-quota-limit.md) voor meer informatie.

## <a name="additional-configuration-for-user-subscription-mode"></a>Aanvullende configuratie voor de modus Gebruikersabonnement

Als u ervoor kiest een Batch-account te maken in de modus Gebruikersabonnement, moet u de volgende aanvullende stappen uitvoeren voordat u het account gaat maken.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Azure Batch toegang geven tot het abonnement (eenmalige bewerking)

Wanneer u uw eerste Batch-account maakt in de modus Gebruikersabonnement, moet u de volgende stappen uitvoeren om uw abonnement te registreren bij Batch. (Als u dit eerder hebt gedaan, gaat u naar de volgende sectie.)

1. Meld u aan bij [Azure Portal][azure_portal].

1. Selecteer **Meer services** > **Abonnementen** en selecteer het abonnement dat u wilt gebruiken voor het Batch-account.

1. Selecteer op de pagina **Abonnement** de optie **Resourceproviders** en zoek naar **Microsoft.Batch**. Controleer of de resourceprovider **Microsoft.Batch** is geregistreerd in het abonnement. Als deze niet is geregistreerd, selecteert u de koppeling **Registreren**.

    ![Microsoft.Batch-provider registreren][register_provider]

1. Op de pagina **Abonnement** selecteert u **Toegangsbeheer (IAM)**  > **Roltoewijzingen** > **Roltoewijzing toevoegen**.

    ![Toegangsbeheer voor abonnement][subscription_access]

1. Selecteer op de pagina **Roltoewijzing toevoegen** de rol **Bijdrager** en zoek naar de Batch API. Zoek deze tekenreeksen totdat u de API hebt gevonden:
    1. **MicrosoftAzureBatch**.
    1. **Microsoft Azure Batch**. Nieuwere Azure AD-tenants kunnen deze naam gebruiken.
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** is de id voor de Batch-API.

1. Als u de Batch-API hebt gevonden, selecteert u deze en selecteert u **Opslaan**.

    ![Batch-machtigingen toevoegen][add_permission]

### <a name="create-a-key-vault"></a>Een sleutelkluis maken

In de modus Gebruikersabonnement is een Azure-sleutelkluis vereist die behoort tot dezelfde resourcegroep als het Batch-account dat moet worden gemaakt. Zorg ervoor dat de resourcegroep zich bevindt in een regio waarin Batch [beschikbaar](https://azure.microsoft.com/regions/services/) is en die uw abonnement ondersteunt.

1. Selecteer in [het Azure Portal][azure_portal] **nieuwe** > **beveiligings** > **Key Vault**.

1. Typ op de pagina **Sleutelkluis maken** een naam voor de sleutelkluis en maak een resourcegroep in de gewenste regio voor uw Batch-account. Laat de overige instellingen op de standaardwaarden staan en selecteer **Maken**.

Wanneer u het batch-account maakt in de modus gebruikers abonnement, gebruikt u de resource groep voor de sleutel kluis. Geef **gebruikers abonnement** op als Pool toewijzings modus, selecteer de sleutel kluis en schakel het selectie vakje in om Azure batch toegang te geven tot de sleutel kluis. 

Als u liever hand matig toegang tot de sleutel kluis wilt verlenen, gaat u naar de sectie **toegangs beleid** van de sleutel kluis en selecteert u **toegangs beleid toevoegen** en zoeken naar **Microsoft Azure batch**. Als deze optie is geselecteerd, moet u de **geheime machtigingen** configureren via de vervolg keuzelijst. Aan Azure Batch moet mini maal de machtigingen **Get**, **List**, **set**en **Delete** worden opgegeven.

![Geheime machtigingen voor Azure Batch](./media/batch-account-create-portal/secret-permissions.png)

### <a name="configure-subscription-quotas"></a>Abonnementquota configureren

Kerngeheugenquota worden niet standaard ingesteld voor Batch-accounts van gebruikersabonnementen. Kerngeheugenquota moeten handmatig worden ingesteld, omdat Batch-kerngeheugenquota standaard niet van toepassing zijn op accounts in de modus voor gebruikersabonnementen.

1. Selecteer in de [Azure Portal][azure_portal]uw batch-account voor de gebruikers abonnements modus om de instellingen en eigenschappen ervan weer te geven.

1. Selecteer **Quota** in het linkermenu om de kerngeheugenquota die aan uw Batch-account zijn gekoppeld, te bekijken en configureren.

Raadpleeg [Quota en limieten voor Batch-service](batch-quota-limit.md) voor meer informatie over kerngeheugenquota in de modus gebruikersabonnementen.

## <a name="other-batch-account-management-options"></a>Andere beheeropties voor uw Batch-account

Naast het gebruik van Azure Portal kunt u met de volgende hulpprogramma's Batch-accounts maken en beheren:

* [Batch-PowerShell-cmdlets](batch-powershell-cmdlets-get-started.md)
* [Azure-CLI](batch-cli-get-started.md)
* [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Volgende stappen

* Zie [Overzicht van Batch-functies](batch-api-basics.md) voor meer informatie over de concepten en functies van de Batch-service. In het artikel worden de primaire Batch-resources zoals pools, rekenknooppunten, jobs en taken besproken en vindt u een overzicht van de servicefuncties voor grootschalige rekenworkloads.
* Lees de basisbeginselen van het ontwikkelen van een voor Batch geschikte toepassing met behulp van de [clientbibliotheek Batch .NET](quick-run-dotnet.md) of [Python](quick-run-python.md). Deze snelstarts leiden u stapsgewijs door een voorbeeldtoepassing die gebruikmaakt van de Batch-service voor het uitvoeren van een workload op meerdere rekenknooppunten. U vindt er ook informatie over het gebruik van Azure Storage voor het faseren en ophalen van een workloadbestand.

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[marketplace_portal]: ./media/batch-account-create-portal/marketplace-batch.png
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch-account-portal.png
[pool_allocation]: ./media/batch-account-create-portal/batch-pool-allocation.png
[account_keys]: ./media/batch-account-create-portal/batch-account-keys.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[register_provider]: ./media/batch-account-create-portal/register_provider.png
