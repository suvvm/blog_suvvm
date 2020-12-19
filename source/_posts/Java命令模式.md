---
title: Java命令模式
date: 2020-03-20 18:24:17
categories: 
- Java
tags:
- 设计模式
- 行为模式
---

# Java命令模式

命令模式是一种行为模式，命令模式可将请求者与执行者解耦，请求以命令的形式封装在对象中传递给调用者，由调用者寻找合适的接收者处理。

## 适用场景

- 客户端只知道要发出的指令而不知道指令的具体实施方式
- 在执行命令的过程中想要对命令进行记录撤销等处理
- 只要涉及命令的地方都可以使用命令模式

## 优缺点

- 优点
  - 实现了请求者与执行者解耦降低了系统耦合度
  - 使得添加新命令变得简便
  - 可以实现撤销和恢复操作
- 缺点
  - 针对每一种命令都要实现具体的命令类

##  实现

以遥控器为例，遥控器可以遥控电灯电视等电器，并可以实现撤销操作

创建接收者

```java
public class Light{
    public void on(){
        System.out.println("电灯开启");
    }
    public void off(){
        System.out.println("电灯关闭");
    }
}
```

```java
public class Television{
    public void on(){
        System.out.println("电视开启");
    }
    public void off(){
        System.out.println("电视关闭");
    }
}
```

实现命令接口

```java
public interface Command {
	public void execute();	// execute用于调用接收对象
    public void undo();	// undo为撤销方法
}
```

创建命令

```java
public class NoCommand implements Command {	// 无命令
    public void execute() {
        System.out.println("无命令");
    }
    public void undo() {
        System.out.println("无命令，无法撤销");
    }
}
```

```java
public class LightOnCommand implements Command {	// 开灯
    private Light light;
    public LightOnCommand(Light light) {
        this.light = light;
    }
    public void execute() {
        light.on();
    }
    public void undo() {	// LightOnCommand execute方法为开灯，相应撤销操作为关灯
        light.off();
    }
}
```

```java
public class LightOffCommand implements Command {	// 关灯
    private Light light;
    public LightOffCommand(Light light) {
        this.light = light;
    }
    public void execute() {
        light.off();
    }
    public void undo() {	// LightOffCommand execute方法为关灯，相应撤销操作为开灯
        light.on();
    }
}
```

```java
public class TVOnCommand implements Command {	// 开电视
    private Television tv;
    public TVOnCommand(Television tv) {
        this.tv = tv;
    }
    public void execute() {
        tv.on();
    }
    public void undo() {	// TVOnCommand execute方法为开电视，相应撤销操作为关电视
        tv.off();
    }
}
```

```java
public class TVOffCommand implements Command {	// 关电视
    private Television tv;
    public TVOffCommand(Television tv) {
        this.tv = tv;
    }
    public void execute() {
        tv.off();
    }
    public void undo() {	// TVOffCommand execute方法为关电视，相应撤销操作为开电视
        tv.on();
    }
}
```



创建调用者

```java
public class RemoteControl {
    // 遥控器按钮数量一定，共10个,这里使用数组
    private Cammand[] onButtons;	// 开启命令按钮
    private Cammand[] offButtons;	// 关闭命令按钮
    private Cammand undoCommand;
    public RemoteControl() {
        onCommands = new Command[5];
        offCommands = new Command[5];
        Command noCommand = new NoCommands();
        for(int i = 0; i < 5; i++) {
            onCommands[i] = noCommand;
            offCommands[i] = noCommand;
        }
        undoCommand = noCommand;
    }
    public void setCommand(int solt, Caomand onCommand, Command offCommand) {	// 设置按钮命令
        if (solt < 0 || solt >= 5)
            return;
    	onCommands[solt] = onCommand;
        offCommands[solt] = offComand;
    }
    public void pressOnButton(int solt) {	// 按下开启按钮
        if (solt < 0 || solt >= 5)
            return;
        onCommands[solt].execute();
        undoCommand = onCommands[solt];
    }
    public void pressOffButton(int solt) {	// 按下关闭按钮
        if (solt < 0 || solt >= 5)
            return;
        offCommands[solt].execute();
        undoCommand = offCommands[solt];
    }
    public void pressUndoButton() {	// 按下撤销按钮
		undoCommand.undo();
    }
}
```

实现客户端

```java
public class RemoteControlTest {
    public static void main(String[] args) {
        RemoteControl remote = new RemoteControl();	// 实例化调用者
        // 实例化接收者
        Light light = new Light();	
        Television tv = new Television();
        // 实例化命令指定接收者
        LightOnCommand lightOn = new LightOnCommand(light);	
        LightOffCommand lightOff = new LightOffCommand(light);
        TVOnCommand tvOn = new TVOnCommand(tv);
        TVOnCommand tvOff = new TVOffCommand(tv);
        
        remote.setCommand(0, lightOn, lightOff);	// 把命令传给调用者
        remote.setCommand(1, tvOn, tvOff);	// 把命令传给调用者
        
        remote.pressOnButton(0);
        remote.pressOnButton(1);
        remote.pressOffButton(1);
        remote.pressOffButton(0);
        remote.pressUndoButton();
    }
}
```

## 参考文献

- Head First 设计模式