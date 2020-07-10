---
title: Problemen met de verbinding vaststellen in de SSIS Integration runtime
description: Dit artikel bevat richt lijnen voor probleem oplossing voor het vaststellen van de connectiviteit in de SSIS Integration runtime
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: meiyl
author: meiyl
ms.reviewer: sawinark
manager: yidetu
ms.date: 06/07/2020
ms.openlocfilehash: 8e520048a6067f134e847953f4f4aa0598d9926e
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86172597"
---
# <a name="troubleshoot-diagnose-connectivity-in-the-ssis-integration-runtime"></a>Problemen met de verbinding vaststellen in de SSIS Integration runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Als u verbindings problemen ondervindt tijdens het uitvoeren van SSIS-pakketten in de SSIS Integration runtime, met name als uw SSIS Integration runtime is gekoppeld aan het virtuele netwerk van Azure. U kunt zelf problemen proberen op te sporen met behulp van de functie voor het controleren van de connectiviteit op de pagina bewaking SSIS Integration runtime van de Azure Data Factory Portal. 

 ![Pagina bewaken: de pagina connectiviteits controle diagnosticeren ](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png) ![ -verbinding testen](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png)
 
In dit artikel vindt u de meest voorkomende fouten die kunnen optreden wanneer u een verbinding test in de SSIS Integration runtime. Hierin worden de mogelijke oorzaken en acties beschreven om de fouten op te lossen. 

## <a name="common-errors-potential-causes-and-recommendation-solutions"></a>Veelvoorkomende fouten, mogelijke oorzaken en aanbevelings oplossingen

### <a name="error-code-invalidinput"></a>Fout code: InvalidInput.
* **Fout bericht**: Controleer of de invoer juist is.
* **Mogelijke oorzaken:** De invoer is onjuist.
* **Aanbeveling:** Controleer de invoer.

### <a name="error-code-firewallornetworkissue"></a>Fout code: FirewallOrNetworkIssue.
* **Fout bericht**: Controleer of deze poort is geopend op de firewall/server-NSG en of het netwerk stabiel is.
* **Mogelijke oorzaken:** 
  * Deze poort wordt niet door de server geopend.
  * Het uitgaande verkeer op deze poort wordt door uw netwerk beveiligings groep geweigerd
  * Deze poort wordt niet geopend met uw NVA/Azure firewall/on-premises firewall.
* **Advies** 
  * Open deze poort op de server.
  * Werk de netwerk beveiligings groep bij om uitgaand verkeer op deze poort toe te staan.
  * Open deze poort op de firewall NVA/Azure firewall/on-premises.

### <a name="error-code-misconfigureddnssettings"></a>Fout code: MisconfiguredDnsSettings.
* **Fout bericht**: als u uw eigen DNS-server gebruikt in het VNet dat is gekoppeld aan uw Azure-SSIS IR, controleert u of de naam van uw host kan worden omgezet.
* **Mogelijke oorzaken:** 
  *  Het probleem van aangepaste DNS
  *  U gebruikt geen Fully Qualified Domain Name (FQDN) voor de naam van uw particuliere host
* **Advies** 
  *  Los uw aangepaste DNS-probleem op om er zeker van te zijn dat de hostnaam kan worden omgezet.
  *  Gebruik de Fully Qualified Domain Name (FQDN), bijvoorbeeld gebruik <your_private_server>. contoso.com in plaats van <your_private_server>, omdat Azure-SSIS IR uw eigen DNS-achtervoegsel niet automatisch toevoegt.

### <a name="error-code-servernotallowremoteconenction"></a>Fout code: ServerNotAllowRemoteConenction.
* **Fout bericht**: Controleer of op uw server externe TCP-verbindingen via deze poort zijn toegestaan.
* **Mogelijke oorzaken:** 
  *  De firewall van uw server staat geen externe TCP-verbindingen toe.
  *  Uw server is niet online.
* **Advies** 
  *  Externe TCP-verbindingen op de Server Firewall toestaan.
  *  Start de server.
   
### <a name="error-code-misconfigurednsgsettings"></a>Fout code: MisconfiguredNsgSettings.
* **Fout bericht**: Controleer of het NSG van uw VNet uitgaand verkeer via deze poort toestaat. Als u Azure ExpressRoute en of een UDR gebruikt, moet u controleren of deze poort is geopend op uw firewall/server.
* **Mogelijke oorzaken:** 
  *  Het uitgaande verkeer op deze poort wordt door uw netwerk beveiligings groep geweigerd.
  *  Deze poort wordt niet geopend met uw NVA/Azure firewall/on-premises firewall.
* **Advies** 
  *  Werk de netwerk beveiligings groep bij om uitgaand verkeer op deze poort toe te staan.
  *  Open deze poort op de firewall NVA/Azure firewall/on-premises.

### <a name="error-code-genericissues"></a>Fout code: GenericIssues.
* **Fout bericht**: de test verbinding is mislukt vanwege algemene problemen.
* **Mogelijke oorzaken:** De test verbinding heeft een algemeen tijdelijk probleem aangetroffen.
* **Aanbeveling:** Voer de test verbinding later opnieuw uit. Neem contact op met het ondersteunings team van Azure Data Factory als u het niet kunt doen.


### <a name="error-code-pspingexecutiontimeout"></a>Fout code: PSPingExecutionTimeout.
* **Fout bericht**: time-out voor de verbinding testen, probeer het later opnieuw.
* **Mogelijke oorzaken:** Time-out bij testen van connectiviteit.
* **Aanbeveling:** Voer de test verbinding later opnieuw uit. Neem contact op met het ondersteunings team van Azure Data Factory als u het niet kunt doen.

### <a name="error-code-networkinstable"></a>Fout code: NetworkInstable.
* **Fout bericht**: de verbindings test is onregelmatig geslaagd vanwege een netwerk Insta Biel.
* **Mogelijke oorzaken:** Tijdelijk netwerk probleem.
* **Aanbeveling:** Controleer of de server of het firewall netwerk stabiel is.

## <a name="next-steps"></a>Volgende stappen

- Implementeer uw pakketten. Zie [een SSIS-project implementeren in azure met SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms)voor meer informatie.
- Voer uw pakketten uit. Zie [SSIS-pakketten uitvoeren in azure met SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms)voor meer informatie.
- Plan uw pakketten. Zie [SSIS-pakketten plannen in azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15)voor meer informatie.

