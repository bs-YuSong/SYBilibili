由于正在学习Swift，而刚好又发现了一个不错的OC版的Bilibili仿写App，于是尝试自己“翻译”成Swift版本。
顺便的，记录一下自己学习Swift的经历。
1.闭包
	闭包表达式的语法： { (parameters) -> returnType in
						statements
					}
	闭包表达式的优化方法有：
		如果可以根据上下文推断出参数类型和返回值类型，可以省略
		单表达式闭包隐式返回，当闭包表达式的内容只有一行代码时，可以省略returen而不会引起歧义。
		参数名缩写，在闭包中，可以通过$0,$1来获取传入闭包的第一个和第二参数，以此类推。
		尾随闭包，当要传入一个很长的函数或者闭包表达式作为最后一个参数时，可以将闭包表达式跟在括号后面，如果函数只有一个参数，可以省略括号。
	捕获值：
		闭包可以在其被定义的上下文中捕获常量或变量。即使定义这些常量和变量的作用域已经不存在，闭包仍然可以在闭包函数体内引用和修改这些值。
		被闭包捕获的值会和闭包一起存在或销毁。如果闭包不被销毁，那么被闭包捕获的值也会一直存在。
	闭包是引用类型：
		也就是说，如果将闭包赋值给两个不同的常量或者变量，	两者指向的其实是一个闭包。
	逃逸闭包：
		当一个闭包在函数内（生成或者传入），但是却在函数返回后才被调用，我们称该闭包从函数中逃逸。如果不想让闭包逃逸，那么我们可以在接收闭包参数时，使用关键字@noespace来指明这个闭包是不允许逃逸的。
		将闭包标注为@noescape使你能够在闭包中隐式的使用self
	自动闭包：
		自动闭包是一种自动创建的闭包，用于包装传递给函数作为参数的表达式。这种闭包不接受任何参数，当它被调用的时候，会返回被包装在其中的表达式的值。这种便利的语法让你能够用一个普通的表达式来代替显式的闭包，从而省略闭包的花括号。
		@autoclosure关键字用来生命传入的参数是自动闭包,@autoclosure暗含了@noescape特性，如果想要闭包可以逃逸需要使用@autpclosure(escaping)


2.属性
	总览：
		Swift的属性主要有3重：
			1>存储属性：存储实例的常量和变量，与类、结构体实例相关
			2>计算属性：通过某种方式计算出来的属性，与类、结构体、枚举的实例相关
			3>类型属性：与类型自身相关
	存储属性：
		1>可以给存储属性提供一个默认值，也可以在初始化方法中进行初始化，常量型属性也可以。
		2>常量类型的结构体实例中的变量型存储属性无法修改，因为结构体是值类型，当一个值类型被标记为常量时，他的所有属性也都是常量。类是引用类型，所以不适用这条。（值类型的赋值是创建一个新的，引用类型的赋值是传引用，并不会创建新的）
		3>懒加载的存储属性要定义为变量，因为常量型属性总需要在初始化方法完成之前有个初始值。（懒加载属性在没有初始化时如果被多个线程同时访问，则不能保证只初始化一次）
		4>与Objective-C不同的是，Swift中的属性不需要一个与之对应的成员变量（例：_property），我们不能直接访问属性的后备存储(backing store)。这种方式避免了混淆不同上下文环境下对值的访问，并将属性简化为单一、明确的声明。
	计算属性：
		1>如果计算属性的setter没有定义一个新值的变量名，则默认为newValue
		2>如果只提供getter，而不提供setter，则该计算属性为只读属性
		3>我们只能声明变量型计算属性，因为它们的值不是固定的
		4>如果计算属性是只读的，则可以不使用get{}
		5>如果setter没有定义新的参数名，则默认使用newValue作为参数名
		6>只读计算属性可以去掉get关键字和花括号，在大括号里直接写。
		7>
	属性观察器：
		1>属性观察器监控和响应属性值的变化。每次属性被设置值的时候就会调用属性观察器，就算新值和旧值相同时也不例外。
		2>除了懒加载的属性外，其他属性都可以设置属性观察器。
		3>可以同时为属性添加一个或全部的属性观察器：willSet（默认参数newValue），didSet（默认参数oldValue）。
		4>父类的属性在子类的构造器中被赋值时，它在父类中的观察器会被调用。
		5>
	全局变量：
		1>全局变量是在函数、方法、闭包活任何类型之外定义的变量。
		2>全局变量、常量都是延迟计算的，不过不需要@lazy关键字。
		3>可以使用属性观察器。
	类型属性：
		1>类型属性是类共有的，类似于C语言中的静态方法
		2>类型属性只能是变量
		3>类型属性一定要有默认值，因为类型属性不能再初始化构造时赋值
		4>存储型类型属性也是延迟初始化的，只在第一次被访问时初始化一次，即使被多个线程同时访问也只初始化一次。
		5>一般使用static来定义类型属性。在为class定义计算类型属性时，可以使用关键字class来支持子类对父类的实现进行重写。（进行重写时可以将只读属性变为读写属性）
		6>


3.枚举
	Swift中的枚举：
		在Swift中枚举是一等类型，采用了很多传统上只被类支持的特性。例如计算型属性，用于提供枚举值附加信息的实例方法。也可以通过定义构造函数来提供一个初始值。可以在原始实现的基础上扩展它们的功能，甚至可以通过遵守协议来提供标准的功能。
	枚举：
		1>枚举类型不再需要设置值就可以直接用于switch的比较
		2>switch和枚举搭配使用时，switch要穷举枚举类型的所有可能，所以如果没有给所有的可能都设置了case，需要用default，不然会报错。
		3>
	关联值：
		1>每个枚举成员的关联值类型可以各不相同
	原始值：
		1>原始值可以是字符串、字符或者任意整型浮点型值。
		2>原始值和关联值不同，原始值是在定义枚举时被预先填充的，对于一个特定的枚举成员，它的原始值始终不对。
		3>在使用原始值为整数或者字符串类型的枚举时，Swift将会自动帮你赋值。当原始值为整数时，向后+1，向前-1
		4>当使用字符串赋值时，每个枚举成员的隐式原始值为该枚举成员的名称。
		5>使用枚举成员的rawValue可以访问原始值。
		6>如果在定义枚举成员时使用了原始值，那么就会自动获得一个初始化方法，Planet（rawValue:）,通过原始值可以获取对应的枚举实例，如果没有则返回nil。
		7>
	递归枚举：
		1>递归枚举是一种枚举类型，它有一个或多个枚举成员使用该枚举类型的实例作为关联值。使用递归枚举时，编译器会插入一个间接层。你可以在枚举成员前加上indirect来表示该成员可以递归。也可以在关键字enum前加上indirect来表示所有枚举成员都是可递归的。


4.类和结构体
	类和结构体的相同点：
		1>定义属性用于存储值
		2>定义方法用于提供功能
		3>定义附属脚本用于访问值
		4>定义构造器用于生成初始化值
		5>通过扩展以增加默认实现功能
		6>实现协议以提供某种标准功能
	类的附加功能：
		1>继承允许一个类继承
		2>类型转换允许在运行时检查和解释
		3>结构器允许一个类实例释放任何其所被分配的资源
		4>引用计数允许对一个类的多次引用
	结构体类型的成员注意构造器：
		1>所有结构体都有一个自动生成的成员逐一构造器。