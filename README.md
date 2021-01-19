# Holistic Server

<!-- References -->
[Encapsule]: https://encapsule.io/docs
[holistic_node]: ./holistic_node_service.md
[holism]: ./holistic_holism.md
[arccore]: https://encapsule.io/docs/ARCcore

[Encapsule Project][Encapsule]
[Holistic Node.js Service][holistic_node]
[Holistic Holism][holism]


Holistic App Platform also provides a package for developers with a simple mechanism to define and re-use backend operations as plug-in services filters hosted by the [holism server RTL][holism]. It contains
* [holism][holism] (an experimental HTTP 1.1 application server)
* [Holistic Node.js Service][holistic_node] (REST framework derived from Node.js HTTP APIs)
* [Arccore][arccore] (@encapsule/arccore filter RTL)

After the [holistic RTL package][Encapsule] is installed in the derived app/service project.

To start a node server, simply construct a new [HolisticNodeService][holistic_node] instance, valid it and invoke the listen method

```javascript
const { HolisticNodeService } = require("@encapsule/holistic-nodejs-service");
const holisticNodeServiceConstructionRequest = {
    // information for the server
    // * 
    // *
}

const holisticServer = new HolisticNodeService(holisticNodeServiceConstructionRequest);

if (!holisticServer.isValid()) throw new Error(holisticServer.toJSON());
holisticServer.listen(8000);
```

The holsticNodeServiceConstructionRequest encapsuled all the information about the server (check [Holistic Node.js Service #Construction](./holistic_node_service.md#construction) for more details).

## Holistic Server Construction Request
The request is a developer-defined descriptor object containing the information requried to configure and initialize a holistic server running inside a Node.js OS process.
```javascript
const constructionRequest = {
    appServiceCore: {...},

}
```

### appServiceCore (required)
App service core ([Holistic Node Service - appServiceCore](./holistic_node_service.md)) is a reference to the HolisticServiceCore (`TODO: add link to holistic service core`) or descriptor object from which a new instance of HolisticServiceCore can be construct.

## Functions