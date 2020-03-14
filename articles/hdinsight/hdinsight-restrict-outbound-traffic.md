---
title: Beperking van uitgaand netwerk verkeer configureren-Azure HDInsight
description: Meer informatie over het configureren van de beperking van uitgaand netwerk verkeer voor Azure HDInsight-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 6e0c98cffef06fb6d6345fc2b23bbc22715909b4
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370182"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall"></a>Uitgaand netwerk verkeer voor Azure HDInsight-clusters configureren met behulp van Firewall

In dit artikel worden de stappen beschreven voor het beveiligen van uitgaand verkeer van uw HDInsight-cluster met behulp van Azure Firewall. In de onderstaande stappen wordt ervan uitgegaan dat u een Azure Firewall configureert voor een bestaand cluster. Als u een nieuw cluster implementeert en zich achter een firewall bevindt, maakt u eerst uw HDInsight-cluster en subnet en volgt u de stappen in deze hand leiding.

## <a name="background"></a>Achtergrond

Azure HDInsight-clusters worden normaal gesp roken geïmplementeerd in uw eigen virtuele netwerk. Het cluster heeft afhankelijkheden voor services buiten dat virtuele netwerk waarvoor netwerk toegang nodig is.

Er zijn verschillende afhankelijkheden waarvoor binnenkomend verkeer is vereist. Het inkomende beheer verkeer kan niet via een firewall apparaat worden verzonden. De bron adressen voor dit verkeer zijn bekend en worden [hier](hdinsight-management-ip-addresses.md)gepubliceerd. U kunt ook regels voor netwerk beveiligings groepen (NSG) met deze informatie maken om inkomend verkeer naar de clusters te beveiligen.

De afhankelijkheden voor uitgaand verkeer van HDInsight zijn bijna volledig gedefinieerd met FQDN-adressen. Het ontbreken van statische adressen betekent dat netwerk beveiligings groepen (Nsg's) niet kunnen worden gebruikt voor het vergren delen van het uitgaande verkeer van een cluster. De adressen veranderen vaak voldoende, waardoor er geen regels kunnen worden ingesteld op basis van de huidige naam omzetting en voor het instellen van NSG-regels.

De oplossing voor het beveiligen van uitgaande adressen is het gebruik van een firewall apparaat waarmee het uitgaande verkeer op basis van domein namen kan worden beheerd. Azure Firewall kunt het uitgaande HTTP-en HTTPS-verkeer beperken op basis van de FQDN-code van de doel-of [FQDN-Tags](../firewall/fqdn-tags.md).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Azure Firewall configureren met HDInsight

Een samen vatting van de stappen voor het vergren delen van uitgaand verkeer van uw bestaande HDInsight met Azure Firewall zijn:

1. Maak een subnet.
1. Maak een firewall.
1. Toepassings regels toevoegen aan de firewall
1. Voeg netwerk regels toe aan de firewall.
1. Maak een routeringstabel.

### <a name="create-new-subnet"></a>Nieuw subnet maken

Maak een subnet met de naam **AzureFirewallSubnet** in het virtuele netwerk waarin uw cluster zich bevindt.

### <a name="create-a-new-firewall-for-your-cluster"></a>Een nieuwe firewall voor uw cluster maken

Maak een firewall met de naam **test-FW01** met behulp van de stappen in de hand leiding voor **het implementeren van de firewall** [: Implementeer en configureer Azure Firewall met behulp van de Azure Portal](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).

### <a name="configure-the-firewall-with-application-rules"></a>De firewall configureren met toepassings regels

Maak een toepassings regel verzameling waarmee het cluster belang rijke communicatie kan verzenden en ontvangen.

1. Selecteer de nieuwe firewall **test-FW01** van de Azure Portal.

1. Navigeer naar **instellingen** > **regels** > **verzameling van toepassings regels** >  **+ verzameling toepassings regels toevoegen**.

    ![Titel: toepassings regel verzameling toevoegen](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

1. Geef op het scherm **toepassings regel toevoegen** de volgende informatie op:

    **Bovenste sectie**

    | Eigenschap|  Waarde|
    |---|---|
    |Naam| FwAppRule|
    |Prioriteit|200|
    |Actie|Toestaan|

    **Sectie FQDN-Tags**

    | Naam | Bron adres | FQDN-label | Opmerkingen |
    | --- | --- | --- | --- |
    | Rule_1 | * | WindowsUpdate en HDInsight | Vereist voor HDI-Services |

    **Sectie FQDN-doel items**

    | Naam | Bron adressen | Protocol: poort | Doel-FQDN-naam | Opmerkingen |
    | --- | --- | --- | --- | --- |
    | Rule_2 | * | https:443 | login.windows.net | Windows-aanmeldings activiteit toestaan |
    | Rule_3 | * | https:443 | login.microsoftonline.com | Windows-aanmeldings activiteit toestaan |
    | Rule_4 | * | https:443,http:80 | storage_account_name. blob. core. Windows. net | Vervang `storage_account_name` door de werkelijke naam van het opslag account. Als uw cluster wordt ondersteund door WASB, voegt u een regel toe voor WASB. Als u alleen HTTPS-verbindingen wilt gebruiken, moet u ervoor zorgen dat ["beveiligde overdracht vereist"](../storage/common/storage-require-secure-transfer.md) is ingeschakeld op het opslag account. |

   ![Titel: Details van toepassings regel verzameling invoeren](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

1. Selecteer **Toevoegen**.

### <a name="configure-the-firewall-with-network-rules"></a>De firewall met netwerk regels configureren

Maak de netwerk regels om uw HDInsight-cluster correct te configureren.

1. Ga verder met de vorige stap, navigeer naar **netwerk regel verzameling** >  **+ verzameling netwerk regels toevoegen**.

1. Geef in het scherm **verzameling van netwerk regels toevoegen** de volgende informatie op:

    **Bovenste sectie**

    | Eigenschap|  Waarde|
    |---|---|
    |Naam| FwNetRule|
    |Prioriteit|200|
    |Actie|Toestaan|

    **Sectie IP-adressen**

    | Naam | Protocol | Bron adressen | Doel adressen | Doelpoorten | Opmerkingen |
    | --- | --- | --- | --- | --- | --- |
    | Rule_1 | UDP | * | * | 123 | Time-service |
    | Rule_2 | Alle | * | DC_IP_Address_1, DC_IP_Address_2 | * | Als u Enterprise Security Package (ESP) gebruikt, voegt u een netwerk regel toe aan de sectie IP-adressen die communicatie met AAD-DS voor ESP-clusters mogelijk maakt. U kunt de IP-adressen van de domein controllers vinden in de sectie AAD-DS in de portal |
    | Rule_3 | TCP | * | IP-adres van uw Data Lake Storage-account | * | Als u Azure Data Lake Storage gebruikt, kunt u een netwerk regel toevoegen in de sectie IP-adressen om een SNI-probleem op te lossen met ADLS Gen1 en Gen2. Met deze optie wordt het verkeer naar de firewall gerouteerd. Dit kan leiden tot hogere kosten voor grote gegevens belasting, maar het verkeer wordt vastgelegd en gecontroleerd in Firewall Logboeken. Bepaal het IP-adres voor uw Data Lake Storage-account. U kunt een Power shell-opdracht zoals `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` gebruiken om de FQDN om te zetten in een IP-adres.|
    | Rule_4 | TCP | * | * | 12000 | Beschrijving Als u Log Analytics gebruikt, maakt u een netwerk regel in de sectie IP-adressen om communicatie met uw Log Analytics-werk ruimte in te scha kelen. |

    **Sectie Service Tags**

    | Naam | Protocol | Bron adressen | Service Tags | Doel poorten | Opmerkingen |
    | --- | --- | --- | --- | --- | --- |
    | Rule_7 | TCP | * | SQL | 1433 | Configureer een netwerk regel in het gedeelte service tags voor SQL waarmee u het SQL-verkeer kunt registreren en controleren, tenzij u service-eind punten voor SQL Server op het HDInsight-subnet hebt geconfigureerd, waardoor de firewall wordt omzeild. |

   ![Titel: toepassings regel verzameling invoeren](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

1. Selecteer **Toevoegen**.

### <a name="create-and-configure-a-route-table"></a>Een route tabel maken en configureren

Maak een route tabel met de volgende vermeldingen:

* Alle IP-adressen van de [status-en beheer Services: alle regio's](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-all-regions) met het volgende hop-type **Internet**.

* Twee IP-adressen voor de regio waarin het cluster is gemaakt op basis van de [status-en beheer Services: specifieke regio's](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-specific-regions) met het volgende hop-type **Internet**.

* Eén virtuele-toestel route voor IP-adres 0.0.0.0/0 met de volgende hop wordt uw Azure Firewall privé-IP-adres.

Gebruik bijvoorbeeld de volgende stappen om de route tabel te configureren voor een cluster dat is gemaakt in de regio VS-Oost VS:

1. Selecteer uw Azure Firewall **-test-FW01**. Kopieer het **privé-IP-adres** dat wordt weer gegeven op de pagina **overzicht** . In dit voor beeld gebruiken we een voor beeld **van een adres van 10.0.2.4**.

1. Ga vervolgens naar **alle services** > **netwerk** > **route tabellen** en **Maak een route tabel**.

1. Ga vanuit de nieuwe route naar **instellingen** > **routes** >  **+ toevoegen**. Voeg de volgende routes toe:

| Routenaam | Adresvoorvoegsel | Volgend hoptype | Adres van de volgende hop |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | N.v.t. |
| 23.99.5.239 | 23.99.5.239/32 | Internet | N.v.t. |
| 168.61.48.131 | 168.61.48.131/32 | Internet | N.v.t. |
| 138.91.141.162 | 138.91.141.162/32 | Internet | N.v.t. |
| 13.82.225.233 | 13.82.225.233/32 | Internet | N.v.t. |
| 40.71.175.99 | 40.71.175.99/32 | Internet | N.v.t. |
| 0.0.0.0 | 0.0.0.0/0 | Virtueel apparaat | 10.0.2.4 |

De configuratie van de route tabel volt ooien:

1. Wijs de route tabel die u hebt gemaakt aan uw HDInsight-subnet toe door **subnetten** onder **instellingen**te selecteren.

1. Selecteer **+ koppelen**.

1. Selecteer in het scherm **subnet koppelen** het virtuele netwerk waarin het cluster is gemaakt en het **subnet** dat u hebt gebruikt voor uw HDInsight-cluster.

1. Selecteer **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Edge-knoop punt of aangepast toepassings verkeer

Met de bovenstaande stappen kan het cluster zonder problemen worden uitgevoerd. U moet nog steeds afhankelijkheden configureren voor uw aangepaste toepassingen die worden uitgevoerd op de rand knooppunten, indien van toepassing.

Toepassings afhankelijkheden moeten worden geïdentificeerd en aan de Azure Firewall of de route tabel worden toegevoegd.

Routes moeten worden gemaakt voor het toepassings verkeer om asymmetrische routerings problemen te voor komen.

Als uw toepassingen andere afhankelijkheden hebben, moeten ze worden toegevoegd aan uw Azure Firewall. Maak toepassings regels om HTTP/HTTPS-verkeer en netwerk regels voor alle andere toe te staan.

## <a name="logging-and-scale"></a>Logboek registratie en-schaal

Azure Firewall kunt logboeken naar een aantal verschillende opslag systemen verzenden. Volg de stappen in de [zelf studie: Monitor Azure firewall logboeken en metrische gegevens](../firewall/tutorial-diagnostics.md)voor instructies over het configureren van logboek registratie voor uw firewall.

Als u de logboek registratie-instellingen hebt voltooid, kunt u het geblokkeerde verkeer met een query als volgt weer geven als u gegevens wilt Log Analytics.

```Kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Het integreren van uw Azure Firewall met Azure Monitor-Logboeken is handig wanneer u voor het eerst van een toepassing werkt wanneer u niet op de hoogte bent van alle toepassings afhankelijkheden. U kunt meer informatie over Azure Monitor logboeken van het [analyseren van logboek gegevens in azure monitor](../azure-monitor/log-query/log-query-overview.md)

Zie [Dit](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits) document of Raadpleeg de [Veelgestelde vragen](../firewall/firewall-faq.md)voor meer informatie over de schaal limieten van Azure firewall en het aanvragen van verg Roten.

## <a name="access-to-the-cluster"></a>Toegang tot het cluster

Nadat de firewall is ingesteld, kunt u het interne eind punt (`https://CLUSTERNAME-int.azurehdinsight.net`) gebruiken om toegang te krijgen tot de Ambari vanuit het virtuele netwerk.

Als u het open bare eind punt (`https://CLUSTERNAME.azurehdinsight.net`) of SSH-eind punt (`CLUSTERNAME-ssh.azurehdinsight.net`) wilt gebruiken, moet u ervoor zorgen dat u de juiste routes in de route tabel en NSG-regels hebt om te voor komen dat het asymmetrische routerings probleem [hier](../firewall/integrate-lb.md)wordt uitgelegd. In dit geval moet u het IP-adres van de client in de regels voor binnenkomende NSG toestaan en dit ook toevoegen aan de door de gebruiker gedefinieerde route tabel waarbij de volgende hop is ingesteld als `internet`. Als deze niet correct is ingesteld, ziet u een time-outfout.

## <a name="configure-another-network-virtual-appliance"></a>Een ander virtueel netwerk apparaat configureren

> [!Important]
> De volgende informatie is **alleen** vereist als u een andere virtuele netwerk apparaten (NVA) dan Azure firewall wilt configureren.

Met de vorige instructies kunt u Azure Firewall configureren voor het beperken van uitgaand verkeer van uw HDInsight-cluster. Azure Firewall wordt automatisch geconfigureerd om verkeer toe te staan voor veel van de algemene belang rijke scenario's. Als u een ander virtueel netwerk apparaat wilt gebruiken, moet u hand matig een aantal extra functies configureren. Houd het volgende in aanmerking bij het configureren van uw virtuele netwerk apparaat:

* Services die geschikt zijn voor service-eind punten moeten worden geconfigureerd met Service-eind punten.
* Afhankelijkheden van IP-adressen zijn voor niet-HTTP/S-verkeer (TCP-en UDP-verkeer).
* FQDN HTTP/HTTPS-eind punten kunnen worden geplaatst op uw NVA-apparaat.
* Joker teken-HTTP/HTTPS-eind punten zijn afhankelijkheden die kunnen variëren op basis van een aantal kwalificaties.
* Wijs de route tabel toe die u hebt gemaakt voor uw HDInsight-subnet.

### <a name="service-endpoint-capable-dependencies"></a>Afhankelijkheden voor service-eind punten

| **Endpoints** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>Afhankelijkheden van IP-adressen

| **Endpoints** | **Details** |
|---|---|
| \*: 123 | NTP-klok controle. Verkeer wordt gecontroleerd op meerdere eind punten op poort 123 |
| [Hier](hdinsight-management-ip-addresses.md) gepubliceerde ip's | Dit zijn de HDInsight-service |
| Privé-IP-adressen van AAD-DS voor ESP-clusters |
| \*: 16800 voor KMS Windows-activering |
| \*12000 voor Log Analytics |

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS-afhankelijkheden

> [!Important]
> De onderstaande lijst bevat alleen enkele van de belangrijkste FQDN-namen. U kunt de volledige lijst met FQDN-namen ophalen voor het configureren van uw NVA [in dit bestand](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json).

| **Endpoints**                                                          |
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
| azure.archive.ubuntu.com:80                                           |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>Volgende stappen

* [Azure HDInsight Virtual Network-architectuur](hdinsight-virtual-network-architecture.md)
