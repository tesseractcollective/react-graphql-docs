# FlexFormLocal

## INTRO

To build mutation in a project using graphql for it's data layer you need to:
* Build your UI and components
* Save their state on the screen
* Setup a graphql query
* execute the mutation
* Manage errors and success

FlexFormLocal gives you a composable component that combines multiple libraries together to give you a rapid experience for building UIs connected to a graphql backend.


## FlexFormLocal vs FlexForm

> Hey, what's the difference between this and your FlexForm

A lot of the power of FlexForm is in its automatic layouts, and you might want that, without all the rest of it, that's what FlexFormLocal does.

> Uh, can you give me an example

Sure, let's say you want to split your form up across multiple components with a single save on completion, wizard style.  FlexFormLocal will let you do that by having you pass the react-hook-form controller around and connecting it.

Another example would be wanting to put a form inside your form.  You can nest FlexFormLocal inside another FlexFormLocal or a FlexForm and share the same react-hook-form controller, and use all the magic, together.  Just like My Little Ponies, but we lost the office vote when trying to name it "MyLittlePoniesForm".


## Libraries
* react-hook-form - for managing the form, state, error, dirty, etc
* graphql & react-graphql - To insert and update your data
* prime react - To show the components
* tailwindcss - to handle styling

## By Use-case

### USE CASE: I want a basic register form with 4 fields

> You probably want a single column grid

```
const {control} = useForm(); // from react-hook-form

  <FlexFormLocal fields={[{...}, {...}]}
            control={control}            
            onSuccess={(data:any)=> {
              //Navigate to your next page or show a modal, or save
            }}
            >

```

### USE CASE: I want to nest FlexFormLocal inside another FlexForm

> You probably want a single column grid


```tsx
<FlexForm
  config={HasuraConfig.client}
  api={api}  
  components={{
    displayName: (props: ScalarComponentPropsBase) => {
      const fields = [...] //See FlexFormLocal
      return (
        <div>
          <FlexFormLocal
          control={props.control}
          disableAllFields={props?.disabled}
          fields={fields}
        />
        </div>
      )
    },
  }}
></FlexForm>
```



## Required Props

### `fields: ReactGraphql.FlexFormFieldOutputType[]`
Pass in an array of field defintions.  Specifiy typeName, fieldName, labels, relationship specifications, etc.

Typically you'll build these by hand including the 3 required properties and anything else relevant.

You can also get them from a config Object on the `[tableName].fields.fieldSimpleMap` and filter or modify as you wish. 

```
interface FlexFormFieldOutputType {
  table: string; //This has no impact on FlexFormLocal except helping you get better debugging messages
  name: string; //The name of the field, fully qualified.  
  typeName: string; //Any string.  Will resolved to components passed into the RGUI ContextProvider.  By default we include ScalarTypes (found in your graphql.generated.tsx file at the top) IE - Text, Float, DateTime, etc.  But you could call this bubbles and include a BubblesInput entry in the components you register and it will resolve to that component.
  isNonNull?: boolean;
  isObject?: boolean;
  isList?: boolean;
  data?: any;
  relationship?: { table: string; field: string };
  label?: string //Use this as the label instead of name
}
```

```
NOTE: You can pass a nested path into name, just like you can with react-hook form.  

IE - 'userProfile.addresses[0].street'

This will nest your field according to the path specified and build objects and arrays for you based on your path.
```

### `control: ReactHookForm.Control`
Pass in the control prop received from `ReactHookForm.useForm`.

```
const formState = useForm();
...
<FlexFormLocal control={formState.control}/>
```


## Optional Props

### `data : {key: value}`
Pass in an object where the keys match the shape of your data
### USE CASE: I have data already when the component is loaded, and I want it populate it up front



### `configs: { string: HasuraDataConfig }`
Configs are used to tell the form how to handle relationships.  Everything works off of react-graphql configs, so that's all we need to supply here.  

-> HasuraDataConfig
-> SelectViaRelationship

### USE CASE: I have a view that I exposed in hasura and need to modify the table the view is over
### USE CASE: I have a relationship that isn't exposed in my graphql layer and need to connect two tables
### USE CASE: I want to provide a where clause to filter the query for on particular relationship
### USE CASE: I want to override the fragment/config used on this form from the default one found by react-graphql-ui

### `components: Record<string, (props: ScalarComponentPropsBase) => ReactElement>`
Override one or more components used by the form.

The key of the object needs to exactly match the graphql field name.

The component props must be ScalarComponentPropsBase and connect your component into react-hook-form via useController.

-> StringInput

### USE CASE: Normally I show an input component, but for this one form I want to use a select box instead to limit input options.
### USE CASE: I have a relationship in my graphql fragment, and I don't want to setup my scalarComponent definition for some terrible awful no good reason.

### `grid: { styles: string,  columnCount: number }`
styles: is a string that will override the styles applied to the grid.  Use this if you don't want to use CSS grid by default, or if you want more find grained control. 
  
> Default: `grid grid-cols-3 gap-x-12 gap-y-8 justify-items-stretch`

### USE CASE: I want a smaller/larger gap between my elements
### USE CASE: I want to use flexbox instead
### USE CASE: My employer makes me use bootstrap
### USE CASE: I think CSS stands for Crapy-Stupid-Sheets
### USE CASE: I keep meaning to learn CSS Grid, but you know, no one is making me so...

columnCount: How many columns do you want.

> Default: `3`

### `props: {fieldName: { propName : any, rules: ReactHookFormRules }}`

Yo dawg!  I heard you like props...

Props allows you to pass props to individual components.

### USE CASE: I need to modify the validation rules on my components.
```
props={ 
  firstName: { rules: { required: true } } 
  kittens: { rules: { max: 4 } }
  pattern: { rules: { pattern: /[43ll0]/ } } }
}
```
> See: https://react-hook-form.com/api/useform/register


### USE CASE: I need want to turn on time on the PrimeReact Calendar

> Prime React wants this: `<Calendar showTime hourFormat="12 or 24">` to turn on time so all we have to do is pass those in

```
  createdAt: { showTime: true, hourFormat: "24" } 
```
> See: https://www.primefaces.org/primereact/showcase/#/setup for components and props

### USE CASE: I want to supply options to my select box without overriding anything else

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


### `onDataFromGrid?: (data: any) => void;`
DEPRECATED
