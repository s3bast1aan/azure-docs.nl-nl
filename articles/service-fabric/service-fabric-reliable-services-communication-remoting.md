---
title: Service voor externe toegang met behulp van C# in Service Fabric | Microsoft Docs
description: Externe communicatie met Service Fabric kan clients en services om te communiceren met C#-services met behulp van een externe procedureaanroep.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: e7652fe1b211e6811a4a3aa61bc2aa9d2f529dda
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205813"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Service voor externe toegang in C# met betrouwbare Services
> [!div class="op_single_selector"]
> * [C# op Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java op Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Voor services die niet zijn gekoppeld aan een bepaalde communicatieprotocol of stack, zoals WebAPI, Windows Communication Foundation (WCF) of andere resources, biedt het framework van Reliable Services een mechanisme voor externe toegang snel en eenvoudig instellen van externe procedureaanroepen voor Services. In dit artikel wordt beschreven hoe het instellen van externe procedureaanroepen voor services die zijn geschreven met C#.

## <a name="set-up-remoting-on-a-service"></a>Instellen van een Service voor externe toegang
Instellen van externe toegang tot een service wordt uitgevoerd in twee eenvoudige stappen:

1. Maak een interface voor uw service te implementeren. Deze interface definieert de methoden die beschikbaar voor een externe procedureaanroep op uw service zijn. De methoden retourneren van de taak moeten zijn asynchrone methoden. De interface moet implementeren `Microsoft.ServiceFabric.Services.Remoting.IService` om aan te geven dat de service een externe communicatie-interface heeft.
2. Gebruik een listener voor externe toegang in uw service. RemotingListener is een `ICommunicationListener` mogelijkheden voor externe toegang biedt-implementatie. De `Microsoft.ServiceFabric.Services.Remoting.Runtime` naamruimte bevat een uitbreidingsmethode`CreateServiceRemotingListener` voor staatloze en stateful services die kunnen worden gebruikt om te maken van een externe listener met behulp van het standaardprotocol voor het transport van externe toegang.

>[!NOTE]
>De `Remoting` naamruimte is beschikbaar als een afzonderlijke NuGet-pakket met de naam `Microsoft.ServiceFabric.Services.Remoting`

Bijvoorbeeld, een één methode voor het "Hallo wereld" ophalen via een externe procedureaanroep wordt aangegeven dat de volgende stateless service.

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task<string> HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
     return this.CreateServiceRemotingInstanceListeners();
    }
}
```
> [!NOTE]
> De argumenten en de typen retourneren die in de service-interface kunnen een eenvoudige, complexe of aangepaste typen, maar ze moeten worden geserialiseerd met de .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).
>
>

## <a name="call-remote-service-methods"></a>Externe servicemethoden aanroepen
Methoden voor het aanroepen van een service met behulp van de stack voor externe toegang wordt uitgevoerd met behulp van een lokale proxyserver naar de service via de `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` klasse. De `ServiceProxy` methode maakt u een lokale proxyserver met behulp van dezelfde interface die de service implementeert. Met deze proxy, kunt u methoden op afstand op de interface aanroepen.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

Het framework voor externe toegang geeft uitzonderingen die door de service naar de client. Als gevolg hiervan, bij het gebruik van `ServiceProxy`, de client is ervoor verantwoordelijk voor het verwerken van de uitzonderingen die door de service.

## <a name="service-proxy-lifetime"></a>De levensduur van de service-Proxy
Het maken van ServiceProxy is een lichte bewerking, zodat u zoveel behoefte kunt maken. Proxy-service-exemplaren kunnen worden hergebruikt, zolang ze wel nodig zijn. Als een externe procedureaanroep is een uitzondering genereert, kunt u nog steeds hetzelfde exemplaar van de proxy opnieuw gebruiken. Elke ServiceProxy bevat een communicatie-client gebruikt voor het verzenden van berichten via de kabel. Tijdens het aanroepen van externe oproepen, interne controles uitgevoerd om te bepalen of de client communicatie geldig is. Op basis van de resultaten van deze controles, de client communicatie wordt opnieuw gemaakt indien nodig. Dus als er een uitzondering optreedt, u hoeft niet opnieuw maken `ServiceProxy`.

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory Lifetime
[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) is een factory die de proxy worden exemplaren voor externe communicatie van andere interfaces gemaakt. Als u de api `ServiceProxy.Create` voor het maken van proxy, het framework maakt u een singleton-ServiceProxy.
Het is handig om een handmatig maken wanneer u nodig hebt voor de onderdrukking [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory) eigenschappen.
Maken van de factory is een dure bewerking. ServiceProxyFactory onderhoudt een interne cache van client voor communicatie.
Aanbevolen procedure is ServiceProxyFactory zo lang mogelijk in de cache.

## <a name="remoting-exception-handling"></a>Afhandeling van uitzonderingen voor externe toegang
Alle externe uitzonderingen die door de API-service worden verzonden naar de client als AggregateException. RemoteExceptions moet DataContract serialiseerbare; Als dat niet het geval is, de API-proxy genereert [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) met de serialisatiefout erin.

ServiceProxy verwerkt alle failover-uitzonderingen voor de servicepartitie die deze wordt gemaakt. Deze oplossing voor de eindpunten weer als er failover-uitzonderingen (niet-tijdelijke uitzonderingen) zijn en probeert om opnieuw de aanroep met het juiste eindpunt. Het aantal nieuwe pogingen voor failover-uitzonderingen worden onbepaalde.
Als er tijdelijke uitzonderingen optreden, probeert de proxy opnieuw de aanroep.

Standaardparameters voor nieuwe pogingen worden geleverd door [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

Gebruiker kan deze waarden configureren doordat OperationRetrySettings object doorgegeven aan de constructor ServiceProxyFactory.

## <a name="how-to-use-the-remoting-v2-stack"></a>Het gebruik van de stack Remoting V2

Vanaf versie 2.8 van de externe communicatie van NuGet-pakket hebt u de optie voor het gebruik van de stapel Remoting V2. De Remoting V2-stack beter is en biedt functies, zoals aangepaste serialisatie en meer pluggable API's.
Sjablooncode blijft de V1-Stack van externe toegang gebruiken.
Remoting V2 is niet compatibel met V1 (de vorige voor externe toegang-stack), dus Volg de onderstaande instructies op [over het upgraden van V1 in V2](#how-to-upgrade-from-remoting-v1-to-remoting-v2) zonder enige impact op de beschikbaarheid van de service.

De volgende methoden zijn beschikbaar voor het inschakelen van de V2-stack.

### <a name="using-an-assembly-attribute-to-use-the-v2-stack"></a>Gebruik een assembly-kenmerk van de stack V2

Deze stappen sjablooncode voor het gebruik van de V2-Stack met behulp van een kenmerk assembly te wijzigen.

1. Wijzigen van de Resource-eindpunt in `"ServiceEndpoint"` naar `"ServiceEndpointV2"` in het servicemanifest.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
  </Resources>
  ```

2. Gebruik de `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners` uitbreidingsmethode maken voor externe toegang-listeners (equal voor zowel V1 als V2).

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3. Markeren van de assembly met de interfaces voor externe toegang met een `FabricTransportServiceRemotingProvider` kenmerk.

  ```csharp
  [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
  ```

Er zijn geen codewijzigingen vereist zijn in het clientproject.
Bouw de client-assembly met de interface-assembly om ervoor te zorgen dat de assembly-kenmerk dat wordt weergegeven boven wordt gebruikt.

### <a name="using-explicit-v2-classes-to-use-the-v2-stack"></a>Gebruik van expliciete V2-klassen de stack V2

Als alternatief voor het gebruik van een assembly-kenmerk kan de V2-stack ook worden ingeschakeld met behulp van expliciete V2-klassen.

Deze stappen sjablooncode voor het gebruik van de V2-Stack met behulp van expliciete V2-klassen te wijzigen.

1. Wijzigen van de Resource-eindpunt in `"ServiceEndpoint"` naar `"ServiceEndpointV2"` in het servicemanifest.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
  </Resources>
  ```

2. Gebruik de [FabricTransportServiceRemotingListener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet) uit de `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime` naamruimte.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                return new FabricTransportServiceRemotingListener(c, this);

            })
        };
    }
  ```

3. Gebruik de [FabricTransportServiceRemotingClientFactory ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) uit de `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` naamruimte voor het maken van clients.

  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2"></a>Klik hier voor meer informatie over het upgraden van externe toegang V1 naar Remoting V2.
2-stap upgrades zijn om bij te werken van V1 in V2, vereist. Volgende stappen uit om te worden uitgevoerd in de reeks die worden vermeld.

1. De V1-Service upgraden naar V2-Service met behulp van onderstaande kenmerk.
Deze wijziging zorgt ervoor dat service op V1 en V2-Listener luistert.

    (a) een Resource-eindpunt met de naam toevoegen als 'ServiceEndpointV2' in het servicemanifest.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    (b) Gebruik de volgende methode van de extensie voor het maken van externe toegang-Listener.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c) kenmerk Assembly toevoegen op externe toegang-Interfaces voor het gebruik van V1 en V2-Listener en V2-Client.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. De V1-Client upgraden naar V2-Client met behulp van V2-Client-kenmerk.
Deze stap zorgt ervoor dat Client gebruikmaakt van V2-stack.
Er is geen wijziging in Client-Project/Service vereist. Client-projecten bouwen met bijgewerkte interface assembly is voldoende.

3. Deze stap is optioneel. Het kenmerk V2Listener gebruiken en werk vervolgens de V2-Service.
Deze stap zorgt ervoor dat service alleen op V2-Listener luistert.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
```


## <a name="how-to-use-remoting-v2interfacecompatible-stack"></a>Het gebruik van externe toegang V2(InterfaceCompatible) stack
 Remoting V2 (ook wel V2_1 InterfaceCompatible)-stack heeft alle functies van Remoting V2-stack van het feit dat het compatibel stack interface naar externe communicatie van V1-stack is maar niet compatibel met V2 en V1 is. Als u wilt u de upgrade van V1 naar V2_1 zonder enige impact op de beschikbaarheid van de service, volg de onderstaande artikel[bijwerken van V1 naar V2(InterfaceCompatible)](#how-to-upgrade-from-remoting-v1-to-remoting-v2interfacecompatible).


### <a name="using-assembly-attribute-to-use-remoting-v2interfacecompatible-stack"></a>Met behulp van Assembly-kenmerk voor externe toegang V2(InterfaceCompatible) stack gebruiken.

Hier volgen de stappen te volgen als u wilt wijzigen naar V2_1 Stack.

1. Een Resource-eindpunt met de naam toevoegen als 'ServiceEndpointV2_1' in het servicemanifest.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
  </Resources>
  ```

2.  Gebruik externe communicatie uitbreidingsmethode voor externe toegang-Listener maken.

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3.  Voeg [kenmerk Assembly](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet) op Interfaces voor externe toegang.

  ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

  ```
Er zijn geen wijzigingen vereist zijn in Client-Project.
Bouw de Client-assembly met de interface assembly, naar zorgt ervoor dat hierboven assembly kenmerk wordt gebruikt.

### <a name="using-explicit-remoting-classes-to-create-listener-clientfactory-for-v2interfacecompatible-version"></a>Listener maken met behulp van expliciete Remoting klassen / ClientFactory voor V2(InterfaceCompatible) versie.
Hier volgen de stappen te volgen.
1.  Een Resource-eindpunt met de naam toevoegen als 'ServiceEndpointV2_1' in het servicemanifest.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
  </Resources>
  ```

2. Gebruik [Remoting V2Listener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet). Bron van de Service-eindpunt uit de standaardnaam die wordt gebruikt "ServiceEndpointV2_1" is en moet worden gedefinieerd in het servicemanifest.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                var settings = new FabricTransportRemotingListenerSettings();
                settings.UseWrappedMessage = true;
                return new FabricTransportServiceRemotingListener(c, this,settings);

            })
        };
    }
  ```

3. Gebruik van V2 [Clientfabriek](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet).
  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2interfacecompatible"></a>Over het upgraden van externe toegang V1 naar V2(InterfaceCompatible) voor externe toegang.
Om bij te werken van V1 in V2 (InterfaceCompatible wel V2_1), 2-stap upgrades zijn vereist. Volgende stappen uit om te worden uitgevoerd in de reeks die worden vermeld.

1. De V1-Service upgraden naar V2_1 Service met behulp van kenmerk te volgen.
Deze wijziging zorgt ervoor dat service op de V1- en V2_1 Listener luistert.

    (a) een Resource-eindpunt met de naam toevoegen als 'ServiceEndpointV2_1' in het servicemanifest.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    (b) Gebruik de volgende methode van de extensie voor het maken van externe toegang-Listener.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c) Assembly-kenmerk op Interfaces voor externe communicatie met V1, V2_1 Listener en V2_1 Client toevoegen.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. De V1-Client naar V2_1 Client upgraden met behulp van V2_1 Client kenmerk.
Deze stap zorgt ervoor dat Client gebruikmaakt van V2_1 stack.
Er is geen wijziging in Client-Project/Service vereist. Client-projecten bouwen met bijgewerkte interface assembly is voldoende.

3. Deze stap is optioneel. V1-Listener versie verwijderen uit kenmerk en werk vervolgens de V2-Service.
Deze stap zorgt ervoor dat service alleen op V2-Listener luistert.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
```

### <a name="how-to-use-custom-serialization-with-remoting-wrapped-message"></a>Het gebruik van aangepaste serialisatie met externe communicatie verpakt bericht.
In externe toegang verpakt bericht maken we één verpakte object met alle parameters als een veld in het.
Hier volgen de stappen uit:

1. Implementeer IServiceRemotingMessageSerializationProvider-interface voor implementatie voor aangepaste serialisatie.
    Hier volgt het codefragment op hoe implementatie ziet.

      ```csharp
      public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
      {
        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
          return new JsonMessageFactory();
        }

        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> requestWrappedType, IEnumerable<Type> requestBodyTypes = null)
        {
          return new ServiceRemotingRequestJsonMessageBodySerializer();
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> responseWrappedType, IEnumerable<Type> responseBodyTypes = null)
        {
          return new ServiceRemotingResponseJsonMessageBodySerializer();
        }
      }
      ```
      ```csharp
        class JsonMessageFactory : IServiceRemotingMessageBodyFactory
            {

              public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName, int numberOfParameters, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }

              public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }
            }
      ```
      ```csharp
      class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
        {
            private JsonSerializer serializer;

            public ServiceRemotingRequestJsonMessageBodySerializer()
            {
              serializer = JsonSerializer.Create(new JsonSerializerSettings()
              {
                TypeNameHandling = TypeNameHandling.All
                });
              }

              public IOutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
             {
               if (serviceRemotingRequestMessageBody == null)
               {
                 return null;
               }          
               using (var writeStream = new MemoryStream())
               {
                 using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
                 {
                   serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);
                   jsonWriter.Flush();
                   var bytes = writeStream.ToArray();
                   var segment = new ArraySegment<byte>(bytes);
                   var segments = new List<ArraySegment<byte>> { segment };
                   return new OutgoingMessageBody(segments);
                 }
               }
              }

              public IServiceRemotingRequestMessageBody Deserialize(IIncomingMessageBody messageBody)
             {
               using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
               {
                 using (JsonReader reader = new JsonTextReader(sr))
                 {
                   var ob = serializer.Deserialize<JsonBody>(reader);
                   return ob;
                 }
               }
             }
            }
      ```
      ```csharp
      class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
       {
         private JsonSerializer serializer;

        public ServiceRemotingResponseJsonMessageBodySerializer()
        {
          serializer = JsonSerializer.Create(new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
            });
          }

          public IOutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
          {
            if (responseMessageBody == null)
            {
              return null;
            }

            using (var writeStream = new MemoryStream())
            {
              using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
              {
                serializer.Serialize(jsonWriter, responseMessageBody);
                jsonWriter.Flush();
                var bytes = writeStream.ToArray();
                var segment = new ArraySegment<byte>(bytes);
                var segments = new List<ArraySegment<byte>> { segment };
                return new OutgoingMessageBody(segments);
              }
            }
          }

          public IServiceRemotingResponseMessageBody Deserialize(IIncomingMessageBody messageBody)
          {

             using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
             {
               using (var reader = new JsonTextReader(sr))
               {
                 var obj = serializer.Deserialize<JsonBody>(reader);
                 return obj;
               }
             }
           }
       }
    ```
    ```csharp
    class JsonBody : WrappedMessage, IServiceRemotingRequestMessageBody, IServiceRemotingResponseMessageBody
    {
          public JsonBody(object wrapped)
          {
            this.Value = wrapped;
          }

          public void SetParameter(int position, string parameName, object parameter)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object GetParameter(int position, string parameName, Type paramType)
          {
            //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public void Set(object response)
          { //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object Get(Type paramType)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }
    }
    ```

2.    Serialisatie standaardprovider met JsonSerializationProvider voor externe toegang-Listener overschrijven.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
   {
       return new[]
       {
           new ServiceInstanceListener((c) =>
           {
               return new FabricTransportServiceRemotingListener(c, this,
                   new ServiceRemotingJsonSerializationProvider());
           })
       };
   }
  ```

3.    Onderdrukking van de standaardprovider voor serialisatie met JsonSerializationProvider voor de Clientfabriek van externe toegang.

```csharp
var proxyFactory = new ServiceProxyFactory((c) =>
{
    return new FabricTransportServiceRemotingClientFactory(
    serializationProvider: new ServiceRemotingJsonSerializationProvider());
  });
  ```
## <a name="next-steps"></a>Volgende stappen
* [Web-API met OWIN in Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [WCF-communicatie via Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Beveiliging van de communicatie voor Reliable Services](service-fabric-reliable-services-secure-communication.md)
