# λ# - GraphQL with AWS's AppSync

Congratulations! Welcome to Lambda Sharp, LLC! A **fictitious** company that you just started working at! [AppSync](https://aws.amazon.com/appsync/) will be the focus of your work.

> SUBJECT: Data project that must be completed!
>
> FROM: Dave aka DBA <mailto:<dba@lambdasharp.io>>
>
> We just acquirered **Northwind Traders**! We need an API ASAP and your the only developer available right now. I've created a partial GraphQL schema and a mock datasource lambda function to start querying the data. We need this in production **TONIGHT!**
>
> Thanks,
>
> Dave aka "The DBA"

![Northwind Traders database schema](http://archive.oreilly.com/oreillyschool/courses/dba3/images/FinalProject/Northwind.png)

## Level 0 - Setup



* Create a new lambda function from this repo. [northwindTradersMockDb.js](src/northwindTradersMockDb.js)
  1. Navigate to your [Lambda Console](console.aws.amazon.com/lambda) and click **Create function**.
  2. Keep the default **Author from scratch** selected.
  3. Name your function.
  4. Keep the default **Runtime** (NodeJs).
  5. Select the role you created from the **Existing Role** dropdown.
  6. Click **Create function** to create function
* Create a **new api** in AppSync as **LambdaSharp** with a **Custom Schema**. Copy/paste [schema.graphql](src/schema.graphql) contents in to the **Schema** window. Save.
* Create new Data Source in AppSync as `NorthwindTraders` referencing the new lambda function.


## Level 1 - Query

* Add a resolver to get all customers. In Schema, Data Types > `Query` > `allCustomers`. Use the lambda function `NorthwindTraders` > Click Attach. Add the request mapping template.

```
{
    "version" : "2017-02-28",
    "operation": "Invoke",
    "payload": {
    	"field":"allCustomers"
    }
}
```

* Add a resolver to get a single customer. In Schema, Data Types > `Query` > `getCustomer`. Use the lambda function `NorthwindTraders` > Click Attach. Add the request mapping template.

```
{
    "version": "2017-02-28",
    "operation": "Invoke",
    "payload": {
        "field": "getCustomer",
        "arguments":  $util.toJson($context.arguments)
    }
}
```

* In `Queries`, query for customers.

```
query {
  allCustomers {
    CustomerID
    ContactName
  }
}
```

## Level 2 - Query Related data

* Get a single customer order using `getCustomer`
```
query {
  getCustomer(id: "ADD A CUSTOMER ID HERE") {
    __typename
    ContactName
    ContactTitle
    Country
  }
}
```

* Add a resolver to get orders of a single customer. In Schema, Data Types > `Customer` > `getCustomerOrders`. Use the lambda function `NorthwindTraders` > Click Attach. Add the request mapping template.

```
{
    "version" : "2017-02-28",
    "operation": "Invoke",
    "payload": {
        "field": "getCustomerOrders",
        "source":  $utils.toJson($context.source)
    }
}
```
* Get a single customer's orders
```
query {
  getCustomer(id: "ANTON") {
   	ContactName
    getCustomerOrders { # lambda invocation
      __typename
      OrderID
    }
  }
}
```

## Level 3 - Schema

* Add Employee Type in the schema
* Add a resolver.
* An Employee is connected to each order



## Level 4

## Boss
 * Add another datasource for appsync (lambda function)
 


## References

https://docs.aws.amazon.com/appsync/latest/devguide/quickstart-write-queries.html

https://docs.aws.amazon.com/appsync/latest/devguide/tutorial-lambda-resolvers.html

https://docs.aws.amazon.com/appsync/latest/devguide/designing-your-schema.html

https://docs.aws.amazon.com/appsync/latest/devguide/resolver-context-reference.html

https://docs.aws.amazon.com/appsync/latest/devguide/building-a-client-app-javascript.html

## Datasources
https://github.com/tmcnab/northwind-mongo

https://www.csvjson.com/csv2json
