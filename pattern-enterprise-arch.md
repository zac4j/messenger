Patterns

## Have a look at pattern

Each pattern describes a problem which occurs over and over again in our environment, and then describes the core of the solution to that problem, in such a way that you can use this solution a million times over, without ever doing it the same way twice -- Christopher Alexander

Another way of looking at it is that a pattern is a chunk of advice and the art of creating patterns is to divide up many pieces pf advice into relatively independent chunks so that you can refer to them and discuss them more or less separetely.

## Layering

#### *layer* and *tier*
Often the two are used as synonyms, but most people see tier as implying a physical separation. Client-server systems are often described as two-tier systems, and the separation is physical: The client is a mobile phone or desktop and the server is a server. I use layer to stress that you don't have to run the layers on different machines.

#### The Theee Principal Layers
An architecture of three primary layers:
| Layer        |                                          Responsibilities                                         |
|--------------|:-------------------------------------------------------------------------------------------------:|
| Presentation | Provision of services, display of information, HTTP requests, command-line invocations, batch API |
| Domain       | Logic that is the real point of the system                                                        |
| Data Source  | Communication with databases, messaging systems, transaction managers, other packages             |

#### Origanizing Domain Logic

Separeted into three primary patterns:
+ Transaction Script:
	A Transaction Script is essential a procedure that takes the input from presentation -> process it with validations & calculations -> stores data in database -> invokes any operations from other systems.
+ Domain Model
	Domain Model is the object-oriented way to handle complex logic, with a Domain Model we build a model of domain,is origanized primarily around the nouns in the domain. The logic for handling validations & calculations would be placed into this domain model.
+ Table Module
	A Table Module is in many ways a middle ground between a Transaction Script and a Domain Model. Organizing the domain logic around tables rather than straight procedures provides more structure and makes it easier to find and remove duplication.