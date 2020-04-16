---
title: Eindpunten voor virtuele netwerkservices voor Azure Key Vault - Azure Key Vault | Microsoft Documenten
description: Overzicht van eindpunten voor virtuele netwerkservices voor Key Vault
services: key-vault
author: amitbapat
ms.author: ambapat
manager: rkarlin
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 2a68a50a5d15b9f38407c19494a39a14abfa0a5a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432071"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Eindpunten voor virtuele netwerkservices voor Azure Key Vault

Met de eindpunten voor de virtuele netwerkservice voor Azure Key Vault u de toegang tot een opgegeven virtueel netwerk beperken. Met de eindpunten u ook de toegang tot een lijst met iPv4-adresbereiken (internetprotocol versie 4) beperken. Elke gebruiker die verbinding maakt met uw sleutelkluis van buiten deze bronnen, wordt de toegang geweigerd.

Er is één belangrijke uitzondering op deze beperking. Als een gebruiker zich heeft aangemeld om vertrouwde Microsoft-services toe te staan, worden verbindingen van die services via de firewall verhuurd. Deze services omvatten bijvoorbeeld Office 365 Exchange Online, Office 365 SharePoint Online, Azure Compute, Azure Resource Manager en Azure Backup. Deze gebruikers moeten nog steeds een geldig Azure Active Directory-token presenteren en moeten machtigingen (geconfigureerd als toegangsbeleid) hebben om de gevraagde bewerking uit te voeren. Zie [Eindpunten voor virtuele netwerkservice voor](../../virtual-network/virtual-network-service-endpoints-overview.md)meer informatie.

## <a name="usage-scenarios"></a>Gebruiksscenario's

U [Key Vault-firewalls en virtuele netwerken](network-security.md) standaard configureren om de toegang tot verkeer van alle netwerken (inclusief internetverkeer) te weigeren. U toegang verlenen tot verkeer van specifieke Azure virtuele netwerken en ip-adresbereiken voor openbare internet, zodat u een veilige netwerkgrens voor uw toepassingen bouwen.

> [!NOTE]
> Key Vault-firewalls en virtuele netwerkregels zijn alleen van toepassing op het [gegevensvlak](secure-your-key-vault.md#data-plane-access-control) van Key Vault. Key Vault control plane operations (zoals bewerkingen maken, verwijderen en wijzigen, toegangsbeleid instellen, firewalls instellen en virtuele netwerkregels) worden niet beïnvloed door firewalls en virtuele netwerkregels.

Hier volgen enkele voorbeelden van hoe u serviceeindpunten gebruiken:

* U gebruikt Key Vault om versleutelingssleutels, toepassingsgeheimen en certificaten op te slaan en u wilt de toegang tot uw sleutelkluis vanaf het openbare internet blokkeren.
* U wilt de toegang tot uw sleutelkluis vergrendelen, zodat alleen uw toepassing, of een korte lijst met aangewezen hosts, verbinding kan maken met uw sleutelkluis.
* U hebt een toepassing die wordt uitgevoerd in uw virtuele Azure-netwerk en dit virtuele netwerk is vergrendeld voor alle binnenkomende en uitgaande verkeer. Uw toepassing moet nog steeds verbinding maken met Key Vault om geheimen of certificaten op te halen of cryptografische sleutels te gebruiken.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Key Vault-firewalls en virtuele netwerken configureren

Hier volgen de stappen die nodig zijn om firewalls en virtuele netwerken te configureren. Deze stappen zijn van toepassing of u PowerShell, Azure CLI of de Azure-portal gebruikt.

1. [Schakel Key Vault-logboekregistratie in)](logging.md)om gedetailleerde toegangslogboeken te bekijken. Dit helpt bij diagnostiek, wanneer firewalls en virtuele netwerkregels de toegang tot een sleutelkluis verhinderen. (Deze stap is optioneel, maar wordt ten zeerste aanbevolen.)
2. **Serviceeindpunten inschakelen voor sleutelkluis** voor virtuele doelnetwerken en subnetten.
3. Stel firewalls en virtuele netwerkregels in voor een sleutelkluis om de toegang tot die sleutelkluis van specifieke virtuele netwerken, subnetten en IPv4-adresbereiken te beperken.
4. Als deze sleutelkluis toegankelijk moet zijn voor vertrouwde Microsoft-services, schakelt u de optie in om **Trusted Azure Services** verbinding te laten maken met Key Vault.

Zie [Azure Key Vault-firewalls en virtuele netwerken configureren](network-security.md)voor meer informatie.

> [!IMPORTANT]
> Nadat firewallregels van kracht zijn, kunnen gebruikers alleen key [vault-gegevensvlakbewerkingen](secure-your-key-vault.md#data-plane-access-control) uitvoeren wanneer hun aanvragen afkomstig zijn van toegestane virtuele netwerken of IPv4-adresbereiken. Dit geldt ook voor toegang tot Key Vault vanuit de Azure-portal. Hoewel gebruikers vanuit de Azure-portal naar een sleutelkluis kunnen bladeren, kunnen ze mogelijk geen sleutels, geheimen of certificaten vermelden als hun clientmachine niet in de toegestane lijst staat. Dit heeft ook gevolgen voor de Key Vault Picker van andere Azure-services. Gebruikers kunnen mogelijk een lijst met sleutelkluizen zien, maar geen lijstsleutels, als firewallregels hun clientmachine verhinderen.


> [!NOTE]
> Houd rekening met de volgende configuratiebeperkingen:
> * Er zijn maximaal 127 virtuele netwerkregels en 127 IPv4-regels toegestaan. 
> * Kleine adresbereiken die de voorvoegselgrootte '/31' of '/32' gebruiken, worden niet ondersteund. Configureer deze bereiken in plaats daarvan met behulp van afzonderlijke IP-adresregels.
> * IP-netwerkregels zijn alleen toegestaan voor openbare IP-adressen. IP-adresbereiken gereserveerd voor particuliere netwerken (zoals gedefinieerd in RFC 1918) zijn niet toegestaan in IP-regels. Particuliere netwerken omvatten adressen die beginnen met **10.**, **172.16-31**en **192.168.** 
> * Op dit moment worden alleen IPv4-adressen ondersteund.

## <a name="trusted-services"></a>Vertrouwde services

Hier is een lijst met vertrouwde services die toegang hebben tot een sleutelkluis als de optie **Vertrouwde services toestaan** is ingeschakeld.

|Vertrouwde service|Ondersteunde gebruiksscenario's|
| --- | --- |
|Azure Virtual Machines-implementatieservice|[Implementeer certificaten naar VM's vanuit door de klant beheerde Key Vault.](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)|
|Azure Resource Manager-sjabloonimplementatieservice|[Geef veilige waarden door tijdens de implementatie.](../../azure-resource-manager/templates/key-vault-parameter.md)|
|Azure Disk Encryption-volumeversleutelingsservice|Geef toegang tot BitLocker Key (Windows VM) of DM Passphrase (Linux VM) en Key Encryption Key, tijdens de implementatie van virtuele machines. Hierdoor [u Azure Disk Encryption gebruiken.](../../security/fundamentals/encryption-overview.md)|
|Azure Backup|Back-ups en herstel van relevante sleutels en geheimen toestaan tijdens azure virtual machines-back-up met [Azure Backup.](../../backup/backup-introduction-to-azure-backup.md)|
|Exchange Online & SharePoint Online|Geef toegang tot de klantgegevens voor Azure Storage Service-versleuteling met [klantsleutel.](/microsoft-365/compliance/customer-key-overview)|
|Azure Information Protection|Geef toegang tot de tenantsleutel voor [Azure Information Protection.](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)|
|Azure App Service|[Azure Web App-certificaat implementeren via Key Vault](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Azure SQL Database|[Transparante gegevensversleuteling met Bring Your Own Key-ondersteuning voor Azure SQL Database en Data Warehouse.](../../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current)|
|Azure Storage|[Opslagserviceversleuteling met behulp van door de klant beheerde sleutels in Azure Key Vault](../../storage/common/storage-service-encryption-customer-managed-keys.md).|
|Azure Data Lake Store|[Versleuteling van gegevens in Azure Data Lake Store](../../data-lake-store/data-lake-store-encryption.md) met een door de klant beheerde sleutel.|
|Azure Databricks|[Snelle, eenvoudige en collaboratieve Apache Spark-gebaseerde analyseservice](../../azure-databricks/what-is-azure-databricks.md)|
|Azure API Management|[Certificaten voor Aangepast domein implementeren vanuit Key Vault met MSI](../../api-management/api-management-howto-use-managed-service-identity.md#use-the-managed-service-identity-to-access-other-resources)|
|Azure Data Factory|[Referenties voor gegevensopslag ophalen in Key Vault uit gegevensfabriek](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure Event Hubs|[Toegang verlenen tot een sleutelkluis voor het scenario van door de klant beheerde sleutels](https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key)|
|Azure Service Bus|[Toegang verlenen tot een sleutelkluis voor het scenario van door de klant beheerde sleutels](https://docs.microsoft.com/azure/service-bus-messaging/configure-customer-managed-key)|
|Azure Import/Export| [Door de klant beheerde sleutels gebruiken in Azure Key Vault voor import/exportservice](https://docs.microsoft.com/azure/storage/common/storage-import-export-encryption-key-portal)

> [!NOTE]
> U moet het relevante key vault-toegangsbeleid instellen om de bijbehorende services toegang te geven tot Key Vault.

## <a name="next-steps"></a>Volgende stappen

* [Beveilig uw sleutelkluis)](secure-your-key-vault.md)
* [Azure Key Vault-firewalls en virtuele netwerken configureren](network-security.md)
