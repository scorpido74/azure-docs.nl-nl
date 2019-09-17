---
title: Virtuele netwerk service-eind punten voor Azure Key Vault-Azure Key Vault | Microsoft Docs
description: Overzicht van service-eind punten voor virtuele netwerken voor Key Vault
services: key-vault
author: amitbapat
ms.author: ambapat
manager: rkarlin
ms.date: 01/02/2019
ms.service: key-vault
ms.topic: conceptual
ms.openlocfilehash: b39482f5c753fbfe6cc6663dda4f5381300c2c21
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2019
ms.locfileid: "71017257"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Virtuele netwerk service-eind punten voor Azure Key Vault

Met de service-eind punten voor virtuele netwerken voor Azure Key Vault kunt u de toegang tot een opgegeven virtueel netwerk beperken. Met de eind punten kunt u ook de toegang beperken tot een lijst met IPv4-adresbereiken (Internet Protocol versie 4). Gebruikers die verbinding maken met uw sleutel kluis van buiten deze bronnen, krijgen geen toegang.

Er is een belang rijke uitzonde ring op deze beperking. Als een gebruiker is aangemeld om vertrouwde micro soft-Services toe te staan, kunnen verbindingen van deze services via de firewall worden toegestaan. Deze services omvatten bijvoorbeeld Office 365 Exchange Online, Office 365 share point online, Azure compute, Azure Resource Manager en Azure Backup. Dergelijke gebruikers moeten nog steeds een geldig Azure Active Directory token presen teren en moeten machtigingen hebben (geconfigureerd als toegangs beleid) om de aangevraagde bewerking uit te voeren. Zie [service-eind punten voor virtuele netwerken](../virtual-network/virtual-network-service-endpoints-overview.md)voor meer informatie.

## <a name="usage-scenarios"></a>Gebruiksscenario's

U kunt [Key Vault firewalls en virtuele netwerken](key-vault-network-security.md) configureren om de toegang tot verkeer van alle netwerken (met inbegrip van Internet verkeer) standaard te weigeren. U kunt toegang verlenen aan verkeer van specifieke virtuele Azure-netwerken en open bare IP-adresbereiken voor het Internet, zodat u een beveiligde netwerk grens voor uw toepassingen maakt.

> [!NOTE]
> Key Vault firewalls en regels voor virtuele netwerken zijn alleen van toepassing op het [gegevens vlak](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) van Key Vault. Key Vault besturings vlak bewerkingen (zoals het maken, verwijderen en wijzigen van bewerkingen, het instellen van toegangs beleid, firewalls en regels voor virtuele netwerken) worden niet beïnvloed door firewalls en regels voor virtuele netwerken.

Hier volgen enkele voor beelden van hoe u service-eind punten kunt gebruiken:

* U gebruikt Key Vault om versleutelings sleutels, toepassings geheimen en certificaten op te slaan, en u wilt de toegang tot uw sleutel kluis blok keren via het open bare Internet.
* U de toegang tot uw sleutel kluis wilt vergren delen zodat alleen uw toepassing of een korte lijst met aangewezen hosts verbinding kan maken met uw sleutel kluis.
* U hebt een toepassing die wordt uitgevoerd in uw virtuele Azure-netwerk en dit virtuele netwerk is vergrendeld voor al het binnenkomende en uitgaande verkeer. Uw toepassing moet nog steeds verbinding maken met Key Vault om geheimen of certificaten op te halen, of om cryptografische sleutels te gebruiken.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Key Vault firewalls en virtuele netwerken configureren

Hier volgen de stappen die nodig zijn voor het configureren van firewalls en virtuele netwerken. Deze stappen zijn van toepassing, ongeacht of u Power shell, de Azure CLI of de Azure Portal gebruikt.

1. Schakel [Key Vault logboek registratie](key-vault-logging.md) in om gedetailleerde toegangs logboeken te bekijken. Dit helpt in diagnostische gegevens, wanneer firewalls en regels voor virtuele netwerken toegang tot een sleutel kluis verhinderen. (Deze stap is optioneel, maar wordt nadrukkelijk aanbevolen.)
2. Schakel **service-eind punten in voor de sleutel kluis** voor virtuele netwerken en subnetten die doel zijn.
3. Stel firewalls en regels voor virtuele netwerken in voor een sleutel kluis om de toegang tot de sleutel kluis te beperken op basis van specifieke virtuele netwerken, subnetten en IPv4-adresbereiken.
4. Als deze sleutel kluis toegankelijk moet zijn voor vertrouwde micro soft-Services, schakelt u de optie voor het toestaan van **vertrouwde Azure-Services** om verbinding te maken met Key Vault.

Zie [Azure Key Vault firewalls en virtuele netwerken configureren](key-vault-network-security.md)voor meer informatie.

> [!IMPORTANT]
> Nadat de firewall regels van kracht zijn, kunnen gebruikers alleen Key Vault [gegevensvlak](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) bewerkingen uitvoeren wanneer hun aanvragen afkomstig zijn van toegestane virtuele netwerken of IPv4-adresbereiken. Dit geldt ook voor toegang tot Key Vault vanuit de Azure Portal. Hoewel gebruikers kunnen bladeren naar een sleutel kluis van de Azure Portal, kunnen ze mogelijk geen sleutels, geheimen of certificaten weer geven als hun client computer niet in de lijst met toegestane clients staat. Dit is ook van invloed op de Key Vault kiezer door andere Azure-Services. Gebruikers kunnen mogelijk een lijst met sleutel kluizen zien, maar geen lijst met sleutels als firewall regels hun client computer verhinderen.


> [!NOTE]
> Houd rekening met de volgende configuratie beperkingen:
> * Er zijn Maxi maal 127 virtuele-netwerk regels en 127 IPv4-regels toegestaan. 
> * Kleine adresbereiken die de grootte van het voor voegsel/31 of/32 gebruiken, worden niet ondersteund. In plaats daarvan configureert u deze bereiken met behulp van afzonderlijke IP-adres regels.
> * IP-netwerk regels zijn alleen toegestaan voor open bare IP-adressen. IP-adresbereiken die zijn gereserveerd voor particuliere netwerken (zoals gedefinieerd in RFC 1918) zijn niet toegestaan in IP-regels. Particuliere netwerken bevatten adressen die beginnen met **10.** , **172.16-31**en **192,168.** . 
> * Er worden op dit moment alleen IPv4-adressen ondersteund.

## <a name="trusted-services"></a>Vertrouwde services

Hier volgt een lijst met vertrouwde services die toegang mogen hebben tot een sleutel kluis als de optie **vertrouwde services toestaan** is ingeschakeld.

|Vertrouwde service|Ondersteunde gebruiks scenario's|
| --- | --- |
|Service voor implementatie van Azure Virtual Machines|[Implementeer certificaten op vm's vanuit door de klant beheerde Key Vault](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/).|
|Implementatie service voor Azure Resource Manager-sjabloon|[Beveiligde waarden door geven tijdens de implementatie](../azure-resource-manager/resource-manager-keyvault-parameter.md).|
|Volume Encryption-service Azure Disk Encryption|Toegang tot de BitLocker-sleutel (Windows-VM) of DM-wachtwoordzin (Linux-VM) en sleutel versleutelings sleutel toestaan tijdens de implementatie van de virtuele machine. Hiermee maakt u [Azure Disk Encryption](../security/azure-security-disk-encryption.md)mogelijk.|
|Azure Backup|Het maken van back-ups en het herstellen van relevante sleutels en geheimen tijdens Azure Virtual Machines backup toestaan met behulp van [Azure backup](../backup/backup-introduction-to-azure-backup.md).|
|Exchange Online & Share Point online|Toegang tot de klant sleutel toestaan voor de code ring van Azure Storage-service met de code van de [klant](https://support.office.com/article/Controlling-your-data-in-Office-365-using-Customer-Key-f2cd475a-e592-46cf-80a3-1bfb0fa17697).|
|Azure Information Protection|Toegang tot de Tenant sleutel voor [Azure Information Protection toestaan.](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)|
|Azure App Service|[Implementeer het Azure web app-certificaat via Key Vault](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Azure SQL Database|[Transparent Data Encryption met Bring your own Key ondersteuning voor Azure SQL database en het Data Warehouse](../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current).|
|Azure Storage|[Storage service Encryption door de klant beheerde sleutels gebruiken in azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md).|
|Azure Data Lake Store|[Versleuteling van gegevens in azure data Lake Store](../data-lake-store/data-lake-store-encryption.md) met een door de klant beheerde sleutel.|
|Azure-databricks|[Snelle, eenvoudige en samen werkende, Apache Spark-gebaseerde analyse service](../azure-databricks/what-is-azure-databricks.md)|
|Azure API Management|[Certificaten voor een aangepast domein van Key Vault implementeren met behulp van MSI](../api-management/api-management-howto-use-managed-service-identity.md#use-the-managed-service-identity-to-access-other-resources)|



> [!NOTE]
> U moet de relevante Key Vault toegangs beleid instellen zodat de bijbehorende services toegang krijgen tot Key Vault.

## <a name="next-steps"></a>Volgende stappen

* [Uw Key Vault beveiligen](key-vault-secure-your-key-vault.md)
* [Azure Key Vault firewalls en virtuele netwerken configureren](key-vault-network-security.md)
