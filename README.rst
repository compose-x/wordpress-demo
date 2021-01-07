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

.. code-block:: yaml

   ecs-composex up -f docker-compose.yml -f aws.yml -n wordpress-demo

.. hint::

   If you do not wish to specify a bucket you can let ecs-composex create all the necessary for you. Simply run

   ecs-composex init

