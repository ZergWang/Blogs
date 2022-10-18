# C/C++在Windows下的环境配置
### 下载
首先安装minGW的编译器，dev cpp用的正是这个编译器，但一直是32位不说，还一直不更新……而实际上这个编译器有64位，而且一直在更新。

然后VS Code中安装C/C++插件IntelliSense和Code Runner。

### 配置具体环境
VS Code作为多种语言的编译器，不同环境、任务中配置不尽相同，因此建议单开一个目录用作C/C++环境，针对该环境的配置信息也保存在该目录的.vscode下。

以下配置文件中凡涉及路径的，建议全部使用“/”来表示层级关系并用双引号引起，否则可能会带来问题（例如，路径中含有空格会导致完整路径被识别成空格前和空格后的两个参数……）

以下三个配置文件均在.vscode目录中。
#### c_cpp_properties.json
用于设置编译器路径和IntelliSense。本人的配置内容为：
```js
{
    "configurations": [
        {
            "name": "MinGW",
            "intelliSenseMode": "gcc-x64",
            "includePath": [
                "${workspaceFolder}/**"
            ],
            "defines": [],
            "compilerPath": "C:/Softwares/mingw64/bin/g++.exe",
            "cStandard": "c11",
            "cppStandard": "c++17"
        }
    ],
    "version": 4
}
```
其中compilerPath后为本机安装的MinGW中g++的位置。

#### settings.json
用于设置Code Runner等插件及VS Code软件本身，本人为：
```js
{
    "code-runner.saveFileBeforeRun": true,
    "code-runner.fileDirectoryAsCwd": true,
    "code-runner.clearPreviousOutput": true,
    "code-runner.runInTerminal": true,
    "code-runner.preserveFocus": false,
    "code-runner.executorMap": {
        "c": "gcc $fileName -o $fileNameWithoutExt && ./$fileNameWithoutExt",
        "cpp": "g++ $fileName -o $fileNameWithoutExt && ./$fileNameWithoutExt",
    },
}
```
对于VS Code软件本身的设置，只有异于默认设置的，才会被记录到settings.json中。

#### launch.json
配置调试环境，本人为：
```js
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(gdb) Launch",
            "type": "cppdbg",
            "request": "launch",
            "program": "${fileDirname}/${fileBasenameNoExtension}.exe",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": true,
            "MIMode": "gdb",
            "miDebuggerPath": "C:/Softwares/mingw64/bin/gdb.exe",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
            "preLaunchTask": "C++ Debug"
        }
    ]
}
```
miDebuggerPath项填写本机安装的MinGW的gdb.exe的位置即可。

注意externalConsole这一项，如果是true，那么在使用VS Code自带的编译运行和调试（即F5和Ctrl + F5）命令时，会另外弹出终端进行操作，如果为false，则会在VS Code集成的终端中操作。若使用Code Runner运行代码则不受此项影响。

#### tasks.json
编译器相关配置，本人为：
```js
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "C++ Debug",
            "type": "shell",
            "command": "g++",
            "args": [
                "-g",
                "${file}",
                "-o",
                "${fileDirname}/${fileBasenameNoExtension}"
            ],
            "presentation": {
                "reveal": "always",
                "panel": "shared",
                "focus": false,
                "echo": true
            },
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "problemMatcher": {
                "owner": "cpp",
                "fileLocation": "absolute",
                "pattern": {
                    "regexp": "^(.*):(/d+):(/d+):/s+(error):/s+(.*)$",
                    "file": 1,
                    "line": 2,
                    "column": 3,
                    "severity": 4,
                    "message": 5
                }
            }
        }
    ]
}
```
### 编译及运行
安装Code Runner插件的目的是为了使编译运行过程更为便捷。在无该插件的情况下，编译运行需要Debug选单下的Start Without Debugging（Ctrl+F5）或者Start Debugging（F5），然后在终端中输入输出，若一次执行结束，看不到结果，可能还要在程序末尾手动写段按任意键退出的代码，极其智障……

而使用Code Runner，可在VS Code集成的终端中输入输出，上次的运行结果也得以保留，无需写“按任意键退出”的代码了。

在Code Runner存在的情况下，按Ctrl+Alt+N编译运行，或者按右上角的三角形按钮：

![](VS%20Code使用技巧及开发环境配置_1.png)

然后在VS Code集成的终端中输入输出：

![](VS%20Code使用技巧及开发环境配置_2.png)

这里用的例子是一个排序（从大到小）程序，输入3个数：1、2、3，输出3、2、1。

### 调试

![](VS%20Code使用技巧及开发环境配置_3.png)

按F5进行调试，并在左侧调式选单中查看变量相关信息，在WATCH添加要查看的变量。

这里要讲讲数组变量的查看方式（在WATCH中右键Add Expression添加以下代码）：
```
*(int(*)[n])array_name
```
n为要查看的数组范围，array_name为数组名，其实也可以直接添加数组名查看，但如果数组范围比较大，程序就直接崩溃了……

在相应行号前单击设置断点，断点显示为一个红点：

![](VS%20Code使用技巧及开发环境配置_4.png)

使用该面板控制调试过程：

![](VS%20Code使用技巧及开发环境配置_5.png)


Step Into（F10）：执行下一步，遇到函数会单步进入。

Step Over（F11）：执行下一步，遇到函数会将其整个执行完，不会单步进入。

Step Out（Shift+F11）：若执行位置所处函数内无断点，会执行完函数剩余部分并跳出函数，若有则跳到断点。
<br/><br/>

# Python在Windows下环境配置

安装Python插件：

![](VS%20Code使用技巧及开发环境配置_6.png)

和C++一样，建议单开一个目录用作Python环境的模板，相关配置保存在该目录下的.vscode目录中。

#### launch.json
配置Python的调试器：
```js
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Python: Current File",
            "type": "python",
            "request": "launch",
            "program": "${file}",
            "console": "integratedTerminal",
            "pythonPath":"${config:python.pythonPath}"
        }
    ]
}
```
#### settings.json
```js
{
    "python.pythonPath": "C:/Softwares/Anaconda3/envs/ml/python",
    "python.jediEnabled": true,
    
    "code-runner.executorMap": {
        "python": "C:/Softwares/Anaconda3/envs/ml/python",    
    }
}
```
python.pythonPath填入本机的python地址（是不是虚拟环境并不影响），无需.exe。python.jediEnabled是代码补全、函数提示功能，默认关闭，这里要开启它。

如果装了CodeRunner插件，还可以使用CodeRunner运行python程序，将CodeRunner中关于python的解析器改为本机地址即可（与python.pythonPath相同）。

#### pylint
如果安装有pylint，可以用来检查潜在的代码错误（对于可能的bug，pylint会在编译界面中的相应代码下加上波浪线）

配置pylint（在settings.json中额外加入）：
```js
{   
    "python.linting.pylintEnabled": true,
    "python.linting.enabled": true,
    "python.linting.pylintPath": "C:/Softwares/Anaconda3/envs/ml/Scripts/pylint"
}
```

这里的pylint的地址为相应的虚拟环境中搜索到的pylint.exe的地址，把这个地址写进去，去掉.exe即可。
<br/><br/>

# VS Code使用笔记
### 快捷键
#### 注释
选中相应代码块，Ctrl+/，这部分代码就会自动变成注释，再按一次解除~

#### 同时选中相同内容
例如在批量更改变量名时，先选中其中一个变量名，再按Ctrl+Shift+L，即可选中所有同名内容，键入可同时修改。

#### Tab与Shift + Tab
前者为令光标后的代码加一个缩进，后者为使整行代码减一个缩进（无论光标在何处）。
<br/>
### bug
#### 终端输入无反应
在终端（Terminal）中输入命令，发现无反应（或者反复输入一个键多次才能成功输入一次），解决方法：把输入法切回英文……

#### Segmentation fault或cygwin.S的文件问题
Debug C/C++时，若进入函数报Segmentation fault，且报错定位到cygwin.S:126

一般是因为在函数中开辟过大内存空间（例如开了大数组），把数组设为全局变量（在int main()前定义）即可。
