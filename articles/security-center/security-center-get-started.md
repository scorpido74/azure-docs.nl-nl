---
title: Upgraden naar standaardlaag - Azure Security Center
description: In deze snelstartgids wordt beschreven hoe u een upgrade uitvoert naar de prijscategorie Standard van Security Center voor extra beveiliging.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: memildin
ms.openlocfilehash: 26d62f2c027a093ba518b98fa37ce3a31a14f175
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73664290"
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>Snelstartgids: uw Azure-abonnement onboarden voor Security Center Standard
Azure Security Center biedt geïntegreerd beveiligingsbeheer en bedreigingsbeveiliging voor uw verschillende hybride cloudworkloads. De Gratis laag biedt beperkte beveiliging voor alleen uw Azure-resources, maar de prijscategorie Standard biedt deze mogelijkheden ook voor on-premises en andere clouds. Standard van Security Center helpt u beveiligingsproblemen te vinden en op te lossen, toegangs- en toepassingsbesturingselementen toe te passen om schadelijke activiteiten te blokkeren, bedreigingen te detecteren met behulp van analyses en gegevens en snel te reageren bij aanvallen. U kunt Security Center Standard kosteloos proberen. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie.

In dit artikel gaat u upgraden naar de prijscategorie Standard voor extra beveiliging en MMA installeren op uw virtuele machines om te controleren op beveiligingsproblemen en bedreigingen.

## <a name="prerequisites"></a>Vereisten
U moet over een abonnement op Microsoft Azure beschikken om met Security Center aan de slag te gaan. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/pricing/free-trial/).

Als u een abonnement wilt upgraden naar de prijscategorie Standard, moet de rol Abonnementhouder, Inzender of Beveiligingsbeheerder aan u zijn toegewezen.

## <a name="enable-your-azure-subscription"></a>Uw Azure-abonnement inschakelen

1. Meld u aan bij de [Azure-portal](https://azure.microsoft.com/features/azure-portal/).
2. Selecteer In het menu **Microsoft Azure** de optie **Beveiligingscentrum**. **Security Center - Overzicht** wordt geopend.

   ![Overzicht van Security Center][2]

**Security Center - overzicht** biedt een duidelijk overzicht van de beveiligingsstatus van uw hybride cloudworkloads, zodat u de beveiliging van uw workloads kunt bepalen en beoordelen en risico's kunt herkennen en verminderen. Security Center activeert automatisch de Azure-abonnementen naar de Gratis laag waarvoor u of een andere abonnementsgebruiker niet eerder onboarding hebt uitgevoerd.

U kunt de lijst met abonnementen bekijken en filteren door op het menu-item **Abonnementen** te klikken. Security Center begint met het beoordelen van de beveiliging van deze abonnementen om beveiligingsproblemen te identificeren. U kunt het beveiligingsbeleid wijzigen om de soorten evaluaties aan te passen. Een beveiligingsbeleid definieert de gewenste configuratie van uw workloads en helpt ervoor te zorgen dat aan de beveiligingsvereisten van het bedrijf of aan regelgeving wordt voldaan.

Binnen enkele minuten nadat u Security Center voor het eerst hebt gestart, ziet u mogelijk het volgende:

- **Aanbevelingen** voor manieren om de beveiliging van uw Azure-abonnementen te verbeteren. Als u op de tegel **Aanbevelingen** klikt, wordt er een gerangschikte lijst geopend.
- Een overzicht van de resources **Compute en apps**, **Netwerken**, **Gegevensbeveiliging** en **Identiteit en toegang** die nu worden beoordeeld in Security Center, samen met de beveiligingsstatus van elke resource.

Voltooi de stappen hieronder om naar de prijscategorie Standard te upgraden en de MMA te installeren om zo volledig van Security Center te profiteren.

## <a name="upgrade-to-the-standard-tier"></a>Upgraden naar de prijscategorie Standard
Voor de snelstartgidsen en zelfstudies van Security Center moet u upgraden naar de prijscategorie Standard. Er is een gratis proefversie van Security Center Standard. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie. 

1. Selecteer in het hoofdmenu van Security Center de optie **Aan de slag**.
 
   ![Aan de slag][4]

2. Onder **Upgrade** vermeldt Security Center abonnementen en werkruimten die in aanmerking komen voor onboarding. 
   - U kunt op het uitvouwbare **Uw proefversie toepassen** klikken om een lijst met alle abonnementen en werkruimten te bekijken met de status voor een proefversie.
   -    U kunt abonnementen en werkruimten upgraden die niet in aanmerking komen voor een proefversie.
   -    U kunt in aanmerking komende werkruimten en abonnementen selecteren om uw proefversie te beginnen.
3. Klik op **Proefversie beginnen** om uw proefversie van de geselecteerde abonnementen te beginnen.


  ![Beveiligingswaarschuwingen][9]

## <a name="automate-data-collection"></a>Gegevensverzameling automatiseren
Security Center verzamelt gegevens van uw Azure-VM's en niet-Azure-computers om te controleren op beveiligingsproblemen en bedreigingen. De gegevens worden verzameld met behulp van de MMA, die verschillende configuraties en gebeurtenislogboeken met betrekking tot beveiliging van de machine leest en de gegevens kopieert naar uw werkruimte voor analyse. Security Center maakt standaard een nieuwe werkruimte voor u.

Als automatisch inrichten is ingeschakeld, installeert Security Center de MMA op alle ondersteunde Azure-VM's en op nieuwe VM's. Automatische inrichting wordt sterk aanbevolen.

Automatische inrichting van de MMA inschakelen:

1. Selecteer onder het hoofdmenu Van het Beveiligingscentrum de optie **Prijzen & instellingen**.
2. Klik in de rij van het abonnement op het abonnement waarop u de instellingen wilt wijzigen.
3. Stel in het tabblad **Gegevensverzameling****Automatische inrichting** in op **Aan**.
4. Selecteer **Opslaan**.
---
  ![Automatische inrichting inschakelen][6]

Security Center kan deze nieuwe inzichten in uw Azure-VM's gebruiken om extra aanbevelingen te doen met betrekking tot de updatestatus van het systeem, de beveiligingsconfiguraties van het besturingssysteem en de eindpuntbeveiliging. Ook kan Security Center aanvullende beveiligingswaarschuwingen genereren.

  ![Aanbevelingen][8]

## <a name="clean-up-resources"></a>Resources opschonen
Andere snelstartgidsen en zelfstudies in deze verzameling zijn gebaseerd op deze snelstartgids. Als u de volgende snelstartgidsen en zelfstudies ook wilt doornemen, blijf dan de prijscategorie Standard gebruiken en houd automatische inrichting ingeschakeld. Als u niet wilt doorgaan of wilt terugkeren naar de laag gratis:

1. Ga terug naar het hoofdmenu van het Beveiligingscentrum en selecteer **Prijzen & instellingen.**
2. Klik op het abonnement dat u wilt wijzigen in de gratis laag.
3. Selecteer **Prijscategorie** en vervolgens **Gratis** om het abonnement te wijzigen van Standard in Gratis.
5. Selecteer **Opslaan**.

Als u automatisch inrichten wilt uitschakelen:

1. Ga terug naar het hoofdmenu van het Beveiligingscentrum en selecteer **Prijzen & instellingen.**
2. Maak schoon op het abonnement waarop u de automatische inrichting wilt uitschakelen.
3. Stel in het tabblad **Gegevensverzameling****Automatische inrichting** in op **Uit**.
4. Selecteer **Opslaan**.

>[!NOTE]
> Wanneer u automatische inrichting uitschakelt, wordt MMA niet verwijderd van Azure-VM's waarop de agent is ingericht. Door automatische inrichting uit te schakelen, wordt de beveiligingsbewaking voor uw resources beperkt.
>

## <a name="next-steps"></a>Volgende stappen
In deze snelstartgids hebt u een upgrade naar de prijscategorie Standard uitgevoerd en de MMA ingericht voor geïntegreerd beveiligingsbeheer en bescherming tegen bedreigingen voor uw hybride cloudworkloads. Ga naar de snelstartgids voor het onboarden van Windows-computers die zich on-premises en in andere clouds bevinden voor meer informatie over hoe u Security Center kunt gebruiken.

> [!div class="nextstepaction"]
> [Snelstart: Windows-computers onboarden naar Azure Security Center](quick-onboard-windows-computer.md)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png
