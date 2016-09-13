---
layout: page
title: Messaging Construction
permalink: /message-construction/
categories: akka messaging .net csharp
---
<p class="rss-subscribe">
For more details and full analysis of each pattern described in this section, please refer to <b>Chapter 6</b> of 
<a href="http://www.informit.com/store/reactive-messaging-patterns-with-the-actor-model-applications-9780133846836">Reactive Messaging Patterns with the Actor Model</a> 
by <a href="https://twitter.com/vaughnvernon">Vaughn Vernon</a>.
</p>
<a name="Sections">Sections</a>
<ul>
    <li>
      <a href="#CommandMessage">Command Message</a>
    </li>
    <li>    
      <a href="#DocumentMessage">Document Message</a>
    </li>
    <li>
      <a href="#EventMessage">Event Message</a>
    </li>
    <li>
      <a href="#RequestReply">Request-Reply</a>
    </li>
    <li>
      <a href="#ReturnAddress">Return Address</a>
    </li>
    <li>
      <a href="#CorrelationIdentifier">Correlation Identifier</a>
    </li>
    <li>
      <a href="#MessageExpiration">Message Expiration</a>
    </li>
    <li>
      <a href="#FormatIndicator">Format Indicator</a>
    </li>
</ul>

<h2 class="page-heading"><a name="CommandMessage">Command Message</a></h2>
<p class="rss-subscribe">Use a Command Message to reliably invoke a procedure in another application.</p>

{% gist 6f393e3406a2c3d030ef78f9326c8a6d %}
<a href="{{ site.github_repository }}MessageConstruction/Command%20Message/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>

<h2 class="page-heading"><a name="DocumentMessage">Document Message</a></h2>
<p class="rss-subscribe">Use a Document Message to reliably transfer a data structure between applications.</p>

{% gist ac92bd2cf6299812dbe257c4adcdf7e4 %}
<a href="{{ site.github_repository }}MessageConstruction/Document%20Message/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>

<h2 class="page-heading"><a name="EventMessage">Event Message</a></h2>
<p class="rss-subscribe">Use an Event Message for reliable, asynchronous event notification between applications.</p>

{% gist 0629894184166029eb0f8d6342259316 %}
<a href="{{ site.github_repository }}MessageConstruction/Event%20Message/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>

<h2 class="page-heading"><a name="RequestReply">Request-Reply</a></h2>
<p class="rss-subscribe">Send a pair of Request-Reply messages, each on its own channel.</p>

{% gist 128379245f4b2bad6630522af1965890 %}
<a href="{{ site.github_repository }}MessageConstruction/Request-Reply/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>

<h2 class="page-heading"><a name="ReturnAddress">Return Address</a></h2>
<p class="rss-subscribe">The request message should contain a Return Address that indicates where to send the reply message.</p>

{% gist 4664027480527be727df3738539bdd07 %}
<a href="{{ site.github_repository }}MessageConstruction/Return%20Address/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>

<h2 class="page-heading"><a name="CorrelationIdentifier">Correlation Identifier</a></h2>
<p class="rss-subscribe">
Each reply message should contain a Correlation Identifier, a unique identifier that indicates which request message this reply is for.
</p>

{% gist 03b798fb124c364b6a50a7df07c24fa0 %}
<a href="{{ site.github_repository }}MessageConstruction/Correlation%20Identifier/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>

<h2 class="page-heading"><a name="MessageExpiration">Message Expiration</a></h2>
<p class="rss-subscribe">Set the Message Expiration to specify a time limit how long the message is viable.</p>

{% gist 0abb69a9de16bd1e4bda96d60d630309 %}
<a href="{{ site.github_repository }}MessageConstruction/Message%20Expiration/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>

<h2 class="page-heading"><a name="FormatIndicator">Format Indicator</a></h2>
<p class="rss-subscribe">
Design a data format that includes a Format Indicator, so that the message specifies what format it is using.
</p>

{% gist 017d91a9028c12e6bb133af563804077 %}
<a href="{{ site.github_repository }}MessageConstruction/Format%20Indicator/Program.cs" 
    target="_blank">Complete Code</a>
<p><a href="#Sections">Sections</a></p>