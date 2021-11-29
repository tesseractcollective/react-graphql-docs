## Setup & run graphql code generator

You want to use typescript, typescript-operations, and introspection plugins.
We also use typescript-urql.
We generate all our fragments, scalars, and types to a resourceApi file.
We generate our schema to a separate file.
You can customize this how you want.

example config:
```js
// codegen.js
const env = process.argv[4] || 'dev';
const config = require(`../......`);

module.exports = {
  schema: [
    {
      [`${config.HASURA_GRAPHQL_ENDPOINT}/v1/graphql`]: {
        headers: {
          'x-hasura-admin-secret': config.HASURA_GRAPHQL_ADMIN_SECRET
        }
      }
    }
  ],
  documents: ['src/**/*.graphql'],
  overwrite: true,
  generates: {
    'src/resources/config/generated/resourceApi/index.ts': {
      plugins: [
        'typescript',
        'typescript-operations',
        'typescript-urql',
      ],
    },
    'src/resources/config/generated/graphql.schema.json': {
      plugins: ['introspection'],
    },
  },
};
```

## graphql-code-generator docs

[graphql code generator](https://www.graphql-code-generator.com/docs/getting-started)

## Set up config file
IE - `hasuraConfig.ts`

`import schema from './generated/graphql.schema.json'; //comes from graphql-code-generator`



### Set up the basics:
```
const HasuraConfig = buildHasuraConfig(
  schema,
  {
    claim: {
      typename: 'claim',
      primaryKey: ['id'],
      fieldFragment: ClaimFieldsFragmentDoc,
    }
  }
```

> NAMING CONVENTIONS:  We highly recommend your configs match your typenames wherever possible.  It will make things easier to remember and allow us to auto-detect some things for you.

> NAMING CONVENTIONS for Lookup Tables: If you're going to be using any tables to lookup values/ids for another table then we recommend the pattern of typeNameSelect.  We key off of this and add relationships for you.

### Other options

from react-graphql/src/types/hasuraConfig/index.d.ts

```ts
export interface HasuraDataConfig {
    typename: string;
    primaryKey: string[];
    fieldFragment: DocumentNode;
    schema?: GraphQLSchema;
    primaryKeyRequiredOnCreate?: boolean;
    instanceId?: string;
    relationshipMeta?: {
        labelField?: string;
        defaultWhere?: any;
    };
    excludeAggregate?: any;
    fields?: {
        fieldSimpleMap: {
            [key: string]: IFieldOutputType;
        };
        fieldTypeMap: {
            [key: string]: GraphQLOutputType;
        };
    };
    jsonb?: {
        columnName: string;
    };
    overrides?: {
        operationNames?: {
            query_many?: string;
            query_aggregate?: string;
            query_by_pk?: string;
            delete_by_pk?: string;
            insert_many?: string;
            insert_one?: string;
            update_by_pk?: string;
        };
        onConflict?: {
            insert?: string;
            insert_args?: string;
        };
        fieldFragments?: {
            query_many?: DocumentNode;
            query_aggregate?: DocumentNode;
            query_by_pk?: DocumentNode;
            delete_by_pk?: DocumentNode;
            insert?: DocumentNode;
            insert_core_one?: DocumentNode;
            update_core?: DocumentNode;
        };
    };
}
```

## Add metadata for relationship support

By adding metadata information from hasura we are able to do a lot more for you accross your relationships.

To add metadata we need to get it in json format and pass it into our `buildHasuraConfig` call:

```ts
const HasuraConfig = buildHasuraConfig(
  schema,
  {
    typename: 'claim',
    primaryKey: ['id'],
    fieldFragment: ClaimFieldsFragmentDoc,
  },
  metadata
```

How we get our metadata:

* We already have our hasura-cli setup so all we have to do is call `hasura metadata export`.  This updates all of our .yaml files for our tables from hasura.
* We then use a tool called `any-json` to smash all our .yaml files into a single json file we can import.  Our package.json script looks like this: 
`pnpx any-json combine ../hasura/metadata/databases/default/tables/*.yaml --out=./src/resources/config/generated/metadata.json`
* We have our hasura folder as a sibling to our UI folder.  By running this from our UI folder we're able to generate the metadata right next to where we generate our schema, and our typescript keeping it all in one place to reference.

> IF you don't have your hasura-cli setup, follow their docs, they're good.

