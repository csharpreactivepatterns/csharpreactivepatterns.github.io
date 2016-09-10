---
layout: page
title: Messaging Channels
permalink: /messaging-channels/
categories: akka messaging channel .net csharp
---
For more details and full analysis of each pattern described in this section, please refer to <b>Chapter 5</b> of 
<a href="http://www.informit.com/store/reactive-messaging-patterns-with-the-actor-model-applications-9780133846836">Reactive Messaging Patterns with the Actor Model</a> 
by <a href="https://twitter.com/vaughnvernon">Vaughn Vernon</a>.

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

<h2 class="page-heading"><a name="PointPoint">Point-to-Point Channel</a></h2>
Send the message on a Point-to-Point Channel, which ensures that only one receiver will receive a particular message.

{% gist 9ea1b458c42e9fac1c964ea31dd5f253 %}
<a href="{{ site.github_repository }}MessagingChannels/Point-to-Point%20Channel/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>

<h2 class="page-heading"><a name="PublishSubscribe">Publish-Subscribe Channel</a></h2>
Send the event on a Publish-Subscribe Channel, which delivers a copy of a particular event to each receiver.

{% gist bd6869782b5a9474b44b9aad020b2a2f %}
<a href="{{ site.github_repository }}MessagingChannels/Publish-Subscribe%20Channel/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>

<h2 class="page-heading"><a name="Datatype">Datatype Channel</a></h2>
Use a separate Datatype Channel for each data type, so that all data on a particular channel is of the same type.

{% gist fac07786f23b07a7ae4236d7935ee020 %}
<a href="{{ site.github_repository }}MessagingChannels/Datatype%20Channel/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>

<h2 class="page-heading"><a name="InvalidMessage">Invalid Message Channel</a></h2>
The receiver should move the improper message to an Invalid Message Channel, a special channel for messages that could not be processed by their receivers.

{% gist e0d69c49e008d645d258901724a939a7 %}
<a href="{{ site.github_repository }}MessagingChannels/Invalid%20Message%20Channel/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>

<h2 class="page-heading"><a name="DeadLetter">Dead Letter Channel</a></h2>
When a messaging system determines that it cannot or should not deliver a message, it may elect to move the message to a Dead Letter Channel.

{% gist bc07fde4fe825ecae20808c9bb04cef2 %}
<a href="{{ site.github_repository }}MessagingChannels/Dead%20Letter%20Channel/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>

<h2 class="page-heading"><a name="ChannelAdapter">Channel Adapter</a></h2>
Use a Channel Adapter that can access the application's API or data and publish messages on a channel based on this data, and that likewise can receive messages and invoke functionality inside the application.

{% gist 26696827ca98cf2504fc3a19a0898777 %}
<a href="{{ site.github_repository }}MessagingChannels/Channel%20Adapter/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>

<h2 class="page-heading"><a name="MessageBridge">Message Bridge</a></h2>
Use a Message Bridge, a connection between messaging systems, to replicate messages between systems.

{% gist 63a9e2120ee16716370ab51a6d40b137 %}
<a href="{{ site.github_repository }}MessagingChannels/Message%20Bridge/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>

<h2 class="page-heading"><a name="MessageBus">Message Bus</a></h2>
Structure the connecting middleware between these applications as a Message Bus that enables them to work together using messaging.

{% gist d2f50f5b2ca9e7af50ce54c13c444b5c %}
<a href="{{ site.github_repository }}MessagingChannels/Message%20Bus/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>
