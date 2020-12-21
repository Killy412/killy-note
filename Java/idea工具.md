### [IDEA 显示Run Dashboard窗口的2种方式](https://www.cnblogs.com/panchanggui/p/10811446.html)

在.idea文件夹下面找到workspace.xml文件，添加以下标签

```xml
<component name="RunDashboard">
    <option name="configurationTypes">
      <set>
        <option value="SpringBootApplicationConfigurationType" />
      </set>
    </option>
  </component>
```

### 重置试用期

- linux

```shell
rm -rf ~/.java/.userPrefs/jetbrains/pycharm
cd ~/.config/JetBrains/PyCharm2020.2
rm -rf eval
rm -rf options/other.xml
```

- windows

```shell
cd "C:%HOMEPATH%\.IntelliJIdea*\config"
rmdir "eval" /s /q
del "options\other.xml"
reg delete "HKEY_CURRENT_USER\Software\JavaSoft\Prefs\jetbrains\idea" /f
```

  

### 常用插件

- Translation   翻译
- Save Actions  自动格式化代码
- One Dark Theme  主题
- Rainbow Brackets  括号变色
- Maven Helper
- Key Promoter X  快捷键
- Jrebel    热部署
- CodeGlance   文件缩略图
- Alibaba Java Coding Guilelines   阿里编码规约
- Alibaba cloud Toolkit          云部署工具
- Free Mybatis plugin   mybatis插件
- GsonFormat         json格式化工具
- RestfulTool              mvc路径映射搜索工具
- RestfulToolkit          同上
- GenerateAllSetter         一键设置所有属性
- GenerateSerialVersionUID
- ASM bytecode viewer
- .env files support
- Elasticsearch