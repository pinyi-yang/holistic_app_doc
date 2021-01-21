# Holistic Node.js Service
<!-- reference -->
[holism]: ./holistic_holism.md
[arccore]: https://encapsule.io/docs/ARCcore

Holistic Node.js Service is a javascript ES6 class encapsuled all the server information and shared models and information between server and client in Holistic Platform. It offers a simple API for developers to start their own holistic app server.

**Methods**
| Method | Description |
|-----|-----|
| .isValid() | return true or false. check whether the Holistic Node.js Service is correctly constructed |
| .toJSON() | return the server memory object |
| .listen(port) | make server listen to the given port |

## Starting Server
After the Holistic Node.js Service Instance is constructed and valided,
```javascript
const { HolisticNodeService } = require("@encapsule/holistic-nodejs-service");
const holisticNodeServiceConstructionRequest = {
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
            cellModels: undefined // pending
            display: {
                d2r2: undefined //pending
            }
        }
    }
}

const holisticServer = new HolisticNodeService(holisticNodeServiceConstructionRequest);

if (!holisticServer.isValid()) throw new Error(holisticServer.toJSON());
holisticServer.listen(8000);
```

## Construction Request
* [appServiceCore](#appServiceCore) (required): a reference to [Holistic Service Core](../holistic-service-core/README.md) class instance.
* [appTypes](#appTypes) (recommended)
    * information of user session and replica spec (trusted)
* [appModels](#appModels) (required):
    * **httpRequestProcessor.holismConfig (required)**: [Holsim][holism] HTTP Server Config: initialization options, type and runtime behavior specialization for runtime-type filtered server instance (**All namespace inside required**).
        * [deploymentEnvironmentFlag](#deploymentEnvironmentFlag)
        * resources
            * [getMemoryFileRegistrationMap](#getMemoryFileRegistrationMap)
            * getServiceFilterRegistrationMap
        * lifecycle
            * redirectPreprocessor
            * getUserIdentityAssertion
            * getUserLoginSession
            * renderHTML5Options
        * appStateContext
    * cellModels (pending): potential future feature
    * display (pending): rare case as most view components can be inherited from appServiceCore (`todo: link to display section in app service core`)

### appServiceCore
**required**

It is a reference to [Holistic Service Core](../holistic-service-core/README.md) class instance. It contains information of:
* shared app metadata between server and client.
* shared models (`TODO: add link to cell model`) between server and client.
* information of user session spec (untrusted)

### appTypes
It is the Holistic Node.js Service Runtime Types. It is a place to add developer-defined runtime type defintions, and extensions to holistic-platfrom-defined types that the Node.js kernel will provides runtime type filtering and/or generic orchestration services

Currently, only [userLoginSession](#userLoginSession) customization is allowed

#### userLoginSession
`TODOs: add`
`1. where and when this information is integrated into holism server`
`2. how is it used during http request at server`
`3. how is it affected the client http request`

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

`TODO: how to add user session in the http requst from client`

### appModels
**required**

App Models is a collenction of application-specific plug-in artifacts derived from @encapsule/holistic RTL's to register for use inside this holiistic Node.js instance

#### httpRequestProcessor.holismConfig
**required**

It contains information to specialize the server with [holism][holism].

##### deploymentEnvironmentFlag
**required**, default to "development"
A flag indicating the app environment and tell backend Node.js bootstrap (server.js) which protocol to use.
`TODOs: including protocol setup??`
* "development"
* "test"
* "staging"
* "production"


##### getMemoryFileRegistrationMap
**require**

It is a synchronous function to obtain server's [holism][holism] memory-cached registration map. This is the placeto register files for the app.

```javascript
function getMemoryFileRegistrationMap({ appBuild, deploymentEnvironment}) {
    const memoryFileRegistrationMap = {
        // one file
        "file path relative to the root of the runtime package": {
            request_bindings: {method: "GET", uris: [""]},
            response_properties: {
                contentEncoding: "utf8", // binary
                contentType: "text/plain", // application/json, application/xml, image/svg+xml, image/png
            }
        },
        //more files
        "some path 1": {...},
        "some path 2": {...},
    };

    if (deploymentEnvironment === "development") {
        // add environment specified file here
    }

    return {error: null, result: getMemoryFileRegistrationMap}
}
```
* method: `TODO: add all methods and explaination`
* uris: `TODO: add explanation`
* contentEncoding: `TODO: add all supported encoding`
* contentType: `TODO: add all types and explaination`


##### getServiceFilterRegistrationMap
**required**

It is a function to obtain the service filter plug-in registration map. It is the place to register all the services.
```javascript
function getServiceFilterRegistrationMap({ appBuild, deploymentEnvironment}) {
    const holismServiceFilterRegistrationMap = [
        // service example
        {
            authentication: { required: false },
            filter, // a build-in holism filter
            request_bindings: { method: "GET", uris: ["/"]},
            response_properties: { 
                contentEncoding: "utf8",
                contentType: "text/html",
            },
            options: {
                // optinal view component render data
                // example
                Login: {}
            }
        },
        //more services
        {...}, {...}
    ]


    if (deploymentEnvironment === "development") {
        // add environment specified services here
    }

    return {error: null, result: holismServiceFilterRegistrationMap}
}
```
`TODO: add list of all namespace`

##### lifecycle

##### appSateContext

#### cellModels
Holisitic v0.0.49 spectrolite does not support extensibility of the Node.js Service via CellModel yet.

#### display
This is only used in rare circumstances. Right now, Holistic Server can inherits the display components from [appServiceCore](#appServiceCore). 

## Construction
1. get appBuild information from [appServiceCore](#appServiceCore)
2. **validate** and **create filters (dispatcher) for** [getMemoryFileRegistrationMap](#getMemoryFileRegistrationMap) and [getServiceFilterRegistrationMap](#getServiceFilterRegistrationMap) with 
    * [arrcore][arccore] filter and built-in spec in [holism][holism]
    * appBuild information
    * [deploymentEnvironmentFlag](#deploymentEnvironmentFlag)
3. create memory and service registration map from filters at 2
4. integrate all view components from [appServiceCore](#appServiceCore) and index [appModels](#appModels) and crate ComponentRouter from d2r2 (`TODO: add link here`).
5. get app metadata type spec from [appServiceCore](#appServiceCore)
6. create [holism][holism] integration filter with:
    * appStateContext
        * input [appSateContext](#appSateContext)
        * ComponentRouter
    * app meta data from [appServiceCore](#appServiceCore)
    * [lifecyle](#lifecycle) 
7. create [holism][holism] server with: 
    * holism integration filter created at 5
    * memory and service registration map created at 3
8. store below in private:
    * [appServiceCore](#appServiceCore)
    * [getMemoryFileRegistrationMap](#getMemoryFileRegistrationMap) and [getServiceFilterRegistrationMap](#getServiceFilterRegistrationMap) at 2
    * memory and service registration map at 3
    * holism integration filter at 6
    * holism server at 7