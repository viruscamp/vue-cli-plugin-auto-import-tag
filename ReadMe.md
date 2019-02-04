# vue-cli-plugin-auto-import-tag
Auto import vue components by tags.  
Should be used along with using [babel-plugin-import](https://github.com/ant-design/babel-plugin-import) or [babel-plugin-transform-imports](https://github.com/viruscamp/babel-plugin-transform-imports/tree/babel-7).

USE ON YOUR OWN RISK. WITHOUT ANY EXPRESS OR IMPLIED WARRANTIES.

Currently, there is a fatal bug. We had generated the correct source, but the generated source was not processed by babel-plugin-import nor babel-plugin-transform-imports. Here is what we generated:
```javascript
// It's just like what vue custom block generated
import { DatePicker as ADatePicker } from 'ant-design-vue'
import { AutoComplete as AAutoComplete } from 'ant-design-vue'

export default function (Component) {
  const c = Component.options.components
  if (c.ADatePicker == null) c.ADatePicker = ADatePicker
  if (c.AAutoComplete == null) c.AAutoComplete = AAutoComplete
}
```


## Usage with vue-cli-3.x
0. Prerequirements  Your project must have these as devDependencies:

"@vue/cli-service": "^3.0.0"

"babel-plugin-import": "^1.0.0"  
or  
"babel-plugin-transform-imports": "github:viruscamp/babel-plugin-transform-imports"

1. Install  
npm install github:viruscamp/vue-cli-plugin-auto-import-tag --save-dev

2. Configurate  
Just add "tagPrefix" to your existing babel-plugin-import/babel-plugin-transform-imports options in .babelrc or babel.config.js .  
To work with babel-plugin-transform-imports's regular expressions library match, you must add "libraryName" to it.
```javascript
// babel.config.js
module.exports = {
  presets: [
    '@vue/app'
  ],
  "plugins": [
    [
      "import",
      {
        tagPrefix: "a", // added for babel-plugin-import
        libraryName: "ant-design-vue",
        libraryDirectory: "es",
        style: "css"
      }
    ],
    [
      "transform-imports", {
        "ant-design-vue": {
          tagPrefix: "a", // added for babel-plugin-transform-imports
          kebabCase: true,
          transform: "ant-design-vue/lib/${member}",
          transformStyle: "ant-design-vue/lib/${member}/style/css",
          preventFullImport: true
        },
        "my-library\/?(((\\w*)?\/?)*)": {
          tagPrefix: "my", // added for babel-plugin-transform-imports
          libraryName: "my-library", // added for babel-plugin-transform-imports's regular expressions library match
          kebabCase: true,
          transform: "my-library/${1}/${member}",
          transformStyle: "my-library/${1}/${member}/style",
          preventFullImport: true
        }
      }
    ]
  ]
}

```

3. Code  
You can now write:  
```vue
<template>
  <div>
    <AButton />
    <a-auto-complete />
  </div>
</template>
```
Instead of:  
```vue
<template>
  <div>
    <AButton />
    <a-auto-complete />
  </div>
</template>
<script>
import { Button, AutoComplete } from 'ant-design-vue'
export default {
  components: {
    AButton: Button,
    AAutoComplete: AutoComplete
  }
}
</script>
```

## Usage with manual mantained webpack.conf
TODO

## Limitations
1. Only works for *.vue file \<template\> part
2. Invalid for dynamic 'is', like \<div :is="compName" /\>
3. Invalid for string template
4. Invalid for render function and JSX

## Fatal bugs:
1. The code generated by this plugin was not processed by babel-plugin-import nor babel-plugin-transform-imports. See [#2](https://github.com/viruscamp/vue-cli-plugin-auto-import-tag/issues/2)

2. Just use vue-cli-3.x, babel-plugin-import, and ant-design-vue, without this plugin.  
```javascript
import {Breadcrumb as ABreadcrumb} from 'ant-design-vue'
```
It try to import ant-design-vue/es/a-breadcrumb

