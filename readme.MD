# λ# - GraphQL with AWS's AppSync

Congratulations! Welcome to Lambda Sharp, LLC! A **fictitious** company that you just started working at! [AppSync](https://aws.amazon.com/appsync/) will be the focus of your work.

> SUBJECT: Data project that must be completed!
>
> FROM: Dave aka DBA <mailto:<dba@lambdasharp.io>>
>
> We just acquirered **Northwind Traders**! We need an API ASAP and your the only developer available right now. I've created a partial GraphQL schema and a (not so pretty) mock datasource lambda function to start querying the data. We need this in production **TONIGHT!**
>
> Thanks,
>
> Dave aka "The DBA"

![Northwind Traders database schema](http://archive.oreilly.com/oreillyschool/courses/dba3/images/FinalProject/Northwind.png)

## Level 0 - Setup

- Create a new lambda function from this repo. [northwindTradersMockDb.js](src/northwindTradersMockDb.js)
  1. Navigate to your [Lambda Console](console.aws.amazon.com/lambda) and click **Create function**.
  2. Keep the default **Author from scratch** selected.
  3. Name your function.
  4. Use the **Runtime** (NodeJs 10.x).
  5. Set a timeout of 10 seconds.
  6. Select the role you created from the **Existing Role** dropdown.
  7. Click **Create function** to create function
- Create a **new api** in AppSync
  1. Click **Build from Scratch** option with the name **HackathonApp**.
  2. Click **Edit Schema**. Copy/paste [schema.graphql](src/schema.graphql) contents in to the **Schema** window. Save.
- Create new **Data Source** in AppSync
  1. Data source name: **NorthwindTraders**.
  2. Data source type: **AWS Lambda Function**.
  3. Region: sames as the lambda function created previously (likely US-EAST-1)
  4. Data source: Reference the lambda function created previously.
  5. Use **New role** option.

Helpful hint: Be sure you can access the CloudWatch trail events for this lambda function.

## Level 1 - Query

<details>
  <summary>AWS Resources</summary>
  
  * [Quick Start](https://docs.aws.amazon.com/appsync/latest/devguide/quickstart-write-queries.html)
  * [Configure Resolvers](https://docs.aws.amazon.com/appsync/latest/devguide/configuring-resolvers.html)
  * [Lambda Resolvers](https://docs.aws.amazon.com/appsync/latest/devguide/tutorial-lambda-resolvers.html)
  
</details>

- Add a resolver to get all customers. In Schema, right pane (no scroll bar but it will scroll) > `Query` > `allCustomers`. Use the lambda function `NorthwindTraders` > Click Attach. Add the request mapping template.

```json
{
  "version": "2017-02-28",
  "operation": "Invoke",
  "payload": {
    "field": "allCustomers"
  }
}
```

- Add a resolver to get a single customer. In Schema, right pane (no scroll bar but it will scroll) > `Query` > `getCustomer`. Use the lambda function `NorthwindTraders` > Click Attach. Add the request mapping template.

```json
{
  "version": "2017-02-28",
  "operation": "Invoke",
  "payload": {
    "field": "getCustomer",
    "arguments": $util.toJson($context.arguments)
  }
}
```

- In `Queries`, query for customers.

```graphql
query {
  allCustomers {
    CustomerID
    ContactName
  }
}
```

## Level 2 - Query Related data

- Get a single customer order using `getCustomer`

```graphql
query {
  getCustomer(id: "ADD A CUSTOMER ID HERE") {
    __typename
    ContactName
    ContactTitle
    Country
  }
}
```

- Add a resolver to get orders of a single customer. In Schema, right pane (no scroll bar but it will scroll) > `Customer` > `getCustomerOrders`. Use the lambda function `NorthwindTraders` > Click Attach. Add the request mapping template.

```json
{
  "version": "2017-02-28",
  "operation": "Invoke",
  "payload": {
    "field": "getCustomerOrders",
    "source": $utils.toJson($context.source)
  }
}
```

- Get a single customer's orders

```graphql
query {
  getCustomer(id: "ANTON") {
    ContactName
    getCustomerOrders {
      # lambda invocation
      __typename
      OrderID
    }
  }
}
```

## Level 3 - Schema

[AWS Resource - Schema design](https://docs.aws.amazon.com/appsync/latest/devguide/designing-your-schema.html)

Based on the previous levels, build the following:

- Add Employee type in the schema.
- Add a resolver. data source field `getEmployee` can be resolved.
- An Employee type is connected to an Order type.
- Construct a query that will show the employee's name on a customer's order.

<details>
  <summary>Example of employee data</summary>

```json
{
  "EmployeeID": 9,
  "LastName": "Dodsworth",
  "FirstName": "Anne",
  "Title": "Sales Representative",
  "TitleOfCourtesy": "Ms.",
  "BirthDate": "1966-01-27 00:00:00.000",
  "HireDate": "1994-11-15 00:00:00.000",
  "Address": "7 Houndstooth Rd.",
  "City": "London",
  "Region": "NULL",
  "PostalCode": "WG2 7LT",
  "Country": "UK",
  "HomePhone": "(71) 555-4444",
  "Extension": 452,
  "Photo": "",
  "Notes": "Anne has a BA degree in English from St. Lawrence College.  She is fluent in French and German.",
  "ReportsTo": 5,
  "PhotoPath": "http://accweb/emmployees/davolio.bmp"
}
```

</details>

## Level 4 - Mutations

 <details>
  <summary>Helpful Resources</summary>
  * [AWS Resource](https://docs.aws.amazon.com/appsync/latest/devguide/designing-your-schema.html#adding-a-mutation-type)
  * [Quick start writing queries](https://docs.aws.amazon.com/appsync/latest/devguide/quickstart-write-queries.html)
</details>
 
* Add a mutation type for updating an order.
* Resolve to lambda function method `updateOrder`
* Write a mutation in the query window to update the `ShippedDate` field.

```graphql
type Mutation {
  updateOrder(OrderID: Int, ShippedDate: String): Order
}
```

Add `mutation: Mutation` to the type `Schema`.  Construct an update order in the `Queries`.  This resource is helpful: [AWS Run Queries and Mutations](https://docs.aws.amazon.com/appsync/latest/devguide/quickstart-write-queries.html)

You know you did it right when you send a new **ShippedDate** and returns with the order object as expected.

## Boss

- Add another data source for AppSync (DynamoDB is a good option).
- Extend a type in the schema to use the new data source.
- Build a query and show us customer data with new information!

 <details>
  <summary>Suggested Data Sources</summary>
  
  * [JSON Currency Exchange](https://free.currencyconverterapi.com/)
  * [Google Trends](https://trends.google.com/trends/)
</details>

 <details>
  <summary>Additional Resources</summary>
  
  * [Resolver Context Reference](https://docs.aws.amazon.com/appsync/latest/devguide/resolver-context-reference.html)
  * [Building a client](https://docs.aws.amazon.com/appsync/latest/devguide/building-a-client-app-javascript.html)
  * [Common Mistakes](https://docs.aws.amazon.com/appsync/latest/devguide/troubleshooting-and-common-mistakes.html)
</details>

## Demo Time!

Show us your **Schema** and run some queries in front of us!

## Data Sources

https://github.com/tmcnab/northwind-mongo

https://www.csvjson.com/csv2json
