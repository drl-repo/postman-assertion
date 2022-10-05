# Example of Postman API Testing Assertion (Guide)

## Check response header

response status
```javascript
pm.response.to.have.status(200)
pm.response.to.have.status('OK')
pm.expect(pm.response.status).equal('OK')
```

response code
```javascript
pm.expect(pm.response.code).to.eql(200)
pm.expect(pm.response.code).to.be.oneOf([201,200])
````

response time

```javascript
pm.response.to.have.responseTime.below(200)
pm.expect(pm.response.responseTime).to.be.below(200)
````
header info
```javascript
pm.response.headers.get('[header-key]')
pm.response.to.have.header('Content-Type')
pm.expect(pm.response.headers.get('Content-Type')).equal('application/json; charset=utf-8')
```

## Check response body

### First, parse to json
```javascript
const responseBody = pm.response.json()
```


### json object (single)

check type of response
```javascript
pm.expect(responseBody).to.be.an('object')
```
has property
```javascript
pm.expect(responseBody).to.have.property('name')
```
value of property
```javascript
pm.expect(responseBody.name).equal('jhone')
```

or chain version
```javascript
pm.expect(responseBody).to.have.property('name').equal('jhone')
```

length of property value
```javascript
pm.expect(responseBody.name).to.have.lengthOf(5)
```

nested property
```javascript
pm.expect(responseBody).to.have.nested.property('address.city')
```

value of nested property
```javascript
pm.expect(responseBody.address.city).to.be.a('string')
pm.expect(responseBody.address.city).equal('London')
//chain version
pm.expect(responseBody).to.have.nested.property('address.city').that.a('string').equal('London')
```

more nested property
```javascript
expect(responseBody).to.have.nested.property('job.company.name')
expect(responseBody).to.have.nested.property('job.company.name').equal('Company name')
```

check has multiple properties
```javascript
//check contain some keys
pm.expect(responseBody).to.include.keys('page','per_page')
//contain all keys
pm.expect(responseBody).to.have.all.keys('page','per_page','total','total_page','data')
```


## list/array json (multiple)


```javascript
//check type
pm.expect(responseBody).to.be.an('array')
//length at least 1
pm.expect(responseBody).to.have.lengthOf.at.least(1)
//chain version
pm.expect(responseBody).be.an('array').that.have.lengthOf.at.least(1)
```

```javascript
pm.expect(responseBody).to.include.key(0)
pm.expect(responseBody[0]).to.have.property('name')
pm.expect(responseBody[0].name).to.be.a('string')
pm.expect(responseBody[0].name).equal('jhone')
pm.expect(responseBody[0].name).to.have.lengthOf(5)

pm.expect(responseBody[0]).to.have.property('name').that.a('string').equal('jhone')
```



```javascript
//check object contain some properties
pm.expect(responseBody[0]).to.include.keys('id','email')

//check object contain all properties
pm.expect(responseBody[0]).to.have.all.keys('id','email','name','avatar')
  ```

more:</br>
Chai BDD [https://www.chaijs.com/api/bdd/](https://www.chaijs.com/api/bdd/)

## validate json response using AJV Schema validator

json object (single)
```javascript

var Ajv = require('ajv')
var ajv = new Ajv({allErrors: true})

var schema = { 
  type: "object",
  properties : {
    data : { 
      type : "object",
      properties: {
          id : { type: "number"},
          email : { type: "string"},
          first_name : { type: "string"},
          last_name : { type: "string"},
          avatar : { type : "string"}
      },
      required : ['id','email','first_name','last_name','avatar']
    },
  },
  required : ["data"]
}

pm.test('validate json response', ()=>{
  const reponseBody = pm.response.json()
  let validate = ajv.compile(schema)
  let isValid = validate(responseBody)
  if(!isValid) console.log(validate.errors)
  pm.expect(isValid).equal(true)
})
````

validate list/array json (multiple)

```javascript
var Ajv = require('ajv')
var ajv = new Ajv({allErrors: true})

var schema = {
  type: "array",
  items: { 
      type: "object",
      properties : {
        id : { type : "number"},
        name : { type: "string"},
        username : { type : "string"},
        email : { type : "string"},
        address : { type : "object"},
        phone : { type : "string"},
        website : { type : "string"},
        company : { type : "object"},
      },
      additionalProperties: false
    }
}

pm.test('validate list json response', ()=>{
  const responseBody = pm.response.json()
  let validate = ajv.compile(schema)
  let isValid = validate(responseBody)
  if(!isValid) console.log(validate.errors)
  pm.expect(isValid).equal(true)
})
```


More: </br>
Ajv [https://ajv.js.org/](https://ajv.js.org/) </br>
JSON schema [https://json-schema.org/understanding-json-schema/](https://json-schema.org/understanding-json-schema/)
