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

