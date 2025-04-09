# Migration Guide
Taken from [i18next.com](https://www.i18next.com/misc/migration-guide).

## v23.x.x to v24.0.0

- Remove support for older environments
> **Current Migration Status**  
> There are no older environments in use that we are aware of.

- Remove old i18next JSON formats  
  To convert your existing v3 translations to the v4 format, have a look at [`i18next-v4-format-converter`](https://github.com/i18next/i18next-v4-format-converter) or [this web tool](https://format-converter.i18next.com).
> **Current Migration Status**  
> Under i18next JSON v4, docs state that "The only difference to v3 is the plural suffixes." TODO: Check https://www.i18next.com/translation-function/plurals#how-to-find-the-correct-plural-suffix to ensure that plural suffixes are current.

- Remove support for compatibility to the very first v1 API (old docs)
> **Current Migration Status**  
> From inspecting the documentation, it does not appear that we are using the v1 API. TODO: Confirm with Austin or Donal.

- `Intl` API is mandatory now and will not fallback anymore.  
  Use a polyfill (`Intl.PluralRules` and `Intl.getCanonicalLocales`) if your environment does not support it.  
  For those who really need the old behaviour, you'll need to create a compatibility layer similar to [this](#).

- Renamed `initImmediate` to `initAsync`

- Fallback to `dev` language if plural rule not found

- Dropped support for Node.js < v14
> **Current Migration Status**  
> Root package.json states: `"node": ">=18.x"`

### TypeScript

- Now only TypeScript >5 versions are supported.  
  v4 types are now removed from the codebase.

- `jsonFormat` option has been removed.  
  When a new JSON version is released, you can use the `compatibilityJSON` option, which now only accepts `v4` as value.

---

## v22.x.x to v23.0.0

- Redesigned TypeScript types  
  This PR redesigned the types to be less complex, faster and easier to maintain.  
  The redesign endeavors to enhance the approach to parsing and inferring keys for the `t` function.  
  Instead of performing a recursive examination of each key-value pair in resources associated with specific namespace(s) each time the `t` function is invoked, we generate a comprehensive set of keys from all namespaces just once.

  Make sure your `tsconfig` `compilerOptions` has the `strict` flag or the `strictNullChecks` set to `true`.

  Also use TypeScript v5.

### Codemods

To assist with the upgrade from i18n v22.x.x to v23.0.0, Codemod provides open-source codemods that automatically transform your code to many of the new APIs and patterns.

These following codemods are available (see the notes in the "More information" section below):

- `i18next/23/add-namespace-type-annotation`
- `i18next/23/remove-options`
- `i18next/23/replace-keys`

If you encounter any issues, please report them to the Codemod team with:

```bash
npx codemod feedback
```

- Removed `setDebug` function in internal logger  
  Based on [this discussion](#), we decided to remove the `setDebug` function.
> **Current Migration Status**  
> Discussion above defines usage as `i18next.logger.setDebug(true);`
> `git grep setDebug` returns nothing.

- Changed default value for `returnNull` option to `false`  
  To improve the usage for TypeScript users (in combination with React.js) we decided to set the `returnNull` value to `false` by default.  
  More information can be found [here](#).

- Dropped support for old browsers and Node.js < v12  
  To have smaller builds and faster loads, we now transpile only for modern browsers and runtimes.  
  More information can be found [here](#).

- Prefixed ordinal plural keys  
  To help translators, ordinal plural keys are now prefixed with `_ordinal`.

---

## v21.x.x to v22.0.0

Since this is a major rewrite for TypeScript usage, we decided to create a major version.  
For JavaScript users, `v22.0.0` is equivalent to `21.10.0`.

---

## v20.x.x to v21.0.0

### JSON format v4 - pluralization

One of the biggest breaking changes is regarding suffixing plurals.  
This change streamlines the suffix with the one used in the `Intl` API.  
You may need to polyfill the `Intl.PluralRules` API. In case it is not available, it will fallback to the i18next JSON format v3 plural handling.  
To enforce old behaviour, you can enable `compatibilityJSON = 'v3'` on i18next init call.

```js
import i18next from 'i18next';

i18next.init({
  compatibilityJSON: 'v3'
}, (err, t) => {
  /* resources are loaded */
});
```

There is also support for ordinal numbers (referring to the ordering or ranking of things, e.g. "1st", "2nd", "3rd" in English).  
Learn more about plurals: https://www.i18next.com/translation-function/plurals

---

### `skipOnVariables = true`

By default, the `skipOnVariables` option is now set to `true`.  
To enforce old behaviour, you can set `skipOnVariables = false` on i18next init call.

```js
import i18next from 'i18next';

i18next.init({
  interpolation: {
    skipOnVariables: false
  }
}, (err, t) => {
  /* resources are loaded */
});
```

---

### Natural language detection

i18next now automatically tries to detect natural language keys.  
This way there is no need to set `nsSeparator` or `keySeparator` option to `false`.  
In case you want to skip this natural language detection, provide a `keySeparator` and/or a `nsSeparator` option.

---

### Removed deprecated

The old deprecated `whitelist` options and functions have been definitively removed.

- Rename option `whitelist` to `supportedLngs`
- Rename option `nonExplicitWhitelist` to `nonExplicitSupportedLngs`
- Rename function `languageUtils.isWhitelisted` to `languageUtils.isSupportedCode`

---

### New `resolvedLanguage`

There is a new `i18next.resolvedLanguage` property that represents the current resolved language.  
It can be used as the primary used language, for example in a language switcher.

---

### `defaultNS`

If passing the `ns` option, the `defaultNS` will, by default, be set to the first `ns` passed.
