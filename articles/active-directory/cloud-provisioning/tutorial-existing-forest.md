---
title: Zelfstudie - Integreer een bestaand forest en een nieuw forest met één Azure AD-tenant met Azure AD Connect-cloudinrichting.
description: Tutorial.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa612ad30ae0faa42071613be15c1d91fb96b8f6
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80332269"
---
# <a name="integrate-an-existing-forest-and-a-new-forest-with-a-single-azure-ad-tenant"></a>Een bestaande forest en een nieuwe forest integreren met enkele Azure AD-tenant

Met deze zelfstudie u cloudprovisioning toevoegen aan een bestaande hybride identiteitsomgeving. 

![Maken](media/tutorial-existing-forest/existing-forest-new-forest.png)

U de omgeving die u in deze zelfstudie maakt gebruiken om te testen of om meer vertrouwd te raken met hoe een hybride identiteit werkt. 

In dit scenario wordt een bestaand forest gesynchroniseerd met Azure AD Connect-synchronisatie met een Azure AD-tenant. En u hebt een nieuw forest dat u wilt synchroniseren met dezelfde Azure AD-tenant. U zult cloudprovisioning instellen voor het nieuwe forest. 

## <a name="prerequisites"></a>Vereisten
### <a name="in-the-azure-active-directory-admin-center"></a>In het Azure Active Directory-beheercentrum

1. Maak een algemeen beheerdersaccount voor de cloud op uw Azure AD-tenant. Op deze manier u de configuratie van uw tenant beheren als uw on-premises services mislukken of niet meer beschikbaar zijn. Meer informatie over [het toevoegen van een wereldwijd beheerdersaccount voor alleen cloud](../active-directory-users-create-azure-portal.md). Het voltooien van deze stap is essentieel om ervoor te zorgen dat u niet buitengesloten van uw huurder.
2. Voeg een of meer [aangepaste domeinnamen](../active-directory-domains-add-azure-portal.md) toe aan uw Azure AD-tenant. Uw gebruikers kunnen zich aanmelden met een van deze domeinnamen.

### <a name="in-your-on-premises-environment"></a>In uw on-premises omgeving

1. Een hostserver met domeinlid met Windows Server 2012 R2 of hoger met minimaal 4 GB RAM en .NET 4.7.1+ runtime 

2. Als er een firewall is tussen uw servers en Azure AD, configureert u de volgende items:
   - Controleer of agents *uitgaande* aanvragen kunnen indienen bij Azure AD via de volgende poorten:

     | Poortnummer | Hoe dat wordt gebruikt |
     | --- | --- |
     | **80** | Downloadt de certificaatintrekkingslijsten (CRL's) terwijl het TLS/SSL-certificaat wordt valideren |
     | **443** | Verwerkt alle uitgaande communicatie met de service |
     | **8080** (optioneel) | Agenten melden hun status elke 10 minuten via poort 8080, als poort 443 niet beschikbaar is. Deze status wordt weergegeven op de Azure AD-portal. |
     
     Als uw firewall regels afdwingt volgens de oorspronkelijke gebruikers, opent u deze poorten voor verkeer van Windows-services die als netwerkservice worden uitgevoerd.
   - Als u met uw firewall of proxy veilige achtervoegsels kunt ** \*** opgeven, voegt u verbindingen toe aan ** \*.msappproxy.net** en servicebus.windows.net . Zo niet, geef dan toegang tot de [IP-bereiken van Azure-datacenters,](https://www.microsoft.com/download/details.aspx?id=41653)die wekelijks worden bijgewerkt.
   - Uw agenten hebben toegang nodig tot **login.windows.net** en **login.microsoftonline.com** voor de eerste registratie. Open uw firewall ook voor die URL's.
   - Voor certificaatvalidatie deblokkeren van de volgende URL's: **mscrl.microsoft.com:80,** **crl.microsoft.com:80,** **ocsp.msocsp.com:80**en **\.www microsoft.com:80**. Aangezien deze URL's worden gebruikt voor certificaatvalidatie met andere Microsoft-producten, u deze URL's al laten deblokkeren.

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>De Azure AD Connect-inrichtingsagent installeren
1. Meld u aan bij de domeinserver.  Als u de zelfstudie [voor de basisad- en Azure-omgeving](tutorial-basic-ad-azure.md) gebruikt, is dit DC1.
2. Meld u aan bij de Azure-portal met globale beheerdersreferenties van alleen de cloud.
3. Selecteer aan de linkerkant **Azure Active Directory**, klik op Azure AD **Connect** en selecteer in het midden **Provisioning beheren (voorbeeld)**.</br>
![Azure Portal](media/how-to-install/install6.png)</br>
4. Klik op 'Download agent'
5. De Azure AD Connect-inrichtingsagent uitvoeren
6. **Accepteer** op het welkomstscherm de licentievoorwaarden en klik op **Installeren**.</br>
![Welkomstscherm](media/how-to-install/install1.png)</br>

7. Zodra deze bewerking is voltooid, wordt de wizard configuratie gestart.  Meld u aan met uw azure AD-globale beheerdersaccount.  Houd er rekening mee dat als u IE-verbeterde beveiliging hebt ingeschakeld, dit de aanmelding blokkeert.  Als dit het geval is, sluit u de installatie, schakelt u de verbeterde beveiliging van IE in Serverbeheer uit en klikt u op de **wizard AAD Connect Provisioning Agent** om de installatie opnieuw te starten.
8. Klik in het scherm **Active Directory verbinden** op Map **toevoegen** en meld u vervolgens aan met uw Active Directory-domeinbeheerdersaccount.  OPMERKING: Het domeinbeheerdersaccount mag geen vereisten voor wachtwoordwijziging hebben. Als het wachtwoord verloopt of verandert, moet u de agent opnieuw configureren met de nieuwe referenties. Met deze bewerking wordt uw on-premises directory toegevoegd.  Klik op **Volgende**.</br>
![Welkomstscherm](media/how-to-install/install3.png)</br>

9. Klik **op het scherm Configuratie voltooid** op **Bevestigen**.  Deze bewerking registreert en start de agent opnieuw.</br>
![Welkomstscherm](media/how-to-install/install4.png)</br>

10. Zodra deze bewerking is voltooid, ziet u een melding: **uw agentconfiguratie is geverifieerd.**  U op **Afsluiten**klikken.</br>
![Welkomstscherm](media/how-to-install/install5.png)</br>
11. Als u nog steeds het eerste welkomstscherm ziet, klikt u op **Sluiten**.


## <a name="verify-agent-installation"></a>Agentinstallatie verifiëren
Agentverificatie vindt plaats in de Azure-portal en op de lokale server waarop de agent wordt uitgevoerd.

### <a name="azure-portal-agent-verification"></a>Azure-portalagentverificatie
Als u wilt controleren of de agent wordt gezien door Azure, voert u de volgende stappen uit:

1. Meld u aan bij Azure Portal.
2. Selecteer aan de linkerkant **Azure Active Directory**, klik op Azure AD **Connect** en selecteer in het midden **Provisioning beheren (voorbeeld)**.</br>
![Azure Portal](media/how-to-install/install6.png)</br>

3.  Klik in het scherm **Azure AD Provisioning (preview)** op **Alle agents controleren**.
![Azure AD-inrichting](media/how-to-install/install7.png)</br>
 
4. Op het **scherm On-premises provisioning agents** ziet u de agents die u hebt geïnstalleerd.  Controleer of de agent in kwestie aanwezig is en **actief**is gemarkeerd .
![Voorzieningsmiddelen](media/how-to-install/verify1.png)</br>

### <a name="on-the-local-server"></a>Op de lokale server
Volg de volgende stappen om te controleren of de agent wordt uitgevoerd:

1.  Inloggen op de server met een beheerdersaccount
2.  Open **Services** door ernaar te navigeren of door naar Start/Run/Services.msc te gaan.
3.  Controleer **onder Services**of Microsoft Azure AD Connect Agent **Updater** en Microsoft Azure AD Connect **Provisioning Agent** aanwezig zijn en of de status wordt **uitgevoerd**.
![Services](media/how-to-troubleshoot/troubleshoot1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Azure AD Connect-cloudinrichting configureren
 De volgende stappen gebruiken om de inrichting te configureren

1.  Meld u aan bij de Azure AD-portal.
2.  Klik **op Azure Active Directory**
3.  Klik **op Azure AD Connect**
4.  Inrichten **beheren selecteren (voorbeeld)**
![](media/how-to-configure/manage1.png)
5.  Klik op **Nieuwe configuratie**
![](media/tutorial-single-forest/configure1.png)
7.  Voer in het configuratiescherm een **e-mail met melding**in, verplaats de kiezer naar **Inschakelen** en klik op **Opslaan**.
![](media/tutorial-single-forest/configure2.png)
1.  De configuratiestatus moet nu **gezond**zijn.
![](media/how-to-configure/manage4.png)

## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Controleer of gebruikers zijn gemaakt en synchronisatie plaatsvindt
U controleert nu of de gebruikers die u in onze on-premises directory had, zijn gesynchroniseerd en nu bestaan in onze Azure AD-tenant.  Dit synchronisatieproces kan enkele uren duren.  Ga als volgt te werk om te controleren of gebruikers zijn gesynchroniseerd.


1. Meld u bij de [Azure Portal](https://portal.azure.com) aan met een account waaraan een Azure-abonnement is gekoppeld.
2. Selecteer links **Azure Active Directory**
3. Onder **Beheren**, selecteer **Gebruikers**.
4. Kijk of u de nieuwe gebruikers ziet in onze tenant.</br>
![Synchroniseren](media/tutorial-single-forest/synchronize1.png)</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Aanmelden testen met een van onze gebruikers

1. Blader naar[https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Meld u aan met een gebruikersaccount dat is gemaakt in onze nieuwe tenant.  U moet zich aanmelden met de volgende indeling: (user@domain.onmicrosoft.com). Gebruik hetzelfde wachtwoord dat de gebruiker gebruikt om zich on-premises aan te melden.</br>
   ![Verifiëren](media/tutorial-single-forest/verify1.png)</br>

U hebt nu met succes een hybride identiteitsomgeving ingesteld die u gebruiken om uzelf te testen en vertrouwd te maken met wat Azure te bieden heeft.

## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect-cloudinrichting?](what-is-cloud-provisioning.md)
