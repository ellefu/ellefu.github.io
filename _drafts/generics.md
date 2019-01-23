---
layout: post
title:  "Generic"
date:   2019-01-21 14:15:00 +0800
categories: Java
---

泛型在物件導向和設計模式中都有廣泛的應用

```
import java.util.*;
public class Generic {

   public static void main(String[] args) {
        List arrayList = new ArrayList();
        arrayList.add("aaa");
        arrayList.add(100);

        for (int i =0; i<arrayList.size(); i++) {
                String item = (String) arrayList.get(i);  //crash here
                System.out.println("item = " + item);
        }
   }
}
```
crash here

```
Exception in thread "main" java.lang.ClassCastException: java.lang.Integer cannot be cast to java.lang.String
	at Generic.main(Generic.java:11)
```

ArrayList可以存在任意類型，為了在編譯階段解決這樣的問題，泛型應運而生。
更改初始化list的設定，就可以在編譯器發現問題

```java
List<String> arrayList = new ArrayList<String>();
arrayList.add(100); //complie error
```

泛型只在編譯時期有效

```java
import java.util.*;

public class Generic2 {
  public static void main(String[] args) {
        List<String> stringArrayList =  new ArrayList<String>();
        List<Integer> integerArrayList =  new ArrayList<Integer>();

        Class classStringArrayList =  stringArrayList.getClass();
        Class classIntegerArrayList = integerArrayList.getClass();

        if (classStringArrayList.equals(classIntegerArrayList)) {
                System.out.println("Generic test: the same class");
        }
  }
}
```

java的泛型只在編譯階段有效
泛型類型在邏輯上可以看成是多個不同的類型，實際上都是相同的基本類型

# 泛型的使用 

泛型有三種使用方式：泛型類、泛型接口、泛型方法

## 泛型類

泛型類型用於類的定義中，被稱為泛型類。<br>
通過泛型可以完成對一組類的操作對外開放相同的接口，最典型的就是各種容器類<br>
如 List, Set, Map <br>

### 泛型類的基本寫法 

```java
class 類名稱<泛型標識: 可以是任意標識，標識指定的泛型的類型> {
	private 泛型標識 /*(成員變量類型)*/ var;
}
```

一個最普通的泛型類:

```java
public class GenericClass {
	public static void main(String[] args) {
			    //定義泛型類，傳入泛型類型實參
			Generic<Integer> genericInteger = new Generic<Integer>(123456);
			Generic<String> genericString = new Generic<String>("key_value");
			System.out.println("key is = " + genericInteger.getKey());
			System.out.println("key is  = " + genericString.getKey());	
			
			//定義泛型類時，不一定要傳入泛型類型實參，此時在泛型類中使用泛型的方式或是成員變量定義的類型可以為任何的類型
			Generic generic = new Generic("11111");
			Generic generic1 = new Generic(4444);
			Generic generic2 = new Generic(55.55);
			Generic generic3 = new Generic(false);		
			
			System.out.println("key is = " + generic.getKey());
			System.out.println("key is  = " + generic1.getKey());
			System.out.println("key is  = " + generic2.getKey());	
			System.out.println("key is  = " + generic3.getKey());	
	}

	//此處T可以隨便寫為任意標識，如T, E, K, V等形式的參數用於表示泛型
    //在實例化泛型類時，必須指定T的具體類型
	public static class Generic<T> {
	
	        // key這個成員變量的類型為T, T的類型由外部指定
			private T key;
			
			// 泛型建構子的參數類型也為T, T的類型由外部指定
			public Generic(T key) {
					this.key =  key;
			}
			
			//泛型方法 getKey 的返回值類型為T, T的類型由外部指定
			public T getKey() {
					return key;
			}
	}
}
```

the result: <br>
key is = 123456  <br>
key is  = key_value  <br>
key is = 11111  <br>
key is  = 4444  <br>
key is  = 55.55  <br>
key is  = false  <br>

```
注意：
泛型的類型參數只能是類類型，不能是簡單類型。
不能對確切的泛型類型使用instanceof操作，編譯會出錯，如以下非法的操作：
if(ex_num instanceof Generic<Number>){   
} 
```

<H2> 泛型接口 </H2>

```java
import java.util.*;

class GeneratorInterface {
	
	public static void main (String[] args) {
		FruitGenerator fruitGenerator =  new FruitGenerator();
		System.out.println(fruitGenerator.next());
		
		FruitGenerator1 fruitGenerator1 = new FruitGenerator1();
		System.out.println(fruitGenerator1.next());
	}


	// 定義一個泛型接口
	public interface Generator<T> {
		public T next();
	}

	/*
	當實現泛型接口的類，未傳入泛型實參時, 與泛型類的定義相同，在聲明類的時候，需將泛型的聲明也一起加到類中
	即 class FruitGenerator<T> implements Generator<T> 
	如果不聲明泛型，如 class FruitGenerator implements Generator<T>，編譯器會報錯: "Unknow class"
	*/
	public static class FruitGenerator<T> implements Generator<T> {
		@Override
		public T next() {
			return null;
		}
	}

	/*
	當實現泛型接口的類，傳入泛型實參時：
	定義一個生產器實現這個接口，雖然我們只創建了一個泛型接口Generator<T>
	但是我們可以為T傳入無數個實參，形成無數種類型的Generator接口
	在實現類實現泛型接口時，如已將泛型類型傳入實參類型，則所有使用泛型的地方都要替換成傳入的實參類型
	即 Generator<T>, 
	public T next(); 中的T都要換成String 類型
	public String next();
	*/
	public static class FruitGenerator1 implements Generator<String> {
		private String[] fruits = new String[]{"Apple", "Banana", "Pear"};
		@Override
		public String next() {
			Random rand = new Random();
			return fruits[rand.nextInt(3)];
		}
	}
}
```

<h2> 泛型通配符 </h2>

我們知道 Integer 是 Number 的一個子類 <br>
也驗證過 Generic<Integer> 和 Generic<Number> 實際上都是相同的基本類型 <br>
那麼在使用 Generic<Number> 作為形參的方法中，能否使用 Generator<Integer> 的實例傳入? <br>
在邏輯上類似於 Generic<Number> 和 Generic<Integer> 是否可以看到具有繼承關係的泛型類型? <br>

```
import java.util.*;

class GenericInheritance {

	public static void main(String[] args) {
		Integer integerTest = 123;
		Generic<Integer> gInteger = new Generic<Integer>(integerTest) ;
		Generic<Number> gNumber = new Generic<Number>(456);
		
		showKeyValue(gInteger);   
		//  GenericInheritance.java:10: error: incompatible types: Generic<Integer> cannot be converted to Generic<Number>
		showKeyValue(gNumber);
	}
	
	public static void showKeyValue(Generic<Number> obj) {
		System.out.println("key value is " + obj.getKey());
	}
	
	public static class Generic<T> {
			private T key;
			
			public Generic(T key) {
					this.key =  key;
			}
			public T getKey() {
					return key;
			}
	}
}
```

我們需要一個在邏輯上可以同時是 Generic<Integer> 和 Generic<Number> 父類的引用類型，<br>
因此類型通配符應運而生 <br>

```
import java.util.*;

class GenericInheritance {

	public static void main(String[][ args) {
		Integer integerTest = 123;
		Generic<Integer> gInteger = new Generic<Integer>(integerTest) ;
		Generic<Number> gNumber = new Generic<Number>(456);
		
		showKeyValue(gInteger); 
		showKeyValue(gNumber);
	}
	
	// 類型通配符一般是使用 ? 代替具體的類型實參
	// 也就是說，此處的 ? 和 Number, String, Integer 一樣都是一種實際的類型
	// 可以把 ? 看成所有類型的父類。是一種真實的類型
	// 可以解決當具體類型不確定的時候，這個通配符就是 ?
	// 當操作類型時，不需要使用類型的具體功能，只使用Object類中的功能，那麼可以用 ? 通配符來表示未知類型
	public static void showKeyValue(Generic<?> obj) {
		System.out.println("key value is " + obj.getKey());
	}
	
	public static class Generic<T> {
			private T key;
			
			public Generic(T key) {
					this.key =  key;
			}
			public T getKey() {
					return key;
			}
	}
}
```
<h2> 泛型方法 </h2>
在java中，泛型類的定義非常簡單，但是泛型方法就比較複雜了 <br>
尤其是我們見到的大多數泛型類中的成員方法也都使用了泛型，有的甚至泛型類中也包含著泛型方法 <br>
這樣在初學者中非常容易將泛型方法理解錯誤。 <br>

泛型類，是在實例化類的時候指明泛型的具體類型。<br>
泛型方法，是在調用方法的時候指明泛型的具體類型。 <br>

<h3> 泛型方法的基本用法 </h3>

```java
/*
泛型方法的基本介紹
@param tClass 傳入的泛型實參
@return T返回值為T類型

1) public與返回值中間<T>非常重要，可以理解為聲明此方法為泛型方法
2) 只有聲明了<T>的方法才是泛型方法，泛型類中的使用了泛型的成員方法並不是泛型方法。
3) <T> 表明該方法將使用泛型類型T，此時才可以在方法中使用泛型類型Ｔ
4) 與泛型類的定義一樣，此處 T 可以隨便寫為任意標識，如 E, K, V等
*/

public <T> T genericMethod(Class<T> tclass) throws InstantiationException, IllegalAccessException {
	T instance = tClass.newInstance();
	return instance;
}

Object obj = genericMethod(Class.forName("com.test.test"));
```

```java
public class GenericTest {

	// 這個類是個泛型類
	public class Generic<T> {
		private T key;
		
		public Generic(T key) {
			this.key = key;
		}
		// 雖然在方法中使用了泛型，但這並不是一個泛型方法，
		// 只是類中一個普通的成員方法，只不過他的返回值是在聲明泛型類已經聲明過的泛型。
		// 所以在這個方法中才可以繼續使用 T 這個泛型
		public T getKey() {
			return key;
		}
		
		/*
		這個方法顯然是有問題的，編譯器錯誤: cannot resolve symbol E
		因為在類的聲明中並未聲明泛型E, 所以在使用 E 做形參和返回值類型時，編譯單會無法識別
		public E setKey(E key) {
				this.key = key;
		}
		*/
		
		/*
		這才是一個真正的泛型方法。
		首先在public與返回值之間的 <T> 必不可少，表明這是一個泛型方法，並且聲明了一個泛型 T
		這個 T 可以出現在這個泛型方法的任意位置
		泛型的數量也可以為任意多個
		如 public <T, K> K showKeyName(Generic<T> container) {.... }
		*/
		public <T> T showKeyName(Generic<T> container) {
				System.out.println("container.key: "+container.getKey());
				// 這個舉例不太合適，只是為了說明泛型方法的特性
				T test = container.getKey();
				return test;
		}
			
		// 這也不是一個泛型方法，只是一個普通方法，使用Generic<Number> 這個泛型類做形參而已
		public void showKeyValue1(Generic<Number> obj) {
			System.out.println("key value is "+obj.getKey());
		}
		
		// 這也不是一個泛型方法，只是一個普通方法，使用泛型通配符 ? 
		// 同時也印證了泛型通配符章節所描述的， ? 是一種類型實參，可以看做為 Number 等所有類的父類
		public void showKeyValue2(Generic<?> obj) {
				System.out.println("key value is " + obj.getKey();
		}
		
	}
}
```

<h3> 類中的泛型方法 </h3>

這並不是泛型方法的全部，泛型方法可以出現在任何地方和任何場景。<br>
但是有一種情況是非常特殊的，當泛型方法出現在泛型類中時：<br>

```java
public class GenericMethod {
		static class Fruit {
				@Override
				public String toString() {
					return "fruit";
				}
		}
		
		static class Apple extends Fruit {
				@Override
				public String toString() {
					return "apple";
				}
		}
		
		static class Person {
				@Override
				public String toString() {
						return "Person";
				}
		}
		
		static class GenerateTest<T> {
				public void show_1(T t) {
						System.out.println(t.toString());
				}
				
				// 在泛型類中聲明了一個泛型方法，使用泛型E, 這個泛型E可以為任意類型。可以與 T 相同，也可以不同
				// 由於泛型方法在聲明時會聲明泛型<E>，因此即使在泛型類中並未聲明泛型，編譯器也能夠正確識別泛型方法中識別的泛型
				public <E> void show_3(E t) {
						System.out.println(t.toString());
				}
				
				// ?????????
				// 在泛型類中聲明了一個泛型方法，使用泛型T，注意這個 T 是一種全新的類型，可以與泛型類中聲明的 T 是不同的類型
				public <T> void show_2(T t) {
						System.out.println(t.toString());
				}
		}
		
		public static void main (String[] args) {
			Apple apple = new Apple();
			Person person = new Person();
			
			GenerateTest<Fruit> generateTest = new GenerateTest<Fruit>();
			generateTest.show_1(apple);   // apple是Fruit的子類，所以可以
			//generateTest.show_1(person);  //Complie error, 因為泛型類型實參指定的是Fruit, 傳入的是Person
			
			generateTest.show_2(apple);
			generateTest.show_2(person); 
			
			generateTest.show_3(apple);
			generateTest.show_3(person);	
		}
}
```

<h3> 泛型方法與可變參數 </h3>

```java
class GenericVariable {	

	public static void main(String[] args) {
		printMsg("111", 222, "aaaaa", "2323.4", 55.55);
	}
	
	public static <T> void printMsg(T... args) {
		for (T t: args) {
			System.out.println("t is " + t);
		}
	}
}
```

<h3> 靜態方法與泛型 </h3>

靜態方法無法訪問類上定義的泛型；如果靜態方法操作的引用類據類型不確定的時候，必須要將泛型定義在方法上。<br>
即：如果靜態方法要使用泛型的話，必須將靜態方法也定義成泛型方法。

```java
public class StaticGenerator<T> {		
		/*
		如果在類中定義使用泛型的靜態方法，需要添加額外的泛型聲明（將這個方法定義成泛型方法）
		即使靜態方法要使用泛型類中已經聲明過的泛型也不可以
		如 public static void show(T t) {....}  
		compile error: StaticGenerator cannot be refreenced from static context
		*/
		public static <T> void show(T t) {
		
		}
}
```

<h3> 泛型方法總結 </h3>
泛型方法能使方法獨立於類而產生變化<br>
無論何時，如果你能做到，你就該盡量使用泛型方法。<br>
也就是說，如果使用泛型方法將整個類泛型化，那麼就應該使用泛型方法。<br>
另外對於一個static的方法而已，無法訪問泛型類型的參數。<br>
所以如果static方法要使用泛型能力，就必須使其成為泛型方法。<br>

<h2> 泛型上下邊界 </h2>

在使用泛型時，我們可以為傳入的泛型類型實參進行上下邊界的限制，<br>
如：類型實參只准傳入某種類型的父類或某種類型的子類。












