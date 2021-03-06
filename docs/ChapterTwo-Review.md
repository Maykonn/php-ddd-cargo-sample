ChapterTwo Review
=================

Heavy Refactoring
-----------------
In ChapterTwo the team changed the complete domain model again. The initial ideas about the booking procedure were redefined with the help of the domain expert and the Ubiquitous Language. Now, the team has a clear picture of what the shipping application should be responsible for.
You can follow the decisions by browsing through the [issues of ChapterTwo](https://github.com/codeliner/php-ddd-cargo-sample/issues?milestone=1&page=1&state=closed)


Value of using an Ubiquitous Language
-------------------------------------
We don't want repeat Eric Evans here. Please read his book for detailed information. The team has changed many method names to better reflect the Ubiquitous Language. This is especially true for all PHPUnit test methods. The team switched to a behavior naming convention for test methods. See [issue #13](https://github.com/codeliner/php-ddd-cargo-sample/issues/13) for details.
In addition all getter methods were renamed. It is a rule of thumb that you should avoid the usage of `getProperty` and `setProperty` methods in domain model classes. Find more expressive names to reflect the Ubiquitous Language. For simple property getters that means `$entity->property()` is quite enough cause there is no public setter pendant. Instead you define a use case specific method that changes the property of the entity while processing the use case `$entity->doSomethingThatChangesProperty()`.


Persisting ValueObjects with doctrine - Part 1 
----------------------------------------------
The team now uses [doctrine xml mapping files](http://docs.doctrine-project.org/en/2.0.x/reference/xml-mapping.html) instead of entity annotations. The new files can be found in the [ORM](https://github.com/codeliner/php-ddd-cargo-sample/tree/ChapterTwo/module/Application/src/Application/Infrastructure/Persistence/Doctrine/ORM) directory. Now, the domain model is more decoupled of the infrastructure. And we have another good reason for that. Doctrine can only map entities to tables, but we have to store ValueObjects in own tables, too. For example the [RouteSpecification](https://github.com/codeliner/php-ddd-cargo-sample/blob/ChapterTwo/module/Application/src/Application/Domain/Model/Cargo/RouteSpecification.php) and the [Itinerary](https://github.com/codeliner/php-ddd-cargo-sample/blob/ChapterTwo/module/Application/src/Application/Domain/Model/Cargo/Itinerary.php). To achieve this, we tell doctrine, that it should treat them as entities. If we do this with annotations, the classes would look confusing, cause we would have ValueObjects marked as @Entity. But our ValueObjects have still a confusing detail that we can not avoid, just hide. The RouteSpecification is an immutable ValueObject for our domain. It's public interface is designed that way, but
internally the RouteSpecification has a `private $id` property, a so called [surrogate key](http://en.wikipedia.org/wiki/Surrogate_key), only visible for the class itself and doctrine. The id is auto generated and doesn't play a role in our domain. The surrogate key only supports doctrine's requirements that a class needs an identifier when we want to persist it in a table.

Another strategy to persist ValueObjects is shown with the [Legs](https://github.com/codeliner/php-ddd-cargo-sample/blob/ChapterTwo/module/Application/src/Application/Domain/Model/Cargo/Leg.php) of an Itinerary. We have created a [doctrine custom mapping type](http://doctrine-orm.readthedocs.org/en/latest/cookbook/custom-mapping-types.html) for the [legs](https://github.com/codeliner/php-ddd-cargo-sample/blob/ChapterTwo/module/Application/src/Application/Infrastructure/Persistence/Doctrine/Type/LegsDoctrineType.php) property of an Itinerary. The custom type serializes the legs array using `json_encode`. In the [Itinerary xml file](https://github.com/codeliner/php-ddd-cargo-sample/blob/ChapterTwo/module/Application/src/Application/Infrastructure/Persistence/Doctrine/ORM/Application.Domain.Model.Cargo.Itinerary.dcm.xml) you can find the mapping definition. The `custom type` is set as type of the `Itinerary#legs` property. Thus the serialized `legs json string` is stored in the `legs column` of the `itinerary table`. ValueObjects containing just one property can be mapped in a similar fashion.

In one of the next chapters, we will show a third way of persisting ValueObjects, called `embedded ValueObject`. If you can't wait, have a look at this [doctrine pull request](https://github.com/doctrine/doctrine2/pull/835).

Finally, a very good article of [Entities vs Value Objects and Doctrine 2](http://russellscottwalker.blogspot.de/2013/11/entities-vs-value-objects-and-doctrine-2.html) written by Russell Walker. He uses another interesting way to persist ValueObjects.


Continuous Integration
----------------------
During ChapterTwo release the team has setup the php-ddd-cargo-sample to run on [travis-ci](https://travis-ci.org/codeliner/php-ddd-cargo-sample).
Travis-ci is a Continuous Integration System that can be connected to GitHub projects. It checks out a GitHub repo, read the [.travis.yml](https://github.com/codeliner/php-ddd-cargo-sample/blob/master/.travis.yml) configuration file to
setup a temporary virtual box and executes the defined test scripts. We have configured travis-ci to first run the PHPUnit tests and than run the Behat feature tests.
The Behat setup was a bit tricky. If you have a look at the commit history or the travis-ci build history you can find various failed builds. But now everything works as a charm. First we thought, that we have to install an apache2 server with mod_php, but our cargo sample requires PHP 5.4 and with that in mind, we simply use the internal PHP 5.4 for the tests.
Another nice tool is [Scrutinizer](https://scrutinizer-ci.com/g/codeliner/php-ddd-cargo-sample/). Scrutinizer performs code analyses after each commit and shows you buggy or confusing parts of your code.