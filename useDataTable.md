# useDataTable

## INTRO

To build a dataTable against graphql in a project you need to

- Find a 3rd party or build your own grid system
- Query for rows
- Setup and manage pagination
- Add Filter boxes and connect them into your query
- Add server side sorting
- Handle row clicks
- Implement custom columns
- Implement Inserts and make sure they update your table
- Implement Deletes
- Implement updating rows
- Probably implement a confirmation dialogue to confirm deletion
- Probably implement a seamless modal system to help with inserts, or navigation to a new screen
- Probably implement a dialog or a new screen to show your update form

That is just for the front end.  If you have to do server-side as well you have a lot more.  Fortunately hasura gives us all the server side support we need with pagination, sorting, and filtering.

useDataTable gives you a composable hook that marries multiple libraries together to give you a rapid experience for building dataTables connected to a graphql backend

## Libraries

- graphql & react-graphql - To do the queries and mutations
- [PrimeReact](https://www.primefaces.org/primereact/showcase) - has a slick DataTable component, confirmation dialogues, and modals, and much more. See: https://www.primefaces.org/primereact/showcase/#/datatable
- [Tailwindcss](https://tailwindcss.com) - to handle styling
- [ReactHookForm](https://react-hook-form.com/api) - for managing the form, state, error, dirty, etc for updates and inserts
- [Jotai](https://jotai.pmnd.rs/docs/introduction) - To connect react-graphql and useDataTable together so you keep full composability


## Composing your DataTable
useDataTable will give you these props back:
```ts
dataTableProps: Record<string, any>;   //Always spread onto your <DataTable>
columns: ColumnFromConfig[];           //Children of <DataTable>
paginationProps: DataTablePaginationProps<T>; //Spread onto <DataTable>
orderByProps: UseDataTableOrderby;     //Spread onto <DataTable>
filterProps: UseDataTableWhere;        //Spread onto <DataTable>
toolbar: ReactNode;                    //Placed anywhere outside of <DataTable>
insertButton: ReactNode;               //Placed anywhere outside of <DataTable>
actionColumn: ReactNode;               //Child of <DataTable>
dialogs: ReactNode[];                  //Placed anywhere outside of <DataTable> for insert/update/delete
selectedRow: any;                      //The currently selected row
setSelectedRow: (row?: any) => void;   //Set currently selected row externaly
hideUpsertDialog: () => void;          //Call this function to hide the upsertDialog
hideDeleteDialog: () => void;          //Call this function to hide the deleteDialog
```


### Basic Composition
```tsx
import {DataTable} from 'primereact/datatable';
import {useReactGraphql} from '@tesseractcollective/react-graphql'
import {useDataTable} from '@tesseractcollective/react-graphql-ui'

function DataTableBasic() {
  const myWombatZooState = useReactGraphql(
    HasuraConfig.wombat
  ).useInfiniteQueryMany({})

  const { dataTableProps, columns } = useDataTable({
    gqlConfig: HasuraConfig.wombat,
    queryManyState: myWombatZooState,
  })

  return (
    <div>
      <DataTable {...dataTableProps}>
        {columns.map((col) => col.component)}
      </DataTable>
    </div>
  )
}
```

### Pagination, Filter, Order by
```tsx
  const { dataTableProps, columns, filterProps, paginationProps } = useDataTable({
      gqlConfig: HasuraConfig.claimAmount,
      queryManyState: queryState,
      queryArgsAtom: exampleQueryArgsAtom4,
      filterable: true,
      sortable: true
  })

  return (
    <div>
      <DataTable {...dataTableProps} {...filterProps} {...paginationProps}>
        {columns.map((col) => col.component)}
      </DataTable>
    </div>
  )
```

### Insert, Delete, Update
```tsx
  const  {
    dataTableProps,
    columns,
    toolbar,
    actionColumn,
    dialogs,
  }= useDataTable({
      gqlConfig: HasuraConfig.claimAmount,
      queryManyState: queryState,
      delete: true,
      insert: 'toolBarRight',
      update: true,
  })

  return (
    <div>      
      {toolbar}
      <DataTable {...dataTableProps}>
        {columns.map((col) => col.component)}
        {actionColumn}
      </DataTable>
      {dialogs.map(d=> d)}
    </div>
  )
```


### Insert button instead of Toolbar
```tsx
  const  {
   ...,
   insertButton
  }= useDataTable({
      gqlConfig: HasuraConfig.claimAmount,
      queryManyState: queryState,
      insert: 'manual',
  })

  return (
    <div>      
      <Toolbar right={insertButton}/>
      <DataTable {...dataTableProps}>
        {columns.map((col) => col.component)}
      </DataTable>
    </div>
  )
```

### Omitting/sorting/custom/other to your columns
```tsx
  const { dataTableProps, columns } = useDataTable({
      gqlConfig: HasuraConfig.claimAmount,
      queryManyState: queryState,      
  });

  //Columns have this shape: {key, component}
  //Use them, ignore them, filter, splice, add, remove, order, whatever you want.
  const filteredColumns = columns.filter((col) =>
    ['id', 'displayName'].includes(col.key)
  );

  //Insert a custom column at an index
  filteredColumns.splice(2, 0, {
    key: 'difference',
    component: (
      <Column key="difference" field="difference" body={() => <div>777</div>} />
    ),
  })

  return (
    <div>
      <DataTable {...dataTableProps} {...filterProps} {...paginationProps}>
        {filteredColumns.map((col) => col.component)}
      </DataTable>
    </div>
  )
```

### To add additional props to a column we give you, use columnProps arg, documented below


## Required Args

### `gqlConfig: HasuraDataConfig;`

See [HasuraDataConfig](https://github.com/tesseractcollective/react-graphql/blob/master/src/types/hasuraConfig/index.ts) for the shape

### `queryManyState: IUseInfiniteQueryManyResults<T>;`

The queryManyState returned from ReactGraphql.useInfiniteQueryMany.

This allows us to monitor and use the data from your queries and to update your query args when you tell us too

### `pageSize?: number;`

How many items do you want per page.

### `insert?: 'toolBarLeft' | 'toolBarRight' | 'manual';`

If you want us to help with inserts then tell us where to put it on the toolbar, or that you want it manually.

REQUIRES: Place the `toolbar` or the `insertButton` prop we return.
REQUIRES: Place `dialogues` in your render

### `update?: boolean;`

Do you want to show the update button in the actionColumn. 

REQUIRES: Place `dialogues` in your render

### `delete?: boolean;`

Do you want to show the delete button in the actionColumn

REQUIRES: Place `dialogues` in your render

### `upsert?: { upsertGqlConfig?: HasuraDataConfig; upsertFlexFormProps?: Partial<IFlexFormProps>; };`

Configure the FlexForm used by updates and inserts. Changes here will impact both forms since they are both shown in the same modal.

- `upsertGqlConfig?` - Only needed if you want to use a different resource for your form than you do your dataTable.  This is always the case when using a `View` in hasura/postgres (non-materialized/default).
- `upsertFlexFormProps?` - Pass in any props you would to `<FlexForm>` here and we'll pass them through and help everything play nice together.

> What if I want the forms to be different?!

Use this for your updates, and then place your own insertButton

### `onRowClick?: (path: string) => void;`

NEW in `RGUI@0.2.0` and above.

Invoke the given function whenever a row is clicked.  Will pass in a `path:string` that can be used to easily navigate to a new page.  This is how we build the path:

```tsx
function updateSelectRowInQuery(primaryKeyValue: string, onRowClick?: (path: string) => void, primaryKeyName = 'id') {
  const currentParams = queryString.parse(window.location.search);
  let nextParams: { [key: string]: any } = {};
  nextParams = { ...currentParams, [primaryKeyName]: primaryKeyValue };
  const queryStr = queryString.stringify(nextParams);
  if (onRowClick) {
    onRowClick(window.location.pathname + '?' + queryStr);
  }
}
```

To navigate somewhere you would do something like this in React-router-5:

```tsx
const history = useHistory();
...

onRowClick={(path)=> history.push(path)}

```


### `queryArgsAtom?: PrimitiveAtom<UseDataTableQueryArgsAtom>;`

If you want sorting or filtering we need to connect useDataTable back to useInfiniteQueryMany. This is how we do it.

```ts
const queryArgsAtom = atom<UseDataTableQueryArgsAtom>({
  pause: true,
});

function myComponent() {
  const [queryArgs] = useAtom(queryArgsAtom)

  const queryState = useReactGraphql(
    HasuraConfig.userProfile
  ).useInfiniteQueryMany({
    ...queryArgs,
  });

  ... = useDataTable({
    gqlConfig: HasuraConfig.userProfile,
    queryManyState: queryState,
    pageSize: 5,
    queryArgsAtom,
  })
}
```

### `sortable?: boolean;`

Do you want sorting? If the user clicks a column header we'll use the queryArgsAtom to update your infiniteQueryMany with an order by.

```tsx
const { dataTableProps, columns, orderByProps } = useDataTable({
    gqlConfig: HasuraConfig.claimAmount,
    queryManyState: queryState,
    queryArgsAtom: exampleQueryArgsAtom3,
    sortable: true,
  })

  return (
    <div>
      <DataTable {...dataTableProps} {...orderByProps}>
        {columns.map((col) => col.component)}
      </DataTable>
    </div>
  )
```

#### Excluding columns from sort:

To exclude a column from sort you would override sortable on that column by passing in columnProps using the name of the column as the key:

```
const { dataTableProps, columns, orderByProps } = useDataTable({
    gqlConfig: HasuraConfig.claimAmount,
    queryManyState: queryState,
    queryArgsAtom: exampleQueryArgsAtom3,
    sortable: true,
    columnProps: { bio: { sortable: false } }
  })
```


### `columnProps?: Record<string, ColumnPropsForEquality | ColumnPropsForString>;`

Pass through props onto the `PrimeReact.Column`

`<PrimeReact.Column {...columnProps[fieldName]/>`

See: https://www.primefaces.org/primereact/showcase/#/datatable

### `filterable?: boolean;`

Should we filter?

Example: 
```tsx
const { dataTableProps, columns, filterProps, paginationProps } =
    useDataTable({
      gqlConfig: HasuraConfig.claimAmount,
      queryManyState: queryState,
      queryArgsAtom: exampleQueryArgsAtom4,
      filterable: true,
    })

  return (
    <div>
      <DataTable {...dataTableProps} {...filterProps} {...paginationProps}>
        {columns.map((col) => col.component)}
      </DataTable>
    </div>
  )
```

Prime React defines the following props that influence filtering that we can pass through:

|propName|type|defaultValue|description|
|---|---|---|---|
|filter	              |boolean|	false|	Defines if a column can be filtered.|
|filterMatchMode	      |string|	null|	Defines filterMatchMode; "startsWith", "contains", "endsWidth", "equals", "notEquals", "in", "lt", "lte", "gt", "gte" and "custom".|
|filterType	          |string|	text|	Type of the filter input field.|
|filterPlaceholder	    |string|	null|	Defines placeholder of the input fields.|
|filterMaxlength	      |number|	null|	Specifies the maximum number of characters allowed in the filter element.|
|filterElement	        |object|	null|	Element for custom filtering.|
|filterFunction	      |function|	null|	Custom filter function.|
|excludeGlobalFilter	  |boolean|	false|	Whether to exclude from global filtering or not.|
|filterHeaderStyle	    |object|	null|	Inline style of the filter header.|
|filterHeaderClassName	|string|	null|	Style class of the filter header.|


The main support we're providing is attaching filter to the columns if you set `filterable:true`.

As secondary support we mapped `filterMatchMode` to Hasura operations based on the scalar datatype of the field.  Basically we give you typescript types so that you don't do something with PrimeReact that Hasura doesn't support, like doing `startsWith` against a number.

#### USE CASE: change match mode

By default, the filterMatchMode is `startsWith`, so if you want contains, ends with, equality, or other comparison types you'll do it via the columnProps

```
useDataTable({
      ...
      filterable: true,
      columnProps: {
        displayName: {
          gqlScalarType: 'String', <--- This ensures you don't pick anything illegal in hasura for filterMatchMode
          filterMatchMode: 'contains' <--- Set this to anything defined above, to change how this filter behaves
        },
      },
    })
```

#### USE CASE: filter on only some fields
If you only want the filter on certain fields you can manually set it, or exclude it.

```
Opt-in:

useDataTable({
  filterable: false,
  columnProps: {
   displayName: {
      filter: true
    },
  },
});


```

```
Opt-out:

useDataTable({
  filterable: true,
  columnProps: {
   displayName: {
      filter: false
    },
  },
});
```

#### USE CASE: Custom filter component

```
useDataTable({
  filterable: true,
  columnProps: {
   displayName: {
      filterElement: CUSTOM_COMPONENT
    },
  },
});
```

### `setIdInQueryOnClick?: boolean;`

DEPRECATED in `RGUI@0.2.0` and above. Replaced with `onRowClick`

set this to true if you want us to update the queryParams on rowClick

IE - `www.myawesomesite.com` becomes `www.myawesomesite.com?pk=row-id-for-last-clicked-row` so you can do navigate, show a modal, whatever

### `primaryKeyName?: string;`

DEPRECATED in `RGUI@0.2.0` and above.

Override the primary key label used during `setIdInQueryOnClick`

IE - change `pk` into `id` or `pk` into `zorgons-chosen`.  Default is id.
