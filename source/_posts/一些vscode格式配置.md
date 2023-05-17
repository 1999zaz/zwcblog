---
title: 一些vscode格式配置
banner:
  type: img
  bgurl: https://pic1.zhimg.com/v2-b3c2c6745b9421a13a3c4706b19223b3_r.jpg
cover: >-
  https://tse3-mm.cn.bing.net/th/id/OIP-C.fnmA2ubzvBQYO1oj6yixuAHaE-?pid=ImgDet&rs=1
categories: 学习之路
date: 2022-03-02 14:35:00
---

## 脚手架创建完项目后 
###  在.vscode目录下新建settings.json
  ```bash
  {
    "[typescript]": {
      "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[vue]": {
      "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "eslint.enable": true,
    "eslint.run": "onType",
    "eslint.options": {
      "extensions": [
        ".js",
        ".ts",
        ".vue",
        ".jsx",
        ".tsx",
      ]
    },
    // 操作时作为单词分隔符的字符
    "editor.wordSeparators": "`~!@#%^&*()=+[{]}\\|;:'\",.<>/?",
    // 一个制表符等于的空格数
    "editor.tabSize": 2,
    // 保存时格式化
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
      "source.fixAll": true,
      "source.fixAll.eslint": true
    },
    // 文件行尾符号,不需要记，甚至不需要了解，CRLF是window换行\r\n, LF是linux/git 换行\n
    "files.eol": "\n",
    // 是否以紧凑形式展示文件夹
    "explorer.compactFolders": false
  }
  ```

  ### 根目录下新建.prettierrc.json或.prettierrc
  ```bash
     {
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "printWidth": 80
}
  ```
  ### 根目录下新建.editorconfig
  ```bash
     # editorconfig.org
root = true

[*]
charset = utf-8
indent_style = space
indent_size = 2
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true
quote_type = single
  ``` 
  