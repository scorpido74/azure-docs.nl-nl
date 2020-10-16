---
title: Een account maken in de Azure Portal
description: Informatie over het maken van een Azure Batch-account in Azure Portal voor het uitvoeren van grootschalige parallelle workloads in de cloud
ms.topic: how-to
ms.date: 06/10/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1205de2b800588b735aeb20d388ba4b64bc6b078
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84711337"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Een Batch-account maken met behulp van Azure Portal

In dit onderwerp wordt beschreven hoe u een Azure Batch-account maakt in de [Azure Portal](https://portal.azure.com)en hoe u de account Eigenschappen kiest die passen bij uw reken scenario. U leert ook waar u belang rijke account eigenschappen kunt vinden, zoals toegangs sleutels en account-Url's.

Zie [batch-service werk stroom en resources](batch-service-workflow-features.md)voor achtergrond informatie over batch-accounts en-scenario's.

## <a name="create-a-batch-account"></a>Batch-account maken

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **een resource maken**en selecteer vervolgens **Compute** en **batch-service**.

1. Voer instellingen in voor **Nieuw Batch-account**. Zie de volgende details.

    ![Batch-account maken][account_portal]

    a. **Abonnement**: het abonnement waarin het Batch-account moet worden gemaakt. Als u slechts één abonnement hebt, is het standaard geselecteerd.

    b. **Resourcegroep**: Selecteer een bestaande resourcegroep voor het nieuwe Batch-account. U kunt er ook voor kiezen om een nieuwe resourcegroep te maken.

    c. **Accountnaam**: de naam die u kiest, moet uniek zijn in de Azure-regio waarin het account wordt gemaakt (zie **Locatie** hieronder). De accountnaam mag alleen kleine letters of cijfers bevatten en moet 3 tot 24 tekens lang zijn.

    d. **Locatie**: de Azure-regio om het Batch-account in te maken. Alleen de regio's die door uw abonnement en resourcegroep worden ondersteund, worden als opties weergegeven.

    e. **Opslag account**: een optioneel Azure Storage account dat u aan uw batch-account koppelt. Een v2-opslagaccount voor algemeen gebruik wordt aanbevolen voor de beste prestaties. Zie het [Overzicht van Batch-functies](accounts.md#azure-storage-accounts) voor alle opslagaccountopties in Batch. Selecteer in de portal een bestaand opslagaccount of maak een nieuw opslagaccount.

      ![Create a storage account][storage_account]

    f. **Groeps toewijzings modus**: op het tabblad **Geavanceerde** instellingen kunt u de groeps toewijzings modus opgeven als **batch-service** of **gebruikers abonnement**. Voor de meeste scenario's kunt u de standaardinstelling **Batch-service** accepteren.

      ![Groepstoewijzingsmodus van Batch][pool_allocation]

1. Selecteer **Maken** om het account te maken.

## <a name="view-batch-account-properties"></a>Eigenschappen van Batch-account weergeven

Zodra het account is gemaakt, selecteert u het account om naar de instellingen en eigenschappen te gaan. U hebt toegang tot alle eigenschappen en accountinstellingen via het linkermenu.

> [!NOTE]
> De naam van het batch-account is de ID en kan niet worden gewijzigd. Als u de naam van een batch-account wilt wijzigen, moet u het account verwijderen en een nieuwe maken met de gewenste naam.

![Pagina Batch-account in Azure Portal][account_blade]

* **Batch-account, URL en sleutels**: wanneer u een toepassing ontwikkelt met de [Batch-API‘s](batch-apis-tools.md#azure-accounts-for-batch-development), hebt u een account-URL en sleutel nodig voor toegang tot de Batch-resources. (Batch ondersteunt ook Azure Active Directory-verificatie.)

    Selecteer **Sleutels** om de toegangsgegevens voor het Batch-account te bekijken.

    ![Batch-accountsleutels in Azure Portal][account_keys]

* Als u de naam en sleutels van het opslagaccount wilt zien dat is gekoppeld aan uw Batch-account, selecteert u **Opslagaccount**.

* Als u wilt zien welke resourcequota van toepassing zijn op het Batch-account, selecteert u **Quota**. Zie [Quota en limieten voor Batch-service](batch-quota-limit.md) voor meer informatie.

## <a name="additional-configuration-for-user-subscription-mode"></a>Aanvullende configuratie voor de modus Gebruikersabonnement

Als u ervoor kiest een Batch-account te maken in de modus Gebruikersabonnement, moet u de volgende aanvullende stappen uitvoeren voordat u het account gaat maken.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Azure Batch toegang geven tot het abonnement (eenmalige bewerking)

Wanneer u uw eerste Batch-account maakt in de modus Gebruikersabonnement, moet u de volgende stappen uitvoeren om uw abonnement te registreren bij Batch. (Als u dit al hebt gedaan, gaat u naar de volgende sectie.)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **alle services**  >  **abonnementen**en selecteer het abonnement dat u wilt gebruiken voor het batch-account.

1. Selecteer op de pagina **Abonnement** de optie **Resourceproviders** en zoek naar **Microsoft.Batch**. Controleer of de resourceprovider **Microsoft.Batch** is geregistreerd in het abonnement. Als deze niet is geregistreerd, selecteert u de koppeling **Registreren**.

    ![Microsoft.Batch-provider registreren][register_provider]

1. Op de pagina **Abonnement** selecteert u **Toegangsbeheer (IAM)** > **Roltoewijzingen** > **Roltoewijzing toevoegen**.

    ![Toegangsbeheer voor abonnement][subscription_access]

1. Selecteer op de pagina **roltoewijzing toevoegen** de rol **Inzender** of **eigenaar** en zoek vervolgens naar de batch-API. Zoek deze tekenreeksen totdat u de API hebt gevonden:
    1. **MicrosoftAzureBatch**.
    1. **Microsoft Azure Batch**. Nieuwere Azure AD-tenants kunnen deze naam gebruiken.
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** is de id voor de Batch-API.

1. Als u de Batch-API hebt gevonden, selecteert u deze en selecteert u **Opslaan**.

    ![Batch-machtigingen toevoegen][add_permission]

### <a name="create-a-key-vault"></a>Een sleutelkluis maken

In de modus gebruikers abonnement is een [Azure Key Vault](../key-vault/general/overview.md) vereist. De Key Vault moeten zich in hetzelfde abonnement en dezelfde regio bevinden als het batch-account dat moet worden gemaakt. 

1. Selecteer in [Azure Portal](https://portal.azure.com) de optie **Nieuw** > **Beveiliging** > **Sleutelkluis**.

1. Voer op de pagina **Key Vault maken** een naam in voor de Key Vault en maak een resource groep in de gewenste regio voor uw batch-account. Laat de overige instellingen op de standaardwaarden staan en selecteer **Maken**.

Wanneer u het batch-account maakt in de modus gebruikers abonnement, geeft u **gebruikers abonnement** op als de pool toewijzings modus, selecteert u de Key Vault en schakelt u het selectie vakje in om Azure batch toegang te verlenen aan de Key Vault.

Als u liever hand matig toegang wilt verlenen aan de Key Vault, gaat u naar de sectie **toegangs beleid** van de Key Vault, selecteert u **toegangs beleid toevoegen** en zoeken naar **Microsoft Azure batch**. Als deze optie is geselecteerd, moet u de **geheime machtigingen** configureren via de vervolg keuzelijst. Aan Azure Batch moet mini maal de machtigingen **Get**, **List**, **set**en **Delete** worden opgegeven.

![Geheime machtigingen voor Azure Batch](./media/batch-account-create-portal/secret-permissions.png)

> [!NOTE]
> Zorg ervoor dat de selectie vakjes **Azure virtual machines voor implementatie** en **Azure Resource Manager voor implementatie van sjabloon** zijn geselecteerd onder **toegangs beleid** voor de gekoppelde **Key Vault** resource.
>
> ![Verplicht Key Vault toegangs beleid](./media/batch-account-create-portal/key-vault-access-policy.png)

### <a name="configure-subscription-quotas"></a>Abonnementquota configureren

Voor gebruikers abonnements batch-accounts moeten kern quota hand matig worden ingesteld. Standard-batch kern quota gelden niet voor accounts in de modus gebruikers abonnement.

1. Selecteer in de [Azure-portal](https://portal.azure.com) uw Batch-account in de modus voor gebruikersabonnementen om de instellingen en eigenschappen ervan weer te geven.
1. Selecteer **Quota** in het linkermenu om de kerngeheugenquota die aan uw Batch-account zijn gekoppeld, te bekijken en configureren.

Zie voor meer informatie over de kern quota van de gebruikers abonnements modus de [batch service quota's en limieten](batch-quota-limit.md).

## <a name="other-batch-account-management-options"></a>Andere beheeropties voor uw Batch-account

Naast het gebruik van Azure Portal kunt u met de volgende hulpprogramma's Batch-accounts maken en beheren:

* [Batch-PowerShell-cmdlets](batch-powershell-cmdlets-get-started.md)
* [Azure-CLI](batch-cli-get-started.md)
* [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [Werkstroom van de batch-service en primaire resources](batch-service-workflow-features.md) als pools, knooppunten, jobs en taken.
* Lees de basisbeginselen van het ontwikkelen van een voor Batch geschikte toepassing met behulp van de [clientbibliotheek Batch .NET](quick-run-dotnet.md) of [Python](quick-run-python.md). Deze snelstarts leiden u stapsgewijs door een voorbeeldtoepassing die gebruikmaakt van de Batch-service voor het uitvoeren van een workload op meerdere rekenknooppunten. U vindt er ook informatie over het gebruik van Azure Storage voor het faseren en ophalen van een workloadbestand.

[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch-account-portal.png
[pool_allocation]: ./media/batch-account-create-portal/batch-pool-allocation.png
[account_keys]: ./media/batch-account-create-portal/batch-account-keys.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[register_provider]: ./media/batch-account-create-portal/register_provider.png
