---
title: Azure Monitor-werkmappen gebruiken met Azure AD Domain Services | Microsoft Documenten
description: Meer informatie over het gebruik van Azure Monitor Werkmappen om beveiligingsaudits te controleren en problemen in een beheerd Azure Directory Domain Services-domein te begrijpen.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/18/2020
ms.author: iainfou
ms.openlocfilehash: bdfc7d37d99dc5511f47e33d1848c3f142a9693e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654457"
---
# <a name="review-security-audit-events-in-azure-ad-domain-services-using-azure-monitor-workbooks"></a>Beveiligingscontrolegebeurtenissen in Azure AD Domain Services controleren met Azure Monitor-werkmappen

U beveiligingscontrolegebeurtenissen inschakelen om inzicht te krijgen in de status van uw azure active directory domain services (Azure AD DS)-domein. Deze beveiligingscontrolegebeurtenissen kunnen vervolgens worden beoordeeld met Azure Monitor-werkmappen die tekst, analysequery's en parameters combineren tot uitgebreide interactieve rapporten. Azure AD DS bevat werkmapsjablonen voor beveiligingsoverzicht en accountactiviteit waarmee u zich verdiepen in controlegebeurtenissen en uw omgeving beheren.

In dit artikel ziet u hoe u Azure Monitor Workbooks gebruiken om beveiligingscontrolegebeurtenissen in Azure AD DS te controleren.

## <a name="before-you-begin"></a>Voordat u begint

Als u dit artikel wilt voltooien, hebt u de volgende bronnen en bevoegdheden nodig:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan .
* Een Azure Active Directory-tenant die is gekoppeld aan uw abonnement, gesynchroniseerd met een on-premises directory of een map met alleen wolken.
    * Maak indien nodig [een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account.][associate-azure-ad-tenant]
* Een beheerd azure Directory Domain Services-domein is ingeschakeld en geconfigureerd in uw Azure AD-tenant.
    * Vul indien nodig de zelfstudie in om [een Azure Active Directory Domain Services-exemplaar][create-azure-ad-ds-instance]te maken en te configureren.
* Beveiligingscontrolegebeurtenissen die zijn ingeschakeld voor uw beheerde domein van Azure Active Directory Domain Services dat gegevens streamt naar een Log Analytics-werkruimte.
    * Schakel indien nodig [beveiligingsaudits in voor Azure Active Directory Domain Services.][enable-security-audits]

## <a name="azure-monitor-workbooks-overview"></a>Overzicht van Azure Monitor-werkmappen

Wanneer beveiligingscontrolegebeurtenissen zijn ingeschakeld in Azure AD DS, kan het moeilijk zijn om problemen in het beheerde domein te analyseren en te identificeren. Met Azure Monitor u deze beveiligingscontrolegebeurtenissen samenvoegen en de gegevens opvragen. Met Azure Monitor Workbooks u deze gegevens visualiseren om problemen sneller en gemakkelijker te kunnen identificeren.

Werkmapsjablonen zijn samengestelde rapporten die zijn ontworpen voor flexibel hergebruik door meerdere gebruikers en teams. Wanneer u een werkmapsjabloon opent, worden de gegevens uit uw Azure Monitor-omgeving geladen. U sjablonen gebruiken zonder dat dit gevolgen heeft voor andere gebruikers in uw organisatie en u uw eigen werkmappen opslaan op basis van de sjabloon.

Azure AD DS bevat de volgende twee werkmapsjablonen:

* Rapport over beveiligingsoverzicht
* Rapport Accountactiviteit

Zie Overzicht van [Azure Monitor Werkmappen](../azure-monitor/platform/workbooks-overview.md)voor meer informatie over het bewerken en beheren van werkmappen.

## <a name="use-the-security-overview-report-workbook"></a>De werkmap voor het rapport voor het beveiligingsoverzicht gebruiken

Om u te helpen het gebruik beter te begrijpen en potentiële beveiligingsbedreigingen te identificeren, worden in het rapport beveiligingsoverzicht aanmeldingsgegevens samengevat en worden accounts geïdentificeerd waarop u mogelijk wilt controleren. U gebeurtenissen in een bepaald datumbereik bekijken en inzoomen op specifieke aanmeldingsgebeurtenissen, zoals pogingen voor slecht wachtwoord of waarbij het account is uitgeschakeld.

Voer de volgende stappen uit om toegang te krijgen tot de werkmapsjabloon voor het rapport beveiligingsoverzicht:

1. Zoeken naar en selecteer **Azure Active Directory Domain Services** in de Azure-portal.
1. Selecteer uw beheerde domein, zoals *aaddscontoso.com*
1. Kies in het menu aan de linkerkant de optie **Controle > Werkmappen**

    ![De menuoptie Werkmappen selecteren in de Azure-portal](./media/use-azure-monitor-workbooks/select-workbooks-in-azure-portal.png)

1. Kies het **rapport Beveiligingsoverzicht**.
1. Selecteer in de vervolgkeuzemenu's boven aan de werkmap uw Azure-abonnement en vervolgens de werkruimte Azure Monitor. Kies een **tijdsbereik,** zoals *Laatste 7 dagen*.

    ![De menuoptie Werkmappen selecteren in de Azure-portal](./media/use-azure-monitor-workbooks/select-query-filters.png)

    De opties **voor de tegelweergave** en **de grafiekweergave** kunnen ook worden gewijzigd om de gegevens naar wens te analyseren en te visualiseren

1. Als u wilt inzoomen op een specifiek gebeurtenistype, selecteert u het een van de **aanmeldingsresultatenkaarten,** zoals *Account vergrendeld,* zoals weergegeven in het volgende voorbeeld:

    ![Voorbeeld rapportrapportrapportrapportrapportdat is gevisualiseerd in Azure Monitor-werkmappen](./media/use-azure-monitor-workbooks/example-security-overview-report.png)

1. In het onderste gedeelte van het rapport over het beveiligingsoverzicht onder de grafiek wordt vervolgens het geselecteerde activiteitstype afgebroken. U filteren op gebruikersnamen die aan de rechterkant betrokken zijn, zoals in het volgende voorbeeldrapport wordt weergegeven:

    [![](./media/use-azure-monitor-workbooks/account-lockout-details-cropped.png "Details of account lockouts in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-lockout-details.png#lightbox)

## <a name="use-the-account-activity-report-workbook"></a>De werkmap voor het rapport Accountactiviteitsrapport gebruiken

Om problemen voor een specifiek gebruikersaccount op te lossen, worden in het rapport accountactiviteit gedetailleerde informatie over het logboek van de controlegebeurtenis opsplitst. U controleren wanneer een slechte gebruikersnaam of wachtwoord is opgegeven tijdens het aanmelden en de bron van de aanmeldingspoging.

Voer de volgende stappen uit om toegang te krijgen tot de werkmapsjabloon voor het accountactiviteitenrapport:

1. Zoeken naar en selecteer **Azure Active Directory Domain Services** in de Azure-portal.
1. Selecteer uw beheerde domein, zoals *aaddscontoso.com*
1. Kies in het menu aan de linkerkant de optie **Controle > Werkmappen**
1. Kies het **rapport Accountactiviteit**.
1. Selecteer in de vervolgkeuzemenu's boven aan de werkmap uw Azure-abonnement en vervolgens de werkruimte Azure Monitor. Kies een **tijdsbereik,** zoals *Laatste 30 dagen,* en hoe u de **tegelweergave** wilt weergeven. U filteren **op gebruikersnaam van het account**, zoals *felix*, zoals weergegeven in het volgende voorbeeldrapport:

    [![](./media/use-azure-monitor-workbooks/account-activity-report-cropped.png "Account activity report in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-activity-report.png#lightbox)

    Het gebied onder de grafiek toont afzonderlijke aanmeldingsgebeurtenissen, samen met informatie zoals het activiteitsresultaat en het bronwerkstation. Deze informatie kan helpen bij het bepalen van herhaalde bronnen van aanmeldingsgebeurtenissen die accountuitsluitingen kunnen veroorzaken of een potentiële aanval kunnen aangeven.

Net als bij het rapport over het beveiligingsoverzicht u inzoomen op de verschillende tegels boven aan het rapport om de gegevens te visualiseren en te analyseren als dat nodig is.

## <a name="save-and-edit-workbooks"></a>Werkmappen opslaan en bewerken

De twee sjabloonwerkmappen van Azure AD DS zijn een goede plek om te beginnen met uw eigen gegevensanalyse. Als u meer gedetailleerd wilt worden in de gegevensquery's en onderzoeken, u uw eigen werkmappen opslaan en de query's bewerken.

1. Als u een kopie van een van de werkmapsjablonen wilt opslaan, selecteert u **Bewerken > opslaan als > gedeelde rapporten,** geeft u vervolgens een naam op en slaat u deze op.
1. Selecteer **Bewerken** om de bewerkingsmodus in te voeren in uw eigen exemplaar van de sjabloon. U de blauwe knop **Bewerken** naast een deel van het rapport kiezen en deze wijzigen.

Alle grafieken en tabellen in Azure Monitor-werkmappen worden gegenereerd met Kusto-query's. Zie [Azure Monitor-logboekquery's][azure-monitor-queries] en [de zelfstudie met Kusto-query's voor][kusto-queries]meer informatie over het maken van uw eigen query's.

## <a name="next-steps"></a>Volgende stappen

Zie [Beleid voor wachtwoord- en accountuitsluiting op beheerde domeinen][password-policy]als u het beleid voor wachtwoord- en uitsluitingsbeleid moet aanpassen.

Voor problemen met gebruikers leest u hoe u [aanmeldingsproblemen][troubleshoot-sign-in] met uw account of problemen met [de vergrendeling van uw account][troubleshoot-account-lockout]oplossen.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-security-audits]: security-audit-events.md
[password-policy]: password-policy.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-monitor-queries]: ../azure-monitor/log-query/query-language.md
[kusto-queries]: https://docs.microsoft.com/azure/kusto/query/tutorial?pivots=azuredataexplorer
