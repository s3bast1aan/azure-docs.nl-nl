---
title: Web application firewall toevoegen in Azure Security Center | Microsoft Docs
description: Dit document ziet u hoe de Azure Security Center aanbevelingen implementeren **web application firewall toevoegen** en **Toepassingsbeveiliging voltooien**.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 8f56139a-4466-48ac-90fb-86d002cf8242
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/15/2018
ms.author: terrylan
ms.openlocfilehash: e28a1f6b865dae3abe2cb9dfac2921c6a2034491
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34203519"
---
# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Web application firewall toevoegen in Azure Security Center
Azure Security Center kan aanbevelen web application firewall (WAF) toe te voegen uit een Microsoft-partner voor het beveiligen van uw webtoepassingen. Dit document vindt u via een voorbeeld van het toepassen van deze aanbeveling.

Een WAF aanbeveling wordt voor een openbare internetgerichte IP (exemplaar niveau IP- of Load Balanced IP) met een gekoppelde netwerkbeveiligingsgroep met poorten voor inkomend web openen (80,443) weergegeven.

Security Center raadt het inrichten van een WAF om te helpen beschermen tegen aanvallen die gericht is op uw webtoepassingen op virtuele machines en op externe App Service-omgevingen (as-omgeving) geïmplementeerd onder de [geïsoleerd](https://azure.microsoft.com/pricing/details/app-service/windows/) service-abonnement. Het Isolated-abonnement host uw apps in een privé en speciale Azure-omgeving en is ideaal voor apps die beveiligde verbindingen in uw lokale netwerk of extra prestaties en schaalgrootte vereisen. Naast uw app wordt in een geïsoleerde omgeving, moet uw app in een extern IP-adres op de load balancer hebben. Zie voor meer informatie over het as-omgeving, de [documentatie van App Service-omgeving](../app-service/environment/intro.md).

> [!NOTE]
> In dit document wordt de service geïntroduceerd aan de hand van een voorbeeldimplementatie.  Dit document is niet een stapsgewijze handleiding.
>
>

## <a name="implement-the-recommendation"></a>De aanbeveling implementeren
1. Onder **aanbevelingen**, selecteer **-webtoepassing met web application firewall Secure**.
   ![Web Application beveiligen][1]
2. Onder **beveiligen van uw webtoepassingen met web application firewall**, selecteert u een webtoepassing. **Een Web Application Firewall toevoegen** wordt geopend.
   ![Een firewall voor webtoepassingen toevoegen][2]
3. U kunt kiezen om een bestaande web application firewall als beschikbaar of u kunt een nieuwe maken. In dit voorbeeld zijn er geen bestaande WAFs beschikbaar, dus we een WAF maken.
4. Selecteer voor het maken van een WAF, een oplossing uit de lijst met geïntegreerde partners. In dit voorbeeld selecteren we **Barracuda Web Application Firewall**.
5. **Barracuda Web Application Firewall** wordt geopend zodat u informatie over de partneroplossing. Selecteer **Maken**.

   ![Firewall-informatie-blade][3]

6. **Nieuwe Web Application Firewall** wordt geopend, waar u kunt uitvoeren **VM-configuratie** stappen en geef **WAF informatie**. Selecteer **VM-configuratie**.
7. Onder **VM-configuratie**, voert u gegevens die zijn vereist voor de virtuele machine die wordt uitgevoerd de WAF draaien.
   ![VM-configuratie][4]
8. Ga terug naar **nieuwe Web Application Firewall** en selecteer **WAF informatie**. Onder **WAF informatie**, u de WAF zelf configureren. Stap 7 kunt u voor het configureren van de virtuele machine waarop de WAF wordt uitgevoerd en stap 8 kunt u voor het inrichten van de WAF zelf.

## <a name="finalize-application-protection"></a>Toepassingsbeveiliging voltooien
1. Ga terug naar **aanbevelingen**. Een nieuwe vermelding is gegenereerd nadat u de WAF, aangeroepen gemaakt **Toepassingsbeveiliging voltooien**. Deze vermelding laat u weten dat u voltooien van het proces moet van het daadwerkelijk bekabeling van de WAF binnen het virtuele netwerk van Azure, zodat deze de toepassing kunt beveiligen.

   ![Toepassingsbeveiliging voltooien][5]

2. Selecteer **Toepassingsbeveiliging voltooien**. Er wordt een nieuwe blade geopend. U ziet dat er een webtoepassing die u moet beschikken over de verkeer gerouteerd.
3. Selecteer de webtoepassing. Er wordt een blade geopend waarmee u stappen voor het voltooien van de web application firewall-instellingen. Voer de stappen uit en selecteer vervolgens **verkeer te beperken**. Security Center doet vervolgens de bedrading-up voor u.

   ![Verkeer beperken][6]

> [!NOTE]
> U kunt meerdere webtoepassingen in Security Center beveiligen door deze toepassingen toevoegen aan uw bestaande WAF-implementaties.
>
>

De logboeken van die WAF zijn nu volledig geïntegreerd. Security Center kunt starten automatisch verzamelen en analyseren in de logboeken zodat dit kan belangrijk beveiligingswaarschuwingen surface voor u.

## <a name="next-steps"></a>Volgende stappen
Dit document hebt u geleerd hoe u implementeert de aanbeveling Security Center "Een webtoepassing toevoegen". Zie de volgende onderwerpen voor meer informatie over het configureren van een web application firewall:

* [Web Application Firewall (WAF) configureren voor App Service-omgeving](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)

Zie de volgende onderwerpen voor meer informatie over het Beveiligingscentrum:

* [Setting security policies in Azure Security Center](security-center-policies.md) (Beveiligingsbeleid instellen in Azure Security Center): leer hoe u beveiligingsbeleid voor uw Azure-abonnementen en -resourcegroepen configureert.
* [Beveiligingsstatus bewaken in Azure Security Center](security-center-monitoring.md) --informatie over het bewaken van de status van uw Azure-resources.
* [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md) (Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center): ontdek hoe u beveiligingswaarschuwingen kunt beheren en erop kunt reageren.
* [Aanbevelingen voor beveiliging in Azure Security Center beheren](security-center-recommendations.md) --Leer hoe aanbevelingen u uw Azure-resources te beveiligen.
* [Azure Security Center FAQ](security-center-faq.md) (Veelgestelde vragen over Azure Security Center): raadpleeg veelgestelde vragen over het gebruik van de service.
* [Azure-Beveiligingsblog](http://blogs.msdn.com/b/azuresecurity/) --Lees blogberichten over Azure-beveiliging en naleving.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
