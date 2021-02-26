This is a minimum reproduction for [@babel/preset-env bug](https://github.com/babel/babel/issues/12880)

## Steps to reproduce:
1. Run `yarn install`
2. Check that `yarn.lock` isn't regenerated and that `node_modules/jest-config/node_modules/@babel/core/package.json` has version 7.12.16
3. Run `yarn test` (test will fail with `ReferenceError: regeneratorRuntime is not defined` error)
4. Run `yarn build` and compare `Using targets:` output from `test` and `build` commands

## Technical discussion:
This bug is possible only on old repositories that update their dependencies gradually. I had to take `package.json` and `yarn.lock` from existing repo and then update it by removing all extra libraries. The bug happens because `yarn.lock` contains 2 different versions of `@babel/core` and will disappear if remove `yarn.lock` and `node_modules` and regenerate everything from scratch.  
When jest transpiles test files it has `@babel/core@7.12.16` in its own `node_modules` and that is why `api.targets()` call in `@babel/preset-env` hits `apiPolyfills.targets` from `helper-plugin-utils` instead of real implementation of targets api.  
The same bug can be reproduced if use `@babel/core v12` with `@babel/preset-env v13` in project direct dependencies, but it is less valid scenario than the one provided above.
