---
title: Java外观模式
date: 2020-03-24 18:54:49
categories:
- Java
tags:
- 设计模式
- 结构型模式
---

# Java外观模式

外观模式使一种结构型模式，提供了一个可以使子系统更易使用的高层接口，来访问子系统中的一群接口，隐藏了系统的复杂性。

## 适用场景

再子系统使用方式极度繁琐时可通过外观模式提供更加智能的接口实现使用过程的简化，如在家看电影时涉及对爆米花机、屏幕、灯光、功放、投影仪、DVD播放器的一系列设备的操作，如果有个遥控器，便可以按下放映电影的按钮直接完成所有操作。

## 优缺点

- 优点
  - 减少了系统的依赖性
  - 提高了灵活性
  - 提高了安全性
- 缺点
  - 不符合开闭原则，不易扩展与修改

##  实现

使用家庭影院看电影：

 	3. 开始爆米花
   	2. 打开爆米花机
   	3. 调暗灯光至10%
   	4. 放下屏幕
   	5. 打开投影仪
   	6. 将投影仪输入切换至DVD
   	7. 将投影仪设置为宽屏模式
   	8. 打开功率放大器
   	9. 将功率放大器输入设为DVD
   	10. 将功率放大器设置为环绕立体声
   	11. 将功率放大器音量设为中（5）
   	12. 打开DVD播放器
   	13. 播放DVD

操作极度繁琐，可以为家庭影院构造外观

实现家庭影院子系统组件

```java
家庭影院有
    功率放大器	Amplifier
    调谐器	Tuner
    DVD播放器	DvdPlayer
    CD播放器	CdPlayer
    投影仪	Projector
    灯光影院	TheaterLights
    屏幕	Screen
    爆米花机	PopcornPopper
    
```

由于数量过多不做具体描述

实现家庭影院外观，并创建高级接口

```java
public class HomeTheaterFacade {
    private Amplifier amp;
    private Tuner tuner;
    private DvdPlayer dvd;
    private CdPlayer cd;
    private Projector projector;
    private TheaterLights lights;
    private Screen screen;
    private PopcornPopper popper;
    
    // 在构造器中为组件实例变量赋值对应引用
    public HomeTheaterFacade(Amplifier amp, Tuner tuner, DvdPlayer dvd,
    	CdPlayer cd, Projector projector, TheaterLights lights,
    	Screen screen, PopcornPopper popper;) {
        this.amp = amp;
        this.tuner = tuner;
        this.dvd = dvd;
        this.cd = cd;
        this.projector = projector;
        this.lights = lights;
        this.screen = screen;
        this.popper = popper;
    }
    // 创建高级接口
    public void watchMovie(String movie) {	// 看电影
        System.out.println("Get ready to watch a movie...");
        popper.on();	// 开启爆米花机
        popper.pop();	// 开始爆米花
        lights.dim(10);	// 调暗灯光至10%
        screen.down();	// 放下屏幕
        projector.on();	// 开启投影仪
        projector.setInput(dvd);	// 设置投影仪输入模式为dvd
        projector.wideScreenMode();	// 设置宽屏模式
        amp.on();	// 开启功放
        amp.setDvd(dvd);	// 功率放大器输入设为DVD
        amp.setSurroundSound();	// 功率放大器设置为环绕立体声
        amp.setVolume(5);	// 功率放大器音量设为5
        dvd.on();	// 打开DVD播放器
        dvd.play(movie);	// 播放电影
    }
    
    public void endMovie() {	// 看完电影关闭设备
        System.out.println("Shutting movie theater down...");
        popper.off();	// 关闭爆米花机
        lights.on();	// 灯光至最大
        screen.up();	// 屏幕升起
        projector.off();	// 关闭投影仪
        amp.off();	// 关闭功放
       	dvd.stop();	// 停止播放DVD
        dvd.eject();	// 弹出碟片
        dvd.off();	// 关闭DVD播放器
    }
    // 其他高级接口
}
```

## 参考文献

- Head First 设计模式