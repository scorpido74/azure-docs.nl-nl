---
title: Logboekregistratie voor Azure Key Vault | Microsoft Docs
description: Meer informatie over het bewaken van de toegang van uw sleutelkluizen door logboekregistratie voor Azure Key Vault in te schakelen. Hierbij wordt de informatie opgeslagen in een Azure-opslagaccount dat u opgeeft.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: a51e9a628f67269357d42bd1d3af10c1d86f301a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91739779"
---
# <a name="azure-key-vault-logging"></a>Logboekregistratie voor Azure Key Vault

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Nadat u een of meer sleutelkluizen hebt gemaakt, wilt u wellicht controleren hoe en wanneer uw sleutelkluizen toegankelijk zijn en voor wie. U kunt dit doen door logboekregistratie voor Azure Key Vault in te schakelen. Hierbij wordt de informatie opgeslagen in een Azure-opslagaccount dat u opgeeft. Er wordt automatisch een nieuwe container genaamd **insights-logs-auditevent** gemaakt voor uw opgegeven opslagaccount. U kunt ditzelfde opslagaccount gebruiken om logboeken voor meerdere sleutelkluizen te verzamelen.

U kunt uw logboekgegevens (maximaal) tien minuten nadat de sleutelkluisbewerking is uitgevoerd, bekijken. In de meeste gevallen gaat het echter veel sneller.  Het is aan u om uw logboeken in uw opslagaccount te beheren:

* Gebruik standaardmethoden van Azure voor toegangsbeheer om uw logboeken te beveiligen door het aantal gebruikers te beperken dat toegang heeft tot de logboeken.
* Verwijder de logboeken die u niet meer in uw opslagaccount wilt bewaren.

Zie [Wat is Azure Key Vault?](overview.md) voor algemene informatie over Key Vault. Bekijk de [pagina met prijzen](https://azure.microsoft.com/pricing/details/key-vault/) voor informatie over waar Key Vault beschikbaar is. Zie [Azure Monitor voor Key Vault](https://docs.microsoft.com/azure/azure-monitor/insights/key-vault-insights-overview) voor informatie over het gebruik ervan.

## <a name="interpret-your-key-vault-logs"></a>Uw Key Vault-logboeken interpreteren

Afzonderlijke blobs worden opgeslagen als tekst, die is opgemaakt als een JSON-blob. Laten we eens naar een voorbeeld van een logboekvermelding kijken. 

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

De volgende tabel bevat de namen en beschrijvingen van velden:

| Veldnaam | Beschrijving |
| --- | --- |
| **time** |Datum en tijd in UTC. |
| **resourceId** |Azure Resource Manager-resource-id. Voor Key Vault-logboeken is dit altijd de Key Vault-resource-id. |
| **operationName** |Naam van de bewerking, zoals beschreven in de volgende tabel. |
| **operationVersion** |REST-API-versie die door de client is aangevraagd. |
| **category** |Type resultaat. Voor Key Vault-logboeken is `AuditEvent` de enige beschikbare waarde. |
| **resultType** |Resultaat van de REST-API-aanvraag. |
| **resultSignature** |HTTP-status. |
| **resultDescription** |Extra beschrijving van het resultaat, indien beschikbaar. |
| **durationMs** |De tijd die nodig was om de REST-API-aanvraag af te handelen in milliseconden. Hierbij wordt geen rekening gehouden met de netwerklatentie, zodat de tijd die u aan de clientzijde meet mogelijk niet overeenkomt met de tijd die hier wordt weergegeven. |
| **callerIpAddress** |IP-adres van de client die de aanvraag heeft ingediend. |
| **correlationId** |Een optionele GUID die de client kan doorgeven om de logboeken aan de clientzijde te relateren aan (Sleutelkluis-)logboeken aan de servicezijde. |
| **identity** |De identiteit van het token dat is opgegeven in de REST-API-aanvraag. Dit is meestal ‘user’, ‘service principal’ of de combinatie ‘user+appId’, bijvoorbeeld bij een aanvraag via een Azure PowerShell-cmdlet. |
| **properties** |Gegevens die variëren op basis van de bewerking (**operationName**). In de meeste gevallen bevat dit veld clientgegevens (de useragent-tekenreeks die door de client wordt doorgegeven), de exacte URI voor de REST-API-aanvraag en de HTTP-statuscode. Als een object wordt geretourneerd als gevolg van een aanvraag (bijvoorbeeld **KeyCreate** of **VaultGet**), bevat dit veld ook de sleutel-URI (als `id`), kluis-URI of geheim-URI. |

De veldwaarden voor **operationName** hebben de *ObjectVerb*-indeling. Bijvoorbeeld:

* Alle sleutelkluisbewerkingen hebben de `Vault<action>`-indeling, zoals `VaultGet` en `VaultCreate`.
* Alle sleutelbewerkingen hebben de `Key<action>`-indeling, zoals `KeySign` en `KeyList`.
* Alle geheimbewerkingen hebben de `Secret<action>`-indeling, zoals `SecretGet` en `SecretListVersions`.

De volgende tabel bevat de **operationName**-waarden en de bijbehorende REST-API-opdrachten:

### <a name="operation-names-table"></a>Tabel met waarden voor operationName

| operationName | REST-API-opdracht |
| --- | --- |
| **Verificatie** |Verifiëren via het Azure Active Directory-eindpunt |
| **VaultGet** |[Informatie over een sleutelkluis ophalen](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Een sleutelkluis maken of bijwerken](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Een sleutelkluis verwijderen](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Een sleutelkluis bijwerken](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Alle sleutelkluizen in een resourcegroep weergeven](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Een sleutel maken](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Informatie over een sleutel ophalen](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[Een sleutel in een kluis importeren](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[Een back-up van een sleutel maken](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[Een sleutel verwijderen](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[Een sleutel herstellen](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[Aanmelden met een sleutel](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[Verifiëren met een sleutel](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[Een sleutel inpakken](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[Een sleutel uitpakken](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[Versleutelen met een sleutel](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[Ontsleutelen met een sleutel](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[Een sleutel bijwerken](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[De sleutels in een kluis weergeven](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[De versies van een sleutel weergeven](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[Een geheim maken](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[Een geheim ophalen](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Een geheim bijwerken](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Een geheim verwijderen](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Geheimen in een kluis weergeven](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Versies van een geheim weergeven](https://msdn.microsoft.com/library/azure/dn986824.aspx) |
| **VaultAccessPolicyChangedEventGridNotification** | Gebeurtenis gepubliceerd voor wijzigen van toegangsbeleid voor kluis |
| **SecretNearExpiryEventGridNotification** |Gebeurtenis gepubliceerd voor bijna verlopen van geheim |
| **SecretExpiredEventGridNotification** |Gebeurtenis gepubliceerd voor verlopen van geheim |
| **KeyNearExpiryEventGridNotification** |Gebeurtenis gepubliceerd voor bijna verlopen van sleutel |
| **KeyExpiredEventGridNotification** |Gebeurtenis gepubliceerd voor verlopen van sleutel |
| **CertificateNearExpiryEventGridNotification** |Gebeurtenis gepubliceerd voor bijna verlopen van certificaat |
| **CertificateExpiredEventGridNotification** |Gebeurtenis gepubliceerd voor verlopen van certificaat |

## <a name="use-azure-monitor-logs"></a><a id="loganalytics"></a>Azure Monitor-logboeken gebruiken

Met de Key Vault-oplossing in Azure Monitor-logboeken kunt u de `AuditEvent`-logboeken van Key Vault controleren. In Azure Monitor-logboeken kunt u logboekquery’s gebruiken om gegevens te analyseren en de informatie op te halen die u nodig hebt. 

Zie [Azure Key Vault in Azure Monitor](../../azure-monitor/insights/key-vault-insights-overview.md) voor meer informatie.

## <a name="next-steps"></a><a id="next"></a>Volgende stappen

Zie [Azure Key Vault in een webtoepassing gebruiken](tutorial-net-create-vault-azure-web-app.md) voor een zelfstudie over het gebruik van Azure Key Vault in een .NET-webtoepassing.

Zie de [Ontwikkelaarshandleiding voor Azure Key Vault](developers-guide.md) voor het programmeren van verwijzingen.

Zie [Cmdlets voor Azure Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault) voor een lijst met Azure PowerShell 1.0-cmdlets voor Azure Key Vault.
