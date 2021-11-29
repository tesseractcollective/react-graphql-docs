# SelectViaRelationship

SelectViaRelationship is a component build on react-graphql to quickly and easily enable selecting values accross graphql relationships/foreign keys.

Input Props with comments:

```ts
export interface SelectViaRelationshipProps {
  name: string; // The name of the field we're saving the final value to. IE - personId
  value?: string // The initial value if we have one (useful when editing)
  onSelect?: (selectedItem: any) => void 
  configForRelationship: HasuraDataConfig //This is a config for the table we're pulling options from
  relationshipColumnNameForLabel: string //The name of the column to show in the dropdown as the label
  relationshipColumnNameForValue: string //The name of the column to save back to our 'name' column above. The foreign key id usually.
  autoSave?: boolean //TODO: Coming soon
  wrapperClassName?: string //ClassNames for the wrapping div
  styles?: {
    containerStyle?: any | Readonly<any>
    menuStyle?: any | Readonly<any>
    itemStyle?: any | Readonly<any>
    itemActiveStyle?: any | Readonly<any>
  }
  where?: any //Optional where clause to apply to the query for options
  control: any //The react-hook-form control to report selection changes too
  rules?: Omit< //validation rules
    RegisterOptions,
    'valueAsNumber' | 'valueAsDate' | 'setValueAs' | 'disabled'
  >
  dropdownProps?: DropdownProps //Pass through props for the underlying DropDown
}
```