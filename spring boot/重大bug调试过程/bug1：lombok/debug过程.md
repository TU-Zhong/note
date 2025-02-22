1. 简单排查
	- ### pom.xml文件中有没有正确引入依赖
	```xml
	<dependency> <groupId>org.projectlombok</groupId>                        <artifactId>lombok</artifactId> 
	<version>1.18.30</version> <!-- 使用最新版本 --> 
	<scope>provided</scope> </dependency>
	```

	- ### idea有没有安装lombok插件
		1. 插件市场安装lombok插件，确保已启用
		2.  启用注解处理：
		    - <mark style="background: #FFF3A3A6;">打开 File -> Settings -> Build, Execution, Deployment -> Compiler -> Annotation Processors。</mark>
		    - <mark style="background: #FFF3A3A6;">勾选 Enable annotation processing。</mark>
		3. 重启 IDE。
	- ### 显式的编写了setter方法，发现可以识别到yml文件的值<br>
	<br>
	- ### 类中的值如果要使用lombok进行getter()和setter()的方法构造，必须要是private属性<br>
	<br>
	- ### 对目标 .java文件进行了编译，生成了 .class文件，然后使用idea对该.class文件进行<mark style="background: #FFF3A3A6;">反编译</mark>发现这个注解没有生成setter方法和getter()方法，或者说什么都没生成。所以直接断定Lombok组件没有工作

2.到处乱点
# <mark style="background: #FFF3A3A6;">然后就一筹莫展，确实毫无办法，然后就到处乱点，然后在.idea文件夹内发现了compiler.xml文件，于是死马当活马医给deepseek看了一下：</mark>
	
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<project version="4">  
  <component name="CompilerConfiguration">  
    <annotationProcessing>      <profile default="true" name="Default" enabled="true" />  
      <profile name="Annotation profile for demo2" enabled="true">  
        <sourceOutputDir name="target/generated-sources/annotations" />  
        <sourceTestOutputDir name="target/generated-test-sources/test-annotations" />  
        <outputRelativeToContentRoot value="true" />  
        <processorPath useClasspath="false">  
          <entry name="$PROJECT_DIR$/../../../coding learning/java_study/apache-maven-3.9.9/maven_warehouse/org/springframework/boot/spring-boot-configuration-processor/unknown/spring-boot-configuration-processor-unknown.jar" />  
          <entry name="$PROJECT_DIR$/../../../coding learning/java_study/apache-maven-3.9.9/maven_warehouse/org/projectlombok/lombok/1.18.36/lombok-1.18.36.jar" />  
        </processorPath>        <module name="demo2" />  
      </profile>    </annotationProcessing>  </component>  <component name="JavacSettings">  
    <option name="ADDITIONAL_OPTIONS_OVERRIDE">  
      <module name="demo2" options="-parameters" />  
    </option>  </component></project>
```
从你的 `compile.xml` 配置来看，问题出在 **Lombok 注解处理器的路径被手动错误配置**，导致 Lombok 无法正常参与编译过程。以下是关键问题分析及修复步骤：

---

### 🔥 核心问题定位
3. **路径中的 `unknown` 版本号**
   ```xml
   <entry name="$PROJECT_DIR$/.../lombok/unknown/lombok-unknown.jar" />
   ```
   - 这说明你的 Lombok 依赖 **版本未正确解析**（本地 Maven 仓库中未找到实际版本号的 jar 包）。
   - **根本原因**：可能是 `pom.xml` 中 Lombok 依赖未明确指定版本，或者 Maven 未正确下载依赖。

4. **手动指定 Processor Path**
   - 你通过 `<processorPath useClasspath="false">` 强制指定了注解处理器的路径，这会覆盖 Maven/Gradle 的自动配置，导致依赖路径错误。

---

### 🛠️ 修复步骤
#### 1. **删除现有错误配置**
在 IntelliJ IDEA 中：
5. 打开 **File > Settings > Build > Compiler > Annotation Processors**
6. 删除名为 `Annotation profile for demo2` 的自定义配置（或直接取消勾选 `Enable annotation processing` 后重新启用）

#### 2. **修正 `pom.xml` 依赖**
确保 Lombok 依赖包含明确的版本号：
```xml
<!-- pom.xml -->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.30</version> <!-- 指定具体版本 -->
    <scope>provided</scope>
</dependency>
```

#### 3. **清理 Maven 本地仓库**
7. 删除本地仓库中损坏的 Lombok 目录：
   ```bash
   # 路径示例（根据你的实际仓库路径调整）:
   rm -rf ~/.m2/repository/org/projectlombok/lombok/
   ```
8. 在项目根目录执行：
   ```bash
   mvn clean install -U
   ```

#### 4. **重新配置 IDEA 注解处理器**
9. 确保 IDEA 配置恢复为默认状态：
   - **File > Settings > Build > Compiler > Annotation Processors**
   - 勾选 **Enable annotation processing**
   - 勾选 **Obtain processors from project classpath**（关键！不要手动指定路径）
   - 生成目录保持默认的 `target/generated-sources/annotations`
   
   ![Annotation Processors Settings](https://i.imgur.com/5zQJv3x.png)

#### 5. **验证路径**
重新编译后，检查 Lombok 的 jar 路径是否包含正确版本号：
```bash
# 正确路径应该类似:
$PROJECT_DIR$/.../lombok/1.18.30/lombok-1.18.30.jar
```

---

### ✅ 验证是否生效
10. 再次反编译 `test01.class`，应出现以下生成的方法：
   ```java
   public class test01 {
       // 应自动生成以下方法
       public void setUser(String user) { this.user = user; }
       public String getUser() { return user; }
       // 其他 hashCode/equals/toString 方法
   }
   ```

11. 如果仍不生效，尝试在命令行直接编译（绕过 IDE）：
   ```bash
   mvn clean compile
   ```
   观察编译日志中是否包含 Lombok 处理信息。

---

## <mark style="background: #FFF3A3A6;">然后按照它的建议，找到了本地maven的仓库，发现\org\projectlombok\lombok这个里面有三个lombok的jar版本，两个确实有确定的版本，一个是unknown。然后对compiler.xml文件进行更改，将unknown改成了确定的版本。但是还是无法识别那个特殊符号。于是就索性删除了unknown的版本，重新让pom.xml进行刷新，重新下载依赖。然后竟然就可以运行了。</mark>

所以最后的结论是：因为这个spring boot项目构建的时候确实要进行maven仓库，中央仓库地址的改变，所以可能是在一开始构建项目的时候因为远程仓库地址是中央仓库，是国外的，没网，所以没下载好，导致最后都识别不了。
这个原因想破脑袋都想不出来只能说。