# Bonita Continuous Delivery Add-on

::: info
Welcome to **Bonita Continuous Delivery** documentation website!
:::

![Bonita Continuous Delivery Add-on Logo](images/bcd_logo.png "Bonita Continuous Delivery Add-on Logo")

The Bonita Continuous Delivery (BCD) add-on provides a solution to use _Continuous Delivery_ practice for your Bonita Living Application development and deployment.

It is composed of two parts:

*   First, a framework for **Continuous Integration** of Living Applications.  
    BCD lets you build a Living Application repository and deploy the generated artifacts from the command line. Besides, a pre-configured standalone [Jenkins CI](https://jenkins.io/) environment is also provided with BCD.
*   Second, a solution for **Provisioning** of Bonita stacks on cloud or on-premises hosts.  
    BCD lets you deploy Bonita on existing servers using [Docker](https://www.docker.com/what-container) containers. Furthermore, when used with the [Amazon Web Services (AWS) cloud platform](https://aws.amazon.com/), BCD enables you to create and delete [AWS EC2 instances](https://aws.amazon.com/ec2/) automatically.

![BCD Overview](images/bcd_capabilities.png "BCD Overview")

<div class="col-md-4">
<div class="panel panel-default">
<div class="panel-heading">Highlights</div>
<div class="panel-body">
<div style="padding: 15px; padding-bottom: 0px;">Find out what’s new in Bonita Continuous Delivery add-on! Read the release notes.</div>
<div class="menu-block-wrapper">

* [:fa-angle-double-right: What's new in BCD?](release_notes.md)
<!--{ul:.menu .nav}-->
</div>
</div>
</div>
</div>

<div class="col-md-4">
<div class="panel panel-default">
<div class="panel-heading">Getting started</div>
<div class="panel-body">
<div style="padding: 15px; padding-bottom: 0px;">Follow this guide with step-by-step instructions to start using Bonita Continuous Delivery solution.</div>
<div class="menu-block-wrapper">

* [:fa-angle-double-right: Getting started with BCD](getting_started.md)
<!--{ul:.menu .nav}-->
</div>
</div>
</div>
</div>

<div class="col-md-4">
<div class="panel panel-default">
<div class="panel-heading">Howtos</div>
<div class="panel-body">
<div style="padding: 15px; padding-bottom: 0px;">Check out the tutorials for practical guides about using Bonita Continuous Delivery add-on.</div>
<div class="menu-block-wrapper">

* [:fa-angle-double-right: BCD Howtos](_howtos.md)
<!--{ul:.menu .nav}-->
</div>
</div>
</div>
</div>

<div class="clearfix"></div>

Basic concepts <!--{.h2}-->

This add-on provides a `bcd` command-line interface (BCD CLI) which enables to:

*  Build and deploy Bonita Living Applications
*  Deploy a full Bonita stack - A **Bonita stack** is formed by a database service, Bonita Docker containers running Tomcat application servers and a HAProxy load balancer when deploying a multi-node Bonita cluster.

The BCD CLI is provided within a ready-to-use environment called **BCD Controller**. This environment is packaged as a Docker image.  
Then a controller container will have to be started interactively on your control host from which `bcd` commands will be issued.

::: warning
It is highly recommended to have a clear understanding of [Docker essential concepts](https://docs.docker.com/engine/docker-overview/) prior to reading this documentation.
:::

