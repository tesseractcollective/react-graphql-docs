# Custom Components

RGUI contextProvider recieves: `defaultComponents` as one of it's props.

We use this map to try and resolve field types to components in your project.

IE - If your fragment has a field: `name: String` then we'll find the String scalar type and look for a `StringInput` to be passed into `defaultComponents={{ StringInput: StringInput }}`.

## ScalarComponents are the most common use case

- To be a valid default component its props must be or extend the ScalarComponentPropsBase interface.
- To save/update any field values it should implement useController from react-hook-form and connect the field to your input component (see `ref`, and `inputProps` below)

```tsx
const StringInput = React.FC<ScalarComponentPropsBase> = (props: ScalarComponentPropsBase) => {
      const {
        field: { ref, ...inputProps },
        fieldState: { invalid, isTouched, isDirty },
        formState: { touchedFields, dirtyFields },
      } = useController({
        name: props.fieldInfo.name,
        control: props.control,
        rules: {},
        defaultValue: '',
      })

      return (
        <div className="p-float-label">
          <InputText
            id={'ff-' + props.fieldInfo.name}
            ref={ref}
            {...inputProps}
            className="p-inputtext-sm w-full bg-purco-blue-primary "
          />
          <label htmlFor={'ff-' + props.fieldInfo.name}>
            {Case.sentence(props.fieldInfo.name)}
          </label>
        </div>
      )
    },
  }
```

This will tell RGUI how you want every TextInput rendered in the application whenever we encounter a graphql field of type Text.

### Multiple components for a single graphql type

It would be normal to want different components for a single type. IE- we'll have a single String type from graphql, but in our code we might sometimes want an input box, and sometimes a rich text editor.

The power of registering components with our provider is that you control all components, not us. So you can solve this with a simple if statement, or something more complicated if you'd like.

Example: using props to change rendered component

```tsx

export interface IStringInputProps extends ScalarComponentPropsBase {
  mask?: string
  richText?: boolean
}

const StringInput = React.FC<IStringInputProps> = (props: IStringInputProps) => {
      const {
        field: { ref, ...inputProps },
        fieldState: { invalid, isTouched, isDirty },
        formState: { touchedFields, dirtyFields },
      } = useController({
        name: props.fieldInfo.name,
        control: props.control,
        rules: {},
        defaultValue: '',
      })

      const renderInput = ()=> {
        if (props.mask) {
           return (
              <InputMask
              mask={mask}
              id={'ff-' + fieldInfo.name}
              ref={ref}
              {...inputProps}
              className="p-inputtext-sm w-full"
              {...passthroughProps}
            />
          )
        }

        if (props.richText) {
           return (
              <Editor
                id={'ff-' + fieldInfo.name}
                ref={ref}
                {...inputProps}
                // need to manually trigger the onChange from onTextChange
                onTextChange={(e) => inputProps.onChange(e.htmlValue)}
                // HACK: There is currently (afaik) no easy way to detect the focus event
                // for the Editor component, meaning the floatlabel won't work correctly
                // setting this class is a hack to force the float label to always appear
                // as though the wrapper has been filled to have the proper style
                className={'p-inputwrapper-filled'}
                // This changes what buttons are displayed to change formatting
                headerTemplate={toolbarHeader} />
          )
        }
        
        return <InputText
            id={'ff-' + props.fieldInfo.name}
            ref={ref}
            {...inputProps}
            className="p-inputtext-sm w-full bg-purco-blue-primary "
          />;
      }

      return (
        <div className="p-float-label">
          {renderInput()}
          <label htmlFor={'ff-' + props.fieldInfo.name}>
            {Case.sentence(props.fieldInfo.name)}
          </label>
        </div>
      )
    },
  }
```

This would then be used like so:
```tsx
<FlexForm config={HasuraConfig.userProfile} 
          props={{phoneNumber: { mask: '....' }, bio: { richText: true }}}>

```


## Custom components for a type

If you want to create a form for a specific type you can.  Let's say we have a user that has addresses, and we want to setup a component for the Address type.

We would then regsiter `AddressInput: AddressInput` on our defaultComponents on our provider context.

That AddressInput would be setup just like a Scalar component above with the same props structure.  It would still need to interact with react-hook-form, but could do so in many ways (see react hook form docs on controller), including using FlexFormLocal to control/organize the fields shown.  More likely it will use the fields passed down to it via props.fieldInfo and it's own fragment.