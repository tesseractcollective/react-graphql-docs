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
