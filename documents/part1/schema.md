# Schema

The Schema has two concerns:

1. It describes the shape of your data.
2. It provides constraints and hints which a UI can use when presenting your data.

A schema has a name and an array of types. Each type has a name and multiple fields. Each field has a name and a type.

## Types

There are primitive types, and there are composite types.

### Primitive Types

Primitive types are the "raw" building blocks of Sanity data. Except for date and reference, they can all be resolved using regular javascript type checking.

- string
- number
- boolean
- object
- array
- date (iso8601)
- reference

## Additional keys

A field can also have the following keys:

- **to** - Which type the object being referred to is. Required, and only allowed, when `type` has value `reference`.
- **of** - A list of which types are permitted in an array. Required, and only allowed, when `type` has value `array`.
- **anyOne** - A list of which types which may be allocated to the type field. E.g. bikeType == bmx | trike.
- **required** - Set to `true` if this field must contain data in order to allow document save. Defaults to `false`.


### Composite Types

Composite types are built from multiple primitive types and come in two flavors:

1. Bundled types. These are commonly used types which are provided with Sanity as convenience.
2. Custom types. Your own, hand crafted types.


## An example

Your name is Mike and you run a bike rental. Below we'll model customer, bike, accessory and rental. Here, have a diagram:

```
Bike <-- Rental --> Customer
           |
           v
           v
        Accessory
```

```js
{
  name: 'mikesbikes',
  types: [
    {
      name: 'bike',
      type: 'object',
      fields: [
        {
          name: 'brand',
          type: 'string', // primitive type string
          title: 'Brand'
        },
        {
          name: 'model',
          type: 'string', // primitive type string
          title: 'Model'
        },
        {
          name: 'available',
          type: 'boolean', // primitive type boolean
          title: 'Available for rent'
        },
        {
          name: 'price',
          type: 'number', // primitive type number
          title: 'Price per hour'
        }
      ]
    },
    {
      name: 'customer',
      type: 'object',
      fields: [
        {
          name: 'name',
          type: 'string', // primitive type string
          title: 'Full name'
        },
        {
          name: 'mobile',
          type: 'phone', // composite (bundled) type
          title: 'Mobile phone number'
        }
      ]
    },
    {
      name: 'rental',
      type: 'object',
      fields: [
        {
          name: 'startTime',
          type: 'date', // primitive type date
          title: 'Start time'
        },
        {
          name: 'endTime',
          type: 'date', // primitive type date
          title: 'End time'
        },
        {
          name: 'bike',
          type: 'reference', // primitive type, referencing bike
          to: {type: 'bike'}
        },
        {
          name: 'customer',
          type: 'reference', // primitive type, referencing customer
          to: {type: 'customer'}
        },
        {
          name: 'accessories',
          type: 'array', // primitive type array, containing multiple references to accessory
          of: [{type: 'reference', to: {type: 'accessory'}}]
        }
      ]
    },
    {
      name: 'accessory',
      type: 'object',
      fields: [
        {
          name: 'name',
          type: 'string', // primitive type string
          title: 'Accessory name'
        },
        {
          name: 'price',
          type: 'number', // primitive type number
          title: 'Price per hour'
        }
      ]
    }
  ]
}
```
