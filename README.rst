=========================
Wordpress - ECS ComposeX
=========================

This repository serves as resource and example point people exploring with AWS ECS and ECS ComposeX.

Our objective here is to how easy it is to use an existing docker-compose file for an application, such as
wordpress, split it into multiple "environment" which will allow local development and publish to AWS.

The docker-compose file originally comes from https://gallery.ecr.aws/bitnami/wordpress.


Running locally
================

To run the split docker-compose file locally as original, simply run

.. code-block:: bash

   docker-compose -f docker-compose.yml -f local.yml up


Deploy to AWS
===============


Verify and adapt all the settings and configuration you find in **aws.yml** file to match your environment.
Once that is done, all you have to do is deploy to AWS via

.. code-block::

   ecs-compose-x up -f docker-compose.yml -f aws.yml -p wordpress-demo

.. hint::

   If you do not wish to specify a bucket you can let ecs-composex create all the necessary for you. Simply run

   ecs-compose-x init

Adding WAF
----------

To add WAF, simply the run the command including the ``wafv2.yaml`` to the command line

.. code-block::

   ecs-compose-x up -f docker-compose.yml -f aws.yml -p wordpress-demo -f wafv2.yml


.. note::

   Enabling the WAF with the defined rules here will deny access to ``/wp-admin``. You can create rules to allow your IP addresses to access it.


x-elbv2 Conditions
-------------------

You will notice in the conditions for ``x-elbv2`` pointing to the service, a ``Conditions`` section.
There, we indicate that we want traffic to be sent to wordbress **only if the hostname matches**.
By default, the ELBv2 will be configured to return 418

.. code-block::

    curl -k https://wordpr-wordp-sw06gyb58v6i-1922613310.eu-west-1.elb.amazonaws.com/ -v
    < HTTP/2 418
    < server: awselb/2.0
    < date: Wed, 28 Feb 2024 10:35:11 GMT
    < content-type: application/json; charset=utf-8
    < content-length: 24
    <
    * Connection #0 to host wordpr-wordp-sw06gyb58v6i-1922613310.eu-west-1.elb.amazonaws.com left intact
    {"Info": "Be our guest"}

I have found this is a great way to rebuke crawlers that try their luck on an IP addresses basis instead of hostnames.
If I try to get on the hostname itself, i.e ``wordpress.demos.bdd-testing.compose-x.io``, I can reach it and all subsequent
pages.

To further secure the wp-admin path, I recommend to use the x-cognito integration to force going through authentication
before you can even hit the WP Admin login page.
