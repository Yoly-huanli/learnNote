[toc]

# 背景

+ vue cli提供的项目模版是面向所有开发者的，只有最基础的配置
+ 面向国际化场景，每次创建新的项目时， 都必须进行vue-i18n等的重复配置

# 自定义模版文件简介

+ 新建preset.json文件，先用 `vue create` 去创建一个项目，然后把预设信息保存下来，到 `~/.vuerc` 中，把刚保存的预设信息复制到 `preset.json`

  ```json
  {
    "useConfigFiles": true,
    "plugins": {
      "@vue/cli-plugin-babel": {},
      "@vue/cli-plugin-router": {
        "historyMode": true
      },
      "@vue/cli-plugin-vuex": {},
      "@vue/cli-plugin-eslint": {
        "config": "prettier",
        "lintOn": [
          "save",
          "commit"
        ]
      }
    },
    "cssPreprocessor": "less"
  }
  ```

+ 新建prompts.js文件， 这个文件用来进行问题问答

  ```
  module.exports = []
  ```

+ 新建generator.js文件，`extendPackage` 方法是给 `package.json` 添加命令和开发与生产环境的包；再调用 `render` 方法去复制template中的模版

+ 项目模版复制到 template 中，然后删除 `package.json` 文件，对于以 **`.`** 开头的文件，改成 **`_`** ，例如 `.eslintrc.js` ==》`_eslintrc.js` , 最后把项目上传到gitHub

+ 示例教程https://juejin.cn/post/6844904099821912077：

  ```
  vue create --preset luokaibin/iframe-child-template your-project-name
  ```

  

  