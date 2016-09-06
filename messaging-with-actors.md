---
layout: page
title: Messaging with Actors
permalink: /messaging-with-actors/
categories: akka messaging actor .net csharp
---
<a name="Sections">Sections</a>
<ul>
    <li>
      <a href="#MessageChannel">Message Channel</a>
    </li>
    <li>    
      <a href="#Message">Message</a>
    </li>
    <li>
      <a href="#PipesFilters">Pipes and filters</a>
    </li>
    <li>
      <a href="#MessageRouter">Message Router</a>
    </li>
    <li>
      <a href="#MessageEndpoint">Message Endpoint</a>
    </li>
</ul>

<h2 class="page-heading"><a name="MessageChannel">Message Channel</a></h2>
{% highlight csharp %}
class Program
{
    static void Main(string[] args)
    {
        using (var system = ActorSystem.Create("system"))
        {
            var actor = system.ActorOf<Processor>("processor");
            actor.Tell(new ProcessorMessage {X = 10, Y = 20, Z = 30});
        }
        Console.ReadLine();
    }
}
public class Processor : ReceiveActor
{
    public Processor()
    {
        Receive<ProcessorMessage>(x => 
            Console.WriteLine($"Processor: received ProcessJob {x.X} {x.Y} {x.Z}"));
    }
}
public class ProcessorMessage
{
    public int  X { get; set; }
    public int Y { get; set; }
    public int Z { get; set; }
}
{% endhighlight %}
<a href="https://github.com/berczeck/csharpreactivepatterns/blob/master/MessagingWithActors/Message%20Channel/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>

<h2 class="page-heading"><a name="Message">Message</a></h2>
{% highlight csharp %}
class Program
{
    static void Main(string[] args)
    {
        using (var system = ActorSystem.Create("system"))
        {
            var primitiveActor = 
                system.ActorOf<ScalarValuePrinter>("scalarValuePrinter");
            primitiveActor.Tell(100);
            primitiveActor.Tell("Hello");
            var orderProcessor = 
                system.ActorOf<OrderProcessor>("orderProcessor");
            system.ActorOf(Props.Create(() => 
                new Caller(orderProcessor)),"caller");
        }
        Console.ReadLine();
    }
}
public class ScalarValuePrinter : ReceiveActor
{
    public ScalarValuePrinter()
    {
        Receive<string>(x => 
            Console.WriteLine($"ScalarValuePrinter: received String {x}"));
        Receive<int>(x => 
            Console.WriteLine($"ScalarValuePrinter: received Int32 {x}"));
    }
}
public class OrderProcessor : ReceiveActor
{
    public OrderProcessor()
    {
        Receive<ExecuteBuyOrder>(x => Sender.Tell(new BuyOrderExecuted()));
        Receive<ExecuteSellOrder>(x => Sender.Tell(new SellOrderExecuted()));
    }
}
public class Caller : ReceiveActor
{
    public Caller(IActorRef orderProcessor)
    {
        Receive<BuyOrderExecuted>(x => 
            Console.WriteLine("Caller: received BuyOrderExecuted"));
        Receive<SellOrderExecuted>(x => 
            Console.WriteLine("Caller: received SellOrderExecuted"));

        orderProcessor.Tell(new ExecuteBuyOrder());
        orderProcessor.Tell(new ExecuteSellOrder());
    }
}
{% endhighlight %}
<a href="https://github.com/berczeck/csharpreactivepatterns/blob/master/MessagingWithActors/Message/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>

<h2 class="page-heading"><a name="PipesFilters">Pipes and Filters</a></h2>
{% highlight csharp %}
class Program
{
    static void Main(string[] args)
    {
        var orderText = "(encryption)(certificate)<order id='123'>...</order>";
        var rawOrderBytes = Encoding.Default.GetBytes(orderText);
        using (var system = ActorSystem.Create("system"))
        {
            var filter5 = system.ActorOf<OrderManagerSystem>("orderManagerSystem");
            var filter4 = system.ActorOf(Props.Create(() => 
                new Deduplicator(filter5)), "deduplicator");
            var filter3 = system.ActorOf(Props.Create(() => 
                new Authenticator(filter4)), "authenticator");
            var filter2 = system.ActorOf(Props.Create(() => 
                new Decrypter(filter3)), "decrypter");
            var filter1 = system.ActorOf(Props.Create(() => 
                new OrderAcceptanceEndpoint(filter2)), "orderAcceptanceEndpoint");

            filter1.Tell(rawOrderBytes);
            filter1.Tell(rawOrderBytes);
        }
        Console.ReadLine();
    }
}
public class OrderManagerSystem : ReceiveActor
{
    public OrderManagerSystem()
    {
        Receive<ProcessIncomingOrder>(x =>
        {
            var text = Encoding.Default.GetString(x.Message);
            Console.WriteLine($"OrderManagmntSystem:processing unique order {text}");
        });
    }
}
public class Decrypter : ReceiveActor
{
    public Decrypter(IActorRef nextFilter)
    {
        Receive<ProcessIncomingOrder>(x =>
        {
            var text = Encoding.Default.GetString(x.Message);
            Console.WriteLine($"Decrypter: processing  {text}");
            var orderText = text.Replace("(encryption)", string.Empty);
            nextFilter.Tell(new ProcessIncomingOrder { 
                Message = Encoding.Default.GetBytes(orderText)});
        });
    }
}

public class Authenticator : ReceiveActor
{
    public Authenticator(IActorRef nextFilter)
    {
        Receive<ProcessIncomingOrder>(x =>
        {
            var text = Encoding.Default.GetString(x.Message);
            Console.WriteLine($"Authenticator: processing  {text}");
            var orderText = text.Replace("(certificate)", string.Empty);
            nextFilter.Tell(new ProcessIncomingOrder { 
                Message = Encoding.Default.GetBytes(orderText) });
        });
    }
}
{% endhighlight %}
<a href="https://github.com/berczeck/csharpreactivepatterns/blob/master/MessagingWithActors/Pipes%20and%20Filters/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>

<h2 class="page-heading"><a name="MessageRouter">Message Router</a></h2>
{% highlight csharp %}
class Program
{
    static void Main(string[] args)
    {
        using (var system = ActorSystem.Create("system"))
        {
            var processor1 = system.ActorOf<Processor>("processor1");
            var processor2 = system.ActorOf<Processor>("processor2");
            var alternatingRouter = system.ActorOf(
                Props.Create(() => 
                new AlternatingRouter(processor1, processor2)), "alternatingRouter");

            for (int i = 0; i < 10; i++)
            {
                alternatingRouter.Tell($"{i}");
            }
        }
        Console.ReadLine();
    }
}
public class AlternatingRouter : ReceiveActor
{
    private int _alternate = 1;
    private readonly IActorRef _processor1;
    private readonly IActorRef _processor2;

    public AlternatingRouter(IActorRef processor1, IActorRef processor2)
    {
        _processor1 = processor1;
        _processor2 = processor2;

        Receive<string>(x =>
        {
            var processor = AlternateProcessor();
            Console.WriteLine($"Routing {x} to {processor.Path.Name}");
            processor.Tell(x);
        });
    }
    private IActorRef AlternateProcessor()
    {
        if (_alternate == 1)
        {
            _alternate = 2;
            return _processor1;
        }
        _alternate = 1;
        return _processor2;
    }
}
public class Processor : ReceiveActor
{
    public Processor()
    {
        Receive<string>(x => 
        Console.WriteLine($"Processor: {Self.Path.Name} received {x}"));
    }
}
{% endhighlight %}
<a href="https://github.com/berczeck/csharpreactivepatterns/blob/master/MessagingWithActors/Message%20Router/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>

<h2 class="page-heading"><a name="MessageEndpoint">Message Endpoint</a></h2>
{% highlight csharp %}
class Program
{
    static void Main(string[] args)
    {
        using (var system = ActorSystem.Create("system"))
        {
            var discounter = system.ActorOf<Discounter>("discounter");
            var highSierraPriceQuotes = 
                system.ActorOf(Props.Create(() => 
                new HighSierraPriceQuotes(discounter)), "highSierraPriceQuotes");
            var requester = system.ActorOf(Props.Create(() => 
                new Requester(highSierraPriceQuotes)), "requester");
            Console.WriteLine(requester.Path);
        }
        Console.ReadLine();
    }
}
public class HighSierraPriceQuotes : ReceiveActor
{
    public HighSierraPriceQuotes(IActorRef discounter)
    {
        Receive<RequestPriceQuote>(x =>
        {
            Console.WriteLine("HighSierraPriceQuotes:RequestPriceQuote received");
            discounter.Tell(new CalculatedDiscountPriceFor());
        });

        Receive<DiscountPriceCalculated>(x =>
        {
            Console.WriteLine("HighSierraPriceQuotes:DiscountPriceCalculated received");
            x.Requester.Tell(new PriceQuote());
        });
    }
}
public class Discounter : ReceiveActor
{
    public Discounter()
    {
        Receive<CalculatedDiscountPriceFor>(x =>
        {
            Console.WriteLine("Discounter: CalculatedDiscountPriceFor received");
            Sender.Tell(new DiscountPriceCalculated {
                Requester = Context.ActorSelection("/user/requester") });
        });
    }
}

public class Requester : ReceiveActor
{
    public Requester(IActorRef quotes)
    {
        Receive<PriceQuote>(x =>
        {
            Console.WriteLine("Requester: PriceQuote");
        });
        quotes.Tell(new RequestPriceQuote());
    }
}
{% endhighlight %}
<a href="https://github.com/berczeck/csharpreactivepatterns/tree/master/MessagingWithActors/Message%20Endpoint" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>