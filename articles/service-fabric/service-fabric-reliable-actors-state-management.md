---
title: Reliable Actors status management | Microsoft Docs
description: Beschrijft hoe Reliable Actors status is beheerd, opgeslagen en gerepliceerd voor hoge beschikbaarheid.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 3cab4d87eacc7bce17da64cda213086c262179a8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206195"
---
# <a name="reliable-actors-state-management"></a>Statusbeheer voor betrouwbare Actors
Reliable Actors zijn single thread-objecten die zowel de logica en de status kunnen inkapselen. Omdat actoren op Reliable Services worden uitgevoerd, kunnen ze status betrouwbaar onderhouden met behulp van dezelfde persistentie en replicatiemechanismen. Op deze manier actoren niet hun status na fouten bij het opnieuw te activeren nadat garbagecollection of wanneer ze worden verplaatst tussen knooppunten in een cluster als gevolg van resource netwerktaakverdeling of upgrades verliezen.

## <a name="state-persistence-and-replication"></a>Status persistentie en replicatie
Alle Reliable Actors worden beschouwd als *stateful* omdat elk exemplaar actor wordt toegewezen aan een unieke ID. Dit betekent dat herhaalde aanroepen naar dezelfde actor-ID worden doorgestuurd naar hetzelfde actor-exemplaar. In een stateless systeem daarentegen zijn clientaanroepen niet gegarandeerd elke keer naar dezelfde server worden doorgestuurd. Actorservices zijn daarom altijd stateful services.

Hoewel actoren worden beschouwd als stateful die betekent niet dat ze betrouwbaar status moeten bevatten. Actoren kunnen het niveau van de status persistentie en replicatie op basis van hun gegevens opslagvereisten:

* **Status persistent**: status is opgeslagen op schijf en is gerepliceerd naar drie of meer replica's. Permanente status is de meest duurzame status opslagoptie waar status via het volledige cluster storing kunt behouden.
* **Vluchtige status**: status wordt gerepliceerd met drie of meer replica's en alleen in het geheugen worden bewaard. Vluchtige status biedt veerkracht tegen storingen van knooppunt en actor-fout en tijdens upgrades en resource netwerktaakverdeling. De status is echter niet persistent naar schijf. Dus als alle replica's verloren gaan in één keer, de status is verloren gegaan ook.
* **Er is geen permanente status**: status niet wordt gerepliceerd of geschreven naar de schijf voor actoren die niet hoeven te onderhouden, de status op betrouwbare wijze alleen worden gebruikt.

Verschillende niveaus van persistentie is gewoon een andere *state-provider* en *replicatie* configuratie van uw service. Wel of niet de status wordt geschreven naar schijf is afhankelijk van de state-provider--het onderdeel in een betrouwbare service die de status opslaat. Replicatie is afhankelijk van hoeveel replica's een service wordt geïmplementeerd met. Net als bij Reliable Services kunnen de state-provider- en het aantal replica's gemakkelijk handmatig wilt instellen. Het framework actor biedt een kenmerk dat, wanneer op een actor gebruikt, selecteert automatisch een standaard state-provider en genereert automatisch de instellingen voor het aantal replica's om te zorgen voor een van deze drie persistentie-instellingen. Het kenmerk StatePersistence wordt niet overgenomen door een afgeleide klasse, elk type Actor het niveau StatePersistence moet opgeven.

### <a name="persisted-state"></a>Permanente status
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl  extends FabricActor implements MyActor
{
}
```  
Deze instelling maakt gebruik van een state-provider die gegevens worden opgeslagen op schijf en het aantal replica automatisch ingesteld op 3.

### <a name="volatile-state"></a>Vluchtige status
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Volatile)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Deze instelling maakt gebruik van een in-memory-only state-provider en wordt het aantal replica's op 3.

### <a name="no-persisted-state"></a>Er is geen permanente status
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.None)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Deze instelling maakt gebruik van een in-memory-only state-provider en wordt het aantal replica's ingesteld op 1.

### <a name="defaults-and-generated-settings"></a>Standaardwaarden en gegenereerde instellingen
Wanneer u de `StatePersistence` kenmerk, een state-provider wordt automatisch geselecteerd voor tijdens runtime wanneer de actor-service wordt gestart. Het aantal replica's echter is ingesteld op het tijdstip van compilatie door Visual Studio actor build tools. Automatisch genereren van de build hulpprogramma's voor een *standaardservice* voor de service actor in ApplicationManifest.xml. Parameters worden gemaakt voor **grootte van de replicaset min** en **doelreplica grootte instellen**.

U kunt deze parameters handmatig wijzigen. Maar telkens wanneer de `StatePersistence` kenmerk wordt gewijzigd, worden de parameters zijn ingesteld op de replica set grootte standaardwaarden voor de geselecteerde `StatePersistence` kenmerk, een vorige waarden overschreven. Met andere woorden, de waarden die u in ServiceManifest.xml instelt zijn *alleen* genegeerd op build-tijd wanneer u wijzigt de `StatePersistence` kenmerkwaarde.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="3" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## <a name="state-manager"></a>Status manager
Elke actor-exemplaar heeft een eigen statusbeheer: een dictionary-achtige gegevensstructuur slaat veilig sleutel/waarde-paren. De status manager is een wrapper rond een state-provider. U kunt deze gebruiken voor het opslaan van gegevens, ongeacht welke persistentie instelling wordt gebruikt. Biedt geen garanties dat een actieve actor-service van een vluchtige (in-memory-only) Beleidsstatus kan worden gewijzigd in een permanente statusinstelling via een uitrollende upgrade behoud van gegevens. Het is echter mogelijk aantal replica's voor een actieve service wijzigen.

Status manager sleutels moeten tekenreeksen zijn. Waarden zijn algemeen en kunnen worden ingesteld, inclusief aangepaste typen. Waarden die zijn opgeslagen in de status manager moeten gegevenscontract serialiseerbaar omdat ze tijdens de replicatie via het netwerk naar andere knooppunten worden verzonden en kunnen worden geschreven naar de schijf, afhankelijk van een actor-statusinstelling persistentie.

De status manager beschrijft algemene woordenlijst methoden voor het beheren van status, vergelijkbaar met die in betrouwbare bibliotheek gevonden.

Lees voor voorbeelden van het beheer van de actorstatus [toegang, opslaan en het verwijderen van Reliable Actors status](service-fabric-reliable-actors-access-save-remove-state.md).

## <a name="best-practices"></a>Aanbevolen procedures
Hier volgen enkele aanbevolen procedures en tips voor probleemoplossing voor het beheren van de actorstatus.

### <a name="make-the-actor-state-as-granular-as-possible"></a>Als gedetailleerde mogelijk maken van de actorstatus
Dit is essentieel voor de prestaties en gebruik van bronnen van uw toepassing. Wanneer er schrijven/bijwerken met de 'benoemde status' van een acteur, wordt de hele waarde die overeenkomt met die 'benoemde status' geserialiseerd en verzonden via het netwerk naar de secundaire replica's.  De secundaire replica's schrijven naar de lokale schijf en de antwoord terug naar de primaire replica. Wanneer de primaire bevestigingen van een quorum van secundaire replica's ontvangt, worden de status naar de lokale schijf geschreven. Stel de waarde is een klasse die 20 leden en een grootte van 1 MB is. Zelfs als u een van de klasseleden van alleen gewijzigd formaat van 1 KB, einde van de kosten van serialisatie en netwerk- en schrijfbewerkingen betaalt voor de volledige 1 MB. Op dezelfde manier als de waarde is een verzameling (zoals een lijst, de matrix of de woordenlijst), betaalt u de kosten voor de volledige verzameling zelfs als u een van de leden wijzigen. De StateManager-interface van de klasse actor is vergelijkbaar met een woordenlijst. U moet altijd een model van de structuur van de gegevens die van de actorstatus boven op deze woordenlijst.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>De actor-levenscyclus correct beheren
U hebt wissen beleid over het beheren van de grootte van de status in elke partitie van een actor-service. Uw actor-service moet een vast aantal actoren hebben en een veel mogelijk blijven gebruiken. Als u nieuwe actoren continu maakt, moet u ze verwijderen als ze klaar bent met hun werk. Het framework actor slaat bepaalde metagegevens over elke actor dat zich voordoet. Als de status van een acteur, verwijdert metagegevens over die actor niet verwijderd. U moet de actor verwijderen (Zie [verwijderd actoren en hun status](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)) te verwijderen van alle informatie over deze opgeslagen in het systeem. Als een extra controle, moet u een query de actor-service (Zie [inventariseren actoren](service-fabric-reliable-actors-platform.md)) en om te controleren of de nummer actoren binnen het verwachte bereik.
 
Als u ooit ziet dat bestandsgrootte van de database van een Service Actor buiten de verwachte grootte toeneemt, ervoor zorgen dat u de voorgaande richtlijnen volgt. Als u deze richtlijnen volgen en nog steeds database problemen met de grootte van bestanden, moet u [een ondersteuningsticket opent](service-fabric-support.md) met het productteam om hulp te krijgen.

## <a name="next-steps"></a>Volgende stappen

Status die opgeslagen in Reliable Actors moet worden geserialiseerd voordat de is naar schijf geschreven en de gerepliceerde voor hoge beschikbaarheid. Meer informatie over [Actor type serialisatie](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Vervolgens meer wilt weten over [Actor diagnostische gegevens en prestatiebewaking](service-fabric-reliable-actors-diagnostics.md).
