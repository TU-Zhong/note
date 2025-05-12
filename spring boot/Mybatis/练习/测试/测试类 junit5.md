在纯 Maven 项目中编写测试类，通常遵循以下约定和步骤，并结合使用像 JUnit 或 TestNG这样的测试框架。这里以最常用的 JUnit 5 为例。

**1. Maven 项目结构约定**

Maven 对测试代码的存放位置有标准约定：

- **`src/main/java`**: 存放你的主应用程序代码。
- **`src/test/java`**: 存放你的测试类代码。
- **`src/main/resources`**: 存放主应用程序的资源文件。
- **`src/test/resources`**: 存放测试时使用的资源文件（例如，测试用的配置文件、示例数据文件等）。

**2. 添加测试框架依赖 (JUnit 5)**

在你的 `pom.xml` 文件中，你需要添加测试框架的依赖。对于 JUnit 5，通常需要以下依赖：

XML

```
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>my-app</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>11</maven.compiler.source> <maven.compiler.target>11</maven.compiler.target> <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <junit.jupiter.version>5.10.2</junit.jupiter.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-api</artifactId>
            <version>${junit.jupiter.version}</version>
            <scope>test</scope> </dependency>

        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-engine</artifactId>
            <version>${junit.jupiter.version}</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-params</artifactId>
            <version>${junit.jupiter.version}</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>3.2.5</version> </plugin>
        </plugins>
    </build>
</project>
```

**关键点:**

- `scope>test</scope>`: 这非常重要，它确保测试相关的库不会被打包到你的最终应用程序产物（如 JAR 或 WAR 文件）中，只在编译和执行测试时使用。
- `maven-surefire-plugin`: 这是 Maven 用来运行单元测试的默认插件。它会自动查找并执行符合特定命名约定的测试类。

**3. 编写测试类**

- **位置**: 在 `src/test/java` 目录下创建你的测试类。包结构通常建议与 `src/main/java` 中被测试类的包结构相对应。例如，如果你的主类是 `com.example.service.MyService`，那么测试类可以是 `com.example.service.MyServiceTest`。
- **命名约定**: Maven Surefire 插件默认会执行以下名称模式的测试类：
    - `*Test.java`
    - `*Tests.java`
    - `*TestCase.java`
    - `Test*.java` 最常用的是 `*Test.java`，例如 `MyServiceTest.java`。
- **测试方法**: 在 JUnit 5 中，测试方法需要使用 `@Test` 注解。

**示例：**

假设你有以下主类 `Calculator.java` 在 `src/main/java/com/example/Calculator.java`:

Java

```
package com.example;

public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }

    public int subtract(int a, int b) {
        return a - b;
    }

    public double divide(int a, int b) {
        if (b == 0) {
            throw new IllegalArgumentException("Cannot divide by zero");
        }
        return (double) a / b;
    }
}
```

你可以为其编写一个测试类 `CalculatorTest.java` 在 `src/test/java/com/example/CalculatorTest.java`:

Java

```
package com.example;

import org.junit.jupiter.api.Test; // 导入 @Test 注解
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import static org.junit.jupiter.api.Assertions.*; // 导入断言方法

class CalculatorTest { // 类名以 Test 结尾

    private Calculator calculator;

    @BeforeEach // 这个方法在每个 @Test 方法执行之前都会执行
    void setUp() {
        calculator = new Calculator(); // 初始化被测试对象
        System.out.println("Setup for new test...");
    }

    @Test // 标记这是一个测试方法
    @DisplayName("测试加法功能：1 + 2 应该等于 3") // (可选) 为测试方法提供一个更易读的名称
    void testAdd() {
        // Arrange (准备)
        int numberA = 1;
        int numberB = 2;
        int expectedResult = 3;

        // Act (执行)
        int actualResult = calculator.add(numberA, numberB);

        // Assert (断言)
        assertEquals(expectedResult, actualResult, "1 + 2 should equal 3");
    }

    @Test
    @DisplayName("测试减法功能")
    void testSubtract() {
        assertEquals(2, calculator.subtract(5, 3), "5 - 3 should equal 2");
        assertEquals(-1, calculator.subtract(2, 3), "2 - 3 should equal -1");
    }

    @Test
    @DisplayName("测试除法功能")
    void testDivide() {
        assertEquals(2.5, calculator.divide(5, 2), "5 / 2 should equal 2.5");
    }

    @Test
    @DisplayName("测试除以零时抛出异常")
    void testDivideByZero_shouldThrowException() {
        // 断言当执行 calculator.divide(1, 0) 时会抛出 IllegalArgumentException 异常
        IllegalArgumentException thrown = assertThrows(
            IllegalArgumentException.class,
            () -> calculator.divide(1, 0),
            "Expected divide by zero to throw IllegalArgumentException, but it didn't"
        );

        // (可选) 进一步断言异常信息
        assertTrue(thrown.getMessage().contains("Cannot divide by zero"));
    }
}
```

**常用 JUnit 5 注解:**

- `@Test`: 标记一个方法为测试方法。
- `@BeforeEach`: 在每个测试方法执行前执行。用于初始化测试环境或对象。
- `@AfterEach`: 在每个测试方法执行后执行。用于清理资源。
- `@BeforeAll`: 在当前测试类的所有测试方法执行前执行一次。方法必须是 `static`。
- `@AfterAll`: 在当前测试类的所有测试方法执行后执行一次。方法必须是 `static`。
- `@DisplayName("自定义测试名称")`: 为测试类或测试方法提供一个更易读的显示名称。
- `@Disabled`: 禁用某个测试类或测试方法。
- `@ParameterizedTest`: 用于参数化测试，可以使用 `@ValueSource`, `@CsvSource`, `@MethodSource` 等提供参数。

**JUnit 5 断言 (Assertions):** JUnit 5 提供了丰富的静态断言方法在 `org.junit.jupiter.api.Assertions` 类中，例如：

- `assertEquals(expected, actual)`
- `assertNotEquals(unexpected, actual)`
- `assertTrue(condition)`
- `assertFalse(condition)`
- `assertNull(object)`
- `assertNotNull(object)`
- `assertSame(expected, actual)` (检查对象引用是否相同)
- `assertNotSame(unexpected, actual)`
- `assertThrows(expectedType, executable)` (检查是否抛出特定类型的异常)
- `assertAll(executables...)` (组合多个断言，所有断言都会被执行，即使前面的失败了)

**4. 运行测试**

- **通过 Maven 命令**: 在项目根目录下打开终端或命令行，执行：
    
    Bash
    
    ```
    mvn test
    ```
    
    Maven 会编译主代码和测试代码，然后 Surefire 插件会执行所有符合命名约定的测试类。测试结果会显示在控制台，并且通常会生成测试报告（例如在 `target/surefire-reports` 目录下）。
    
- **通过 IDE**: 大多数现代 Java IDE (如 IntelliJ IDEA, Eclipse, VS Code with Java extensions) 都能很好地集成 Maven 和 JUnit。你可以直接在 IDE 中右键点击测试类或测试方法来运行它们。IDE 通常会提供更友好的测试结果展示界面。
    

**5. 集成测试 (可选)**

如果你的测试需要与外部系统（数据库、网络服务等）交互，它们通常被认为是集成测试。Maven 有一个专门用于集成测试的插件 `maven-failsafe-plugin`。集成测试通常有不同的命名约定（如 `*IT.java`）并在 Maven 构建生命周期的不同阶段运行。

**总结**

编写测试类的核心步骤：

1. 确保项目是 Maven 项目，并遵循其目录结构。
2. 在 `pom.xml` 中添加 JUnit 5 (或其他测试框架) 的依赖，并确保 `scope` 为 `test`。
3. 在 `src/test/java` 目录下创建测试类，遵循 Surefire 的命名约定 (如 `ClassNameTest.java`)。
4. 使用 `@Test` 注解标记测试方法。
5. 在测试方法中使用断言来验证代码的行为是否符合预期。
6. 使用 `mvn test` 命令或 IDE 来运行测试。

这就是在纯 Maven 项目中编写和运行测试类的基本方法。随着测试需求的复杂化，你可能还会用到 Mockito (用于模拟依赖对象)、AssertJ (提供更流畅的断言) 等其他测试相关的库。