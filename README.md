php-ddd-cargo-sample
====================

PHP 5.4+ port of the cargo sample used in Eric Evans Domain-Driven Design book

Goal Of Redesign
-------------------
Before we continue adding functionality to the cargo sample the team has decided to change the technical layers of the application.
One change is replacing ZF2 with [pimf-framework](http://pimf-framework.de). The reason for that is simple. We want to decrease complexity
in the application layer and focus on the model. Pimf supports the [Action-Domain-Responder](https://github.com/pmjones/adr)
pattern and uses an easy to understand structure. This makes it a perfect choice for a weblication whose application layer
only routes requests to the model and manages the presentation of data returned by the model.
Second change is the replacement of Doctrine with [ProophEventStore](https://github.com/prooph/event-store). The team wants to split
the model in a Write Side and a Read Side ([CQRS](http://cqrs.files.wordpress.com/2010/11/cqrs_documents.pdf)) and wants to use
[Event Sourcing](http://verraes.net/2014/03/practical-event-sourcing/) to get the full power out of Domain Events.
[Buttercup.protects](http://buttercup-php.github.io/protects/) provides the basics for our new event sourced aggregates.
Last but not least the communication between application controllers and Write Model is decoupled with the help of [ProophServiceBus](https://github.com/prooph/service-bus).
This allows the team to use commands to trigger actions in Write Model without coupling controllers directly to command handlers or services.


TODO
----
- [ ] Replace ZF2 with pimf
- [ ] Replace Doctrine with PES and Buttercup.protects
- [ ] Introduce PSB