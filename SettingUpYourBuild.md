## Setting up your build

We use vite for our build.  Find our current Vite file below.
You'll also want:
```
postcss.config.js
tailwind.config.js
```

You'll need to follow the vite docs on setting up the .html file correctly and get your imports starting.
Then you can run `vite` on the command line and you're off to the races.


vite.config.ts
```
import reactRefresh from '@vitejs/plugin-react-refresh';
import { defineConfig } from 'vite';
import tsconfigPaths from 'vite-tsconfig-paths'; //This is because we use absolute import paths
import path from 'path';

export default defineConfig({
  plugins: [reactRefresh(), tsconfigPaths()],
  optimizeDeps: {    
    esbuildOptions: {      
      resolveExtensions: [
        '.web.tsx',
        '.web.ts',
        '.web.jsx',
        '.web.js',
        '.tsx',
        '.ts',
        '.jsx',
        '.cjs',
        '.mjs',
        '.js',
      ], //This takes into account priority, so it will resolve a .web.tsx of the same file name before a .tsx if it exists
    },
  },
  define: {
    'process.env.SERVER': '1'
  },
  resolve: {
    extensions: [
      '.web.tsx',
      '.web.ts',
      '.web.jsx',
      '.web.js',
      '.tsx',
      '.ts',
      '.jsx',
      '.mjs',
      '.js',
    ], //This takes into account priority, so it will resolve a .web.tsx of the same file name before a .tsx if it exists
  },
})
```

postcss.config.js
```
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