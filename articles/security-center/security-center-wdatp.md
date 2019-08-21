---
title: Windows Defender Advanced Threat Protection met Azure Security Center
description: In dit document wordt de integratie van Azure Security Center en Windows Defender Advanced Threat Protection geïntroduceerd.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2018
ms.author: v-mohabe
ms.openlocfilehash: 597670803514fa59faaafdad73786cdb745a0976
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640905"
---
# <a name="windows-defender-advanced-threat-protection-with-azure-security-center"></a>Windows Defender Advanced Threat Protection met Azure Security Center

Azure Security Center breidt het aanbod van Cloud Workload Protection Platforms uit door te integreren met [Windows Defender Advanced Threat Protection](https://www.microsoft.com/en-us/WindowsForBusiness/windows-atp) (ATP).
Met deze wijziging komen uitgebreide mogelijkheden voor EDR (Endpoint Detection and Response) beschikbaar. Met Windows Defender ATP-integratie kunt u afwijkingen herkennen. U kunt ook geavanceerde aanvallen detecteren en erop reageren op server-eind punten die worden bewaakt door Azure Security Center.

## <a name="windows-defender-atp-features-in-security-center"></a>Windows Defender ATP-functies in Security Center

Wanneer u Windows Defender ATP gebruikt, krijgt u het volgende:

- **Detectie Sens oren van de volgende generatie**: Windows Defender ATP Sens oren voor Windows-servers verzamelen een grote reeks gedrags signalen.

- **Detectie van op de cloud gebaseerde post-inbreuken op basis van analyses**: Windows Defender ATP wordt snel aangepast aan veranderende bedreigingen. Het maakt gebruik van geavanceerde analyses en big data. Windows Defender ATP wordt versterkt door de kracht van de Intelligent Security Graph met signalen over Windows, Azure en Office om onbekende bedreigingen te detecteren. Het biedt actie bare waarschuwingen en stelt u in staat snel te reageren.

- **Bedreigings informatie**: Windows Defender ATP bevat hulpprogram ma's, technieken en procedures voor aanvallers. Wanneer deze worden gedetecteerd, worden er waarschuwingen gegenereerd. Het maakt gebruik van gegevens die zijn gegenereerd door micro soft Threat jagers en beveiligings teams, uitgebreid door Intelligence van partners.

Deze mogelijkheden zijn nu beschikbaar in Azure Security Center:

- **Automatische**onboarding: De Windows Defender ATP-sensor wordt automatisch ingeschakeld voor Windows-servers die worden voorAzure Security Centerd.

- **Eén glas venster**: In de Azure Security Center-console worden Windows Defender ATP-waarschuwingen weer gegeven.

- **Gedetailleerd onderzoek**van de computer: Azure Security Center klanten hebben toegang tot de Windows Defender ATP-console om een gedetailleerd onderzoek uit te voeren om het bereik van een schending te ontdekken.

![Azure Security Center, een lijst met waarschuwingen en algemene informatie over elke waarschuwing weer geven](media/security-center-wdatp/image1.png)

U kunt de waarschuwing verder onderzoeken door te draaien naar Windows Defender ATP. Hier kunt u aanvullende informatie weer geven, zoals de structuur van het waarschuwings proces en de incidenten grafiek. U kunt ook een gedetailleerde computer tijdlijn bekijken waarin elk gedrag wordt weer gegeven voor een historische periode van Maxi maal zes maanden.

![Pagina Windows Defender ATP met gedetailleerde informatie over een waarschuwing](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Platformondersteuning

Windows Defender ATP in Security Center ondersteunt detectie op Windows Server 2016-, 2012 R2-en 2008 R2 SP1-besturings systemen in een standaard service-abonnement.

> [!NOTE]
> Wanneer u Azure Security Center gebruikt voor het bewaken van servers, wordt automatisch een Windows Defender ATP-Tenant gemaakt en worden de Windows Defender ATP-gegevens standaard in Europa opgeslagen. Als u uw gegevens naar een andere locatie wilt verplaatsen, moet u contact opnemen met Microsoft Ondersteuning om de Tenant opnieuw in te stellen.

## <a name="onboarding-servers-to-security-center"></a>Servers voorbereiden op Security Center 

Als u servers wilt Security Center, klikt u op **Ga naar Azure Security Center voor** onboarding-servers van de Windows Defender ATP server-onboarding.

1. Selecteer in de Blade **voorbereiden** of maak een werk ruimte waarin u de gegevens wilt opslaan. <br>
2. Als u niet al uw werk ruimten kunt zien, kan dit worden veroorzaakt door een gebrek aan machtigingen. Controleer of uw werk ruimte is ingesteld op de laag van Azure-beveiliging. Zie voor meer informatie [upgraden naar de Standard-laag van Security Center voor verbeterde beveiliging](security-center-pricing.md).
    
3. Selecteer **servers toevoegen** om instructies te bekijken voor het installeren van micro soft monitoring agent. 

4. Nadat u het onboarding hebt uitgevoerd, kunt u de machines bewaken onder **Compute en apps**.

   ![Onboard computers](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-windows-defender-atp-integration"></a>Windows Defender ATP-integratie inschakelen

Als u wilt weer geven of Windows Defender ATP-integratie is ingeschakeld, selecteert u de prijzen voor **Security Center** >  **& instellingen** > klikt u op uw abonnement.

  ![Azure Security Center-beleids beheer](media/security-center-wdatp/policy-management.png)

Hier ziet u de integraties die momenteel zijn ingeschakeld.

  ![Azure Security Center pagina instellingen voor detectie van bedreigingen met Windows Defender ATP-integratie ingeschakeld](media/security-center-wdatp/enable-integrations.png)

- Als u de servers al hebt geklaard om Azure Security Center Standard-laag te maken, hoeft u geen verdere actie te ondernemen. De servers worden automatisch door Azure Security Center op de Windows Defender ATP. Dit kan tot 24 uur duren.

- Als u de servers nooit hebt gevrijt op Azure Security Center Standard-laag, kunt u deze op de gebruikelijke manier Azure Security Center.

- Als u de servers hebt onboardd via Windows Defender ATP:
  - Raadpleeg de documentatie voor meer informatie over [het niet meer vrijgeven van Server computers](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Onboarding van deze servers naar Azure Security Center.

## <a name="access-to-the-windows-defender-atp-portal"></a>Toegang tot de Windows Defender ATP-Portal

Volg de instructies in [gebruikers toegang toewijzen aan de portal](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/assign-portal-access-windows-defender-advanced-threat-protection).

## <a name="set-the-firewall-configuration"></a>De firewall configuratie instellen

Als u een proxy of firewall hebt die anonieme verkeer blokkeert, wordt een Windows Defender ATP-sensor waarmee een verbinding wordt gemaakt vanuit de systeem context, ervoor zorgen dat anoniem verkeer is toegestaan. Volg de instructies in [toegang tot de url's van de Windows Defender ATP-service inschakelen in de proxy server](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/configure-proxy-internet-windows-defender-advanced-threat-protection#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>De functie testen

Een goed aardige Windows Defender ATP-test waarschuwing genereren:

1. Gebruik Extern bureaublad om toegang te krijgen tot een virtuele machine met Windows Server 2012 R2 of een virtuele machine met Windows Server 2016.  Open een opdrachtpromptvenster.

2. Kopieer de volgende opdracht bij de prompt en voer deze uit. Het opdracht prompt venster wordt automatisch gesloten.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-WDATP-test\\invoice.exe'); Start-Process 'C:\\test-WDATP-test\\invoice.exe'
    ```

   ![Een opdracht prompt venster met de bovenstaande opdracht](media/security-center-wdatp/image4.jpeg)

3. Als de opdracht is geslaagd, ziet u een nieuwe waarschuwing in het dash board van Azure Security Center en de Windows Defender ATP-Portal. Het kan enkele minuten duren voordat deze waarschuwing wordt weer gegeven.

4. Als u de waarschuwing in Security Center wilt bekijken, gaat u naar **beveiligings waarschuwingen** >  **verdachte Power shell**-opdracht regel.

5. Selecteer in het venster onderzoek de koppeling om naar de Windows Defender ATP-portal te gaan.

## <a name="next-steps"></a>Volgende stappen

- [Beveiligings beleid instellen in azure Security Center](tutorial-security-policy.md): Meer informatie over het configureren van beveiligings beleid voor uw Azure-abonnementen en-resource groepen.
- [Aanbevelingen voor beveiliging in azure Security Center beheren](security-center-recommendations.md): Meer informatie over hoe u met aanbevelingen uw Azure-resources kunt beveiligen.
- [Beveiligings status controleren in azure Security Center](security-center-monitoring.md): Meer informatie over het controleren van de status van uw Azure-resources.
