---
layout: post

title: java算法常用的Stream

category: usually

tags: usually

description: java算法常用的Stream 

keywords: Md5

score: 5.0

coverage: libra_coverage.png

published: true


---

# java算法常用的Stream

> # 1. Stream流
>
> Stream [Api]是「集合操作」的一种简化表达形式。其特点是惰性求值，流在中间处理过程中，只是对操作进行了记录，并不会立即执行，需要等到执行终止操作的时候才会进行实际的计算。
>
> 参考Java 8 stream的详细用法，其分类如下：
> ![在这里插入图片描述](/assets/imgs/61c19ae42bb7442a8c125a2055cb3562.png)
>
> - 无状态：指元素的处理不受之前元素的影响；
> - 有状态：指该操作只有拿到所有元素之后才能继续下去。
> - 非短路操作：指必须处理所有元素才能得到最终结果；
> - 短路操作：指遇到某些符合条件的元素就可以得到最终结果，如 A || B，只要A为true，则无需判断B的结果。`
>
> **创建方法：**
>
> 1. Collection（包括List、Set、Map键和值）下的 stream() 和 parallelStream() 方法获取流
>
> ```java
> List<String> list = new ArrayList<>();
> Stream<String> stream = list.stream(); //获取一个顺序流
> Stream<String> parallelStream = list.parallelStream(); //获取一个并行流
> 123
> ```
>
> 1. 数组获取流
>
> ```java
> Integer[] nums = new Integer[10];
> Stream<Integer> stream = Arrays.stream(nums);
> 还可以
> Stream<String> stream = Stream.of(nums)
> 1234
> ```
>
> 1. 使用Stream中的静态方法：of()、iterate()、generate()
>
> ```java
> Stream<Integer> stream = Stream.of(1,2,3,4,5,6);
>  
> Stream<Integer> stream2 = Stream.iterate(0, (x) -> x + 2).limit(6);
> stream2.forEach(System.out::println); // 0 2 4 6 8 10
>  
> Stream<Double> stream3 = Stream.generate(Math::random).limit(2);
> stream3.forEach(System.out::println);
> 1234567
> ```
>
> 常用操作：
> Collection容器转换成数组：将`Collection<Integer>`类型的容器转换为`IntStream`，然后再用`IntStream.toArray()`方法将`IntStream`转换为int[]数组。
>
> ```java
> int[]= list.stream().mapToInt(x->x).toArray();
> int[]= stack.stream().mapToInt(x->x).toArray();
> 12
> ```
>
> # 2. Lambda表达式
>
> Lambda表达式是「创建匿名内部类对象」的一种简化方式。
>
> Lambda表达式常见的用法就是将其创建的对象作为参数传递给方法。
>
> ## 函数式接口
>
> Lambda表达式的目标类型必须是「函数式接口」，函数式接口是只包含一个抽象方法的接口，可以使用`@FunctionalInterface 注解`进行检查。函数式接口可以包含多个默认方法、类方法，但仅能声明一个抽象方法。感性理解来看，实现接口中的抽象方法，我们就能使用该被实现的接口创建匿名内部类对象，正是对这个过程进行简化。例如：
>
> ```java
> //自定义函数式接口
> @FunctionalInterface
> interface eat {
>   void eatFood();
> }
> 
> 
> public static void main(String[] args) {	
> 	//override实现抽象方法，创建匿名内部类
>     eat e1 = new eat(){
> 	  	@Override
> 	  	public void eatFood(){
> 	  		System.out.println("传统方法创建对象");
> 	  	}
>   	};
>   	e1.eatFood();
>   
>    //lambda表达式进行匿名内部类创建简化
>    eat e2 = () -> System.out.println("lambda方式创建对象");
>    e2.eatFood();
> }
> 123456789101112131415161718192021
> ```
>
> **自定义函数式接口：**
>
> ```java
> @FunctionalInterface //此注解用来表明是函数式接口
> public interface MyInterface<T> {
> 	//函数式接口中只能有一个抽象方法
> 	void getValue(T t);
> }
> 
> //自定义函数的Lambda表达式实现
> MyInterface<String> myinter= (x)-> System.out.println(x);
> 12345678
> ```
>
> **JDK中常用的函数式接口：**
> ![在这里插入图片描述](/assets/imgs/c5514a701a354331aa437b362cb48a05.png)
> ![在这里插入图片描述](/assets/imgs/707960a99ffe4ba5a09e4ffcda32c7e3.png)
> 常见的还有`Comparator<T>`
>
> ## Lambda书写语法
>
> lambda表达式的语法格式如下：
>
> ```java
> (实现的这个接口中的抽象方法中的形参列表parameters) -> 抽象方法的处理expression;
> 或
> (实现的这个接口中的抽象方法中的形参列表parameters) ->{抽象方法的处理 statements; };
> 123
> ```
>
> - 可选参数类型声明：可以不声明参数类型。
> - 可选的参数圆括号：一个参数无需定义圆括号。
> - 可选的方法体大括号：expression只有一条语句，可以不用大括号。
> - 可选的return关键字：expression只有一个返回值，可以不写返回关键字。
>
> **无返回值的抽象方法：**
>
> ```java
> //抽象方法无返回值
> public interface MyInterface {
>     public abstract void show(int a,int b);
> }
> 
> 
> public class MyTest1 {
>     public static void main(String[] args) {
>     	//传统的匿名内部类中重写方法
>         MyInterface myInter = new MyInterface() {
>             @Override
>             public void show(int a, int b) {
>                 System.out.println(a + b);
>             }
>         };
>         myInter.show(20, 30);//50
> 		
>         //简写1：标准的lambda简化，抽象方法实现
>         MyInterface myInter1 = (int a, int b) -> {
>             System.out.println(a + b);
>         };
>         myInter1.show(20, 40);//60
> 
>         //简写2：省略形参列表中的形参类型
>         MyInterface myInter2 = (a, b) -> {
>             System.out.println(a + b);//70
>         };
>         myInter2.show(20, 50);
> 
>         //简写3：方法体中只有一行代码，进行简化
>         MyInterface myInter3 = (a, b) -> System.out.println(a + b);
>         myInter3.show(20, 60);//80
>     }
> }
> 12345678910111213141516171819202122232425262728293031323334
> ```
>
> **有返回值的抽象方法：**
>
> ```java
> //抽象方法有int返回值
> public interface MyInterface {
>     public abstract int test(int a,int b);
> }
> 
> public class MyTest2 {
>     public static void main(String[] args) {
>     	//传统的匿名内部类写法
>         MyInterface test1 = new MyInterface() {
>             @Override
>             public int test(int a, int b) {
>                 return a - b;
>             }
>         };
>         System.out.println(test1.test(90, 8));//82
> 
>         //简写1：标准的lambda简化，抽象方法实现
>         MyInterface test2 = (int a, int b) -> {
>             return a - b;
>         };
>         System.out.println(test2.test(20, 10));//10
> 
>         //简写2：省略形参列表中的形参类型
>         MyInterface test3 = (a, b) -> {return a - b;};
>         System.out.println(test3.test(30, 10));//20
> 
>         //简写3：方法中只有一行代码，可以简化，同时去掉return关键字
>         MyInterface test4 = (a, b) -> a - b;
>         System.out.println(test4.test(40, 10));//30
>     }
> }
> 
> 1234567891011121314151617181920212223242526272829303132
> ```
>
> **只有一个形参的抽象方法：**
>
> ```java
> //抽象方法只有一个形参
> public interface MyInterface {
>     public abstract int show(int a);
> }
> 
> public class MyTest3 {
>     public static void main(String[] args) {
>     	//传统的匿名内部类写法
>         MyInterface myInter = new MyInterface(){
>         	@Override
>         	public int show(int a){
> 				return a-20;
> 			}
>         }; 
>         System.out.println(myInter.show(20););//0
> 		
> 		//简写1：标准的lambda简化，抽象方法实现
> 		MyInterface myInter1= (int a)->{
> 			return a-20;
> 		};
> 		System.out.println(myInter1.show(30););//10
> 		
> 		//简写2：省略形参列表中的形参类型
> 		MyInterface myInter2=(a)->{
> 			return a-20;
> 		};
> 		System.out.println(myInter2.show(40););//20
> 
> 		 //简写3：方法中只有一行代码，可以简化，同时去掉return关键字
> 		 MyInterface myInter3=(a)->a-20;
> 		 System.out.println(myInter3.show(50););//30
> 
> 		//简写4:一个形参可以不写圆括号
> 		MyInterface myInter4=a->a-20;
> 		System.out.println(myInter4.show(50););//30
>     }
> }
> 12345678910111213141516171819202122232425262728293031323334353637
> ```
>
> ## 方法引用
>
> 当Lambda方法体中的操作，已经有别的对象或类实现了，就可以尝试引用已实现的方法。
>
> 分类：
>
> | 种类         | 语法           | 说明                                                         | 标准Lambda表达式                    |
> | ------------ | -------------- | ------------------------------------------------------------ | ----------------------------------- |
> | 静态方法引用 | 类名::静态方法 | 函数式接口中「被实现的方法」的全部参数传给该方法作为参数     | (a,b,...) -> 类名.静态方法(a,b,...) |
> | 实例方法引用 | 对象::实例方法 | 函数式接口中「被实现的方法」的全部参数传给该方法作为参数     | (a,b,...) -> 对象.实例方法(a,b,...) |
> | 对象方法引用 | 类名::对象方法 | 函数式接口中「被实现的方法」的第一个参数作为调用者，后面的参数全部传给该方法作为参数 | (a,b,...)->a.对象方法(b,...)        |
> | 构造方法引用 | 类名::new      | 函数式接口中「被实现的方法」的全部参数传给该构造器作为参数   | (a,b,...)->new 类名(a,b,...)        |
>
> **静态方法引用:**
>
> ```java
> Consumer<String> c1 = r -> Integer.parseInt(r);
> c1.accept("1");
> Consumer<String> c2 =Integer::parseInt;
> c1.accept("2");
> 1234
> ```
>
> **实例方法引用:**
>
> ```java
>  Consumer<String> ins1 = r -> System.out.print(r);
>  c1.accept("1");
>  Consumer<String> ins2 =System.out::print;
>  c1.accept("2");
> 1234
> ```
>
> **对象方法引用：**
>
> ```java
> Comparator<String> comparator = (o1, o2)->o1.compareTo(o2);
> System.out.println(comparator.compare("20", "12"));//1
> 
> Comparator<String> comparator1 = String::compareTo;
> System.out.println(comparator1.compare("20", "12"));//1
> 12345
> ```
>
> **构造方法引用：**
>
> ```java
> Consumer<String> n1 = r ->new BigDecimal(r);
>  c1.accept("1");
>  Consumer<String> n2 =BigDecimal::new;
>  c1.accept("2");
> 1234
> ```
>
> # 3. 各类Api
>
> ## Array
>
> ```java
> //以a,b,c为元素生成List
> List arr=Arrays.asList(a,b,c)
> 
> //使用提供的生成函数来计算每个元素，设置指定数组的所有元素
> setAll(T[] array, IntFunction<? extends T> generator)
> 例子：
> ArrayList<Integer>[] g=new ArrayList[k];
> Arrays.setAll(g,e->new ArrayList<Integer>());
> 
> //将二维数组按照第二维升序排列
>  Arrays.sort(points,(o1,o2)->o1[1]-o2[1]);
> //但是减法可能会碰到负数溢出的问题，本来应该为负数的结果转变成正数，调用Interger.compare()
> Arrays.sort(points,(o1,o2)->Integer.compare(o1[1],o2[1]));
> //有可能原数据就需要用long表示，调用Long.compare()
> Arrays.sort(points,(o1,o2)->Long.compare(o1[1],o2[1]));
> 123456789101112131415
> ```
>
> ## ArrayList
>
> ```java
> //在对应位置处加入val
> ArrayList<Integer> list = new ArrayList<>();
> list.add(0,val);
> 
> //从别的Collection接口下的容器，拷贝生成ArrayList
> //常在回溯记录路径中使用
> LinkedList<Integer> path=new LinkedList<>();
> ArrayList<Integer> list=new ArrayList<>(path);
> 12345678
> ```
>
> ## LinkedList
>
> ```java
> //在对应位置处加入val
> ArrayList<Integer> list = new ArrayList<>();
> list.add(0,val);
> 
> //删除最后一个元素
> list.removeLast();
> 123456
> ```
>
> ## HashMap
>
> ```java
> //判断key是否存在
> boolean flag=map.containsKey(key);
> //判断value是否存在
> boolean flag=map.containsValue(key);
> 
> //获取Map中的所有key
> Set<Integer> map1 = map.keySet();
> //获取Map中的所有value
> Collection<String> map2 = map.values();
> //获取Map中所有的key-value对象
> Set<Map.Entry<Integer, String>> map3 = map.entrySet();
> 
> 
> // 如果添加key对应value为空，添加新元素
> map.putIfAbsent(key,value)
> 
> // 如果查找key对应value为空，则得到默认的元素
> map.getOrDefault(key,new ArrayList<>(Integer));
> 
> 12345678910111213141516171819
> ```
>
> ## Deque
>
> ```java
> //标准队列操作
> Deque<TreeNode>  que= new ArrayDeque<>(); 
> Deque<TreeNode>  que= new LinkedList<>(); 
> que.offer(); 
> que.poll(); 
> que.peek();
> 
> //双端队列操作
> //头部添加和尾部添加 
> que.offerFirst(val);
> que.offerLast(val);
> //头部删除和尾部删除
> que.pollFirst();
> que.pollLast();
> //头部查询和尾部查询
> que.peekFirst();
> que.peekLast();
> 1234567891011121314151617
> ```
>
> ## Stack
>
> ```java
> //标准栈操作
> Stack<TreeNode> st= new Stack<>(); 
> st.push(); 
> st.pop(); 
> st.peek()
> ```
