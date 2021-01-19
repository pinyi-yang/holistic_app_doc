# Holistic Holism
[distribution](#usage)
## Distribution

The package is intended for use in derived applications and services.

```
Package: @encapsule/holism v0.0.49 "spectrolite" build ID "DVURnq5RRpq6i_80XjzOHg"
Sources: Encapsule/holistic-master#6d19185ce32be38ac71ae2f8b74eab8a190f7cd3
Created: 2021-01-13T03:36:18.000Z Purpose: library (Node.js) License: MIT
```

The @encapsule/holism package is a runtime library (RTL) distributed in the @encapsule/holistic package:

```
@encapsule/holistic/PACKAGES/holism
```

The `appgen` utility is used to create a copy of this RTL package inside your derived app/service project...

```
@AcmeCo/SampleApp/HOLISTIC/PACKAGES/holism
```

... and modify its `package.json` file to include the following package registration:

```
"devDependencies": {
    "@encapsule/holism": "file:./HOLISTIC/PACKAGES/holism"
}
```

## Usage
In your derived app/service implementation code:

```JavaScript
const holism = require('@encapsule/holism');
console.log(JSON.stringify(holism.__meta));
/* ... your derived code here ... */
```