---
title: Azure SQL VM-verbinding voor zoeken indexering
titleSuffix: Azure Cognitive Search
description: Versleutelde verbindingen inschakelen en de firewall zo configureren dat verbindingen met SQL Server op een Virtuele Azure-machine (VM) van een indexer op Azure Cognitive Search worden ingeschakeld.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1ab2b7860e8a75da5f8acef2fc4fa54d4b73a30d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256960"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-server-on-an-azure-vm"></a>Een verbinding configureren van een Azure Cognitive Search-indexer naar SQL Server op een Azure VM

Zoals opgemerkt in [Het verbinden van Azure SQL Database met Azure Cognitive Search met behulp van indexeerders,](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)wordt het maken van indexers tegen **SQL Server op Azure VM's** (kortweg **SQL Azure VM's)** ondersteund door Azure Cognitive Search, maar er zijn een paar beveiligingsgerelateerde vereisten om eerst voor te zorgen. 

Verbindingen van Azure Cognitive Search naar SQL Server op een VM is een openbare internetverbinding. Alle beveiligingsmaatregelen die u normaal gesproken zou volgen voor deze verbindingen gelden hier ook:

+ Een certificaat verkrijgen bij een [certificaatautoriteitprovider](https://en.wikipedia.org/wiki/Certificate_authority#Providers) voor de volledig gekwalificeerde domeinnaam van het SQL Server-exemplaar op de Azure VM.
+ Installeer het certificaat op de VM en schakel versleutelde verbindingen op de VM in en configureer deze met behulp van de instructies in dit artikel.

## <a name="enable-encrypted-connections"></a>Versleutelde verbindingen inschakelen
Azure Cognitive Search vereist een versleuteld kanaal voor alle indexeraanvragen via een openbare internetverbinding. In deze sectie worden de stappen weergegeven om dit te laten werken.

1. Controleer de eigenschappen van het certificaat om te controleren of de onderwerpnaam de volledig gekwalificeerde domeinnaam (FQDN) van de Azure VM is. U een tool zoals CertUtils of de module Certificaten gebruiken om de eigenschappen weer te geven. U de FQDN ophalen in de sectie Essentials van het VM-serviceblade, in het veld **Public IP-adres/DNS-naamlabel** in de [Azure-portal.](https://portal.azure.com/)
   
   * Voor VM's die zijn gemaakt met de sjabloon nieuwere **Resource Manager,** wordt de FQDN opgemaakt als`<your-VM-name>.<region>.cloudapp.azure.com`
   * Voor oudere VM's die als **klassieke** VM zijn gemaakt, wordt de FQDN opgemaakt als `<your-cloud-service-name.cloudapp.net>`.

2. Configureer SQL Server om het certificaat te gebruiken met behulp van de registereditor (regedit). 
   
    Hoewel SQL Server Configuration Manager vaak wordt gebruikt voor deze taak, u deze niet gebruiken voor dit scenario. Het zal het geïmporteerde certificaat niet vinden omdat de FQDN van de VM op Azure niet overeenkomt met de FQDN zoals bepaald door de VM (het identificeert het domein als de lokale computer of het netwerkdomein waaraan het is verbonden). Wanneer namen niet overeenkomen, gebruikt u regedit om het certificaat op te geven.
   
   * Blader in regedit naar deze `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`registersleutel: .
     
     Het `[MSSQL13.MSSQLSERVER]` onderdeel varieert op basis van de versie en de naam van de instantie. 
   * Stel de waarde van de **certificaatsleutel** in op de **duimafdruk** van het TLS/SSL-certificaat dat u naar de VM hebt geïmporteerd.
     
     Er zijn verschillende manieren om de duimafdruk te krijgen, sommige beter dan anderen. Als u het kopieert uit de module **Certificaten** in MMC, pikt u waarschijnlijk een onzichtbaar hoofdteken op [zoals beschreven in dit ondersteuningsartikel](https://support.microsoft.com/kb/2023869/), wat resulteert in een fout wanneer u een verbinding probeert. Er bestaan verschillende oplossingen voor het corrigeren van dit probleem. Het gemakkelijkst is om backspace over en vervolgens opnieuw typen het eerste teken van de duimafdruk om de hoofdrol in de belangrijkste waarde veld in regedit te verwijderen. U ook een ander gereedschap gebruiken om de duimafdruk te kopiëren.

3. Machtigingen verlenen aan het serviceaccount. 
   
    Controleer of het SQL Server-serviceaccount de juiste toestemming krijgt voor de privésleutel van het TLS/SSL-certificaat. Als u deze stap over het hoofd ziet, wordt SQL Server niet gestart. U de module **Certificaten** of **CertUtils** voor deze taak gebruiken.
    
4. Start de SQL Server-service opnieuw.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>SQL Server-connectiviteit configureren in de VM
Nadat u de versleutelde verbinding hebt ingesteld die vereist is voor Azure Cognitive Search, zijn er aanvullende configuratiestappen die inherent zijn aan SQL Server op Azure VM's. Als u dit nog niet hebt gedaan, is de volgende stap om de configuratie te voltooien met een van deze artikelen:

* Zie [Verbinding maken met een virtuele machine van SQL Server op Azure met Resource Manager](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md)voor een VM voor **Resourcebeheer.** 
* Zie Verbinding maken [met een virtuele machine van SQL Server op Azure Classic](../virtual-machines/windows/classic/sql-connect.md)voor een **klassieke** virtuele vm.

In het bijzonder, bekijk de sectie in elk artikel voor "verbinding maken via het internet".

## <a name="configure-the-network-security-group-nsg"></a>De netwerkbeveiligingsgroep (NSG) configureren
Het is niet ongebruikelijk om de NSG en het bijbehorende Azure-eindpunt of toegangsbeheerlijst (ACL) te configureren om uw Azure VM toegankelijk te maken voor andere partijen. De kans is groot dat u dit al eerder hebt gedaan om uw eigen toepassingslogica verbinding te laten maken met uw SQL Azure VM. Het is niet anders voor een Azure Cognitive Search-verbinding met uw SQL Azure VM. 

De onderstaande links geven instructies over DEG-configuratie voor VM-implementaties. Gebruik deze instructies om een Azure Cognitive Search-eindpunt te gebruiken op basis van het IP-adres.

> [!NOTE]
> Zie Wat [is een netwerkbeveiligingsgroep voor](../virtual-network/security-overview.md) een achtergrond?
> 
> 

* Zie [NsGs maken voor ARM-implementaties voor](../virtual-network/tutorial-filter-network-traffic.md)een VM **voor Resourcemanager.** 
* Zie [NsGs maken voor Klassieke implementaties voor](../virtual-network/virtual-networks-create-nsg-classic-ps.md)een **klassieke** vm.

IP-adressering kan een paar uitdagingen opleveren die gemakkelijk kunnen worden overwonnen als u zich bewust bent van het probleem en mogelijke oplossingen. De overige secties geven aanbevelingen voor het afhandelen van problemen met betrekking tot IP-adressen in de ACL.

#### <a name="restrict-access-to-the-azure-cognitive-search"></a>Toegang tot azure cognitive search beperken
We raden u ten zeerste aan de toegang tot het IP-adres van uw zoekservice en het IP-adresbereik van `AzureCognitiveSearch` de [servicetag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) in de ACL te beperken in plaats van uw SQL Azure VM's open te stellen voor alle verbindingsaanvragen.

U het IP-adres achterhalen door de FQDN (bijvoorbeeld `<your-search-service-name>.search.windows.net`) van uw zoekservice te pingen.

U `AzureCognitiveSearch` kunt het IP-adresbereik van de [servicetag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) achterhalen door [downloadbare JSON-bestanden](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files) te gebruiken of via de Service Tag Discovery [API.](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview) Het IP-adresbereik wordt wekelijks bijgewerkt.

#### <a name="managing-ip-address-fluctuations"></a>Schommelingen van IP-adressen beheren
Als uw zoekservice slechts één zoekeenheid heeft (dat wil zeggen één replica en één partitie), wordt het IP-adres gewijzigd tijdens het opnieuw opstarten van de routineservice, waardoor een bestaande ACL ongeldig wordt gemaakt met het IP-adres van uw zoekservice.

Een manier om de volgende verbindingsfout te voorkomen, is door meer dan één replica en één partitie te gebruiken in Azure Cognitive Search. Hierdoor verhoogt de kosten, maar het lost ook het IP-adres probleem. In Azure Cognitive Search worden IP-adressen niet gewijzigd wanneer u meer dan één zoekeenheid hebt.

Een tweede benadering is om de verbinding te laten mislukken en vervolgens de ACL's in de NSG opnieuw te configureren. Gemiddeld u verwachten dat IP-adressen om de paar weken veranderen. Voor klanten die gecontroleerd indexeren op een onregelmatige basis, kan deze aanpak levensvatbaar zijn.

Een derde haalbare (maar niet bijzonder veilige) benadering is het opgeven van het IP-adresbereik van de Azure-regio waar uw zoekservice is ingericht. De lijst met IP-bereiken waaruit openbare IP-adressen worden toegewezen aan Azure-resources, wordt gepubliceerd in [IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653)van Azure Datacenter. 

#### <a name="include-the-azure-cognitive-search-portal-ip-addresses"></a>IP-adressen van de Azure Cognitive Search-portal opnemen
Als u de Azure-portal gebruikt om een indexer te maken, heeft Azure Cognitive Search-portallogica ook toegang nodig tot uw SQL Azure VM tijdens het maken van de tijd. IP-adressen van Azure Cognitive Search-portal `stamp2.search.ext.azure.com`kunnen worden gevonden door te pingen.

## <a name="next-steps"></a>Volgende stappen
Als configuratie uit de weg is, u nu een SQL Server op Azure VM opgeven als gegevensbron voor een Azure Cognitive Search-indexer. Zie [Azure SQL Database verbinden met Azure Cognitive Search met behulp van indexeerders](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) voor meer informatie.

