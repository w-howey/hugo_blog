---
title: "java自动化控制鼠标和键盘"
date: 2024-11-06T12:20:16+08:00
draft: false
slug: "202241106"
authors: ["howey"]
series: ["编程系列"]
categories: ["后端"]
tags: ["java"]
---

### 在Java中，可以使用 java.awt.Robot 类来实现对鼠标和键盘的自动化控制。Robot 类提供了模拟鼠标移动、点击、键盘按键等操作的方法。以下是一些常见的用法示例。

### 安装和导入
不需要额外的库，java.awt.Robot 类是Java标准库的一部分，直接导入即可使用。

### 示例代码
##### 1. 控制鼠标
```java
import java.awt.Robot;
import java.awt.event.InputEvent;
import java.awt.event.KeyEvent;

public class MouseControl {
    public static void main(String[] args) {
        try {
            // 创建 Robot 实例
            Robot robot = new Robot();

            // 获取屏幕尺寸
            int screenWidth = (int) java.awt.Toolkit.getDefaultToolkit().getScreenSize().getWidth();
            int screenHeight = (int) java.awt.Toolkit.getDefaultToolkit().getScreenSize().getHeight();
            System.out.println("Screen resolution: " + screenWidth + "x" + screenHeight);

            // 移动鼠标到指定位置
            robot.mouseMove(100, 100);
            System.out.println("Moved mouse to (100, 100)");

            // 单击鼠标左键
            robot.mousePress(InputEvent.BUTTON1_DOWN_MASK);
            robot.mouseRelease(InputEvent.BUTTON1_DOWN_MASK);
            System.out.println("Clicked left mouse button");

            // 双击鼠标左键
            robot.mousePress(InputEvent.BUTTON1_DOWN_MASK);
            robot.mouseRelease(InputEvent.BUTTON1_DOWN_MASK);
            robot.delay(100); // 稍微延迟一下
            robot.mousePress(InputEvent.BUTTON1_DOWN_MASK);
            robot.mouseRelease(InputEvent.BUTTON1_DOWN_MASK);
            System.out.println("Double-clicked left mouse button");

            // 右击鼠标
            robot.mousePress(InputEvent.BUTTON3_DOWN_MASK);
            robot.mouseRelease(InputEvent.BUTTON3_DOWN_MASK);
            System.out.println("Clicked right mouse button");

            // 拖动鼠标
            robot.mousePress(InputEvent.BUTTON1_DOWN_MASK);
            robot.mouseMove(200, 200);
            robot.delay(1000); // 等待1秒
            robot.mouseRelease(InputEvent.BUTTON1_DOWN_MASK);
            System.out.println("Dragged mouse from (100, 100) to (200, 200)");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```
##### 2. 控制键盘
```java
import java.awt.Robot;
import java.awt.event.KeyEvent;

public class KeyboardControl {
    public static void main(String[] args) {
        try {
            // 创建 Robot 实例
            Robot robot = new Robot();

            // 输入文本
            String text = "Hello, World!";
            for (char c : text.toCharArray()) {
                int keyCode = KeyEvent.getExtendedKeyCodeForChar(c);
                robot.keyPress(keyCode);
                robot.keyRelease(keyCode);
                robot.delay(100); // 每个字符之间稍作延迟
            }
            System.out.println("Typed: " + text);

            // 按下和释放 Enter 键
            robot.keyPress(KeyEvent.VK_ENTER);
            robot.keyRelease(KeyEvent.VK_ENTER);
            System.out.println("Pressed Enter key");

            // 按下和释放 Shift 键
            robot.keyPress(KeyEvent.VK_SHIFT);
            robot.keyPress(KeyEvent.VK_A); // 按下大写字母 A
            robot.keyRelease(KeyEvent.VK_A);
            robot.keyRelease(KeyEvent.VK_SHIFT);
            System.out.println("Pressed Shift + A");

            // 组合键 Ctrl + C
            robot.keyPress(KeyEvent.VK_CONTROL);
            robot.keyPress(KeyEvent.VK_C);
            robot.keyRelease(KeyEvent.VK_C);
            robot.keyRelease(KeyEvent.VK_CONTROL);
            System.out.println("Pressed Ctrl + C");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```
##### 代码解释
##### 创建 Robot 实例：

使用 `new Robot()` 创建一个 Robot 实例。
##### 控制鼠标：
```java
robot.mouseMove(x, y)：将鼠标移动到指定的坐标 (x, y)。
robot.mousePress(button) 和 robot.mouseRelease(button)：按下和释放鼠标按钮。button 可以是 InputEvent.BUTTON1_DOWN_MASK（左键）、InputEvent.BUTTON2_DOWN_MASK（中键）或 InputEvent.BUTTON3_DOWN_MASK（右键）。
robot.delay(milliseconds)：暂停指定的毫秒数，用于模拟用户的操作延迟。
```
##### 控制键盘：
```java
robot.keyPress(keyCode) 和 robot.keyRelease(keyCode)：按下和释放键盘按键。keyCode 是按键的虚拟键码，例如 KeyEvent.VK_A 表示字母 A。
KeyEvent.getExtendedKeyCodeForChar(char)：获取字符对应的虚拟键码。
robot.delay(milliseconds)：暂停指定的毫秒数，用于模拟用户的操作延迟。
```
