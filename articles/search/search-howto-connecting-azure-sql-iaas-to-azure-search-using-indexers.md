---
title: VM-verbinding van virtuele Azure SQL-machine voor zoek indexering-Azure Search
description: Schakel versleutelde verbindingen in en configureer de firewall om verbindingen met SQL Server op een virtuele Azure-machine (VM) van een Indexeer functie op Azure Search toe te staan.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/04/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 7629750da8f58c2c62f15102b60b5b562689f087
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656703"
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-server-on-an-azure-vm"></a>Een verbinding van een Azure Search Indexeer functie configureren om te SQL Server op een Azure VM
Zoals beschreven in het [verbinden van Azure SQL database naar Azure Search met Indexeer functies](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), is het maken van Indexeer functies op basis **van SQL Server op Azure-vm's** (of **SQL Azure vm's** voor korte) door Azure Search ondersteund, maar zijn er een paar beveiligings vereisten voor Zorg ervoor dat u eerst. 

Verbindingen van Azure Search naar SQL Server op een VM is een open bare Internet verbinding. Alle beveiligings maatregelen die u normaal gesp roken uitvoert, zijn hier ook van toepassing:

+ Verkrijg een certificaat van een [certificerings instantie provider](https://en.wikipedia.org/wiki/Certificate_authority#Providers) voor de Fully Qualified Domain name van de SQL Server instantie op de virtuele Azure-machine.
+ Installeer het certificaat op de VM en configureer vervolgens versleutelde verbindingen op de VM met behulp van de instructies in dit artikel.

## <a name="enable-encrypted-connections"></a>Versleutelde verbindingen inschakelen
Voor Azure Search is een versleuteld kanaal vereist voor alle indexer aanvragen via een open bare Internet verbinding. In deze sectie vindt u de stappen voor het maken van dit werk.

1. Controleer de eigenschappen van het certificaat om te controleren of de onderwerpnaam de Fully Qualified Domain Name (FQDN) van de virtuele Azure-machine is. U kunt een hulp programma gebruiken zoals CertUtil of de module Certificaten om de eigenschappen weer te geven. U kunt de FQDN-naam ophalen uit de sectie belangrijkste onderdelen van de VM-service in het veld **openbaar IP-adres/DNS-label** in het [Azure Portal](https://portal.azure.com/).
   
   * Voor virtuele machines die zijn gemaakt met de nieuwere **Resource Manager** -sjabloon, wordt de FQDN-indeling`<your-VM-name>.<region>.cloudapp.azure.com`
   * Voor oudere Vm's die zijn gemaakt als een **klassieke** virtuele machine, wordt de `<your-cloud-service-name.cloudapp.net>`FQDN-indeling als.

2. Configureer SQL Server om het certificaat te gebruiken met behulp van de REGI ster-editor (regedit). 
   
    Hoewel SQL Server Configuration Manager vaak wordt gebruikt voor deze taak, kunt u dit niet gebruiken voor dit scenario. Het geïmporteerde certificaat wordt niet gevonden omdat de FQDN-naam van de virtuele machine in azure niet overeenkomt met de FQDN die door de virtuele machine wordt bepaald (het domein wordt aangeduid als de lokale computer of het netwerk domein waarmee het is verbonden). Wanneer namen niet overeenkomen, gebruikt u regedit om het certificaat op te geven.
   
   * In Regedit, bladert u naar deze register `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`sleutel:.
     
     Het `[MSSQL13.MSSQLSERVER]` deel is afhankelijk van versie en exemplaar naam. 
   * Stel de waarde van de **certificaat** sleutel in op de **vinger afdruk** van het SSL-certificaat dat u in de virtuele machine hebt geïmporteerd.
     
     Er zijn verschillende manieren om de vinger afdruk te verkrijgen, wat beter is dan andere. Als u het kopieert vanuit de module **certificaten** in MMC, haalt u waarschijnlijk een onzichtbare voorloop tekens op [zoals beschreven in dit ondersteunings artikel](https://support.microsoft.com/kb/2023869/), wat resulteert in een fout wanneer u een verbinding probeert te maken. Er zijn verschillende tijdelijke oplossingen voor het oplossen van dit probleem. Het eenvoudigste is om op BACKSPACE te drukken en vervolgens het eerste teken van de vinger afdruk opnieuw te typen om het voorloop teken te verwijderen uit het veld sleutel waarde in Regedit. U kunt ook een ander hulp programma gebruiken om de vinger afdruk te kopiëren.

3. Machtigingen verlenen aan het service account. 
   
    Zorg ervoor dat aan het SQL Server-service account de juiste machtigingen worden verleend voor de persoonlijke sleutel van het SSL-certificaat. Als u deze stap verkrijgt, wordt SQL Server niet gestart. U kunt de module **certificaten** of **certutil** voor deze taak gebruiken.
    
4. Start de SQL Server-service opnieuw.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>SQL Server connectiviteit in de VM configureren
Nadat u de versleutelde verbinding hebt ingesteld die is vereist voor Azure Search, zijn er extra configuratie stappen in intrinsiek voor SQL Server op Azure-Vm's. Als u dit nog niet hebt gedaan, is de volgende stap het volt ooien van de configuratie met behulp van een van deze artikelen:

* Zie [verbinding maken met een virtuele machine van SQL Server in azure met behulp van Resource Manager](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md)voor een **Resource Manager** -VM. 
* Zie [verbinding maken met een virtuele machine van SQL Server op Azure Classic](../virtual-machines/windows/classic/sql-connect.md)voor een **klassieke** VM.

Bekijk in het bijzonder de sectie in elk artikel voor ' verbinding maken via internet '.

## <a name="configure-the-network-security-group-nsg"></a>De netwerk beveiligings groep (NSG) configureren
Het is niet ongebruikelijk om de NSG en de bijbehorende Azure-eind punt of Access Control lijst (ACL) te configureren om uw Azure-VM toegankelijk te maken voor andere partijen. U hebt dit gedaan voordat u uw eigen toepassings logica kunt koppelen aan uw SQL Azure-VM. Het is geen verschil voor een Azure Search verbinding met uw SQL Azure VM. 

De onderstaande koppelingen bieden instructies voor de NSG-configuratie voor VM-implementaties. Gebruik deze instructies om een Azure Search-eind punt op basis van het IP-adres van de toegangs beheer lijst te gebruiken.

> [!NOTE]
> Zie [Wat is een netwerk beveiligings groep?](../virtual-network/security-overview.md) voor achtergrond informatie.
> 
> 

* Zie [nsg's maken voor arm](../virtual-network/tutorial-filter-network-traffic.md)-implementaties voor een **Resource Manager** -VM. 
* Zie [nsg's maken voor klassieke implementaties](../virtual-network/virtual-networks-create-nsg-classic-ps.md)voor een **klassieke** virtuele machine.

IP-adres Sering kan enkele uitdagingen opleveren die eenvoudig kunnen worden verholpen als u op de hoogte bent van het probleem en mogelijke oplossingen. De overige secties bevatten aanbevelingen voor het verwerken van problemen met betrekking tot IP-adressen in de ACL.

#### <a name="restrict-access-to-the-search-service-ip-address"></a>De toegang tot het IP-adres van de zoek service beperken
We raden u ten zeerste aan de toegang tot het IP-adres van uw zoek service in de ACL te beperken, in plaats van uw SQL Azure Vm's breed open te maken voor verbindings aanvragen. U kunt het IP-adres eenvoudig vinden door de FQDN-namen (bijvoorbeeld `<your-search-service-name>.search.windows.net`) van uw zoek service te pingen.

#### <a name="managing-ip-address-fluctuations"></a>Schommelingen van IP-adressen beheren
Als uw zoek service slechts één Zoek eenheid (dat wil zeggen, één replica en één partitie) heeft, wordt het IP-adres gewijzigd tijdens het starten van de routine service, waarbij een bestaande ACL met het IP-adres van uw zoek service ongeldig wordt.

Een van de manieren om de volgende verbindings fout te voor komen, is het gebruik van meer dan één replica en één partitie in Azure Search. Hierdoor worden de kosten verhoogd, maar wordt ook het probleem met het IP-adres opgelost. In Azure Search worden IP-adressen niet gewijzigd als u meer dan één Zoek eenheid hebt.

Een tweede aanpak is om de verbinding te laten mislukken en vervolgens de Acl's opnieuw te configureren in de NSG. Gemiddeld kunt u instellen dat IP-adressen om de paar weken worden gewijzigd. Voor klanten die het indexeren regel matig uitvoeren, kan deze aanpak levensvatbaar zijn.

Een derde levensvat bare methode (maar niet bijzonder veilig) is het IP-adres bereik opgeven van de Azure-regio waar uw zoek service is ingericht. De lijst met IP-bereiken waaruit open bare IP-adressen worden toegewezen aan Azure-resources, wordt gepubliceerd op [Azure Data Center IP-adresbereiken](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-search-portal-ip-addresses"></a>De IP-adressen van de Azure Search-Portal toevoegen
Als u de Azure Portal gebruikt om een Indexeer functie te maken, moet Azure Search-Portal-logica ook toegang hebben tot uw SQL Azure VM tijdens de aanmaak tijd. IP-adressen van Azure Search-Portal kunnen worden gevonden `stamp2.search.ext.azure.com`door te pingen.

## <a name="next-steps"></a>Volgende stappen
Met configuratie uit de weg kunt u nu een SQL Server op de Azure-VM opgeven als de gegevens bron voor een Azure Search indexer. Zie [Azure SQL database verbinding maken met Azure Search met Indexeer functies](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) voor meer informatie.

