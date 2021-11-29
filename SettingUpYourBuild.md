## Setting up your build

We use vite for our build.  Find our current Vite file below.
You'll also want:
```
postcss.config.js
tailwind.config.js
```

You'll need to follow the vite docs on setting up the .html file correctly and get your imports starting.
Then you can run `vite` on the command line and you're off to the races.

[Vite Setup Docs](https://vitejs.dev/guide/#scaffolding-your-first-vite-project)

vite.config.ts
```ts
import reactRefresh from '@vitejs/plugin-react-refresh';
import { defineConfig } from 'vite';
import tsconfigPaths from 'vite-tsconfig-paths'; //This is because we use absolute import paths
import path from 'path';

export default defineConfig({
  plugins: [reactRefresh(), tsconfigPaths()],  
  define: {
    'process.env.SERVER': '1'
  },  
})
```

postcss.config.js
```js
module.exports = {
   plugins: {
      tailwindcss: {},
      'postcss-nested': {},
      autoprefixer: {},
   },
};
```

tailwind.config.js
```
  ...Find this on the tailwind docs page
```
[Tailwind Installation docs](https://tailwindcss.com/docs/installation)