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

<details>
  <summary>AWS Resources</summary>
  
  * [Quick Start](https://docs.aws.amazon.com/appsync/latest/devguide/quickstart-write-queries.html)
  * [Configure Resolvers](https://docs.aws.amazon.com/appsync/latest/devguide/configuring-resolvers.html)
  * [Lambda Resolvers](https://docs.aws.amazon.com/appsync/latest/devguide/tutorial-lambda-resolvers.html)
  
</details>


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
[AWS Resource - Schema design](https://docs.aws.amazon.com/appsync/latest/devguide/designing-your-schema.html)

* Add Employee type in the schema.
* Add a resolver.
* An Employee type is connected to an Order type.
* Construct a query that will show the employee's name on a customer's order.

<details>
  <summary>Example of employee data</summary>

```
{
    EmployeeID: 9,
    LastName: "Dodsworth",
    FirstName: "Anne",
    Title: "Sales Representative",
    TitleOfCourtesy: "Ms.",
    BirthDate: "1966-01-27 00:00:00.000",
    HireDate: "1994-11-15 00:00:00.000",
    Address: "7 Houndstooth Rd.",
    City: "London",
    Region: "NULL",
    PostalCode: "WG2 7LT",
    Country: "UK",
    HomePhone: "(71) 555-4444",
    Extension: 452,
    Photo:"",
    Notes:
      "Anne has a BA degree in English from St. Lawrence College.  She is fluent in French and German.",
    ReportsTo: 5,
    PhotoPath: "http://accweb/emmployees/davolio.bmp"
}
```

</details>


## Level 4 - Mutations
WIP 

* Add a mutation type.
* Resolve to lambda function method `updateOrder`


```
type Mutation {
    updateOrder(order: Order!): Order
}

```


## Boss
 
 * Add another data source for AppSync (lambda function).
 * Extend a type in the schema to use the new data source by attaching a resolver.
 * Build a query and show us customer data with new information!
 
 <details>
  <summary>Suggested Data Sources</summary>
  
  * [JSON Currency Exchange](http://free.currencyconverterapi.com/api/v5/convert?q=USD_EUR&compact=y)
  * [Google Trends](https://trends.google.com/trends/)
</details>
 
 

 <details>
  <summary>Additional Resources</summary>
  
  * [Resolver Context Reference](https://docs.aws.amazon.com/appsync/latest/devguide/resolver-context-reference.html)
  * [Building a client](https://docs.aws.amazon.com/appsync/latest/devguide/building-a-client-app-javascript.html)
  * [Common Mistakes](https://docs.aws.amazon.com/appsync/latest/devguide/troubleshooting-and-common-mistakes.html)
</details>


## Data Sources
https://github.com/tmcnab/northwind-mongo

https://www.csvjson.com/csv2json
