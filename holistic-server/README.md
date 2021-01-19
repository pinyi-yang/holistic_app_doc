# Holistic Server

<!-- References -->
[Encapsule]: https://encapsule.io/docs
[holistic_node]: ./holistic-server/holistic_node_service.md
[holism]: ./holistic-server//holistic_holism.md
[arccore]: https://encapsule.io/docs/ARCcore
[service core]: ../holistic-service-core/README.md

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
    appTypes: {
        userLoginSession: {
            trusted: {
                userIdentityAssertionDescriptorSpec: {...} //default to null
                userLoginSessionReplicaDataSpec: {...} // default to null
            }
        }
    },
    appModels: {
        httpRequestProcessor: {
            holismConfig: {
                deploymentEnvironmentFlag: "development", // "production"
                registrations: {
                    resources: {
                        getMemoryFileRegistrationMap: function(...) {...},
                        getServiceFilterRegistrationMap: function(...) {...}
                    }
                },
                lifecycle: {
                    redirectPreprocessor: function(...) {...},
                    getUserIdentityAssertion: function(...) {...},
                    getUserLoginSession: function(...) {...},
                    renderHTML5Options
                },
                appStateContext: {
                    vp5GroupAuthorizer
                }
            },
        }
    }
}
```

### appServiceCore (required)
App service core is a reference to the HolisticServiceCore ([link][service core]) or descriptor object from which a new instance of HolisticServiceCore can be construct. It contains information about:
* app metadata
* user session control
* page uri and hashroute information
* shared models between server and client

For more details, please check the [Holistic Service Core Readme][service core]

### appTypes (recommended)
The appTypes is developer-defined runtime type definitions, and extensions to holistic-platform-defined types for a small set of core application-layer objects for which the Node.js service kernel provides runtime type filtering and/or generic orchestration services on behalf of the derived app service.

* [userLoginSession](#userLoginSession)

#### userLoginSession
It provides runtime data filtering and orchestration of sensitive user login session information.

**userIdentityAssertionDescriptorSpec** is an assertion of a user's identify presented back to [holism][holism] during HTTP request processing that contains a deserialized copy of information the app uses to represent such an assertion, e.g. a cookie.

```javascript
// example
const userIdentityAssertionDescriptorSpec = {
    ____types: "jsObject",
    ____defaultValue: {},
    sessionId: {
        ____accept: ["jsNull", "jsString"],
        ____defaultValue: null
    }
}
```
</br>

**userLoginSessionReplicaDataSpec** is the format of the data that a registered [holism][holism] service filter plug-in will receive when it is dispatched if the requester has attached a valide assertion of identity to the HTTPM request
```javascript
// example

const userLoginSessionReplicaDataSpec = {
    sessionId: {
        ____accept: "jsString",
        ____appdsl: {
            createUserSessionOutParam: true,
            sensitive: true
        }
    },
    createdBy: {
        ____accept: "jsString",
        ____inValueSet: ["google", "holistic"],
        ____appdsl: {
            sensitive: true
        }
    },
    expireTime: {
        ____accept: "jsNumber",
        ____appdesl: { createUserSessionOutParam: true }
    },
    ...
}
```
</br>

## appModels (required)
App Models is a collenction of application-specific plug-in artifacts derived from @encapsule/holistic RTL's to register for use inside this holiistic Node.js instance

### httpRequestProcessor (required)
The httpRequestProcessor contains information used to specialize a Holistic App Server class instance's embedded HTTP request processor.

During the Holistic Node Service instance construction the information will be used to create a server with [holism][holism].

* holismConfig: initialization options,  type and runtime behvaior specializations f or runtime-type filtered [holism][holism] embedded HTTP 1.1 server instance
    * deploymentEnvironmentFlag: development, test, staging or production, default to development
    * 

#### deploymentEnvironmentFlag (required)
One of flags in a flag set (development, test, staging or production). It tells the backend Node.js service bootstrap (server.js) which protocal it chooses.



#### registrations

#### lifecycle

#### appStateContext

#### options


### cellModels (pending)
Holisitic v0.0.49 spectrolite does not support extensibility of the Node.js Service via CellModel yet.

### display (pending)
This is only used in rare circumstances. Right now, Holistic Server can inherits the display components from [Holistic Service Core][service core]. 

## Functions