# What is this and why do I care?

`react-graphql` is a library built to make CRUD easy when working with graphql back-ends that standardize their format.  We've primarily built it using [Hasura.io](https://hasura.io) and use their naming convention as our defaults.

RG primarily uses hooks to provide support for query one, query many, mutation one, delete one, insert one.

Developer experience is going to involve one-time setup of your code generator and one-time definition of your typeNames/resources and then the ability to pass that resource config around to the various hooks to get typed graphql operations easily.

`react-graphql-ui` is a library built on top of react-graphql to give you various components and hooks to help speed up development.  Its main offerings are currently: 
* useDataTable: a hook helping you quickly compose a DataTable to your liking with full pagination, filtering, ordering, and more
* FlexForm/FlexFormLocal: a component that will build a customizable form, complete with UI components, for you based on your graphql fragment/config.
* SelectViaRelationship: a select box component that pulls from your DB for its options

# Initial Setup

## Dependencies 
Install these dependencies:

`@tesseractcollective/react-graphql jotai primereact primeicons primeflex react-hook-form urql`

And these dev dependencies:

`graphql graphql-tag @graphql-codegen/cli @graphql-codegen/introspection @graphql-codegen/typescript @graphql-codegen/typescript-operations postcss postcss-nested postcss-import`

## react-graphql-ui Provider

```ts
import { ReactGraphqlUIContext, PrimeReactDefaultComponents } from '@tesseractcollective/react-graphql-ui';
import HasuraConfig from './...../HasuraConfig.ts

return <ReactGraphqlUIContext.Provider value={{
  defaultComponents: PrimeReactDefaultComponents,
  configsMap: HasuraConfig
}}>
  ...
</ReactGraphqlUIContext.Provider>
```

# Next Steps

If you're new to Tesseract's react-graphql library checkout:

* [How to setup your project](SetupYourProject.md)
* [How to configure react-graphql](reactGraphqlConfig.md)
* [Setting up your build](SettingUpYourBuild.md)
* [Dive deeper into Item vs Variable on mutations](ExplainingMutationItemVsVariable.md)

If you want to checkout the cool UI support we have look at

* [FlexForm](FlexForm.md)
* [FlexFormLocal](FlexFormLocal.md)
* [useDataTable](useDataTable.md)

Other references

* [Typescript support](ReactGraphqlTypescriptSupport.md)

Third Parties we use

* [PrimeReact](https://www.primefaces.org/primereact/showcase)
* [Jotai](https://jotai.pmnd.rs/docs/introduction)
* [Tailwindcss](https://tailwindcss.com)
* [urql](https://formidable.com/open-source/urql/docs/)
* [Hasura](https://hasura.io)
* [ReactHookForm](https://react-hook-form.com/api)

# Developer Dependencies

In development we also use:
* [Vite](https://vitejs.dev)