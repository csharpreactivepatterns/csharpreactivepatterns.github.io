---
layout: page
title: Messaging Channels
permalink: /messaging-channels/
categories: akka messaging channel .net csharp
---
<a name="Sections">Sections</a>
<ul>
    <li>
      <a href="#PointPoint">Point-to-Point Channel</a>
    </li>
    <li>    
      <a href="#PublishSubscribe">Publish-Subscribe Channel</a>
    </li>
    <li>
      <a href="#Datatype">Datatype Channel</a>
    </li>
    <li>
      <a href="#InvalidMessage">Invalid Message Channel</a>
    </li>
    <li>
      <a href="#DeadLetter">Dead Letter Channel</a>
    </li>
    <li>
      <a href="#ChannelAdapter">Channel Adapter</a>
    </li>
    <li>
      <a href="#MessageBridge">Message Bridge</a>
    </li>
    <li>
      <a href="#MessageBus">Message Bus</a>
    </li>
</ul>

<h2 class="page-heading"><a name="Datatype">Datatype Channel</a></h2>
{% highlight csharp %}
class Program
{
    static void Main(string[] args)
    {
        using (var system = ActorSystem.Create("system"))
        {
            var productQueriesChannel = 
                system.ActorOf<ProductQueriesChannel>("productQueriesChannel");
            productQueriesChannel.Tell(new ProductQuery());
            productQueriesChannel.Tell(Encoding.UTF8.GetBytes("test query"));
        }
        Console.ReadLine();
    }
}
public class ProductQueriesChannel : ReceiveActor
{
    public ProductQueriesChannel()
    {
        Receive<ProductQuery>(x =>
        {
            Console.WriteLine($"ProductQueriesChannel: ProductQuery received");
        });
    }
}
{% endhighlight %}
<a href="https://github.com/berczeck/csharpreactivepatterns/blob/master/MessagingChannels/Datatype%20Channel/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>

<h2 class="page-heading"><a name="InvalidMessage">Invalid Message Channel</a></h2>
{% highlight csharp %}
class Program
{
    static void Main(string[] args)
    {
        using (var system = ActorSystem.Create("system"))
        {
            var invalidMessageChannel = 
                system.ActorOf<InvalidMessageChannel>("invalidMessageChannel");
            var nextFilter = system.ActorOf<NextFilter>("nextFilter");
            var authenticator =
                system.ActorOf(Props.Create(() => 
                    new Authenticator(nextFilter, invalidMessageChannel)), "authenticator");
            authenticator.Tell("Invalid message");
        }
        Console.ReadLine();
    }
}
public class InvalidMessageChannel : ReceiveActor
{
    public InvalidMessageChannel()
    {
        Receive<InvalidMessage>(x => 
            Console.WriteLine("InvalidMessageChannel: InvalidMessage received, message") );
    }
}
public class Authenticator : ReceiveActor
{
    public Authenticator(IActorRef nextFilter, IActorRef invalidMessageChannel)
    {
        Receive<ProcessIncomingOrder>(x =>
        {
            var text = Encoding.Default.GetString(x.Message);
            Console.WriteLine($"Decrypter: processing {text}");
            var orderText = text.Replace("(encryption)", string.Empty);
            nextFilter.Tell(new ProcessIncomingOrder (Encoding.Default.GetBytes(orderText)));
        });
        ReceiveAny(x => invalidMessageChannel.Tell(new InvalidMessage (x)));
    }
}
{% endhighlight %}
<a href="https://github.com/berczeck/csharpreactivepatterns/blob/master/MessagingChannels/Invalid%20Message%20Channel/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>

<h2 class="page-heading"><a name="DeadLetter">Dead Letter Channel</a></h2>
{% highlight csharp %}
class Program
{
    static void Main(string[] args)
    {
        using (var system = ActorSystem.Create("system"))
        {
            var sysListener = system.ActorOf<SysListener>("sysListener");
            system.EventStream.Subscribe(sysListener, typeof(DeadLetter));
            var deadActor = system.ActorOf<DeadActor>("deadActor");
            deadActor.Tell(PoisonPill.Instance);
            deadActor.Tell("Message to dead actor");
        }
        Console.ReadLine();
    }
}
public class DeadActor : ReceiveActor
{
}
public class SysListener : ReceiveActor
{
    public SysListener()
    {
        Receive<DeadLetter>(x => 
            Console.WriteLine($"SysListner, DeadLetter received: {x}"));
    }   
}
{% endhighlight %}
<a href="https://github.com/berczeck/csharpreactivepatterns/blob/master/MessagingChannels/Dead%20Letter%20Channel/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>

<h2 class="page-heading"><a name="ChannelAdapter">Channel Adapter</a></h2>
{% highlight csharp %}
class Program
{
    static void Main(string[] args)
    {
        using (var system = ActorSystem.Create("system"))
        {
            var tradingBus = system.ActorOf<TradingBus>("tradingBus");
            var stockTrader =
                system.ActorOf(Props.Create(() => 
                new StockTrader(tradingBus,new BuyerService(), 
                    new SellerService())), "stockTrader");
            stockTrader.Tell(new ExecuteBuyOrder());
            stockTrader.Tell(new ExecuteSellOrder());
        }
        Console.ReadLine();
    }
}
public class StockTrader : ReceiveActor
{
    public StockTrader(IActorRef tradingBus, 
        BuyerService buyerService, SellerService sellerService)
    {
        Receive<ExecuteBuyOrder>(x =>
        {
            var result = buyerService.PlaceBuyOrder();
            tradingBus.Tell(new TradingNotification ("BuyOrderExecuted", result));
        });
        Receive<ExecuteSellOrder>(x =>
        {
            var result = buyerService.PlaceBuyOrder();
            tradingBus.Tell(new TradingNotification ("SellOrderExecuted", result));
        });

        tradingBus.Tell(new RegisterCommandHandler());
        tradingBus.Tell(new RegisterCommandHandler());
    }
}
public class TradingBus : ReceiveActor
{
    public TradingBus()
    {
        Receive<TradingNotification>(x => 
            Console.WriteLine($"TradingBus: received {x.Type}"));
        ReceiveAny(x => Console.WriteLine($"TradingBus: received {x}"));
    }
}
{% endhighlight %}
<a href="https://github.com/berczeck/csharpreactivepatterns/blob/master/MessagingChannels/Channel%20Adapter/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>

<h2 class="page-heading"><a name="MessageBridge">Message Bridge</a></h2>
{% highlight csharp %}
class Program
{
    static void Main(string[] args)
    {
        using (var system = ActorSystem.Create("system"))
        {
            var inventoryProductAllocationBridge =
                system.ActorOf<InventoryProductAllocationBridge>("inventoryProductAllocationBridge");
            inventoryProductAllocationBridge.Tell(new RabbitMQTextMessage("Rabbit test message"));
        }
        ReadLine();
    }
}
public class InventoryProductAllocationBridge : ReceiveActor
{
    public InventoryProductAllocationBridge()
    {
        Receive<RabbitMQTextMessage>(x =>
        {
            WriteLine($"InventoryProductAllocationBridge: received {x.Message}");
            var inventoryProductAllocation = TranslatedToInventoryProductAlloction(x.Message);
            WriteLine($"InventoryProductAllocationBridge: translated {inventoryProductAllocation}");
            AcknowledgeDelivery(x);
        });
    }
    private void AcknowledgeDelivery(RabbitMQTextMessage textMessage) => WriteLine($"InventoryProductAllocationBridge: acknowledged {textMessage.Message}");
    private string TranslatedToInventoryProductAlloction(string message) => $"Inventory product alloction for {message}";
}
{% endhighlight %}
<a href="https://github.com/berczeck/csharpreactivepatterns/blob/master/MessagingChannels/Message%20Bridge/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>

<h2 class="page-heading"><a name="MessageBus">Message Bus</a></h2>
{% highlight csharp %}
class Program
{
    static void Main(string[] args)
    {
        using (var system = ActorSystem.Create("system"))
        {
            var tradingBus = system.ActorOf<TradingBus>("tradingBus");
            var marketAnalysisTools = system.ActorOf(Props.Create(() => 
                new MarketAnalysisTools(tradingBus)),
                "marketAnalysisTools");
            var portfolioManager = system.ActorOf(Props.Create(() => 
                new PortfolioManager(tradingBus)),
                "portfolioManager");
            var stockTrader = system.ActorOf(Props.Create(() => 
                new StockTrader(tradingBus)), "stockTrader");

            var t1 = marketAnalysisTools.Ask(new Start());
            var t2 = portfolioManager.Ask(new Start());
            var t3 = stockTrader.Ask(new Start());

            Task.WaitAll(t1, t2, t3);
            
            tradingBus.Tell(new Status());
            
            tradingBus.Tell(new TradingCommand ("ExecuteBuyOrder", new ExecuteBuyOrder()));
            tradingBus.Tell(new TradingCommand ("ExecuteSellOrder" , new ExecuteSellOrder()));
            tradingBus.Tell(new TradingCommand ("ExecuteBuyOrder", new ExecuteBuyOrder()));
        }
        Console.ReadLine();
    }
}

public class TradingBus : ReceiveActor
{
    private readonly IList<RegisterCommandHandler> _commandHandlers = 
        new List<RegisterCommandHandler>();
    private readonly IList<RegisterNotificationInterest> _notificationInterests = 
        new List<RegisterNotificationInterest>();

    public TradingBus()
    {
        Receive<RegisterCommandHandler>(x =>_commandHandlers.Add(x));

        Receive<RegisterNotificationInterest>(x => _notificationInterests.Add(x));

        Receive<TradingCommand>(x => 
        _commandHandlers.Where(c => c.CommandId == x.CommandId)
        .ForEach(c => c.Handler.Tell(x.Command)));

        Receive<TradingNotification>(x => 
        _notificationInterests.Where(c => c.NotificationId == x.NotificationId)
        .ForEach(c => c.Interested.Tell(x.Notification)));

        Receive<Status>(x =>
        {
            Console.WriteLine("TradingBus: STATUS:");
            _commandHandlers.ForEach(c => 
                Console.WriteLine($"{c.CommandId} {c.Handler}"));
            Console.WriteLine("TradingBus: STATUS:");
            _notificationInterests.ForEach(c => 
                Console.WriteLine($"{c.NotificationId} {c.Interested}"));
        });
    }
}

public class StockTrader : ReceiveActor
{
    public StockTrader(IActorRef tradingBus)
    {
        Receive<ExecuteBuyOrder>(x =>
        {
            Console.WriteLine($"StockTrader: buying for: {x}");
            tradingBus.Tell(new TradingNotification 
                ("BuyOrderExecuted", new BuyOrderExecuted()));
        });
        Receive<ExecuteSellOrder>(x =>
        {
            Console.WriteLine($"StockTrader: selling for: {x}");
            tradingBus.Tell(new TradingNotification 
                ("SellOrderExecuted", new SellOrderExecuted()));
        });
        Receive<Start>(x =>
        {
            tradingBus.Tell(new RegisterCommandHandler ("ExecuteBuyOrder", Self));
            tradingBus.Tell(new RegisterCommandHandler ("ExecuteSellOrder", Self));
            Sender.Tell("ok");
        });
    }
}

public class PortfolioManager : ReceiveActor
{
    public PortfolioManager(IActorRef tradingBus)
    {

        Receive<BuyOrderExecuted>(x => 
            Console.WriteLine($"PortfolioManager: adding holding: {x}"));
        Receive<SellOrderExecuted>(x => 
            Console.WriteLine($"PortfolioManager: adjusting holding: {x}"));
        Receive<Start>(x =>
        {
            tradingBus.Tell(new RegisterNotificationInterest("BuyOrderExecuted",Self));
            tradingBus.Tell(new RegisterNotificationInterest("SellOrderExecuted", Self));
            Sender.Tell("ok");
        });
    }
}

public class MarketAnalysisTools : ReceiveActor
{
    public MarketAnalysisTools(IActorRef tradingBus)
    {
        Receive<BuyOrderExecuted>(x => 
            Console.WriteLine($"MarketAnalysisTools: adding holding: {x}"));
        Receive<SellOrderExecuted>(x => 
            Console.WriteLine($"MarketAnalysisTools: adjusting holding: {x}"));
        Receive<Start>(x =>
        {
            tradingBus.Tell(new RegisterNotificationInterest("BuyOrderExecuted",Self));
            tradingBus.Tell(new RegisterNotificationInterest("SellOrderExecuted", Self));
            Sender.Tell("ok");
        });
    }
}
{% endhighlight %}
<a href="https://github.com/berczeck/csharpreactivepatterns/blob/master/MessagingChannels/Message%20Bus/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>
