---
title: Een back-up maken van een SharePoint-farm naar Azure met DPM
description: In dit artikel vindt u een overzicht van dpm/azure-back-serverbeveiliging van een SharePoint-farm naar Azure
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 0199495e3b0eb002e58c096ed9abf05d46f43f97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054113"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-dpm"></a>Een back-up maken van een SharePoint-farm naar Azure met DPM

U maakt een back-up van een SharePoint-farm naar Microsoft Azure door System Center Data Protection Manager (DPM) te gebruiken op vrijwel dezelfde manier als u een back-up maakt van andere gegevensbronnen. Azure Backup biedt flexibiliteit in het back-upschema om dagelijkse, wekelijkse, maandelijkse of jaarlijkse back-uppunten te maken en biedt u bewaarbeleidsopties voor verschillende back-uppunten. DPM biedt de mogelijkheid om lokale schijfkopieën op te slaan voor snelle hersteltijddoelstellingen (RTO) en kopieën op te slaan naar Azure voor economische langetermijnretentie.

Een back-up van SharePoint naar Azure met DPM is een zeer vergelijkbaar proces om lokaal een back-up te maken van SharePoint naar DPM. In dit artikel worden specifieke overwegingen voor Azure vermeld.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Door SharePoint ondersteunde versies en gerelateerde beveiligingsscenario's

Zie [Waarvan kunt u een back-up maken met DPM?](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019#applications-backup) voor een lijst met ondersteunde SharePoint-versies en de vereiste DPM-versies voor het maken van een back-up.

## <a name="before-you-start"></a>Voordat u begint

Er zijn een paar dingen die u moet bevestigen voordat u een back-up maakt van een SharePoint-farm naar Azure.

### <a name="prerequisites"></a>Vereisten

Voordat u verder gaat, moet u ervoor zorgen dat u aan alle [vereisten voldoet voor het gebruik van Microsoft Azure Backup](backup-azure-dpm-introduction.md#prerequisites-and-limitations) om workloads te beschermen. Enkele taken voor vereisten zijn: maak een back-upkluis, download vault credentials, installeer Azure Backup Agent en registreer DPM/Azure Backup Server met de kluis.

Aanvullende vereisten en beperkingen zijn te vinden in het artikel [Back-up van SharePoint met DPM.](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#prerequisites-and-limitations)

## <a name="configure-backup"></a>Back-up configureren

Als u een back-up wilt maken van een SharePoint-farm, configureert u de beveiliging voor SharePoint met ConfigureSharePoint.exe en maakt u vervolgens een beveiligingsgroep in DPM. Zie [Back-up configureren](https://docs.microsoft.com//system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#configure-backup) in de DPM-documentatie voor instructies.

## <a name="monitoring"></a>Bewaking

Als u de back-uptaak wilt controleren, volgt u de instructies in [Het bewaken van DPM-back-up](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#monitoring)

## <a name="restore-sharepoint-data"></a>SharePoint-gegevens terugzetten

Zie [SharePoint-gegevens](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#restore-sharepoint-data)herstellen voor meer informatie over het herstellen van een SharePoint-item vanaf een schijf met DPM.

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Een SharePoint-database herstellen vanuit Azure met Behulp van DPM

1. Als u een SharePoint-inhoudsdatabase wilt herstellen, bladert u door verschillende herstelpunten (zoals eerder is weergegeven) en selecteert u het herstelpunt dat u wilt herstellen.

    ![DPM SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Dubbelklik op het SharePoint-herstelpunt om de beschikbare SharePoint-catalogusgegevens weer te geven.

   > [!NOTE]
   > Omdat de SharePoint-farm is beveiligd voor langdurige bewaring in Azure, zijn er geen catalogusgegevens (metagegevens) beschikbaar op de DPM-server. Als gevolg hiervan moet u de SharePoint-farm opnieuw catalogiseren wanneer een point-in-time SharePoint-inhoudsdatabase moet worden hersteld.
   >
   >
3. Klik **op Opnieuw catalogus**.

    ![DPM SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Het statusvenster **Voor de catalogus van de cloud** wordt geopend.

    ![DPM SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Nadat het catalogiseren is voltooid, verandert de status in *Succes*. Klik op **Sluiten**.

    ![DPM SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Klik op het SharePoint-object dat wordt weergegeven op het tabblad **DPM-herstel** om de structuur van de inhoudsdatabase op te halen. Klik met de rechtermuisknop op het item en klik vervolgens op **Herstellen**.

    ![DPM SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Volg op dit moment de herstelstappen eerder in dit artikel om een SharePoint-inhoudsdatabase van schijf te herstellen.

## <a name="switching-the-front-end-web-server"></a>De front-end webserver schakelen

Als u meer dan één front-end webserver hebt en de server wilt schakelen die DPM gebruikt om de farm te beschermen, volgt u de instructies in [Switching the Front-End Web Server](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#switching-the-front-end-web-server).

## <a name="next-steps"></a>Volgende stappen

* [Azure Backup Server en DPM - Veelgestelde vragen](backup-azure-dpm-azure-server-faq.md)
* [Problemen oplossen met System Center Data Protection Manager](backup-azure-scdpm-troubleshooting.md)
