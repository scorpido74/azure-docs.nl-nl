---
title: IT Service Management-connector-beveiligd exporteren in Azure Monitor
description: Dit artikel bevat informatie over het verbinden van uw ITSM-producten/-services met de beveiligde export in Azure Monitor om de ITSM-werk items centraal te controleren en te beheren.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 160054e7e98dc2cb06c2c7daf325536766963daa
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2020
ms.locfileid: "89568639"
---
# <a name="connect-azure-to-itsm-tools-using-secure-export"></a>Verbinding maken tussen Azure en ITSM-hulpprogram ma's met behulp van beveiligde export

Dit artikel bevat informatie over het configureren van de verbinding tussen uw ITSM-product en-service met behulp van beveiligde export.

Beveiligde export is een bijgewerkte versie van [ITSMC](./itsmc-overview.md) (IT-Service beheer). Met beide versies kunt u werk items maken in een ITSM-hulp programma wanneer Azure Monitor waarschuwingen worden geactiveerd. De functionaliteit omvat waarschuwingen voor metrische gegevens, logboeken en activiteiten Logboeken.

[ITSMC](./itsmc-overview.md) maakt gebruik van gebruikers-en wachtwoord referenties, terwijl beveiligde export een sterkere verificatie heeft, omdat deze gebruikmaakt van Azure Active Directory (Azure AD). Azure Active Directory (Azure AD) is de identiteits- en toegangsbeheerservice van Microsoft op basis van de cloud. Het helpt gebruikers zich aan te melden en toegang te krijgen tot interne of externe resources. Met Azure AD met ITSM kunt u Azure-waarschuwingen identificeren (met de Azure AD-toepassings-ID) die naar het externe systeem zijn verzonden.

> [!NOTE]
> De hulpprogram ma's voor het verbinden van Azure met ITSM met behulp van beveiligd exporteren is in Preview

## <a name="secure-export-architecture"></a>Beveiligde export architectuur

De Secure export-architectuur introduceert de volgende nieuwe mogelijkheden:

* **Nieuwe actie groep** : waarschuwingen worden verzonden naar het ITSM-hulp programma met behulp van de actie groep voor beveiligde webhooks (in plaats van ITSM actie groep met in ITSMC).
* **Verificatie van Azure AD** -verificatie vindt plaats met behulp van Azure AD in plaats van gebruikers-en wachtwoord referenties.

## <a name="secure-export-data-flow"></a>Gegevens stroom voor beveiligde export

De stappen voor beveiligde export gegevens stroom zijn:

1) Een waarschuwing die is geconfigureerd voor het gebruik van beveiligde export wordt geactiveerd in Azure Monitor
2) De nettolading van de waarschuwing wordt door een beveiligde webhook-actie verzonden naar het ITSM-hulp programma.
3) De ITSM-toepassing controleert met Azure AD als de waarschuwing is gemachtigd om het ITSM-hulp programma in te voeren.
4) Als de waarschuwing de toepassing heeft geautoriseerd:
    1) Hiermee maakt u een werk item (bijvoorbeeld incident) in het ITSM-hulp programma.
    2) Koppelt de ID van het configuratie-item (CI) aan de klant beheer database (CMDB).
![ITSM diagram](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="connection-with-bmc-helix"></a>Verbinding met BMC Helix

Secure export ondersteunt BMC helix. Enkele voor delen van de integratie zijn:

* **Betere verificatie** : Azure AD biedt veiliger verificatie zonder de time-outs die zich vaak voordoen in ITSMC.
* **Waarschuwingen die zijn opgelost in het hulp programma ITSM** – metrische waarschuwingen implementeren een ' geactiveerde ' en ' opgeloste ' status. Wanneer aan de voor waarde wordt voldaan, wordt de status van de waarschuwing geactiveerd. Als niet meer aan de voor waarde wordt voldaan, is de waarschuwings status opgelost. In ITSMC kunnen waarschuwingen niet automatisch worden opgelost. Met beveiligde export stromen de omgezette status naar het ITSM-hulp programma en worden updates automatisch uitgevoerd.
* **[Gebruikelijk schema](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema)** : in ITSMC kan het schema van de nettolading van de waarschuwing afwijken van het type waarschuwing. In Secure export hebben we een gemeen schappelijk schema voor alle waarschuwings typen. Dit nieuwe algemene schema bevat de CI voor alle waarschuwings typen. Doordat alle waarschuwings typen hun CI met de CMDB kunnen binden.

Begin met het gebruik van de ITSM-connector met de volgende stappen:

1. Registreer uw app bij Azure Active Directory.
2. Maak een beveiligde webhook-actie groep.
3. Configureer uw partner omgeving.

## <a name="register-with-azure-active-directory"></a>Registreren bij Azure Active Directory

Volg deze stappen voor het registreren van een Azure AD-toepassing met Azure Active Directory

1) [Azure AD maken](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)
2) Selecteer in de Azure Active Directory ' toepassing beschikbaar maken '
3) Selecteer set in de URI van de toepassings-ID [ ![ Azure AD](media/it-service-management-connector-secure-webhook-connections/azure-ad.png)](media/it-service-management-connector-secure-webhook-connections/azure-ad-expand.png#lightbox)
4) Klik op opslaan.

## <a name="create-a-secure-webhook-action-group"></a>Een actie groep voor een beveiligde webhook maken

Zodra u uw Azure AD hebt geregistreerd, kunt u een of meer werk items maken in uw ITSM-hulp programma op basis van Azure-waarschuwingen met behulp van de actie beveiligde webhook in actie groepen.
Actie groepen bieden een modulaire en herbruikbare manier om acties voor uw Azure-waarschuwingen te activeren. U kunt actie groepen met metrische waarschuwingen, waarschuwingen voor activiteiten logboeken en waarschuwingen voor Azure Log Analytics gebruiken in Azure Portal.
Zie voor meer informatie over actie groepen [actie groepen maken en beheren in de Azure Portal](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).
Gebruik de volgende procedure:

In de BMC Helix-omgeving:

1. Meld u aan bij Integration Studio.
2. Zoek naar de stroom incident maken vanuit Azure Alerts.
3. Kopieer de webhook-URL.
![BMC-URL](media/it-service-management-connector-secure-webhook-connections/bmc-url.png)

Volg de instructies voor een beveiligde webhook om een webhook aan een actie toe te voegen:

1. Zoek in het [Azure Portal](https://portal.azure.com/)naar en selecteer **monitor**. In het deel venster **monitor** worden al uw bewakings instellingen en-gegevens in één weer gave geconsolideerd.
2. Selecteer **Waarschuwingen** en vervolgens **Acties beheren**.
3. Selecteer [actie groep toevoegen](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#create-an-action-group-by-using-the-azure-portal)en vul de velden in.
4. Voer een naam in het vak Naam van de **actie groep** in en voer een naam in het vak **korte naam** in. De korte naam wordt gebruikt in plaats van een volledige naam van de actiegroep als er meldingen via deze groep worden verzonden.
5. **Beveiligde webhook** selecteren
6. Selecteer Details bewerken. De volgende afbeelding toont een voor beeld van een beveiligde webhook-actie:
    1. Selecteer de juiste object-ID van de Azure Active Directory die u hebt geregistreerd
    2. Plak het veld in URI de webhook-URL die u hebt gekopieerd uit de ' BMC Helix-omgeving '
    3. Stel **gebruikelijk waarschuwings schema** in op **Ja**. 
7. In de volgende afbeelding wordt een voor beeld van een beveiligde webhook-actie configuratie weer gegeven: ![ beveiligde webhook](media/it-service-management-connector-secure-webhook-connections/secure-webhook.png)

## <a name="configure-partner-environment"></a>Partner omgeving configureren

### <a name="connect-bmc-helix-to-azure-monitor"></a>BMC Helix verbinden met Azure Monitor

De volgende sectie bevat informatie over hoe u verbinding kunt maken met uw BMC Helix-product en hoe u de export in azure kunt beveiligen.

### <a name="prerequisites"></a>Vereisten

Zorg ervoor dat aan de volgende vereisten wordt voldaan:

* Azure AD is geregistreerd.
* De ondersteunde versie van BMC Helix multi-Cloud Service Management: 20,02-versie of hoger

De BMC Helix-verbinding configureren:

1) [Het inschakelen van vooraf gebouwde integratie met Azure Monitor voor versie 20,2](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html)

2) Als onderdeel van de configuratie van de verbinding in de BMC Helix gaat u naar uw integratie-BMC-exemplaar en volgt u de instructies:

1. **Catalogus** selecteren
2. **Azure-waarschuwingen** selecteren
3. **Connectors** selecteren
4. **Configuratie** selecteren
5. Selecteer **nieuwe verbindings configuratie toevoegen**
6. Vul de informatie in voor de configuratie sectie.
    1. **Naam** : Maak uw eigen
    2. **Autorisatie type** -geen
    3. **Beschrijving**: Maak uw eigen
    4. **Site**-Cloud
    5. **Aantal instanties** -2-standaard waarde
    6. **Controleren** -standaard ingeschakeld en gebruik inschakelen
    7. Azure-Tenant-ID, Azure-toepassings-ID afkomstig uit de toepassing die in de stap ' gemaakt Azure Active Directory ' is gedefinieerd.
![BMC-configuratie](media/it-service-management-connector-secure-webhook-connections/bmc-configuration.png)

## <a name="next-steps"></a>Volgende stappen

* [ITSM-werk items maken op basis van Azure-waarschuwingen](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
