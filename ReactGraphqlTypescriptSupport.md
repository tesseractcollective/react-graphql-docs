# How to do reactGraphql typing

## Pattern for the fragmentType  

Pattern: `hasuraConfig.RESOURCE.fragmentType` 

- Usually this is `TablePascalCase + 'FieldFragment'`
- `ClaimFieldsFragment`
- `ClaimAmountsFieldsFragment`
- `PaymentsFieldsFragment`

## Applying the type to a mutation

The order needed by the mutations is: <TResultData, TVariables, TItem>

- TResultData = the shape of the resultItem, this always matches the fragment on the config and follows the above pattern
- TVariables = The shape of the variables, varies per query and type
- TItem = The shape of the item sent on the mutation

### useInsert

- TResultData = the shape of the resultItem, this always matches the fragment on the config and follows the above pattern
- TVariables: we can't know what TVariables will look like, because using them on an insert is non-standard, they're doing something special in the fragment
- TItem: Always TableNamePascalCase + _Insert_Input

```
const insertStateAmounts = api.useInsert<ClaimAmountFieldsFragment, {}, ClaimAmount_Insert_Input>({})
const insertStateAmounts = api.useInsert<ClaimAmountFieldsFragment, {id: string}, ClaimAmount_Insert_Input>({
    initialVariables: {id: userId}
  })
```

### useDelete

- TResultData = the shape of the resultItem, this always matches the fragment on the config and follows the above pattern
- TVariables: TableNamePascalCase + _Pk_Columns_Input
- TItem: Not used or passed in here, leave it off

 ```
 const deleteStateAmounts = api.useDelete<ClaimAmountFieldsFragment, ClaimAmount_Pk_Columns_Input>({})
 ```

### useUpdate
- TResultData = the shape of the resultItem, this always matches the fragment on the config and follows the above pattern
- TVariables: TableNamePascalCase + _Pk_Columns_Input
- TItem: Always TableNamePascalCase + _Set_Input

```
const updateStateAmounts = api.useUpdate<ClaimAmountFieldsFragment, ClaimAmount_Pk_Columns_Input, ClaimAmount_Set_Input>({})
```


## Applying the type to a query


The order needed by the query is: <TData>

### useQuery
```
const queryState = api.useQuery<ClaimAmountFieldsFragment>({})
```

### useInfiniteQueryMany
```
const queryState = api.useInfiniteQueryMany<ClaimAmountFieldsFragment>({})
```
