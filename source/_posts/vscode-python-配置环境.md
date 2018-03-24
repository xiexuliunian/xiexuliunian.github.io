---
title: vscode python 配置环境
date: 2017-12-07 09:19:09
tags:
---
# <center>vscode python 的配置环境</center>
* 以下的配置仅针对，python vscode 
## 第一步，安装好vscode,安装好python
## 第二步，在vscode中配置自己安装的python环境，由于我的环境采用了python虚拟环境，需要在vscode中说明。在设置的python configration中进行设置。如下图
<center>{% asset_img 1.png 说明 %}</center>

## 这里设置是，python的虚拟环境绝对路径，尽量不要添加空格，本文中由于之前添加空格了，如图，由于许多的开发环境都在里面，就很难改了。
<center>{% asset_img 2.png 说明 %}</center>

## 第三步，在vscode中新建一个python文件，按`ctrl`+`shift`+`B`来运行的话，会让你配置一个`tasks.json`的文件，由于有两个版本，这里就放两种写法。
```json
{
	"version": "0.1.0",
	// "windows": {
	// 	"command": "python"
    // },
	// "command": "${config:python.pythonPath}",
	"command": "python",
	"isShellCommand": true,
	"showOutput": "always",
	"args": ["${file}"],
	"options": {
        "env": {
            "PYTHONIOENCODING": "UTF-8"
        }
    }
}
```
``` json
// {
//     // See https://go.microsoft.com/fwlink/?LinkId=733558
//     // for the documentation about the tasks.json format
//     "version": "2.0.0",
//     "tasks": [
//         {
//             "label": "Run Python Code",
//             "type": "shell",
//             "command": "python",
//             "args": [
//                 "'${file}'"
//             ],
//             "group": {
//                 "kind": "build",
//                 "isDefault": true
//             },
//             "presentation": {
//                 "echo": true,
//                 "reveal": "always",
//                 "focus": true,
//                 "panel": "shared"
//             }
//         }
//     ]
// }
```
* 注意，请尽量不要在绝对路径里面加空格，不然很麻烦。


