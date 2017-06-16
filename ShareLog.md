# 移动端技术交流总结

### 一、IOS编程规范

本次交流内容为IOS编程规范，[交流相关资料](http://7xsbfz.com1.z0.glb.clouddn.com/iOSStandardCodding.pdf)，交流过程中遇到的问题，总结如下：

#### 使用`Switch case`时需注意的几个小点：

  1. 如果`case`后面的内容最好加上`{}`,如果不加的话，那么表达式中的临时变量就会在同一个作用域中，这样在某些情况下，如果跳过了那个定义变量的case，就会出现意向不到的错误。如下所示,最终b输出的是`b=32770`

		      int main()
				{
				 int a ;
				 switch(1){
				 case 0:
				    a =1;
				    int b =2;
				   printf("case 0\n");
				  break;
				 case 1:
				   a = 2;
				   b +=3;
				   printf("b=%d\n",b);
				  break;
				   };
			    }   
		  
  2. 在数据库升级，或者其它某些需要顺序执行，并且不能跳着执行，那么可以把`break`去掉。例如：
		
		
		        switch (dbVersion) { // 获取当前数据库的版本号
		        case -1:
		        {
		            //创建系统消息表
		            [self createSystemMesDB];
		            //NSLog(@"初始化数据库");
		            
		            //创建400电话详情表 & 通话列表
		            [self createPhoneDetailTable];
		            [self createDBinfo];
		            [self setDBVerion:@"1"];
		         
		        }
		        case 1:
		        {
		           //第二版升级数据库需要变更时
		            [self createSpecialClientImMessageTable];
		            [self setDBVerion:@"2"];
		        }
		        case 2:
		        {
		            //下一版需要修改数据库时
		            [self createQuickReplyMessageTable];
		            //创建用户备注的表
		            [self creatRemarkMessageTable];
		            [self setDBVerion:@"3"];
		            
		            
		        }
		        case 3:
		        {
		            //第三版本需要改动的字段
		        }

 这种方式，可以解决连续版本升级也可以解决跨版本升级带来的数据库升级问题。


##### 有副作用的方法

如果一个方法改变了方法外的某个变量，难么就称该方法有`副作用`（side effect），有副作用的方法最好不要有返回值，如果有了返回值，那么这个方法的作用就不是唯一的，该方法的名字就说明不了该方法的作用。如果外面也用到了这个被改变的变量。很容易造成bug，并且很难被发现。[相关资料请见WIKI百科](https://en.wikipedia.org/wiki/Side_effect_(computer_science)
)

##### 编译器语言`\`

`\`是一种编译器语言，编译器遇到`\`就认为该行结束了，如果某行代码很长，其实可以加`\`，然后将剩下的内容在下一行顶格写即可。


##### IOS中的状态枚举和选项枚举

IOS中有两种枚举形式如下
![IOS中的ENUM](http://7xsbfz.com1.z0.glb.clouddn.com/IOSEnum.png)
第一种枚举是状态枚举，某个该枚举值不会同时有多重数据。第二种枚举可以让某个数据同时具有多种情况（比如，某个同学是班长，数学课代表，有是迅雷VIP会员），这个时候就使用选项枚举。具体的选项个数和电脑的位数有关，如：64位机，最多有64个选项。具体的赋值和使用如下所示：
![如何使用ENUM](http://7xsbfz.com1.z0.glb.clouddn.com/USEEnum.png)


##### 单例的规范写法

在使用单例中为了防止单例被非`sharedInstance`之外方法所创建，可以使用如下的创建方式：

     //.h
	   + (instancetype)sharedInstance;
		- (instancetype)init NS_UNAVAILABLE;
		- (instancetype)new  NS_UNAVAILABLE;
	  //.m
	 + (instancetype)sharedInstance {

	    static HYRobOrderWindowManager *robManager = nil;
	    static dispatch_once_t onceToken;
	    dispatch_once(&onceToken, ^{
	        
	        robManager = [super allocWithZone:NULL];
	               
	    });
	    return robManager;
    }

	+ (instancetype)allocWithZone:(struct _NSZone *)zone {
	
	    return [HYRobOrderWindowManager sharedInstance];
	    
	}


#### 集中常用的大小写问题

1. 宏定义全部用大写：例如苹果的`NS_UNAVAILABLE`
2. static用k开头，如：`kImageThumbnailHeight`
3. entern，使用项目的前缀:`HYCacheControllerDidClearCacheNotification.`


#### IOS中的__kindof

如果方法中参数加上`__kindof`，则表明该方法可以返回这个类及其子类，且不会报警告。如下所示:


    - (__kindof NSArray)getArray{

      return [NSMutableArray ]
    }

加__kindof修饰后，该方法的返回值原本是NSArray，但是方法里边却返回了一个NSArray的子类
NSMutableArray，也就是说，加__kindof修饰后，本类及其子类都是返回，调用使用时也可以使用本类或者本类的子类去接收方法的返回值，调用方法如下，使用NSArray和NSMutableArray来接收返回值都是正确的：

	NSArray *array = [instance getArray];
	NSMUtableArray *mutableArray = [instance getArray];
<<<<<<< HEAD
  
  
=======
	
	

## 面向对象的基本原则：

本周我们主要交流了面向对象的六大基本原则：职责单一原则，依赖倒置原则，开放封闭原则，迪米特法则，里氏替换原则，接口隔离原则。交流过程中的主要收获如下：

#### 什么是抽象？

   抽象就是不依赖于其它对象的一种约束，可以理解为接口，每个对象都可以遵守。比如现实社会中的钱，法律，其本身没有任何的意义，只要遵守了它就可以做任何的事情。

#### 依赖倒置原则

  依赖倒置原则的定义：高层模块不依赖于低层模块，他们都应该依赖于抽象。这里就是为了隔离高层模块对于低层模块的依赖(因为可能有一天低层模块会变化)。比如我们的数据库处理类，要是面向实现编程就是具体的某个数据库比如MySql,Oracel,那么有一天换了数据库，拿这些代码就不能用了，JDBC就是使用了依赖倒置的原则，提供一套接口，然后MySql,Oracel就根据自己自身的语法实现这些接口就可以了。这样使用JDBC就不用和具体的数据库相耦合了。


#### 关于SelfManager
 
  在某些特殊的场合View层是可以处理些业务逻辑的，比如点赞功能。这些业务如果把点击事件传到Controller，然后Controller再将结果返回给点击的Button,改变Button的状态，就太麻烦，这个时候可以将网络请求放到View层，然后将结果直接作用于View。但是如果这个请求结果还会改变除了这个View之外的其它View的状态就最好不要在View层做网络请求了。
  
#### 控制反转

   控制反转就是把本来由框架处理的业务交给了Clinet端，这样就会变的更加的灵活。比如我们去餐厅吃饭，本来我们要吃什么菜，只能根据菜单来要。但是如果控制反转，餐厅将da

#### 封装变化
  
   为了将项目变得更加易于维护，更加清晰，减少重复代码。我们通常将可能变化或者已经变化的部分封装起来。比如工厂模式，封装的是对象产生的过程的变化。策略模式封装的就是将算法的变化封装起来，在后续的重构讲解过程中会不断涉及到这些思想。
  

#### Xib和源码的互掉
  
   [黄魏的XibHelper](https://github.com/parallelWorld/PWXibHelper)利用Protocol做为类的标识，在类加载时期利用RunTime，将`initWithCoder:`和`initWithFrame:`中嵌套部分代码加载Xib到自身的ContentView上，实现Xib调用Xib和Code调用Xib的效果。具体代码如下:
 
    NSString *className = NSStringFromClass(self.class);
    
    NSAssert([[NSBundle mainBundle] pathForResource:className ofType:@"nib"],
             ([NSString stringWithFormat:@"没有找到%@.xib文件", className]));
    
    // 加载nib文件，并对view赋值。
    self.contentView = [[[NSBundle mainBundle] loadNibNamed:className owner:self options:nil] firstObject];;
    
    // 模仿ViewController中view的设置。
    NSAssert(self.contentView, @"File's Owner没有设置");
    
    [self addSubview:self.contentView];
    
    // 填充整个view
    self.contentView.translatesAutoresizingMaskIntoConstraints = NO;
    // 添加约束
 

#### MustOverride

   [MustOverride](https://github.com/nicklockwood/MustOverride)框架主要解决了子类必须重写父类方法的问题。相比在父类方法中直接抛异常的做法有如下好处：
   
   1. 如果**子类没有被用到，但是在项目中创建了**,也能在运行时检测出来。
   2. 用一句宏即可完成，操作简单。
   3. 子类调用`super`不会抛异常。
   4. 子类如果被用到了，但是没有覆盖父类的方法，那么会在运行时期检测出来。
 其实现的具体思路是：在编译时在Section中加入需要覆盖的的方法信息，然后类被Load的时候，取出Section中所有的方法，然后利用runTime判断子类中是否是覆盖了这个方法，如果没有覆盖就抛异常。**因为Load方法类被加载到内存中时候自动调用的，所以即使这个子类没有被用到，那么也会做检测。**
 
#### 有疑问:里氏替换原则

子类能够完全替换父类的方法。主要意思是说，子类尽量不要覆盖父类有意义的方法实现，否则在多级继承过程中会造成难以发现的Bug，但是这用情况至今没有遇到，所以不甚理解。




>>>>>>> origin/master


