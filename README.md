# Initial Setup

## Dependenceis 
Install these dependencies:

`@tesseractcollective/react-graphql primereact primeicons primeflex react-hook-form jotai urql`

And these dev dependencies:

`postcss-import @graphql-codegen/cli @graphql-codegen/introspection @graphql-codegen/typescript @graphql-codegen/typescript-operations postcss postcss-nested`

## react-graphql-ui Provider

```
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
