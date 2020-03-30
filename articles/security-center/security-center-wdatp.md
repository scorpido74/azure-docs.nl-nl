---
title: Geavanceerde bescherming voor geavanceerde bedreigingen van Microsoft Defender - Azure Security Center
description: Dit document introduceert de integratie tussen Azure Security Center en Microsoft Defender Advanced Threat Protection.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: memildin
ms.openlocfilehash: 13852acb39a420e2f0da84e18bef4df823c1fa78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206262"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>Geavanceerde bedreigingsbeveiliging van Microsoft Defender met Azure Security Center

Azure Security Center breidt zijn cloud workload protection platforms aanbod door te integreren met [Microsoft Defender Advanced Threat Protection](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp) (ATP).
Met deze wijziging komen uitgebreide mogelijkheden voor EDR (Endpoint Detection and Response) beschikbaar. Met microsoft Defender ATP-integratie u afwijkingen herkennen. U ook geavanceerde aanvallen op servereindpunten detecteren en erop reageren die worden gecontroleerd door Azure Security Center.

## <a name="microsoft-defender-atp-features-in-security-center"></a>Microsoft Defender ATP-functies in Security Center

Wanneer u Microsoft Defender ATP gebruikt, krijgt u het:

- **Geavanceerde detectiesensoren na inbreuk:** Microsoft Defender ATP-sensoren voor Windows-servers verzamelen een breed scala aan gedragssignalen.

- **Analytics-gebaseerde, cloud-powered post breach detectie:** Microsoft Defender ATP past zich snel aan veranderende bedreigingen aan. Het maakt gebruik van geavanceerde analytics en big data. Microsoft Defender ATP wordt versterkt door de kracht van de Intelligent Security Graph met signalen in Windows, Azure en Office om onbekende bedreigingen te detecteren. Het biedt bruikbare waarschuwingen en stelt u in staat om snel te reageren.

- **Bedreigingsinformatie:** Microsoft Defender ATP genereert waarschuwingen wanneer het hulpprogramma's, technieken en procedures voor aanvallers identificeert. Het maakt gebruik van gegevens gegenereerd door Microsoft bedreiging jagers en security teams, aangevuld met intelligentie die door partners.

De volgende mogelijkheden zijn nu beschikbaar in Azure Security Center:

- **Automatische onboarding**: De Microsoft Defender ATP-sensor is automatisch ingeschakeld voor Windows-servers die zijn aangesloten op Azure Security Center.

- **Eén ruit:** in de Azure Security Center-console worden ATP-waarschuwingen van Microsoft Defender weergegeven.

- **Gedetailleerd machineonderzoek**: Azure Security Center-klanten kunnen de ATP-console van Microsoft Defender gebruiken om een gedetailleerd onderzoek uit te voeren om de omvang van een inbreuk aan het licht te brengen.

![Azure Security Center, met een lijst met waarschuwingen en algemene informatie over elke waarschuwing](media/security-center-wdatp/image1.png)

Gebruik Microsoft Defender ATP om verder te onderzoeken. Microsoft Defender ATP biedt aanvullende informatie, zoals de waarschuwingsprocesstructuur en de incidentgrafiek. U ook een gedetailleerde machinetijdlijn zien die elk gedrag voor een historische periode van maximaal zes maanden weergeeft.

![Microsoft Defender ATP-pagina met gedetailleerde informatie over een waarschuwing](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Platformondersteuning

Microsoft Defender ATP in Security Center ondersteunt detectie op Windows Server 2016, 2012 R2 en 2008 R2 SP1, voor Azure VM's hebt u een standaardabonnement nodig en voor niet-Azure VM's hebt u alleen standaardlaag op werkruimteniveau nodig.

> [!NOTE]
> Wanneer u Azure Security Center gebruikt om servers te controleren, wordt er automatisch een Microsoft Defender ATP-tenant gemaakt en worden de ATP-gegevens van Microsoft Defender standaard in Europa opgeslagen. Als u uw gegevens naar een andere locatie wilt verplaatsen, moet u contact opnemen met Microsoft Support om de tenant opnieuw in te stellen. Serverendpoint monitoring gebruik te maken van deze integratie is uitgeschakeld voor Office 365 GCC klanten.

## <a name="onboarding-servers-to-security-center"></a>Onboarding uitvoeren van servers voor Security Center 

Als u servers wilt instappen in beveiligingscentrum, klikt u op **Ga naar Azure Security Center om servers aan boord te nemen** vanaf de onboarding van de Microsoft Defender ATP-server.

1. Selecteer of maak in het **gebied Onboarding** een werkruimte waarin u de gegevens wilt opslaan. <br>
2. Als u niet al uw werkruimten zien, kan dit te wijten zijn aan een gebrek aan machtigingen, controleer of uw werkruimte is ingesteld op de laag Azure Security Standard. Zie Upgraden naar de standaardlaag van [het beveiligingscentrum voor verbeterde beveiliging voor](security-center-pricing.md)meer informatie.
    
3. Selecteer **Servers toevoegen** om instructies weer te geven over het installeren van de Microsoft Monitoring Agent. 

4. Na het instappen u de machines controleren onder **Compute en apps.**

   ![Boordcomputers](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>Microsoft Defender ATP-integratie inschakelen

Als u wilt zien of de ATP-integratie van Microsoft Defender is ingeschakeld, selecteert u **Prijsinstellingen voor beveiligingscentrum** > **& instellingen** > op uw abonnement klikt.
Hier ziet u de integraties die momenteel zijn ingeschakeld.

  ![Pagina met instellingen voor bedreigingsdetectie azure security center met Microsoft Defender ATP-integratie ingeschakeld](media/security-center-wdatp/enable-integrations.png)

- Als u de servers al hebt aangesloten op de standaardlaag azure security center, hoeft u geen verdere actie te ondernemen. Azure Security Center wordt automatisch aan boord van de servers van Microsoft Defender ATP. Onboarding kan tot 24 uur duren.

- Als u de servers nog nooit hebt aangesloten op de standaardlaag azure security center, u ze zoals gewoonlijk ingebruiknemen in Azure Security Center.

- Als u de servers hebt ingebouwd via Microsoft Defender ATP:
  - Raadpleeg de documentatie voor richtlijnen voor [het offboarden van servermachines.](https://go.microsoft.com/fwlink/p/?linkid=852906)
  - Deze servers aan boord van Azure Security Center.

## <a name="access-to-the-microsoft-defender-atp-portal"></a>Toegang tot de Microsoft Defender ATP-portal

Volg de instructies in [Gebruikerstoegang toewijzen tot de portal.](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)

## <a name="set-the-firewall-configuration"></a>De firewallconfiguratie instellen

Als u een proxy of firewall hebt die anoniem verkeer blokkeert, omdat een MICROSOFT Defender ATP-sensor verbinding maakt vanuit de systeemcontext, moet u ervoor zorgen dat anoniem verkeer is toegestaan. Volg de instructies in [Toegang tot Microsoft Defender ATP-service-URL's inschakelen in de proxyserver.](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)

## <a name="test-the-feature"></a>De functie testen

Ga als lid van het werk om een goedaardige Microsoft Defender ATP-testwaarschuwing te genereren:

1. Maak een map 'C:\test-MDATP-test'.

1. Gebruik Extern bureaublad om toegang te krijgen tot een Windows Server 2012 R2-vm of een Windows Server 2016-vm. Open een opdrachtregelvenster.

1. Kopieer en voer bij de prompt de volgende opdracht uit. Het venster Opdrachtprompt wordt automatisch gesloten.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```

   ![Een opdrachtpromptvenster met de opdracht hierboven](media/security-center-wdatp/image4.jpeg)

3. Als de opdracht succesvol is, ziet u een nieuwe waarschuwing op het Azure Security Center-dashboard en de ATP-portal van Microsoft Defender. Het kan enkele minuten duren voordat deze waarschuwing is weergegeven.

4. Als u de waarschuwing in Beveiligingscentrum wilt controleren, gaat u naar **Beveiligingswaarschuwingen** > **Verdachte PowerShell CommandLine**.

5. Selecteer in het onderzoeksvenster de koppeling om naar de ATP-portal van Microsoft Defender te gaan.

## <a name="next-steps"></a>Volgende stappen

- [Platforms en functies die worden ondersteund door Azure Security Center](security-center-os-coverage.md)
- [Beveiligingsbeleid instellen in Azure Security Center:](tutorial-security-policy.md)meer informatie over het configureren van beveiligingsbeleid voor uw Azure-abonnementen en brongroepen.
- [Beveiligingsaanbevelingen beheren in Azure Security Center:](security-center-recommendations.md)lees hoe aanbevelingen u helpen uw Azure-bronnen te beschermen.
- [Beveiligingsstatus controleren in Azure Security Center](security-center-monitoring.md): meer informatie over het controleren van de status van uw Azure-resources.
