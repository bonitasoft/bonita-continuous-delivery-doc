# Bonita Continuous Delivery Add-on

::: info
Welcome to **Bonita Continuous Delivery** documentation website!
:::

![Bonita Continuous Delivery Add-on Logo](images/bcd_logo.png "Bonita Continuous Delivery Add-on Logo")

The Bonita Continuous Delivery (BCD) add-on provides a solution to use _Continuous Delivery_ practice for your Bonita Living Application development and deployment.

The BCD add-on is powered by [Docker containerization solution](https://www.docker.com/) and [Ansible automation framework](https://www.ansible.com/). It is composed of two parts:

*   First, a solution for **Provisioning** of a Bonita stack on cloud or on-premises hosts.  
    BCD lets you deploy Bonita on existing servers using [Docker](https://www.docker.com/what-container) containers. Furthermore, when used with [Amazon Web Services (AWS) cloud platform](https://aws.amazon.com/), BCD enables you to create and delete [AWS EC2 instances](https://aws.amazon.com/ec2/) automatically.
*   Second, a framework for **Continuous Delivery** of Living Applications.  
    BCD lets you build a Living Application repository and deploy the generated artifacts from the command line.

::: warning
It is highly recommended to have a clear understanding of [Docker essential concepts](https://docs.docker.com/engine/docker-overview/) prior to reading this documentation.
:::

<div class="col-md-4">
<div class="panel panel-default">
<div class="panel-heading">Quick start</div>
<div class="panel-body">
<div style="padding: 15px; padding-bottom: 0px;">Follow this guide with step-by-step instructions to quickly start using Bonita Continuous Delivery solution.</div>
<div class="menu-block-wrapper">

*  [:fa-angle-double-right: Getting started with BCD](quickstart.md)
<!--{ul:.menu .nav}-->
</div>
</div>
</div>
</div>

<div class="col-md-4">
<div class="panel panel-default">
<div class="panel-heading">Release notes</div>
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

*   deploy a full Bonita stack
*   build and deploy Bonita Living Applications

A **Bonita stack** is formed by a database service, Bonita Docker containers running Tomcat application servers and a HAProxy load balancer when deploying a multi-node Bonita cluster.

The BCD CLI is provided within a ready-to-use environment called **BCD Controller**. This environment is packaged as a Docker image.  
Then a controller container will have to be started interactively on your control workstation from which `bcd` commands will be issued.

Architecture overview <!--{.h2}-->

Here is an overview of how BCD drives the provisioning of a Bonita stack.

![BCD Architecture Overview](images/bonita_platform_stack.png "BCD Architecture Overview")

*   A **Host** can be a AWS EC2 instance or an existing server running on-premises or on another cloud provider.
*   The control workstation bind mounts some essential files with the controller container including:
    *   `~/bonita-continuous-delivery` - used as a working directory for BCD CLI known as the `BCD_HOME` directory
    *   `~/.ssh/private_key` - used to allow SSH access to target hosts
    *   `~/.boto` - credential to your AWS account (when applicable)
