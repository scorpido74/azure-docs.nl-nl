---
title: Azure HDInsight Een cluster maken - foutwoordenboek
description: Meer informatie over het oplossen van fouten die optreden bij het maken van Azure HDInsight-clusters
author: karkrish
ms.author: v-todmc
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/19/2019
ms.openlocfilehash: b0dc974185ad616d57327e9cc3743db9ecb20e54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302726"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight: fouten bij het maken van clusteren

In dit artikel worden resoluties beschreven voor fouten die u zou kunnen tegenkomen bij het maken van clusters.

> [!NOTE]
> De eerste drie fouten die in dit artikel worden beschreven zijn validatiefouten. Ze kunnen optreden wanneer een Azure HDInsight-product de **CsmDocument_2_0** klasse gebruikt.

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Foutcode: DeploymentDocument 'CsmDocument_2_0' is mislukt voor de validatie

### <a name="error"></a>Fout

"Script actie locatie kan niet\<worden\>geopend URI: SCRIPT ACTION URL "

#### <a name="error-message"></a>Foutbericht

"De externe server heeft een fout geretourneerd: (404) Niet gevonden."

### <a name="cause"></a>Oorzaak

De HDInsight-service heeft geen toegang tot de URL van de scriptactie die u hebt opgegeven als onderdeel van de clusteraanvraag maken. De service ontvangt het vorige foutbericht wanneer deze toegang probeert te krijgen tot de scriptactie.

### <a name="resolution"></a>Oplossing

- Voor een HTTP- of HTTPS-URL controleert u de URL door te proberen deze vanuit een incognitobrowservenster naar deze URL te gaan.
- Voor een WASB-URL moet u ervoor zorgen dat het script bestaat in het opslagaccount dat u in de aanvraag geeft. Zorg er ook voor dat de opslagsleutel voor dit opslagaccount correct is.
- Voor een ADLS-URL moet u ervoor zorgen dat het script in het opslagaccount bestaat.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Foutcode: DeploymentDocument 'CsmDocument_2_0' is mislukt voor de validatie

### <a name="error"></a>Fout

"Script actie locatie kan niet \<\>worden geopend URI: SCRIPT_ACTION_URL "

#### <a name="error-message"></a>Foutbericht

"Het gegeven \<script\> URI SCRIPT_URI is in ADLS, maar dit cluster heeft geen data lake storage principal"

### <a name="cause"></a>Oorzaak

De HDInsight-service heeft geen toegang tot de URL van de scriptactie die u hebt opgegeven als onderdeel van de clusteraanvraag maken. De service ontvangt het vorige foutbericht wanneer deze toegang probeert te krijgen tot de scriptactie.

### <a name="resolution"></a>Oplossing

Voeg het bijbehorende Azure Data Lake Storage Gen 1-account toe aan het cluster. Voeg ook de serviceprincipal toe die toegang heeft tot het Data Lake Storage Gen 1-account aan het cluster.

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>Foutcode: DeploymentDocument 'CsmDocument_2_0' is mislukt voor de validatie

### <a name="error"></a>Fout

"VM-grootte\<\>' CUSTOMER_SPECIFIED_VM_SIZE ' in het verzoek ongeldig\<is\>of niet wordt ondersteund voor rol ' ROL '. Geldige waarden \<zijn: VALID_VM_SIZE_FOR_ROLE."\>

### <a name="cause"></a>Oorzaak

De virtuele machinegrootte die u hebt opgegeven, is niet toegestaan voor de rol. Deze fout kan optreden omdat de waarde van de VM-grootte niet werkt zoals verwacht of niet geschikt is voor de computerrol.

### <a name="resolution"></a>Oplossing

Het foutbericht bevat de geldige waarden voor de VM-grootte. Selecteer een van deze waarden en probeer de clusteraanvraag maken opnieuw.

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Foutcode: OngeldigVirtualNetworkId  

### <a name="error"></a>Fout

"De VirtualNetworkId is niet geldig. VirtualNetworkId\<'\>USER_VIRTUALNETWORKID '*"

### <a name="cause"></a>Oorzaak

De **VirtualNetworkId-waarde** die u tijdens het maken van het cluster hebt opgegeven, bevindt zich niet in de juiste indeling.

### <a name="resolution"></a>Oplossing

Zorg ervoor dat de **virtualnetworkid-** en subnetwaarden in de juiste indeling staan. Ga als resultaat op zoek naar de **virtualnetworkid-waarde:**

1. Ga naar Azure Portal.
1. Selecteer uw virtuele netwerk.
1. Selecteer het **menu-item Eigenschappen.** De **eigenschapresourceID-waarde** is de waarde **VirtualNetworkId.**

Hier is een voorbeeld van een virtuele netwerk-ID:

"/abonnementen/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroepen/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>Foutcode: CustomizationFailedErrorCode

### <a name="error"></a>Fout

"De implementatie van het cluster is mislukt door een fout in de aangepaste scriptactie. Mislukte \<acties:\>SCRIPT_NAME , Ga naar Ambari UI om de fout verder te debuggen."

### <a name="cause"></a>Oorzaak

Het aangepaste script dat u tijdens de clusteraanvraag maken hebt opgegeven, wordt uitgevoerd nadat het cluster is geïmplementeerd. Deze foutcode geeft aan dat er een \<fout\>is opgetreden tijdens de uitvoering van het aangepaste script met de naam SCRIPT_NAME .

### <a name="resolution"></a>Oplossing

Omdat het script uw aangepaste script is, raden we u aan het probleem op te lossen en het script indien nodig opnieuw uit te voeren. Als u de scriptfout wilt oplossen, onderzoekt u de logboeken in de map /var/lib/ambari-agent/*. Of open de pagina **Bewerkingen** in de Gebruikersinterface van Ambari en selecteer vervolgens de **run_customscriptaction** bewerking om de foutgegevens weer te geven.

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Foutcode: OngeldigeDocumentErrorCode

### <a name="error"></a>Fout

"De \<\> \<META_STORE_TYPE Metastore-schemaversie METASTORE_MAJOR_VERSION\> in \<database \<DATABASE_NAME\>\> is niet compatibel met clusterversie CLUSTER_VERSION"

### <a name="cause"></a>Oorzaak

De aangepaste metastore is niet compatibel met de geselecteerde HDInsight-clusterversie. Momenteel ondersteunen HDInsight 4.0-clusters alleen Metastore-versie 3.0 en hoger, terwijl HDInsight 3.6-clusters geen Metastore-versie 3.0 en hoger ondersteunen.

### <a name="resolution"></a>Oplossing

Gebruik alleen Metastore-versies die uw HDInsight-clusterversie ondersteunt. Als u geen aangepaste metastore opgeeft, maakt HDInsight intern een metastore en verwijdert deze vervolgens bij het verwijderen van het cluster.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Foutcode: FailedToConnectWithClusterErrorCode 

### <a name="error"></a>Fout

"Kan geen verbinding maken met het eindpunt van clusterbeheer om de schalingbewerking uit te voeren. Controleer of netwerkbeveiligingsregels de externe toegang tot het cluster niet blokkeren en of de gebruikersinterface van de clusterbeheerder (Ambari) met succes kan worden geopend."

### <a name="cause"></a>Oorzaak

Een firewallregel op uw netwerkbeveiligingsgroep (NSG) blokkeert clustercommunicatie met kritieke Azure-status- en beheerservices.

### <a name="resolution"></a>Oplossing

Als u netwerkbeveiligingsgroepen wilt gebruiken om het netwerkverkeer te beheren, neemt u de volgende acties voordat u HDInsight installeert:

- Identificeer het Azure-gebied dat u voor HDInsight wilt gebruiken.
- Identificeer de IP-adressen die door HDInsight worden vereist. Zie voor meer informatie [HDInsight management IP addresses](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses) (IP-adressen voor HDInsight-beheer).
  - Maak of wijzig de netwerkbeveiligingsgroepen voor het subnet waarop u HDInsight wilt installeren.
  - Voor netwerkbeveiligingsgroepen u binnenkomend verkeer toestaan op poort 443 vanaf de IP-adressen. Deze configuratie zorgt ervoor dat HDInsight-beheerservices het cluster van buiten het virtuele netwerk kunnen bereiken.

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Foutcode: StoragePermissionsBlockedForMsi  

### <a name="error"></a>Fout

"De beheerde identiteit heeft geen machtigingen voor het opslagaccount. Controleer of de rol 'Opslagblob-gegevenseigenaar' is toegewezen aan de beheerde identiteit voor het opslagaccount. Opslag: \</subscriptions/\> Subscription ID\< /resourceGroups/ Resource Group Name\> /providers/Microsoft.Storage/storageAccounts/ \<Storage Account Name\>, Managed Identity: /subscriptions/ \<Subscription ID\> /resourceGroups/ /\< Resource Group Name\> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/ \<User Managed Identity Name\>"

### <a name="cause"></a>Oorzaak

U hebt niet de machtigingen verstrekt die nodig zijn om de identiteit te beheren. De door de gebruiker toegewezen beheerde identiteit heeft niet de rol blobopslagbijdrager in het Azure Data Lake Storage Gen2-opslagaccount.

### <a name="resolution"></a>Oplossing

1. Open Azure Portal.
1. Ga naar uw opslagaccount.
1. Kijk onder **Toegangscontrole (IAM)**.
1. Controleer of de gebruiker de rol Opslagblob-gegevensbijdrage heeft of de rol Opslagblobgegevens-eigenaar die aan hem is toegewezen.

Zie [Machtigingen instellen voor de beheerde identiteit op het Data Lake Storage Gen2-account voor](hdinsight-hadoop-use-data-lake-storage-gen2.md)meer informatie .

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Foutcode: Ongeldige NetworkSecurityGroupSecurityRules  

### <a name="error"></a>Fout

"De beveiligingsregels in de Network\<Security\>Group /subscriptions/ SubscriptionID\> /resourceGroups/<Resource Group naam\<default/providers/Microsoft.Network/networkSecurityGroups/\>Network Security\<Group Name\> \> configured with subnet /subscriptions/\<SubscriptionID /resourceGroups/ Resource\<Group name RG-westeurope-vnet-tomtom-default/providers/Microsoft.Network/virtualNetworks/ Virtual Netwerknaam\>/subnetten/\<Subnetnaam\> staat geen vereiste inkomende en/of uitgaande connectiviteit toe. Ga voor meer informatie naar [Een virtueel netwerk plannen voor Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)of neem contact op met ondersteuning."

### <a name="cause"></a>Oorzaak

Als netwerkbeveiligingsgroepen of door gebruikers gedefinieerde routes (UDR's) binnenkomend verkeer naar uw HDInsight-cluster beheren, moet u ervoor zorgen dat uw cluster kan communiceren met kritieke Azure-status- en beheerservices.

### <a name="resolution"></a>Oplossing

Als u netwerkbeveiligingsgroepen wilt gebruiken om het netwerkverkeer te beheren, neemt u de volgende acties voordat u HDInsight installeert:

- Identificeer het Azure-gebied dat u voor HDInsight wilt gebruiken en maak een veilige lijst met de IP-adressen voor uw regio. Zie [gezondheids- en beheerdiensten: Specifieke regio's voor](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions)meer informatie.
- Identificeer de IP-adressen die HDInsight nodig heeft. Zie [IP-adressen van HDInsight-beheer voor](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)meer informatie.
- Maak of wijzig de netwerkbeveiligingsgroepen voor het subnet waarop u HDInsight wilt installeren. Voor netwerkbeveiligingsgroepen u binnenkomend verkeer toestaan op poort 443 vanaf de IP-adressen. Deze configuratie zorgt ervoor dat HDInsight-beheerservices het cluster van buiten het virtuele netwerk kunnen bereiken.
  
---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Foutcode: clusterinstelling kan geen onderdelen installeren op een of meer hosts

###  <a name="error"></a>Fout

"De clusterinstelling kan geen onderdelen installeren op een of meer hosts. Probeer uw verzoek opnieuw."

### <a name="cause"></a>Oorzaak 

Deze fout wordt meestal gegenereerd wanneer er een tijdelijk probleem of een Azure-storing optreedt.

### <a name="resolution"></a>Oplossing

Controleer de [Azure-statuspagina](https://status.azure.com) op Azure-storingen die van invloed kunnen zijn op de clusterimplementatie. Als er geen uitval is, probeert u de implementatie van het cluster opnieuw.

## <a name="next-steps"></a>Volgende stappen

Zie Fouten bij het [maken van clustercreatie oplossen met Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails)voor meer informatie over het oplossen van fouten in clustercreatie.
