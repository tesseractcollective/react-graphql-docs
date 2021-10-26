# FlexForm

## INTRO

To build mutation in a project using graphql for it's data layer you need to:
* Build your UI and components
* Save their state on the screen
* Setup a graphql query
* execute the mutation
* Manage errors and success

FlexForm gives you a composable component that marries multiple libraries together to give you a rapid experience for building UIs connected to a graphql backend.

Did we say it was composable, because it is. If you want 80% of what FlexForm does, you can probably override whatever 20% you don't like.

We try to use sane defaults as much as possible, but if you don't like them, you have full control.

Still think FlexForm is too much?  Check out FlexFormLocal for even more control.


## Libraries
* react-hook-form - for managing the form, state, error, dirty, etc
* graphql & react-graphql - To insert and update your data
* prime react - To show the components
* tailwindcss - to handle styling

## By Use-case

##### USE CASE: I want a basic register form with 4 fields

> You probably want a single column grid

```
  <FlexForm config={HasuraConfig.userProfile}
            grid={ { 
              columnCount: 1
            } }
            isNew
            onSuccess={(data:any)=> {
              //Navigate to your next page or show a modal
            }}>

```

##### USE CASE: My form has a field that needs to get its value from another table

> As long as your config is setup fully and correctly this should just work

> You might need to setup your metadata, or pass in a config specifically if you need a where clause on that relationship


## Required Props
### `config: HasuraDataConfig (react-graphql)`
-> hasura
  -> graphql
    -> typename
    -> primary key
    -> fragments

-> graphql code generator

## Optional Props

### `data : {key: value}`
Pass in an object where the keys match the shape of your data
##### USE CASE: I have data already when the component is loaded, and I want it populate it up front

### `fields: string`
Pass in an array of field names 
##### USE CASE:  I have a fragment with a lot more fields than I want to use on this form

### `configs: { string: HasuraDataConfig }`
Configs are used to tell the form how to handle relationships.  Everything works off of react-graphql configs, so that's all we need to supply here.  

-> HasuraDataConfig
-> SelectViaRelationship

##### USE CASE: I have a view that I exposed in hasura and need to modify the table the view is over
##### USE CASE: I have a relationship that isn't exposed in my graphql layer and need to connect two tables
##### USE CASE: I want to provide a where clause to filter the query for on particular relationship
##### USE CASE: I want to override the fragment/config used on this form from the default one found by react-graphql-ui

### `insertConfig: HasuraDataConfig`
Pass in a separate config for insert statments to use instead of the config

-> useInsert Props

##### USE CASE: I need to insert using different props than the defaults

### `updateConfig: HasuraDataConfig`
Pass in a separate config for update statments to use instead of the config

-> useUpdate Props

##### USE CASE: I need to update using different props than the defaults

### `isNew: boolean`
if isNew is true, FlexForm will insert.  Else it will update (default).

##### USE CASE: I want to use flexForm to gather data to insert

### `onSubmit: (data: any) => void`
Called when the form is submitted.  This prevents our default from getting called, thereby, not saving the data.

##### USE CASE: I want to override the form submission and handle it myself.

### `onSuccess: (data: any) => void`
Called when the form submission is successful.
Does not work when onSubmit is also provided.

##### USE CASE: I want to show a success Toast
##### USE CASE: I want to trigger a navigation event after the form is saved
##### USE CASE: I want start my dancing gnomes animation complete with celebratory confetti

### `components: Record<string, (props: ScalarComponentPropsBase) => ReactElement>`
Override one or more components used by the form.

The key of the object needs to exactly match the graphql field name.

The component props must be ScalarComponentPropsBase and connect your component into react-hook-form via useController.

-> StringInput

##### USE CASE: Normally I show an input component, but for this one form I want to use a select box instead to limit input options.
##### USE CASE: I have a relationship in my graphql fragment, and I don't want to setup my scalarComponent definition for some terrible awful no good reason.

### `grid: { styles: string,  columnCount: number }`
styles: is a string that will override the styles applied to the grid.  Use this if you don't want to use CSS grid by default, or if you want more find grained control. 
  
> Default: `grid grid-cols-3 gap-x-12 gap-y-8 justify-items-stretch`

##### USE CASE: I want a smaller/larger gap between my elements
##### USE CASE: I want to use flexbox instead
##### USE CASE: My employer makes me use bootstrap
##### USE CASE: I think CSS stands for Crapy-Stupid-Sheets
##### USE CASE: I keep meaning to learn CSS Grid, but you know, no one is making me so...

columnCount: How many columns do you want.

> Default: `3`

### `props: {fieldName: { propName : any, rules: ReactHookFormRules }}`

Yo dawg!  I heard you like props...

Props allows you to pass props to individual components.

##### USE CASE: I need to modify the validation rules on my components.
```
props={ 
  firstName: { rules: { required: true } } 
  kittens: { rules: { max: 4 } }
  pattern: { rules: { pattern: /[43ll0]/ } } }
}
```
> See: https://react-hook-form.com/api/useform/register


##### USE CASE: I need want to turn on time on the PrimeReact Calendar

> Prime React wants this: `<Calendar showTime hourFormat="12 or 24">` to turn on time so all we have to do is pass those in

```
  createdAt: { showTime: true, hourFormat: "24" } 
```
> See: https://www.primefaces.org/primereact/showcase/#/setup for components and props

##### USE CASE: I want to supply options to my select box without overriding anything else

```
  kangarooCourtJudgeCount: { options: [
    {label: "1 hopper", value: 1}, 
    {label: "2 bouncers", value: 2}, 
    {label: "3 party Roos", value 3}, 
    {label: "4 shall not be counted to", value 3}, 
    {value: "5 is right out!", value: 3}
  ] }
```

### `labels: { fieldName: newLabel }`
Override one or more labels shown on the form


### `onDataChange?: (data: any) => void;`
DEPRECATED
