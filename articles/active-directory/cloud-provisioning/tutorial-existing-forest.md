---
title: 'Zelf studie: een bestaand forest en een nieuw forest met één Azure AD-Tenant integreren met Azure AD Connect Cloud inrichting.'
description: vind.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80332269"
---
# <a name="integrate-an-existing-forest-and-a-new-forest-with-a-single-azure-ad-tenant"></a>Een bestaande forest en een nieuwe forest integreren met enkele Azure AD-tenant

In deze zelf studie wordt u begeleid bij het toevoegen van Cloud inrichting aan een bestaande hybride identiteits omgeving. 

![Maken](media/tutorial-existing-forest/existing-forest-new-forest.png)

U kunt de omgeving die u in deze zelf studie maakt, gebruiken om te testen of om meer vertrouwd te raken met de werking van een Hybrid Identity. 

In dit scenario wordt een bestaand forest gesynchroniseerd met Azure AD Connect gesynchroniseerd met een Azure AD-Tenant. En u hebt een nieuw forest dat u wilt synchroniseren met dezelfde Azure AD-Tenant. U gaat de inrichting van de Cloud instellen voor het nieuwe forest. 

## <a name="prerequisites"></a>Vereisten
### <a name="in-the-azure-active-directory-admin-center"></a>In het Azure Active Directory-beheer centrum

1. Maak een alleen-Cloud account voor globale beheerders op uw Azure AD-Tenant. Op deze manier kunt u de configuratie van uw Tenant beheren als uw on-premises Services mislukken of niet meer beschikbaar zijn. Meer informatie over [het toevoegen van een globale beheerders account voor de Cloud](../active-directory-users-create-azure-portal.md). Het volt ooien van deze stap is van cruciaal belang om ervoor te zorgen dat de Tenant niet wordt vergrendeld.
2. Voeg een of meer [aangepaste domein namen](../active-directory-domains-add-azure-portal.md) toe aan uw Azure AD-Tenant. Uw gebruikers kunnen zich aanmelden met een van deze domein namen.

### <a name="in-your-on-premises-environment"></a>In uw on-premises omgeving

1. De identiteit van een hostserver die lid is van een domein en waarop Windows Server 2012 R2 of hoger wordt uitgevoerd met mini maal 4 GB RAM en .NET 4.7.1 + runtime 

2. Als er een firewall is tussen uw servers en Azure AD, moet u de volgende items configureren:
   - Zorg ervoor dat agenten *uitgaande* aanvragen kunnen indienen bij Azure AD via de volgende poorten:

     | Poortnummer | Hoe dat wordt gebruikt |
     | --- | --- |
     | **80** | Hiermee worden de certificaatintrekkingslijsten (Crl's) gedownload tijdens het valideren van het TLS/SSL-certificaat |
     | **443** | Hiermee wordt alle uitgaande communicatie met de service verwerkt |
     | **8080** (optioneel) | Agents rapporteren hun status elke 10 minuten via poort 8080, als poort 443 niet beschikbaar is. Deze status wordt weer gegeven in de Azure AD-Portal. |
     
     Als uw firewall regels afdwingt op basis van de oorspronkelijke gebruikers, opent u deze poorten voor verkeer van Windows-services die als een netwerk service worden uitgevoerd.
   - Als u met uw firewall of proxy veilige achtervoegsels kunt opgeven, voegt u vervolgens verbindingen toe aan ** \*. msappproxy.net** en ** \*. servicebus.Windows.net**. Als dat niet het geval is, verleent u toegang tot de [IP-bereiken van het Azure-Data Center](https://www.microsoft.com/download/details.aspx?id=41653), die wekelijks worden bijgewerkt.
   - Uw agenten hebben toegang tot **login.Windows.net** en **login.microsoftonline.com** nodig voor de eerste registratie. Open ook uw firewall voor deze Url's.
   - Voor validatie van het certificaat kunt u de volgende Url's blok keren: **mscrl.Microsoft.com:80**, **CRL.Microsoft.com:80**, **OCSP.msocsp.com:80**en **www\.Microsoft.com:80**. Omdat deze Url's worden gebruikt voor certificaat validatie met andere micro soft-producten, is het mogelijk dat deze Url's al worden gedeblokkeerd.

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>De Azure AD Connect-inrichtings agent installeren
1. Meld u aan bij de server die lid is van het domein.  Als u de Basic-zelf studie over [AD en Azure-omgeving](tutorial-basic-ad-azure.md) gebruikt, zou deze DC1 zijn.
2. Meld u aan bij de Azure Portal met behulp van globale beheerders referenties voor de Cloud.
3. Selecteer aan de linkerkant **Azure Active Directory**, klik op **Azure AD Connect** en selecteer in het midden de optie **inrichting beheren (preview)**.</br>
![Azure Portal](media/how-to-install/install6.png)</br>
4. Klik op agent downloaden
5. De Azure AD Connect-inrichtings agent uitvoeren
6. **Accepteer** de licentie voorwaarden in het welkomst scherm en klik op **installeren**.</br>
![Welkomstscherm](media/how-to-install/install1.png)</br>

7. Zodra deze bewerking is voltooid, wordt de configuratie wizard gestart.  Meld u aan met uw Azure AD Global Administrator-account.  Houd er rekening mee dat als u verbeterde beveiliging van Internet Explorer hebt ingeschakeld, het aanmelden wordt geblokkeerd.  Als dit het geval is, sluit u de installatie, schakelt u verbeterde beveiliging van Internet Explorer in Serverbeheer uit en klikt u op de **wizard Aad Connect inrichtings agent** om de installatie opnieuw te starten.
8. Klik in het scherm **verbinding maken Active Directory** op **map toevoegen** en meld u vervolgens aan met uw Active Directory domein beheerders account.  Opmerking: het domein beheerders account mag geen vereisten voor wachtwoord wijziging hebben. Als het wacht woord is verlopen of gewijzigd, moet u de agent opnieuw configureren met de nieuwe referenties. Met deze bewerking wordt uw on-premises Directory toegevoegd.  Klik op **Volgende**.</br>
![Welkomstscherm](media/how-to-install/install3.png)</br>

9. Klik in het scherm **configuratie voltooid** op **bevestigen**.  Met deze bewerking wordt de agent geregistreerd en opnieuw gestart.</br>
![Welkomstscherm](media/how-to-install/install4.png)</br>

10. Zodra deze bewerking is voltooid, ziet u een melding: **de configuratie van de agent is geverifieerd.**  U kunt op **Afsluiten**klikken.</br>
![Welkomstscherm](media/how-to-install/install5.png)</br>
11. Als het eerste openings scherm nog steeds wordt weer gegeven, klikt u op **sluiten**.


## <a name="verify-agent-installation"></a>Agent installatie verifiëren
Verificatie van de agent vindt plaats in de Azure Portal en op de lokale server waarop de-agent wordt uitgevoerd.

### <a name="azure-portal-agent-verification"></a>Verificatie van Azure Portal-agent
Voer de volgende stappen uit om te controleren of de agent wordt gezien door Azure:

1. Meld u aan bij Azure Portal.
2. Selecteer aan de linkerkant **Azure Active Directory**, klik op **Azure AD Connect** en selecteer in het midden de optie **inrichting beheren (preview)**.</br>
![Azure Portal](media/how-to-install/install6.png)</br>

3.  Klik in het scherm **Azure AD inrichten (preview)** op **Alle agents controleren**.
![Azure AD-inrichting](media/how-to-install/install7.png)</br>
 
4. Op het **scherm on-premises Provisioning agents** ziet u de agents die u hebt geïnstalleerd.  Controleer of de agent in kwestie is en is gemarkeerd als **actief**.
![Inrichtings agenten](media/how-to-install/verify1.png)</br>

### <a name="on-the-local-server"></a>Op de lokale server
Voer de volgende stappen uit om te controleren of de agent wordt uitgevoerd:

1.  Meld u aan bij de server met een beheerders account
2.  Open **Services** door ernaar te navigeren of door naar start/uitvoeren/services. msc te gaan.
3.  Zorg er bij **Services**voor dat **Microsoft Azure AD connect agent updater** en **Microsoft Azure AD Connect inrichtings agent** aanwezig zijn en of de status wordt **uitgevoerd**.
![Services](media/how-to-troubleshoot/troubleshoot1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Azure AD Connect Cloud inrichting configureren
 Gebruik de volgende stappen om inrichting te configureren

1.  Meld u aan bij de Azure AD-Portal.
2.  Klik op **Azure Active Directory**
3.  Klik op **Azure AD Connect**
4.  Selecteer **inrichting beheren (preview-versie)**
![](media/how-to-configure/manage1.png)
5.  Klik op **nieuwe configuratie**
![](media/tutorial-single-forest/configure1.png)
7.  Voer in het scherm configuratie een **e-mail melding**in, verplaats de selector naar **in en klik** op **Opslaan**.
![](media/tutorial-single-forest/configure2.png)
1.  De configuratie status moet nu **in orde**zijn.
![](media/how-to-configure/manage4.png)

## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Controleer of gebruikers zijn gemaakt en synchronisatie plaatsvindt
U gaat nu controleren of de gebruikers die u in onze on-premises Directory had, zijn gesynchroniseerd en nu aanwezig zijn in onze Azure AD-Tenant.  Dit synchronisatieproces kan enkele uren duren.  Ga als volgt te werk om te controleren of gebruikers zijn gesynchroniseerd.


1. Meld u bij de [Azure Portal](https://portal.azure.com) aan met een account waaraan een Azure-abonnement is gekoppeld.
2. Selecteer links **Azure Active Directory**
3. Onder **Beheren**, selecteer **Gebruikers**.
4. Kijk of u de nieuwe gebruikers ziet in onze tenant.</br>
![Synchroniseren](media/tutorial-single-forest/synchronize1.png)</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Aanmelden testen met een van onze gebruikers

1. Bladeren naar[https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Meld u aan met een gebruikersaccount dat is gemaakt in onze nieuwe tenant.  U moet zich aanmelden met de volgende indeling: (user@domain.onmicrosoft.com). Gebruik hetzelfde wachtwoord dat de gebruiker gebruikt om zich on-premises aan te melden.</br>
   ![Verifiëren](media/tutorial-single-forest/verify1.png)</br>

U hebt nu een hybride identiteits omgeving ingesteld die u kunt gebruiken om te testen en vertrouwd te raken met wat Azure te bieden heeft.

## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect-cloudinrichting?](what-is-cloud-provisioning.md)
