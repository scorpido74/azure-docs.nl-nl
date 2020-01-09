---
title: Een cluster fout woordenlijst maken
description: Meer informatie over het maken van een cluster fout woordenlijst.
ms.reviewer: hrasheed
author: karkrish
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: troubleshooting
ms.date: 11/19/2019
ms.author: v-todmc
ms.openlocfilehash: e537014f741196024c24dd6ee98af0d8af2e1b31
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483061"
---
# <a name="hdinsight-cluster-creation-errors"></a>HDInsight: fouten bij het maken van een cluster

In dit artikel worden oplossingen beschreven voor fouten die zijn opgetreden bij het maken van clusters. 

> [!NOTE]
> De eerste drie fouten in de onderstaande lijst treden op als gevolg van validatie fouten wanneer de klasse CsmDocument_2_0 wordt gebruikt door een HDInsight-product.



## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Fout code: DeploymentDocument ' CsmDocument_2_0 ' is niet gevalideerd

### <a name="error-script-action-location-cannot-be-accessed-uriscript-action-url"></a>**Fout**: de locatie van de script actie kan geen toegang krijgen tot de URI:\<script actie-URL\>

**Fout bericht: de externe server heeft een fout geretourneerd: (404) niet gevonden. "**

### <a name="cause"></a>Oorzaak
De URL van de script actie die als onderdeel van de aanvraag voor het maken van een cluster wordt gegeven, is niet toegankelijk vanuit de HDInsight-service. Het bericht ' ErrorMessage ' wordt weer gegeven wanneer we proberen om de script actie te openen.

### <a name="resolution"></a>Resolutie 
  - Voor een HTTP/HTTPS-URL kunt u controleren door te proberen de URL te openen vanuit een incognito-browser venster. 
  - Controleer voor een WASB-URL of het script bestaat in het opslag account dat is opgegeven in de aanvraag. Zorg ervoor dat de opslag sleutel voor dit opslag account juist is. 
  - Zorg ervoor dat het script in het opslag account bestaat voor een ADLS-URL.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Fout code: DeploymentDocument ' CsmDocument_2_0 ' is niet gevalideerd

### <a name="error-script-action-location-cannot-be-accessed-uri-script-action-url"></a>**Fout**: de locatie van de script actie kan geen toegang krijgen tot de URI: \<script actie-URL\>

**Fout bericht: de opgegeven script-URI \<URI van het SCRIPT\> bevindt zich in ADLS, maar dit cluster heeft geen data Lake Storage-Principal**

### <a name="cause"></a>Oorzaak
De URL van de script actie die wordt gegeven als onderdeel van de aanvraag voor het maken van een cluster, is niet toegankelijk vanuit de HDInsight-service. Het bericht ' ErrorMessage ' wordt weer gegeven wanneer we proberen om de script actie te openen. 

### <a name="resolution"></a>Resolutie

Zorg ervoor dat het bijbehorende Azure Data Lake Store gen 1-account wordt toegevoegd aan het cluster. De service-principal die wordt gebruikt voor toegang tot het Azure Data Lake Store gen 1-account wordt ook toegevoegd aan het cluster. 

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>Fout code: DeploymentDocument ' CsmDocument_2_0 ' is niet gevalideerd

### <a name="error-vm-size-customer-specified-vm-size-provided-in-the-request-is-invalid-or-not-supported-for-role-role-valid-values-are-valid-vm-size-for-role"></a>**Fout**: de VM-grootte\<opgegeven VM-grootte\>in de aanvraag is ongeldig of wordt niet ondersteund voor de rol\<Role\>. Geldige waarden zijn: \<geldige VM-grootte voor de functie\>.

### <a name="cause"></a>Oorzaak
De door de klant opgegeven VM-grootten zijn niet toegestaan voor de rol. Dit kan waar zijn omdat de waarde voor de VM-grootte niet werkt zoals verwacht of niet geschikt is voor de computer functie. 

### <a name="resolution"></a>Resolutie
In het fout bericht worden de geldige waarden voor de VM-grootte weer gegeven. Selecteer een van deze geldige waarden en voer de aanvraag voor het maken van het cluster opnieuw uit. 

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Fout code: InvalidVirtualNetworkId  

### <a name="error-the-virtualnetworkid-is-not-valid-virtualnetworkid-user_virtualnetworkid"></a>**Fout**: de VirtualNetworkId is ongeldig. VirtualNetworkId '\<USER_VIRTUALNETWORKID\>' *

### <a name="cause"></a>Oorzaak
De **VirtualNetworkId** -waarde die wordt opgegeven tijdens het maken van het cluster heeft niet de juiste indeling. 

### <a name="resolution"></a>Resolutie
Zorg ervoor dat **VirtualNetworkId** en subnet de juiste indeling hebben. Als u de waarde voor **VirtualnetworkId** wilt ophalen, gaat u naar de Azure Portal, selecteert u het virtuele netwerk en selecteert u vervolgens **Eigenschappen** in het menu. De eigenschap **ResourceID** is de waarde van **VirtualNetworkId** . 

Een voor beeld van een virtuele netwerk-ID is: 

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet" 

---

## <a name="error-code-customizationfailederrorcode"></a>Fout code: CustomizationFailedErrorCode

### <a name="error-cluster-deployment-failed-due-to-an-error-in-the-custom-script-action-failed-actions-script_name-please-go-to-ambari-ui-to-further-debug-the-failure"></a>**Fout**: de implementatie van het cluster is mislukt vanwege een fout in de aangepaste script actie. Mislukte acties: \<SCRIPT_NAME\>, gaat u naar de Ambari-gebruikers interface om de fout op te lossen.

### <a name="cause"></a>Oorzaak
Het aangepaste script van de gebruiker dat is gegeven tijdens de aanvraag voor het maken van een cluster wordt uitgevoerd nadat het cluster is geïmplementeerd. Deze fout code geeft aan dat er een fout is opgetreden tijdens het uitvoeren van dit aangepaste script met de naam \<SCRIPT_NAME\>.   

### <a name="resolution"></a>Resolutie
Omdat dit het aangepaste script van de gebruiker is, moeten gebruikers het probleem oplossen en het script indien nodig opnieuw uitvoeren. Raadpleeg de logboeken in de map/var/lib/ambari-agent/* om de script fout op te lossen. U kunt ook de pagina bewerkingen openen in de Ambari-gebruikers interface en vervolgens de **run_customscriptionaction** bewerking selecteren om de fout gegevens weer te geven. 

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Fout code: InvalidDocumentErrorCode

### <a name="error-the-meta_store_type-metastore-schema-version-metastore_major_version-in-database-database_name-is-incompatible-with-cluster-version-cluster_version"></a>**Fout**: de \<META_STORE_TYPE\> meta Store-schema versie \<METASTORE_MAJOR_VERSION\> in de data base \<database_name\> is niet compatibel met de cluster versie \<CLUSTER_VERSION\>

### <a name="cause"></a>Oorzaak
De aangepaste meta Store is niet compatibel met de geselecteerde versie van het HDInsight-cluster. HDInsight 4,0-cluster ondersteunt momenteel alleen meta Store versie 3. *x*en HDInsight 3,6 biedt geen ondersteuning voor meta Store versie 3. *x* of hoger. 

### <a name="resolution"></a>Resolutie
Zorg ervoor dat u alleen meta Store-versies gebruikt die worden ondersteund door elke versie van het HDInsight-cluster. Als er geen aangepaste meta Store is opgegeven, maakt HDInsight intern een meta Store. Deze meta Store wordt echter automatisch verwijderd wanneer het cluster wordt verwijderd. 

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Fout code: FailedToConnectWithClusterErrorCode 

### <a name="error-unable-to-connect-to-cluster-management-endpoint-to-perform-scaling-operation-verify-that-network-security-rules-are-not-blocking-external-access-to-the-cluster-and-that-the-cluster-manager-ambari-ui-can-be-successfully-accessed"></a>**Fout**: kan geen verbinding maken met het eind punt van het cluster beheer om een schaal bewerking uit te voeren. Controleer of de netwerk beveiligings regels geen externe toegang tot het cluster blok keren en of de Ambari-gebruikers interface (cluster manager) kan worden geopend.

### <a name="cause"></a>Oorzaak
U hebt een firewall regel op uw netwerk beveiligings groep (NSG) die cluster communicatie blokkeert met de essentiële Azure-status-en beheer Services. 

### <a name="resolution"></a>Resolutie
Als u van plan bent om **netwerk beveiligings groepen** te gebruiken om netwerk verkeer te beheren, voert u de volgende acties uit voordat u HDInsight installeert: 
  - Bepaal de Azure-regio die u wilt gebruiken voor HDInsight. 
  - Identificeer de IP-adressen die zijn vereist voor HDInsight. Zie voor meer informatie [HDInsight management IP addresses](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses) (IP-adressen voor HDInsight-beheer). 
    - Maak of wijzig de netwerk beveiligings groepen voor het subnet waarop u HDInsight wilt installeren. 
    - **Netwerk beveiligings groepen:**  **Binnenkomende** verkeer op poort **443** van de IP-adressen toestaan. Dit zorgt ervoor dat HDInsight-beheer Services het cluster kunnen bereiken van buiten het virtuele netwerk. 

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Fout code: StoragePermissionsBlockedForMsi  

### <a name="error-the-managed-identity-does-not-have-permissions-on-the-storage-account-please-verify-that-storage-blob-data-owner-role-is-assigned-to-the-managed-identity-for-the-storage-account-storage-subscriptions-subscription-id-resourcegroups-resource-group-name-providersmicrosoftstoragestorageaccounts-storage-account-name-managed-identity-subscriptions-subscription-id-resourcegroups--resource-group-name-providersmicrosoftmanagedidentityuserassignedidentities-user-managed-identity-name"></a>**Fout**: de beheerde identiteit heeft geen machtigingen voor het opslag account. Controleer of de rol Storage BLOB data owner is toegewezen aan de beheerde identiteit voor het opslag account. Opslag:/Subscriptions/\<abonnements-ID\>/resourceGroups/\< naam van de resource groep\>/providers/Microsoft.Storage/storageAccounts/\<naam van het opslag account\>, beheerde identiteit:/Subscriptions/\<abonnements-ID\>/resourceGroups//\< resource groep naam\>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/\<door de gebruiker beheerde identiteits naam\>

### <a name="cause"></a>Oorzaak
De vereiste machtigingen zijn niet opgegeven voor het beheren van de **identiteit.** De door de **gebruiker toegewezen beheerde identiteit** heeft geen Blob Storage rol inzender op ADLS Gen2-opslag account. 

### <a name="resolution"></a>Resolutie

Open de Azure Portal, ga naar uw opslag account, kijk onder **Access Control (IAM)** , en zorg ervoor dat de rol gegevens eigenaar van de opslag-BLOB of de Gegevensbeheerderrol ' toegewezen ' is ingesteld voor toegang tot de door de **gebruiker toegewezen beheerde identiteit** voor het abonnement. Zie [machtigingen voor de beheerde identiteit instellen op het data Lake Storage Gen2-account](hdinsight-hadoop-use-data-lake-storage-gen2.md)voor meer informatie. 

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Fout code: InvalidNetworkSecurityGroupSecurityRules  

### <a name="error-the-security-rules-in-the-network-security-group-subscriptionssubscriptionidresourcegroupsresource-group-name-defaultprovidersmicrosoftnetworknetworksecuritygroupsnetwork-security-group-name-configured-with-subnet-subscriptionssubscriptionidresourcegroupsresource-group-name-rg-westeurope-vnet-tomtom-defaultprovidersmicrosoftnetworkvirtualnetworksvirtual-network-namesubnetssubnet-name"></a>**Fout**: de beveiligings regels in de netwerk beveiligings groep/Subscriptions/\<SubscriptionID >\/resourceGroups/<Resource Group name> default/providers/micro soft. Network/networkSecurityGroups/\<naam van de netwerk beveiligings groep\> geconfigureerd met subnet/Subscriptions/\<SubscriptionID >\/resourceGroups/\<resource groeps naam > RG-Europa West-vnet-TomTom-default\/-providers/micro soft. Network/virtualNetworks/\<virtueel De netwerk naam >\/subnetten/\<Subnetnaam\> 
de vereiste binnenkomende en/of uitgaande connectiviteit is niet toegestaan. Ga voor meer informatie naar [een virtueel netwerk voor Azure HDInsight plannen](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)of neem contact op met de ondersteuning. * *

### <a name="cause"></a>Oorzaak
Als u gebruikmaakt van netwerk beveiligings groepen (Nsg's) of door de gebruiker gedefinieerde routes (Udr's) voor het beheren van inkomend verkeer naar uw HDInsight-cluster, moet u ervoor zorgen dat uw cluster kan communiceren met de essentiële Azure-status-en beheer Services.

### <a name="resolution"></a>Resolutie
Als u van plan bent om **netwerk beveiligings groepen** te gebruiken om netwerk verkeer te beheren, voert u de volgende acties uit voordat u HDInsight installeert: 
  - Identificeer de Azure-regio die u wilt gebruiken voor HDInsight en maak een veilige lijst met de IP-adressen voor uw regio: [status-en beheer Services: specifieke regio's](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions).
  - Identificeer de IP-adressen die zijn vereist voor HDInsight. Zie [IP-adressen voor HDInsight-beheer](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)voor meer informatie. 
  - Maak of wijzig de netwerk beveiligings groepen voor het subnet waarop u HDInsight wilt installeren. **Netwerk beveiligings groepen**: Allow inkomend verkeer op poort **443** van de IP-adressen. Dit zorgt ervoor dat HDInsight-beheer Services het cluster kunnen bereiken van buiten het virtuele netwerk.
  
---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Fout code: het installeren van de onderdelen op een of meer hosts door het cluster installatie programma is mislukt

###  <a name="errorcluster-setup-failed-to-install-components-on-one-or-more-hosts-please-retry-your-request"></a>**Fout**: de installatie van de onderdelen op een of meer hosts door het cluster is mislukt. Probeer de aanvraag opnieuw uit te voeren. 

### <a name="cause"></a>Oorzaak 
Normaal gesp roken wordt deze fout gegenereerd als er een tijdelijk probleem is of er sprake is van een onderbreking van Azure 

### <a name="resolution"></a>Resolutie

Controleer de [Azure-status](https://status.azure.com/status) pagina voor mogelijke Azure-uitval die van invloed kunnen zijn op de cluster implementatie. Als er geen storingen zijn, voert u de cluster implementatie opnieuw uit. 

## <a name="next-steps"></a>Volgende stappen

Zie problemen met het maken van [clusters oplossen met Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails)voor meer inzicht in het oplossen van problemen met het maken van een cluster.
