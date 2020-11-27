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

```shell
rm -rf ~/.java/.userPrefs/jetbrains/pycharm
cd ~/.config/JetBrains/PyCharm2020.2
rm -rf eval
rm -rf options/other.xml
```