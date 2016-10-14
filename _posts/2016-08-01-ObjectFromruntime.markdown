---
layout: post
title:  "Object From runtime"
date:   2016-08-01 01:23:32
categories: iOS高级
tags: iOS
description: 
---

七夕快到了

![七夕](http://ooo.0o0.ooo/2016/08/07/57a737853b079.jpg)

# Objective-C  对象

每个Objective-C对象都是一个C语言的结构体

在runtime源码中的runtime.h文件的55行处,有这样一个声明:

```
struct objc_class {
    Class isa  OBJC_ISA_AVAILABILITY;

#if !__OBJC2__
    Class super_class                                        OBJC2_UNAVAILABLE;
    const char *name                                         OBJC2_UNAVAILABLE;
    long version                                             OBJC2_UNAVAILABLE;
    long info                                                OBJC2_UNAVAILABLE;
    long instance_size                                       OBJC2_UNAVAILABLE;
    struct objc_ivar_list *ivars                             OBJC2_UNAVAILABLE;
    struct objc_method_list **methodLists                    OBJC2_UNAVAILABLE;
    struct objc_cache *cache                                 OBJC2_UNAVAILABLE;
    struct objc_protocol_list *protocols                     OBJC2_UNAVAILABLE;
#endif

### } OBJC2_UNAVAILABLE;
```

| 名词 | 说明 |
| :-: | :-: |
| isa | isa指针 |
| super_class | 父类 |
| objc_ivar_list | 实例变量列表 |
| objc_method_list | 方法列表 |
| objc_cache | 缓存 |
| objc_protocol_list | 协议列表 |

**注意**:方法.属性.协议,这些信息都可以在运行时候被改变,这也是 `Category`的实现远离,ivar是实例变量,所以不能被改变,因为如果改变的话会影响到已有的类

笔者以前不了解 ,category开发是什么意思,现在看来可能是不影响原来的类结构的情况下进行编程吧!

说到这里 , 可能就要说一下类的实例成员和属性了

## 变量和属性(property)

```
@interface MyViewController :UIViewController
{
    //实例变量
    UIButton *Button;
}
// 属性 (而且默认还会生成 `_myButton` 实例变量)
@property (nonatomic, retain) UIButton *myButton;
@end
```
实例变量   `self->Button` `_myButton`

属性   `myButton`

调用属性变量的setter getter 方法: `self.myButton`

注:oc语法关于点表达式的说明："点表达式(.),如果点表达式出现在等号 ＝ 左边，该属性名称的setter方法将被调用。如果点表达式出现在右边，该属性名称的getter方法将被调用。"

### 变量

在runtime源码中的runtime.h文件的1646行处,有这样一个声明:

```
struct objc_ivar {
    char *ivar_name                                          OBJC2_UNAVAILABLE;
    char *ivar_type                                          OBJC2_UNAVAILABLE;
    int ivar_offset                                          OBJC2_UNAVAILABLE;
#ifdef __LP64__
    int space                                                OBJC2_UNAVAILABLE;
#endif
}  
```

| 名词 | 说明 |
| :-: | :-: |
| objc_ivar_list | 成员变量列表 |
| objc_ivar | 单个成员变量结构体 |
| ivar_offset | 基地址偏移字节 |


### 属性

在runtime源码中的 objc-runtime-new.h 文件的244行处,有这样一个声明:

```
struct property_t {
    const char *name;
    const char *attributes;
};
```

| 名词 | 说明  |
| :-: | :-: |
| name | 属性名称 |
| attributes | 属性特质 |;

如果使用了属性,编译器会在编译期自动合成访问这些属性的方法,也就是autosynthesis .同时编译器还会生成属性前面加(_)下划线的实例变量名


| 方法 | 说明 | eg. |
| --- | --- | --- |
| @synthesize | 指定实例变量,并且合成setter,getter方法 | @synthesize myButton; |
| @dynamic | 不指定实例变量,不合成setter,getter方法 | @dynamic myButton; |

#### 属性特质(attribute)

属性的特质可以分为4类


| 原子性 | 内存管理 | 读/写权限 | 方法名 |
| :-: | :-: | :-: | :-: |
| atomic| assign | readwriter | getter=< name > |
| nonatomic | strong | readonly | setter=< name > |
|  | weak |  |  |
|  | unsafe_unretained |  |  |
|  | copy |  |  |

每种特质说明:

| 特质 | 说明 |
| :-: | --- |
| atomic | 如果有多个线程同时调用setter的话，不会出现某一个线程执行完setter全部语句之前，另一个线程开始执行setter情况，相当于函数头尾加了锁一样，可以保证数据的完整性,具备atomic特质的获取方法会通过锁定机制来确保其操作的原子性. |
| nonatmic | 禁止多线程，变量保护，提高性能 |
| readwrite | 拥有setter 和 getter 方法 |
| readonly | 属性仅具有getter方法,只有当该属性有@synthesize实现时,编译器才会为其合成获取方法. |
| assign | 基本变量,纯量类型的简单赋值 |
| strong | 赋新值时,方法会保留新值,释放旧值,再将新值赋值. |
| weak | 赋新值时,不保留新值,不释放旧值,当其所指对象销毁时,属性也会被清空(nil) |
| unsafe_unretained | 当所指对象销毁,属性值不会自动清空 |
| copy | 赋值时,设置方法不保留新值,而是拷贝内容. |
| getter=< name > | 指定getter的方法名 |
| setter=< name > | 指定setter的方法名(若属性特征为copy,则在setter方法中也应拷贝对象) |

##### 利用runtime 扫描属性

在runtime源码中的 objc-runtime-new.h 文件的4098行处,有这样一个声明:

```
objc_property_t *
class_copyPropertyList(Class cls, unsigned int *outCount)
{
    if (!cls) {
        if (outCount) *outCount = 0;
        return nil;
    }

    rwlock_reader_t lock(runtimeLock);

    assert(cls->isRealized());
    auto rw = cls->data();

    property_t **result = nil;
    unsigned int count = rw->properties.count();
    if (count > 0) {
        result = (property_t **)malloc((count + 1) * sizeof(property_t *));

        count = 0;
        for (auto& prop : rw->properties) {
            result[count++] = &prop;
        }
        result[count] = nil;
    }

    if (outCount) *outCount = count;
    return (objc_property_t *)result;
}
```
在runtime源码中的 objc-runtime-new.h 文件的3061行处,有这样一个声明:

```
const char *property_getName(objc_property_t prop)
{
    return prop->name;
}

const char *property_getAttributes(objc_property_t prop)
{
    return prop->attributes;
}
```

可以用这些方法获得属性列表,和属性特征

`Student.h`

```
#import <Foundation/Foundation.h>
#import <UIKit/UIKit.h>
@interface Student : NSObject
@property (nonatomic, copy) NSString *Id;
@property (nonatomic, strong) UILabel *lab;
@end
```

导入 `<objc/runtime.h>` `Student.h`

```
unsigned int outCount = 0;
objc_property_t *properties = class_copyPropertyList([Student class],&outCount);
for (NSInteger i = 0; i < outCount; i++) {
    NSString *name = @(property_getName(properties[i]));
    NSString *attributes = @(property_getAttributes(properties[i]));
    NSLog(@"(%@)%@",attributes,name);
}
free(properties);
```

控制台输出:

```
2016-08-06 21:32:33.099 Runtime[3011:300142] (T@"NSString",C,N,V_Id)Id
2016-08-06 21:32:33.099 Runtime[3011:300142] (T@"UILabel",&,N,V_lab)lab
```

##### 利用runtime动态添加属性

在runtime源码中的 objc-runtime.mm 文件的641行处,有这样一个声明:

```
id 
objc_getAssociatedObject(id object, const void *key) 
{
    return objc_getAssociatedObject_non_gc(object, key);
}

void 
objc_setAssociatedObject(id object, const void *key, id value, 
                         objc_AssociationPolicy policy) 
{
    objc_setAssociatedObject_non_gc(object, key, value, policy);
}
```

`Student.m`

```
// 定义关联的key
static const char *key = "name";

@implementation Student

- (NSString *)name
{
    // 根据关联的key，获取关联的值。
    return objc_getAssociatedObject(self, key);
}

- (void)setName:(NSString *)name
{
    // 添加关联对象  关联的key  关联的value  关联属性特征值
    objc_setAssociatedObject(self, key, name, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
}

@end
```

`Student.h`

```
@interface Student : NSObject
- (NSString *)name;
- (void)setName:(NSString *)name;
```

调用

```
Student *stu = [[Student alloc] init];
stu.name = @"学生";
NSLog(@"%@",stu.name);
```

输出台输出:

```
2016-08-06 23:15:06.491 Runtime[3256:320704] 学生
```

## 方法

在runtime源码中的runtime.h文件的1665行处,有这样一个声明:

```
struct objc_method {
    SEL method_name                                          OBJC2_UNAVAILABLE;
    char *method_types                                       OBJC2_UNAVAILABLE;
    IMP method_imp                                           OBJC2_UNAVAILABLE;
}                                                            OBJC2_UNAVAILABLE;

struct objc_method_list {
    struct objc_method_list *obsolete                        OBJC2_UNAVAILABLE;

    int method_count                                         OBJC2_UNAVAILABLE;
#ifdef __LP64__
    int space                                                OBJC2_UNAVAILABLE;
#endif
    /* variable length structure */
    struct objc_method method_list[1]                        OBJC2_UNAVAILABLE;
}  
```

| 方法 | 说明 |
| --- | --- |
| method_name | 方法(选择器)的名字 |
| method_types | 存储方法的参数类型和返回值类型 |
| method_imp  | 一个指向某个函数的指针 |



#### 利用runtime动态扫描方法

```
unsigned int outCount = 0;
Method *method = class_copyMethodList([Student class], &outCount);
for (NSInteger i = 0; i < outCount; i++) {
    SEL sel = method_getName(method[i]);
    NSString *name = @(sel_getName(sel));
    NSLog(@"%@",name);
        
}
free(method);
```

输出台输出:

```
2016-08-07 07:04:36.662 Runtime[4058:381012] Id
2016-08-07 07:04:36.662 Runtime[4058:381012] setId:
2016-08-07 07:04:36.662 Runtime[4058:381012] lab
2016-08-07 07:04:36.662 Runtime[4058:381012] setLab:
2016-08-07 07:04:36.662 Runtime[4058:381012] .cxx_destruct
2016-08-07 07:04:36.662 Runtime[4058:381012] name
2016-08-07 07:04:36.662 Runtime[4058:381012] setName:
```

.cxx_destruct方法原本是为了C++对象析构的，ARC借用了这个方法插入代码实现了自动内存释放的工作


## 消息机制

objc_msgSend  消息发送的步骤:

1. 检查当前类缓存中是否有方法实现,有则直接调用 return 结束步骤
2. 比较当前类定义中选择器和请求的的选择器,有则直接调用 return 结束步骤
3. 查找父类定义的方法,找到调用方法实现,如果未找到,并依次查找父类的父类, 有则直接调用 return 结束步骤
4. 未找到的类方法调用 `+(BOOL)resolveClassMethod:(SEL)sel` 未找到的实例方法调用 `+(BOOL)resolveInstanceMethod:(SEL)sel` 可以在这里添加方法(`class_addMethod()`) 并且返回YES 重新响应方法 (没有的话,返回NO 继续步骤5)
5. 如果未找得到方法 则会调用 `-(id)forwardingTargetForSelector:(SEL)aSelector` (没有的话,返回nil 继续步骤6)
6. 如果未找得到方法 则会调用 `-(NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector`  返回一个`NSMethodSignature` 对象,传递给 ` -(void)forwardInvocation:(NSInvocation *)anInvocation  ` (没有的话,返回nil 继续步骤7)
7. 调用 `-(void)doesNotRecognizeSelector:(SEL)aSelector` 抛出异常


注: **返回方法不要带有self ,会陷入死循环**
注: **类缓存方法说明详见[查看](http://www.cocoachina.com/ios/20150818/13075.html)**

### 动态消息

 说到消息,就不得不说一个方法 
 
 ```
class_addMethod(<#__unsafe_unretained Class cls#>, <#SEL name#>, <#IMP imp#>, <#const char *types#>)
 ```
这是利用runtime给一个类添加新方法,需要填写的4个参数是:
1. 类
2. 方法名
3. 指向方法函数的指针
4. 变量类型

### 动态添加方法

 
`Student.m`

```
// 设置方法名
static SEL selName(NSString* selname){
    NSString *name = [selname copy];
    return NSSelectorFromString(name);
}
// 设置方法
static id selIMP(id self , SEL _cmd){
    NSString *name = NSStringFromSelector(_cmd);
    NSLog(@"%@",name);
    return name;
}
+ (BOOL)resolveInstanceMethod:(SEL)sel
{
    
    if (sel == @selector(addmethod:)) {
        class_addMethod(self, selName(@"addmethod:"), (IMP)selIMP, "@@:");
    }
    return [super resolveInstanceMethod:sel];
}
```
注: class_addMethod 第四个参数的声明 `"返回值类型+参数类型"`
上面方法中: `static id selIMP(id self , SEL _cmd)`

对照下面

Objective-C类型编码


| 编码 | 含义 |
| :-: | :-: |
| c | char |
| i | int |
| s | short |
| l | long  在64位程序中，l为32位 |
| q | long long |
| C | unsigned char |
| I | unsigned int |
| S | unsigned short |
| L | unsigned long |
| Q | unsigned long long |
| f | float |
| d | double |
| B | C++标准的bool或者C99标准的_Bool |
| v | void |
| * | 字符串（char *） |
| @ | 对象（无论是静态指定的还是通过id引用的） |
| # | 类（Class） |
| : | 方法选标（SEL） |
| [array type] | 数组 |
| {name=type...} | 结构体 |
| (name=type...) | 联合体 |
| bnum | num个bit的位域 |
| ^type | type类型的指针 |
| ? | 未知类型（其它时候，一般用来指函数指针） |


对应:@(返回值)+@(id self)+ : ( SEL _cmd)  

### 动态调用方法

方法已经在检测不到的情况下会自动添加了,那么如何调用呢?

```
Student*st = [[Student alloc]init];
[st performSelector:@selector(addmethod:) withObject:@"addmethod"];
```

输出台输出:

```
2016-08-07 12:07:29.940 Runtime[4731:521045] addmethod:
```

### 将消息转出某对象

```
- (id)forwardingTargetForSelector:(SEL)aSelector
{
    NSLog(@"_cmd: %@", NSStringFromSelector(_cmd));

    Student *st = [[Student alloc] init];
    if ([st respondsToSelector: aSelector]) {
        return st;
    }
    
    return [super forwardingTargetForSelector: aSelector];
}
```

### 方法签名和调用

NSInvocation 里面有目标,选择器,还有方法签名

`receiver` 目标是接收消息的对象


`selector` 选择器是一个选择器或者选择器的名字

例如刚才的 `@selector(addmethod:)`

`NSMethodSignature` 签名 

例如:这是 `Student*st = [[Student alloc]init];` 的签名,返回类型与上文中Objective-C类型编码相同

```
NSMethodSignature *sig = [NSMethodSignature signatureWithObjCTypes:"@@:"];
```
注:`@selector(init)` 一个返回对象+传入对象self+SEL =  `@@:`

定义一个方法

```
-(int)a:(int)a andb:(int)b{
    return a+b;
}
```

NSInvocation 用指定对象调用方法

```
SEL myMethod = @selector(a:andb:);
//从类中请求实例方法签名
NSMethodSignature * sig  = [[self class] instanceMethodSignatureForSelector:myMethod];
//从类中请求类方法签名
//NSMethodSignature * sig  = [[self class] methodSignatureForSelector:myMethod];
NSInvocation * invocatin = [NSInvocation invocationWithMethodSignature:sig];
int a=11;
int b=22;
int c=00;
ViewController *selff = self;
[invocatin setArgument:&selff atIndex:0];
[invocatin setArgument:&myMethod atIndex:1];
[invocatin setArgument:&a atIndex:2];
[invocatin setArgument:&b atIndex:3];
[invocatin retainArguments];
[invocatin invoke];
//取这个返回值
[invocatin getReturnValue:&c];
NSLog(@"%d",c);
```

控制台输出:

```
2016-08-07 20:22:40.490 Runtime[6015:691660] 33
```

注 :如果还记的上文的 自定义的SEl `static id selIMP(id self , SEL _cmd)` 当时说第一个参数是id  第二个参数是方法,也就是IMP的指针原型是:

`id (*IMP)(id,SEL,...)`

IMP 是一个函数指针，这个被指向的函数包含一个接收消息的对象id(self  指针), 调用方法的选标 SEL (方法名)，以及不定个数的方法参数，并返回一个id

所以一般的NSInvocation的实例设置 receiver 和SEL的方式就是:

```
[invocatin setArgument:&selff atIndex:0];
[invocatin setArgument:&myMethod atIndex:1];
```

当签名函数参数数量大于被调函数时，也是没有问题.

其实 `NSInvocation` 和上文中的动态方法调用 `[st performSelector:@selector(addmethod:) withObject:@"addmethod"];` 很像,但是perform相关的这些函数，有一个局限性，其参数数量不能超过2个,`NSInvocation` 可以任意数量

### black magic

还记得这个吗?

```
class_addMethod(<#__unsafe_unretained Class cls#>, <#SEL name#>, <#IMP imp#>, <#const char *types#>)
```

第二个参数和第三个参数分别是方法名,和指向方法函数的指针

在runtime源码中的 objc-runtime-new.mm 文件的2992行处,有这样一个声明:

```
void method_exchangeImplementations(Method m1, Method m2)
{
    if (!m1  ||  !m2) return;

    rwlock_writer_t lock(runtimeLock);

    if (ignoreSelector(m1->name)  ||  ignoreSelector(m2->name)) {
        // Ignored methods stay ignored. Now they're both ignored.
        m1->imp = (IMP)&_objc_ignored_method;
        m2->imp = (IMP)&_objc_ignored_method;
        return;
    }

    IMP m1_imp = m1->imp;
    m1->imp = m2->imp;
    m2->imp = m1_imp;


    // RR/AWZ updates are slow because class is unknown
    // Cache updates are slow because class is unknown
    // fixme build list of classes whose Methods are known externally?

    flushCaches(nil);

    updateCustomRR_AWZ(nil, m1);
    updateCustomRR_AWZ(nil, m2);
}
```

那么就很好理解了,他是把方法的IMP交换了

声明两个方法

```
-(int)a:(int)a andb:(int)b{
    return a+b;
}
-(int)b:(int)b anda:(int)a{
    return b-a;
}
```

然后进行交换

```
Method Getter1 = class_getInstanceMethod([self class], @selector(a:andb:));
Method Getter2 = class_getInstanceMethod([self class], @selector(b:anda:));
method_exchangeImplementations(Getter1, Getter2); 
NSLog(@"%d", [self a:10 andb:20]);
```

控制台输出:

```
2016-08-07 21:05:33.675 Runtime[6202:709996] -10
```

runtime是OC最重要的也是最核心的语法,Objective-C runtime可以有效的帮助我们为程序增加很多动态的行为,这也是OC被称为动态语言的原因!!



