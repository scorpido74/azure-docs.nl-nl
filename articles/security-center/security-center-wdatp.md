---
title: Micro soft Defender Advanced Threat Protection-Azure Security Center
description: In dit document wordt de integratie van Azure Security Center en micro soft Defender Advanced Threat Protection geïntroduceerd.
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
ms.openlocfilehash: 46b9fe5c6a038aa98cf8df64c40bf8ea1747efec
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73663598"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>Micro soft Defender Advanced Threat Protection met Azure Security Center

Azure Security Center is een uitbrei ding van de beveiligings platforms voor Cloud werkbelasting die worden aangeboden door te integreren met [micro soft Defender Advanced Threat Protection](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp) (ATP).
Met deze wijziging komen uitgebreide mogelijkheden voor EDR (Endpoint Detection and Response) beschikbaar. Met micro soft Defender ATP-integratie kunt u afwijkingen herkennen. U kunt ook geavanceerde aanvallen detecteren en erop reageren op server-eind punten die worden bewaakt door Azure Security Center.

## <a name="microsoft-defender-atp-features-in-security-center"></a>Micro soft Defender ATP-functies in Security Center

Wanneer u micro soft Defender ATP gebruikt, krijgt u het volgende:

- **Geavanceerde detectie Sens oren**: micro soft Defender ATP Sens oren voor Windows-servers verzamelen een grote reeks gedrags signalen.

- In de **cloud gebaseerde post-inbreuk detectie op basis van analyses**: micro soft Defender ATP wordt snel aangepast aan veranderende bedreigingen. Het maakt gebruik van geavanceerde analyses en big data. Micro soft Defender ATP wordt versterkt door de kracht van de Intelligent Security Graph met signalen over Windows, Azure en Office om onbekende bedreigingen te detecteren. Het biedt actie bare waarschuwingen en stelt u in staat snel te reageren.

- **Bedreigings informatie**: micro soft Defender ATP genereert waarschuwingen wanneer de aanvaller hulpprogram ma's, technieken en procedures identificeert. Het maakt gebruik van gegevens die zijn gegenereerd door micro soft Threat jagers en beveiligings teams, uitgebreid door Intelligence van partners.

De volgende mogelijkheden zijn nu beschikbaar in Azure Security Center:

- **Automatische**voor bereiding: de micro soft Defender ATP-sensor wordt automatisch ingeschakeld voor Windows-servers die worden uitgevoerd voor Azure Security Center.

- **Eén venster glas**: de Azure Security Center-console geeft micro soft Defender ATP-waarschuwingen weer.

- **Gedetailleerd computer onderzoek**: Azure Security Center klanten kunnen de micro soft Defender ATP-console gebruiken om een gedetailleerd onderzoek uit te voeren om het bereik van een schending te ontdekken.

![Azure Security Center, een lijst met waarschuwingen en algemene informatie over elke waarschuwing weer geven](media/security-center-wdatp/image1.png)

Gebruik micro soft Defender ATP om verder te onderzoeken. Micro soft Defender ATP biedt aanvullende informatie, zoals de structuur van het waarschuwings proces en de incidenten grafiek. U kunt ook een gedetailleerde computer tijdlijn bekijken waarin elk gedrag wordt weer gegeven voor een historische periode van Maxi maal zes maanden.

![Micro soft Defender ATP-pagina met gedetailleerde informatie over een waarschuwing](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Platformondersteuning

Micro soft Defender ATP in Security Center ondersteunt detectie op Windows Server 2016, 2012 R2 en 2008 R2 SP1, voor virtuele machines van Azure hebt u een Standard-laag abonnement en voor niet-Azure-Vm's hebt u alleen een Standard-laag op het niveau van de werk ruimte nodig.

> [!NOTE]
> Wanneer u Azure Security Center gebruikt voor het bewaken van servers, wordt automatisch een micro soft Defender ATP-Tenant gemaakt en worden de micro soft Defender ATP-gegevens standaard in Europa opgeslagen. Als u uw gegevens naar een andere locatie wilt verplaatsen, moet u contact opnemen met Microsoft Ondersteuning om de Tenant opnieuw in te stellen. Het gebruik van de server eindpunt bewaking die gebruikmaakt van deze integratie is uitgeschakeld voor Office 365 GCC-klanten.

## <a name="onboarding-servers-to-security-center"></a>Servers voorbereiden op Security Center 

Als u servers wilt Security Center, klikt u op **Ga naar Azure Security Center voor** onboarding-servers van de micro soft Defender ATP server-onboarding.

1. Selecteer of maak **in het** voorbereidings gebied een werk ruimte waarin u de gegevens wilt opslaan. <br>
2. Als u niet al uw werk ruimten kunt zien, kan dit worden veroorzaakt door een gebrek aan machtigingen. Controleer of uw werk ruimte is ingesteld op de laag van Azure-beveiliging. Zie voor meer informatie [upgraden naar de Standard-laag van Security Center voor verbeterde beveiliging](security-center-pricing.md).
    
3. Selecteer **servers toevoegen** om instructies te bekijken voor het installeren van micro soft monitoring agent. 

4. Nadat u het onboarding hebt uitgevoerd, kunt u de machines bewaken onder **Compute en apps**.

   ![Onboard computers](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>Micro soft Defender ATP-integratie inschakelen

Als u wilt weer geven of micro soft Defender ATP Integration is ingeschakeld, selecteert u **security center** > **prijzen & instellingen** > klikt u op uw abonnement.
Hier ziet u de integraties die momenteel zijn ingeschakeld.

  ![Azure Security Center pagina instellingen voor detectie van bedreigingen met micro soft Defender ATP-integratie ingeschakeld](media/security-center-wdatp/enable-integrations.png)

- Als u de servers al hebt geklaard om Azure Security Center Standard-laag te maken, hoeft u geen verdere actie te ondernemen. De servers worden automatisch door Azure Security Center op de micro soft Defender ATP-server geïmplementeerd. Het kan tot 24 uur duren voordat de onboarding is uitgevoerd.

- Als u de servers nooit hebt gevrijt op Azure Security Center Standard-laag, kunt u deze op de gebruikelijke manier Azure Security Center.

- Als u de servers hebt geklaard via micro soft Defender ATP:
  - Raadpleeg de documentatie voor meer informatie over [het niet meer vrijgeven van Server computers](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Onboarding van deze servers naar Azure Security Center.

## <a name="access-to-the-microsoft-defender-atp-portal"></a>Toegang tot de micro soft Defender ATP-Portal

Volg de instructies in [gebruikers toegang toewijzen aan de portal](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

## <a name="set-the-firewall-configuration"></a>De firewall configuratie instellen

Als u een proxy of firewall hebt die anonieme verkeer blokkeert, wordt een micro soft Defender ATP-sensor waarmee een verbinding wordt gemaakt vanuit de systeem context, ervoor zorgen dat anoniem verkeer is toegestaan. Volg de instructies in [toegang tot de url's van micro soft Defender ATP-service inschakelen in de proxy server](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>De functie testen

Een goed aardige micro soft Defender ATP-test waarschuwing genereren:

1. Gebruik Extern bureaublad om toegang te krijgen tot een virtuele machine met Windows Server 2012 R2 of een virtuele machine met Windows Server 2016. Open een opdrachtpromptvenster.

2. Kopieer de volgende opdracht bij de prompt en voer deze uit. Het opdracht prompt venster wordt automatisch gesloten.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-WDATP-test\\invoice.exe'); Start-Process 'C:\\test-WDATP-test\\invoice.exe'
    ```

   ![Een opdracht prompt venster met de bovenstaande opdracht](media/security-center-wdatp/image4.jpeg)

3. Als de opdracht is geslaagd, ziet u een nieuwe waarschuwing in het dash board van Azure Security Center en de micro soft Defender ATP-Portal. Het kan enkele minuten duren voordat deze waarschuwing wordt weer gegeven.

4. Als u de waarschuwing in Security Center wilt bekijken, gaat u naar **beveiligings waarschuwingen** > **verdachte Power shell**-opdracht regel.

5. Selecteer in het venster onderzoek de koppeling om naar de micro soft Defender ATP-portal te gaan.

## <a name="next-steps"></a>Volgende stappen

- [Platforms en functies die worden ondersteund door Azure Security Center](security-center-os-coverage.md)
- [Beveiligings beleid instellen in azure Security Center](tutorial-security-policy.md): meer informatie over het configureren van beveiligings beleid voor uw Azure-abonnementen en-resource groepen.
- [Aanbevelingen voor beveiliging in azure Security Center](security-center-recommendations.md): Ontdek hoe aanbevelingen u helpen uw Azure-resources te beveiligen.
- [Beveiligingsstatus controleren in Azure Security Center](security-center-monitoring.md): meer informatie over het controleren van de status van uw Azure-resources.
