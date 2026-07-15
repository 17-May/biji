# Eslint+Prettier

### eslint.config.ts

```ts
import stylistic from '@stylistic/eslint-plugin'
import tsParser from '@typescript-eslint/parser'
import pluginVue from 'eslint-plugin-vue'
import globals from 'globals'
import vueEslintParser from 'vue-eslint-parser'
import validComponentFilename from './eslint-rules/valid-component-filename.js'

const sharedRules = {
  // 禁止使用var，强制用let/const
  'no-var': 'error', // 建议未重新赋值的变量用const（警告级别）
  'prefer-const': 'warn', // 禁止未使用变量，忽略未用参数、剩余参数及下划线前缀变量
  'no-unused-vars': [
    'error',
    { args: 'none', ignoreRestSiblings: true, varsIgnorePattern: '^_' },
  ], // 强制使用===/!==，允许null比较
  'eqeqeq': ['error', 'allow-null'], // 确保typeof结果与字符串字面量比较
  'valid-typeof': ['error', { requireStringLiterals: true }], // 允许使用undefined
  'no-undefined': 'off', // 优先单引号，允许转义及模板字符串
  'quotes': [
    'error',
    'single',
    { avoidEscape: true, allowTemplateLiterals: true },
  ], // 关键字（if/for等）前后必须有空格
  'keyword-spacing': ['error', { before: true, after: true }], // 代码块（{}）前必须有空格
  'space-before-blocks': 'error', // 点运算符与属性同线（如obj.prop）
  'dot-location': ['error', 'property'], // 文件末尾必须有且仅有一个空行
  'eol-last': ['error', 'always'], // 最多1个连续空行，首尾不允许空行
  'no-multiple-empty-lines': ['error', { max: 1, maxEOF: 0, maxBOF: 0 }],
}

export default [
  {
    ignores: [
      'dist/',
      'build/',
      'node_modules/',
      '.cache/',
      '.eslintcache',
      'coverage/',
      'jest-report/',
      '*.lock',
      '*.min.*',
      '*.{svg,png,jpg,jpeg,gif,ico,mp4,woff,woff2,log,tmp,swp}',
    ],
  },

  {
    languageOptions: {
      globals: { ...globals.browser, ...globals.node },
      ecmaVersion: 2020,
      sourceType: 'module',
    },
    plugins: {
      stylistic,
      custom: {
        rules: {
          'valid-component-filename': validComponentFilename,
        },
      },
    },
    rules: {
      ...sharedRules,
      'custom/valid-component-filename': 'error',
    },
  },

  {
    files: ['**/*.vue'],
    languageOptions: {
      parser: vueEslintParser,
      parserOptions: {
        parser: tsParser,
        ecmaVersion: 2020,
        sourceType: 'module',
        ecmaFeatures: { jsx: true },
      },
    },
    plugins: { vue: pluginVue },
    rules: {
      ...pluginVue.configs['flat/recommended'].rules, // 强制v-slot使用完整语法（如v-slot:header）
      'vue/v-slot-style': ['error', 'longform'], // 禁止直接修改props
      'vue/no-mutating-props': 'error', // 原生空标签自闭合、非空标签不闭合，自定义组件自闭合
      'vue/html-self-closing': [
        'error',
        {
          html: { void: 'always', normal: 'never', component: 'always' },
          svg: 'always',
          math: 'always',
        },
      ], // 禁止属性继承冲突（如props与$attrs重复）
      'vue/no-duplicate-attr-inheritance': 'error', // 禁止重复属性（如多个class/style）
      'vue/no-duplicate-attributes': [
        'error',
        { allowCoexistClass: false, allowCoexistStyle: false },
      ], // 确保v-model使用正确
      'vue/valid-v-model': 'error', // 模板属性强制连字符命名，忽略data-/aria-/v-前缀
      'vue/attribute-hyphenation': [
        'error',
        'always',
        { ignore: ['data-', 'aria-', 'v-'] },
      ], // 多行元素内容强制换行，忽略空内容及pre/textarea/template
      'vue/multiline-html-element-content-newline': [
        'error',
        { ignoreWhenEmpty: true, ignores: ['pre', 'textarea', 'template'] },
      ], // 单行标签右括号不换行，多行标签右括号单独换行
      'vue/html-closing-bracket-newline': [
        'error',
        { singleline: 'never', multiline: 'always' },
      ], // 模板缩进2空格，属性缩进1级，闭合标签与开始标签对齐
      'vue/html-indent': [
        'error',
        2,
        { attribute: 1, baseIndent: 1, closeBracket: 0 },
      ], // v-html降级为警告（提示XSS风险）
      'vue/no-v-html': 'warn', // 组件定义名必须是 PascalCase（大写驼峰）
      'vue/component-definition-name-casing': ['error', 'PascalCase'], // 模板中自定义组件名必须是 PascalCase（大写驼峰）
      'vue/component-name-in-template-casing': [
        'error',
        'PascalCase',
        {
          ignores: ['/^el-/', '/^a-/', '/^v-/'], // 忽略el-ui el-plus ant-design-vue vuetfy
        },
      ], // 不强制props默认值
      'vue/require-default-prop': 'off', // 不强制声明prop类型
      'vue/require-prop-types': 'off', // 关闭单行元素内容换行限制
      'vue/singleline-html-element-content-newline': 'off', // 不强制属性顺序
      'vue/attributes-order': 'off',
    },
  },

  {
    files: ['**/*.ts', '**/*.tsx'],
    languageOptions: {
      parser: tsParser,
      ecmaVersion: 2020,
      sourceType: 'module',
    },
    rules: {
      // 类型注解空格规范（如const a: number）
      'stylistic/type-annotation-spacing': [
        'error',
        { before: false, after: true },
      ], // 接口/类型成员用分号分隔，多行末尾需分号
      'stylistic/member-delimiter-style': [
        'error',
        {
          multiline: { delimiter: 'semi', requireLast: true },
          singleline: { delimiter: 'semi', requireLast: false },
        },
      ], // 数组方法回调必须有return
      'array-callback-return': 'error', // 允许any类型
      'stylistic/no-explicit-any': 'off', // 不强制接口属性分号
      'stylistic/interface-property-semi': 'off', // 允许非空断言（!）
      'stylistic/no-non-null-assertion': 'off',
    },
  },

  {
    files: ['**/*.js', '**/*.jsx'],
    languageOptions: {
      ecmaVersion: 2020,
      sourceType: 'module',
      ecmaFeatures: { jsx: true },
    },
    rules: {
      // 箭头函数体可省略{}（如x => x+1）
      'arrow-body-style': ['error', 'as-needed'], // 建议箭头函数作为回调
      'prefer-arrow-callback': 'error', // 避免箭头函数与比较运算符混淆（允许括号包裹）
      'no-confusing-arrow': ['error', { allowParens: true }], // 建议函数表达式（如const fn = () => {}）
      'func-style': ['error', 'expression', { allowArrowFunctions: true }], // 对象字面量使用简写（如{ x, y }）
      'object-shorthand': ['error', 'always'], // 建议用展开运算符（{ ...obj }）替代Object.assign
      'prefer-object-spread': 'error', // 数组方法回调必须有return
      'array-callback-return': 'error', // 禁止使用Array构造函数（用[]替代）
      'no-array-constructor': 'error', // 禁止未声明变量
      'no-undef': 'error', // 禁止修改全局对象（如window）
      'no-global-assign': 'error', // 建议复合赋值（如a += 1）
      'operator-assignment': ['error', 'always'], // 禁止抛出非Error实例
      'no-throw-literal': 'error', // 回调函数必须处理err参数
      'handle-callback-err': ['error', 'err'], // alert降级为警告
      'no-alert': 'warn',
    },
  },
]
```

### prettier

```fold:.prettierc
{
  "printWidth": 80,
  "tabWidth": 2,
  "useTabs": false,
  "semi": false,
  "singleQuote": true,
  "arrowParens": "avoid",
  "endOfLine": "lf",
  "plugins": ["prettier-plugin-tailwindcss"],
  "overrides": [
    {
      "files": ["*.html"],
      "options": { "tabWidth": 4 }
    },
    {
      "files": ["*.css", "*.scss", "*.less"],
      "options": { "singleQuote": false }
    },
    {
      "files": ["*.js", "*.jsx"],
      "options": { "trailingComma": "all", "parser": "babel" }
    },
    {
      "files": ["*.ts", "*.tsx"],
      "options": { "trailingComma": "all", "parser": "typescript" }
    }
  ]
}
```

### prettierignore

```fold:prettierignore
# 构建产物目录
/dist
/build
# 依赖与缓存目录
/node_modules
/.cache
/.eslintcache
# 测试与报告
/coverage
/jest-report
# 锁文件
yarn.lock
package-lock.json
pnpm-lock.yaml
# 压缩/混淆文件
*.min.js
*.min.css
*.min.html
# 二进制/非文本文件
*.svg
*.png
*.jpg
*.jpeg
*.gif
*.ico
*.mp4
*.woff
*.woff2
# 日志/临时文件
*.log
*.tmp
*.swp
```

### valid-component-filename.ts

```ts
export default {
  meta: {
    type: 'problem',
    docs: {
      description:
        '组件文件名必须为 PascalCase（如 UserProfile.vue）或 kebab-case（如 user-profile.vue）',
    },
    fixable: null,
    schema: [],
  },

  create(context) {
    return {
      Program(node) {
        // 获取当前文件路径
        const filePath = context.getFilename() // 兼容正反斜杠获取文件名
        const fileName = filePath.split(/[\\/]/).pop() // 只校验 Vue 组件文件
        const filesReg = [/components.*\.vue$/, /views.*\.vue$/]
        const isMatch = filesReg.some(reg => reg.test(filePath))
        if (!isMatch)
          return // 忽略文件列表，可根据项目结构调整
        const ignoreFiles = ['index.vue', '404.vue', '500.vue']
        if (ignoreFiles.includes(fileName) || ignoreFiles.includes(filePath)) {
          return
        } // 校验规则：PascalCase 或 kebab-case
        const isPascalCase = /^[A-Z][a-zA-Z0-9]*\.vue$/.test(fileName)
        const isKebabCase = /^[a-z]+(?:-[a-z0-9]+)+\.vue$/.test(fileName)
        if (!isPascalCase && !isKebabCase) {
          context.report({
            node,
            message: `组件文件名 "${fileName}" 格式错误，必须为：- PascalCase（如 UserProfile.vue）- kebab-case（如 user-profile.vue）`,
          })
        }
      },
    }
  },
}

```
