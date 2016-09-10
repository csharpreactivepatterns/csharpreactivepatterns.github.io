---
layout: page
title: Messaging with Actors
permalink: /messaging-with-actors/
categories: akka messaging actor .net csharp
---
For more details and full analysis of each pattern described in this section, please refer to <b>Chapter 4</b> of 
<a href="http://www.informit.com/store/reactive-messaging-patterns-with-the-actor-model-applications-9780133846836">Reactive Messaging Patterns with the Actor Model</a> 
by <a href="https://twitter.com/vaughnvernon">Vaughn Vernon</a>.

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
Connect the applications using a Message Channel, where one application writes information to the channel and the other one reads that information from the channel. 
When using Akka.NET, this concept is represented by the actor's mailbox.

{% gist 61b03bae298fd0eed57804d0a0757003 %}
<a href="{{ site.github_repository }}MessagingWithActors/Message%20Channel/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>

<h2 class="page-heading"><a name="Message">Message</a></h2>
Package the information into a Message, a data record that the messaging system can transmit through a message channel.

{% gist 298542716b078652b292639ab94d0f56 %}
<a href="{{ site.github_repository }}MessagingWithActors/Message/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>

<h2 class="page-heading"><a name="PipesFilters">Pipes and Filters</a></h2>
Use the Pipes and Filters architectural style to divide a larger processing task into a sequence of smaller, independent processing steps (Filters) that are connected by channels (Pipes).

{% gist b5b4dfaaf816c7b5b30160926fbd0df2 %}
<a href="{{ site.github_repository }}MessagingWithActors/Pipes%20and%20Filters/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>

<h2 class="page-heading"><a name="MessageRouter">Message Router</a></h2>
Insert a special filter, a Message Router, which consumes a Message from one Message Channel and republishes it to a different Message Channel channel depending on a set of conditions.

{% gist e3ab3bf538e36d97bedc4a82504e6004 %}
<a href="{{ site.github_repository }}MessagingWithActors/Message%20Router/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>

<h2 class="page-heading"><a name="MessageEndpoint">Message Endpoint</a></h2>
Connect an application to a messaging channel using a Message Endpoint, a client of the messaging system that the application can then use to send or receive messages.

{% gist 54832755f79b4fc7b238e2d0401dc9a9 %}
<a href="{{ site.github_repository }}MessagingWithActors/Message%20Endpoint/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>