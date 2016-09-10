---
layout: page
title: Introduction
permalink: /introduction/
---
<h2 class="page-heading"><a name="Sections">Sections</a></h2>
  <ol>
    <li>Introduction</li>
    <li><a href="/messaging-with-actors/">Messaging with Actors</a></li>
    <li>Messaging Channels</li>
    <li>Message Construction</li>
    <li>Message Routing</li>
    <li>Message Transformation</li>
    <li>Message Endpoints</li>
    <li>System Management and Infrastructure</li>
  </ol>

This tutorial is intended to give an introduction to using <a href="http://getakka.net/">Akka .NET</a> by creating a simple HelloWorld actor using C#.

<h2 class="page-heading">Set up your project</h2>
<ul>
<li>Create a new C# Console Application.</li>
<li>Open up the Package Manager Console and type: </li>
{% gist 0d7288d673c08370349227d064dfd46a %}

<li>Import the next namespaces:</li>
{% gist 52741f59efdd9cbb40015072d3fd7344 %}

</ul>
<h2 class="page-heading">Create your first actor</h2>
<ul>
<li>Create a message type that our actor will respond to:</li>
{% gist 52741f59efdd9cbb40015072d3fd7344 %}
<li>Create our Hellow World actor:</li>
{% gist b5f3c765a4a44f4c544222fd36f65c3f %}
<li>Create an ActorSystem and calling ActorOf</li>
{% gist 5e439641584dd5c86378bfcfb3ce3c8f %}
<li>For more information read the <a href="http://getakka.net/docs/" target="_blank">Akka.Net documentation</a></li>
<li>Download the complete code <a href="{{ site.github_repository }}/Introduction/HelloWorldAkka/" target="_blank">here</a></li>
</ul>