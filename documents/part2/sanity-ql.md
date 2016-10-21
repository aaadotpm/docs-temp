# Sanity Query Language


## Document store

Sanity stores all documents in [Gradient](https://github.com/sanity-io/gradient), our custom built, open source cloud database. Specifically, Gradient is a document-oriented database abstraction layer tailored for web applications. It's built on [PostgreSQL](https://www.postgresql.org/) and [Elasticsearch](http://www.elasticsearch.org), both of which are also open source software.


## How to access your data

All operations, be they read, create, update, patch or delete, are sent through the `api.sanity.io/v1/data/` endpoint. If you're on [Node](http://nodejs.org), you may prefer to use the [Node Sanity Client](https://github.com/sanity-io/client) instead of rolling your own HTTP requests. We're also planning a PHP Sanity Client.

However you choose to access Gradient, your queries must conform to the Sanity Query Language, a simple, compact and powerful syntax for querying and manipulating your documents.


## Query Language walkthrough

A typical query looks like this:

`mikesbikes.api.sanity.io/v1/data/q/bikerental?query=customer[mobile == '555-12345']`

Let's break this down

| URL component | Description |
| --- | --- |
| mikesbikes| project id |
| api.sanity.io| sanity api host |
| /v1/data/q/| data query path |
| bikerental| dataset |
| customer| document type |
| [mobile == '55512345'] | filter by attribute |

The above request is a query for all customers, filtered by a specific value for mobile.


### Selection filtering

```
bike[price < 3] # all bikes with price less than 3
bike[price > 3] # greater than
bike[price >= 3] # greater than or equal
bike[price <= 3] # less than or equal
bike[model == null] # null value
bike[brand == 'Surly'] # all bikes of the Surly brand
bike[category != 'Fat'] # all bikes non-phat
bike[price < 4 && category == 'Tandem'] # and
bike[price < 4 || brand == 'Trek'] //  # or
bike[(price < 3 && category == 'Tandem') || category == 'Cargo'] # cheap tandems or any cargo bike
bike[problems include 'worn tread'] # all bikes with worn tires
bike[problems include ['worn tread', 'prone to snakebite']] # multiple named problems
rental[bike[category == 'Cargo']] # nested values (all rentals of cargo bikes)
```


### Attribute projection

Use `{...}` either around, or at the end of, a query to specify which fields are returned.

```
# Get all bikes, but not complete objects, just model and price attributes
bike[]{price, model}
```

```
# Alias (rename) a return field
bike[]{price, 'displayName': model}
```

```
# Alias the outer return field
{
  'expensiveBikes': bike[price > 4]{price, 'displayName': model}
}
```


### Aggregate functions

```
count(listOfAnything)
average(listOfNumbers)
min(listOfNumbers)
max(listOfNumbers)
```

E.g.

```
bike[count(problems) > 3] # bikes in desperate need of love
average([7, -1, 2, 9, 3]) # 5
min([7, -1, 2, 9, 3]) # -1
max([7, -1, 2, 9, 3]) # 9
```


### Other nice tricks

```
bike[_id == 'a1b2c3'] # bikes by internal attribute
bike[order: price asc] # bikes ordered by price low-high
first(bike[]) # first bike
last(bike[]) # last bike registered
bike[!available] # unary operator not
bike[defined(model)] # model value set
bike[undefined(model)] # model value not set
bike[problems == [] || undefined(problems)] # no hay problema
{"bikesByPrice": group(bike[], price)} # group bikes by price
(customer, bike)[name == 'Gary Fisher'] # multiple types by same filter
```


## Joins

```
# Cartesian join: All possible customer/bike combinations
customer[]{'allBikes': bike[]}
```

```
# Simple join
customer[]{'rentals': rental[customer._id == ^._id]}
```

**Note:** The caret symbol `^` makes the attached attribute belong to the "above" result, in this case `customer`.

```
# Reference join: Customers and the ids of their 2016 rentals
customer[]{
	'rentalIds': rentals[
		startTime > '2015-12-31',
		startTime < '2017-01-01'
	]{
		_id
	}
}
```

**Note:** Get customers and their rentals started before 2016-01-01 and only return the rental id (not the whole rental object).


#### Attribute projection and join

```
{
  'variousStuff': {
    'cheapCommuterBikesModels': bike[
      price < 3,
      category == 'Commuter'
    ]{
      brand,
      model
    },
    'happyCustomerNames': customer[
      rentals[
        count(customer == ^) > 3
      ]
    ]{
      name
    }
  }
}
```


## Cross-path query

```
bikerental.bike in rental/bikes/*[]{
	"stolenBikes": in missing/bikes/*[_id == ^_id]
}
```


## Now what?

Start using [Sanity Client](https://github.com/sanity-io/client) to perform queries!
