---
title: Beperking van uitgaand netwerk verkeer voor Azure HDInsight-clusters configureren
description: Meer informatie over het configureren van de beperking van uitgaand netwerk verkeer voor Azure HDInsight-clusters.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 829f3e730b4993a6a7f32a9224d3c6c38bd4c06e
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70811956"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall-preview"></a>Uitgaand netwerk verkeer voor Azure HDInsight-clusters configureren met behulp van Firewall (preview-versie)

In dit artikel worden de stappen beschreven voor het beveiligen van uitgaand verkeer van uw HDInsight-cluster met behulp van Azure Firewall. In de onderstaande stappen wordt ervan uitgegaan dat u een Azure Firewall configureert voor een bestaand cluster. Als u een nieuw cluster implementeert en zich achter een firewall bevindt, maakt u eerst uw HDInsight-cluster en subnet en volgt u de stappen in deze hand leiding.

## <a name="background"></a>Achtergrond

Azure HDInsight-clusters worden normaal gesp roken geïmplementeerd in uw eigen virtuele netwerk. Het cluster heeft afhankelijkheden voor services buiten dat virtuele netwerk waarvoor netwerk toegang nodig is.

Er zijn verschillende afhankelijkheden waarvoor binnenkomend verkeer is vereist. Het inkomende beheer verkeer kan niet via een firewall apparaat worden verzonden. De bron adressen voor dit verkeer zijn bekend en worden [hier](hdinsight-management-ip-addresses.md)gepubliceerd. U kunt ook regels voor netwerk beveiligings groepen (NSG) met deze informatie maken om inkomend verkeer naar de clusters te beveiligen.

De afhankelijkheden voor uitgaand verkeer van HDInsight zijn bijna volledig gedefinieerd met FQDN-adressen. Het ontbreken van statische adressen betekent dat netwerk beveiligings groepen (Nsg's) niet kunnen worden gebruikt voor het vergren delen van het uitgaande verkeer van een cluster. De adressen veranderen vaak voldoende, waardoor er geen regels kunnen worden ingesteld op basis van de huidige naam omzetting en voor het instellen van NSG-regels.

De oplossing voor het beveiligen van uitgaande adressen is het gebruik van een firewall apparaat waarmee het uitgaande verkeer op basis van domein namen kan worden beheerd. Azure Firewall kunt het uitgaande HTTP-en HTTPS-verkeer beperken op basis van de FQDN-code van de doel-of [FQDN-Tags](https://docs.microsoft.com/azure/firewall/fqdn-tags).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Azure Firewall configureren met HDInsight

Een samen vatting van de stappen voor het vergren delen van uitgaand verkeer van uw bestaande HDInsight met Azure Firewall zijn:
1. Maak een firewall.
1. Toepassings regels toevoegen aan de firewall
1. Voeg netwerk regels toe aan de firewall.
1. Maak een routeringstabel.

### <a name="create-a-new-firewall-for-your-cluster"></a>Een nieuwe firewall voor uw cluster maken

1. Maak een subnet met de naam **AzureFirewallSubnet** in het virtuele netwerk waarin uw cluster zich bevindt. 
1. Maak een nieuwe firewall **test-FW01** met behulp van [de stappen in de zelf studie: Implementeer en configureer Azure Firewall met behulp](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall)van de Azure Portal.

### <a name="configure-the-firewall-with-application-rules"></a>De firewall configureren met toepassings regels

Maak een toepassings regel verzameling waarmee het cluster belang rijke communicatie kan verzenden en ontvangen.

Selecteer de nieuwe firewall **test-FW01** van de Azure Portal. Klik op **regels** onder **instellingen** > **toepassings regel verzameling** > **toepassings regel verzameling toevoegen**.

![Hoofd Verzameling met toepassingsregels toevoegen](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

Voer de volgende stappen uit op het scherm **toepassings regel toevoegen** :

1. Voer een **naam**en **prioriteit**in en klik op **toestaan** in het vervolg keuzemenu **actie** , en voer de volgende regels in in het **gedeelte FQDN-labels** :

   | **Name** | **Bron adres** | **FQDN-label** | **Opmerkingen** |
   | --- | --- | --- | --- |
   | Rule_1 | * | HDInsight en WindowsUpdate | Vereist voor HDI-Services |

1. Voeg de volgende regels toe aan de **sectie FQDN-doel items** :

   | **Name** | **Bron adres** | **Protocol:Port** | **Doel-FQDN-naam** | **Opmerkingen** |
   | --- | --- | --- | --- | --- |
   | Rule_2 | * | https:443 | login.windows.net | Windows-aanmeldings activiteit toestaan |
   | Rule_3 | * | https:443,http:80 | <storage_account_name.blob.core.windows.net> | Als uw cluster wordt ondersteund door WASB, voegt u een regel toe voor WASB. Als u alleen HTTPS-verbindingen wilt gebruiken, moet u ervoor zorgen dat [beveiligde overdracht vereist](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) is ingeschakeld voor het opslag account. |

1. Klik op **Toevoegen**.

   ![Hoofd Details van toepassings regel verzameling invoeren](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

### <a name="configure-the-firewall-with-network-rules"></a>De firewall met netwerk regels configureren

Maak de netwerk regels om uw HDInsight-cluster correct te configureren.

1. Selecteer de nieuwe firewall **test-FW01** van de Azure Portal.
1. Klik op **regels** onder **instellingen** > **netwerk regel verzameling** > **netwerk regel verzameling toevoegen**.
1. Voer op het scherm **netwerk regel verzameling toevoegen** een **naam**en **prioriteit**in en klik op **toestaan** in het vervolg keuzemenu **actie** .
1. Maak de volgende regels in de sectie **IP-adressen** :

   | **Name** | **Protocol** | **Bron adres** | **Doel adres** | **Doel poort** | **Opmerkingen** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_1 | UDP | * | * | `123` | Time-service |
   | Rule_2 | Any | * | DC_IP_Address_1, DC_IP_Address_2 | `*` | Als u Enterprise Security Package (ESP) gebruikt, voegt u een netwerk regel toe aan de sectie IP-adressen die communicatie met AAD-DS voor ESP-clusters mogelijk maakt. U kunt de IP-adressen van de domein controllers vinden in de sectie AAD-DS in de portal | 
   | Rule_3 | TCP | * | IP-adres van uw Data Lake Storage-account | `*` | Als u Azure Data Lake Storage gebruikt, kunt u in de sectie IP-adressen een netwerk regel toevoegen om een SNI-probleem met ADLS Gen1 en Gen2 te verhelpen. Met deze optie wordt het verkeer naar de firewall doorgestuurd. Dit kan leiden tot hogere kosten voor grote gegevens belasting, maar het verkeer wordt vastgelegd en gecontroleerd in Firewall Logboeken. Bepaal het IP-adres voor uw Data Lake Storage-account. U kunt een Power shell-opdracht gebruiken `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` , bijvoorbeeld om de FQDN om te zetten in een IP-adres.|
   | Rule_4 | TCP | * | * | `12000` | Beschrijving Als u Log Analytics gebruikt, maakt u een netwerk regel in de sectie IP-adressen om communicatie met uw Log Analytics-werk ruimte in te scha kelen. |

1. Maak de volgende regels in de sectie **service Tags** :

   | **Name** | **Protocol** | **Bron adres** | **Service Tags** | **Doel poort** | **Opmerkingen** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_7 | TCP | * | SQL | `1433` | Configureer een netwerk regel in het gedeelte service tags voor SQL waarmee u het SQL-verkeer kunt registreren en controleren, tenzij u service-eind punten voor SQL Server op het HDInsight-subnet hebt geconfigureerd, waardoor de firewall wordt overgeslagen. |

1. Klik op **toevoegen** om het maken van de netwerk regel verzameling te volt ooien.

   ![Hoofd Toepassings regel verzameling invoeren](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

### <a name="create-and-configure-a-route-table"></a>Een route tabel maken en configureren

Maak een route tabel met de volgende vermeldingen:

1. Zes adressen uit [deze lijst met vereiste IP-adressen voor HDInsight-beheer](../hdinsight/hdinsight-management-ip-addresses.md) met een volgende hop van **Internet**:
    1. Vier IP-adressen voor alle clusters in alle regio's
    1. Twee IP-adressen die specifiek zijn voor de regio waarin het cluster is gemaakt
1. Eén virtuele-toestel route voor IP-adres 0.0.0.0/0 met de volgende hop wordt uw Azure Firewall privé-IP-adres.

Gebruik bijvoorbeeld de volgende stappen om de route tabel te configureren voor een cluster dat is gemaakt in de regio VS-centraal.

1. Meld u aan bij Azure Portal.
1. Selecteer uw Azure Firewall **-test-FW01**. Kopieer het **privé-IP-adres** dat wordt weer gegeven op de pagina **overzicht** . Voor dit voor beeld gebruiken we een voor beeld **van een adres van 10.1.1.4**
1. Een nieuwe route tabel maken.
1. Klik op **routes** onder **instellingen**.
1. Klik op **toevoegen** om routes te maken voor de IP-adressen in de onderstaande tabel.

| Routenaam | Adresvoorvoegsel | Volgend hoptype | Volgend hopadres |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | N.v.t. |
| 23.99.5.239 | 23.99.5.239/32 | Internet | N.v.t. |
| 168.61.48.131 | 168.61.48.131/32 | Internet | N.v.t. |
| 138.91.141.162 | 138.91.141.162/32 | Internet | N.v.t. |
| 13.67.223.215 | 13.67.223.215/32 | Internet | N.v.t. |
| 40.86.83.253 | 40.86.83.253/32 | Internet | N.v.t. |
| 0.0.0.0 | 0.0.0.0/0 | Virtueel apparaat | 10.1.1.4 |

De configuratie van de route tabel volt ooien:

1. Wijs de route tabel die u hebt gemaakt in uw HDInsight-subnet toe door te klikken op **subnetten** onder **instellingen** en vervolgens te **koppelen**.
1. Selecteer in het scherm **subnet koppelen** het virtuele netwerk waarin het cluster is gemaakt en het Hdinsight- **subnet** dat u hebt gebruikt voor uw HDInsight-cluster.
1. Klik op **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Edge-knoop punt of aangepast toepassings verkeer

Met de bovenstaande stappen kan het cluster zonder problemen worden uitgevoerd. U moet nog steeds afhankelijkheden configureren voor uw aangepaste toepassingen die worden uitgevoerd op de rand knooppunten, indien van toepassing.

Toepassings afhankelijkheden moeten worden geïdentificeerd en aan de Azure Firewall of de route tabel worden toegevoegd.

Routes moeten worden gemaakt voor het toepassings verkeer om asymmetrische routerings problemen te voor komen.

Als uw toepassingen andere afhankelijkheden hebben, moeten ze worden toegevoegd aan uw Azure Firewall. Maak toepassings regels om HTTP/HTTPS-verkeer en netwerk regels voor alle andere toe te staan.

## <a name="logging"></a>Logboekregistratie

Azure Firewall kunt logboeken naar een aantal verschillende opslag systemen verzenden. Volg de stappen in [de zelf studie voor instructies over het configureren van logboek registratie voor uw firewall: Azure Firewall logboeken en metrische gegevens](../firewall/tutorial-diagnostics.md)bewaken.

Als u de logboek registratie-instellingen hebt voltooid en u gegevens wilt Log Analytics, kunt u het geblokkeerde verkeer bekijken met een query zoals de volgende:

```
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Het integreren van uw Azure Firewall met Azure Monitor-Logboeken is handig wanneer u voor het eerst van een toepassing werkt wanneer u niet op de hoogte bent van alle toepassings afhankelijkheden. U kunt meer informatie over Azure Monitor logboeken van het [analyseren van logboek gegevens in azure monitor](../azure-monitor/log-query/log-query-overview.md)

## <a name="access-to-the-cluster"></a>Toegang tot het cluster
Nadat de firewall is geïnstalleerd, kunt u het interne eind punt (`https://<clustername>-int.azurehdinsight.net`) gebruiken om toegang te krijgen tot de Ambari vanuit het VNET. Als u het open bare eind`https://<clustername>.azurehdinsight.net`punt () of SSH`<clustername>-ssh.azurehdinsight.net`-eind punt () wilt gebruiken, zorg er dan voor dat u de juiste routes in de route tabel en de NSG-instellingen hebt ingesteld om te voor komen dat het Assymetric-routerings probleem [hier](https://docs.microsoft.com/azure/firewall/integrate-lb)wordt uitgelegd.

## <a name="configure-another-network-virtual-appliance"></a>Een ander virtueel netwerk apparaat configureren

>[!Important]
> De volgende informatie is **alleen** vereist als u een andere virtuele netwerk apparaten (NVA) dan Azure firewall wilt configureren.

Met de vorige instructies kunt u Azure Firewall configureren voor het beperken van uitgaand verkeer van uw HDInsight-cluster. Azure Firewall wordt automatisch geconfigureerd om verkeer toe te staan voor veel van de algemene belang rijke scenario's. Als u een ander virtueel netwerk apparaat wilt gebruiken, moet u hand matig een aantal extra functies configureren. Houd het volgende in acht als uw virtuele netwerk apparaat configureren:

* Services die geschikt zijn voor service-eind punten moeten worden geconfigureerd met Service-eind punten.
* Afhankelijkheden van IP-adressen zijn voor niet-HTTP/S-verkeer (TCP-en UDP-verkeer).
* FQDN HTTP/HTTPS-eind punten kunnen worden geplaatst op uw NVA-apparaat.
* Joker teken-HTTP/HTTPS-eind punten zijn afhankelijkheden die kunnen variëren op basis van een aantal kwalificaties.
* Wijs de route tabel toe die u hebt gemaakt voor uw HDInsight-subnet.

### <a name="service-endpoint-capable-dependencies"></a>Afhankelijkheden voor service-eind punten

| **Endpoint** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>Afhankelijkheden van IP-adressen

| **Endpoint** | **Details** |
|---|---|
| \*:123 | NTP-klok controle. Verkeer wordt gecontroleerd op meerdere eind punten op poort 123 |
| [Hier](hdinsight-management-ip-addresses.md) gepubliceerde ip's | Dit zijn de HDInsight-service |
| Privé-IP-adressen van AAD-DS voor ESP-clusters |
| \*: 16800 voor KMS Windows-activering |
| \*12000 voor Log Analytics |

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS-afhankelijkheden

>[!Important]
> De onderstaande lijst bevat alleen enkele van de belangrijkste FQDN-namen. U kunt de volledige lijst met FQDN-namen ophalen voor het configureren van uw NVA [in dit bestand](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json).

| **Endpoint**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                |
| ocsp.digicert.com:80                                                |

## <a name="next-steps"></a>Volgende stappen

* [Azure HDInsight Virtual Network-architectuur](hdinsight-virtual-network-architecture.md)
