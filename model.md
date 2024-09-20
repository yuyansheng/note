# model

### 观察者模式

```
//
//  Data.h
//  model
//
//  Created by sanmuzhang on 2024/6/25.
//  Copyright © 2024 Tencent. All rights reserved.
//

#import <Foundation/Foundation.h>

NS_ASSUME_NONNULL_BEGIN
///所有观察者必须实现updata
@protocol DataUpdata <NSObject>

- (void)updata;

@end
///数据来源
@interface Data : NSObject
/// 注册观察者
- (void)registerObserver:(id<DataUpdata>)Observer;
///移除
- (void)removeObserver:(id<DataUpdata>)Observer;
///如果数据发生变化通知观察者
- (void)notifyObservers;

@end

NS_ASSUME_NONNULL_END


//
//  Data.m
//  model
//
//  Created by sanmuzhang on 2024/6/25.
//  Copyright © 2024 Tencent. All rights reserved.
//

#import "Data.h"

@interface Data ()

@property (nonatomic, copy)NSMutableArray *observers;

@end

@implementation Data

- (void)registerObserver:(nonnull id<DataUpdata>)Observer {
    [self.observers addObject:Observer];
}

- (void)notifyObservers {
    for(id<DataUpdata> observer in self.observers){
        [observer updata];
    }
}

- (void)removeObserver:(nonnull id<DataUpdata>)Observer {
    [self.observers removeObject:Observer];
}

- (NSMutableArray *)observers{
    if(!_observers){
        _observers = [NSMutableArray array];
    }
    return _observers;
}

@end

//
//  Observer.m
//  model
//
//  Created by sanmuzhang on 2024/6/25.
//  Copyright © 2024 Tencent. All rights reserved.
//

#import "Observer.h"
#import "Data.h"
///观察者数据改动后从自行读取需要的数据
@interface Observer () <DataUpdata>

@end

@implementation Observer

- (void)updata {
    
}

@end

```

### 装饰者模式

```
//
//  Beverage.h
//  model
//
//  Created by sanmuzhang on 2024/6/25.
//  Copyright © 2024 Tencent. All rights reserved.
//

#import <Foundation/Foundation.h>

NS_ASSUME_NONNULL_BEGIN
///所有类的基类，不实例化
@interface Beverage : NSObject

@property (nonatomic, strong, readonly) NSMutableString *beverageDescription;

- (double) cost;

@end

NS_ASSUME_NONNULL_END

//
//  Condiment.h
//  model
//
//  Created by sanmuzhang on 2024/6/25.
//  Copyright © 2024 Tencent. All rights reserved.
//

#import "Beverage.h"

NS_ASSUME_NONNULL_BEGIN
/// 用来装饰各种Beverage的类的基类
@interface Condiment : Beverage

@property (nonatomic, strong) Beverage *beverage;

- (void)initWithBeverage:(Beverage *)beverage;

@end

NS_ASSUME_NONNULL_END


#import "coffer.h"
///一种饮料，做为基底
@implementation coffer

- (double)cost{
    return .99;
}

@end

///一种调料做为装饰可以包裹饮料
@implementation Mocha

- (void)initWithBeverage:(Beverage *)beverage{
    self.beverage = beverage;
}
- (double)cost{
    return [self.beverage cost] +0.22;
}
@end
用法首先实例化基底之后按照需求使用装饰类一层层包裹饮料
```

### 工厂模式

object c中的类族中就是一种工厂模式

### 单例模式

```
///单例
@implementation MyClass

+ (MyClass *)getInstabce{
    static MyClass *myClass = nil;
    if(!myClass){
        myClass = [[MyClass alloc] init];
    }
    return myClass;
}
@end

+ (MyClass *)getInstabce{
    static MyClass *myClass = nil;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        myClass = [[MyClass alloc] init];
    });
    return myClass;
}
```

### 命令模式

 将命令用对象包裹
 
```
//
//  Command.h
//  model
//
//  Created by sanmuzhang on 2024/6/25.
//  Copyright © 2024 Tencent. All rights reserved.
//

#import <Foundation/Foundation.h>

NS_ASSUME_NONNULL_BEGIN
/// 命令的基类所有命令都继承此类，此类不实例化
@interface Command : NSObject

-(void)execute;

@end

NS_ASSUME_NONNULL_END


#import "LightOnCommand.h"
#import "Light.h"
///开关开命令封装为一个对象
@interface LightOnCommand ()
@property (nonatomic, strong) Light *light;
@end

@implementation LightOnCommand

- (void)execute {
    [self.light on];
}
///命令控制的对象
- (instancetype)initWithLight:(nonnull Light *)light {
    if(self = [super init]){
        self.light = light;
    }
    return self;
}
@end

///需要接受命令的对象
@implementation Light
- (void)on{
    NSLog(@"light is on");
}
- (void)off{
    NSLog(@"light is off");
}
@end


/// 遥控器对象，命令的发起者
@implementation Control

- (void)buttonPressed {
    [self.slot execute];
}

- (void)setCommand:(nonnull Command *)command {
    _slot = command;
}
@end

//使用方法
 Control *remote = [[Control alloc] init];
        Light *light = [[Light alloc] init];
        LightOnCommand *lightOn = [[LightOnCommand alloc] initWithLight:light];
        [remote setCommand:lightOn];
        [remote buttonPressed];

```

### 适配器和外观

```
///鸭子类
@implementation Duck

- (void)fly {
}

- (void)quack {
}
@end
///火鸡类
@implementation Turkey
- (void)fly {
    NSLog("fly short");
}

- (void)quack {
}
@end
```
两者不通用

```
#import "Duck.h"
@class Turkey;
NS_ASSUME_NONNULL_BEGIN

@interface TurkeyAdapter : Duck
@property (nonatomic, strong) Turkey *turkey;
-(void)fly；
-(void)quack；
@end

NS_ASSUME_NONNULL_END
```
使用组合的方式保留一个火鸡对象将所有消息都由火鸡处理并进行适配 这是对象适配器

类适配器是通过多继承的方式来进行适配oc不存在多继承略过

外观模式用处是提供一个更加简单的接口比如如果某项任务需要的流程很繁琐并且使用次数很多，那么你可以将所有的流程封装到另一个类内的方法中，将所有需要用到的子类都委托给另一个类这样可以简化接口，不需要一个个调用。

为子系统中的一组接口提供了一个统一接口。

### 模版方法模式
在一个方法中定义一个算法的骨架，把实现的步骤延迟到子类实现。让子类可以在不改变结构的情况下，重新定义算法中的某些步骤的实现

可以使用缺省实现部分代码由子类确定是否要改写方法

 