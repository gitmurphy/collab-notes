# Migration Guide
Taken from [i18next.com](https://www.i18next.com/misc/migration-guide).

## v23.x.x to v24.0.0

- [ ] Remove support for older environments
      
> **Current Migration Status** 
> **[PENDING]** There are no older environments in use that we are aware of.
> 
> TODO: Confirm with Austin/Donal/Ian.

- [ ] Remove old i18next JSON formats  
  To convert your existing v3 translations to the v4 format, have a look at [`i18next-v4-format-converter`](https://github.com/i18next/i18next-v4-format-converter) or [this web tool](https://format-converter.i18next.com).  

> **Current Migration Status**  
> **[PENDING]** Under i18next JSON v4, docs state that "The only difference to v3 is the plural suffixes." 
> ###Plural suffix changes v3 -> v4:
> "keyPluralMultipleEgArabic_0": "the plural form 0"	->	"keyPluralMultipleEgArabic_zero": "the plural form 0"
> "keyPluralMultipleEgArabic_1": "the plural form 1"	->	"keyPluralMultipleEgArabic_one": "the plural form 1"
> "keyPluralMultipleEgArabic_2": "the plural form 2"	->	"keyPluralMultipleEgArabic_two": "the plural form 2"
> "keyPluralMultipleEgArabic_3": "the plural form 3"	->	"keyPluralMultipleEgArabic_few": "the plural form 3"
> "keyPluralMultipleEgArabic_4": "the plural form 4"	->	"keyPluralMultipleEgArabic_many": "the plural form 4"
> "keyPluralMultipleEgArabic_5": "the plural form 5"	->	"keyPluralMultipleEgArabic_other": "the plural form 5"
>   
> `git grep -E '_[0-9]+":'` returned a couple of results including `"Wrong_element_0_Did_you_mean_1":` and `"STTA_SALES_ORDER_ND_SRV_01": {`
> However neither of these examples are related to pluralization. 
> Pluralization is/has always been initlialized by passing a count variable: `t('key', { count: someNumber });`
>
> TODO: Check with Austin/Donal/Ian, does data imported to open-ux-tools contain plurals? 
> I noticed a pluralize package in **filesystem.ts** under **data-access** in the **store** package.


- [ ] Remove support for compatibility to the very first v1 API (old docs)  

> **Current Migration Status**  
> **[PENDING]** From inspecting the documentation, it does not appear that we are using the v1 API. 
>  
> TODO: Confirm with Austin/Donal/Ian.

- [ ] `Intl` API is mandatory now and will not fallback anymore.  
  Use a polyfill (`Intl.PluralRules` and `Intl.getCanonicalLocales`) if your environment does not support it.  
  For those who really need the old behaviour, you'll need to create a compatibility layer similar to [this](#).

> **Current Migration Status** 
> **[DONE]** We are already using the `Intl` API (e.g. `Intl.DateTimeFormatOptions`) and our environment (Node.js 18+, modern browsers) fully supports required features like `Intl.PluralRules` and `Intl.getCanonicalLocales`. No action required.  

- Renamed `initImmediate` to `initAsync`

- [x] Fallback to `dev` language if plural rule not found

> **Current Migration Status** 
> **[DONE]** dev language is defined by fallbackLng option in init function or else first detected language.  
> There is a fallnackLng defined in all of the init functions in open-ux-tools.  

- [x] Dropped support for Node.js < v14

> **Current Migration Status**   
> **[DONE]** Root package.json states: `"node": ">=18.x"`

### TypeScript

- [ ] Now only TypeScript >5 versions are supported.  
  v4 types are now removed from the codebase.

> **Current Migration Status**  
> **[PENDING]** TypeScript version is ^4.6.3 in two test applications for integration testing fe-fpm-writer:
> 
> ```
> $ git grep '"typescript":'
> .eslintrc:            "typescript": {
> docs/tools-offline-instructions/info/projectModules.json:    "typescript": [
> examples/ui-prompting-examples/package.json:        "typescript": "5.6.2",
> package.json:        "typescript": "5.8.2",
> packages/adp-tooling/templates/project/package.json:        "typescript": "^5.7.3",
> packages/fe-fpm-writer/test/test-input/integration/basic-lrop-app/ts/package.json:    "typescript": "^4.6.3",
> packages/fe-fpm-writer/test/test-input/integration/custom-page-app/ts/package.json:    "typescript": "^4.6.3",
> packages/generator-adp/package.json:        "typescript": "5.3.3",
> packages/ui-service-inquirer/package.json:        "typescript": "5.3.3",
> packages/ui-service-sub-generator/package.json:        "typescript": "5.3.3",
> packages/ui5-application-writer/templates/optional/typescript/package.json:        "typescript": "^5.1.6",
> packages/ui5-library-sub-generator/test/unit/expected-output/tslibrary1/package.json:    "typescript": "^5.1.6",
> packages/ui5-library-writer/templates/optional/typescript/package.json:      "typescript": "^5.1.6",
>   
> ```
> 
> TODO: Check if this needs to be updated with Austin/Donal/Ian.  

- [x] `jsonFormat` option has been removed.  
  When a new JSON version is released, you can use the `compatibilityJSON` option, which now only accepts `v4` as value.

> **Current Migration Status**  
> **[DONE]** Both options could be passed as parameters to the i18next.init function. Example from docs:
> ```
> i18next.init({
>  compatibilityJSON: 'v3'
> });
> ```
>
> Neither appear in open-ux-tools:
> `git grep jsonFormat` returns nothing.
> AND
> `git grep compatibilityJSON` returns nothing.


---

## v22.x.x to v23.0.0

- [ ] Redesigned TypeScript types  
  This PR redesigned the types to be less complex, faster and easier to maintain.  
  The redesign endeavors to enhance the approach to parsing and inferring keys for the `t` function.  
  Instead of performing a recursive examination of each key-value pair in resources associated with specific namespace(s) each time the `t` function is invoked, we generate a comprehensive set of keys from all namespaces just once.

  Make sure your `tsconfig` `compilerOptions` has the `strict` flag or the `strictNullChecks` set to `true`.
  Also use TypeScript v5.
  
> **Current Migration Status**  
> **[PENDING]** strictNullChecks is set to true in root tsconfig. TypeScript version is 5 apart from two test apps as mentioned above.
> 
> TODO: Check with Austin/Donal/Ian if satisfying both of these conditions means that this migration step is completed/ can be skipped.

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

> **Question**  
> **[PENDING]** Are these codemods optional? They don't appear to be necessary in our case.  

- [x] Removed `setDebug` function in internal logger  
  Based on [this discussion](#), we decided to remove the `setDebug` function.

> **Current Migration Status**  
> **[DONE]** Discussion above defines usage as `i18next.logger.setDebug(true);`
> `git grep setDebug` returns nothing.

- [x] Changed default value for `returnNull` option to `false`  
  To improve the usage for TypeScript users (in combination with React.js) we decided to set the `returnNull` value to `false` by default.  
  More information can be found [here](#).  

> **Current Migration Status**  
> **[DONE]** `git grep returnNull` returns nothing for open-ux-tools.

- [x] Dropped support for old browsers and Node.js < v12  
  To have smaller builds and faster loads, we now transpile only for modern browsers and runtimes.  
  More information can be found [here](#).  

> **Current Migration Status**  
> **[DONE]** `Node version is >=18.x. Presumably only modern browsers are also supported by open-ux-tools.  

- [ ] Prefixed ordinal plural keys  
  To help translators, ordinal plural keys are now prefixed with `_ordinal`.
> **Current Migration Status**  
> **[PENDING]** 
> ```
> // Examples from docs
> {
>   "key_ordinal_one": "{{count}}st place", // 1st, 21st, 31st
>   "key_ordinal_two": "{{count}}nd place", // 2nd, 22nd, 32nd
>   "key_ordinal_few": "{{count}}rd place", // 3rd, 23rd, 33rd
>   "key_ordinal_other": "{{count}}th place" // 4th, 5th, 24th, 11th
> }
> 
> // Called like:
> i18next.t('key', { count: 1, ordinal: true }); // -> "1st place"
> ```
> 
> `git grep '_ordinal_'` returns nothing.
> 
> TODO: Check with Austin/Donal/Ian how we can test open-ux-tools by passing plurals to it?

---

## v21.x.x to v22.0.0

- [x] Since this is a major rewrite for TypeScript usage, we decided to create a major version.  
For JavaScript users, `v22.0.0` is equivalent to `21.10.0`.  

> **Current Migration Status**  
> **[DONE]** Project is already compatibile with TypeScript.

---

## v20.x.x to v21.0.0

### JSON format v4 - pluralization

- [x] One of the biggest breaking changes is regarding suffixing plurals.  
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

> **Current Migration Status**  
> **[DONE]** Pluralization looked at under v24 migration.

---

### `skipOnVariables = true`

By default, the `skipOnVariables` option is now set to `true`.  
- [ ] To enforce old behaviour, you can set `skipOnVariables = false` on i18next init call.  

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

> **Current Migration Status**  
> **[PENDING]** `git grep skipOnVariables` returns nothing. 
>
> TODO: Check if our logic relies on the old default false value for skipOnVariables.

---

### Natural language detection

- [x] i18next now automatically tries to detect natural language keys.  
This way there is no need to set `nsSeparator` or `keySeparator` option to `false`.  
In case you want to skip this natural language detection, provide a `keySeparator` and/or a `nsSeparator` option.  

> **Current Migration Status**  
> **[DONE]** Both `git grep nsSeparator` and `git grep keySeparator` return nothing.

---

### Removed deprecated

The old deprecated `whitelist` options and functions have been definitively removed.

- Rename option `whitelist` to `supportedLngs`
- Rename option `nonExplicitWhitelist` to `nonExplicitSupportedLngs`
- Rename function `languageUtils.isWhitelisted` to `languageUtils.isSupportedCode`

> **Current Migration Status**  
> **[PENDING]**

---

### New `resolvedLanguage`

- [x] There is a new `i18next.resolvedLanguage` property that represents the current resolved language.  
It can be used as the primary used language, for example in a language switcher.  

> **Current Migration Status**  
> **[DONE]** 
>	
> ```
> git grep nonExplicitWhitelist
> git grep nonExplicitSupportedLngs
> git grep isWhitelisted
> git grep isSupportedCode
> ```
>
>	None of the above return anything related to i18next. 

---

### `defaultNS`

- [ ] If passing the `ns` option, the `defaultNS` will, by default, be set to the first `ns` passed.

> **Current Migration Status**  
> **[PENDING]** 
